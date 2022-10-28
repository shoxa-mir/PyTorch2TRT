# PyTorch model to TensorRT engine conversion

YoloV7 is the SOTA object detection model at high inference speed. Below are instructions to convert model from PyTorch to TensorRT.

## Hardware Requirements
- Target device to deploy TensorRT model (TensorRT is gpu-specific framework which requires to convert model on the exact same device that it will be deployed)
- Windows, Linux or MacOS device to handle PyTorch-->ONNX conversion (optional)

## Package Requirements Installation

Use the package manager [pip](https://pip.pypa.io/en/stable/) to install requirements.

```bash
pip install --upgrade setuptools pip --user
pip install --ignore-installed PyYAML
pip install Pillow

#Packages required to handle PyTorch-->ONNX
pip install protobuf<4.21.3
pip install onnxruntime-gpu
pip install onnx>=1.9.0
pip install onnx-simplifier>=0.3.6 --user

#Packages required to handle ONNX-->TRT (on target device)
pip install nvidia-pyindex
pip install --upgrade nvidia-tensorrt # TensorRT 8.2.1 or higher required for NMS plugin
pip install pycuda
```

## Usage
Clone WonKinYou's YoloV7 official repo and TensorRT conversion repo from [Github](https://github.com/)
```bash
git clone https://github.com/WongKinYiu/yolov7.git
cd yolov7/
git clone https://github.com/shoxa-mir/tensorrt-python.git
```
Place trained weights to yolov7/ folder or download pre-trained weights: [yolov7-tiny](https://github.com/WongKinYiu/yolov7/releases/download/v0.1/yolov7-tiny.pt), [yolov7](https://github.com/WongKinYiu/yolov7/releases/download/v0.1/yolov7.pt) from official [release](https://github.com/WongKinYiu/yolov7/releases/download/v0.1/yolov7-tiny.pt).

```bash
wget https://github.com/WongKinYiu/yolov7/releases/download/v0.1/yolov7-tiny.pt (optional)
# export temporary ONNX model for TensorRT converter(w/ NMS)
python export.py --weights ./yolov7-tiny.pt --grid --iou-thres 0.65 --conf-thres 0.35 --img-size 640 640  --end2end --simplify --topk-all 100 
# export temporary ONNX model for TensorRT converter(w/o NMS)
python export.py --weights ./yolov7-tiny.pt --grid --iou-thres 0.65 --conf-thres 0.35 --img-size 640 640 --simplify --topk-all 100 
ls
```

After successful conversion to ONNX model last step is to convert to TensorRT engine. And do inference to check the correct conversion.

```bash
cd tensorrt-python
python export.py -o ../yolov7-tiny.onnx -e ./yolov7-tiny-nms.trt -p fp16
# w/ NMS
python trt.py -e yolov7-tiny.trt  -i src/1.jpg -o yolov7-tiny-1.jpg --end2end
# w/o NMS
python trt.py -e yolov7-tiny.trt  -i src/1.jpg -o yolov7-tiny-1.jpg

```

## Contributing

## License

