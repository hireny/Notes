# 前言

Spring Boot 在启动后会在控制台显示默认的Banner横幅：



但有时我们需要将默认横幅更换掉，换上自己的或者公司的LOGO，因此我们研究一下 Spring Boot 的 Banner 究竟是不是可配置的。

源码分析
现在我们分析一下Spring Boot在启动时必须经过的方法 `run()`：



进入 SpringApplication 类，查看一下 run() 方法：

```java
public ConfigurableApplicationContext run(String... args) {
    StopWatch stopWatch = new StopWatch();
    stopWatch.start();
    ConfigurableApplicationContext context = null;
    Collection<SpringBootExceptionReporter> exceptionReporters = new ArrayList();
    this.configureHeadlessProperty();
    SpringApplicationRunListeners listeners = this.getRunListeners(args);
    listeners.starting();

    Collection exceptionReporters;
    try {
        ApplicationArguments applicationArguments = new DefaultApplicationArguments(args);
        ConfigurableEnvironment environment = this.prepareEnvironment(listeners, applicationArguments);
        this.configureIgnoreBeanInfo(environment);
        // 该方法为调用 Banner 的打印方法
        Banner printedBanner = this.printBanner(environment);
        ...省略...
    } catch (Throwable var10) {
        ...省略...
    }
    ...省略...
}
```

`run()` 方法中调用了 `printBanner()` 方法，该方法是用来打印 Banner 横幅的方法，现在我们看一下 `printBanner()` 方法：

```java
private Banner printBanner(ConfigurableEnvironment environment) {
    // Banner 的 Mode 为 OFF - 禁止输出
    if (this.bannerMode == Mode.OFF) {
        return null;
    } else {
        ResourceLoader resourceLoader = this.resourceLoader != null ? this.resourceLoader : new DefaultResourceLoader(this.getClassLoader());
        SpringApplicationBannerPrinter bannerPrinter = new SpringApplicationBannerPrinter((ResourceLoader)resourceLoader, this.banner);
        // 在这里判断另外两种情况 LOG 与 CONSOLE
        return this.bannerMode == Mode.LOG ? bannerPrinter.print(environment, this.mainApplicationClass, logger) : bannerPrinter.print(environment, this.mainApplicationClass, System.out);
    }
}
```

查看 `printBanner()` 方法，可知 banner 打印有三种情况：查看一下 Mode 源码：

```java
package org.springframework.boot;
import java.io.PrintStream;
import org.springframework.core.env.Environment;
@FunctionalInterface
public interface Banner {
    void printBanner(Environment environment, Class<?> sourceClass, PrintStream out);
    public static enum Mode {
        OFF,  // 停止
        CONSOLE, // 控制台
        LOG;  // 日志
        private Mode() {}
    }
}
```

从上述源码可知， banner 的三种情况就是在控制台打印，在日志中打印与停止打印。

```java
private Banner getBanner(Environment environment) {
    SpringApplicationBannerPrinter.Banners banners = new SpringApplicationBannerPrinter.Banners();
    // 获取关于图像的Banner图
    banners.addIfNotNull(this.getImageBanner(environment));
    // 获取关于文本的Banner图
    banners.addIfNotNull(this.getTextBanner(environment));
    // 在这里判断Banner至少存在一个
    if (banners.hasAtLeastOneBanner()) {
        return banners;
    } else {
        // 如果不存在，判断是否设置了获取Banner失败后应急Banner图，如果该Banner也没有，则返回默认的
        return this.fallbackBanner != null ? this.fallbackBanner : DEFAULT_BANNER;
    }
}
```

接着继续往下查看，可从 `getBanner()` 方法中得知，banner 存储的内容会是图像和文本，如果都没有获到，会先判断fallbackBanner 是否为空，为空的话在使用默认 banner。

