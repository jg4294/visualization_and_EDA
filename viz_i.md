Visualization
================
JingYao Geng
2020-10-09

``` r
library(tidyverse)
```

    ## -- Attaching packages ----------------------------------- tidyverse 1.3.0 --

    ## v ggplot2 3.3.2     v purrr   0.3.4
    ## v tibble  3.0.3     v dplyr   1.0.2
    ## v tidyr   1.1.2     v stringr 1.4.0
    ## v readr   1.3.1     v forcats 0.5.0

    ## -- Conflicts -------------------------------------- tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(ggridges)
```

## Load the weather data

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(
    c("USW00094728", "USC00519397", "USS0023B17S"),
    var = c("PRCP", "TMIN", "TMAX"), 
    date_min = "2017-01-01",
    date_max = "2017-12-31") %>%
  mutate(
    name = recode(
      id, 
      USW00094728 = "CentralPark_NY", 
      USC00519397 = "Waikiki_HA",
      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) %>%
  select(name, id, everything())
```

    ## Registered S3 method overwritten by 'hoardr':
    ##   method           from
    ##   print.cache_info httr

    ## using cached file: C:\Users\yaoya\AppData\Local\Cache/R/noaa_ghcnd/USW00094728.dly

    ## date created (size, mb): 2020-10-08 22:33:57 (7.54)

    ## file min/max dates: 1869-01-01 / 2020-10-31

    ## using cached file: C:\Users\yaoya\AppData\Local\Cache/R/noaa_ghcnd/USC00519397.dly

    ## date created (size, mb): 2020-10-08 22:36:03 (0.008)

    ## file min/max dates: Inf / -Inf

    ## using cached file: C:\Users\yaoya\AppData\Local\Cache/R/noaa_ghcnd/USS0023B17S.dly

    ## date created (size, mb): 2020-10-08 22:38:05 (0.008)

    ## file min/max dates: Inf / -Inf

    ## Warning in rnoaa::meteo_pull_monitors(c("USW00094728", "USC00519397", "USS0023B17S"), : The following stations could not be pulled from the GHCN ftp:
    ##  USC00519397, USS0023B17S 
    ## Any other monitors were successfully pulled from GHCN.

``` r
weather_df
```

    ## # A tibble: 365 x 6
    ##    name           id          date        prcp  tmax  tmin
    ##    <chr>          <chr>       <date>     <dbl> <dbl> <dbl>
    ##  1 CentralPark_NY USW00094728 2017-01-01     0   8.9   4.4
    ##  2 CentralPark_NY USW00094728 2017-01-02    53   5     2.8
    ##  3 CentralPark_NY USW00094728 2017-01-03   147   6.1   3.9
    ##  4 CentralPark_NY USW00094728 2017-01-04     0  11.1   1.1
    ##  5 CentralPark_NY USW00094728 2017-01-05     0   1.1  -2.7
    ##  6 CentralPark_NY USW00094728 2017-01-06    13   0.6  -3.8
    ##  7 CentralPark_NY USW00094728 2017-01-07    81  -3.2  -6.6
    ##  8 CentralPark_NY USW00094728 2017-01-08     0  -3.8  -8.8
    ##  9 CentralPark_NY USW00094728 2017-01-09     0  -4.9  -9.9
    ## 10 CentralPark_NY USW00094728 2017-01-10     0   7.8  -6  
    ## # ... with 355 more rows

## Basic scatterplot

Create my first scatterplot ever.

``` r
ggplot(weather_df, aes(x = tmin, y = tmax)) + 
  geom_point()
```

