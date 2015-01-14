## Frequentist A/B testing

Your web page is currently getting a 10% subscription sign up rate with a total of 1500 views (views + sign ups). Your designers put together 4 new page options (branches) with the goal of increasing it by 2%. 

[Visual Explanation of Everything that is happening](http://rpsychologist.com/d3/NHST/)

1. Calculate how many total visitors (views + sign ups) do you need to see on each branch to be convinced that one of them is delivering the goal. Assume that you want a significance of 95% and a power of 80%.
    * Python has a statistics module called [statsmodels](http://statsmodels.sourceforge.net/) with convenient functions to calculate missing parameters for power calculation.
    * You will first need to [calculate](http://statsmodels.sourceforge.net/devel/generated/statsmodels.stats.proportion.proportion_effectsize.html#statsmodels.stats.proportion.proportion_effectsize) your effect size
    * You can then use this to [perform](http://statsmodels.sourceforge.net/devel/generated/statsmodels.stats.power.NormalIndPower.html#statsmodels.stats.power.NormalIndPower) a power analysis.
 
2. Play with values of significance and power to see how the number of observations needed changes.

3. Make a plot of how the number of observations scales with significance level (holding power constant) and with power (holding significance constant)

4. Discuss the real world impact of these effects in terms of revenue, time, etc. and why you might want to sacrifice some significance and/or power.

    10 days later you see that the branches reached these numbers:

    <table>
    <tr> 
        <th>branch </th> <th> clicks  </th> <th>views</th>


    <tr><td>Baseline </td><td>150</td><td>1500</td></tr>
    <tr><td>Branch A </td><td>187</td><td>1259</td></tr>
    <tr><td>Branch B </td><td>25</td><td>750 </td></tr>
    <tr><td>Branch C </td><td>36</td><td>431 </td></tr>
    <tr><td>Branch D </td><td>47</td><td>631 </td></tr>
    </table>

    Your boss is complaining to you that the test is taking too long. He wants to know which branches are currently performing better, and to predict how long it will take for branches A B C and D to reach the needed number of visitors.

5. Given that each sign up is earning your web site $5, should you stop the test? 

6. Discuss the pros and cons for stopping it now. Take into account the facts that you know, don't know and the ones you can calculate.
