# Images App

A web app for sharing images and finding ones that look alike.

> **University project, 2025.** We built this as a team of three (Amine, Anass and me) for our third-year Bachelor's (L3) software development project at the University of Bordeaux. My part was the AI side: the Python model, image classification and AI-based similarity search.

**Live demo:** https://images-app-production.up.railway.app/

---

## What it does

You upload a picture, and the app does three things with it:

1. **Stores it in a public gallery.** Anyone can browse and download it.
2. **Guesses what's in it.** A small neural network (CNN) trained on CIFAR-10 labels each image as one of ten classes: airplane, automobile, bird, cat, deer, dog, frog, horse, ship or truck.
3. **Finds similar images.** Pick any image and ask for the *N* closest ones. You can choose how "similar" is measured:
   - `histogram_2d` and `histogram_3d` compare the colour distributions.
   - `histogram_of_visual_words` compares shapes and textures with a *Bag of Visual Words* model. We extract local features from every image and group them into a visual vocabulary with k-means. Each image then becomes a histogram of those "visual words", and images are compared by Euclidean distance.

Once you've signed in (email/password or Google) and verified your email, you can also:

- upload images and get a public profile page;
- delete your own images;
- save favourites and organise images into albums;
- see the images you viewed recently;
- delete your account. Your uploaded images stay public unless you remove them first.

---

## How it's built

| Folder | What it is | Stack |
|---|---|---|
| `backend/` | Main REST API: stores images, computes descriptors, runs the classifier | Java 17, Spring Boot, PostgreSQL, BoofCV, TensorFlow Java |
| `frontend/` | The web interface | Vue 3, TypeScript, Vite, PrimeVue, Tailwind CSS, Pinia |
| `auth-backend/` | Small service for accounts and emails | Node.js, Express, Firebase Admin, Resend |
| `python/` | Scripts to train and test the CIFAR-10 model | TensorFlow / Keras |

The trained model (`new_cifar10_model`) and the visual dictionary (`visual_dictionary.dat`) are already included in `backend/src/main/resources`. You don't need to run the Python scripts to use the app.

---

## Running it locally

### Requirements

- Java 17+ (with `JAVA_HOME` set)
- Node.js 16+ and npm 8+
- A PostgreSQL database
- A Firebase project, for authentication

### Backend

Put your PostgreSQL connection details in `backend/src/main/resources/application.properties`, then run:

```bash
cd backend
./mvnw clean install
./mvnw spring-boot:run
```

Run the tests with `./mvnw test`.

### Frontend

```bash
cd frontend
npm install
npm run dev
```

For a production build, run `npm run build`.

### Auth service

Create a `.env` file in `auth-backend/` with `PORT`, `HOST`, `CLIENT_HOST`, `JWT_SECRET`, `RESEND_API_KEY` and `SDK` (your Firebase Admin credentials). Then run:

```bash
cd auth-backend
npm install
npm run dev
```

---

## API overview

| Method | Endpoint | What it does |
|---|---|---|
| `GET` | `/images` | List all images with their metadata |
| `POST` | `/images` | Upload an image (`multipart/form-data`). Returns `201 Created` |
| `DELETE` | `/images/{id}` | Delete an image. Returns `200 OK` |
| `GET` | `/images/{id}/similar?number=N&descriptor=D` | Get the `N` most similar images (default 5), each with a similarity score |

`descriptor` is required and must be `histogram_2d`, `histogram_3d` or `histogram_of_visual_words`.

---

## Team

Built by Amine, Anass and me.

I took care of the AI and Python side. I trained the CIFAR-10 classifier in Python, wired it into the backend so every uploaded image gets labelled, and built the AI-based similar-image search.

Built in 2025 at the University of Bordeaux, as part of the L3 software development project.
