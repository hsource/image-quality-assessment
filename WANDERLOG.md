# Image Quality Assessment

This model is taken from the the [repository](https://github.com/idealo/image-quality-assessment) that provides an implementation of an aesthetic and technical image quality model based on Google's research paper ["NIMA: Neural Image Assessment"](https://arxiv.org/pdf/1709.05424.pdf). You can find a quick introduction on their [Research Blog](https://research.googleblog.com/2017/12/introducing-nima-neural-image-assessment.html).

We use the model to assess quality inside of our app, using TensorFlowJS. Since the original model is run in Python, we need to export it for our use.

# How-to convert the model for Javascript use

Before starting all of this, clone the repository at https://github.com/hsource/image-quality-assessment instead of the original idealo one, since it has special code.

1.  Create a venv with python3 and enter it

```sh
cd ..
python3 -m venv image-quality-assessment
cd image-quality-assessment
source bin/activate
```

2.  Install dependencies

```sh
pip install -r src/requirements.txt
```

3.  Run the file to run the predictions once and write the model. This is based on the code in `entrypoint.predict.cpu.sh` and in `README.md`. The code to do this is shown below step 4.

4.  Convert the model to the format needed for TensorflowJS. Based on code from https://www.tensorflow.org/js/tutorials/conversion/import_keras

```sh
# Start in the project root dir
for model in technical aesthetic; do
  if [ "$model" = "technical" ]; then
    weights_file=weights_mobilenet_technical_0.11.hdf5
  else
    weights_file=weights_mobilenet_aesthetic_0.07.hdf5
  fi

  # Output the full model file
  cd src
  rm -f ../models/$model.h5
  python3 -m evaluater.predict \
    --base-model-name MobileNet \
    --weights-file ../models/MobileNet/$weights_file \
    --image-source tests/test_images/42039.jpg \
    --model-output-file ../models/$model.h5
  cd ..

  # Convert to TensorflowJS
  rm -f models/"$model"/*
  mkdir -p models/"$model"
  tensorflowjs_converter --input_format keras \
    --weight_shard_size_bytes 1000000000 \
    models/$model.h5 \
    models/"$model"
done
```

5.  Copy the files over to where you want to save them.
