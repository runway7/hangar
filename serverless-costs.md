# How to think about serverless costs

With the new pricing model that serverless computing options introduce, there's now more ways than ever to run your code - each of which have different cost implications. 

The simplest deployment system from a costing point of view is buying physical hardware and racking it at a colo, where you pay $X for the hardawre and $Y per month for the racking fees. This isn't really as popular as it used to be, because "cloud", and also because we've collectively agreed that paying $X upfront doesn't make sense when you might need $X/100 worth of hardware on most days and $X * 1000 worth of hardware on some days. Even if you don't have extreme demand variability, $X might be too much to pay upfront, not to mention that you need to drive to the colo datacenter to install your machine and have to do that again every time anything breaks down or needs to be upgraded. 

Enter cloud services, where you can now rent servers for a monthly / hourly / per second fee. This makes more sense than buying hardware in a lot of cases, but there are still situations where it's not efficient. If you have a few customers who place high value orders once a week, it doesn't make sense to leave your servers running 24/7 for that. There are also spiky situations where you suddenly need 100X the compute capacity for a short period of time - and every cloud provider still doesn't have instant start on any of their rent-a-server options. Even if they did, the nature of most load balancers and their health checks wouldn't allow scaling to happen fast enough. 

That's where serverless options come in - they follow a model where you submit your code or executable package to the cloud provider, and they have a giant mesh of sandboxes ready and waiting. Any time a request comes in, your code is loaded into one of the sandboxes in the mesh and executed. They then charge you for just that execution time. This allows a lot of efficiency that you can't get with a regular server model - your code can run just once a week or a month for instance, and you're charged for just that run and nothing else. And because cloud providers maintain a giant mesh (pseudo-infinite from your point of view) your code can run in 1, 100, or 1,000,000 of the sandboxes simultaneously with no input from you other than a willingness to pay for that capacity. 

This opens up a pressing question - which is cheaper? And the answer, as with most things, is it depends. This is a multi-dimensional problem that we're trying to boil down to a one dimensional solution, which isn't easy even with the simplest of problems. 









