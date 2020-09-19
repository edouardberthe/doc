# Best Practices for Peer Code Review

Original article from Cisco : [BestPracticesForPeerCodeReview.pdf](https://www.kessler.de/prd/smartbear/BestPracticesForPeerCodeReview.pdf)

### Summary of the article:

1. "Review fewer than 200-400 lines of code at a time within 60-90min, 70-90% defect"

--> **Don't review too much at the same time**

2. "Aim for an inspection rate of less than 300-500 LOC/hour** (6-8 LOC/mn!)"

--> **Don't review too quickly**

3. "Take enough time for a proper, slow review, but not more than 60-90 minutes"

--> **Don't spend too much time**

4. Authors should annotate source code before the review begins

--> **Describe your dev**

- Authors might be able to eliminate most defects themselves. They should "prepare" the CR by annotating code. These comments are not *code comments* but *comments for reviewers*.
- By doing so, they find themselves potential bugs in their devs.
- Potential explanation of why fewer bugs: where there are comments, the reviewers is "biased" and see less bugs (because his spirit has been anchored to the developer interpretation). But after investigation, this is not the case.

5. Establish quantifiable goals for code review and capture metrics so you can improve your processes

--> **Measure the impact of the CR**

6. Checklists substantially improve results for both authors and reviewers

7. Verify that defects are actually fixed!

8. Managers must foster a good code review culture in which finding defects is viewed positively

- Easy to see defects negatively (mistakes in the code), but it should be seen as a positive mean for *learning, growing and communication*.
- It's not an accusation way of working: the *author* mades the bugs and the *reviewer* saw it, but more as en efficient pair-programming session.
- To maintain a consistent message that finding bugs is good, management must promise that defect densities will never be used in performance reports

9. Beware the “Big Brother” effect

10. The Ego Effect: Do at least some code review, even if you don’t have time to review it all

The Ego Effect: knowing that your code will be reviewed makes you more conscientious before pushing your work. This effect is also the case even if the review is not compulsory: reviewing 20-30% of the code would probably be optimal to assure the Ego Effect with minimal time expenditure.

11. Lightweight-style code reviews are efficient, practical, and effective at finding bugs

    --> GitHub is pretty nice!