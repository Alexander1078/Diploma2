# Diploma2
My diploma
detection_site/
├───object_detection/
│ ├───models.py
│ ├───forms.py
│ ├───views.py
│ ├───urls.py
│ ├───utils.py
│ ├───templates/ 
│ │ └───object_detection/
│ │ ├───base_generic.html 
│ │ ├───dashboard.html
│ │ ├───home.html
│ │ ├───login.html
│ │ └───register.html
│ └───static/ Bootstrap CSS
│ └───object_detection/
│ └───css/
│ └───js/
├───media/
│ └───processed_images/
detection_site/
│ ├───asgi.py
│ ├───settings.py
│ ├───urls.py
│ └───wsgi.py
from django.db import models
from django.contrib.auth.models import User

class ImageFeed(models.Model):
user = models.ForeignKey(User, on_delete=models.CASCADE)
image = models.ImageField(upload_to='images/')
timestamp = models.DateTimeField(auto_now_add=True)

class ProcessedImage(models.Model):
image_feed = models.OneToOneField(ImageFeed, on_delete=models.CASCADE)
processed_image = models.ImageField(upload_to='processed_images/')
detections = models.JSONField(default=list) 
from django import forms
from .models import ImageFeed

class ImageUploadForm(forms.ModelForm):
class Meta:
model = ImageFeed
fields = ['image']
import cv2
import numpy as np
import json
from django.shortcuts import render, redirect, get_object_or_404
from django.contrib.auth.decorators import login_required
from django.contrib.auth import authenticate, login, logout
from django.contrib.auth.forms import AuthenticationForm, UserCreationForm
from .forms import ImageUploadForm
from .models import ImageFeed, ProcessedImage
from .utils import detect_objects 


@login_required
def dashboard(request):
if request.method == 'POST':
form = ImageUploadForm(request.POST, request.FILES)
if form.is_valid():
image_feed = form.save(commit=False)
image_feed.user = request.user
image_feed.save()

 processed_image = process_image(image_feed.image.path)
 processed_img_obj = ProcessedImage.objects.create(image_feed=image_feed, processed_image=processed_image)
 return redirect('dashboard')
else:
form = ImageUploadForm()
images = ProcessedImage.objects.filter(image_feed__user=request.user)
return render(request, 'object_detection/dashboard.html', {'images': images, 'form': form})

def process_image(image_path):

 net = load_mobilenet_ssd()
 image = cv2.imread(image_path)
 detections = detect_objects(image, net)
 
 save_processed_image(image, detections)
 return image
from fancyimageprocessinglibrary import image_processing_method

@login_required
def process_image(request):
 
 source_image = request.FILES['file']
 
 processed_image = image_processing_method(source_image)
 return StreamingHttpResponse(processed_image, content_type="image/jpeg")
import cv2
import numpy as np
from PIL import Image
from io import BytesIO
from django.core.files.uploadedfile import InMemoryUploadedFile

def load_mobilenet_ssd():
 MobileNet SSD 
import os
import cv2

def load_mobilenet_ssd():
 prototxt = os.path.join(os.path.dirname(__file__), 'mobilenet_ssd_deploy.prototxt')
 caffemodel = os.path.join(os.path.dirname(__file__), 'mobilenet_iter_73000.caffemodel')
 net = cv2.dnn.readNetFromCaffe(prototxt, caffemodel)
return net


def detect_objects(image, net):



def save_processed_image(image, detections):

from django.urls import path
from . import views

urlpatterns = [
path('', views.home, name='home'),
path('register/', views.register, name='register'),
path('login/', views.login_view, name='login'),
path('logout/', views.logout_view, name='logout'),
path('dashboard/', views.dashboard, name='dashboard'),
path('delete//', views.delete_image, name='delete_image'),
]







Сначала я создаю саму структуру диплома (в него включены базовый шаблон,статический Boostrap CSS и сама структура сайта.Затем добавляю модели и формы Django.После создаю функции для выхода,регистрации и входа,не забываю об обработке фото с помощью mobile net SSD.После его загрузки,после сохраняю обработанное изображение,добавляю пару вспомагательных функций и маршрут для джанго
