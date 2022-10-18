# DjangoCon 2022 Day 1 (20221017) Notes

I had a fantastic day, learned a lot, and met some great people. Here are the notes from the talks I attended today.


## Day 1 - 0945 - Keynote

- Jay Miller [@kjaymiller](https://twitter.com/kjaymiller)
- Melanie Arbor [@melaniearbor](https://twitter.com/melaniearbor)

They both have faced struggles and challenges, personally and professionally.

### Content

- Used analogy of swimming (and inexperienced swimmers) to coding (and folks new to coding and to our community)
- Country Clubs - historical racist foundations - used as a way to keep segregated spaces despite integration efforts/laws in the past
- Talked about PyCon Host City Diversity - are we as a community meeting the mark when selecting host cities that are diverse and not super-costly?
- Something like 7 times more likely for Black and indigenous children to drown vs white children - much due to lack of training opportunities
	- Similarly, the lack of educational opportunities for underrepresented groups keeps them out of tech
	- Studies show that racial and gender discrimination in tech has increased recently - we have a lot of work to do
- Stats show an overwhelming *male/cis/straight/white* population in tech, with the result being...
	- Policies are made with a focus on this population
	- Companies focus their marketing on this population
- Geographic separation, discrimination, and lack of opportunities > A trifecta hurting underrepresented groups

- How to protect those in AND around the water (tech)? **LIFEGUARDS**
	- Help people to feel that *anyone* can jump in the water (tech)
	- Add Code of Conduct in your projects and community events and actually mean it
		- Know
		- Use
		- Enforce
	- Watch out for sharks!!
	- Be a lifeguard - the greatest contribution to tech you can make is bringing someone new into tech!
- Shout-outs from the speakers
	- **Marlene Mhangami** - Diversity Chair, PSF
	- **Naomi Ceder** - Trans*Code
	- **Iqbal Abdullah** - Organizer in SE Asia
- Uplift other folks - If I have a way to assist with training/opportunities, share with folks in need
- When you're the swimmer, don't try to stay afloat by holding others down
- Code of Conduct is how we care for everybody in the community

Interesting company mention (among several others): Underdog Devs - employing formerly incarcerated folks in the tech world

---

## Day 1 - 1030 - What IMPACT will you have?

Calvin Hendryx-Parker
[@calvinhp](https://twitter.com/calvinhp)
Co-founder & CTO at Six Feet Up

### Content

- What impact could a decision you made 20 years ago have today? Django is now about 20 years old
- Six Feet Up goal to complete 10 impactful projects by 2025
- Talked about RevSys and Octopus Energy, Torch Box, Marine Conservation Society, Lincoln Loop
- Some projects they work on: mapping forest fire trajectories, predicting lightning strikes, and streamlining battery energy operations
- Be intentional about using technology for good and in ways that can be impactful to humankind

---

## Day 1 - 1110 - The Django Admin is Your Oyster
### Let's extend its functionality

Adrienne Franke (pronounced like "Frankie")
[@adriennefranke](https://twitter.com/adriennefranke)

### Content

- Internal tool for trusted users
- Docs say to beware of over-customization, but Adrienne disagrees - go for it!

- Look at [the demo repo](https://github.com/adriennefranke/djangocon2022) for examples of things you can do
- Make good use of `prefetch_related()` & `select_related()` when querying relationships
- Use the `clean()` methods in Admin to provide validation
- Within the `clean()` method, we can add additional `cleaned_data` objects that can then be used in the `save()` method
- It's relatively easy to add a sandbox database, and use it from the same project Admin
- Customizing Admin Templates
	- templates > admin > appname > change_form.html
	- You can define a variable in the Admin, and then apply that variable to the template. Can be used to add extra help/context to each Admin page, for instance
- Admin actions - e.g. Apply a discount to a number of objects, hit an external API to update values for a number of objects, etc

---

## Day 1 - 1200 - Documenting Django Code in 2022

Eric Holscher
[@ericholscher](https://twitter.com/ericholscher)
Co-founder of [ReadTheDocs](https://readthedocs.org/) and [WriteTheDocs](https://www.writethedocs.org/)

### Content

4 Elements of Documentation

- _Authoring_
	- Structure
		- **[Diátaxis](https://diataxis.fr/)** - A framework for thinking about documentation
		- Organized by User needs - not how the author thinks about it
		- 2 Axes of Diátaxis framework:
			- Study (learning) vs Work (doing) - Sometimes we are in one mode or the other
			- Practical (how to do something) vs Theoretical (state of things, ways to think)
		- The 4 Quadrants of Diátaxis framework:
			- Tutorials: Practical & Study (*teaches*)
			- How-to Guides: Practical & Work (*shows*)
			- Explanation: Theoretical & Study (*explains*)
			- Reference: Theoretical & Work (*lists*)
	- Tools
		- Sphinx - uses reStructuredText
			- Has good Markdown (with directives) support with MyST
			- *Note: You can mix RST and Markdown in the same project*
		- MkDocs - uses Markdown (check out Material for MkDocs)
- _Theming & Design_
	- **[Furo](https://pradyunsg.me/furo/quickstart/)** - a nice new Sphinx theme
- _User Experience_
	- **[sphinx-design](https://sphinx-design.readthedocs.io/en/latest/)** - an extension with nice UI elements for docs
		- Bootstrap-based
		- Support for many themes
	- **[sphinx-tabs](https://sphinx-tabs.readthedocs.io/en/latest/)** - Simple tabs display
	- **[sphinx-copy-button](https://sphinx-copybutton.readthedocs.io/en/latest/)** - Adds a code copy button to each code block
	- **[sphinx-hoverxref](https://sphinx-hoverxref.readthedocs.io/en/latest/)** - Popup hover cards
	- **[readthedocs-sphinx-search](https://readthedocs-sphinx-search.readthedocs.io/en/latest/)** - Live search in modal
- _Deployment_
	- RTD is adding lots of extensibility
	- **[build.jobs](https://docs.readthedocs.io/en/stable/build-customization.html)** - Allows customization of build jobs with a number of hooks
	- **[build-commands](https://docs.readthedocs.io/en/stable/build-customization.html)** - Override the entire build process

---

## Day 1 - 1400 - Building a Dev-Focused Learner Management System with Django

Sheena O'Connell
[@sheena_oconnell](https://twitter.com/sheena_oconnell)
CTO of [Umuzi](https://www.umuzi.org/)
https://sheenarbw.github.io/pres-djangocon-2022-tilde/

### Content

Umuzi
- Invests in people and teaches them tech skills
- Pays people to learn once they show that they would benefit from the investment
- Holistic education (not just the technical stuff)
	- Teamwork, professionalism, wellness, financial literacy

<p></p>

*Then, COVID*

<p></p>

Problems:
- Employee turnover
- 250 learners needing help
	- Couldn't just go home because they might not have the tools and structure there to complete the courses
- Don't want to let down students or the funding partners who had already invested

How to resolve this?
- Started with keeping attendance, mostly as a smoke detector (make sure students are getting what they need and taken care of)
- Started with a bunch of Google forms
- System was insufficient, didn't just need butts in specific seats at specific times...
	- Needed accountability mechanisms
	- Needed asynchronous interactions
	- Needed self-paced learning
	- Needed smoke detection tools
- Maybe a Learning Management System (LMS)?

Identified Needs:
- Integrate existing syllabus git repo without too much work
- Reduction in dependency of staff
- Simulate real-world work environments - treat learners like professionals
- Integrate with GitHub - limited support in existing LMS tools

So, they build [Tilde](https://github.com/Umuzi-org/tilde):
- Kept it lean - laser focus on learners
- Deep GitHub integration
	- Repo Project cards automate Repo creation, protection of main branch, etc
- Includes Per reviews and improvement suggestions
- Essentially a Kanban workflow with review stages
	- If many learners mark a card good, but a trusted reviewer disagrees, then there is a knowledge gap that can be plugged
	- If a learner's card bounces between working and review, they may need additional help
	- If a particular project bounces between review and rework for all learners, maybe it needs revision
	- etc

Under the Hood:
- Keep it boring/simple
- [Syllabus](https://github.com/Umuzi-org/ACN-syllabus) as code using [Hugo](https://gohugo.io/) for static site generation
- Generates a knowledge graph of learning which can be mined to better help learners move forward
- Architecture used
	- Hugo
	- Django/dramatiq/Postgres
	- DRF
	- Material UI / React / Redux / Saga

Success!
- Learners are doing fine
- Most learners who complete the course have found long-term jobs
- Have successfully run multiple cohorts of learners through this process
- Hires Juniors from within the Umuzi program, which helps simplify on-boarding

---

## Day 1 - 1450 - Predicting Lightning Strikes with Django and AWS

Calvin Hendryx-Parker
[@calvinhp](https://twitter.com/calvinhp)
Co-founder & CTO at Six Feet Up

### Content

- TampaBay has an inordinate amount of lightning strikes in the US
- Novel algorithm developed by Jason Deese in Jupyter Notebook
	- Takes in Radar data and outputs projected strike locations
	- Wanted to scale to cloud-native and low-latency request/response cycle
- Algorithm predicted lighting strike locations ~25 minutes before it actually happened
- LOTS of data - publicly available data from multiple NOAA Radars (up to 15 MB per sweep)
- Models need to be rebuild continuously with new data inputs
- Original Notebook took 30 s, got down to 500 ms
- Wanted cloud-native from the start
- Wanted to keep stack simple
- Developer experience
	- Keep things local for developer
	- Keep environment consistent
	- Docker/Slack/GitLab
	- Infrastructure as Code: using Terraform
	- Lambdas / S3 / ECR Repos / etc
	- Makes use of [localstack.cloud](https://localstack.cloud/) to simulate a cloud environment locally
- Next stages - MLOps
- Lambdas can be used successfully for some things that may end up being very costly - they're no the be-all-end-all solution for everything. Use the right tool for the job.


*Important note: be careful with Redis caching & serialization if you plan to use the data both with django and outside of django*

---

## Day 1 - 1550 - Miracles in Anarchy

Timothy Allen
[@FlipperPA](https://www.twitter.com/FlipperPA)
Wagtail Core Team

### Content

- "Anxiety is the key ingredient to Impostor Syndrome"
	- We imagine that others knows a lot and us a little, but in reality all of us understand pieces of the puzzle. See https://billwatts.org/the-imposter-syndrome/
	- None of us understand all the layers
	- Nobody can know it all

- There are so many dependencies, it's amazing that anything in the modern world works at all
	- The whole world relies on Python
	- Modern world relies on trust

- Adventures in Tim's career:
	- Building a custom bank service (and credit card) for David Bowie
		- Backend used Cobol, PHP 3, & MySQL3
		- Frontend was Flash
		- Bowie Bank!
	- Querying Company Codes
		- Used fusion code to query wide tables
- Stories from other companies:
	- The missing Windows 9
		- Because so many developers relied on things like (version starts with "9"), Windows 9 would break things across the ecosystem

- Your voice is as important and valid as anyone else's in the community

- Tim's analogies from the 12 Traditions
	1. We're at our best as a community when we work toward inclusion - we are greater than the sum of our parts
	2. Leaders are trusted servants - not governors
	3. If you want to be a member of the community, you are a member
	4. Each opensource community is an autonomous fellowship. More communities mean more people can get involved.
	5. Openly state the purpose of your organization/project/community
	6. Django is specific about how trademarks may be used
	7. Django is funded by those who use it (individuals and companies)
	8. When work transitions beyond volunteering, it should be paid
	9. Django has a number of committees focused on different ways of helping the community
	10. Django has to be careful about any outside politics/purposes lest we fail to be inclusive
	11. Public relations policy based on attraction rather than promotion - a good, enthusiastic community brings more good people to the community
	12. Principles before personalities - Django is not personality-driven

- DjangoCon is By the Community, For the Community
- Practice makes progress - Progress is where life happens

---

## Day 1 - 1640 - Massively increase your productivity on personal projects with comprehensive documentation and automated tests

Simon Willison
[@simonw](https://www.twitter.com/simonw)

### Content

- Unit testing and internal documentation makes it possible to bring together engineers working across 3 continents - and can help you speed up and keep up with a LOT of projects
- The perfect commit: Implementation + Tests + Documentation + a link to an Issue thread
	- **Implementation**
		- A software Engineer's job is to *change* software (the software base usually already exists)
	- **Testing**
		- Prove that implementation works
		- Pass if correct, fail otherwise
		- Seems like a lot of additional work, but if you start with tests, adding incremental tests is really easy
		- Simon's rule: Every project starts with a test, not matter how small - what matters is that the test suite works
		- He generally starts with one of 3 templates
		- He uses GitHub actions and repository templates along with Cookiecutter to quickly build the foundation for apps
	- **Documentation**
		- Should live in same repo as the code
			- Otherwise, docs go out of date and people are less likely to contribute and trust the code
			- Can enforce doc changes through code reviews before committing
			- Bonus trick: Enforce that things are documented to validate that there are documentation sections for each feature (tests that check that headers exist for plugins, etc)
	- **Everything links to an Issue thread**
		- Use Issues more effectively. It's okay to 'talk to yourself' in Issue threads
		- Helps document future actions and explain past actions
		- Add links to documentation, inspiration, SO threads, etc
		- Add code snippets, false starts
		- Add decisions - why did we take this approach? What led to this decision? What else did we consider?
		- Screenshots. Animated screenshots are even better
		- When you close an issue add a link to the relevant docs and documentation

- Issues are Temporal Documentation, and can be used to document personal progress, research, and ideas justas you might for professional work.
	- Time-stamped and contextual
	- Nobody will be upset if you don't update this form of documentation in the future
    - Simon wrote about [his trick for moving issues from a private repo to a public repo](https://til.simonwillison.net/github/transfer-issue-private-to-public)\*, which is not something you can accomplish by default. This is particularly nice if you are writing 

- You can quickly pick projects back up later, even if you forgot them completely
	- You don't have to remember anything
	- If you lose Flow state, this allows you to get back into it at a later time
- Use Issues for deep research tasks
- Simon keeps [repos of personal projects and tasks](https://github.com/simonw/public-notes/), some public and some private
- Make a public-notes repo like simonw/public-notes

- If you do something, tell people about what you did!
	- It's so easy to skip this step
	- Taking an extra 30-60 minutes to write about it gives other people a chance to learn, and helps you recall later
	- Simon uses GitHub releases (with dates)
	- Mindset rule: "No project of mine is finished until I've told people about it in some way"
	- Get a blog
		- Nobody blogs anymore, but you should
		- It works for SEO again these days, because nobody is doing it
	- The enemy of personal projects is guilt
		- Avoid side projects with user accounts - that is an unpaid job, not a side project!
		- If we have documented and tested projects, we can live guilt-free about pausing/not committing 'enough' time to them each

- GitHub Projects v2 is something to look into - brings all of your Issues together

Simon's notes on this talk can be found at: https://github.com/simonw/djangocon-2022-productivity

