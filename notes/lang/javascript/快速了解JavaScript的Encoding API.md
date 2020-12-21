Encoding API 主要用于实现字符串与定型数组之间的转换。规范新增了 4 个用于执行转换的全局类：`TextEncoder`、`TextEncoderStream`、`TextDecoder`和`TextDecoderStream`。

注意：相比于批量（bulk）的编解码，对流（stream）编解码的支持很有限。

# 文本编码

Encoding API 提供了两种将字符串转换为定型数组二进制格式的方法：批量编码和流编码。把字符串转换为定型数组时，编码器始终使用 UTF-8。

**1. 批量编码**

所谓批量，指的是 JavaScript 引擎会同步编码整个字符串。对于非常长的字符串，可能会花较长时间。批量编码是通过 `TextEncoder` 的实例完成的：

```javascript
const textEncoder = new TextEncoder();
```

这个实例上有一个 `encode()` 方法，该方法接收一个字符串参数，并以 `Uint8Array` 格式返回每个字符的 UTF-8 编码：

```javascript
const textEncoder = new TextEncoder();
const decodedText = 'foo';
const encodedText = textEncoder.encode(decodedText);
// f 的UTF-8 编码是0x66（即十进制102）
// o 的UTF-8 编码是0x6F（即二进制111）
console.log(encodedText); // Uint8Array(3) [102, 111, 111]
编码器是用于处理字符的，有些字符（如表情符号）在最终返回的数组中可能会占多个索引：
const textEncoder = new TextEncoder();
const decodedText = '☺';
const encodedText = textEncoder.encode(decodedText);
// ☺的UTF-8 编码是0xF0 0x9F 0x98 0x8A（即十进制240、159、152、138）
console.log(encodedText); // Uint8Array(4) [240, 159, 152, 138]
```

编码器实例还有一个 `encodeInto()` 方法，该方法接收一个字符串和目标 `Unit8Array`，发挥一个字典，该字典包含 `read` 和 `written` 属性，分别表示成功从源字符串读取了多少字符和向目标数组写入了多少字符。如果定型数组的空间不够，编码就会提前终止，返回的字典会体现这个结果：

```javascript
const textEncoder = new TextEncoder();
const fooArr = new Uint8Array(3);
const barArr = new Uint8Array(2);
const fooResult = textEncoder.encodeInto('foo', fooArr);
const barResult = textEncoder.encodeInto('bar', barArr);
console.log(fooArr); // Uint8Array(3) [102, 111, 111]
console.log(fooResult); // { read: 3, written: 3 }
console.log(barArr); // Uint8Array(2) [98, 97]
console.log(barResult); // { read: 2, written: 2 }
```

encode()要求分配一个新的Unit8Array，encodeInto()则不需要。对于追求性能的应用，这个差别可能会带来显著不同。

注意：文本编码会始终使用 UTF-8 格式，而且必须写入 `Unit8Array` 实例。使用其它类型数组会导致 `encodeInto()` 抛出错误。

**2. 流编码**

`TextEncoderStream` 其实就是 `TransformStream` 形式的 `TextEncoder`。将解码后的文本流通过管道输入流编码器会得到编码后文本块的流：

```javascript
async function* chars() {
const decodedText = 'foo';
for (let char of decodedText) {
yield await new Promise((resolve) => setTimeout(resolve, 1000, char));
const decodedTextStream = new ReadableStream({
async start(controller) {
for await (let chunk of chars()) {
controller.enqueue(chunk);
}
controller.close();
}
});
const encodedTextStream = decodedTextStream.pipeThrough(new TextEncoderStream());
const readableStreamDefaultReader = encodedTextStream.getReader();
(async function() {
while(true) {
const { done, value } = await readableStreamDefaultReader.read();
if (done) {
break;
} else {
console.log(value);
}
}
})();
// Uint8Array[102]
// Uint8Array[111]
// Uint8Array[111]
```

# 文本解码

Encoding API 提供了两种将定型数组转换为字符串的方式：批量解码和流解码。与编码器类不同，在将定型数组转换为字符串时，解码器支持非常多的字符串编码，可以参考 Encoding Standard 规范的 “Names and labels” 一节。

默认字符编码格式是 UTF-8。

**1. 批量解码**

所谓批量，指的是 JavaScript 引擎会同步解码整个字符串。对于非常长的字符串，可能会花较长时间。批量解码是通过 `TextDecoder` 的实例完成的：

