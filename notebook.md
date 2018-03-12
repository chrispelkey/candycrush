
## 1. Candy Crush Saga
<p><a href="https://king.com/game/candycrush">Candy Crush Saga</a> is a hit mobile game developed by King (part of Activision|Blizzard) that is played by millions of people all around the world. The game is structured as a series of levels where players need to match similar candy together to (hopefully) clear the level and keep progressing on the level map. If you are one of the few that haven't played Candy Crush, here's a short demo:</p>
<p><a href="https://youtu.be/HGLGxnfs_t8"><img src="https://s3.amazonaws.com/assets.datacamp.com/production/project_139/img/candy_crush_video.jpeg" alt=""></a></p>
<p>Candy Crush has more than 3000 levels, and new ones are added every week. That is a lot of levels! And with that many levels, it's important to get <em>level difficulty</em> just right. Too easy and the game gets boring, too hard and players become frustrated and quit playing.</p>
<p>In this project, we will see how we can use data collected from players to estimate level difficulty. Let's start by loading in the packages we're going to need.</p>


```R
# This sets the size of plots to a good default.
options(repr.plot.width = 5, repr.plot.height = 4)

# Loading in packages
library(tidyverse)
library(scales)
```

## 2. The data set
<p>The dataset we will use contains one week of data from a sample of players who played Candy Crush back in 2014. The data is also from a single <em>episode</em>, that is, a set of 15 levels. It has the following columns:</p>
<ul>
<li><strong>player_id</strong>: a unique player id</li>
<li><strong>dt</strong>: the date</li>
<li><strong>level</strong>: the level number within the episode, from 1 to 15.</li>
<li><strong>num_attempts</strong>: number of level attempts for the player on that level and date.</li>
<li><strong>num_success</strong>: number of level attempts that resulted in a success/win for the player on that level and date.</li>
</ul>
<p>The granularity of the dataset is player, date, and level. That is, there is a row for every player, day, and level recording the total number of attempts and how many of those resulted in a win.</p>
<p>Now, let's load in the dataset and take a look at the first couple of rows. </p>


```R
# Reading in the data
data <- read_csv("candy_crush.csv")

# Printing out the first couple of rows
head(data)
```

    Parsed with column specification:
    cols(
      player_id = col_character(),
      dt = col_date(format = ""),
      level = col_integer(),
      num_attempts = col_integer(),
      num_success = col_integer()
    )



<table>
<thead><tr><th scope=col>player_id</th><th scope=col>dt</th><th scope=col>level</th><th scope=col>num_attempts</th><th scope=col>num_success</th></tr></thead>
<tbody>
	<tr><td>6dd5af4c7228fa353d505767143f5815</td><td>2014-01-04                      </td><td> 4                              </td><td>3                               </td><td>1                               </td></tr>
	<tr><td>c7ec97c39349ab7e4d39b4f74062ec13</td><td>2014-01-01                      </td><td> 8                              </td><td>4                               </td><td>1                               </td></tr>
	<tr><td>c7ec97c39349ab7e4d39b4f74062ec13</td><td>2014-01-05                      </td><td>12                              </td><td>6                               </td><td>0                               </td></tr>
	<tr><td>a32c5e9700ed356dc8dd5bb3230c5227</td><td>2014-01-03                      </td><td>11                              </td><td>1                               </td><td>1                               </td></tr>
	<tr><td>a32c5e9700ed356dc8dd5bb3230c5227</td><td>2014-01-07                      </td><td>15                              </td><td>6                               </td><td>0                               </td></tr>
	<tr><td>b94d403ac4edf639442f93eeffdc7d92</td><td>2014-01-01                      </td><td> 8                              </td><td>8                               </td><td>1                               </td></tr>
</tbody>
</table>




```R
library(tidyverse)

run_tests({
    test_that("data is read in correctly", {
        correct_data <- read_csv("datasets/candy_crush.csv")
        expect_equal(correct_data, data, 
            info = "data should countain datasets/candy_crush.csv read in using read_csv")
        })
})
```




    <ProjectReporter>
      Inherits from: <ListReporter>
      Public:
        .context: NULL
        .end_context: function (context) 
        .start_context: function (context) 
        add_result: function (context, test, result) 
        all_tests: environment
        cat_line: function (...) 
        cat_tight: function (...) 
        clone: function (deep = FALSE) 
        current_expectations: environment
        current_file: some name
        current_start_time: 21.648 0.24 3776.638 0 0
        dump_test: function (test) 
        end_context: function (context) 
        end_reporter: function () 
        end_test: function (context, test) 
        get_results: function () 
        initialize: function (...) 
        is_full: function () 
        out: 3
        results: environment
        rule: function (...) 
        start_context: function (context) 
        start_file: function (name) 
        start_reporter: function () 
        start_test: function (context, test) 


