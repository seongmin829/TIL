from django.urls import path
from . import views

 
urlpatterns = [
    path('', views.index),
    #path('<name>/', views.index2),
    #path('<int:pk>/detail', views.index3),
    path('list/', views.list, name="list"),
    path('<int:pk>/detail/', views.detail),

    path('list2/', views.PostView.as_view()),
    path('login/', views.LoginView.as_view(), name="login"),
]