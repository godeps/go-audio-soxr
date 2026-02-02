# go-audio-soxr（中文）

[English README](README.md)

基于 [libsoxr](https://sourceforge.net/projects/soxr/)（SoX Resampler）实现的纯 Go 高质量音频重采样库，**无 CGO 依赖**，跨平台可用。

## 特性

- **100% 纯 Go** - 无需 C 库或 CGO
- **跨平台** - Linux/macOS/Windows/ARM/WebAssembly
- **多档质量预设** - 从低延迟到高质量母带
- **可选精度** - float32/float64，类型安全 SIMD
- **多段多相 FIR** - 高效多级重采样架构
- **Kaiser 窗** - 优化的阻带衰减
- **SIMD 加速** - AVX2/SSE（依赖 [tphakala/simd](https://github.com/tphakala/simd)）
- **多声道支持** - 最高 256 通道
- **并行处理** - 立体声约 1.7x，加速更多声道
- **流式 API** - 分块处理，状态可复用

## 安装

```bash
go get github.com/godeps/go-audio-soxr
```

需要 Go 1.25 或更高版本。

## 快速开始

### 单次重采样

```go
package main

import (
    "fmt"
    "log"

    resampling "github.com/godeps/go-audio-soxr"
)

func main() {
    // 44.1kHz -> 48kHz
    input := generateSineWave(44100, 1000, 1.0)

    output, err := resampling.ResampleMono(input, 44100, 48000, resampling.QualityHigh)
    if err != nil {
        log.Fatal(err)
    }

    fmt.Printf("Resampled %d samples to %d samples\n", len(input), len(output))
}
```

### 流式重采样

```go
package main

import (
    "log"

    resampling "github.com/godeps/go-audio-soxr"
)

func main() {
    config := &resampling.Config{
        InputRate:  44100,
        OutputRate: 48000,
        Channels:   2,
        Quality:    resampling.QualitySpec{Preset: resampling.QualityHigh},
    }

    r, err := resampling.NewResampler(config)
    if err != nil {
        log.Fatal(err)
    }

    // 使用 r.Process / r.ProcessMulti 分块处理
    _ = r
}
```

## 许可证

本项目采用 **MIT License**。

## 参考与致谢

本库基于 libsoxr（LGPL-2.1）实现，其多段重采样架构、滤波器设计与预设参数等参考了原实现思路。