## 3. Checking the data set
<p>Now that we have loaded the dataset let's count how many players we have in the sample and how many days worth of data we have.</p>


```R
print("Number of players:")
print(length(unique(data$player_id)))

print("Period for which we have data:")
print(max(data$dt)-min(data$dt))
```

    [1] "Number of players:"
    [1] 6814
    [1] "Period for which we have data:"
    Time difference of 6 days



```R
run_tests({
    test_that("nothing", {
        expect_true(TRUE, info = "")
    })
})
```




    <ProjectReporter>
      Inherits from: <ListReporter>
      Public:
        .context: NULL
        .end_context: function (context) 
        .start_context: function (context) 
        add_result: function (context, test, result) 
        all_tests: environment
        cat_line: function (...) 
        cat_tight: function (...) 
        clone: function (deep = FALSE) 
        current_expectations: environment
        current_file: some name
        current_start_time: 21.728 0.244 3776.72 0 0
        dump_test: function (test) 
        end_context: function (context) 
        end_reporter: function () 
        end_test: function (context, test) 
        get_results: function () 
        initialize: function (...) 
        is_full: function () 
        out: 3
        results: environment
        rule: function (...) 
        start_context: function (context) 
        start_file: function (name) 
        start_reporter: function () 
        start_test: function (context, test) 


## 4. Computing level difficulty
<p>Within each Candy Crush episode, there is a mix of easier and tougher levels. Luck and individual skill make the number of attempts required to pass a level different from player to player. The assumption is that difficult levels require more attempts on average than easier ones. That is, <em>the harder</em> a level is, <em>the lower</em> the probability to pass that level in a single attempt is.</p>
<p>A simple approach to model this probability is as a <a href="https://en.wikipedia.org/wiki/Bernoulli_process">Bernoulli process</a>; as a binary outcome (you either win or lose) characterized by a single parameter <em>p<sub>win</sub></em>: the probability of winning the level in a single attempt. This probability can be estimated for each level as:</p>
<p><img src="https://s3.amazonaws.com/assets.datacamp.com/production/project_139/img/latex1.png" style="width:150px"></p>
<!-- $$p_{win} = \frac{\sum wins}{\sum attempts}$$ -->
<p>For example, let's say a level has been played 10 times and 2 of those attempts ended up in a victory. Then the probability of winning in a single attempt would be <em>p<sub>win</sub></em> = 2 / 10 = 20%.</p>
<p>Now, let's compute the difficulty <em>p<sub>win</sub></em> separately for each of the 15 levels.</p>


```R
# Calculating level difficulty
difficulty <- data %>%
    group_by(level) %>%
    summarise(attempts = sum(num_attempts), wins = sum(num_success)) %>%    
    mutate(p_win = wins/attempts)
    
# Printing out the level difficulty
print(difficulty)
```

    # A tibble: 15 x 4
       level attempts  wins  p_win
       <int>    <int> <int>  <dbl>
     1     1     1322   818 0.619 
     2     2     1285   666 0.518 
     3     3     1546   662 0.428 
     4     4     1893   705 0.372 
     5     5     6937   634 0.0914
     6     6     1591   668 0.420 
     7     7     4526   614 0.136 
     8     8    15816   641 0.0405
     9     9     8241   670 0.0813
    10    10     3282   617 0.188 
    11    11     5575   603 0.108 
    12    12     6868   659 0.0960
    13    13     1327   686 0.517 
    14    14     2772   777 0.280 
    15    15    30374  1157 0.0381



```R
run_tests({
    test_that("p_win is calculated correctly", {
        correct_difficulty <- data %>%
            group_by(level) %>%
            summarise(attempts = sum(num_attempts), wins = sum(num_success)) %>%
            mutate(p_win = wins / attempts)
        expect_equal(correct_difficulty$p_win, difficulty$p_win, 
            info = "difficulty$p_win should be estimated probability to pass each level in a single attempt")
        })
})
```




    <ProjectReporter>
      Inherits from: <ListReporter>
      Public:
        .context: NULL
        .end_context: function (context) 
        .start_context: function (context) 
        add_result: function (context, test, result) 
        all_tests: environment
        cat_line: function (...) 
        cat_tight: function (...) 
        clone: function (deep = FALSE) 
        current_expectations: environment
        current_file: some name
        current_start_time: 21.856 0.244 3776.85 0 0
        dump_test: function (test) 
        end_context: function (context) 
        end_reporter: function () 
        end_test: function (context, test) 
        get_results: function () 
        initialize: function (...) 
        is_full: function () 
        out: 3
        results: environment
        rule: function (...) 
        start_context: function (context) 
        start_file: function (name) 
        start_reporter: function () 
        start_test: function (context, test) 


