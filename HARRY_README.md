# How-to convert the model for Javascript use

1. Create a venv with python3 and enter it

```sh
cd ..
python3 -m venv image-quality-assessment
cd image-quality-assessment
source bin/activate
```

2. Install dependencies

```sh
pip install -r src/requirements.txt
```

3. Run the file to run the predictions once and write the model. This is based on the code in `entrypoint.predict.cpu.sh` and in `README.md`

```sh
python3 evaluater.predict \
    --base-model-name MobileNet \
    --weights-file models/MobileNet/weights_mobilenet_technical_0.11.hdf5 \
    --image-source src/tests/test_images/42039.jpg \
    --model-output-file models/FullModel.h5
```

4. Convert the model. Based on code from https://www.tensorflow.org/js/tutorials/conversion/import_keras

```sh
mkdir -p models/FullModelJS
tensorflowjs_converter --input_format keras \
    --weight_shard_size_bytes 1000000000 \
    models/FullModel.h5 \
    models/FullModelJS
```