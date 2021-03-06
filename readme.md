# Ruby Buddha takes a holiday

I love Ruby but life is about balance. I like to balance my love
for one language by taking a look at what else is out there. This
is a series about that journey.

First up, Objective-C. I recently ventured down the iOS path with
a small application called Sparks - Campfire for iPad. Like other
Ruby developers I was intimidated by the close relationship to iOS
and the C language. After all, I haven't used a non-garbage collected
language in over 10 years and even then it was easy to make a mistake.

First the good news: If you're doing Snow Leopard development then
there are so many additions to Objective-C 2.0 that you may feel
pretty comfortable. C Blocks have been added allowing for some
functional programming style closures and the best part: A really
nice Garbage Collector has been added. Now, iOS is never too far
behind it's big brother OS X (and in some areas is actually way ahead!)
so learning these bits on the Mac will pay off in your iOS development
as well. For example, as of iOS 4.2 you can now use blocks in your
iPhone, iPod Touch and iPad applications.

Now the bad news: iOS is a rapidly evolving framework and APIs evolve frequently.
You will want to pay close attention once you have an application in
the store for breaking changes and backward compatibility.
The app store is a great way to sell and market your applications
but make no mistake, it doesn't quite follow the same release schedule as your Sinatra app
on Heroku.com! When the team at Apple is backed up you can wait several days
to get your app approved. There is no difference in review time between a new app
and an update. It takes time and you'll need to account for that when planning your releases.
I like to think of it as the "change management review" process many enterprises
have in place.

## Building something small

There's a few hello world applications on the internet
for new iOS developers but few are aimed at those coming from
the most prevalent platform over the last 15 years. Not windows, not mac, not
Linux... the internet. That's right, the internet. Many developers today
were born in scripting languages, like Ruby, and have no memories or experience
dealing with sandboxes, resource constraints and memory management. In Ruby, and
agile in general, we can defer many of our decisions to the time in which
they become a problem. One of the common "problems" or "ilities" that gets
deferred is performance but I don't recommend that for iOS projects.  Your choices
regarding resources, performance and memory management are important the moment
you begin. It may feel a bit weird, as if we're doing a bit of upfront design
but that's the environment in which you're in.

On a personal note, I find, when visiting other languages, frameworks and platforms,
that it helps to learn to use the chopsticks when eating the noodles rather than complain
that I'm not allowed to use a fork. It will increase the learning curve to some extent
but usually helps to provide a frame of reference when interacting with a new community.
In Ruby, we expect guests to learn our customs (aka idioms) in order to play and
Cocoa/Objective-C is no different. They've got some wonderful traditions and
it only helps to enrich our experience as developers to learn, take part and contribute back to them.

Since you're into Ruby you may have seen my favorite framework intro video of all time; 
David Heinemeier Hansson's "Create a blog in 15 minutes video" (sometimes known affectionately as the "oopsie video!"). 
This was the talk that introduced me to Rails, Ruby, DHH and the culture as a whole. 
David built a blog in 15 minutes, he didn't deploy it,
he didn't scale it to millions he just built a small blog. He was just a guy, having fun,
writing some code. So, we're going to do the same thing.
Build a small blog application for iPad that allows us to create,
update, delete and view a blog post as well as view a list of posts.

## Create the project.

Open Xcode and select the "Split-View Application" and check the "Use Core Data" option.
That's right, friends. We're going to write an iPad application. Something that
will make writing a blog a little more pleasurable!

In the generated project (kind of like running Rails <app> is it not?) files you'll
see a great many files. If you're familiar with Rails then some of the terms are going to
look familiar but don't go thinking that just because Cocoa implements MVC (Model-View-Controller)
that it's the same implementation that Rails uses. That said, let's define our
object model with Core Data. Open the "blog.xcdatamodel" file to begin creating
our object graph.

* Rename the "Event" entity to "Post"
* Create property "title" type String, min 2 chars, uncheck "optional", check "indexed"
* Create property "body" type String, uncheck "optional"
* Rename the "timeStamp" property to "createdAt"

That's it for now. We'll be adding a few things in a bit, but for now, let's get this
app up and running. Open the "DetailView.xib" file in Interface Builder. 

Delete the label that reads "Content goes here." and pull onto the design surface 3
controls:

  * UITextField  (for the title)
  * UITextView   (for the body)
  * UIBarButton  (for saving - place it in the toolbar)

Style them however you like. Save the file and go back to Xcode.

We're going to be jumping back and forth from IB to Xcode so get used to cmd-tabbing
your way around. While you're welcome to use Vim or Emacs or TextMate or whatever 
IDE you heart the most you'll probably not get a better experience for iOS development
than Xcode...remember you're in a guest's house. Be polite. Learn to let go.

Open the "DetailViewController.h" file and add 2 properties and one selector:

    @property (nonatomic, retain) IBOutlet UITextField *titleField;  
    @property (nonatomic, retain) IBOutlet UITextView *bodyField;

    - (IBAction) saveObject:(id)sender;

Jump to the DetailViewController.m and synthesize those properties.

Note: @synthesize and @property are analogous to the attr_accessor macro in Ruby.
a private field, a public getter and a public setter will be generated at compile
time when using @property and @synthesize.

    @synthesize titleField;  
    @synthesize bodyField;

Find the dealloc selector (a method in Objective-c parlance) and release our
new properties. Remember to put your toys away after playing with them, especially
in someone else's house.