```java
private Banner getTextBanner(Environment environment) {
    // 从本地配置路径获取Banner文本路径
    String location = environment.getProperty("spring.banner.location", "banner.txt");
    Resource resource = this.resourceLoader.getResource(location);
    return resource.exists() ? new ResourceBanner(resource) : null;
}

private Banner getImageBanner(Environment environment) {
    // 从本地配置路径获取Banner图像路径
    String location = environment.getProperty("spring.banner.image.location");
    if (StringUtils.hasLength(location)) {
        // 根据路径获取图像资源
        Resource resource = this.resourceLoader.getResource(location);
        return resource.exists() ? new ImageBanner(resource) : null;
    } else {
        // 图像后缀名
        String[] var3 = IMAGE_EXTENSION;
        int var4 = var3.length;

        for(int var5 = 0; var5 < var4; ++var5) {
            String ext = var3[var5];
            // 根据后缀名获取图像资源，根据数组中顺序获取，先获取到谁就先用谁
            Resource resource = this.resourceLoader.getResource("banner." + ext);
            if (resource.exists()) {
                return new ImageBanner(resource);
            }
        }
        return null;
    }
}
```

在调用的 `getTextBanner()` 与 `getImageBanner()` 方法中，我们看到获取资源是根据配置文件中的信息，如果配置文件没有设置，它会默认获取 resources 文件夹下的命名为 banner 的文本和图像。

我们获取 IMAGE_EXTENSION 数组得知banner 获取的图像后缀的数组内容：

```java
static final String[] IMAGE_EXTENSION = new String[]{"gif", "jpg", "png"};
```

因此Spring Boot 查找图像的顺序是：banner.gif、banner.jpg 和 banner.png。

而获取 banner.txt 是与图像横幅同时进行的。在 getBanner() 中有一个 banner.hasAtLeastOneBanner() 判断，至少要有一个横幅存在才会返回 banner 存储。

在获取完 banner 图像后，会返回 `ImageBanner` 对象，我们查看一下 `ImageBanner` 源码，查找到实现 Banner 接口的方法的真正实现是 `printBanner()` 和 `getAsiiPixel()` ，这两个方法主要是进行输出和像素点转换成字符。

```java
private void printBanner(BufferedImage image, int margin, boolean invert, BitDepth bitDepth, ImageBanner.PixelMode pixelMode, PrintStream out) {
    AnsiElement background = invert ? AnsiBackground.BLACK : AnsiBackground.DEFAULT;
    out.print(AnsiOutput.encode(AnsiColor.DEFAULT));
    out.print(AnsiOutput.encode(background));
    out.println();
    out.println();
    AnsiElement lastColor = AnsiColor.DEFAULT;
    AnsiColors colors = new AnsiColors(bitDepth);
    // 这里遍历图像的宽高来获取图像的像素点
    for(int y = 0; y < image.getHeight(); ++y) {
        int x;
        for(x = 0; x < margin; ++x) {
            out.print(" ");
        }

        for(x = 0; x < image.getWidth(); ++x) {
            // 获取像素点
            Color color = new Color(image.getRGB(x, y), false);
            AnsiElement ansiColor = colors.findClosest(color);
            if (ansiColor != lastColor) {
                out.print(AnsiOutput.encode(ansiColor));
                lastColor = ansiColor;
            }
            // 像素点转换为字符输出
            out.print(this.getAsciiPixel(color, invert, pixelMode));
        }

        out.println();
    }

    out.print(AnsiOutput.encode(AnsiColor.DEFAULT));
    out.print(AnsiOutput.encode(AnsiBackground.DEFAULT));
    out.println();
}
// 像素点转换成字符
private char getAsciiPixel(Color color, boolean dark, ImageBanner.PixelMode pixelMode) {
    char[] pixels = pixelMode.getPixels();
    int increment = 10 / pixels.length * 10;
    int start = increment * pixels.length;
    // 根据 color与 dark 计算出一个亮度值
    double luminance = (double)this.getLuminance(color, dark);

    for(int i = 0; i < pixels.length; ++i) {
        if (luminance >= (double)(start - i * increment)) {
            return pixels[i];
        }
    }

    return pixels[pixels.length - 1];
}
```

这样，一个 Banner 的打印流程就完成了。下面我们来配置自己的 Banner图 。

Banner配置
首先我们设置一个 banner.png 文件并将它存储在 src/main/resources 中：



运行该Spring Boot后，在控制台输出：



在设置一个 banner.jpg 文件：



运行后可以看到：



在设置一个 banner.gif 文件：



