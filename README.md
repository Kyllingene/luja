# Web

This is an experiment to create a pseudo-web "replacement".

## Goals

Here are my goals; whether or not I fulfill them is another matter entirely.

- Flexibility. Anyone should be able to self-host, or utilize existing (free)
  hosting providers, with no monetary barrier on my part.
  - It should also be able to perform most of the same tasks as the modern web,
    albeit in different ways. For example, it should remain interactive, and one
    should be able to host games and similar applications.

- Simplicity. It shouldn't take a new framework every month to stay up-to-date
  and clean, nor should websites be difficult to navigate. Moreover, it should
  use languages less archaic and complex than HTML, CSS, and JS: each of these
  is a poor tool for its use-case.

- Efficiency. I'd like to take advantage of the advancements of the past decade
  (such as heavy multithreading, or convenient cooperative multitasking) while
  omitting the tendency towards bloat. Load times should be snappy, and
  gaudy displays of "modern" tech should be heavily discouraged.

My hope is that these three ideals would make free, low-barrier hosting a
reality.

A non-goal is actually replacing the modern web, or even supplementing it. That
task I'll leave to someone with more knowledge, energy, charisma, and hard cash
than I. This is merely a toy for me to play with, though I'd like it to be
functional more or less as if it were a competitor.

## Hosting and DNS

The problem is that, while plenty of providers will give you free file hosting,
precious few will let you use one of their IP addresses for whatever the heck
you like.

To fulfill my first goal, then, I must compromise: I'll have to build on top of
existing infrastructure, rather than achieve total independence. This will take
the form of utilizing git providers (to at least break free of HTTP) to allow
sites to be hosted gratis on third-party servers. Of course, self-hosting is a
must, so my DNS must also handle IP addresses. I'll need a format to distinguish
between these and the URLs of third-party git repositories.

The actual DNS can likely be handled with a git repository; requests to register
a domain would be a pull request (or equivalent), with careful checks to ensure
no merge ever changes or deletes the records of others.

**TODO:** research DNS fully to understand what this'll look like. For now I'm
imagining a very simple file, like so:

```
example git@github.com:some_user/example_xyz.git
foobar 177.245.99.235
```

Given an ultra-centralized (uh oh, I know) DNS like this, TLDs become useless.
I'm considering other ways to decentralize DNS (e.g. link with other DNS
repositories), but none sound particularly useful.

## Technology

### Display (HTML replacement)

HTML is... a mess. Even if I considered it a good base for designing content,
I'd still be forced to can it due to the incredible complexity and number of
assumptions that are made, and the backwards compatibility I'd have to maintain
to make keeping it around useful.

Certain other protocols (*cough* Gemini *cough*) sacrifice universality in the
interest of spartan simplicity. That's not acceptable to me.

Markdown, despite being the second most common markup language on the web, isn't
viable: it's extremely limited (even with extensions), has parsing ambiguity,
and ultimately falls back on HTML to become even vaguely capable of the most
basic kinds of structuring.

Similar formats, like AsciiDoc or reStructuredText, could maybe have enough
usability to make the cut. However, they're just not made for this kind of
workload: they're meant for structuring a document, not an arbitrary website
anywhere between an index to a real-time first-person-shooter.

My ideals here are contradictory. I'd like simple sites to remain simple:
ideally, they'd be readable in their raw textual form. However, it must also be
able to handle a reasonably heavy website, with complex menus and interactive
animations.

The solution I've come up with is to natively support not one, but *two* textual
formats. One simpler (closer to Markdown than XML) would allow for simple sites
to remain simple, while something in the style of XML would open up a world of
complexity. Of course browsers have supported various content types from time
immemorial, but not in this way.

The trouble I foresee with this approach is nesting, i.e. iframes. If you wish
to display a simple page inside a complex page, perhaps that's not too
difficult; but if you're instead nesting a complex page into a simple page, that
could raise serious layout issues.

I can simply omit that functionality for now, though it'll become essential in
the future. The bigger problem will be ensuring that the two formats have a
similar style, which brings us to CSS.

### Styling (CSS replacement)

I dislike the state of styling today (shocker). I think that, while it ought to
be *possible* for websites to achieve a custom look, a situation closer to the
ANSI terminal would be ideal: a "default" set of colors (or more generally,
stylings) to select from, with the option to be as specific as you wish.

With my dualistic documents, I'll have to tackle the issue of consistent (and
general) styling: certain attributes won't be applicable to simple documents,
while some may be too general for complex documents. That will have to be dealt
with when I actually determine on my formats.

### Interactivity (JS replacement)

This is perhaps the most difficult element. A scripting language must be chosen
wisely, for upon it the most intricate castles will be masterminded. I obviously
want to avoid the dumpster fire that is Javascript, for many reasons. Lua is an
appealing choice, but possibly not very ergonomic for large applications.

WASM is another option, but then I have to enter into that very violent arena,
and even so I'd like a scripting language for simplicity's sake.

Python is a mess I don't want to enter into. Ruby's syntax is a little too alien
for such a purpose, as is Perl's. Languages like Rhai are neat, but too
ecosystem-specific.

So, despite its drawbacks, I think Lua is the best choice for now: it's flexible
(there are bindings in just about every language ever), simple (its basics are
easy to learn and its syntax is intuitive enough), and efficient (the available
runtimes are comparable, and often faster, than V8).

### Transport layer

The easiest thing would be to just use TCP, and that'll be how I start out.
However, I'd like to support QUIC (or similar) at some point, if that makes
sense.

Of course, given a custom protocol, I'll need to invent my own form of HTTP
headers. I'll forgo proper HTTP because it's likely overkill for my purposes:
what I come up with may be functionally the same, but I'll evade the
expectations that go with calling it HTTP.

### Storage

Apps will often want to store their data on the user's machine, and I support
that wholeheartedly. If I disallow nested sites (a la `iframe`) from accessing
this storage, I could feasibly lower the restrictions: perhaps access to a
folder on the machine, whose size is limited. I could also experiment with more
fine-grained access grants.
