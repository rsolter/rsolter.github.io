## Creating fake datasets with R’s expand.grid()

*Background*

Recently, I was building a Tableau dashboard for a client that included
a geographic view of the United States that needed to show multiple
layers of data on the same view. Tableau, 2020.4 allows for an
[unlimited number of
layers](https://www.tableau.com/about/blog/2020/12/build-custom-maps-easy-way-multiple-map-layers-tableau)
to be added to a geo-spatial view, but earlier versions only allow for
two. The first of the layers on the map showed social vulnerability of
US postal codes via a color spectrum while the second layer displayed
store locations based upon their latitude and longitude.

![](/rblogging/2021/02/01/Expand_Grid_Image.png){: .align-center}


The view was filterable on several store characteristics, which allowed
the user to hone in on a specific subset of stores for demonstration
purposes. However, the client requested that certain “super stores” be
always visible and unaffected by any filter actions.

I played with LOD and context filters at first to find a solution, but
ultimately decided I needed to multiple records for each “super store”
that duplicated the latitude and longitude measures for every possible
combination of store characteristic; that way, no matter which filters
were engaged, the store location would remain in view.

**expand.grid()**

How to accomplish this? I had six filters that affected the stores that
were in the view, each with their own distinct set of possible values.
In total there roughly \~3,000 possible combinations of the six filter
values. Below is a snippet of R code for creating a similar, fictional
data set:

``` r
# Creating a fake dataset with 5 stores
store_ids <- 1:5
latitude <- c(42.3601,42.5195,42.2626,42.4515,42.065)
longitude <- c(-71.0589,-70.8967,-71.8023,-73.261,-70.197)
Type <- c("Type 1","Type 2","Type 3")
Stories <- c("1-story","2-story")
Size <- c("No Expansion","Possible Expansion")
Set <- c("Set A"," Set B","Set C","Set D")
Network <- c("In Network","Out of Network")
Tier <- c("Tier 1","Tier 2","Tier 3","Tier 4","Tier 5","Tier 6")
Age <- c("1970s","1980s","1990s","2000s","2010s")

DF<-data.frame(store_ids,latitude,longitude,
               Type=sample(x=Type,size = 5,replace = TRUE),
               Stories=sample(x=Stories,size = 5,replace = TRUE),
               Size=sample(x=Size,size = 5,replace = TRUE),
               Set=sample(x=Set,size = 5,replace = TRUE),
               Network=sample(x=Network,size = 5,replace = TRUE),
               Tier=sample(x=Tier,size = 5,replace = TRUE),
               Age=sample(x=Age,size = 5,replace = TRUE))

glimpse(DF)
```

    ## Rows: 5
    ## Columns: 10
    ## $ store_ids <int> 1, 2, 3, 4, 5
    ## $ latitude  <dbl> 42.3601, 42.5195, 42.2626, 42.4515, 42.0650
    ## $ longitude <dbl> -71.0589, -70.8967, -71.8023, -73.2610, -70.1970
    ## $ Type      <chr> "Type 2", "Type 2", "Type 2", "Type 2", "Type 1"
    ## $ Stories   <chr> "2-story", "1-story", "1-story", "1-story", "1-story"
    ## $ Size      <chr> "No Expansion", "No Expansion", "No Expansion", "No Expansio…
    ## $ Set       <chr> " Set B", "Set A", " Set B", "Set A", "Set C"
    ## $ Network   <chr> "Out of Network", "Out of Network", "In Network", "Out of Ne…
    ## $ Tier      <chr> "Tier 3", "Tier 6", "Tier 5", "Tier 3", "Tier 5"
    ## $ Age       <chr> "1980s", "2010s", "1970s", "1970s", "2010s"

Now to create fake data for a single store with all possible values for
store characteristics. In the snippet of code below, I select the record
with ‘store_ids’==1 to create a single-row data set for that store
record.

Then, I use *expand.grid()*, which takes a selection of vectors or
factors as input and outputs a dataframe including all possible values
of those vectors. In this case, I feel Type, Stories, Size, Set,
Network, Tier, & Age vectors from above into a dataframe called ‘Dummy’.
The resulting data.frame ‘Dummy’ 2,880 records.

``` r
## Code to Expand.Grid() records for store_ids 1
Store_id_1_DF <- DF %>% filter(store_ids==1) %>% select(store_ids,latitude,longitude)

Dummy <- expand.grid(Type,Stories,Size,Set,Network,Tier,Age)
glimpse(Dummy)
```

    ## Rows: 2,880
    ## Columns: 7
    ## $ Var1 <fct> Type 1, Type 2, Type 3, Type 1, Type 2, Type 3, Type 1, Type 2, T…
    ## $ Var2 <fct> 1-story, 1-story, 1-story, 2-story, 2-story, 2-story, 1-story, 1-…
    ## $ Var3 <fct> No Expansion, No Expansion, No Expansion, No Expansion, No Expans…
    ## $ Var4 <fct> Set A, Set A, Set A, Set A, Set A, Set A, Set A, Set A, Set A, Se…
    ## $ Var5 <fct> In Network, In Network, In Network, In Network, In Network, In Ne…
    ## $ Var6 <fct> Tier 1, Tier 1, Tier 1, Tier 1, Tier 1, Tier 1, Tier 1, Tier 1, T…
    ## $ Var7 <fct> 1970s, 1970s, 1970s, 1970s, 1970s, 1970s, 1970s, 1970s, 1970s, 19…

I then bind these two results together and then merge with the original
dataset ‘DF’ to create an updated dataset that will always show the
‘store_ids’ 1, regardless of the filters applied.

``` r
Store_id_1_DF <-cbind(Store_id_1_DF,Dummy)
names(Store_id_1_DF) <- names(DF)

new_DF <- rbind(DF,Store_id_1_DF)
head(new_DF,20)
```

    ##    store_ids latitude longitude   Type Stories               Size    Set
    ## 1          1  42.3601  -71.0589 Type 2 2-story       No Expansion  Set B
    ## 2          2  42.5195  -70.8967 Type 2 1-story       No Expansion  Set A
    ## 3          3  42.2626  -71.8023 Type 2 1-story       No Expansion  Set B
    ## 4          4  42.4515  -73.2610 Type 2 1-story       No Expansion  Set A
    ## 5          5  42.0650  -70.1970 Type 1 1-story       No Expansion  Set C
    ## 6          1  42.3601  -71.0589 Type 1 1-story       No Expansion  Set A
    ## 7          1  42.3601  -71.0589 Type 2 1-story       No Expansion  Set A
    ## 8          1  42.3601  -71.0589 Type 3 1-story       No Expansion  Set A
    ## 9          1  42.3601  -71.0589 Type 1 2-story       No Expansion  Set A
    ## 10         1  42.3601  -71.0589 Type 2 2-story       No Expansion  Set A
    ## 11         1  42.3601  -71.0589 Type 3 2-story       No Expansion  Set A
    ## 12         1  42.3601  -71.0589 Type 1 1-story Possible Expansion  Set A
    ## 13         1  42.3601  -71.0589 Type 2 1-story Possible Expansion  Set A
    ## 14         1  42.3601  -71.0589 Type 3 1-story Possible Expansion  Set A
    ## 15         1  42.3601  -71.0589 Type 1 2-story Possible Expansion  Set A
    ## 16         1  42.3601  -71.0589 Type 2 2-story Possible Expansion  Set A
    ## 17         1  42.3601  -71.0589 Type 3 2-story Possible Expansion  Set A
    ## 18         1  42.3601  -71.0589 Type 1 1-story       No Expansion  Set B
    ## 19         1  42.3601  -71.0589 Type 2 1-story       No Expansion  Set B
    ## 20         1  42.3601  -71.0589 Type 3 1-story       No Expansion  Set B
    ##           Network   Tier   Age
    ## 1  Out of Network Tier 3 1980s
    ## 2  Out of Network Tier 6 2010s
    ## 3      In Network Tier 5 1970s
    ## 4  Out of Network Tier 3 1970s
    ## 5      In Network Tier 5 2010s
    ## 6      In Network Tier 1 1970s
    ## 7      In Network Tier 1 1970s
    ## 8      In Network Tier 1 1970s
    ## 9      In Network Tier 1 1970s
    ## 10     In Network Tier 1 1970s
    ## 11     In Network Tier 1 1970s
    ## 12     In Network Tier 1 1970s
    ## 13     In Network Tier 1 1970s
    ## 14     In Network Tier 1 1970s
    ## 15     In Network Tier 1 1970s
    ## 16     In Network Tier 1 1970s
    ## 17     In Network Tier 1 1970s
    ## 18     In Network Tier 1 1970s
    ## 19     In Network Tier 1 1970s
    ## 20     In Network Tier 1 1970s

In conclusion, **expand_grid** is a good utility function to quickly
create a dataframe with all possible combinations of supplied vectors of
factors.
