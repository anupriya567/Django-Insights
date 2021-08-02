# Django-Insights

## 1). Reverse function where and why to use?

Use reverse instead of hardcoded uls. 

Suppose we are using a redirect method lets say  get_absolute_url() method with \users and then if we change the path to \members then the application will break.
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
    
It is good to use values() rather than retrieving the whole query set which might have hundreds of fields. 
Only pull the data that you need.
    
-> views.py
    
    def order_list_view(request):
         orders = Order.objects.values('number','price','quantity')
    
## 3). Messages Framework
    
-> settings.py
    
   from django.contrib.messages import constants as messages
    
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
    
##  4). for … empty
    
    
The for tag can take an optional {% empty %} clause whose text is displayed if the given array is empty or could not be found:

        <ul>
        {% for athlete in athlete_list %}
            <li>{{ athlete.name }}</li>
        {% empty %}
            <li>Sorry, no athletes in this list.</li>
        {% endfor %}
        </ul>
    
The above is equivalent to – but shorter, cleaner, and possibly faster than – the following:

        <ul>
          {% if athlete_list %}
            {% for athlete in athlete_list %}
              <li>{{ athlete.name }}</li>
            {% endfor %}
          {% else %}
            <li>Sorry, no athletes in this list.</li>
          {% endif %}
        </ul>
    
## 5). Base views
    
The following three classes provide much of the functionality needed to create Django views. You may think of them as parent views, which can be used by themselves or inherited from. They may not provide all the capabilities required for projects, in which case there are Mixins and Generic class-based views.

Many of Django’s built-in class-based views inherit from other class-based views or various mixins. Because this inheritance chain is very important, the ancestor classes are documented under the section title of Ancestors (MRO). MRO is an acronym for Method Resolution Order.

View
    
    
class django.views.generic.base.View
    
The master class-based base view. All other class-based views inherit from this base class. It isn’t strictly a generic view and thus can also be imported from django.views.

Method Flowchart

setup()
dispatch()
http_method_not_allowed()
options()
Example views.py:

        from django.http import HttpResponse
        from django.views import View

        class MyView(View):

            def get(self, request, *args, **kwargs):
                return HttpResponse('Hello, World!')
    
    
        Example urls.py:

        from django.urls import path
        from myapp.views import MyView

        urlpatterns = [
            path('mine/', MyView.as_view(), name='my-view'),
        ]
    
    
 ## 6). redirect()
    
    
 By passing some object; that object’s get_absolute_url() method will be called to figure out the redirect URL:

        from django.shortcuts import redirect

                def my_view(request):
                    ...
                    obj = MyModel.objects.get(...)
                    return redirect(obj)
    
    
By passing the name of a view and optionally some positional or keyword arguments; the URL will be reverse resolved using the reverse() method:

        def my_view(request):
            ...
            return redirect('some-view-name', foo='bar')
By passing a hardcoded URL to redirect to:

        def my_view(request):
            ...
            return redirect('/some/url/')
This also works with full URLs:

        def my_view(request):
            ...
            return redirect('https://example.com/')
    
By default, redirect() returns a temporary redirect. All of the above forms accept a permanent argument; if set to True a permanent redirect will be returned:

        def my_view(request):
            ...
            obj = MyModel.objects.get(...)
            return redirect(obj, permanent=True)   
    

## 7). django.contrib.humanize
    
    
A set of Django template filters useful for adding a “human touch” to data.

To activate these filters, add 'django.contrib.humanize' to your INSTALLED_APPS setting. Once you’ve done that, use {% load humanize %} in a template, and you’ll have access to the following filters.    
    
    
apnumber:-
    
        1 becomes one.
        2 becomes two.
        10 becomes 10.
    
    
intcomma:-
    
       4500 becomes 4,500.
       4500.2 becomes 4,500.2.
       45000 becomes 45,000.
    
    
intword:-
    
        1000000 becomes 1.0 million.
        1200000 becomes 1.2 million.
    
    
naturalday:-
    
         Examples (when ‘today’ is 17 Feb 2007):

        16 Feb 2007 becomes yesterday.
        17 Feb 2007 becomes today.
    
    
naturaltime
    
Examples (when ‘now’ is 17 Feb 2007 16:30:00):

        17 Feb 2007 16:30:00 becomes now.
        17 Feb 2007 16:29:31 becomes 29 seconds ago.
        17 Feb 2007 16:29:00 becomes a minute ago.
        17 Feb 2007 16:25:35 becomes 4 minutes ago    
    
ordinal
    
        1 becomes 1st.
        2 becomes 2nd.
        3 becomes 3rd.
