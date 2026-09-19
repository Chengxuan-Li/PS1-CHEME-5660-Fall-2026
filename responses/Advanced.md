# PS1 Advanced-Track Discussion Questions

Question 1 covers the Standard valuation and repricing work included in `src/Advanced.jl`. Questions 2 and 3 cover the additional funding comparison. Write all three answers in this file.

Edit this file in VS Code. Replace each `TODO: Write your response here.` line with your answer, keeping the surrounding HTML answer markers. Explain your reasoning and include the requested values, using tables where useful. Save this file before rerunning the checker.

Run `check_submission.jl` as described in the README to generate results from your saved code. The financial results appear in the terminal after the test report. Report rates as percentages and valuation prices in USD per USD 100 par. Funding amounts are in USD. The checker also saves `results/advanced-strategies.csv` and `results/advanced-scenario-outcomes.csv` in the extracted PS1 folder. Matching `scenario_id` values identify the same future across strategies.

## 1. Valuation and interest-rate risk

Report the following values from the checker, which uses `data/standard-terms.csv`:

* the six-month bill price and its equivalent continuously compounded annual rate;
* the seven-year note price before and after the 50-basis-point yield increase;
* the modified duration and convexity of the note;
* the new note price estimated using duration and convexity, which is L2b's second-order Taylor approximation shown in the README;
* the price recalculated by the package minus the estimated price.

For the price difference, use the checker's **Package price minus estimated note price** result, which is calculated before rounding. You may round reported values to four decimal places. Explain why the note's price changes while its coupon stays fixed, and why the two pricing methods give slightly different results.

<!-- answer-1:start -->
- The six-month bill price is USD 97.5610 per USD 100 par. and its equivalent continuously compounded annual rate is 4.9385%.
- The seven-year note price before the 50-basis-point yield increase is USD 97.9255 per USD 100 par; after the increase, it is USD 95.0485 per USD 100 par.
- The modified duration of the note is 5.9790 years; its convexity is 41.8177 years squared.
- The new note price estimated using duration and convexity is 95.049171 USD per 100 USD par.
- The price delta (minus) is -0.000645 USD per 100 USD par.

- The note's PMT is fixed per the contract; its price is the PV of these PMT per contract, and the PV is changing as the interest rate is changing. When interest rate increases by 50 basis points, PMT discounts at a higher rate resulting in PV given same PMT.
- The duration and convexity calculation is a second order Taylor approximation which leaves out higher order terms which are positive. Thus approximation will be smaller than the actual computation.
<!-- answer-1:end -->

## 2. Funding with the available budget

This comparison uses separate simulated market prices. The purchase price of `N7` need not equal the seven-year note price in Question 1.

The firm has USD 74,000 and owes USD 100,000 at year 7. Compare `N7`, `N2-N5`, `N5-N2`, and `B1-B1-B1-B1-B1-B1-B1`. For each, report the funded count out of 40, mean final wealth, mean shortfall, and largest shortfall. Identify which has the highest funded count and which has the lowest mean shortfall. Explain why those measures can favor different choices. Use `N2-N5` and `N5-N2` to explain how purchase timing and coupon reinvestment affect outcomes, even though both sequences last seven years.

<!-- answer-2:start -->
| Strategy | Funded futures | Mean final wealth (USD) | Mean shortfall (USD) | Max shortfall (USD) |
|:--|--:|--:|--:|--:|
| `N7` | 0 of 40 | 96,863.85 | 3,136.15 | 4,487.96 |
| `N2-N5` | 7 of 40 | 96,783.34 | 3,541.88 | 7,741.91 |
| `N5-N2` | 2 of 40 | 96,805.78 | 3,207.57 | 7,377.11 |
| `B1-B1-B1-B1-B1-B1-B1` | 9 of 40 | 96,512.04 | 4,385.71 | 12,470.97 |

- The `B1-B1-B1-B1-B1-B1-B1` strategy has the highest funded count.
- The `N7` strategy has the lowest funded count.
- The `N7` strategy has the lowest mean shortfall.

- These measures favor different choices: "funded" only count records that cross the 100K cutoff and does not measure the size of a miss. Mean shortfall measures the average dollar deficit across all 40 futures, with a zero shortfall assigned to funded outcomes. Therefore, `B1-B1-B1-B1-B1-B1-B1` can cross the cutoff most often while still having the largest mean and maximum observed shortfalls. `N7` never crosses the cutoff, but its outcomes remain closer to the required payment on average.

- `N2-N5` and `N5-N2` both cover seven years, but they buy their second note at different dates and face different prices. The current rate is too low for the cutoff, given by choice `N7`; `N5-N2` locks in on current (lower) rates and exposes less to uncertainty, thus would have more stable performance and less chance of reaching cutoff. `N2-N5` locks in less on current rates and exposes more to uncertainty, thus may have more chance to reach the cutoff albeit larger max shortfall.
<!-- answer-2:end -->

## 3. Choose a strategy within the budget

The firm cannot borrow or add money to its USD 74,000 budget. Which of the four strategies should it choose? Support your recommendation with both the funded count and shortfall amounts from Question 2. Explain the risk you would be accepting and the consequence of missing the required payment. Give one limitation of using the 40 simulated futures as evidence about real-world funding reliability.

<!-- answer-3:start -->
I would recommend `B1-B1-B1-B1-B1-B1-B1`:

- It has the highest observed funded count: 9 of 40 futures, compared with 7 for `N2-N5`, 2 for `N5-N2`, and 0 for `N7`. Since the payment must be made and no extra money can be added, I would give the greatest weight to the strategy that reaches 100K most often in the provided sample.
- This choice accepts substantial downside risk. Its mean shortfall is USD 4,385.71 and its largest shortfall is USD 12,470.97, both worse than the other three strategies.
- A limitation is that the 40 simulated futures are only a common comparison sample (probably from estimates or past conditions). They are not probabilities and may not represent the full range or likelihood of future interest-rate and Treasury-price conditions. Therefore, 9 out of 40 may not be interpreted as real funding probability.
<!-- answer-3:end -->