## 5. Plotting difficulty profile
<p><img src="https://s3.amazonaws.com/assets.datacamp.com/production/project_139/img/tiffi.jpeg" style="height:150px; float:left"> </p>
<p>Great! We now have the difficulty for all the 15 levels in the episode. Keep in mind that, as we measure difficulty as the probability to pass a level in a single attempt, a <em>lower</em> value (a smaller probability of winning the level) implies a <em>higher</em> level difficulty.</p>
<p>Now that we have the difficulty of the episode we should plot it. Let's plot a line graph with the levels on the X-axis and the difficulty (<em>p<sub>win</sub></em>) on the Y-axis. We call this plot the <em>difficulty profile</em> of the episode.</p>


```R
# Plotting the level difficulty profile
plot.new() 
ggplot(data=difficulty, aes(x=level, y=p_win, group=1)) +
    geom_line(color="red")+
    geom_point()+
    scale_x_continuous(breaks=c(1,2,3,4,5,6,7,8,9,10,11,12,13,14,15))+
    scale_y_continuous(labels=percent)+
    labs(x = "Level", y = "Percentage of Wins", caption = "Level Difficulty")
```




![png](output_13_1.png)



![png](output_13_2.png)



```R
run_tests({
    test_that("the student plotted a ggplot", {
    expect_true('ggplot' %in% class(last_plot()), 
        info = "You should plot difficulty using ggplot.")
    })
})
```




    <ProjectReporter>
      Inherits from: <ListReporter>
      Public:
        .context: NULL
        .end_context: function (context) 
        .start_context: function (context) 
        add_result: function (context, test, result) 
        all_tests: environment
        cat_line: function (...) 
        cat_tight: function (...) 
        clone: function (deep = FALSE) 
        current_expectations: environment
        current_file: some name
        current_start_time: 22.148 0.244 3777.148 0 0
        dump_test: function (test) 
        end_context: function (context) 
        end_reporter: function () 
        end_test: function (context, test) 
        get_results: function () 
        initialize: function (...) 
        is_full: function () 
        out: 3
        results: environment
        rule: function (...) 
        start_context: function (context) 
        start_file: function (name) 
        start_reporter: function () 
        start_test: function (context, test) 


## 6. Spotting hard levels
<p>What constitutes a <em>hard</em> level is subjective. However, to keep things simple, we could define a threshold of difficulty, say 10%, and label levels with <em>p<sub>win</sub></em> &lt; 10% as <em>hard</em>. It's relatively easy to spot these hard levels on the plot, but we can make the plot more friendly by explicitly highlighting the hard levels.</p>


```R
# Adding points and a dashed line
plot.new() 
ggplot(data=difficulty, aes(x=level, y=p_win, group=1)) +
    geom_line(color="red")+
    geom_point()+
    scale_x_continuous(breaks=c(1,2,3,4,5,6,7,8,9,10,11,12,13,14,15))+
    scale_y_continuous(labels=percent)+
    labs(x = "Level", y = "Percentage of Wins", caption = "Level Difficulty")+ 
    geom_hline(yintercept=.10, linetype="dashed")
```




![png](output_16_1.png)



![png](output_16_2.png)



```R
run_tests({
    plot_layers <- sapply(last_plot()$layers, function(layer)  class(layer$geom)[1])
    test_that("the student has plotted lines, points and a hline", {
    expect_true(all(c('GeomLine', 'GeomPoint', 'GeomHline') %in%  plot_layers), 
        info = "The plot should include lines between the datapoints, points at the datapoints and a horisontal line.")
    })
})
```




    <ProjectReporter>
      Inherits from: <ListReporter>
      Public:
        .context: NULL
        .end_context: function (context) 
        .start_context: function (context) 
        add_result: function (context, test, result) 
        all_tests: environment
        cat_line: function (...) 
        cat_tight: function (...) 
        clone: function (deep = FALSE) 
        current_expectations: environment
        current_file: some name
        current_start_time: 22.444 0.244 3777.439 0 0
        dump_test: function (test) 
        end_context: function (context) 
        end_reporter: function () 
        end_test: function (context, test) 
        get_results: function () 
        initialize: function (...) 
        is_full: function () 
        out: 3
        results: environment
        rule: function (...) 
        start_context: function (context) 
        start_file: function (name) 
        start_reporter: function () 
        start_test: function (context, test) 


