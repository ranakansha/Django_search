# Django_search
In this dir i will do coding for search bar in django . that is responsible for search the result on the basis of parameter.

![search](https://user-images.githubusercontent.com/51478832/90886627-5eb75f80-e3d0-11ea-9216-756fa614dd01.png)


--------------------------------------------------Step for making search bar functional--------------------------------------------------------------------
### 1.make url for searc bar in django app like:
``` 
 path('/search',views.serch),
 
```
### 2. make the function in view that is equal to uls views.functionname like:

```
def search(request):
context={}
return render (request.'search.html',context)
```

### add or import the model in which you want to search result like in my case i use Post model and the field is title,description,timestamp,autor etc and i want to serch results on the title parameter.
```
from app.models import modelname(posts)

```
### change form action and method in your search template and define name and id into your input type like:
```
<form method ="get" action="/search">
 <input class="form-control mr-sm-2" type="search" name="query" id="query" placeholder="Search" aria-label="Search">
 <button class="btn btn-outline-success my-2 my-sm-0" type="submit">Search</button>
</form>
```

### make query agains search bar with the help of icontains query set like:
[icontains document referance ](https://docs.djangoproject.com/en/3.1/topics/db/search/)
```
def search(request):
query = request.GET['query']
posts=Post.objects.filter(title__icontains=query)
context={"posts":posts}
return render (request.'search.html',context)

```
what we are do in this function is we are make query agains the parameter that user input in query variable.query variable get the value that user type and with the help of query we will able to search data from post model.in post_title__icontains i use double underscore whwer title is title field in model.


## Improvement for search bar code in django for better user experince.
--------------------------------------------------------------------------------------------------------------------------------------------------------------
1.if user enter too many long string or somthing that are not match with database recored in our title field then it will through something like this.

![Screenshot (244)](https://user-images.githubusercontent.com/51478832/90954029-69382e80-e48e-11ea-83f5-9813a1697185.png)

for adding this functality we use following steps.
step1: go inside search function in your views abd make code like this:
```
 query = request.GET['query']
    if len(query) >100:   # check length of query
        posts = Post.objects.none()  #if the length is greater than 100 then assign none object to query variable as show in  the above result that in image. 
        
    poststitle=Post.objects.filter(post_title__icontains=query)
     if posts.count()==0: #if query is qual to 0 then print below message 
        messages.error(request,"No Search Results Found . Please Refine Your Query.")
    return render(request,'search.html',{"posts":posts,"query":query})
```

step2: Add litle bit of code inside your search template for showing the error and you good to go .
```
 {% if messages %}
    {% for msg in messages%}
    {{msg}}
    {% endfor %}
    {% endif %}
    {% if posts|length == 0%}
    <div class="container">
    <p>Your search -</p><code><b>{{query}}...</b></code><br/><p>- did not match any documents</p><br/>
    Suggestions:
    <ul>
    <li>Make sure that all words are spelled correctly.</li>
    <li>Try different keywords.</li>
    <li>Try more general keywords.</li>
    </ul>
    </div>
    {% endif %}

```
## Search record with merge multiple query set.
if we need to search data from title and content and other mode field then we  need to merge multiple coloum of model with union function like this :
```
def serch(request):
    query = request.GET['query']
    if len(query) >100:
        posts = Post.objects.none()
        
    poststitle=Post.objects.filter(post_title__icontains=query)
    postcontent=Post.objects.filter(post_desc__icontains=query)
    posts = poststitle.union(postcontent)
    if posts.count()==0:
        messages.error(request,"No Search Results Found . Please Refine Your Query.")
    return render(request,'search.html',{"posts":posts,"query":query})
    

```



