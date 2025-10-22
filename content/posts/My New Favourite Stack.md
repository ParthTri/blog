---
draft: "false"
title: My New Favourite Stack
date: 2025-10-21
---
It's been a while since my last blog post but that's mainly cause I've found myself preoccupied with a side project I've been working on. I won't be sharing with this project is yet or share many code snippets, but mainly talk about my experiences of this stack.

> [!TLDR]
> The stack I will be talking about is Django + Postgres + HTMX + Surrealjs + Tailwindcss. It's nothing technical, just sharing something I've been trying out.

# A little background...

I started out my programming journey in school, learning to make small scripts in Python. Not real "*projects*", but just small one file programs no greater than 100 lines.

After making small projects here and there, I got into making automation programs which finally broke the 1 file trend. Pretty soon I was dealing with more complex (to me at the time) things with database and API queries, implementing custom logging, etc. It was around this point where I got bored of Python and wanted something different. Java and C/C++ seemed very daunting and Rust was just taking off, but then I stumbled onto Go. I started writing small programs yet again in Go, and eventually fall on the same path as Python making automation programs.

After a while of doing non-user interaction programs, I finally decided to take a dive into the Javascript ecosystem and the world of SPAs. No I don't think it's a bad language (not that I'm an expert to make an opinion), but it always just felt off. It didn't seem right having the large amount of processing done on the client. The browser wasn't meant to become the execution ground of applications, but rather a window into interacting with the application.

So after a lot of trial and frustration with web frameworks and such I finally came back to Python.

# The Stack

I'm not saying you should go out and use this stack instead of the one you want to use or the one that is best suited for your application, but the main reason I love this stack is because of just how productive I feel in it. Writing code just feels easy and makes sense and all the parts just work together.

It makes sense for the processing to be done on the server and updates sent back to the user.

## Django

Django the main core of my new favourite stack. Dubbed *The web framework for perfectionists on a deadline* has been around since 2005 powering apps like Instagram (in the early days), and Pinterest. 

The MVC (or MVT) architecture makes sense and works and the Django ORM is really quick to wrap your head around and start writing queries.

It nice to be able to just define the model like this that hook into the existing auth system:
```python
class Post(models.Model):
	user = models.ForeignKey(User, on_delet=models.CASCADE)
	title = models.TextField(null=False)
	content = models.TextField(null=False, default="")
	likes = models.IntegerField(default=0)
	image = models.ImageField(null=True)
```

Then just write queries to fetch it
```python
user_1 = User.objects.get(id=1)
posts = Post.objects.filter(user=user_1)
```

Also hooking it in with forms with `ModelForm` for displaying forms and also just parsing data if you have a pre-styled form.
```python
class PostForm(forms.ModelForm):
	class Meta:
		model = Post
		fields = ["name", "title", "content", "image"]
```

Then working with in the `views.py` file:
```python
# using a function based view
def create_post(request):
	if request.method == "POST":
		post = Post()
		post_form = PostForm(request.POST, instance=post)
		post_form.save()
		
		return HTTPResponse("Saved")
```

It all just meshes together nicely and saves time trying to figure out how one should do it.

## Postgres

There's not much else to say other then just Postgres. It is probably one of the most developer loved databases, and one of the easiest to get running. It comes out of the box with loads of great types and niceties. Additionally, you can install Postgres extensions, I haven't used any yet, but from what I've seen it can do some pretty crazy stuff ([pg_cron](https://github.com/citusdata/pg_cron), [pg_analytics](https://github.com/paradedb/pg_analytics), [PostgreML](https://postgresml.org/)).

It's also super easy to get up and running in a Docker container and it's been around for so long that it easy to find articles and documentation for it. Also SQL is a big plus.

## Tailwindcss

Tailwindcss is probably how I think about creating UIs now. Unless I create a custom styling, I barely touch CSS files and will just still my markup with utility classes. Some have argued that it makes the markup look messy, and I used to believe this as well, but the only people seeing it are the end user and the developers. The users will barely ever look at the markup and the developers can use formatters to view it is easier, which when served to the user will be optimised.

Particularly, I find it best for me when paired with [DaisyUI](https://daisyui.com). It great for me since I have horrible design skills, but also I can create my own styles easily. Having also been packaged with lots of components out of the box, you only have to add the class name into the `class` attribute and voila. Saves a lot of time developing and has great developer ergonomics.

## HTMX

The incremental reactivity king. HTMX was all the rave and deservedly so. It just makes it so easy to turn a multi-page application to feel like a single-page application. Although, I'm avoiding using it my project currently as I focus on it's beta stage getting features working, it is nice to have it in the background ready to add in AJAX when I need.

The fact that it is also just a single import like:
```html
<script src="https://cdn.jsdelivr.net/npm/htmx.org@2.0.7/dist/htmx.js" integrity="sha384-yWakaGAFicqusuwOYEmoRjLNOC+6OFsdmwC2lbGQaRELtuVEqNzt11c2J711DeCZ" crossorigin="anonymous"></script>
```

Makes it super easy to quickly add into your project, regardless of the server-side language. As long as you are using HTML for markup you can easily add in HTMX.

## Surrealjs

[Surrealjs](https://github.com/gnat/surreal) is a lesser known incremental reactivity framework. I searched far and wide trying all sorts of different libraries ([Hyperscript](https://hyperscript.org), [Alpine.js](https://alpinejs.dev), Vanilla JS, [jQuery](https://jquery.com)) until I finally settled on surreal. Hyperscript felt too much like a DSL and felt like it would quickly fall apart when writing anything complex. Alpinejs felt weird writing Javascript into HTML attributes inside of quotation marks, and I couldn't seem to wrap my head around it. Granted I only spent a short time working with it. 

Surreal struck the right balance between jQuery and Vanilla JS. Using it's `me()` and `any()` helpers plus the `.on()` function to easily chain events is super intuitive. Also since it's just a Javascript library with a bunch of helper functions, I can just write normal Javascript code anywhere on the page.

It did feel weird and unnatural to create script tags with Javascript anywhere in my web page, but again, no one's gonna care about it unless their a developer. It's still [W3C](https://dev.w3.org/html5/spec-LC/scripting-1.html?utm_source=chatgpt.com) compliant.

Now, one issue that did arise from using this as opposed to existing libraries is that using AI to help debug things wasn't very helpful. It hasn't been around for a long time and it hasn't seen its explosive growth in usage yet, so AI might take a while to get better at it. However, therein lies one of its strengths. It's a tiny library ~320 lines in single file. You can just give that file to your AI of choice, get it to parse it, and after a bit of prompt engineering and AI tuning you can have it spit out reasonably well written code utilising.

# Con.

Having bounced around from lots of different stacks from the JS ecosystem and Go ecosystem (and Java for university but lets not talk about that), this stack hits all the right notes for me. I'm not saying its the best stack in the world and everyone should switch to it, but it is fun and I would also recommend trying it out.

