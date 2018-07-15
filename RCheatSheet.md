# R cheat sheet
#### Hai Hu

# read from subdirectory # use dot .
shortw <- read.table("./data/shortwords.tab", header = T)
# or:
shortw <- read.table("data/shortwords.tab", header = T)

# choose data
df <- read.table(file.choose(),header=T)

# mask means you are attaching/assigning data to a variable that already has some value, and the former value will be discarded (masked).

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

# ggplot passing string variable. [link](https://stackoverflow.com/questions/19826352/pass-character-strings-to-ggplot2-within-a-function)
```R
# use aes_string()
ggplot(dat, aes_string(x = x, y = y))
```

# ggplot geom_point can have only maximum 6 shapes!
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

## ggplot mytheme
```R
mytheme=theme(
  plot.title = element_text(hjust = 0.5),
  axis.title.x = element_text(size=12),
  axis.text.x  = element_text(size=12, vjust = 0.5),
  axis.title.y = element_text(size=12),
  axis.text.y  = element_text(size=12),
  title = element_text(size=12),
  strip.text.x = element_text(face="bold", size=12)
  #legend.position="none" # no legend
)
```

## I want to create a new column based on 4 values in another column.

```R
# if col1=1 then col2= G; 
# if col1=2 then col2=H; 
# if col1=3 then col2=J; 
# if col1=4 then col2=K.

# Here is how to do it: 
col2 <- ifelse(col1==1, "G",        
ifelse(col1==2, "H",        
ifelse(col1==3, "J",        
ifelse(col1==4, "K",                        
NA  )))) # all other values map to NA
```

## mean per group in a data frame
Use **aggregate**
https://stackoverflow.com/questions/21982987/mean-per-group-in-a-data-frame

## aggregate data: dplyr package
```R
library(dplyr)
df18_mean = df18speaker %>%
  group_by(ageNgender, conditionCoarse) %>%
  summarise (grpMeanF1 = mean(F1mean, na.rm=TRUE),
             grpMeanF2 = mean(F2mean, na.rm=TRUE))
```

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

## select col based on list of col names
```R
variableList = c('RC.type', 'ChDist', 'WdDist', 'ChRC', 'WdRC', 
                 'RCdepth', 'RCnumPhrNodes')
dfRCnew = dfRC[,variableList]
```

## wide to long format based on one variable
```R
dfRC.m <- melt(dfRCnew, id.var = "RC.type")
```

## long to wide format
```R
df18_spk_mean_wide <- 
  dcast(df18_spk_mean, Subj + age + gender + ageNgender + lg.attitude ~ 
          conditionCoarse, 
        value.var="grpMeanF1")
```

## plot significance in boxplot
```R
library(ggsignif)
p <- ggplot(data = dfRC.m, aes(x=RC.type, y=value)) + 
  geom_boxplot(aes(fill=RC.type)) +
  geom_signif(comparisons = list(c("0: ORC", "1: SRC")), 
              map_signif_level=TRUE)
p + facet_wrap( ~ variable, scales="free")
```

## frequency table
```R
table(mtcars$gear)
```

## change order of levels of a factor
```R
df$g <- factor(df$g, levels = letters[4:1])
```

## check if substring is in one column: grepl
```R
# if 'below20' is in column Subj, then column age should have value '15-20'
df[grepl('below20', df$Subj), 'age'] = '15-20'
```