In Ruby we've got a nanny: the garbage collector. So, even people that are guests
in our home (Ruby that is, if you're following along in this anaolgy...) don't have
to pick up their play things. Objective-C on iOS, on the other hand is a lot like
Ruby Buddha's house. We're poor and can't afford nannies and maids so guests have to
pick up after themselves. The rule in iOS is if you take it out to play with it, you
have to put it away. Dealloc is your mom calling at the end of the day. It's when you
have to put away your toys.

Add these lines:

    [bodyField release];bodyField = nil;  
    [titleField release];titleField = nil;

Now, the last bits where I mark these fields nil is something I've added. The generated
code does not do this. I do this for one reason: sending messages to nil in Objective-C
causes absolutely nothing to happen but sending a message to a deallocated reference
to an object will raise a pretty nasty exception. Therefore, to reduce the chances of
crashing in front of the end user I've gotten in the habit of releasing my reference
to the object and then just setting it to nil. This is, of course, optional but has
helped me out.

and finally implement the save method like so...

    - (IBAction) saveObject:(id)sender{  
      if(detailItem){  
        [detailItem setValue:titleField.text forKey:@"title"];  
        [detailItem setValue:bodyField.text forKey:@"body"];  
      }  
    }

It may seem odd that we're not calling save or create or update or asking the
table to refresh in order to see a change but that's the magic that is Core Data
and Key-Value-Coding in Cocoa. These powerful concepts deserve their own multi-part
series and are outside the scope of this tutorial. Never-the-less, they are pretty
fantastic.

Jump back to our DetailView.xib file and connect the new properties to
our controls and wire the save button's touchUpInside event to our save Method.

If you're unfamiliar with the concept of making connections in IB, I recommend
the excellent [Interface Builder User Guide](http://developer.apple.com/library/mac/#documentation/DeveloperTools/Conceptual/IB_UserGuide/Introduction/Introduction.html) from Apple.
I'm sorry if that feels like a bit of RT(f)?M but seriously, it's a really nice
manual and worth checking out for all the bells and whistles that IB provides. How's
this for incentive: that big graphical tool is just a visual representation of what
you would do in code serialized to disk and rehydrated as Objects at runtime! Cool stuff
and, in my humble experience, worth your investment in learning.

Once we've done all this, we need to make a few changes to our RootViewController
to ensure that it's using our Post entity for data and not the default data type
of Event.

Open RootViewController.m and edit the insertNewObject: selector. Under the 
comment "// If appropriate, configure the new managed object." change it to this:

    [newManagedObject setValue:[NSDate date] forKey:@"createdAt"];  
    [newManagedObject setValue:@"New Post" forKey:@"title"];  
    [newManagedObject setValue:@"Edit Body" forKey:@"body"];

Now you've setup a new item, gave it some valid data and selected it in the table.
We've got to make one more change... Remember how we changed the "Event" entity
to "Post" and "timeStamp" to "createdAt"? Well, this is why: searching projects
is probably one of the most common things I have to do in a new IDE. I thought it
might be fun to give it a try right now.

Either go to Edit > Find > Find in Project or type (Shift+ CMD+ F) to start a project
search. enter "timeStamp" and click find. You should be able to click the results to
jump immedately to where that key is being used in the application. Go ahead and update
them to "createdAt" and find/update Event to Post.

## Seeing the changes...

OK, you probably want to see it in action. There are 2 selectors that need some
code in order to see our changes in the app. First up, open DetailViewController.m
and look for:
    -(void)configureView;  

add these two lines...

    titleField.text = [detailItem valueForKey:@"title"];  
    bodyField.text = [detailItem valueForKey:@"body"];

Then open RootViewController.m and look for  
    - (void)configureCell:(UITableViewCell *)cell atIndexPath:(NSIndexPath *)indexPath;

let's replace the cell.textLabel line with:  
    cell.textLabel.text = [managedObject valueForKey:@"title"];  

## SHIP IT! 

Just kidding! But you can run it and you should see a fully working application that
allows you to create and edit blog entries. Changes are going to be saved and new
posts will be saved between application launches even though you never wrote code
to save the posts, it's there. Our app is using the Core Data framework to persist your
object graph to SQLite. It's a lot like Rails in the sense that if you use what is 
generated, then by convention you're going to write and maintance a lot less code. Just
like Rails and Ruby you're free to make the changes that your app needs to make but
just remember that at some point if you feel like you're swimming against the current,
or re-inventing the wheel, that's probably a sign that you are!

## BUT WHAT ABOUT THE REST OF CRUD?!

Oh, you didn't notice? Core Data took care of the rest of that for us. Go ahead
and swipe an article in the list of articles... DELETE! Try selecting an article
from the list, make a few changes and click save. UPDATED! It really is an amazing
little framework and when you need to produce data/service driven apps quickly
tools like Xcode, Interface Builder and the Cocoa frameworks go a long way to
get you up and running quickly. The innovated ideas and breakthrough user experience?
That's all you!


## That's it for now...

That's a quick view of creating a small iPad blogging app. Perhaps you'd like to add
some Tags or a search feature? Questions? Concerns? Wanna share your blog app?
Go ahead and post a comment, I'd love to see what you come up with.
If you'd like to download the source code for this article, fork it from
[github.com](https://github.com/leongersing/ruby_buddha_ios_blog_source)

