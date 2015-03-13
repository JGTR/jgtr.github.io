---
layout: post
title: "Organic systems, the pursuit of artificial intelligence, and the internet"
date: 2013-06-27 03:29
comments: true
categories: AI, artificial intelligence, 
---

After four weeks of learning at the Flatiron School, we have been immersed in object oriented thinking, and now create classes and objects to develop simple web applications. Here is a code snippet of an app that generates student profiles programmatically from a sql database, which we previously scraped from a hard-coded HTML site using Nokogiri:

{% codeblock lang:ruby %}
module StudentSite
  class App < Sinatra::Base
    get '/' do
      @students = Student.all
      erb :'students/index', :layout => true
    end

    get '/students' do
      @students = Student.all
      erb :'students/index', :layout => true
    end

    get '/students/new' do  
      erb :'newstudentform', :layout => true
    end

    get '/students/:id' do
      student_id = params[:id]
      @student = Student.find(student_id)
      erb :'students/student', :layout => false
    end
{% endcodeblock %}

As my comfort with the concept and practice of object-oriented programming increases: my thoughts have drifted to a rather impractical notion (presently): the question of whether object oriented programming may be useful in the development of artificial intelligence systems and/or robotics.

### Comparison to organic systems

Is it not true that living things move beyond the continuum from isolated components performing individual tasks (matter) to an object coordinating complex tasks over a layer of abstraction (the body), and finally a subject that learns to be aware of its own existence and place in a broader context and among other subjects (the mind/spirit of a person)?

It is interesting to note that similarly, programming has made the jump from procedural code -- a collection of logical fragments, to the construction of objects that send messages to other objects. The missing part, of course, is the ability to create true sentience that is aware of itself. Some speculate that the rise of AI is coming and the eventual singularity -- and others (like myself) are more skeptical.

For me, there are a few tentative thoughts that emerge from a consideration of animal / human intelligence and the striving to develop so-called artificial intelligence :

1. We should learn from organic systems how to create functioning bodies: the key seems to be a modular organization. Thus, a human person emerges out of augmented parts shared with many other mammals, which in themselves have no relation to the intelligence which they make possible. An intestine processes nutrients, yet these "objects" do their job (methods) and provide their needed "messages" to the rest of the objects of the system.

2. Organic systems are primarily pre-symbolic; hence much activity must happen beneath the "conscious" layer. A person is themselves an entity that is way more than the "rational" or "intelligent" layer. And paradoxically, the intelligence of a person is unaware of most of the "operations" of its own body. This sounds like objects sending messages in a dynamic system, whether glucose molecules, hormones, or flashes of neuron connectivity making-up a complex thought. 

### An emergent brain in the internet

The field of Artificial Intelligence seems to have emerged hand-in-hand with an obsession over logic and computations, and the belief that all facets of human intelligence could be reduced to logical operations. Now it has become apparent that while computers outperform humans in some tasks, they lag behind animals in something as organically simple as gracefully navigating an obstacle course. In this way, computers are not even "smarter" than animals; yet in other ways, they are smarter than any human.

Not to be discouraged by the apparent slow progress, some proponents now turn to a "meta-product" to locate in it their ideas of the emergence of artificial intelligence. The academic/internet entrepreneur Jeff Stibel argues that the internet is like a fast-developing brain. He seems to suggest that out of this flurry of HTTP requests, intelligence can emerge. Certainly there is an element of truth in this statement. For example, Tim Berners-Lee, creator of the internet, gave an interview in which he mentioned there were more webpages in existence than neurons in a human brain. Certainly that is a remarkable fact.

Another interesting thing about this proposition, is that really the internet does seem to emulate an organic structure much more than a computer program could. It is also modular in the sense that it operates as a series of physically real objects (severs and infrastructure), exchanging abstracted objects HTTP requests, which have some meaning beyond themselves -- content of all types. As exciting as I find this idea, there are elements that are questionable.

Is the internet really all that new? To what extent is it that different from a conversation held next to a fire in some cave thousands of years ago? Both events are the unfolding of meaning to different receivers. Whether the object is a packet of information or sound waves with meaning -- the same communication seems to be taking place. It is true that technology has enabled this communication to happen on a different scale, to stretch over time, to exchange image and sound and video. But it is still just one big conversation. 

I think the unprecendented scale will have an impact. But can we be sure the impact of the internet will be unequivocally good? Now we can see photo after photo of cats,get blown away by online ads, peruse pornography, cast uninformed opinions, and lurk the nationally-bounded newsmedia. At the same time, internet users can rely on wikipedia, share real knowledge or information with many people, take an online course, and many other things.  

Thus, I am perhaps less optimistic that any premise of artificial intelligence may emerge from the soup of HTTP requests which model ordinary living. It is my opinion that the web not be fetishized into this abstracted entity of hope and desire (for political liberation, for commerce, to ground abstract academic theories) -- but rather a tool that enriches human life. Hasn't this been the point all along?

### Reining in the web to enrich life

One of the implicit assumptions about the quest for artificial intelligence is that humans are (terminally) intelligent at all. Clearly we have some intelligence -- but is it the end? Edward de Bono has shown how thinking itself has software -- and this software is somewhat deficient and can be developed and improved. I think the act of programming can serve as a discipline for thought -- similarly, the web can become a tool for good thinking as well. We can quickly find information, and share it, and focus on the complex problems of managing large datasets. However, it still remains the responsibility of us users to become the architects of possibility in the internet. 

Programmers in particular, can build the infrastructure that makes good things possible: whether wikipedia, gittip, social ventures, and so on. But one thing we must not do, is rest on the laurels of a relatively instinct-driven, overstimulated patterns of behavior in the internet, and pretend that this will bring improvement or liberation by default.
