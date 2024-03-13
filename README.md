# SAM-HQ Exporter

> **Lightly adapted from [SAM Exporter]()**

Exporting [Segment Anything in High Quality](https://github.com/facebookresearch/segment-anything) models to different formats.

The [Segment Anything in High Quality repository](https://github.com/facebookresearch/segment-anything) does not have a way to export **encoder** to ONNX format. The authors follow the original SAM's method that only export the decoder. For that reason, [@vietanhdev](https://github.com/vietanhdev) created a tool to export the original SAM model and I lightly modified it to export SAM-HQ.


**Supported models:**

- ViT-B HQ-SAM
- ViT-L HQ-SAM
- ViT-H HQ-SAM

## Installation

```bash
git clone https://github.com/vietanhdev/samexporter
cd samexporter
pip install -e .
```

## Usage

- Download all models from [Segment Anything in High Quality](https://github.com/SysCV/sam-hq?tab=readme-ov-file#model-checkpoints) repository (*.pth).


```text
original_models
   + sam_hq_vit_b.pth
   + sam_hq_vit_h.pth
   + sam_hq_vit_l.pth
   ...
```

- Convert encoder VIT-H HQ-SAM to ONNX format:

```bash
python -m samexporter.export_encoder --checkpoint original_models/sam_hq_vit_h.pth \
    --output output_models/sam_hq_vit_h.encoder.onnx \
    --model-type vit_h \
    --quantize-out output_models/sam_hq_vit_h.encoder.quant.onnx \
    --use-preprocess
```

- Convert decoder SAM-H to ONNX format:

```bash
python -m samexporter.export_decoder --checkpoint original_models/sam_hq_vit_h.pth \
    --output output_models/sam_hq_vit_h.decoder.onnx \
    --model-type vit_h \
    --quantize-out output_models/sam_hq_vit_h.decoder.quant.onnx \
    --return-single-mask
```

Remove `--return-single-mask` if you want to return multiple masks.

- Inference using the exported ONNX model:

```bash
python -m samexporter.inference \
    --encoder_model output_models/sam_hq_vit_h.encoder.onnx \
    --decoder_model output_models/sam_hq_vit_h.decoder.onnx \
    --image images/truck.jpg \
    --prompt images/truck_prompt.json \
    --output output_images/truck.png \
    --show
```

![truck](https://raw.githubusercontent.com/vietanhdev/samexporter/main/sample_outputs/truck.png)

```bash
python -m samexporter.inference \
    --encoder_model output_models/sam_hq_vit_h.encoder.onnx \
    --decoder_model output_models/sam_hq_vit_h.decoder.onnx \
    --image images/plants.png \
    --prompt images/plants_prompt1.json \
    --output output_images/plants_01.png \
    --show
```

![plants_01](https://raw.githubusercontent.com/vietanhdev/samexporter/main/sample_outputs/plants_01.png)

```bash
python -m samexporter.inference \
    --encoder_model output_models/sam_hq_vit_h.encoder.onnx \
    --decoder_model output_models/sam_hq_vit_h.decoder.onnx \
    --image images/plants.png \
    --prompt images/plants_prompt2.json \
    --output output_images/plants_02.png \
    --show
```

![plants_02](https://raw.githubusercontent.com/vietanhdev/samexporter/main/sample_outputs/plants_02.png)


**Short options:**

In the original [SAM Exporter]() repo, there are some helper scripts that I didn't change, so I don't include them here. Their usage are as follows:

- Convert all Meta's models to ONNX format:

```bash
bash convert_all_meta_sam.sh
```

- Convert MobileSAM to ONNX format:

```bash
bash convert_mobile_sam.sh
```

## Tips

- Use "quantized" models for faster inference and smaller model size. However, the accuracy may be lower than the original models.
- SAM-B is the most lightweight model, but it has the lowest accuracy. SAM-H is the most accurate model, but it has the largest model size. SAM-M is a good trade-off between accuracy and model size.

## AnyLabeling

The original package was developed by [@vietanhdev](https://github.com/vietanhdev) for auto labeling feature in [AnyLabeling](https://github.com/vietanhdev/anylabeling) project. However, you can use it for other purposes.

[![](https://user-images.githubusercontent.com/18329471/236625792-07f01838-3f69-48b0-a12e-30bad27bd921.gif)](https://youtu.be/5qVJiYNX5Kk)

I myself am using it to develop the [Subvisor](https://github.com/Paulo-Rozatto/subvisor) project. It still is a work in progress. 

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
