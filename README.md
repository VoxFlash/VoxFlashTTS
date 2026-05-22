# ttsv2

业界推理速度最快的语音克隆系统，低端显卡，端侧部署

fast tts (ZH EN) lightweight

https://voxflash.github.io/

![ttsv2](images/20260327-155944.png)

http:127.0.0.1:8000/demo.html

## Installation

cuda >= 12.3.2

docker pull berlinisaiah/ttsv2:v1

docker container run -it --gpus all --mount type=bind,source=$(pwd)/resources,target=/app/resources -p 8000:8000 berlinisaiah/ttsv2:v1

docker container run -d --gpus all --mount type=bind,source=$(pwd)/resources,target=/app/resources -p 8000:8000 berlinisaiah/ttsv2:v1
