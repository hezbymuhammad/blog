## Lesson Learned from Implementing Accelerated Mobile Pages (AMP) on Karir.com.

### About AMP
Recently, google announced their project to improve overall user experience in accessing web. Their project’s name is Accelerated Mobile Pages (AMP). AMP is basically restricted version of HTML. Think of it as Google Weblight, but with more freedom to design user interface and user experience. It’s still like normal HTML, but with some restriction:

1. Not all HTML tag are allowed. Tag like `<img>` are replaced with AMP-specific tags <amp-img>.
2. Javascript are not allowed. Only prebuilt AMP JS are allowed.
3. External CSS are not allowed.
4. Maximum size of embeded CSS is 50 kilobytes.

AMP built with 3 components: AMP HTML, AMP JS and AMP Cache. To put it simply, those components work together to create custom Google Weblight pages. Google claims that by using AMP, your page can be loaded instanteniously.
You can read more about AMP [here](https://www.ampproject.org/learn/overview/).

### Rails and AMP
AMP is a trending tech innovation, and I think everyone will start using it and get the benefit of using it especially because the big name of Google backing it.
At [karir.com](https://karir.com), we truly understand the potential of implementing AMP on our platform. So, we decided to give it a try. We found two possible way of doing it:

1. Using gem. We want a gem that provide robust way of creating amp page with minimal fuss. After searcing for a while, we had 2 possible gem: `amp-html` and `rails_amp`.
2. Just do it our own FTW! We want to avoid this option as much as we could, because do it our own requires ground up aproach and takes more time than using gem.

### AMP Using Gem
At the beginning, we tought using gem was the simplest way. At that time (until this story’s written), AMP itself is a relatively new technology and because of that gems about AMP are still in beta development stage.

First try, we choose `amp-html` gem because it provide us robust way of not only creating amp pages, but also testing it. However, turns out one of our already installed gem broke dependency of amp-html.
Next chance, we tried `rails_amp` gem. It is a nice gem that provide abstraction to create amp page. All you have to do is add new `*.amp.erb` in your views. However, after trying to implement it, we encountered problem of `rails_amp`’s inability to use slim instead of erb as template.

To sum up, both `amp-html` and `rails_amp` didn’t make it. I think the reason was both `amp-html` and `rails_amp` haven’t yet reached stable version. So, they may give solution to amp implementation on rails, but unfortunately in our case it didn’t. We learned that using gem generally makes work simpler, but sometimes when the gem didn’t reach stable version it doesn’t.

### Do It Our Own
Making our own AMP pages requires rewrite of our regular page so it can satisfy AMP requirements. We define our AMP pages as sub namespace of our Mobile pages. The route will be `https://m.karir.com/amp/pages/:id`. Below is step by step on how we’re implementing AMP.

1. Removing standard HTML that didn’t satisfy AMP. In this step, we removed script, standard css, img tag, form, etc. The goal of this step was to make barebone plain AMP-HTML without style and interaction. To make sure we didn’t mess up, we integrated Node package for AMP called `amphtml-validator` in our rspec. You can learn more about validating AMP page manually or through CI/CD process [here](https://www.ampproject.org/docs/guides/validate).
2. Styling. Because AMP can only contain maximum 50 kilobytes CSS, we have to manually select style that was used by particular page. This step was the most tiresome process. You can read more about AMP style and layout specifications [here](https://www.ampproject.org/docs/guides/responsive_amp).
3. Implement custom script. AMP page don’t use standard javascript. By default, there is no way we can capture user interaction through script. However, AMP provide us some simpler way of doing this. We can use their built in interaction by using AMP HTML attributes. You can learn more about capturing user interaction with AMP in this [link](https://www.ampproject.org/docs/reference/components).
4. We’re using newrelic tracking gem, and by default it will inject our page with newrelic script. At first, we don’t realize it because the test was green all the time. But, when AMP code deployed to our production, google didn’t reckognize our AMP page. Something didn’t quite right. We decided to check AMP validation manually (not through CI/CD) by using chrome extension AMP validator. Turns out, newrelic script was injected by newrelic gem and that gem only run in production. The solution is to disable script injecting on AMP pages. You can learn more about it in this [link](https://docs.newrelic.com/docs/browser/new-relic-browser/troubleshooting/google-amp-validator-fails-due-3rd-party-script).

### Conclusion
We’re unable to use gem to implement AMP on our platform. Using gem wasn’t enough. Instead of, we implemented it our own. It was not an easy task, but it wasn’t hard either.

### Lesson Learned
1. We cannot always depend on gem. Gem are built by community and for technology which are new and growing rapidly, finding right gem can be difficult. That case, giving a shot on implementing it your own maybe will give you better solution than using gem. Or even, you can create your own gem if you want!
2. Some task is hard because it IS hard, others because it actually is a simple task but requires more dedication and boring stuff. We will never know until we try, right?
3. One hundred test coverage and green tag doesn’t guarantee our application running fine. Always do double check before deploying your code to production.