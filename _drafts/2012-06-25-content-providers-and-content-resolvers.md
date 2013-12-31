---
layout: post
title: Content Providers & Content Resolvers
date: 2012-06-25
permalink: /2012/06/content-resolvers-and-content-providers.html
comments: true
---

<p>Content Providers and Content Resolvers are a common source of confusion for beginning Android developers. Further, online tutorials and sample code are not sufficient in describing how the two classes work together to provide access to the Android data model. This post hopes to fill in this gap by explaining their place in the <code>android.content package</code>. It concludes with a walk through the life of a simple query to the Content Resolver.</p>

<h4>The <code>android.content</code> Package</h4>

<p>The <a href="http://developer.android.com/reference/android/content/package-summary.html"><code>android.content</code></a> package contains classes for accessing and publishing data. The Android framework enforces a <b>robust</b> and <b>secure</b> data sharing model. Applications are <i>not</i> allowed direct access to other application's internal data. Two classes in the package help enforce this requirement: the <code>ContentResolver</code> and the <code>ContentProvider</code>.</p>

<h4>What is the Content Resolver?</h4>

<p>The Content Resolver is the single, global instance in your application that provides access to your (and other applications') content providers. The Content Resolver behaves exactly as its name implies: it accepts requests from clients, and <i>resolves</i> these requests by directing them to the content provider with a distinct authority. To do this, the Content Resolver stores a mapping from authorities to Content Providers. This design is important, as it allows a simple and secure means of accessing other applications' Content Providers.</p>

<p>The Content Resolver includes the CRUD (create, read, update, delete) methods corresponding to the abstract methods (insert, delete, query, update) in the Content Provider class. The Content Resolver does not know the implementation of the Content Providers it is interacting with (nor does it need to know); each method is passed an URI that specifies the Content Provider to interact with.</p>
<a name='more'></a>

<h4>What is the Content Provider?</h4>

<p>Whereas the Content Resolver provides an abstraction from the application's Content Providers, Content Providers provides an abstraction from the underlying data source (i.e. a SQLite database). They provide mechanisms for defining data security (i.e. by enforcing read/write permissions) and offer a standard interface that connects data in one process with code running in another process.</p>

<p>Content Providers provide an interface for publishing and consuming data, based around a simple URI addressing model using the <code>content://</code> schema. They enable you to decouble your application layers from the underlying data layers, making your application data-source agnostic by abstracting the underlying data source.</p>

<h4>The Life of a Query</h4>

<p>So what exactly is the step-by-step process behind a simple query? As described above, when you query data from your database via the content provider, you don't communicate with the provider directly. Instead, you use the Content Resolver object to communicate with the provider. The specific sequence of events that occurs when a query is made is given below:</p>

<ol>
  <li value="1">A call to <code>getContentResolver().query(Uri, String, String, String, String)</code> is made. The call invokes the Content Resolver's <code>query</code> method, <i>not</i> the <code>ContentProvider</code>'s.</li>
  <li value="2">When the <code>query</code> method is invoked, the Content Resolver parses the <code>uri</code> argument and extracts its authority.</li>
  <li value="3">The Content Resolver directs the request to the content provider registered with the (unique) authority. As seen in the  <a href="http://goo.gl/yrQUV">source code</a>, this is done by calling the Content Provider's <code>query</code> method.</li>
  <li value="4">When the Content Provider's <code>query</code> method is invoked, the query is performed and a Cursor is returned (or an exception is thrown). The resulting behavior depends entirely on the Content Provider's implementation.</li>
</ol>

<h4>Conclusion</h4>

<p>An integral part of the <a href="http://developer.android.com/reference/android/content/package-summary.html"><code>android.content</code></a> package, the <code>ContentResolver</code> and <code>ContentProvider</code> classes work together to ensure secure access to other applications' data. Understanding how the underlying system works becomes second nature once you've written enough Android code, but I hope that someone finds this explanation helpful some day.</p>

<p>Let me know if you have any questions about the process!</p>