![](viz_i_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

New approach, same plot.

``` r
weather_df %>%
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point()
```

![](viz_i_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

Save and edit a plot object

``` r
plot_weather = 
  weather_df %>%
  ggplot(aes(x = tmin, y = tmax)) 

plot_weather + geom_point()
```

![](viz_i_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

## Adcanced scatterplot

Start with the same one and make it fancy

``` r
weather_df %>%
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_point() +
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

![](viz_i_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

What about the ‘aes’ placement…?

``` r
weather_df %>%
ggplot(aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name)) + #not good
  geom_smooth()
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

![](viz_i_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

Let’s facet some things\!

``` r
weather_df %>%
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_point(alpha = 0.2, size = 0.3) + # 20% transparency of the points, size = 0.3 makes the points size smaller
  geom_smooth(se = FALSE, size = 2) + # here the 'size' is to adjust the line
  facet_grid(. ~ name) # multi-panel plot. In this case, put a '.' means nothing defines the row,and put a tilde and tell it which variable defines the column
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

![](viz_i_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

Let’s combine some elements and try a new plot

``` r
weather_df %>%
ggplot(aes(x = date, y = tmax, color = name)) + 
  geom_point(aes(size = prcp), alpha = .5) +
  geom_smooth(se = FALSE) + 
  facet_grid(. ~ name)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

![](viz_i_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

## Some small notes

How many geoms have to exist?

You can have whatever geomes you want.

``` r
weather_df %>%
  ggplot(aes(x = date, y = tmax, color = name)) + 
  geom_smooth(se = FALSE) 
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

![](viz_i_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

You can use a neat geom\!

``` r
weather_df %>%
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_hex() # a 2d histogram, where brighter colors say there's a lot of data points. Overkill for dataset that's got a thousand obs. This sort of plot are helpful for understanding what the data distribution might look like.
```

    ## Warning: Computation failed in `stat_binhex()`:
    ##   Package `hexbin` required for `stat_binhex`.
    ##   Please install and try again.

![](viz_i_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

``` r
  # or 
  # geom_bin2d() same as geom_hex but with squares
  # or
  # geom_density2d() it's sort of like a contour plot
```

## Univariate plots

Histograms are really great.

``` r
weather_df %>%
  ggplot(aes(x = tmin)) +
  geom_histogram()
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

![](viz_i_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

Can we add color….

``` r
weather_df %>%
  ggplot(aes(x = tmin, fill = name)) +
  geom_histogram(position = "dodge")
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

![](viz_i_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

``` r
# or 
#weather_df %>%
  #ggplot(aes(x = tmin, fill = name)) +
  #geom_histogram()  
  #facet_grid(., ~name)

 # or don't need the color
#weather_df %>%
  #ggplot(aes(x = tmin)) +
  #geom_histogram()  
  #facet_grid(., ~name)
```

Let’s try a new geometry\!

``` r
weather_df %>%
  ggplot(aes(x = tmin, fill = name)) +
  geom_density()  
```

![](viz_i_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

``` r
weather_df %>%
  ggplot(aes(x = tmin, fill = name)) +
  geom_density(alpha = 0.3)  
```

![](viz_i_files/figure-gfm/unnamed-chunk-13-2.png)<!-- -->

What about box plots??

``` r
weather_df %>%
  ggplot(aes(y = tmin)) +
  geom_boxplot()
```

![](viz_i_files/figure-gfm/unnamed-chunk-14-1.png)<!-- -->

``` r
weather_df %>%
  ggplot(aes(x = name, y = tmin)) +
  geom_boxplot()
```

![](viz_i_files/figure-gfm/unnamed-chunk-14-2.png)<!-- -->

Trendy plots :)

``` r
weather_df %>%
  ggplot(aes(x = name, y = tmin, fill = name)) +
  geom_violin(alpha = 0.5)
```

![](viz_i_files/figure-gfm/unnamed-chunk-15-1.png)<!-- -->

``` r
  stat_summary(fun = "median")
```

    ## geom_pointrange: na.rm = FALSE, orientation = NA
    ## stat_summary: fun.data = NULL, fun = median, fun.max = NULL, fun.min = NULL, fun.args = list(), na.rm = FALSE, orientation = NA
    ## position_identity

Ridge plot – the most popular plot of 2017

``` r
weather_df %>%
  ggplot(aes(x = tmin, y = name)) +
  geom_boxplot()
```

![](viz_i_files/figure-gfm/unnamed-chunk-16-1.png)<!-- -->

## Save and Embed

Let’s save a scatterplot

``` r
weather_plot = 
  weather_df %>%
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = 0.5)

##ggsave("./results/weather_plot.pdf", weather_plot, width = 8, height = 5)
```

What about embedding…

``` r
weather_plot #adjust all the figures dimension
```

![](viz_i_files/figure-gfm/unnamed-chunk-18-1.png)<!-- -->

embed at different size

``` r
weather_plot
```

![](viz_i_files/figure-gfm/unnamed-chunk-19-1.png)<!-- -->
