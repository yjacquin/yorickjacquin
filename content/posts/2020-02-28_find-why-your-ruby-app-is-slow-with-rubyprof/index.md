---
title: "Find why your Ruby app is slow with ruby-prof"
author: "Yorick Jacquin"
date: 2020-02-28T19:46:01.249Z
lastmod: 2020-04-26T16:59:37+02:00

description: ""

subtitle: "Feeling like your Ruby app should be faster? It was better back in the days? Things are not the same anymore? You find yourself looking at…"

image: "/posts/2020-02-28_find-why-your-ruby-app-is-slow-with-rubyprof/images/1.jpeg" 
images:
 - "/posts/2020-02-28_find-why-your-ruby-app-is-slow-with-rubyprof/images/1.jpeg" 
 - "/posts/2020-02-28_find-why-your-ruby-app-is-slow-with-rubyprof/images/2.gif" 


aliases:
    - "/find-why-your-ruby-app-is-slow-with-ruby-prof-5731aa44bbcb"
---

![image](/posts/2020-02-28_find-why-your-ruby-app-is-slow-with-rubyprof/images/1.jpeg)

Photo by [Krzysztof Niewolny](https://unsplash.com/@epan5?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/snail?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText)



Feeling like your Ruby app should be **faster**? It was better **back in the days**? Things are not the same anymore? You find yourself **looking at other apps backends**? Well have you met [**ruby-prof?**](https://ruby-prof.github.io/)ruby-prof is a gem that will **shed some light** on the **speed** of your app. With it you can test a whole application or just an obscure block of code and compare **within your file** the fastest solution for your problem.### So how does it work?

First, you need to install it:
`gem install ruby-prof`

Then, it’s quite easy to use, you can either use it from the command line as follows:
`ruby-prof [options] &lt;script.rb≥ [- -] [script-options]`

Or use it from within your files with its convenience API:
`require &#39;ruby-prof&#39;  

# profile the code  
RubyProf.start  
# ... code to profile ...  
result = RubyProf.stop  

# print a flat profile to text  
printer = RubyProf::FlatPrinter.new(result)  
printer.print(STDOUT)`

You will easily find [in the doc](https://ruby-prof.github.io/) the different graphs you can use to visualize data from the generated report.### So how can it be useful in day to day life?

The best usage you can make out of this gem is by analyzing your controllers and especially your API Endpoints.

You will often find out that your slow performance is not made up, does not always come from slow libraries but may come from unexpected HTTP requests, nested loops that don&#39;t need to be nested, etc…

Let me tell you about my story:
> It&#39;s christmas, you’re almost alone at work, no one comes asking you a question about this or that, no meetings: **paradise** 🌴> You jump into a **big refactoring** of the core of your app. It&#39;s a **make over**: Inheritance, Abstraction, you show off your OOP skills in such a beautiful refact that has more than **1,500** ligns of diff across **20+** files.> The code gets reviewed, approved, you&#39;re a **happy** dev. The QA is about to test, tests are all green, code goes to release, release is done. You&#39;re an **even happier** dev.> But then we suddenly realize that we lost a tone of performance on a route but my code was not supposed to affect performance that much!



![image](/posts/2020-02-28_find-why-your-ruby-app-is-slow-with-rubyprof/images/2.gif)

You&#39;re a **very sad** dev



The response time was multiplied by **~2000**. 😱

I look at the code and don&#39;t find anything I&#39;ve done that might come close to that performance loss. My pull request concerned a central service used by our top 3 routes and all those routes were impacted, **it must have been me!**  
You look briefly at the content of the release and nothing that could impact the performance… **odd…**

Then I find **ruby-prof**, used the gem with its **convenience API** and I see that what takes the most time by far is [**rest-client**](https://github.com/rest-client/rest-client)****.
`%self      total      self      wait     child     calls  name                           location  
  0.03      0.001     0.000     0.000     0.000        1   &lt;Module::RestClient::Utils&gt;#_cgi_parseparam /usr/local/bundle/gems/rest-client-2.0.2/lib/restclient/utils.rb:40  
  0.01      0.000     0.000     0.000     0.000       11   String#[]  
  0.00      0.001     0.000     0.000     0.001        1   Enumerator#each  
  0.00      0.000     0.000     0.000     0.000        1   Class#new  
  0.00      0.000     0.000     0.000     0.000        2   String#index  
  0.00      0.000     0.000     0.000     0.000        2   String#strip  
  0.00      0.000     0.000     0.000     0.000        1   NilClass#nil?  
  0.00      0.000     0.000     0.000     0.000        1   String#scan  
  0.00      1.159     0.000     1.158     0.001        1   RubyProf::Profile#_inserted_parent_  
  0.00      0.000     0.000     0.000     0.000        1   Exception#initialize  
  0.00      0.000     0.000     0.000     0.000        1   String#count  
  0.00      0.000     0.000     0.000     0.000        1   Kernel#nil?  
  0.00      0.000     0.000     0.000     0.000        1   Array#count  
  0.00      0.000     0.000     0.000     0.000        1   Kernel#block_given?`

I spare you the whole report as it spanned across 20 threads…

But wait, I did not involve any **HTTP** **call,** so I look for the method that ruby-prof reports, and find that it was related to changes made in another file by one of my colleagues.

The code was making third party HTTP requests, and in some cases within loops! **I found the needle in the haystack!**

The code has been corrected, performance was back to normal (_even faster than before the refact_) and my **love story** with ruby-prof started!

I now use it to compare performance before and after a refact, as it may be a useful metric.

I therefore **strongly recommend** you to keep it warm in your `:development` group of your Gemfile, as it may save your life one day.

### If this article was helpful to you feel free to 👏 clap 👏 or share it with you coworkers !
