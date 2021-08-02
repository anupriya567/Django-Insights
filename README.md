# Django-Insights

## 1). Reverse function where and why to use?

Use reverse instead of hardcoded uls. 

Suppose we are using  get_absolute_url() method with \users and then if we change the path to \members then the application will break.
But if we use reverse of url name
At any moment we are free to change the path and the call will not break because our reverse call can still take us to the desired path.

Given a url pattern, Django uses url() to pick the right view and generate a page. That is, url--> view name. But sometimes, like when redirecting, you need to go in the reverse direction and give Django the name of a view, and Django generates the appropriate url. In other words, view name --> url. That is, reverse() (it's the reverse of the url function). It might seem more transparent to just call it generateUrlFromViewName but that's too long and probably not general enough:




from django.urls import path

from . import views

urlpatterns = [
    #...
    path('articles/<int:year>/', views.year_archive, name='news-year-archive'),
    #...
]


According to this design, the URL for the archive corresponding to year nnnn is /articles/<nnnn>/.
You can obtain these in template code by using:

    
<a href="{% url 'news-year-archive' 2012 %}">2012 Archive</a>
<ul>
{% for yearvar in year_list %}
<li><a href="{% url 'news-year-archive' yearvar %}">{{ yearvar }}Archive</a></li>
{% endfor %}
</ul>
  
  
Or in Python code:

from django.http import HttpResponseRedirect
from django.urls import reverse

def redirect_to_year(request):
    # ...
    year = 2006
    # ...
    return HttpResponseRedirect(reverse('news-year-archive', args=(year,)))
  
 ...............................                                            
    
-> Create your models here. 
    
    from django.urls import reverse

    class Course(models.Model):
        slug = models.SlugField()
        title = models.CharField(max_length=120)
        description = models.TextField()

        def get_absolute_url(self):
            return reverse('courses:detail',kwargs = {'slug':self.slug})


    class Lesson(models.Model):
        slug = models.SlugField()
        video_url = models.URLField()
        thumbnail = models.ImageField()

        def get_absolute_url(self):
            return reverse('courses:lesson-detail',
            kwargs= {
                'course_slug': self.course.slug,
                'lesson_slug' : self.slug
            })
  
-> urls.py
  
  from .views import CourseListView, CourseDetailView, LessonDetailView

  app_name = 'courses'

  urlpatterns = [
      path('', CourseListView.as_view(), name='list'),
      path('<slug>', CourseDetailView.as_view(), name='detail'),
      path('<course_slug>/<lesson_slug>',
           LessonDetailView.as_view(), name='lesson-detail')
  ]
  
-> course_detail.html
  
   {% for lesson in object.lessons %}
           <a href="{{ lesson.get_absolute_url }}">{{ lesson.title}}</a>
    {% endfor %}
  
-> course_list.html
    
    
  {%  for object in object_list %}
        <h2><a href="{{ object.get_absolute_url}} ">{{object}}</a></h2>
  {% endfor %}
  
## 2). Database Query Optimisation
    
    It is good to use values() rather than retrieving the whole query set which might have hundreds of fields. Only pull the data that you need.
-> views.py
    
    def order_list_view(request):
         orders = Order.objects.values('number','price','quantity')
    
## 3). Messages Framework
    
-> settings.py
    
from django.contrib.messages import constants as messages

managing django messages
MESSAGE_TAGS = {
        messages.DEBUG: 'alert-secondary ',
        messages.INFO: 'alert-info ',
        messages.SUCCESS: 'alert-success ',
        messages.WARNING: 'alert-warning ',
        messages.ERROR: 'alert-danger ',
 }
 
 -> views.py   
 from django.contrib import messages 
    
 messages.success(request,  "Your account created successfully")
 return redirect('/')   
    
    
