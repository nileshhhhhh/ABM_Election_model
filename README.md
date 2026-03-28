# Influence Formula Explanation for the Supporter Model

This README describes the influence mechanism used in the supporter-only election model. It explains the main formulas, the role of each parameter, and how support changes are attempted over time.

## Model Idea

A supporter of candidate A or B attempts to influence another agent. The target may change support depending on:

- base persuasion
- similarity bonuses
- undecided openness
- demographic preference
- the target's stubbornness

The model tracks only supporters:

- Supporters A
- Supporters B
- Undecided supporters

## Support States

Each agent has a support state:

- `0` = supports A
- `1` = supports B
- `2` = undecided

## General Influence Probability

For an influencer `i` and a target `j`, the probability of successful influence is:

```text
P(i -> j) = clip(
    persuasion-strength
    + I_same-age × same-age-bonus
    + I_same-gender × same-gender-bonus
    + I_same-edu × same-edu-bonus
    + I_target-undecided × undecided-openness
    + preference term
    - stubbornness_j
)
```

The `clip(x)` function keeps the value between 0 and 1:

- if `x < 0`, use `0`
- if `0 <= x <= 1`, use `x`
- if `x > 1`, use `1`

## Preference Term

The preference term depends on which side the influencer supports.

### If the influencer supports A

```text
preference term = preference-weight × pref-A_j
```

### If the influencer supports B

```text
preference term = preference-weight × (1 - pref-A_j)
```

Here, `pref-A_j` is the target agent's built-in preference for candidate A.

## Indicator Terms

These terms are either 1 or 0 depending on whether the condition is true.

### Same-age indicator

```text
I_same-age = 1  if influencer and target are in the same age group
I_same-age = 0  otherwise
```

### Same-gender indicator

```text
I_same-gender = 1  if influencer and target are in the same gender group
I_same-gender = 0  otherwise
```

### Same-education indicator

```text
I_same-edu = 1  if influencer and target are in the same education group
I_same-edu = 0  otherwise
```

### Undecided-target indicator

```text
I_target-undecided = 1  if the target is undecided
I_target-undecided = 0  otherwise
```

## Explicit Stubbornness Formulas

### Decided target

```text
stubbornness_j = base-stubbornness
```

### Undecided target

```text
stubbornness_j = base-stubbornness × undecided-stubbornness-factor
```

## Important Note About Undecided Targets

For an undecided target, the model gives two advantages to the influencer:

1. it adds `undecided-openness`
2. it reduces the stubbornness term through `undecided-stubbornness-factor`

So undecided agents are easier to convert in two ways.

## Expanded Formulas

### 1) Influencing a decided target

```text
P(i -> j_decided) = clip(
    persuasion-strength
    + I_same-age × same-age-bonus
    + I_same-gender × same-gender-bonus
    + I_same-edu × same-edu-bonus
    + preference term
    - base-stubbornness
)
```

### 2) Influencing an undecided target

```text
P(i -> j_undecided) = clip(
    persuasion-strength
    + I_same-age × same-age-bonus
    + I_same-gender × same-gender-bonus
    + I_same-edu × same-edu-bonus
    + undecided-openness
    + preference term
    - (base-stubbornness × undecided-stubbornness-factor)
)
```

## How Influence Is Attempted

Each simulation day, the following process occurs:

1. Each agent makes `contact-number` influence attempts.
2. For each attempt, a random target is chosen from the other agents.
3. If the influencer is undecided, the attempt stops immediately.
4. If the target already supports the same side, nothing changes.
5. The model calculates `influence-prob` using the formula above.
6. A random number between 0 and 1 is drawn.
7. If the random number is less than `influence-prob`, the target adopts the influencer's support.

## Meaning of the Main Parameters

### Persuasion strength

This is the base persuasive power in every interaction.

- higher value -> stronger influence
- lower value -> weaker influence

### Preference weight

This controls how strongly the target's demographic preference affects persuasion.

- higher value -> the target follows built-in preference more strongly
- lower value -> influence depends more on interaction than preference

### Undecided openness

This is an extra bonus applied only when the target is undecided.

- higher value -> undecided agents convert faster
- lower value -> undecided agents remain undecided longer

### Stubbornness

This is the resistance of the target to changing support.

- higher value -> harder to persuade
- lower value -> easier to persuade

## Contact Number

Contact number is the number of other agents each supporter interacts with in one simulation day to try to influence their support choice.

```text
Number of influence attempts per agent per day = contact-number
```

A higher contact number gives more chances for persuasion and can make support spread faster.

## Summary

The model combines:

- social influence through persuasion
- similarity effects through age, gender, and education bonuses
- preference effects through `pref-A`
- lower resistance for undecided agents
- stochastic switching through a random probability check

In short, support changes when the total positive influence is strong enough to overcome the target's stubbornness.
