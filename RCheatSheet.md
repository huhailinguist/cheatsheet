# R cheat sheet
#### Hai Hu

## read from subdirectory # use dot .
shortw <- read.table("./data/shortwords.tab", header = T)
## choose data
df <- read.table(file.choose(),header=T)

## mask means you are attaching/assigning data to a variable that already has some value, and the former value will be discarded (masked).
```R
# equivalent of python string operation 'in'
grepl(substr, str)

# check data types
typeof(x)

# replace the value in cell according to condition:
df$nm <- as.character(df$nm)
df$nm[df$nm == "B"] <- "b"
df$nm <- as.factor(df$nm)


#--------------------------------------
# ggplot
# reorder the x lab in ggplot2
ggplot(mtcars, aes(factor(cyl))) + 
  scale_x_discrete(limits=c(8,4,6)) # set limits to the order you want

# list of plots; print on the same graph
plots <- list()  # new empty list
for (i in 1:6) {
    p1 = qplot(1:10, rnorm(10), main = i)
    plots[[i]] <- p1  # add each plot into plot list
}
multiplot(plotlist = plots, cols = 3)

# ggplot put legend under the graph.
+ theme(legend.position=c(.5,-.7)) 

# set legend rows.
+ guides(col = guide_legend(nrow = 8))
```

## ggplot geom_point can have only maxiumu 6 shapes!!
```
The shape palette can deal with a maximum of 6 discrete values because more
than 6 becomes difficult to discriminate; you have 14. Consider specifying
shapes manually if you must have them. 

# could intead use scale_shape_manual:
scale_shape_manual(name="source", values=14:22,
                   labels=c("台湾中央社", "新华社", "法新社", "新加坡联合早报", "人民日报", 
                   "中央人民广播电台", "中央电视台", "Books: Google ngram"))
```

```
# axis text:
theme(legend.position=c(.5,-1),
      axis.text.x=element_text(angle = 20, hjust = 0.5, vjust=0.8))
```

## I want to create a new column based on 4 values in another column.


```R
if col1=1 then col2= G; 
if col1=2 then col2=H; 
if col1=3 then col2=J; 
if col1=4 then col2=K.
```
```
## Here is how to do it: 
col2 <- ifelse(col1==1, "G",        
ifelse(col1==2, "H",        
ifelse(col1==3, "J",        
ifelse(col1==4, "K",                        
NA  )))) # all other values map to NA
```
## mean per group in a data frame
Use **aggregate**
https://stackoverflow.com/questions/21982987/mean-per-group-in-a-data-frame

## drop levels from the main df
```
levels(droplevels(subdf$letters))
```
## group by interaction of two columns (variables)
https://stackoverflow.com/questions/9968976/group-by-two-columns-in-ggplot2
```
ggplot(d, aes(x=x, y=y, colour=treatment, shape = replicate,
  group=interaction(treatment, replicate))) + 
  geom_point() + geom_line()
```