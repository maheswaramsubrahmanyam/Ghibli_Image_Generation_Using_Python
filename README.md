
![ghibli_portrait_pic](https://github.com/user-attachments/assets/7fe6e45e-7193-4c8e-b427-d66e89b5053a)

Here’s a clean and well-structured `README.md` file for your Ghibli-style image generation project. It includes an introduction, setup instructions, usage guide, and output steps—all formatted for clarity on GitHub or any project repository.

---

```markdown
# 🌸 Ghibli-Style Image Generator (Sustainable AI with Python)

This project allows you to transform any image into a beautiful **Studio Ghibli-style painting** using the power of **Stable Diffusion** locally on your machine or Google Colab — minimizing water wastage associated with cloud AI tools.

---

## 🎯 Features

- Generate **anime-style Ghibli portraits** from your own photos.
- Uses `StableDiffusionImg2ImgPipeline` from the `diffusers` library.
- Optimized for GPU and memory-efficient execution.
- Environmentally conscious: reduce server-side AI image generation and save water 💧.

---

## 🛠️ Requirements

Before running the code, make sure you have the following Python packages installed (if using locally):

```bash
pip install diffusers transformers accelerate torch pillow matplotlib
```

Or you can run the notebook directly in **Google Colab**, which supports GPU.

---

## 🚀 How It Works

1. Loads the **Ghibli Diffusion** model: `nitrosocke/Ghibli-Diffusion`.
2. Uploads a user image (`.jpg`, `.png`, etc.).
3. Accepts a **stylization strength** (range: 0.3 to 0.8).
4. Generates a Ghibli-style version using Stable Diffusion's image-to-image pipeline.
5. Displays the original and transformed image side by side.
6. Allows downloading the final stylized image.

---

## 📸 How to Use

### Step 1: Upload an Image

Once the script starts, it will ask you to upload a photo.

```bash
Please upload your image file:
```

### Step 2: Input Stylization Strength

After uploading, you’ll be prompted to enter a strength value:

```bash
Enter stylization strength (0.3-0.8, recommended 0.6):
```

- `0.3` = minimal stylization
- `0.8` = high Ghibli-style effect

### Step 3: View and Download

- The generated image will be displayed on screen.
- A `.png` version will automatically download.
- Filename format: `ghibli_portrait_<your_image_name>.png`

---

## 📊 Example Output

| Original Image | Ghibli-Stylized Output |
|----------------|------------------------|
| ![Original](https://github.com/user-attachments/assets/73bcc4d2-738a-4821-a013-c038ff504053) | ![Ghibli](https://github.com/user-attachments/assets/5db4d247-71f8-4926-b4c7-8c7f99d85d6d)

 |

> *(Note: Add real images here if you’re publishing the repo)*

---

## 🌍 Sustainability Note

> Generating AI images on cloud platforms consumes **up to 0.5 liters per image**, due to server cooling.

Running 20 images/second on cloud:
- 1,728,000 images/day
- 💧 **864,000 liters/day** of water wasted

By generating images **locally**, we reduce our **carbon and water footprint** significantly.

---

## 🤝 Credits

- Model: [`nitrosocke/Ghibli-Diffusion`](https://huggingface.co/nitrosocke/Ghibli-Diffusion)
- Powered by: Hugging Face `diffusers`, PyTorch, PIL, Matplotlib

---

## 📬 Contact

**Author**: Maheswaram M V Subrahmanyam  
Connect: [LinkedIn](https://www.linkedin.com/in/maheswaram-subrahmanyam-361238275/)

---

## 📌 License

This project is licensed under the [MIT License](LICENSE).

---
```

---
from diffusers import StableDiffusionImg2ImgPipeline
import torch
from PIL import Image
import io
from google.colab import files
import matplotlib.pyplot as plt
import time

# Load model function
def load_model():
    model_id = "nitrosocke/Ghibli-Diffusion"  # Correct model ID
    dtype = torch.float16 if torch.cuda.is_available() else torch.float32
    print("Loading model...")
    pipe = StableDiffusionImg2ImgPipeline.from_pretrained(model_id, torch_dtype=dtype)
    pipe.to("cuda" if torch.cuda.is_available() else "cpu")
    pipe.enable_attention_slicing()  # Optimize memory usage
    print("Model loaded!")
    return pipe

# Function to generate Ghibli-style image
def generate_ghibli_image(image, pipe, strength):
    image = image.convert("RGB")
    image = image.resize((512, 512))  # Ensure proper size

    prompt = "Ghibli-style anime painting, soft pastel colors, highly detailed, masterpiece"

    print("Generating image...")
    start_time = time.time()
    result = pipe(prompt=prompt, image=image, strength=strength).images[0]
    print(f"Image generated in {time.time() - start_time:.2f} seconds!")
    return result

# Check for GPU
gpu_info = "GPU is available!" if torch.cuda.is_available() else "Warning: GPU not available. Processing will be slow."
print(gpu_info)

# Load the model
pipe = load_model()

# Upload image section
print("Please upload your image file:")
uploaded = files.upload()

if uploaded:
    file_name = list(uploaded.keys())[0]
    image = Image.open(io.BytesIO(uploaded[file_name]))

    # Display original image
    plt.figure(figsize=(5, 5))
    plt.imshow(image)
    plt.title("Original Image")
    plt.axis('off')
    plt.show()

    # Ask for strength input with error handling
    while True:
        try:
            strength = float(input("Enter stylization strength (0.3-0.8, recommended 0.6): "))
            strength = max(0.3, min(0.8, strength))  # Clamp between 0.3 and 0.8
            break
        except ValueError:
            print("Invalid input. Please enter a number between 0.3 and 0.8.")

    # Generate and display the result
    result_img = generate_ghibli_image(image, pipe, strength)

    plt.figure(figsize=(5, 5))
    plt.imshow(result_img)
    plt.title("Ghibli Portrait")
    plt.axis('off')
    plt.show()

    # Save the output image and offer download
    output_filename = f"ghibli_portrait_{file_name}"
    result_img.save(output_filename)
    files.download(output_filename)
    print(f"Image saved as {output_filename} and download initiated!")
else:
    print("No file was uploaded. Please run the cell again and upload an image.")

 `.ipynb` file version of the code too!
