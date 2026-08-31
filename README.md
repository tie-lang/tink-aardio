# tink-aardio

tink data-flow node frame protocol — aardio 库（无依赖，字符串按字节处理）。
通用且语言无关：任何遵守帧协议语言/组件都能接入 tink 管道。

```
帧 = [ len: u32 BE ][ payload: len 字节 ][ crc: u32 BE ]
len = payload 字节数
crc = CRC32-IEEE(payload)（多项式 0xEDB88320）
```

与 `std/tink.tie`（tie 标准库）及 Rust / C / Python / JS / C++ / Java / C# /
Go / Zig / Lua 等 tink 库语义一致；纯函数处理字符串（字节向量），IO
（stdin/stdout）由调用方负责。aardio 字符串天生是二进制安全的（可含 `'\0'`）。

## 安装

将 `lib/tink.aardio` 复制到应用根目录的 `lib` 目录下（用户库路径与命名空间
路径一致），脚本中 `import tink;` 即可使用。

## API（`tink` 命名空间）

| 函数 | 说明 |
| --- | --- |
| `crc32(data) -> 整数` | 字符串的 CRC32-IEEE。校验向量：`crc32("123456789") == 3453819686`（即 0xCBF43926） |
| `frame_encode(payload) -> 字符串` | 将 payload 编码为完整帧 `[len][payload][crc]` |
| `frame_next(bytes, pos) -> payload, next \| null` | 在 0 起始 `pos` 处解析一帧（校验 CRC）；越界/不匹配返回 null |
| `frame_skip(bytes, pos) -> next \| null` | 在 0 起始 `pos` 处跳过一帧（不复制、不校验）；越界返回 null |

所有位置为 0 起始字节偏移（aardio 字符串索引是 1 起始，`frame_next`/`frame_skip`
内部已做转换）。返回的 payload 是切片（副本）。

## 用法

```aardio
import console;
import tink;

frame = tink.frame_encode("hi");
payload, nextPos = tink.frame_next(frame, 0);
console.log(#frame);     // 10
console.dump(payload);   // "hi"
```

## 测试

在 aardio 开发环境中打开 `test_tink.aardio` 运行，或命令行：

```text
aardio test_tink.aardio
```

全部通过输出 `ALL 12 TESTS PASSED`。

## 跨语言

tink 帧协议各语言实现（API 语义与校验向量一致）：

| language | library |
| --- | --- |
| tie | `std/tink.tie` |
| Rust | `tink-rust`（tink crate） |
| C | `tink-c`（`tink.h` + `tink.c`） |
| Python | `tink-python`（`tink.py`） |
| JavaScript | `tink-js`（`tink.js` + `tink.d.ts`） |
| C++ | `tink-cpp`（`tink.hpp`） |
| Java | `tink-java`（`org.tielang.tink`） |
| C# | `tink-csharp`（namespace `Tink`） |
| Go | `tink-go`（package `tink`） |
| Zig | `tink-zig`（`tink.zig`） |
| Lua | this module（`tink-lua`） |
| aardio | this module（`tink-aardio`） |

## License

本仓库使用 **TIE-LANG Open Source License v1.1**，完整文本见 [LICENSE](LICENSE)。
This repository is distributed under the **TIE-LANG Open Source License v1.1** — see [LICENSE](LICENSE) for the full text.