---
title: "On hiring a consultant"
categories:
  - management
tags:
  - work
  - consulting
---
# Hiring a consultant

## An open letter to managers

So, you finally got the budget to hire an IT consultant. Great, it’s time to get in touch with one of these pesky recruitment agencies and get inundated by CVs.
It’s going to be a long process: first you have to find the right profile, then interview one or more candidates and figure out the deal with the agency. Eventually the consultant will show up at your door on the initial date of the contract.
Before you even initiate this long and winding process, please take a step back and breath deeply. 
Let’s talk about consultants. The breed of consultants I’m referring to here are the IT ones: software developers, architects, analysts, technical project managers. These are not the man-in-black types, the “management consultants”. The consultants I’m familiar with (because I have been one of them for over 20 years), typically are involved for a variable length of time on a given project. They work on a project from the beginning or they are called in mid-flight (likely when the project is late and the manager thinks that bringing more people in will stop the death march).
Before you pull the trigger on the new hire, it is paramount to understand that there are *good consultants* and *bad consultants* (and also *VERY BAD consultants*, which are not so common but they do exists). Some cases I have witnessed?
- Downloading porn from the office - check
- Working for another client during the office time - check
- Reeking of alcohol - check
- Stealing stuff/stealing code - check

These may be extreme cases of bad consultants, but, sadly, there are a lot of cowboys among us. So, your first duty as a manager, is to *check the references*. I know this is not a perfect way to assess the integrity of a consultant (or any employee for that matter) but it is still better than hiring someone from deep space. Trust me on this.

## The inteview
Ok, you got good references from the previous employers. It’s time to schedule an interview. Interviewing candidates for technical positions it’s a bitch. Everyone has different ideas, approaches, methodologies and then some. It’s crazy out there. So, think for a second. What is this guy going to do for the next  6 months? Write Javascript code? Write technical documents? Design a new mission critical system based on some new technology? Requirements gathering from a particularly difficult client? Whatever is that you have in mind for the consultant (because you do have a mission in mind, YES?), orchestrate an interview that is actually designed to sample his knowledge. But, please, take some time to assess a candidate. I have been requested to run 20 minutes-long technical interviews with potential candidates. I had to rush through a list of questions at light-speed, barely appreciating the answer from the other side because the manager was whispering on the conf-call Darth Vader style “Watch the time!”.
How much time should you schedule for an interview? There is no definitive answer to that question, but *certainly not 20 minutes*. 1 to 2 hours is a good ball park estimate for an *initial screening* interview. Because, yeah, the 20 minutes interview I discussed, was the one and only interview the manager wanted to use in order to decide if the 800 Euro/day consultant was good enough for the project. The more critical is the job, the lengthier and accurate the interview should be.
If you are hiring for a dev position, assess the candidate through code. There are a million ways of doing this nowadays. The simplest one? Send the candidate some specs and ask him to write the code, offline, and ship the solution back in a week. Ask your right-arm technical guy to check the code and evaluate the quality of the candidate's submission.
Finally, meet the guy in person; invite him to lunch with the team; assess his personal and communication skills. Ask the opinion of his future colleagues. Even for shorter contracts (e.g. 6 months) this is important, because in 6 months a lot of damage can be made. I have seen critical pieces of infrastructure written by a nasty consultant that was so bad that nobody dared getting near the code base, because it was so fragile and stinky. It was running all right but the technical debt was so large that it slowed down the organisation for *YEARS*.

## Communication and mentoring
An often overlooked aspect of hiring technical profiles is their *communication and mentoring skills*. It’s common to hire a consultant when there is no in-house knowledge of a certain technology and there is no time to train the permanent employees (or no willingness from their side to learn, as it astonishingly happens). If this is the case, how good is the consultant in spreading the knowledge? Can he explain technically complex subjects in a clear and simple way? Does he actually have the disposition to do so?
If you are looking for a consultant who should also mentor your team, ask to clarify certain complex topics of his core knowledge during one of the interview sessions. At the same time, do not forget to inform him that part of his duties will also include the mentoring of the team.
Bonus points: the guys has a blog, speaks at tech conferences, wrote a book.

