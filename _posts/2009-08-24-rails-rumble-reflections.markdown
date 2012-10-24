---
layout: post
title: "Rails Rumble Reflections"
alias: /post/170966735/rails-rumble-reflections/index.html
---

I competed in [Rails Rumble](http://r09.railsrumble.com/) 2009 over the weekend. It was my first time competing, so I wanted to share some of what I learned while working on a project from start to finish with other people in a 48 hour window.

### Get a real designer

Our team consisted of three developers and no designers. We could all write HTML and CSS and develop front ends, but we weren't designers. We didn't possess the talent necessary to develop a really stunning interface, while actually developing the rest of the application in the time allotted.

Although we produced a full featured application that ended up looking halfway decent, there is no doubt in my mind that we would have benefited immensely if we had a real designer. And by designer, I mean someone who actually makes a living doing design work for web applications.

### Be opinionated, but be flexible

Any project with multiple engineers is bound to bring about disagreements. People are going to discuss and argue their points. I believe that when it comes to software development, you must be opinionated to develop good software. Having strong opinions about something means you are passionate about it. When you're constrained by a 48 hour window, being opinionated is even more important. You don't have the luxury of having an entire discussion about every little thing that comes up.

The problem is that when opinionated people disagree, they spend a lot of time in heated discussion. Normally, these discussions will lead to a better overall implementation and everyone benefits, but when you're constrained by time, you must not only be opinionated, you must also be flexible. You have to pick your battles and make concessions.

### Working with people you trust

Being able to make concessions is related to another point I have. You should work with people you trust. If you don't trust the people you are working with, you are going to have a hard time giving in when the other person yells louder. You aren't going to be able to sit back and say: 

> "I'm working with this person for a reason. I have to trust them, they are very passionate about this."

Being able to trust your team members is not only important during a competition like this one, it's always important. The difference with a competition, though, is that you just don't have the time to spend on every disagreement.

### Don't write tests

Nowadays a lot of people talk about test drive development. Write tests for code that doesn't exist yet. When you write the code, you know you're done because it passes the tests. The problem with [TDD](http://en.wikipedia.org/wiki/Test-driven_development) is that it can be time consuming. Additionally, because you only have a 48 hour window, the scope of your application must be small enough to complete. If it's small enough to complete, it will probably be small enough to manually test throughout the competition.

I spoke to some other contestants who feel the same way. Some even went into the project doing tests and quickly abandoned them because they were too time consuming. Even [Lowdown](http://lowdownapp.com), a Rails Rumble entry specifically built to help developers, designers and managers collaborate on [Cucumber](http://cukes.info/) feature stories [said](http://seancribbs.com/tech/2009/08/24/lowdown-our-rails-rumble-09-application/):

> Ironically – especially considering the purpose of our application - we didn’t test, spec, or write features. There were times we could have benefitted from TDD, but many other times it would have just slowed us down. We considered the app to be a prototype/proof-of-concept anyway, not a polished and hardened work of craftsmanship.

I think this is the key. Testing is good, but testing during the competition could prove fatal. If you only finish a third of your application because you were busy writing tests, you don't get more points - even if that third is really perfect. It's better to get to a feature complete state as early as possible and then iterate and iterate and iterate until the last moments of the competition.

### Conclusion

As you can probably tell, I am *very* opinionated when it comes to software development. Being opinionated can also leak into the rest of my life. This article, these reflections, are *my* reflections. I'm sure many other participants, possibly even my own teammates, don't agree with me - I'm okay with that. The fact is, nothing I said here (other than having a real designer on your team) is necessarily "right". It is, however, right for me and probably a lot of other people.

In the end, I was lucky enough to work with smart people and smart people can work through differences to deliver something they are proud of. And despite our differences, the three of us were really happy that we were able to deliver the application we set out to develop. Being proud of that accomplishment is something we could all agree on.

I'm really looking forward to Rails Rumble 2010.