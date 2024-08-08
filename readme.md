## Overview
Research Project By xiaotojohn.
Analysis of the top 3000 most owned games based on their historical prices. Rank based on steamspy.com
Historical prices from isthereanydeal.com (ITAD). 

This analysis aimed at finding the discounting behaviors of popular video games, which will help both game developers and platform managers to understand the distribution patterns of video games. And  consumers with this project may be able to find a optimized strategy to save their money on games.


## Workflow(Structure of Report)
- Data Collection & Organization
    - *Ranking data and list of games API*
    
        Provided by steamspy, games are linked with steam id. They provided a *ALL* method to fetch 1000 games on each API call.

    - *Price history data API and game info API*

        Provided by ITAD, identified with an ITAD unique id which is different from steam id. One data entry per call for each API.

    - *Game Look UP API*
        
        Provided by ITAD, mapping ITAD id to steam id.

    - *Fetch and Organize*
    
        data_collection.ipynb will call steamspy API first to fetch the list of games. Then a function will call all three APIs from ITAD. Firstly it will call game look up API to fetch the ITAD id corresponding to steam ids in app_list.csv. Then it will call history and game info API to fetch the coresponding data. ITAD's API could be unstable from time to time, thus we store data in batches to unsure authenticity and completeness.
    
    - *Basic Cleaning*
    
        Also some games may not have price histories, we drop those games in this step and merge the valid data into anydeal_merged.csv.

- Data Cleaning & Preprocessing
    - *Remove Duplicated Data*
    
        Steamspy may have two ids pointing to the same game.
    - *Feature Extraction*
        
        Price histories varies from game to game, thus we have to extract uniform features for every game. The following features are extracted:
        
                price_items = {
                    'first_available_price': '', # the first price of the game
                    'regular_price_reduced': '', # boolean, true if the regular price has been reduced
                    'price_reduced_date': '', # null if regular_price_reduced is false
                    'first_available_date': '', # date of the first available price
                    'last_available_date': '', # date of the last available price
                    'historical_low': '', # the lowest price of the game
                    'historical_low_date': '', # the date of the lowest price
                    'historical_high_discount': '', # the highest discount of the game
                    'historical_high_discount_date': '', # the date of the highest discount
                    'first_discount': '', # the first discount of the game
                    'first_discount_date': '', # the date of the first discount
                    'last_discount': '', # the last discount of the game
                    'last_discount_date': '', # the date of the last discount
                    'discount_distribution': '', # the distribution of discounts based on months




                    # the following modules require the daily price history
                    'average_duration_between_discounts_12months': '', # the average duration between discounts in the last 12 months
                    'average_duration_of_discounts_12months': '', # the average duration of discounts in the last 12 months
                    'avg_price': '', # the average price of the game 
                    'avg_discount': '', # the average discount of the game, calculated as the average of the discount percentage
                    # time series analysis items
                    'next_discount': '',
                    # price decay is the average second derivative of a moving average of the price
                    # we take 12 months of data, and calculate the moving average of the price
                    # then we calculate the second derivative of the moving average
                    # finally we average the second derivative over the 12 months
                    'price_decay_rate': ''

                }

- EDA(In progress)
    - Games started as free-to-play will not be considered.
    - A overview of distribution in features. 
    - A overview of tags and developers, a insight into the market preferences. 
    - A cohort based EDA will be conducted on games released in different years. 
- Patterns(In progress)
    - Assumption To be examined: Discounting patterns may tend to be in the three following patterns
        - Decreasing Convex
        - Decreasing Concave
        - Increasing at first, then into one of the categories above( Many games will have a launch price)
    - Approaches:
        - Patterns will be applied to different cohorts of games(2-10years). Games with a price history of less than 2 years will be ignored.
        - moving average price of 12 monthes to smooth the curve, then find the first and second derivatives.
        - How to categorize the patterns? As we are talking about curves. One solution might be KNN--use first/second derivatives as a vector.
        - other methods? need more research.
- Optimized Buying Strategy for Players(In progress)
    - Monte Carlo simulation for a game player with a different buying strategies. Prices sampled from the data set.
    - Evolutionary algorithm？
    - Any other approaches? 
- Predication(Really?) and evaluation
    - Machine learning algorithms for prediction.
        - First_discount_date based on tags/dev/other info: But from the EDA, many games will come with a launch discount, there will be a super high chance of predicting a launch discount.
        - What else to predict?
- Summary


## Dev Notes
Discount continued less than a day will be counted as one-day sale, this is based on the Steam's official settings: 
> Discounts cannot last longer than 14 days, or run for shorter than 1 day. Launch discounts in particular cannot run shorter than 7 days or last longer than 14 days.



## Files
### /jupyter_nb
- **data_collection.ipynb**: script to fetch data from APIs, .env file required. All collected data has been stored in this project folder so you dont have to run this script. If you need to , you have to register in Isthereanydeals.com(ITAD) to acquire an API
- **data_renomalization.ipynb**:  This script is used to process the price histories.The length of price histories are different for all games, thus we choose to preprocess the historical data in this script to produce uniform indicators for all games.
- **analysis.ipynb**: data analysis note book containing visualization and statistical models.

### /data
- **/textfiles/game_info/*.csv** : game info in batches from ITAD. Those files has been merged into *anydeal.csv*.
- **/textfiles/game_info/*.csv** : historical prices in batches from ITAD. Those files has been merged into *anydeal_prices.csv*.
- **shop_info.csv**: ITAD assigned unique IDs for different platforms, here we only need steam's ID.
- **app_list.csv**: list of games ranked by popularity, indexed with the steam id.
- **steam_spy_all_3000.csv**: 3000 mostly owned games from steamspy.
- **anydeal.csv**: all game info from ITAD.
- **anydeal_prices.csv**: all game price histories from ITAD.
- **anydeal_without_history.csv**: games without history. 
- **anydeal_merged.csv**: merged file, containing both game info and price histories. This file excluded games doesn't containing price histories.
- **price_items.csv**: processed dataframe, price histories has been processed and extracted as key indicators.

## Known Problems:

- Isthereanydeal's earliest records shows in late 2012, thus we take games after 2013.


- Dead by Daylight: Free to play for a while.

    > It's not free to claim, it's free to play for the time period. Once it ends, you won't be able to play the game unless you buy it. (https://www.reddit.com/r/deadbydaylight/comments/1edpm3q/i_got_dbd_on_steam_while_it_was_on_the_free/)

- preorder/Early Access made the release date unreliable. Can we define games who's first available date is before release date as who had EA period?
Answer: No. It is impossible to tell whether a game had EA session or not. There are multiple reasons:
    1. Once a game finished it's EA session, its release date will be updated. For ex, Baldur's Gate 3 started EA session in Oct 2020, it didn't publish the full version until Aug 2023. Now its release date is Aug 2023. And for Velheim, who entered EA in 2021, is still in EA, thus its release date is registered as the day of EA.
    2. Pre purchasing made it almost indistinguishable. Some games allow pre-purchasing a long time before its release。
    3. 

## Data Sources:
isthereanydeal.com (ITAD)

steamspy.com

## References:

https://partner.steamgames.com/doc/marketing/discounts

