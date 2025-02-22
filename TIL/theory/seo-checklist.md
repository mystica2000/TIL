# some seo checklist! 
- make sure to handle canonical.. like www.site.dev too (set up a permanent redirect 301) to site.dev
- if duplicate content, use canonical url in ``<link rel canonical>`` (to avoid penalty in ranking)
- when you deploy make sure to check whether the hosting provider supports trailing slash or not and make sure to check with build output. for example: astro build doesn't produces trailing slash and initially i didn't included any about/, blog/, but on the site, i hit with 308 redirect when i typed about to /about resutling in latency 
- sitemaps generation