```javascript
const textDecoder = new TextDecoder();
```

这个实例上有一个 `decode()` 方法，该方法接收一个定型数组参数，返回解码后的字符串：

```javascript
const textDecoder = new TextDecoder();
// f 的UTF-8 编码是0x66（即十进制102）
// o 的UTF-8 编码是0x6F（即二进制111）
const encodedText = Uint8Array.of(102, 111, 111);
const decodedText = textDecoder.decode(encodedText);
console.log(decodedText); // foo
```

解码器不关心传入的是哪种定型数组，它只会专心解码整个二进制表示。在下面这个例子中，只包含 8 位字符的 32 位值被解码为 UTF-8 格式，解码得到的字符串中填入了空格：

```javascript
const textDecoder = new TextDecoder();
// f 的UTF-8 编码是0x66（即十进制102）
// o 的UTF-8 编码是0x6F（即二进制111）
const encodedText = Uint32Array.of(102, 111, 111);
const decodedText = textDecoder.decode(encodedText);
console.log(decodedText); // "f o o "
```

解码器是用于处理定型数组中分散在多个索引上的字符的，包括表情符号：

```javascript
const textDecoder = new TextDecoder();
// ☺的UTF-8 编码是0xF0 0x9F 0x98 0x8A（即十进制240、159、152、138）
const encodedText = Uint8Array.of(240, 159, 152, 138);
const decodedText = textDecoder.decode(encodedText);
console.log(decodedText); // ☺
```

与 `TextEncoder` 不同，`TextDecoder` 可以兼容很多字符编码。比如下面的例子就使用了 UTF-16 而非默认的 UTF-8：

```javascript
const textDecoder = new TextDecoder('utf-16');
// f 的UTF-8 编码是0x0066（即十进制102）
// o 的UTF-8 编码是0x006F（即二进制111）
const encodedText = Uint16Array.of(102, 111, 111);
const decodedText = textDecoder.decode(encodedText);
console.log(decodedText); // foo
```

**2. 流解码**

`TextDecoderStream` 其实就是 `TransformStream` 形式的 `TextDecoder`。将编码后的文本流通过管道输入流解码器得到解码后文本块的流：

```javascript
async function* chars() {
// 每个块必须是一个定型数组
const encodedText = [102, 111, 111].map((x) => Uint8Array.of(x));
for (let char of encodedText) {
yield await new Promise((resolve) => setTimeout(resolve, 1000, char));
}
}
const encodedTextStream = new ReadableStream({
async start(controller) {
for await (let chunk of chars()) {
controller.enqueue(chunk);
}
controller.close();
}
});
const decodedTextStream = encodedTextStream.pipeThrough(new TextDecoderStream());
const readableStreamDefaultReader = decodedTextStream.getReader();
(async function() {
while(true) {
const { done, value } = await readableStreamDefaultReader.read();
if (done) {
break;
} else {
console.log(value);
}
}
})();
// f
// o
// o
```

文本解码器流能够识别可能分散在不同块上的代理对。解码器流会保持块片段直到取得完整的字符。比如在下面的例子中，流解码器在解码流并输出字符之前会等待传入 4 个块：

```javascript
async function* chars() {
// ☺的UTF-8 编码是0xF0 0x9F 0x98 0x8A（即十进制240、159、152、138）
const encodedText = [240, 159, 152, 138].map((x) => Uint8Array.of(x));
for (let char of encodedText) {
yield await new Promise((resolve) => setTimeout(resolve, 1000, char));
}
}
const encodedTextStream = new ReadableStream({
async start(controller) {
for await (let chunk of chars()) {
controller.enqueue(chunk);
}
controller.close();
}
});
const decodedTextStream = encodedTextStream.pipeThrough(new TextDecoderStream());
const readableStreamDefaultReader = decodedTextStream.getReader();
(async function() {
while(true) {
const { done, value } = await readableStreamDefaultReader.read();
if (done) {
break;
} else {
console.log(value);
}
}
})();
// ☺
```

文本解码器流经常与 `fetch()` 一起使用，因为响应体可以作为 `ReadableStream` 来处理。比如：

```javascript
const response = await fetch(url);
const stream = response.body.pipeThrough(new TextDecoderStream());
const decodedStream = stream.getReader()
for await (let decodedChunk of decodedStream) {
console.log(decodedChunk);
}
```

# 小结

Encoding API 用于实现字符串与缓冲区之间的无缝转换（越来越常见的操作）。