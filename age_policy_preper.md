지역과 연령대에 따라 선호 정책이 다를까?
================

1.요약(초록)
============

선호정책이 지역별로 어떻게 다른지 알아보다 특이하게 서남권만 11개 정책 중 교육정책이 상위권이 아닌 중위권이었다. 그래서 연령대별로 선호정책이 다를 것으로 생각하여 연령대별 선호정책을 알아보았다. 많은 데이터 중 필요한 데이터인 지역, 연령, 선호정책 1순위를 활용하였다.

우선 지역별 선호정책 순위를 알기 위해 필요한 형태로 데이터를 가공하였다.(area\_name,policy\_name) 결과는 5개의 지역 중 4개의 지역에서 교육정책이 상위권인데 반면 서남권만 유일하게 중위권이었다. 그래서 나는 서남권에 중년층보다는 노년층이 많을 것으로 생각하여 연령대별 선호정책을 알아보기로 하였다.

다음으로 연령대별 선호정책 순위를 알기 위해 A1\_4\_1\_1열을 필요한 형태로 가공하였다. 보건의료\_건강분야 정책이 상위권이었기 때문에 노인 인구가 많을 것으로 생각했던 나와의 생각과는 달리 다른 지역에 비해 노인 인구가 많지 않았다.

결과적으로 지역별로 선호정책이 다르다는 것은 그래프를 통해 알았지만 ’연령대별로 선호정책이 다를까?’라는 질문에서는 상관관계가 없다는 것으로 나타났다.

2. 분석 주제
============

지역별, 연령대별로 선호정책이 다를까?

3. 데이터 선정
==============

출처 : 서울복지실태조사 다운로드 URL : <http://data.si.re.kr/sisurvey2015er17> 특성,구성 : 통계치가 아닌 가공 전의 원시 데이터이다. 크게 6개의 범주로 설문항목이 구성되어있다. (가구 일반사항,경제활동,건강와 의료이용,가족생활 여가 및 사회활동,복지서비스이용, 복지의식 및 욕구) 또한 모집단의 표본 수가 3,000개 이상으로 통계치를 구할 때때 신뢰도 높은 데이터이다.

4. 분석
=======

1. 지역에 따른 선호정책
-----------------------

지역코드를 알아보기 쉽게 코드를 지역이름으로 변경한다. 지역코드 1~5까지가 정상 데이터이기 때문에 **이상치는 NA로 반환**

``` r
library(readxl)     #필요한 패키지 로드
library(dplyr)
library(ggplot2)

total_data <- read_excel("data.xlsx")  #데이터 불러오기
```

``` r
total_data <- total_data %>% 
  mutate(area_name = ifelse(area == 1, "도심권", 
                            ifelse(area == 2 , "동북권", 
                                   ifelse(area == 3, "서북권",
                                          ifelse(area == 4, "서남권",
                                                 ifelse(area == 5, "동남권", NA))))))
```

결측치 확인

``` r
table(is.na(total_data$area_name))
```

    ## 
    ## FALSE 
    ##  3019

별도의 결측치가 없어 정제하지 않고 다음 단계로 넘어간다.

정책코드를 알아보기 쉽게 코드를 정책이름으로 변경한다.

``` r
policy <- data.frame(F11_1 = c(1:12),
           policy_name = c("복지", "경제", "교육", "보건의료_건강분야", 
                           "여가문화예술", "교통", "주택정책", "도시안전", 
                           "환경오염", "공원녹지문제", "도시계획", "기타"))


total_data <- left_join(total_data, policy, by="F11_1")
```

ifelse를 사용하지 않은 이유는 정책의 종류가 12개이기 때문에 코드가 길어져 새로운 데이터프레임을 만들고 기존 total\_data에 변수로 추가 하였다.

``` r
data <- total_data %>% select(area_name,F11_1,policy_name) #필요한 열만 추출
```

``` r
#도심권에서의 선호정책#

data %>% filter(area_name == "도심권") %>% 
  group_by(policy_name) %>% 
  summarise(count=n()) %>% 
  arrange(desc(count))  
```

    ## # A tibble: 11 x 2
    ##    policy_name       count
    ##    <fct>             <int>
    ##  1 경제                124
    ##  2 복지                 99
    ##  3 교육                 74
    ##  4 보건의료_건강분야    36
    ##  5 주택정책             16
    ##  6 환경오염              8
    ##  7 도시안전              7
    ##  8 교통                  6
    ##  9 여가문화예술          4
    ## 10 공원녹지문제          3
    ## 11 도시계획              3

``` r
city <- total_data %>% filter(area_name == "도심권")
ggplot(data = city, aes(x = policy_name)) + geom_bar() +coord_flip()     
```

![](0531_files/figure-markdown_github/unnamed-chunk-7-1.png) 1순위:경제(124), 2순위:복지(99), 3순위:교육(74)