运行后可以看到：



在设置一个 banner.txt文件：



再次启动项目，可以看到如下内容：



从如上流程与源码中可以看出 Banner 的执行顺序：

先在 classpath 下找 banner.gif、banner.jpg 与 banner.png，根据顺序查找，先找到谁就读取哪个，然后打印，如果都没有，就不打印；
当在 classpath 下存在 banner.txt 时，也会读取该文件并打印，并不与上面冲突；
当都没有的时候，会先判断是否设置了读取失败的 fallbackBanner，如果没有才会使用默认的 SpringBootBanner。
配置文件
当然，不止是换个 Banner 那么简单，我们还可以对 Banner 在配置文件中进行配置。

首先我们可以使用 ${AnsiColor.NAME} 更改 banner.txt 的字体颜色：



输出的结果：



在 banner.txt 中还可以添加下面的表达式：

|表达式         |说明     |
|${application.title}    |MANIFEST>MF中定义的程序名称 |
|${application.version}    |MANIFEST.MF文件中的版本号|
|${application.formatted-version} |格式化后的${application.version}版本信息|
|${spring-boot.version}    |spring boot的版本号  |
|${spring-boot.formatted-version} |格式化后的${spring-boot.version}版本信息  |
下面是给一个使用表达式的例子：



效果如下：



我们还可以在 appliation.properties 文件中配置一些 banner 的属性：

```properties
# 程序名称对应于 banner.txt 中的 ${application.name}
spring.application.name=Spring Boot Banner
# Banner模式，可以配置输出到控制台console，文件log或者禁止off
spring.main.banner-mode=console

#Banner指定编码格式，如果不指定，默认是 UTF-8
spring.banner.charset=utf-8
#Banner指定文件路径
spring.banner.location=classpath:banner.txt
# Banner指定图像路径
spring.banner.image.location=classpath:banner.png
# Banner指定图像宽度
spring.banner.image.width=76
# Banner指定图像高度
spring.banner.image.height=
# Banner图像的左边界（字符数）
spring.banner.image.margin=2
# 是否将图像转换为黑色控制台主题
spring.banner.image.invert=false
```

Banner自定义
如果Spring Boot自带的Banner类不适合，我们可以定制一个实现了 Banner 接口的 Banner 类，来满足我们的需求。

首先我们需要创建一个实现 Banner 接口的类：

```java
public class SampleBanner implements Banner {

    private static final String BANNER =
            "  ___ ___         .__  .__            __      __            .__       .___\n" +
                    " /   |   \\   ____ |  | |  |   ____   /  \\    /  \\___________|  |    __| _/\n" +
                    "/    ~    \\_/ __ \\|  | |  |  /  _ \\  \\   \\/\\/   /  _ \\_  __ \\  |   / __ | \n" +
                    "\\    Y    /\\  ___/|  |_|  |_(  <_> )  \\        (  <_> )  | \\/  |__/ /_/ | \n" +
                    " \\___|_  /  \\___  >____/____/\\____/    \\__/\\  / \\____/|__|  |____/\\____ | \n" +
                    "       \\/       \\/                          \\/                         \\/ ";

    @Override
    public void printBanner(Environment environment, Class<?> sourceClass, PrintStream out) {
        out.println(BANNER);
        out.println();
    }
}
在SpringBootApplication的启动类中进行注册：

@SpringBootApplication
public class SpringBootBannerApplication {
    public static void main(String[] args) {
        SpringApplication springApplication = new SpringApplication(SpringBootBannerApplication.class);
        // 设置自己实现的 Banner 图
        springApplication.setBanner(new SampleBanner());
        // 启动 spring boot
        springApplication.run(args);
    }
}
```

就可以了，如果想对 Banner 进行扩展，只需要对 SampleBanner 类增加功能即可。

# 结束

这就是 Spring Boot 设置 Banner 的全过程，如果企业/团队/个人的项目需要修改 Banner，那么就可以自己定制一个来满足需求，而手工编辑字符画或者将转换成字符画的图片，我们可以在网上找到工具来轻松生成，这里就不列举了。

> 本文使用的 Spring Boot 版本为 2.2.1.RELEASE