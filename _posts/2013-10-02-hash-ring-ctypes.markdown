---
layout: post
title: Minimizing page load times with <code>hash-ring-ctypes</code>, a fast Python hash ring
hp_title: Minimizing page load times with <code>hash-ring-ctypes</code>
summary: Using <code><a href="https://github.com/mattdennewitz/hash-ring-ctypes/">hash-ring-ctypes</a></code> to parallelize asset loading.
date: 2013-10-02 09:00:00
---

Today, I released a new project on GitHub. It's called (droll, I know)
[`hash-ring-ctypes`](https://github.com/mattdennewitz/hash-ring-ctypes/),
and it's a Python ctypes-based wrapper around
[libhashring](https://github.com/chrismoos/hash-ring/).

At Pitchfork, we use a hash ring to parallelize site asset downloads
from our CDN across four domains. Here's the gist: browsers can only 
download so many files from a single domain at a time, and we want to maximize
that to reduce page load time. The easiest solution is to shard
our CDN domain into four segments using CNAMES, creating a cluster of four new domains:
`cdn.pitchfork.com` through `cdn4.pitchfork.com`,
We then populate a hash ring with those domains,
creating four points (&#8220;nodes&#8221;) in the ring.
The ring can then take a value &mdash; in our case, a relative path to an asset
like &#8220;`albumreviews/1/cover.jpg`&#8221; &mdash;
and return the corresponding node. Unless we add or subtract from the ring,
the lookup function is idempotent.

I realize that I haven't explained how nodes are placed into the ring,
or how nodes are looked up. For a thorough explanation
of consistent hashing and the concept of a hash ring, check out
[this](http://michaelnielsen.org/blog/consistent-hashing/)
and [this](http://www.martinbroadhurst.com/Consistent-Hash-Ring.html).

### How do I use this thing?

For now, install from GitHub via `pip`:

{% highlight bash %}
$ pip install -e git+https://github.com/mattdennewitz/hash-ring-ctypes#egg=hash_ring
{% endhighlight %}

To use, import `hash_ring` and create an instance of its `HashRing` class.
Nodes can be provided at creation via the `nodes` kwarg:

{% highlight python %}
import hash_ring

nodes = ['cdn.pitchfork.com', 'cdn2.pitchfork.com', 'cdn3.pitchfork.com']

# create the hash ring with our cdn domains
ring = hash_ring.HashRing(nodes=nodes)
{% endhighlight %}

This class's default settings will create five &#8220;replicas&#8221; for each node
you add. Replicating each node in this fashion ensures that no specific node
is used more than the rest. You can specify the number of replicas
with the `replicas` kwarg:

{% highlight python %}
# create a ring with 10 replicas
ring = hash_ring.HashRing(replicas=10, nodes=nodes)
{% endhighlight %}

Nodes can also be added and removed during the life of the ring:

{% highlight python %}
# add 'cdn4'
ring.add_node('cdn4.pitchfork.com')

# remove 'cdn2'
ring.remove_node('cdn2.pitchfork.com')
{% endhighlight %}

Now that we've populated our hash ring, we can use it to look up
a domain for a specific asset:

{% highlight python %}
# find the CDN domain for a given file path
fn = 'albumreviews/1/cover.jpg'
cdn_domain = ring.lookup(fn)
{% endhighlight %}

`cdn_domain` will be one of the CDN domains in the ring.
The domain name and file path can be glued together to form
the asset's CDN URL. Doing this for all assets on my page
ensures that they're distributed across several domains,
thus achieving higher parallelization of asset downloads.

### Implementation

Here's a simple implementation to illustrate the entire process:

{% highlight pycon %}
>>> import urlparse
>>> import hash_ring

# create hash ring for cdn domains
>>> nodes = ['cdn.pitchfork.com',  'cdn2.pitchfork.com',
...          'cdn3.pitchfork.com', 'cdn4.pitchfork.com']
>>> ring = hash_ring.HashRing(nodes=nodes)

>>> def cdn_url(path):
>>>    return urlparse.urljoin(ring.lookup(path), path)

>>> cdn_url('albumreviews/1/header.jpg')
'cdn.pitchfork.com'

>>> cdn_url('albumreviews/10000/header.jpg')
'cdn2.pitchfork.com'
{% endhighlight %}

Register `cdn_url` with your template system, and you'll have your
asset URLs automatically hashed to maximize parallel downloads.

<code class="blip">&#35;</code>

I hope this library and explanation helps. Please fork the project
and file issues on GitHub. Documentation is available on
[RTFD](http://hash-ring-ctypes.readthedocs.org/en/latest/).
