# Contentious-360 #

Contentious is a content management library for games written in .NET and Mono.
I originally wrote what's now becoming Contentious (this is the third rewrite)
because the XNA Content Pipeline just isn't very good for PC games; it might be
a solid tool for 360 and WP7 development, but personally I don't really care
about either. And despite not personally needing it ( ;-) ) I've quickly put
together this package to go along with contentious, for those who do need XNA
support.

Contentious is released under the ISC license, packaged in this repo as
LICENSE.md.

**ONE IMPORTANT NOTE:** Contentious-360 is not a project that's really on my
radar. If it works for you, awesome, and if it falls out of sync with the
regular Contentious project, feel free to file an issue against the project,
but it's not really my focus.


-Ed Ropple (ed+contentious@edropple.com); @edropple on Twitter

## How It Works ##
Contentious is based around the **ContentContext**. At its core, ContentContext
objects define a location (by default on disk, but at some point adapters for
Android's weird file storage and a layer that can sit on top of XNA's
ContentManager stuff) and a set of content types. Each content type contains a
Type (what a given file resource will be turned into), a list of valid file
extensions for this type, and an an optional sub-path within your content root
where all files pertaining to this type will live (for example, you might have
all Texture2D objects as .png and stored within $CONTENTROOT/Textures).

Once you've created a ContentContext, you load in a resource via the
ContentContext.Load<T>() method. Your path should be relative to the content
subroot for that type; providing a file extension is optional but recommended;
automatic extension appending is provided mostly for future XNA friendliness
but I don't use it in my own projects. All content types that are loaded into
a ContentContext must implement IDisposable, because when a ContentContext is
disposed it will dispose of all objects created through it. (Possible future
change: allow any object, but auto-dispose any IDisposables when done.)

ContentContexts also have the notion of parent contexts. In your application,
you'll generally only explicitly create one global context via a constructor
and the rest via ContentContext.CreateChildContext(). Child contexts inherit
all of the parent's configuration values (even if they are changed after the
child is created). They also check with their parent contexts before attempting
to load a piece of content from disk, which can reduce multiple loads of the
same content. For example, you might load a font file into your global context,
then request the same font file from a child context; it would be stupid to
load the font file into the child context a second time. Of course, the child
context won't try to Dispose() of resources it doesn't own, and disposing of a
parent context disposes of all child contexts.

## Variants ##
**XnaContentContext** is for use in XNA games. The others can be found in the
regular Contentious project at:

http://github.com/eropple/contentious