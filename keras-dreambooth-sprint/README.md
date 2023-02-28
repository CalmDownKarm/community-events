
![](https://huggingface.co/spaces/keras-dreambooth/README/resolve/main/dreambooth.png)

# Keras Dreambooth event 🤗 

Welcome to Keras Dreambooth event!

TODO: Add some introduction here

This document summarises all the relevant information required for the event 📋. Please read it thoroughly.

## Table of Contents

* [Introduction](#introduction)
* [Important Dates](#important-dates)
* [Getting Started](#getting-started--)
* [Submission Categories](#submission-categories)
* [Compute](#compute)
* [Model Training](#model-training)
* [Model Hosting](#model-hosting)
* [Building Demo](#building-demo)
* [Sprint Prizes](#sprint-prizes)

## Introduction

Dreambooth is a fine-tuning technique to teach new visual concepts to text-conditioned Diffusion models with just 3-5 images. With Dreambooth, you could generate funny and realistic images of your dog, yourself and any concept with few images using Stable Diffusion. DreamBooth was proposed in [DreamBooth: Fine Tuning Text-to-Image Diffusion Models for Subject-Driven Generation](https://arxiv.org/abs/2208.12242) by Ruiz et al.

We will be training Dreambooth models using KerasCV, sharing the models and building demos using the models.

We will:
1. Fine-tune Stable Diffusion on your chosen concept using Dreambooth with KerasCV,
2. Push the model to the Hugging Face Hub,
3. Fill the model card,
4. Build a demo on top of the model.


## Important Dates

TODO: Add official dates


## Getting Started  🚀 

To get started, join us in [hf.co/join/discord](http://hf.co/join/discord) and take the role #open-source, and meet us in #keras-working-group channel. 

We will be hosting our demos in this organization on Hugging Face Hub: [keras-dreambooth](https://huggingface.co/keras-dreambooth), send a request to join [here](https://huggingface.co/organizations/keras-dreambooth/share/RMocthadPgpxxUDHtAesrbBzieDLgUfPmv) if you’d like to make a submission 🙂

## Submission Categories

There are 3 different categories in which you can participate:
- Nature and Animals (`nature`)
- Sci-fi/Fantasy Universes (`sci-fi`)
- Consentful (`consentful`): Partner up with an artist to fine-tune on their style, with their consent! Make sure to include a reference to the artist’s express consent (e.g. a tweet) in your model card.
- Wild Card (`wild-card`): If your submission belongs to any category that is not above, feel free to tag it with wild-card so we can evaluate it out of that category.

Add the category with their IDs to the model cards for submission and add `keras-dreambooth` to model card metadata in tags section. Here's an example [model card](https://huggingface.co/spaces/keras-dreambooth/example-submission/blob/main/README.md). All the submissions will be populated [in this leaderboard](https://huggingface.co/spaces/keras-dreambooth/leaderboard) and ranked according to likes on a given Space to determine the winners.

Couple of inspirations for you: 

TODO: it might be confusing to show examples not using keras. maybe worth for us to build some of the examples and share them
1. Lowpoly World: This [model](https://huggingface.co/MirageML/lowpoly-world) generates lowpoly worlds 🤯🌍
2. Future Diffusion: This [model](https://huggingface.co/nitrosocke/Future-Diffusion) generates images in futuristic sci-fi concepts 🤖
3. Fantasy sword: This [model](https://huggingface.co/MirageML/fantasy-sword) generates swords for fantasy themed games 🧙‍♂️

## Compute

[ADD DETAILS ABOUT COMPUTE SPONSORSHIP]

## Model Training

You can find the notebook here and adjust it according to your own dataset 👇  

TODO: The notebook below is a Google Drive. We should add it to the GitHub repo

[Link to notebook](https://colab.research.google.com/drive/1Bh49YBYgaNc3X5aRzaMZNska4ErvU3lJ?usp=sharing). If you need more pointers on Dreambooth implementation with Keras, you can check out [this repository](https://github.com/sayakpaul/dreambooth-keras).

Note/q: having a colab and then a repo could maybe be confusing?
Note: maybe let's change the repo_id in push_to_hub_keras to push to the organization.

## Model Hosting

At the end of this notebook you will see a section dedicated for hosting and a separate one for inference. We will be using the `huggingface_hub` library’s Keras-specific model pushing and loading functions: `push_to_hub_keras` and `from_pretrained_keras` . We will first push the model using `push_to_hub_keras`. After the model is pushed, you will see the model is hosted with a model card like below 👇 

![Repository](https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/dreamboothrepo.png)

To version the models better, enable discoverability and reproducibility, we will fill the model card. Click `Edit model card` . We will first fill the Metadata section of the model card. If your model is trained with a dataset from the Hugging Face Hub, you can fill the datasets section with the dataset. We will provide fill `pipeline_tag` with `Ttext-to-image` and pick a license for our model. 

![Metadata](https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/dreambooth-etadata.png)

Then, we will fill the markdown part. Hyperparameters and plot are automatically generated so we can write a short explanation for description, intended use and dataset.

You can find the example repository below 👇 

[keras-dreambooth/dreambooth_diffusion_model · Hugging Face](https://huggingface.co/keras-dreambooth/dreambooth_diffusion_model)
TODO: also convert this repo to diffusers so the API is enabled
TODO: should we have proper metadata in this repo as well? (e.g. missing the submission or keras event tags)
TODO: mention which other tags need to be added


### Dreambooth Diffusers Integration with KerasCV

Inference and deployment options of `KerasCV` are limited, which is when the `diffusers` library comes to the rescue. You can easily convert a `KerasCV` model into a `diffusers` one and use `diffusers`’ pipelines to perform inference. You can automatically open a Pull Request to convert your model using [this Space](https://huggingface.co/spaces/sayakpaul/convert-kerascv-sd-diffusers).

You can get more information [here](https://huggingface.co/docs/diffusers/main/en/using-diffusers/kerascv).

TODO: Clarify if it's a requirement or not to do this

```python
from diffusers import StableDiffusionPipeline

# checkpoint of the converted Stable Diffusion from KerasCV
model_ckpt = "sayakpaul/text-unet-dogs-kerascv_sd_diffusers_pipeline"
pipeline = StableDiffusionPipeline.from_pretrained(model_ckpt)
pipeline.to("cuda")

unique_id = "sks"
class_label = "dog"
prompt = f"A photo of {unique_id} {class_label} in a bucket"
image = pipeline(prompt, num_inference_steps=50).images[0]
```

## Building demo

We will use Gradio to build our demos for the models we have trained. With `Interface` class it’s straightforward 👇

```python
import gradio as gr

# write function for inference
def infer(prompt):
# assume you loaded the model
    generated_images = sd_dreambooth_model.text_to_image( # TODO: What's sd_dreambooth_model? How to load it?
        prompt, batch_size=4
    )
    return generated_images 
    
output = gr.Gallery(label="Outputs").style(grid=(2,2))

# pass function, input type for prompt, the output for multiple images
gr.Interface(infer, inputs=["text"], outputs=[output]).launch()
```
Q: should the part above be in the Collab already so it's end-to-end?

You can check out `app.py` file of the application below and repurpose it for your model!

[Dreambooth Submission - a Hugging Face Space by keras-dreambooth](https://huggingface.co/spaces/keras-dreambooth/example-submission)

This app generates images of a corgi 🐶 

![Dreambooth App](https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/dreambooth_corgi.png)

### Hosting on Spaces

After our application is written, we can create a Hugging Face Space to host our app. You can go to [huggingface.co](http://huggingface.co), click on your profile on top right and select “New Space”.

![New Space](https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/new_space.png)

We can name our Space, pick a license and select Space SDK as “Gradio”. 

![Space Configuration](https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/space_config.png)

After creating the Space, you can use either the instructions below to clone the repository locally, adding your files and push, OR, graphical interface to create the files and write the code in the browser.

TODO: image below is broken
![Screenshot 2023-02-01 at 14.04.09.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1c332e06-c3ac-4e4d-b1af-5a5836cded7c/Screenshot_2023-02-01_at_14.04.09.png)

To upload your application file, pick “Add File” and drag and drop your file.

![New Space Landing](https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/space_landing.png)

Lastly, we have to create a file called `requirements.txt` and add requirements of Dreambooth project like below: 

```
keras-cv
tensorflow
huggingface-hub
```

And your app should be up and running!

## Sprint Prizes

We will pick three winners among the applications submitted according to the number of likes given to a Space in a given category. 

🛍️ First place will win a 100$ voucher on [hf.co/shop](http://hf.co/shop) or one year subscription to [Hugging Face Pro](https://huggingface.co/pricing#pro)

🛍️ Second place will win a 50$ voucher on [hf.co/shop](http://hf.co/shop) or [the book](https://transformersbook.com/) “Natural Language Processing with Transformers”

🛍️ Third place will win a 30$ voucher on [hf.co/shop](http://hf.co/shop) or three months subscription to [Hugging Face Pro](https://huggingface.co/pricing#pro)