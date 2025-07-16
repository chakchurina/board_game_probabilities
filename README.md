# Prize Probabilities on a Circular Board

## Problem 

There is a circular board game with 14 spaces, each containing a prize. The player starts on the Start (space 14) and rolls a standard six-sided die $X$ times. After all rolls, the total sum of the dice is $N$. The player then moves clockwise $N$ spaces from the Start position and receives the prize on the space where they land. Each prize has a different value.

<img src="attachment:game_field.png" width="300">

Calculate the probability of landing on each prize for different numbers of rolls:
- when the player rolls 3 dice ($X = 3$)
- when the player rolls 6 dice ($X = 6$)
- when the player rolls 15 dice ($X = 15$)

## Solution

Let the player roll $X$ six-sided dice. The total sum of the dice is $N$, and the player moves $N$ steps clockwise on the circular board. I want to find the probability $f_X(y)$ of landing on cell $y \in \{1, \dots, 14\}$.

When rolling $X$ dice, the total sum $N$ can range from $X$ to $6X$. The minimum occurs if all dice show 1, and the maximum if all dice show 6. For example, if $X = 3$, then $N \in [3, 18]$. Each sum $N$ occurs with probability $P(N)$.

Since the board is circular, the player’s final position after moving $N$ steps is

$$(13 + N) \bmod 14 + 1$$

So 
- if $N = 10$, the player lands on cell 10, 
- if $N = 16$, the player lands on cell 2.

The probability of rolling a total sum $N$ with $X$ dice is

$$P(N) = \frac{\text{number of ways to roll sum }N}{6^X}$$

Here $6^X$ is the total number of possible dice combinations.

To compute the probability of landing on a specific cell $y$, we need to sum over all possible $N$ such that

$$(13 + N) \bmod 14 + 1 = y$$

Thus, we need to sum over all values $N$ from $X$ to $6X$, but only those for which the position $(13 + N) \bmod 14 + 1 = y$

Let’s say we want to land on position 5, i.e., $f_X(5)$. Then we need such $N$ that $(13 + N) \bmod 14 + 1 = 5$
- for $X=3$, $N \in [3, 18]$ the only valid value of $N$ is 5.
- for $X=4$, $N \in [4, 24]$, the suitable values of $N$ are 5 and 19.

To find the final probability of landing on cell $y$, we have to sum the probabilities of all suitable $N$:
$$f_X(y) = \sum_{\substack{N = X \\ (13 + N) \bmod 14 + 1 = y}}^{6X} \frac{\text{number of ways to roll sum } N}{6^X}$$

That is, we iterate over all possible sums $N$ and sum those probabilities $P(N)$ for which the condition of landing on cell $y$ is satisfied.

For $X=3$ and $y=5$ we can calculate it manually:
- $N \in [3, 18]$ and only $N=5$ is suitable
- There are $6^X = 216$ possible combinations, 
- There are 6 ways to get a sum of 5 in three rolls: (1, 1, 3) and (1, 2, 2) in different permutations.

According to the formula above:

$$f_3(5) = \frac{6}{216} = \frac{1}{36} \approx 0.28$$

For larger $X$, enumerating all combinations by hand is impractical ($6^{15}$ combinations for $X=15$). To handle this, I used dynamic programming to compute the number of ways to obtain each sum $N$.

| Space | P(X=3) | P(X=6) | P(X=15) |
|-------|--------|--------|---------|
| 1     | 0.046  | 0.052  | 0.071   |
| 2     | 0.028  | 0.058  | 0.070   |
| 3     | 0.019  | 0.067  | 0.070   |
| 4     | 0.019  | 0.076  | 0.070   |
| 5     | 0.028  | 0.085  | 0.070   |
| 6     | 0.046  | 0.091  | 0.071   |
| 7     | 0.069  | 0.093  | 0.071   |
| 8     | 0.097  | 0.091  | 0.072   |
| 9     | 0.116  | 0.085  | 0.072   |
| 10    | 0.125  | 0.076  | 0.073   |
| 11    | 0.125  | 0.067  | 0.073   |
| 12    | 0.116  | 0.058  | 0.072   |
| 13    | 0.097  | 0.052  | 0.072   |
| 14    | 0.069  | 0.050  | 0.071   |

I also ran a Monte Carlo simulation to verify the analytical results. The simulated probabilities converged to the same values as the calculated ones.

## Insight for a player

Let’s look at how the distribution changes as the number of dice rolls $X$ increases.

- For $X=1$, the distribution is uniform: each outcome from 1 to 6 has probability $1/6$, and all other cells are unreachable.
- For $X=2$, the expected value is $E[N]= 2 \times 3.5 = 7$, so the distribution peaks near this value.
- For $X=3$, the expected value shifts to $E[N] = 3 \times 3.5 = 10.5$, and the distribution continues to concentrate around the mean.
- But as $X$ grows, the player repeatedly walks the 14 circular cells, so the probabilities across the cells gradually even out and tend toward a uniform distribution for large $X$.

<img src="attachment:distributions.png" width="300">

So, in practice, if we are aiming for a specific cell, the optimal strategy is to pick $X$ such that the expected value $E[N]=3.5X$ lands close to that cell. For example:

- If the target cell is between 1 and 6, $X=1$ is best.
- If the target is near cell 7, $X=2$ maximizes the probability.
- For $X > 4$, targeting specific cells becomes inefficient because the distribution flattens and the probabilities converge.