``` r
#동북권에서의 선호정책#

data %>% filter(area_name == "동북권") %>% 
  group_by(policy_name) %>% 
  summarise(count=n()) %>% 
  arrange(desc(count)) 
```

    ## # A tibble: 12 x 2
    ##    policy_name       count
    ##    <fct>             <int>
    ##  1 경제                255
    ##  2 복지                255
    ##  3 보건의료_건강분야    75
    ##  4 교육                 49
    ##  5 주택정책             44
    ##  6 교통                 31
    ##  7 여가문화예술         27
    ##  8 도시안전             17
    ##  9 환경오염              9
    ## 10 공원녹지문제          4
    ## 11 도시계획              3
    ## 12 기타                  1

``` r
E_N <- total_data %>% filter(area_name == "동북권")
ggplot(data = E_N , aes(x = policy_name)) + geom_bar() +
  coord_flip()  
```

![](0531_files/figure-markdown_github/unnamed-chunk-8-1.png) 1순위:경제(255),2순위:복지(255),3순위:보건의료\_건강분야(75)

``` r
#서북권에서의 선호정책#
data %>% filter(area_name == "서북권") %>% 
  group_by(policy_name) %>% 
  summarise(count=n()) %>%
  arrange(desc(count))
```

    ## # A tibble: 11 x 2
    ##    policy_name       count
    ##    <fct>             <int>
    ##  1 복지                183
    ##  2 경제                117
    ##  3 교육                 75
    ##  4 보건의료_건강분야    54
    ##  5 주택정책             23
    ##  6 여가문화예술         14
    ##  7 교통                  6
    ##  8 도시안전              4
    ##  9 환경오염              3
    ## 10 공원녹지문제          2
    ## 11 도시계획              1

``` r
W_N <- total_data %>% filter(area_name == "서북권")
ggplot(data = W_N , aes(x = policy_name)) + geom_bar() +
  coord_flip()
```

![](0531_files/figure-markdown_github/unnamed-chunk-9-1.png) 1순위:복지(183),2순위:경제(117),3순위:교육(75)

``` r
#서남권에서의 선호정책#
data %>% filter(area_name == "서남권") %>% 
  group_by(policy_name) %>% 
  summarise(count=n()) %>%
  arrange(desc(count))   
```

    ## # A tibble: 11 x 2
    ##    policy_name       count
    ##    <fct>             <int>
    ##  1 복지                286
    ##  2 경제                240
    ##  3 보건의료_건강분야    78
    ##  4 주택정책             58
    ##  5 교육                 46
    ##  6 여가문화예술         17
    ##  7 교통                 12
    ##  8 도시안전             12
    ##  9 공원녹지문제          6
    ## 10 환경오염              4
    ## 11 도시계획              3

``` r
W_S <- total_data %>% filter(area_name == "서남권")
ggplot(data = W_S , aes(x = policy_name)) + geom_bar() +
  coord_flip()
```

![](0531_files/figure-markdown_github/unnamed-chunk-10-1.png)

#### 1순위:복지(286),2순위:경제(240),3순위:보건의료\_건강분야(78)

#### 다른 지역에서는 교육정책이 상위권인 반면 서남권 지역에서는 하위권에 머물렀다.

``` r
#동남권의 선호정책#
data %>% filter(area_name == "동남권") %>% 
  group_by(policy_name) %>% 
  summarise(count=n()) %>%
  arrange(desc(count))
```

    ## # A tibble: 11 x 2
    ##    policy_name       count
    ##    <fct>             <int>
    ##  1 경제                206
    ##  2 복지                198
    ##  3 교육                 57
    ##  4 보건의료_건강분야    56
    ##  5 주택정책             49
    ##  6 교통                 16
    ##  7 여가문화예술         16
    ##  8 도시안전             12
    ##  9 환경오염             12
    ## 10 공원녹지문제          2
    ## 11 도시계획              1

``` r
E_S <- total_data %>% filter(area_name == "동남권")
ggplot(data = E_S , aes(x = policy_name)) + geom_bar() +
  coord_flip() 
```

![](0531_files/figure-markdown_github/unnamed-chunk-11-1.png) 1순위 : 경제(206), 2순위:복지(198),3순위:교육(57)

*다른지역의 상위순위에 복지 경제 교육 이 포함되는데 왜 동북권만 교육정책이 5순위 이고 보건정책에 관심이 클까? 보건의료\_건강분야 정책이 3순위인 이유가 노인연령대가 많아서이지 않을까?*

2. 연령대에 따른 선호정책
-------------------------

``` r
#연령대 분류#

total_data <- total_data %>% 
  mutate(age = ifelse(A1_4_1_1>=1990 , "20대", 
                            ifelse(A1_4_1_1>=1980 , "30대", 
                                   ifelse(A1_4_1_1>=1970, "40대",
                                          ifelse(A1_4_1_1>=1960, "50대",
                                                 ifelse(A1_4_1_1>=1956, 
                                                        "노인이아닌60대", 
                                                        ifelse(A1_4_1_1>=1950,
                                                               "노인인60대",
                                                               "70대 이상")))))))

data_2 <- total_data %>% select(area_name,age)
```

