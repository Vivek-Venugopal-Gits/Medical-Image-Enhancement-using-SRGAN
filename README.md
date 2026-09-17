# MedEnhance — Medical Image Enhancement Using SRGAN

MedEnhance is a Django-based web application for enhancing medical images using a deep learning super-resolution model. The platform allows users to upload medical images, process them through an RRDBNet-based GAN model, compare the original and enhanced images, and view image enhancement metrics with an explainable-AI style description.

The application also includes user authentication, email-based OTP verification, password reset functionality, and hospital verification support.

> **Note:** MedEnhance is a research/academic project and should not be considered a clinical diagnostic systems or a replacement for professional medical judgment.

---

## Features

###  AI-Powered Image Enhancement

* Upload a medical image through the web interface.
* Preprocess the uploaded image before inference.
* Process the image using an RRDBNet-based super-resolution architecture.
* Generate an enhanced image.
* Preserve the original image dimensions when saving the processed result.

The enhancement pipeline loads the trained model weights, converts the input image into a tensor, performs inference using PyTorch, converts the output back into an image, and resizes it to the original dimensions.

###  Explainable Enhancement Metrics

The application compares the input and output images and calculates enhancement-related metrics including:

* Noise reduction
* Contrast improvement
* Enhancement explanation

Noise analysis uses the variance of the Laplacian, while contrast analysis uses the standard deviation of pixel intensities.

The results page presents the original image, enhanced image, downloadable output, explanation text, and visual enhancement metrics.

###  User Authentication

MedEnhance includes:

* User registration
* Login/logout
* Password reset
* Email-based OTP verification
* Custom user model
* User type management
* Hospital ID support
* Email verification state

New users are initially deactivated and must complete OTP verification before becoming active.

###  Hospital Verification

The custom user system supports hospital-related verification through:

* Hospital ID
* Hospital verification status
* Pending/approved/rejected states
* Admin notes
* Verification by an administrator

###  Administrative Features

The project contains an administrative interface with sections for:

* Dashboard
* User Management
* Hospital Verification
* Image Management
* Analytics
* Logout

The user-management interface displays usernames, emails, user types, and hospital verification status.

---

## Technology Stack

| Technology     | Purpose                        |
| -------------- | ------------------------------ |
| **Python**     | Core programming language      |
| **Django**     | Web application framework      |
| **PyTorch**    | Deep learning inference        |
| **BasicSR**    | RRDBNet architecture           |
| **OpenCV**     | Image processing and analysis  |
| **NumPy**      | Numerical image processing     |
| **Pillow**     | Image loading and manipulation |
| **SQLite**     | Development database           |
| **HTML/CSS**   | Web interface                  |
| **JavaScript** | Client-side interactions       |

The project uses Django with SQLite as its configured database backend.

---

## How It Works

```text
                    ┌─────────────────────┐
                    │      User           │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ Upload Medical      │
                    │ Image               │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ Image Preprocessing │
                    │ RGB + Resize        │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ RRDBNet / GAN       │
                    │ Model Inference     │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ Enhanced Image      │
                    └──────────┬──────────┘
                               │
                 ┌─────────────┴─────────────┐
                 ▼                           ▼
      ┌─────────────────────┐     ┌─────────────────────┐
      │ Original vs Enhanced│     │ Enhancement Metrics │
      │ Image Comparison    │     │ & Explanation       │
      └─────────────────────┘     └─────────────────────┘
```

The uploaded image is stored as the input image, passed through the GAN model, and the resulting image is written to the output directory before the results page is displayed.

---

## Project Structure

```text
vivek-venugopal-gits-medical-image-enhancement-using-srgan/
│
├── manage.py
├── requirements.txt
│
├── MedEnhance/
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── explainable_ai.py
│   ├── forms.py
│   ├── gan_model.py
│   ├── models.py
│   ├── tests.py
│   ├── urls.py
│   ├── views.py
│   │
│   └── templates/
│       ├── Admin Base.html
│       ├── Hospital Verification.html
│       ├── index.html
│       ├── login.html
│       ├── password_reset.html
│       ├── password_reset_complete.html
│       ├── password_reset_confirm.html
│       ├── password_reset_done.html
│       ├── password_reset_email.html
│       ├── reset_password_form.html
│       ├── results.html
│       ├── signup.html
│       ├── upload.html
│       ├── User Management.html
│       └── verify_otp.html
│
└── MedicalImageEnhancement/
    ├── __init__.py
    ├── asgi.py
    ├── settings.py
    ├── urls.py
    └── wsgi.py
```

---

## Core Components

### `gan_model.py`

Responsible for the AI enhancement pipeline.

The application constructs an RRDBNet model with:

* 3 input channels
* 3 output channels
* 64 feature channels
* 23 blocks
* Scale factor of 4

The trained weights are loaded from `net_g_latest.pth`.

The model supports different checkpoint formats by checking for `params_ema`, `params`, or a direct state dictionary.

### `explainable_ai.py`

Responsible for analyzing the difference between the original and enhanced images.

It uses:

* OpenCV
* NumPy
* Pillow
* Laplacian variance
* Pixel-intensity standard deviation

The module returns calculated enhancement values together with an explanation of the image-processing result.

### `models.py`

Defines the application's database models.

#### `CustomUser`

Extends Django's `AbstractUser` and includes:

```text
email
user_type
hospital_id
is_email_verified
is_hospital_verified
```

#### `OTPVerification`

Stores:

```text
user
otp
created_at
attempts
```

#### `HospitalVerification`

Stores:

```text
user
verification_status
admin_notes
verified_by
```

#### `UploadedImage`

Stores uploaded image information and upload timestamps.

---

## Application Flow

### 1. Registration

The user provides:

* Username
* Email
* Optional hospital ID
* Password

The registration form is built on Django's `UserCreationForm` and extends it with email and hospital ID fields.

### 2. OTP Verification

After registration:

```text
User Registration
       ↓
Account Created
       ↓
Account Deactivated
       ↓
OTP Generated
       ↓
OTP Sent Through Email
       ↓
User Enters OTP
       ↓
Account Activated
```

### 3. Image Upload

Users can upload an image through the upload interface. The uploaded image is stored and copied to the configured input-image directory before being passed to the enhancement pipeline.

### 4. AI Processing

```text
Input Image
     ↓
RGB Conversion
     ↓
Resize to 256 × 256
     ↓
Tensor Conversion
     ↓
RRDBNet Inference
     ↓
Tensor → Image
     ↓
Resize to Original Dimensions
     ↓
Output Image
```

### 5. Results

The results interface displays:

* Original image
* Enhanced image
* Download option
* AI enhancement explanation
* Noise reduction metric
* Contrast improvement metric

---

## Installation

### Prerequisites

Make sure the following are installed:

* Python 3.x
* pip
* Git
* Virtual environment support

Because the project performs PyTorch-based inference, an environment capable of installing the project's deep-learning dependencies is also required.

---

### 1. Clone the Repository

```bash
git clone <your-repository-url>
cd vivek-venugopal-gits-medical-image-enhancement-using-srgan
```

---

### 2. Create a Virtual Environment

#### Windows

```bash
python -m venv venv
venv\Scripts\activate
```

#### Linux/macOS

```bash
python3 -m venv venv
source venv/bin/activate
```

---

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

The project includes a `requirements.txt` file containing its Pytho
