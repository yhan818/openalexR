
<!-- README.md is generated from README.Rmd. Please edit that file -->

# openalexR <img src="man/figures/logo.png" align="right" height="139"/>

<!-- badges: start -->

[![R-CMD-check](https://github.com/massimoaria/openalexR/actions/workflows/R-CMD-check.yaml/badge.svg)](https://github.com/massimoaria/openalexR/actions/workflows/R-CMD-check.yaml)
[![Lifecycle:
experimental](https://img.shields.io/badge/lifecycle-experimental-orange.svg)](https://lifecycle.r-lib.org/articles/stages.html#experimental)
[![CRAN
status](https://www.r-pkg.org/badges/version/openalexR)](https://CRAN.R-project.org/package=openalexR)
[![](http://cranlogs.r-pkg.org/badges/grand-total/openalexR)](https://cran.r-project.org/package=openalexR)
[![Codecov test
coverage](https://codecov.io/gh/massimoaria/openalexR/branch/main/graph/badge.svg)](https://app.codecov.io/gh/massimoaria/openalexR?branch=main)
<!-- badges: end -->

**openalexR** helps you interface with the
[OpenAlex](https://openalex.org) API to retrieve bibliographic
infomation about publications, authors, venues, institutions and
concepts with 5 main functions:

-   `oa_fetch`: composes three functions below so the user can execute
    everything in one step, *i.e.*, `oa_query |> oa_request |> oa2df`

-   `oa_query`: generates a valid query, written following the OpenAlex
    API syntax, from a set of arguments provided by the user.

-   `oa_request`: downloads a collection of entities matching the query
    created by `oa_query` or manually written by the user, and returns a
    JSON object in a list format.

-   `oa2df`: converts the JSON object in classical bibliographic
    tibble/data frame.

-   `oa_random`: get random entity, *e.g.*, `oa_random("works")` gives a
    different work each time you run it

## Setup

You can install the developer version of openalexR from
[GitHub](https://github.com) with:

``` r
install.packages("remotes")
remotes::install_github("massimoaria/openalexR")
```

You can install the released version of openalexR from
[CRAN](https://CRAN.R-project.org) with:

``` r
install.packages("openalexR")
```

Before we go any further, we highly recommend you set `openalexR.mailto`
option so that your requests go to [the polite
pool](https://docs.openalex.org/api#the-polite-pool) for faster response
times:

``` r
options(openalexR.mailto = "example@email.com")
```

Bonus point if you put this in your `.Rprofile` with
`file.edit("~/.Rprofile")`.

``` r
library(openalexR)
library(dplyr)
library(ggplot2)
```

## Examples

There are different
[filters](https://massimoaria.github.io/openalexR/articles/Filters)/arguments
you can use in `oa_fetch`, depending on which
[entity](https://docs.openalex.org/about-the-data) you’re interested in:
works, authors, venues, institutions, or concepts. We show a few
examples below.

### Works

**Goal**: Download all information about a givens set of publications
(known DOIs).

Use `doi` as a [works
filter](https://massimoaria.github.io/openalexR/articles/Filters.html#works)
(either the canonical form with <https://doi.org/> or without):

``` r
works_from_dois <- oa_fetch(
  doi = c("10.1016/j.joi.2017.08.007", "https://doi.org/10.1007/s11192-013-1221-3"),
  entity = "works",
  verbose = TRUE
)
#> Requesting url: https://api.openalex.org/works?filter=doi%3A10.1016%2Fj.joi.2017.08.007%7Chttps%3A%2F%2Fdoi.org%2F10.1007%2Fs11192-013-1221-3
#> Getting 1 page of results with a total of 2 records...
```

We can view the output tibble/dataframe, `works_from_dois`,
interactively in RStudio or inspect it with base functions like `str` or
`head`. We also provide the experimental `show_works` function to
simplify the result (e.g., remove some columns, keep first/last author)
for easy viewing.

*Note*: the following table is wrapped in `knitr::kable()` to be
displayed nicely in this README, but you will most likely not need this
function.

``` r
# str(works_from_dois, max.level = 2)
# head(works_from_dois)
# show_works(works_from_dois) 

works_from_dois |>
  show_works() |>
  knitr::kable()
```

| id          | display_name                                                              | first_author      | last_author        | so                      | url | is_oa | top_concepts                          |
|:------------|:--------------------------------------------------------------------------|:------------------|:-------------------|:------------------------|:----|:------|:--------------------------------------|
| W2755950973 | bibliometrix : An R-tool for comprehensive science mapping analysis       | Massimo Aria      | Corrado Cuccurullo | Journal of Informetrics | NA  | FALSE | Data science                          |
| W2038196424 | Coverage and adoption of altmetrics sources in the bibliometric community | Stefanie Haustein | Jens Terliesner    | Scientometrics          | NA  | FALSE | Altmetrics, Bookmarking, Social media |

**Goal**: Download all works published by a set of authors (known
ORCIDs).

Use `author.orcid` as a filter (either canonical form with
<https://orcid.org/> or without will work):

``` r
works_from_orcids <- oa_fetch(
  entity = "works",
  author.orcid = c("0000-0001-6187-6610", "0000-0002-8517-9411"),
  verbose = TRUE
)
#> Requesting url: https://api.openalex.org/works?filter=author.orcid%3A0000-0001-6187-6610%7C0000-0002-8517-9411
#> Getting 2 pages of results with a total of 211 records...

works_from_orcids |> 
  show_works() |> 
  knitr::kable()
```

| id          | display_name                                                                                 | first_author       | last_author          | so                                                                    | url                                                                                             | is_oa | top_concepts                          |
|:------------|:---------------------------------------------------------------------------------------------|:-------------------|:---------------------|:----------------------------------------------------------------------|:------------------------------------------------------------------------------------------------|:------|:--------------------------------------|
| W2755950973 | bibliometrix : An R-tool for comprehensive science mapping analysis                          | Massimo Aria       | Corrado Cuccurullo   | Journal of Informetrics                                               | NA                                                                                              | FALSE | Data science                          |
| W2741809807 | The state of OA: a large-scale analysis of the prevalence and impact of Open Access articles | Heather A. Piwowar | Stefanie Haustein    | PeerJ                                                                 | <https://doi.org/10.7717/peerj.4375>                                                            | TRUE  | Citation, License, Bibliometrics      |
| W2122130843 | Scientometrics 2.0: New metrics of scholarly impact on the social Web                        | Jason Priem        | Bradely H. Hemminger | First Monday                                                          | NA                                                                                              | FALSE | Bookmarking, Altmetrics, Social media |
| W2041540760 | How and why scholars cite on Twitter                                                         | Jason Priem        | Kaitlin L. Costello  | Proceedings Of The Association For Information Science And Technology | <https://onlinelibrary.wiley.com/doi/pdfdirect/10.1002/meet.14504701201>                        | TRUE  | Citation, Conversation, Social media  |
| W2038196424 | Coverage and adoption of altmetrics sources in the bibliometric community                    | Stefanie Haustein  | Jens Terliesner      | Scientometrics                                                        | NA                                                                                              | FALSE | Altmetrics, Bookmarking, Social media |
| W2396414759 | The Altmetrics Collection                                                                    | Jason Priem        | Dario Taraborelli    | PLOS ONE                                                              | <https://journals.plos.org/plosone/article/file?id=10.1371/journal.pone.0048753&type=printable> | TRUE  | Altmetrics                            |

**Goal**: Download all works that have been cited more than 50 times,
published between 2020 and 2021, and include the strings “bibliometric
analysis” or “science mapping” in the title. Maybe we also want the
results to be sorted by total citations in a descending order.

``` r
works_search <- oa_fetch(
  entity = "works",
  title.search = c("bibliometric analysis", "science mapping"),
  cited_by_count = ">50",
  from_publication_date = "2020-01-01",
  to_publication_date = "2021-12-31",
  sort = "cited_by_count:desc",
  verbose = TRUE
)
#> Requesting url: https://api.openalex.org/works?filter=title.search%3Abibliometric%20analysis%7Cscience%20mapping%2Ccited_by_count%3A%3E50%2Cfrom_publication_date%3A2020-01-01%2Cto_publication_date%3A2021-12-31&sort=cited_by_count%3Adesc
#> Getting 1 page of results with a total of 70 records...

works_search |>
  show_works() |>
  knitr::kable()
```

| id          | display_name                                                                                                                  | first_author            | last_author        | so                                        | url                                                                                                                           | is_oa | top_concepts                                                  |
|:------------|:------------------------------------------------------------------------------------------------------------------------------|:------------------------|:-------------------|:------------------------------------------|:------------------------------------------------------------------------------------------------------------------------------|:------|:--------------------------------------------------------------|
| W3160856016 | How to conduct a bibliometric analysis: An overview and guidelines                                                            | Naveen Donthu           | Weng Marc Lim      | Journal of Business Research              | <https://doi.org/10.1016/j.jbusres.2021.04.070>                                                                               | TRUE  | Bibliometrics, Field (mathematics), Resource (disambiguation) |
| W3038273726 | Investigating the emerging COVID-19 research trends in the field of business and management: A bibliometric analysis approach | Surabhi Verma           | Anders Gustafsson  | Journal of Business Research              | NA                                                                                                                            | FALSE | Bibliometrics, Field (mathematics), MEDLINE                   |
| W2990450011 | Forty-five years of Journal of Business Research: A bibliometric analysis                                                     | Naveen Donthu           | Debidutta Pattnaik | Journal of Business Research              | NA                                                                                                                            | FALSE | Bibliometrics                                                 |
| W3001491100 | Software tools for conducting bibliometric analysis in science: An up-to-date review                                          | Jose A. Moral-Munoz     | Manuel Cobo        | Profesional De La Informacion             | <https://revista.profesionaldelainformacion.com/index.php/EPI/article/download/epi.2020.ene.03/47883>                         | TRUE  | Bibliometrics, Software                                       |
| W3044902155 | Financial literacy: A systematic review and bibliometric analysis                                                             | Kirti Savyasacchi Goyal | Satish Kumar       | International Journal of Consumer Studies | NA                                                                                                                            | FALSE | Financial literacy, Citation, Content analysis                |
| W3011866596 | A Bibliometric Analysis of COVID-19 Research Activity: A Call for Increased Output                                            | Mohamad A. Chahrour     | Hussein H. Khachfe | Cureus                                    | <https://www.cureus.com/articles/29507-a-bibliometric-analysis-of-covid-19-research-activity-a-call-for-increased-output.pdf> | TRUE  | Observational study, Gross domestic product, Population       |

### Authors

**Goal**: Download author information when we know their ORCID.

Here, instead of `author.orcid` like earlier, we have to use `orcid` as
an argument. This may be a little confusing, but again, a different
entity (**authors** instead of **works**) requires a [different set of
filters](https://massimoaria.github.io/openalexR/articles/Filters.html#authors).

``` r
authors_from_orcids <- oa_fetch(
  entity = "authors",
  orcid = c("0000-0001-6187-6610", "0000-0002-8517-9411")
)

authors_from_orcids |>
  show_authors() |>
  knitr::kable()
```

| id          | display_name | orcid               | works_count | cited_by_count | affiliation_display_name         | top_concepts                             |
|:------------|:-------------|:--------------------|------------:|---------------:|:---------------------------------|:-----------------------------------------|
| A923435168  | Massimo Aria | 0000-0002-8517-9411 |         161 |           3658 | University of Naples Federico II | Statistics, Internal medicine, Pathology |
| A2208157607 | Jason Priem  | 0000-0001-6187-6610 |          51 |           1663 | HortResearch                     | World Wide Web, Library science, Law     |

**Goal**: Acquire information on the authors of this package.

We can use other filters such as `display_name` and `has_orcid`:

``` r
authors_from_names <- oa_fetch(
  entity = "authors",
  display_name = c("Massimo Aria", "Jason Priem"),
  has_orcid = TRUE
)
authors_from_names |>
  show_authors() |>
  knitr::kable()
```

| id          | display_name | orcid               | works_count | cited_by_count | affiliation_display_name         | top_concepts                             |
|:------------|:-------------|:--------------------|------------:|---------------:|:---------------------------------|:-----------------------------------------|
| A923435168  | Massimo Aria | 0000-0002-8517-9411 |         161 |           3658 | University of Naples Federico II | Statistics, Internal medicine, Pathology |
| A2208157607 | Jason Priem  | 0000-0001-6187-6610 |          51 |           1663 | HortResearch                     | World Wide Web, Library science, Law     |

**Goal**: Download all authors’ records of scholars who work at the
[University of Naples Federico
II](https://explore.openalex.org/institutions/I71267560) (OpenAlex ID:
I71267560) and have published at least 500 publications.

Let’s first check how many records match the query, then download the
entire collection. We can do this by first defining a list of arguments,
then adding `count_only` (default `FALSE`) to this list:

``` r
my_arguments <- list(
  entity = "authors",
  last_known_institution.id = "I71267560",
  works_count = ">499"
)

do.call(oa_fetch, c(my_arguments, list(count_only = TRUE)))
#>      count db_response_time_ms page per_page
#> [1,]    56                  53    1        1
do.call(oa_fetch, my_arguments) |>
  show_authors() |>
  knitr::kable()
```

| id          | display_name        | orcid               | works_count | cited_by_count | affiliation_display_name         | top_concepts                                         |
|:------------|:--------------------|:--------------------|------------:|---------------:|:---------------------------------|:-----------------------------------------------------|
| A2061787601 | Luca Lista          | 0000-0001-6471-5492 |        2712 |          35227 | University of Naples Federico II | Nuclear physics, Particle physics, Quantum mechanics |
| A3088244307 | A. K. Sanchez       | NA                  |        2047 |          38443 | University of Naples Federico II | Quantum mechanics, Nuclear physics, Particle physics |
| A2159261619 | Annamaria Colao     | 0000-0001-6986-266X |        1654 |          28807 | University of Naples Federico II | Internal medicine, Endocrinology, Pathology          |
| A2011452631 | Leonardo Merola     | NA                  |        1575 |          27097 | University of Naples Federico II | Quantum mechanics, Particle physics, Nuclear physics |
| A2725087388 | Mariagrazia Alviggi | NA                  |        1561 |          26514 | University of Naples Federico II | Quantum mechanics, Particle physics, Nuclear physics |
| A2103058924 | Mario Mancini       | NA                  |        1558 |          16510 | University of Naples Federico II | Internal medicine, Endocrinology, Biochemistry       |

## Example analyses

**Goal**: track the popularity of *Biology* concepts over time.

We first download the records of all level-1 concepts/keywords that
concern over one million works:

``` r
library(gghighlight)
concept_df <- oa_fetch(
  entity = "concepts",
  level = 1,
  ancestors.id = "https://openalex.org/C86803240", # Biology
  works_count = ">1000000"
)

concept_df |>
  select(display_name, counts_by_year) |>
  tidyr::unnest(counts_by_year) |>
  filter(year < 2022) |>
  ggplot() +
  aes(x = year, y = works_count, color = display_name) +
  facet_wrap(~display_name) +
  geom_line(linewidth = 0.7) +
  scale_color_brewer(palette = "Dark2") +
  labs(
    x = NULL, y = "Works count",
    title = "Virology spiked in 2020."
  ) +
  guides(color = "none") +
  gghighlight(
    max(works_count) > 244000, 
    label_params = list(nudge_y = 10^5, segment.color = NA)
  )
#> label_key: display_name
```

<img src="man/figures/README-biological-concepts-1.png" width="100%" />

**Goal**: Rank institutions in Italy by total number of citations.

We want download all records regarding Italian institutions
(country_code:it) that are classified as educational (type:education).
Again, we check how many records match the query then download the
collection:

``` r
italy_insts <- oa_fetch(
  entity = "institutions",
  country_code = "it",
  type = "education",
  verbose = TRUE
)
#> Requesting url: https://api.openalex.org/institutions?filter=country_code%3Ait%2Ctype%3Aeducation
#> Getting 2 pages of results with a total of 231 records...

italy_insts |>
  slice_max(cited_by_count, n = 8) |>
  mutate(display_name = forcats::fct_reorder(display_name, cited_by_count)) |>
  ggplot() +
  aes(x = cited_by_count, y = display_name, fill = display_name) +
  geom_col() +
  scale_fill_viridis_d(option = "E") +
  guides(fill = "none") +
  labs(
    x = "Total citations", y = NULL,
    title = "Italian references"
  ) +
  coord_cartesian(expand = FALSE)
```

<img src="man/figures/README-italy-insts-1.png" width="100%" />

And what do they publish on?

``` r
# The package wordcloud needs to be installed to run this chunk
# library(wordcloud)

concept_cloud <- italy_insts |>
  select(inst_id = id, x_concepts) |>
  tidyr::unnest(x_concepts) |>
  filter(level == 1) |>
  select(display_name, score) |>
  group_by(display_name) |>
  summarise(score = sum(score))

pal <- c("black", scales::brewer_pal(palette = "Set1")(5))
set.seed(1)
wordcloud::wordcloud(
  concept_cloud$display_name,
  concept_cloud$score,
  scale = c(2, .4),
  colors = pal
)
```

<img src="man/figures/README-concept-cloud-1.png" width="100%" />

**Goal**: Visualize big journals’ topics.

We first download all records regarding journals that have published
more than 300,000 works, then visualize their scored concepts:

``` r
# The package ggtext needs to be installed to run this chunk
# library(ggtext)

jours <- oa_fetch(
  entity = "venues",
  works_count = ">500000",
  verbose = TRUE
) |>
  filter(is.na(publisher)|!grepl("Elsevier", publisher)) |>
  distinct(display_name, .keep_all = TRUE) |>
  select(jour = display_name, x_concepts) |>
  tidyr::unnest(x_concepts) |>
  filter(level == 0) |>
  left_join(concept_abbrev) |>
  mutate(abbreviation = gsub(" ", "<br>", abbreviation)) |>
  tidyr::complete(jour, abbreviation, fill = list(score = 0)) |>
  group_by(jour) |>
  mutate(
    color = if_else(score > 10, "#1A1A1A", "#D9D9D9"), # CCCCCC
    label = paste0("<span style='color:", color, "'>", abbreviation, "</span>")
  )

jours |>
  ggplot() +
  aes(fill = jour, y = score, x = abbreviation, group = jour) +
  facet_wrap(~jour) +
  geom_hline(yintercept = c(45, 90), colour = "grey90", linewidth = 0.2) +
  geom_segment(
    aes(x = abbreviation, xend = abbreviation, y = 0, yend = 100),
    color = "grey95"
  ) +
  geom_col(color = "grey20") +
  coord_polar(clip = "off") +
  theme_bw() +
  theme(
    plot.background = element_rect(fill = "transparent", colour = NA),
    panel.background = element_rect(fill = "transparent", colour = NA),
    panel.grid = element_blank(),
    panel.border = element_blank(),
    axis.text = element_blank(),
    axis.ticks.y = element_blank()
  ) +
  ggtext::geom_richtext(
    aes(y = 120, label = label),
    fill = NA, label.color = NA, size = 3
  ) +
  scale_fill_brewer(palette = "Set1") +
  guides(fill = "none") +
  labs(y = NULL, x = NULL, title = "Journal clocks")
```

<img src="man/figures/README-big-journals-1.png" width="100%" />

## Snowball search

The user can also perform *snowballing* with `oa_snowball`. Snowballing
is a literature search technique where the researcher starts with a set
of articles and find articles that cite or were cited by the original
set. `oa_snowball` returns a list of 2 elements: *nodes* and *edges*.
Similar to `oa_fetch`, `oa_snowball` finds and returns information on a
core set of articles satisfying certain criteria, but, unlike
`oa_fetch`, it also returns information the articles that cite and are
cited by this core set.

``` r
# The packages ggraph and tidygraph need to be installed to run this chunk
library(ggraph)
library(tidygraph)
#> 
#> Attaching package: 'tidygraph'
#> The following object is masked from 'package:stats':
#> 
#>     filter

snowball_docs <- oa_snowball(
  identifier = c("W1964141474", "W1963991285"),
  verbose = TRUE
)
#> Requesting url: https://api.openalex.org/works?filter=openalex_id%3AW1964141474%7CW1963991285
#> Getting 1 page of results with a total of 2 records...
#> Collecting all documents citing the target papers...
#> Requesting url: https://api.openalex.org/works?filter=cites%3AW1963991285%7CW1964141474
#> Getting 3 pages of results with a total of 470 records...
#> Collecting all documents cited by the target papers...
#> Requesting url: https://api.openalex.org/works?filter=cited_by%3AW1963991285%7CW1964141474
#> Getting 1 page of results with a total of 87 records...

ggraph(graph = as_tbl_graph(snowball_docs), layout = "stress") +
  geom_edge_link(aes(alpha = after_stat(index)), show.legend = FALSE) +
  geom_node_point(aes(fill = oa_input, size = cited_by_count), shape = 21, color = "white") +
  geom_node_label(aes(filter = oa_input, label = id), nudge_y = 0.2, size = 3) +
  scale_edge_width(range = c(0.1, 1.5), guide = "none") +
  scale_size(range = c(3, 10), guide = "none") +
  scale_fill_manual(values = c("#a3ad62", "#d46780"), na.value = "grey", name = "") +
  theme_graph() +
  theme(legend.position = "bottom") +
  guides(fill = "none")
```

<img src="man/figures/README-snowballing-1.png" width="100%" />

## N-grams

OpenAlex offers (limited) support for [fulltext
N-grams](https://docs.openalex.org/api-entities/works/get-n-grams#fulltext-coverage)
of Work entities (these have IDs starting with `"W"`). Given a vector of
work IDs, `oa_ngrams` returns a dataframe of N-gram data (in the
`ngrams` list-column) for each work.

``` r
ngrams_data <- oa_ngrams(
  works_identifier = c("W1964141474", "W1963991285"),
  verbose = TRUE
)

ngrams_data
#> # A tibble: 2 × 4
#>   id                               doi                              count ngrams
#>   <chr>                            <chr>                            <int> <list>
#> 1 https://openalex.org/W1964141474 https://doi.org/10.1016/j.conb.…  2733 <df>  
#> 2 https://openalex.org/W1963991285 https://doi.org/10.1126/science…  2338 <df>

lapply(ngrams_data$ngrams, head, 3)
#> [[1]]
#>                                        ngram ngram_tokens ngram_count
#> 1                 brain basis and core cause            5           2
#> 2                     cause be not yet fully            5           2
#> 3 include structural and functional magnetic            5           2
#>   term_frequency
#> 1   0.0006637902
#> 2   0.0006637902
#> 3   0.0006637902
#> 
#> [[2]]
#>                                          ngram ngram_tokens ngram_count
#> 1          intact but less accessible phonetic            5           1
#> 2 accessible phonetic representation in Adults            5           1
#> 3       representation in Adults with Dyslexia            5           1
#>   term_frequency
#> 1   0.0003756574
#> 2   0.0003756574
#> 3   0.0003756574

ngrams_data |>
  tidyr::unnest(ngrams) |>
  filter(ngram_tokens == 2) |>
  select(id, ngram, ngram_count) |>
  group_by(id) |>
  slice_max(ngram_count, n = 10, with_ties = FALSE) |>
  ggplot(aes(ngram_count, forcats::fct_reorder(ngram, ngram_count))) +
  geom_col(aes(fill = id), show.legend = FALSE) +
  facet_wrap(~ id, scales = "free_y") +
  labs(
    title = "Top 10 fulltext bigrams",
    x = "Count",
    y = NULL
  )
```

<img src="man/figures/README-ngrams-1.png" width="100%" />

`oa_ngrams` can sometimes be slow because the N-grams data can get
pretty big, but given that the N-grams are
`"cached via CDN"`\](<https://docs.openalex.org/api-entities/works/get-n-grams#api-endpoint>),
you may also consider parallelizing for this special case (`oa_ngrams`
does this automatically if you have `{curl} >= v5.0.0`).

## About OpenAlex

![oar-img](man/figures/oar.png)

<div style="text-align: right">

Schema credits: [@dhimmel](https://github.com/dhimmel)

</div>

[OpenAlex](https://openalex.org) is a fully open catalog of the global
research system. It’s named after the ancient [Library of
Alexandria](https://en.wikipedia.org/wiki/Library_of_Alexandria). The
OpenAlex dataset describes scholarly entities and how those entities are
connected to each other. There are five types of entities:

-   **Works** are papers, books, datasets, etc; they cite other works

-   **Authors** are people who create works

-   **Venues** are journals and repositories that host works

-   **Institutions** are universities and other orgs that are affiliated
    with works (via authors)

-   **Concepts** *tag* Works with a topic

## Code of Conduct

Please note that this package is released with a [Contributor Code of
Conduct](https://ropensci.org/code-of-conduct/). By contributing to this
project, you agree to abide by its terms.

## Acknowledgements

Package hex was made with [Midjourney](https://www.midjourney.com/home/)
and thus inherits a [CC BY-NC 4.0
license](https://creativecommons.org/licenses/by-nc/4.0/legalcode).