## 7. Computing uncertainty
<p><img src="https://s3.amazonaws.com/assets.datacamp.com/production/project_139/img/mr_toffee.jpeg" style="height:350px; float:right"> </p>
<p>As Data Scientists we should always report some measure of the uncertainty of any provided numbers. Maybe tomorrow, another sample will give us slightly different values for the difficulties? Here we will simply use the <a href="https://en.wikipedia.org/wiki/Standard_error"><em>Standard error</em></a> as a measure of uncertainty:</p>
<p><img src="https://s3.amazonaws.com/assets.datacamp.com/production/project_139/img/latex2.png" style="width:115px"></p>
<!-- $$
\sigma_{error} \approx \frac{\sigma_{sample}}{\sqrt{n}}
$$ -->
<p>Here <em>n</em> is the number of datapoints and <em>σ<sub>sample</sub></em> is the sample standard deviation. For a Bernoulli process, the sample standard deviation is: </p>
<p><img src="https://s3.amazonaws.com/assets.datacamp.com/production/project_139/img/latex3.png" style="width:195px"></p>
<!-- $$
\sigma_{sample} = \sqrt{p_{win} (1 - p_{win})} 
$$ -->
<p>Therefore, we can calculate the standard error like this:</p>
<p><img src="https://s3.amazonaws.com/assets.datacamp.com/production/project_139/img/latex4.png" style="width:195px"></p>
<!-- $$
\sigma_{error} \approx \sqrt{\frac{p_{win}(1 - p_{win})}{n}}
$$ -->
<p>We already have all we need in the <code>difficulty</code> data frame! Every level has been played <em>n</em> number of times and we have their difficulty <em>p<sub>win</sub></em>. Now, let's calculate the standard error for each level.</p>


```R
# Computing the standard error of p_win for each level
difficulty <- difficulty %>%
    mutate(error = sqrt(p_win * (1 - p_win) / attempts))

head(difficulty)
```


<table>
<thead><tr><th scope=col>level</th><th scope=col>attempts</th><th scope=col>wins</th><th scope=col>p_win</th><th scope=col>error</th></tr></thead>
<tbody>
	<tr><td>1          </td><td>1322       </td><td>818        </td><td>0.61875946 </td><td>0.013358101</td></tr>
	<tr><td>2          </td><td>1285       </td><td>666        </td><td>0.51828794 </td><td>0.013938876</td></tr>
	<tr><td>3          </td><td>1546       </td><td>662        </td><td>0.42820181 </td><td>0.012584643</td></tr>
	<tr><td>4          </td><td>1893       </td><td>705        </td><td>0.37242472 </td><td>0.011111607</td></tr>
	<tr><td>5          </td><td>6937       </td><td>634        </td><td>0.09139397 </td><td>0.003459878</td></tr>
	<tr><td>6          </td><td>1591       </td><td>668        </td><td>0.41986172 </td><td>0.012373251</td></tr>
</tbody>
</table>




```R
run_tests({
    test_that("error is correct", {
        correct_difficulty <- difficulty %>%
            mutate(error = sqrt(p_win * (1 - p_win) / attempts))
        expect_equal(correct_difficulty$error, difficulty$error,
            info = "difficulty$error should be calculated as sqrt(p_win * (1 - p_win) / attempts)")
    })
})
```




    <ProjectReporter>
      Inherits from: <ListReporter>
      Public:
        .context: NULL
        .end_context: function (context) 
        .start_context: function (context) 
        add_result: function (context, test, result) 
        all_tests: environment
        cat_line: function (...) 
        cat_tight: function (...) 
        clone: function (deep = FALSE) 
        current_expectations: environment
        current_file: some name
        current_start_time: 22.488 0.248 3777.487 0 0
        dump_test: function (test) 
        end_context: function (context) 
        end_reporter: function () 
        end_test: function (context, test) 
        get_results: function () 
        initialize: function (...) 
        is_full: function () 
        out: 3
        results: environment
        rule: function (...) 
        start_context: function (context) 
        start_file: function (name) 
        start_reporter: function () 
        start_test: function (context, test) 


## 8. Showing uncertainty
<p>Now that we have a measure of uncertainty for each levels' difficulty estimate let's use <em>error bars</em> to show this uncertainty in the plot. We will set the length of the error bars to one standard error. The upper limit and the lower limit of each error bar should then be <em>p<sub>win</sub></em> + <em>σ<sub>error</sub></em> and <em>p<sub>win</sub></em> - <em>σ<sub>error</sub></em>, respectively.</p>


