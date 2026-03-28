# ABM_Election_model
Influence Formula Explanation for the Supporter Model
Updated to show the explicit undecided-stubbornness case
Model idea. A supporter of candidate A or B attempts to influence another agent. The target may change support depending on base persuasion, similarity bonuses, undecided openness, demographic preference, and the target's stubbornness.
General influence probability
For an influencer i and a target j, the probability of successful influence is:
P(i -> j) = clip(persuasion-strength + I_same-age x same-age-bonus + I_same-gender x same-gender-bonus + I_same-edu x same-edu-bonus + I_target-undecided x undecided-openness + preference term - stubbornness_j )
clip(x) keeps the value between 0 and 1: if x < 0, use 0; if x > 1, use 1.
Preference term
If the influencer supports A: preference term = preference-weight x pref-A_j
If the influencer supports B: preference term = preference-weight x (1 - pref-A_j)
Indicator terms
•	I_same-age = 1 when the influencer and target are in the same age group; otherwise 0.
•	I_same-gender = 1 when they have the same gender group; otherwise 0.
•	I_same-edu = 1 when they have the same education group; otherwise 0.
•	I_target-undecided = 1 when the target is undecided; otherwise 0.
Explicit stubbornness formulas
Decided target: stubbornness_j = base-stubbornness
Undecided target: stubbornness_j = base-stubbornness x undecided-stubbornness-factor
Important note. For an undecided target, the model gives two advantages to the influencer: (1) it adds undecided-openness, and (2) it reduces the stubbornness term through the undecided-stubbornness-factor. So undecided agents are easier to convert in two ways.

Expanded formulas
1) Influencing a decided target
P(i -> j_decided) = clip(persuasion-strength + I_same-age x same-age-bonus + I_same-gender x same-gender-bonus + I_same-edu x same-edu-bonus + preference term - base-stubbornness )
2) Influencing an undecided target
P(i -> j_undecided) = clip(persuasion-strength + I_same-age x same-age-bonus + I_same-gender x same-gender-bonus + I_same-edu x same-edu-bonus + undecided-openness + preference term - (base-stubbornness x undecided-stubbornness-factor) )
How influence is attempted
1.	Each agent tries contact-number influence attempts per day.
2.	A random target is chosen from the other agents.
3.	If the influencer is undecided, the attempt stops immediately.
4.	If the target already supports the same side, nothing changes.
5.	The model calculates influence-prob using the formula above.
6.	A random number between 0 and 1 is drawn.
7.	If the random number is less than influence-prob, the target adopts the influencer's support.