``` r
total_data %>% 
  group_by(area_name) %>% 
  summarise(n())
```

    ## # A tibble: 5 x 2
    ##   area_name `n()`
    ##   <chr>     <int>
    ## 1 도심권      380
    ## 2 동남권      625
    ## 3 동북권      770
    ## 4 서남권      762
    ## 5 서북권      482

노인인구 비유을 구하기 위한 지역별 표본수

``` r
data_2 %>% 
  filter(area_name=="도심권") %>% 
  group_by(age) %>% 
  summarise(n()) %>% 
  arrange(desc(age))
```

    ## # A tibble: 7 x 2
    ##   age            `n()`
    ##   <chr>          <int>
    ## 1 노인인60대        68
    ## 2 노인이아닌60대    36
    ## 3 70대 이상         66
    ## 4 50대             100
    ## 5 40대              57
    ## 6 30대              44
    ## 7 20대               9

``` r
data_2 %>% 
  filter(area_name=="동북권") %>% 
  group_by(age) %>% 
  summarise(n()) %>% 
  arrange(desc(age))
```

    ## # A tibble: 7 x 2
    ##   age            `n()`
    ##   <chr>          <int>
    ## 1 노인인60대       105
    ## 2 노인이아닌60대    81
    ## 3 70대 이상        131
    ## 4 50대             229
    ## 5 40대             132
    ## 6 30대              70
    ## 7 20대              22

``` r
data_2 %>% 
  filter(area_name=="서북권") %>% 
  group_by(age) %>% 
  summarise(n()) %>% 
  arrange(desc(age))
```

    ## # A tibble: 7 x 2
    ##   age            `n()`
    ##   <chr>          <int>
    ## 1 노인인60대        78
    ## 2 노인이아닌60대    61
    ## 3 70대 이상         59
    ## 4 50대             134
    ## 5 40대              82
    ## 6 30대              59
    ## 7 20대               9

``` r
data_2 %>% 
  filter(area_name=="서남권") %>% 
  group_by(age) %>% 
  summarise(n()) %>% 
  arrange(desc(age))
```

    ## # A tibble: 7 x 2
    ##   age            `n()`
    ##   <chr>          <int>
    ## 1 노인인60대       115
    ## 2 노인이아닌60대    76
    ## 3 70대 이상        110
    ## 4 50대             237
    ## 5 40대             108
    ## 6 30대             107
    ## 7 20대               9

``` r
data_2 %>% 
  filter(area_name=="동남권") %>% 
  group_by(age) %>% 
  summarise(n()) %>% 
  arrange(desc(age))
```

    ## # A tibble: 7 x 2
    ##   age            `n()`
    ##   <chr>          <int>
    ## 1 노인인60대        85
    ## 2 노인이아닌60대    53
    ## 3 70대 이상         82
    ## 4 50대             193
    ## 5 40대             114
    ## 6 30대              87
    ## 7 20대              11

``` r
ratio <- data.frame("지역"= c("도심권", "동남권", "동북권", "서북권" ,"서남권"),
                    "노인설문답변자비율"= c("35%", "27%", "31%", "28%", "30%"))
ratio
```

    ##     지역 노인설문답변자비율
    ## 1 도심권                35%
    ## 2 동남권                27%
    ## 3 동북권                31%
    ## 4 서북권                28%
    ## 5 서남권                30%

### 결론 : 지역별 선호정책 분석결과는 경제,복지정책은 모든 지역에서 1순위,2순였고 나머지 10개 정책 중에서 순위가 지역별로 달랐다. 그중에서 “서남권”지역이 뚜렷하게 다른 지역과 차이를 보였고 보건의료\_건강분야 정책을 선호하였다. 이 결과로 나는 서남권 지역에 설문 답변자 중 노인비율이 높을 것으로 생각하고 연령대별 선호 정책을 분석하였지만, 연령대별로 선호정책이 다르다고 볼 수 없었다.

5. 논의
=======

한계점과 비판점 :

모집단(서울시)에서 추출한 표본 수(설문응답자수)는 3,019명으로 통계량을 냈을 때 신뢰할만한 수치이지만 나는 설문응답자에서 다시 지역별로 추출하고 한 번 더 연령대별로 추출하였기 때문에 표본 수가 적었다. 그래서 신뢰도가 높은 결과를 얻지 못하였고 선호정책에서 경제와 복지 정책이 눈에 띄게 큰 값을 차지하기 때문에 나머지 정책들을 가지고 분석을 하기에는 한계가 있었다.

추후 분석 방향
==============

지역별로 선호정책이 다르다는 결과가 나왔지만, 연령대별 선호정책이 다르다는 결과는 얻지 못하였다. 추후 다시 분석한다면 지역별 복지, 보건의료, 교육 시설을 조사하여 시설 분포에 따라 선호 정책을 조사할 것이다.