## On-boarding
Onboarding is possibly the most critical step of the hiring process (and that of course applies to permanent resources as well). I have left projects immediately after the on-boarding stage because of the bad taste left in my mouth after a week or so on the project. A botched on-boarding of a technical resource will determine how well he will perform for the rest of the project. Not only that: a sloppy on-boarding will also affects the way the new hire will perceive the project, the team-members and, you, the project manager. It’s like *imprinting* for ducks and geese.
What are the steps for a successful on-boarding?
Let’s start from the basics.
- Make sure the new hire can *actually enter the building*, if your company has access cards and the like.
- *Be on time*, don’t let the consultant wait for you or whoever is supposed to welcome him on his first day.
- Spend time introducing the new hire to the team and, possibly, to the extended workspace. Make him feel welcome, make him feel a part of the team. I can’t stress this point hard enough! *This is key*.
- Show him around: toilets, coffee space, canteen. And when you do that, don’t actually look annoyed. Be enthusiastic, do show appreciation for your company.
- Get him a proper working space: yeah, I’m talking about an actual desk. If your company has a no-fixed desk policy, make sure that the consultant has always a place to sit or can work from home from time to time.
- If the consultant is supposed to write code, get him a computer that he is not going to be fucking ashamed to use. This is another *key point*. I have personally been given piece of shit hardware that was barely booting up (20 minutes boot time anyone?), full of company crapware, such as 80% CPU antivirus, etc. etc. It is like hiring a gardener for your house and asking to show his hands, so that you can chop them off with a single katana strike. If your company wants developers and architects but it is not able to provide decent hardware, you should run away screaming from this company. No excuses, no bullshit. Hardware is cheap nowadays.

Now that we cleared the absolute basics, let’s move into a more project-specific list of on-boarding steps.
- Introduce the new consultant to the project he will work on. Start with an *initial meeting* where the scope, deadlines and deliverables of the project are clearly laid out. The consultant ought to step out of the meeting with a clear picture of what he is going to do for the next months. Do not improvise: a decent Power Point presentation with the key points can facilitate this process.
- A complex domain requires an additional tutoring effort from the company side
- Make sure the consultant is productive from day one. All the bureaucratic bullshit should be sorted out, including the time-sheet inferno. If your company uses a byzantine time-tracking system, take time to explain it to the consultant and apologise if he has to spend more time tracking time than producing actual work.
- If the consultant is a developer, make sure that it doesn’t take 3 weeks to setup a productive coding environment. Information should be readily available in a shared wiki, including password to servers and whatever other protected resource. Automation is key, so automate as much as possible.
- Take time to introduce the consultant to the project organisation: who is who, stakeholders, methodology, git workflow, testing strategy, definition of “done”, continuous integration, coding style, deployment strategy, whatever your company does to deliver stellar software.

There are different ways to deliver this crucial information. You can prepare a bunch of documents and land it on the new hire desk and expect him to read it (hint: this is a waste of time). Or you can arrange for the key members of the team (such as the lead dev, the architect, the analysts, etc.) to prepare short and interesting presentations. This is also a quick way for the team to get to know the new consultant and a chance for him to ask questions and deepen his knowledge.

# Mission

Assuming that the on-boarding took place in a satisfactory manner and that the consultant is not frantically calling his recruiter to get him another gig (witnessed it, along with consultants who disappeared after a couple of days leaving everyone wonder what did they do wrong), it is now time to deploy him in the trenches and let him do his job.
Right? RIGHT? But wait! We are still gathering requirements and the stakeholder are at each other throat and deadline are unrealistic and the vendor did not deliver in time and ... the consultant is now sitting in his corner, reading outdated documentation,  browsing Hacker News and waiting for the day to pass. What did just happen?

This is a situation I have experienced first-hand in some instances and a recurring event for many buddy contractors of mine.
Grounding consultants is not only a stupid waste of company money (for which almost no one takes responsibility) but also a guaranteed way to destroy the team's morale and any professional respect towards the management. Therefore:

- When you hire a consultant and the guy is paid from your company's bank account (as opposed to your bank account), make sure that there is enough work in the pipeline to keep him busy with useful, project-related work. Up and downs are obviously expected, some days can be slower than other. What I'm pointing my finger at it is a total lack of work streams, an incapacity to provide the team with a roadmap and a vision of short, medium and long term objectives.
- Do not feed your consultant with bogus work, just to keep him busy (personal anecdote: once I was hired to work as software architect on a number of projects in a very large corporatiom and, due to terrible planning, I was asked to lead the Internet 11 to Chrome desktop migration project until the original projects were resumed)
- Similarly try to avoid hiring a Go senior developer and end up asking him to redisign your corporate portal. This is likely not going to fly
- Provide feedback. Consultants are human too and we thrive in an environment where we feel appreciated. At the same time, we can take criticism and change route mid-flight. When a consultant lands in a new company (sometime in a different country) he has no clue of what the company expects from him. We rely on our past experiences and (hopefully) professionalism. This may not be enough: so don't be afraid to schedule recurring one-to-one meetings with your external consultants.


Notes:

- do not use agencies, hire out of reccomendations