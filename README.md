# 🧠 DIFFUSION MODEL — CORE MECHANISM

Your model has **only one job**:

> Learn how to **remove noise**.

That’s it.

Not image generation.
Not digit drawing.

Just **noise removal**.

Everything else comes from that.

---

# 🔁 TWO PROCESSES EXIST

```
FORWARD PROCESS   → destroy image
REVERSE PROCESS   → rebuild image
```

---

# 1️⃣ FORWARD DIFFUSION (DESTROYING DATA)

You start with a clean image:

```
x₀ = real MNIST digit
```

Example:

```
7
```

Now you add **tiny Gaussian noise** step by step.

---

## At each timestep t:

```
x₁ = image + small noise
x₂ = image + more noise
...
x_T = pure noise
```

After enough steps:

```
x_T ≈ N(0, I)
```

Pure random noise.

---

## Formula used:

```
x_t = √ᾱ_t · x₀  +  √(1 − ᾱ_t) · ε
```

Where:

* ε = random Gaussian noise
* ᾱₜ = cumulative product of noise schedule

Meaning:

* early t → mostly image
* late t → mostly noise

---

### 🔥 Important insight

You **never add noise repeatedly**.

You jump directly from x₀ → xₜ in one step.

That’s why training is fast.

---

# 2️⃣ WHAT THE MODEL LEARNS

The UNet **does NOT predict the image**.

It predicts:

```
ε  (the noise)
```

Why?

Because noise is:

* simple
* Gaussian
* stable

Predicting images directly is unstable.

---

## Training target:

```
model(x_t, t, label) ≈ ε
```

---

### During training:

You already know the noise because you generated it.

So you minimize:

```
Loss = MSE(predicted_noise, true_noise)
```

That’s it.

---

# 3️⃣ WHY TIME EMBEDDING EXISTS

The same noisy image can appear at different steps.

The model must know:

> “How noisy is this image?”

So you give it:

```
timestep t
```

Converted into a vector using **sinusoidal embeddings**.

Same idea as Transformers.

---

### Time embedding answers:

> “Are we at step 5 or step 195?”

---

# 4️⃣ WHY LABEL EMBEDDING EXISTS

You want:

```
digit 3
digit 7
digit 9
```

from the same noise distribution.

So you embed the class label:

```
label → vector
```

Then inject it into the UNet.

Now the model learns:

> “When label = 3, denoise like a 3.”

---

# 5️⃣ WHAT YOUR UNET IS ACTUALLY DOING

Input:

```
x_t      → noisy image
t_emb    → time embedding
label_emb → digit class
```

Output:

```
predicted_noise ε̂
```

---

## Architecture logic

```
Downsampling → understand structure
Bottleneck   → combine time + label
Upsampling   → reconstruct image
```

Skip connections preserve details.

---

# 6️⃣ REVERSE DIFFUSION (GENERATION)

This is where magic happens.

---

### Step 1

Start from pure noise:

```
x_T ~ N(0, I)
```

---

### Step 2

For t = T → 0:

```
x_{t-1} = denoise(x_t)
```

At every step:

* model predicts noise
* removes a bit of it
* adds small randomness

---

### Update equation:

```
x_{t−1} =
  1/√α_t ( x_t − β_t ε̂ / √(1 − ᾱ_t) )
  + σ_t z
```

This slowly reveals structure.

---

### What actually happens visually

```
random static
↓
blur
↓
digit shape
↓
clear digit
```

---

# 7️⃣ WHY THIS ACTUALLY GENERATES IMAGES

Because your model learned:

> “If something looks like this noise pattern,
> remove noise in THIS direction.”

Over millions of examples, it learns the **data distribution**.

---

# 8️⃣ WHY THIS IS NOT GAN

| GAN                 | Diffusion            |
| ------------------- | -------------------- |
| One-shot generation | Iterative generation |
| Unstable            | Very stable          |
| Hard to train       | Easy                 |
| Mode collapse       | No collapse          |
| Fast                | Slower               |

Diffusion trades speed for stability.

---

# 9️⃣ WHY STABLE DIFFUSION WORKS

Stable Diffusion is just:

```
your model
+ latent space
+ text conditioning
+ larger UNet
```

Nothing conceptually different.

---

# 🔥 FINAL MECHANISM SUMMARY

```
1. Take real image x₀
2. Add noise → x_t
3. Train UNet to predict noise ε
4. Give timestep embedding
5. Give label embedding
6. Reverse process removes noise step-by-step
7. Image emerges from randomness
```

---

# 🧠 IF WE UNDERSTAND THIS…

We can understand:

* DDPM
* Stable Diffusion
* Imagen
* DALL·E diffusion core
* All modern image generators

Only scale changes.

---

# 🎯 ONE-SENTENCE EXPLANATION

> A diffusion model learns how to gradually remove noise from random data until a structured image appears.

---