```R
# Adding standard error bars
plot.new() 
ggplot(data=difficulty, aes(x=level, y=p_win, group=1)) +
    geom_line(color="red")+
    geom_point()+
    scale_x_continuous(breaks=c(1,2,3,4,5,6,7,8,9,10,11,12,13,14,15))+
    scale_y_continuous(labels=percent)+
    labs(x = "Level", y = "Percentage of Wins", caption = "Level Difficulty")+ 
    geom_hline(yintercept=.10, linetype="dashed")+
    geom_errorbar(aes(ymin=(p_win-error), ymax=(p_win+error)))
```




![png](output_22_1.png)



![png](output_22_2.png)



```R
run_tests({
    plot_layers <- sapply(last_plot()$layers, function(layer)  class(layer$geom)[1])
    test_that("the student has plotted lines, points and a hline", {
    expect_true("GeomErrorbar" %in%  plot_layers, 
        info = "The plot should include error bats using geom_errorbar.")
    })
})
```




    <ProjectReporter>
      Inherits from: <ListReporter>
      Public:
        .context: NULL
        .end_context: function (context) 
        .start_context: function (context) 
        add_result: function (context, test, result) 
        all_tests: environment
        cat_line: function (...) 
        cat_tight: function (...) 
        clone: function (deep = FALSE) 
        current_expectations: environment
        current_file: some name
        current_start_time: 22.8 0.252 3777.806 0 0
        dump_test: function (test) 
        end_context: function (context) 
        end_reporter: function () 
        end_test: function (context, test) 
        get_results: function () 
        initialize: function (...) 
        is_full: function () 
        out: 3
        results: environment
        rule: function (...) 
        start_context: function (context) 
        start_file: function (name) 
        start_reporter: function () 
        start_test: function (context, test) 


## 9. A final metric
<p>It looks like our difficulty estimates are pretty precise! Using this plot, a level designer can quickly spot where the hard levels are and also see if there seems to be too many hard levels in the episode.</p>
<p>One question a level designer might ask is: "How likely is it that a player will complete the episode without losing a single time?" Let's calculate this using the estimated level difficulties!</p>


```R
# The probability of completing the episode without losing a single time
p <- prod(difficulty$p_win)
# Printing it out
p
```


9.44714093448606e-12



```R
run_tests({
    test_that("p is correct", {
        correct_p <- prod(difficulty$p_win)
        expect_equal(correct_p, p,
            info = "p should be calculated as the product of difficulty$p_win .")
    })
})
```




    <ProjectReporter>
      Inherits from: <ListReporter>
      Public:
        .context: NULL
        .end_context: function (context) 
        .start_context: function (context) 
        add_result: function (context, test, result) 
        all_tests: environment
        cat_line: function (...) 
        cat_tight: function (...) 
        clone: function (deep = FALSE) 
        current_expectations: environment
        current_file: some name
        current_start_time: 22.856 0.252 3777.861 0 0
        dump_test: function (test) 
        end_context: function (context) 
        end_reporter: function () 
        end_test: function (context, test) 
        get_results: function () 
        initialize: function (...) 
        is_full: function () 
        out: 3
        results: environment
        rule: function (...) 
        start_context: function (context) 
        start_file: function (name) 
        start_reporter: function () 
        start_test: function (context, test) 


## 10. Should our level designer worry?
<p>Given the probability we just calculated, should our level designer worry about that a lot of players might complete the episode in one attempt?</p>


```R
# Should our level designer worry about that a lot of 
# players will complete the episode in one attempt?
should_the_designer_worry = FALSE
```


```R
run_tests({
    test_that("should_the_designer_worry is FALSE", {
    expect_false(should_the_designer_worry,
        info = "The probability is really small, so I don't think the designer should worry that much...")
    })
})
```




    <ProjectReporter>
      Inherits from: <ListReporter>
      Public:
        .context: NULL
        .end_context: function (context) 
        .start_context: function (context) 
        add_result: function (context, test, result) 
        all_tests: environment
        cat_line: function (...) 
        cat_tight: function (...) 
        clone: function (deep = FALSE) 
        current_expectations: environment
        current_file: some name
        current_start_time: 22.9 0.256 3777.907 0 0
        dump_test: function (test) 
        end_context: function (context) 
        end_reporter: function () 
        end_test: function (context, test) 
        get_results: function () 
        initialize: function (...) 
        is_full: function () 
        out: 3
        results: environment
        rule: function (...) 
        start_context: function (context) 
        start_file: function (name) 
        start_reporter: function () 
        start_test: function (context, test) 

