---
title: 长短数据格式转换
tags: [R,tidyverse]
date: 2019-02-15 22:37:01
permalink:
categories:
description:
---
<p class="description">使用tidyverse在长短数据格式之间游走</p>

<!-- more -->

## Wide to long

```
gather(data, key, value, ..., na.rm = FALSE, convert = FALSE)
```

- `data`: The dataset to be modified (in our case, `seps`)
- `key`: the name of the new “naming” variable (`year`)
- `value`: the name of the new “result” variable (`value`)
- `na.rm`: whether missing values are removed (this dataset doesn’t have any, so it isn’t a problem)
- `convert`: convert anything that seems like it should be in another format to that other format, e.g. numeric to numeric (since we used `read_csv` we don’t need this one either)

## Long to wide

```
spread(data, key, value, fill = NA, convert = FALSE)
```

The format of this one is similar to `gather()`:

- `data`: The data to be reformatted (`inprogress`)
- `key`: The column you want to split apart (`Field`)
- `value`: The column you want to use to populate the new columns (the `value` column we just created in the spread step)
- `fill`: what to substitute if there are combinations that don’t exist (not a problem here)
- `convert`: whether to fix incorrect data types as it goes (not a problem here)

## 参考来源

https://rstudio-pubs-static.s3.amazonaws.com/282405_e280f5f0073544d7be417cde893d78d0.html

http://www.cookbook-r.com/Manipulating_data/Converting_data_between_wide_and_long_format/

https://github.com/rstudio/cheatsheets/blob/master/data-import.pdf

https://uc-r.github.io/tidyr

<hr />