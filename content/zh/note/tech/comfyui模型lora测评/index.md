---
private: false
featured: false
title: "comfyui模型lora测评"
authors:
  - admin
tags:
categories:
  - tech
summary:
---
{{< toc mobile_only=true is_open=true >}}


提示词：户外摄影,美女写真,夏天白色穿搭,长马尾,自然,微风,头发凌乱美,灵动转身,街道的路边,松弛感,近景,水平角度,眼睛特写,广角镜头,曝光过度,层次感,柔和侧光,光影对比,柔焦,胶片颗粒感,日系清新风,冷调,镜头美学,电影质感,高级感,大师级作品,

| 模型                                            | 模型大小    | lora                                 | lora大小  | 时间      | 图                                               | 评分5 | 选择  |
| --------------------------------------------- | ------- | ------------------------------------ | ------- | ------- | ----------------------------------------------- | --- | --- |
| qwen-image-Q4.gguf                            | 11.0 GB | 无                                    | 0       | 145.55s | ![](assets/Pasted%20image%2020251030202521.png) | 2   |     |
| svdq-fp4_r32-qwen-image-lightningv1.1-8steps  | 11.1 GB | 无                                    |         | 76.66s  | ![](assets/Pasted%20image%2020251030212904.png) | 3.5 |     |
| svdq-fp4_r128-qwen-image-lightningv1.1-8steps | 12.1 GB | 无                                    |         | 79.29s  | ![](assets/Pasted%20image%2020251030213407.png) | 3.5 |     |
| qwen-image-Q4.gguf                            | 11.0 GB | Qwen _ 极致真实写实人像摄影_v1.0               | 1.1G    | 205.78s | ![](assets/Pasted%20image%2020251030203348.png) | 3   |     |
| svdq-fp4_r32-qwen-image-lightningv1.1-8steps  | 11.1 GB | Qwen _ 极致真实写实人像摄影_v1.0               | 1.1G    | 78.41s  | ![](assets/Pasted%20image%2020251030213810.png) | 3.7 |     |
| svdq-fp4_r128-qwen-image-lightningv1.1-8steps | 12.1 GB | Qwen _ 极致真实写实人像摄影_v1.0               | 1.1G    | 410.63s | ![](assets/Pasted%20image%2020251030214659.png) | 3.7 |     |
| qwen-image-Q4.gguf                            | 11.0 GB | Qwen 更加真实_ 生图直出_ 写实人像_v1.5           | 562Mb   | 201.03s | ![](assets/Pasted%20image%2020251030203940.png) | 5   |     |
| svdq-fp4_r32-qwen-image-lightningv1.1-8steps  | 11.1 GB | Qwen 更加真实_ 生图直出_ 写实人像_v1.5           | 562Mb   | 77.02s  | ![](assets/Pasted%20image%2020251030215312.png) | 5   | ✅   |
| svdq-fp4_r128-qwen-image-lightningv1.1-8steps | 12.1 GB | Qwen 更加真实_ 生图直出_ 写实人像_v1.5           | 562Mb   | 84.75s  | ![](assets/Pasted%20image%2020251030215751.png) | 5   |     |
| qwen-image-Q4.gguf                            | 11.0 GB | Qwen-Image｜摄影风格化｜写真摄影｜TG15_7_tg15_07 | 225 MB  | 143.03s | ![](assets/Pasted%20image%2020251030204341.png) | 2   |     |
| svdq-fp4_r32-qwen-image-lightningv1.1-8steps  | 11.1 GB | Qwen-Image｜摄影风格化｜写真摄影｜TG15_7_tg15_07 | 225 MB  | 85.58s  | ![](assets/Pasted%20image%2020251030220223.png) | 3.5 |     |
| svdq-fp4_r128-qwen-image-lightningv1.1-8steps | 12.1 GB | Qwen-Image｜摄影风格化｜写真摄影｜TG15_7_tg15_07 | 225 MB  | 81.41s  | ![](assets/Pasted%20image%2020251030220603.png) | 3.5 |     |
| qwen-image-Q4.gguf                            | 11.0 GB | Qwen-人像质感提升_v1.0                     | 225 MB  | 142.41s | ![](assets/Pasted%20image%2020251030204739.png) | 2   |     |
| svdq-fp4_r32-qwen-image-lightningv1.1-8steps  | 11.1 GB | Qwen-人像质感提升_v1.0                     | 225 MB  | 79.12s  | ![](assets/Pasted%20image%2020251030221141.png) | 4.5 | ✅   |
| svdq-fp4_r128-qwen-image-lightningv1.1-8steps | 12.1 GB | Qwen-人像质感提升_v1.0                     | 225 MB  | 60.76s  | ![](assets/Pasted%20image%2020251030220850.png) | 4   |     |
| qwen-image-Q4.gguf                            | 11.0 GB | Qwen-极致真实写实人像-小红书时尚摄影-lora_v1.0      | 281 MB  | 200.12s | ![](assets/Pasted%20image%2020251030205150.png) | 3.5 |     |
| svdq-fp4_r32-qwen-image-lightningv1.1-8steps  | 11.1 GB | Qwen-极致真实写实人像-小红书时尚摄影-lora_v1.0      | 281 MB  | 60.22s  | ![](assets/Pasted%20image%2020251030221431.png) | 3.5 |     |
| svdq-fp4_r128-qwen-image-lightningv1.1-8steps | 12.1 GB | Qwen-极致真实写实人像-小红书时尚摄影-lora_v1.0      | 281 MB  | 79.85s  | ![](assets/Pasted%20image%2020251030221726.png) | 3   |     |
| qwen-image-Q4.gguf                            | 11.0 GB | Qwen-真实人像小红书风格_Qwen小红书真实风格           | 562 MB  | 202.22s | ![](assets/Pasted%20image%2020251030205542.png) | 3.5 |     |
| svdq-fp4_r32-qwen-image-lightningv1.1-8steps  | 11.1 GB | Qwen-真实人像小红书风格_Qwen小红书真实风格           | 562 MB  | 80.90s  | ![](assets/Pasted%20image%2020251030222534.png) | 3.7 |     |
| svdq-fp4_r128-qwen-image-lightningv1.1-8steps | 12.1 GB | Qwen-真实人像小红书风格_Qwen小红书真实风格           | 562 MB  | 61.08s  | ![](assets/Pasted%20image%2020251030222324.png) | 3.5 |     |
| qwen-image-Q4.gguf                            | 11.0 GB | Qwen_情绪滤镜_V20                        | 225 MB  | 142.69s | ![](assets/Pasted%20image%2020251030205847.png) | 2   |     |
| svdq-fp4_r32-qwen-image-lightningv1.1-8steps  | 11.1 GB | Qwen_情绪滤镜_V20                        | 225 MB  | 58.65s  | ![](assets/Pasted%20image%2020251030222719.png) | 4   |     |
| svdq-fp4_r128-qwen-image-lightningv1.1-8steps | 12.1 GB | Qwen_情绪滤镜_V20                        | 225 MB  | 86.25s  | ![](assets/Pasted%20image%2020251030223059.png) | 4   |     |
| qwen-image-Q4.gguf                            | 11.0 GB | 千问-学生感_氛围_摄影_千问                      | 1.09 GB | 204.23s | ![](assets/Pasted%20image%2020251030210423.png) | 4.5 |     |
| svdq-fp4_r32-qwen-image-lightningv1.1-8steps  | 11.1 GB | 千问-学生感_氛围_摄影_千问                      | 1.09 GB | 80.96s  | ![](assets/Pasted%20image%2020251030225023.png) | 4.5 | ✅   |
| svdq-fp4_r128-qwen-image-lightningv1.1-8steps | 12.1 GB | 千问-学生感_氛围_摄影_千问                      | 1.09 GB | 854.5s  | ![](assets/Pasted%20image%2020251030224830.png) | 5   |     |
