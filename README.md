## Django Projects
1. [taskmate- TodoList](https://github.com/anupriya567/taskmate/tree/master) <br />
2. [LevelUp-Blog](https://github.com/anupriya567/LevelUp)<br />
3. [Payment_Integration-Django](https://github.com/anupriya567/Payment_Integration-Django)<br />
4. [Social Book](https://github.com/anupriya567/social_app_django/tree/master)<br/>
5. [Ibm-developer-skills-network-Quiz-App](https://github.com/anupriya567/ibm-developer-skills-network-Quiz-App)<br />


## Django Rest FrameWork
1. [IMDB-API-Clone-DjangoRestFramework](https://github.com/anupriya567/IMDB-API-Clone-DjangoRestFramework)<br />
                               
2. [LMS-Django-Rest-Framework](https://github.com/anupriya567/LMS-Django-Rest-Framework-)<br />
# Django-Insights


## Imp. Terms- 
### 1. Rest Api
* A REST API (also known as RESTful API) is an application programming interface (API or web API) that conforms to the constraints of REST architectural style and allows for interaction with RESTful web services

* One of the key advantages of REST APIs is that they provide a great deal of flexibility. Data is not tied to resources or methods, so REST can handle multiple types of calls, return different data formats and even change structurally with the correct implementation of hypermedia.

### 2. Server
server, network computer, computer program, or device that processes requests from a client (see client-server architecture). On the World Wide Web, for example, a Web server is a computer that uses the HTTP protocol to send Web pages to a client's computer when the client requests them.


### 3. JSON

* JSON stands for JavaScript Object Notation
* JSON is a lightweight data-interchange format
* JSON is plain text written in JavaScript object notation
* JSON is used to send data between computers
* JSON is language independent

>> Why Use JSON?
* The JSON format is syntactically similar to the code for creating JavaScript objects. Because of this, a JavaScript program can easily convert JSON data into           JavaScript objects.

* Since the format is text only, JSON data can easily be sent between computers, and used by any programming language.

* JavaScript has a built in function for converting JSON strings into JavaScript objects:

```
JSON.parse()
```
* JavaScript also has a built in function for converting an object into a JSON string:

```
JSON.stringify()
```

## 4. Serialization

* Serializers in Django REST Framework are responsible for converting objects into data types understandable by javascript and front-end frameworks.
* Serializers also provide deserialization, allowing parsed data to be converted back into complex types, after first validating the incoming data.











## 1). Reverse function where and why to use?

Use reverse instead of hardcoded uls. 

Suppose we are using a redirect method lets say  get_absolute_url() method with \users and then if we change the path to \members then the application will break.
But if we use reverse of url name
At any moment we are free to change the path and the call will not break because our reverse call can still take us to the desired path.

Given a url pattern, Django uses url() to pick the right view and generate a page. That is, url--> view name. But sometimes, like when redirecting, you need to go in the reverse direction and give Django the name of a view, and Django generates the appropriate url. In other words, view name --> url. That is, reverse() (it's the reverse of the url function). It might seem more transparent to just call it generateUrlFromViewName but that's too long and probably not general enough:

```
from django.urls import path
from . import views

urlpatterns = [
    #...
    path('articles/<int:year>/', views.year_archive, name='news-year-archive'),
    #...
]
```

According to this design, the URL for the archive corresponding to year nnnn is /articles/<nnnn>/.
You can obtain these in template code by using:

 ```   
<a href="{% url 'news-year-archive' 2012 %}">2012 Archive</a>
<ul>
{% for yearvar in year_list %}
<li><a href="{% url 'news-year-archive' yearvar %}">{{ yearvar }}Archive</a></li>
{% endfor %}
</ul>
 ```
  
  
Or in Python code:
```
from django.http import HttpResponseRedirect
from django.urls import reverse

def redirect_to_year(request):
    # ...
    year = 2006
    # ...
    return HttpResponseRedirect(reverse('news-year-archive', args=(year,)))
```  
                                           
    
-> Create your models here. 
 ```   
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
 ``` 
-> urls.py
  ```
  from .views import CourseListView, CourseDetailView, LessonDetailView

  app_name = 'courses'

  urlpatterns = [
      path('', CourseListView.as_view(), name='list'),
      path('<slug>', CourseDetailView.as_view(), name='detail'),
      path('<course_slug>/<lesson_slug>',
           LessonDetailView.as_view(), name='lesson-detail')
  ]
  ```
-> course_detail.html
  ```
   {% for lesson in object.lessons %}
           <a href="{{ lesson.get_absolute_url }}">{{ lesson.title}}</a>
    {% endfor %}
 ``` 
-> course_list.html
    
  ```  
  {%  for object in object_list %}
        <h2><a href="{{ object.get_absolute_url}} ">{{object}}</a></h2>
  {% endfor %}
 ``` 
## 2). Database Query Optimisation
    
It is good to use values() rather than retrieving the whole query set which might have hundreds of fields. 
Only pull the data that you need.
    
-> views.py
 ```   
    def order_list_view(request):
         orders = Order.objects.values('number','price','quantity')
```    
## 3). Messages Framework
    
-> settings.py
```    
   from django.contrib.messages import constants as messages
    
   MESSAGE_TAGS = {
        messages.DEBUG: 'alert-secondary ',
        messages.INFO: 'alert-info ',
        messages.SUCCESS: 'alert-success ',
        messages.WARNING: 'alert-warning ',
        messages.ERROR: 'alert-danger ',
     }
 ```
 -> views.py   
  ``` 
     from django.contrib import messages
    
     messages.success(request,  "Your account created successfully")
     return redirect('/')   
  ```
    
 -> Displaying messages

```
{% if messages %}
<ul class="messages">
    {% for message in messages %}
    <li{% if message.tags %} class="{{ message.tags }}"{% endif %}>{{ message }}</li>
    {% endfor %}
</ul>
{% endif %}
```    
<!-- message alert -->
```    
{% for message in messages%}
<div class="alert {{message.tags }}alert-dismissible fade show" role="alert">
  <strong>Message: </strong> {{message}}
  <button type="button" class="btn-close" data-bs-dismiss="alert" aria-label="Close"></button>
</div>
{% endfor %}    
```    
##  4). for … empty
    
    
The for tag can take an optional {% empty %} clause whose text is displayed if the given array is empty or could not be found:
 ```
        <ul>
        {% for athlete in athlete_list %}
            <li>{{ athlete.name }}</li>
        {% empty %}
            <li>Sorry, no athletes in this list.</li>
        {% endfor %}
        </ul>
 ```
The above is equivalent to – but shorter, cleaner, and possibly faster than – the following:
```
        <ul>
          {% if athlete_list %}
            {% for athlete in athlete_list %}
              <li>{{ athlete.name }}</li>
            {% endfor %}
          {% else %}
            <li>Sorry, no athletes in this list.</li>
          {% endif %}
        </ul>
   ``` 

    
 ## 5). redirect()
    
    
 By passing some object; that object’s get_absolute_url() method will be called to figure out the redirect URL:
```
        from django.shortcuts import redirect

                def my_view(request):
                    ...
                    obj = MyModel.objects.get(...)
                    return redirect(obj)
 ```   
    
By passing the name of a view and optionally some positional or keyword arguments; the URL will be reverse resolved using the reverse() method:
```
        def my_view(request):
            ...
            return redirect('some-view-name', foo='bar')
```    
By passing a hardcoded URL to redirect to:
```
        def my_view(request):
            ...
            return redirect('/some/url/')
```    
    
This also works with full URLs:
```
        def my_view(request):
            ...
            return redirect('https://example.com/')
```    
By default, redirect() returns a temporary redirect. All of the above forms accept a permanent argument; if set to True a permanent redirect will be returned:
```
        def my_view(request):
            ...
            obj = MyModel.objects.get(...)
            return redirect(obj, permanent=True)   
 ```   

## 6). django.contrib.humanize
    
    
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

## 7). What is the difference between null=True and blank=True in Django?   
    
  
  ### null
> If True, Django will store empty values as NULL in the database. Default is False.
  ### blank
> If True, the field is allowed to be blank. Default is False.

> Note that this is different than null. null is purely database-related, whereas blank is validation-related. If a field has blank=True, form validation will allow     entry of an empty value. If a field has blank=False, the field will be required.
  ```null=True ```
   ets ```NULL``` (versus NOT NULL) on the column in your DB. Blank values for Django field types such as DateTimeField or ForeignKey will be stored as NULL in the        DB.

> blank determines whether the field will be required in forms. This includes the admin and your custom forms. If ```blank=True``` then the field will not be required,   whereas if it's False the field cannot be blank.

> The combo of the two is so frequent because typically if you're going to allow a field to be blank in your form, you're going to also need your database to allow       NULL values for that field. The exception is CharFields and TextFields, which in Django are never saved as NULL. Blank values are stored in the DB as an empty string   ('').

A few examples:
```
models.DateTimeField(blank=True) # raises IntegrityError if blank

models.DateTimeField(null=True) # NULL allowed, but must be filled out in a form
```
Obviously, Those two options don't make logical sense to use (though there might be a use case for null=True, blank=False if you want a field to always be required in forms, optional when dealing with an object through something like the shell.)

```    
models.CharField(blank=True) # No problem, blank is stored as ''

models.CharField(null=True) # NULL allowed, but will never be set as NULL
 ```
CHAR and TEXT types are never saved as NULL by Django, so null=True is unnecessary. However, you can manually set one of these fields to None to force set it as NULL. If you have a scenario where that might be necessary, you should still include null=True.   
    
## 8). By default Django is using sessions to register the user
    
    we can see session id here:
![d1](https://user-images.githubusercontent.com/72871727/128700434-c4b18ef8-cbd3-4cab-b35f-0d3ba7c6710f.PNG)   
    
    if we delete this id we get logged out

![d2](https://user-images.githubusercontent.com/72871727/128700398-7f89b050-00d6-4acf-9e7f-b834ee7a36cd.PNG)

## 9). Django search functionality
    
 ```
    class TaskList(LoginRequiredMixin, ListView):
    model = Task
    context_object_name = 'tasks'

    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['tasks'] = context['tasks'].filter(user=self.request.user)
        context['count'] = context['tasks'].filter(complete=False).count()

        search_input = self.request.GET.get('search-area') or ''
        if search_input:
            context['tasks'] = context['tasks'].filter(
                title__contains=search_input)

        context['search_input'] = search_input

        return context
 ```
  search acc. to start of title-
    ```
   title__startswith = search_input
    ```
 ## 10). [QuerySet API reference](https://docs.djangoproject.com/en/3.2/ref/models/querysets/)
         Read this. Useful when fetching, filtering, fetching queries in sorted order etc.
    
 ## 11). How to use template filters?
     
When and where to use??
In HTML file you want to o/p something but atfer computing some logic
so will make a python file perform logic there and send o/p to html page and it will render data accordingly

eg-  Checking whether the product is present in session cart or not 

make a folder **templatetags** ,  file in it say cart.py
    
cart.py->    
```   
from django import template
register = template.Library()

@register.filter(name='is_in_cart')
def is_in_cart(product  , cart):
    keys = cart.keys()
    for id in keys:
        if int(id) == product.id:
            return True
    return False;
    
    @register.filter(name='cart_quantity')
    def cart_quantity(product  , cart):
        keys = cart.keys()
        for id in keys:
            if int(id) == product.id:
                return cart.get(id)
        return 0;
 ```   
    
in html file->
```
         {% load cart %}
            {% for product in products %}
            <tr>
                <td>{{forloop.counter}}</td>
                <td><img style="height: 80px;" class="rounded-circle" src="{{product.image.url}}" alt=""></td>
                <td>{{product.name}}</td>
                <td>{{product.price|currency}}</td>
                <td>{{product|cart_quantity:request.session.cart}}</td>
                <td>{{product|price_total:request.session.cart|currency}}</td>
            </tr>

            {% endfor %}
```
## 12). Jinja2 is a modern day templating language for Python developers. 
Why do we need Jinja 2?
Sandboxed Execution: It provides a protected framework for automation of testing programs, whose behaviour is unknown and must be investigated.
    
HTML Escaping: Jinja 2 has a powerful automatic HTML Escaping, which helps preventing Cross-site Scripting (XSS Attack). There are special characters like >,<,&, etc. which carry special meanings in the templates. So, if you want to use them as regular text in your documents then, replace them with entities. Not doing so might lead to XSS-Attack.

Template Inheritance:


 Delimiters
{%....%} are for statements
{{....}} are expressions used to print to template output
{#....#} are for comments which are not included in the template output
#....## are used as line statements

Conditional Statement

Template Inheritance

## 13). using safe = false
```
return JsonResponse({'token': token, 'uid': uid}, safe=False) 
```
> setting the safe parameter to False actually influences JSON to receive any Python Data Type. 
> Personally and professionally, it is advisable you set it the safe parameter to False because it makes JSON accept both {Dictionaries} and others.


# Class-based-View-Django

## 1). Introduction to class-based views

Using class-based views
At its core, a class-based view allows you to respond to different HTTP request methods with different class instance methods, instead of with conditionally branching code inside a single view function.

So where the code to handle HTTP GET in a view function would look something like:
```
from django.http import HttpResponse

def my_view(request):
    if request.method == 'GET':
        # <view logic>
        return HttpResponse('result')
```        
In a class-based view, this would become:

```
from django.http import HttpResponse
from django.views import View

class MyView(View):
    def get(self, request):
        # <view logic>
        return HttpResponse('result')
 ``` 
  
Because Django’s URL resolver expects to send the request and associated arguments to a callable function, not a class, class-based views have an as_view() class method which returns a function that can be called when a request arrives for a URL matching the associated pattern. The function creates an instance of the class, calls setup() to initialize its attributes, and then calls its dispatch() method. dispatch looks at the request to determine whether it is a GET, POST, etc, and relays the request to a matching method if one is defined, or raises HttpResponseNotAllowed if not:

###### urls.py
```
from django.urls import path
from myapp.views import MyView

urlpatterns = [
    path('about/', MyView.as_view()),
]
```
## 2). [Built-in class-based views API](https://docs.djangoproject.com/en/3.2/ref/class-based-views/)

## 3).  [What are Django class based views & should you use them?](https://www.dennisivy.com/post/django-class-based-views/)
## 4). How to restrict the users from seeing data? How to show data of loggedin user only?

in function based view we use decorators to restrict the view
eg:  only loggedin user can see the data

in class-based view will do this using mixins-
```
from django.contrib.auth.mixins import LoginRequiredMixin

class TaskList(LoginRequiredMixin, ListView):
    model = Task
    context_object_name = 'tasks'
    
    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['tasks'] = context['tasks'].filter(user=self.request.user)
        context['count'] = context['tasks'].filter(complete=False).count()

        search_input = self.request.GET.get('search-area') or ''
        if search_input:
            context['tasks'] = context['tasks'].filter(
                title__contains=search_input)

        context['search_input'] = search_input

        return context![p1](https://user-images.githubusercontent.com/72871727/130781005-33ee377f-e550-4a3f-9b46-b0c289a09239.PNG)


```
```context['tasks']``` contains all tasks initially, then they are filtered acc. to loggedin user


# Django Rest FrameWork

-> error

![e1](https://user-images.githubusercontent.com/72871727/130618275-7a55e589-ed60-43be-9964-15a7a15f7d1f.PNG)

-> solve
![e5](https://user-images.githubusercontent.com/72871727/130618309-46a349f7-4dbf-4640-bed1-496ba1b62199.PNG)

-> for put request
  error = id =1 pr update kiya h, but update nhi hua new object create ho gya id = 6 vala
![e3](https://user-images.githubusercontent.com/72871727/130618253-68d00734-a144-40eb-8512-6ee32b5df17f.PNG)
![e4](https://user-images.githubusercontent.com/72871727/130618271-9a3e5f09-298b-45b4-a630-9172e9055d2c.PNG)
![e2](https://user-images.githubusercontent.com/72871727/130618260-fd8d96ce-2bef-4c57-9a9b-zzzzda5b48accf77.PNG)

### Nested Serialization
> As opposed to previously discussed references to another entity, the referred entity can instead also be embedded or nested in the representation of the object that refers to it. Such nested relationships can be expressed by using serializers as fields.
![p1](https://user-images.githubusercontent.com/72871727/130781247-1856bada-ba9b-49d9-8f97-316ec7416e23.PNG)

![p2](https://user-images.githubusercontent.com/72871727/130781073-4ec83ca1-5a8a-4fac-8b29-791b86ba0bb6.PNG)

## Serialization Relations 

##### Relational fields are used to represent model relationships. They can be applied to ForeignKey, ManyToManyField and OneToOneField relationships, as well as to reverse relationships, and custom relationships such as GenericForeignKey.

> They can be applied to ForeignKey, ManyToManyField and OneToOneField relationships
```
models.py
class Review(models.Model):
    rating = models.PositiveIntegerField(validators=[MinValueValidator(1),MaxValueValidator(5)])
    description = models.CharField(max_length=500)
    watchlist = models.ForeignKey(WatchList, on_delete = models.CASCADE, related_name="reviews")
    active = models.BooleanField(default="true")
    created = models.DateTimeField(auto_now_add=True)
    updated = models.DateTimeField(auto_now=True)
    review_user = models.ForeignKey(User, on_delete = models.CASCADE)

    def __str__(self):
        return str(self.rating) + " | " + self.watchlist.title + " | " + str(self.review_user)
```
```
serializer.py
class ReviewSerializer(serializers.ModelSerializer): 
    review_user = serializers.StringRelatedField(read_only = True)
    class Meta:
        model = Review
        # fields = '__all__'
        exclude = ('watchlist',)
```        
> applied to as well as to reverse relationships 
```

class StreamPlateform(models.Model):
    name = models.CharField(max_length=50)
    about = models.CharField(max_length=500)
    website = models.URLField(default =True)

    def __str__(self):
        return self.name

class WatchList(models.Model): 
    title = models.CharField(max_length=50)
    storyline = models.CharField(max_length=500)
    plateform = models.ForeignKey(StreamPlateform, on_delete = models.CASCADE, related_name="watchlist")
    active = models.BooleanField(default =True)
    created = models.DateTimeField(auto_now_add=True)
    avg_rating = models.FloatField(default = 0)
    number_rating  = models.IntegerField(default = 0)

    def __str__(self):
        return self.title
```

```
class StreamPlateformSerializer(serializers.ModelSerializer):
    
    # nested serialization
    # watchlist =  WatchListSerializer(many=True, read_only=True)
    watchlist = serializers.StringRelatedField(many=True)
   
    class Meta:
        model = StreamPlateform
        fields = '__all__'
```
![p4](https://user-images.githubusercontent.com/72871727/130781221-b142e4ec-ca23-4bdb-80b4-4ced154e14e9.PNG)

![p3](https://user-images.githubusercontent.com/72871727/130781185-31ca6944-ec00-453a-92b5-a0a141c0ad23.PNG)
![p5](https://user-images.githubusercontent.com/72871727/130781872-1b720702-64ae-47a8-9509-b71ec86340fe.PNG)

![p6](https://user-images.githubusercontent.com/72871727/130781643-59044273-9b6a-4cc1-adfd-f4e82589b03c.PNG)
![p7](https://user-images.githubusercontent.com/72871727/130781690-e48343e6-2cb7-4a59-9c02-ed4044fde6f8.PNG)
![p8](https://user-images.githubusercontent.com/72871727/130781709-7d548a75-4315-4527-b028-22d355184ac5.PNG)
![p10](https://user-images.githubusercontent.com/72871727/130781769-0831903a-c463-40a6-a51e-4bf738e0d6ff.PNG)
![p11](https://user-images.githubusercontent.com/72871727/130781793-cf874d10-b18d-48d9-957a-d2e04468140b.PNG)




-> error
     view name and class name can't be same

![image](https://user-images.githubusercontent.com/72871727/130780266-fc469931-1e72-479a-8c3f-189b264fb056.png)


## Throttling

```
Throttling is similar to permissions, in that it determines if a request should be authorized. Throttles indicate a temporary state, and are used to control the rate of requests that clients can make to an API.
```

> Eg:
How many images can you download from Freepik?

As non registered user, you can download 3 images a day. As free user, you can download 10 images a day, and as premium user, you can download 100 images a day.
