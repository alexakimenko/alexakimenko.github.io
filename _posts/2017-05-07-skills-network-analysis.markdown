---
layout:     post
title:      Анализ взаимосвязи навыков для профессии программиста с помощью графов в R 
date:       2017-05-07 15:31:19
summary:    Данное исследование помогает с помощью методов анализа данных ответить на вопрос как взаимосвязаны навыки, какие есть специализации, какие навыки более популярны, а какие навыки следует изучить следующим.
categories: research
---

Интересно, но такая область как профессиональное развитие остается немного в стороне от шума из-за data science. Стартапы в сфере HRtech только начинают наращивать обороты и увеличивать свою долю, замещая традиционный подход в сфере работы с профессионалами. 

Сфера HRtech очень разнообразна и включает в себя автоматизацию найма сотрудников, развитие и коачинг, автоматизацию внутренних HR процедур, отслеживание рыночных зарплат, трекинг кандидатов, сотрудников и многое другое. Данное исследование помогает с помощью методов анализа данных ответить на вопрос как взаимосвязаны навыки, какие есть специализации, какие навыки более популярны, а какие навыки следует изучить следующим.




### Постановка задачи и входные данные

Изначально не хотелось разделять навыки по какой-то известной классификации. Например, через среднюю зарплату можно было выделить «дорогие» и «дешевые» навыки. Нам хотелось выделить «специализации» основываясь на математике и статистике. Поэтому в данном исследовании встала задача unsepervised learning для объединения навыков в группы. И первой профессией мы выбрали программиста.
 
Для анализа мы брали данные с портала [Работа в России](http://trudvsem.ru) доступные на [data.gov.ru](http://data.gov.ru/opendata/7710538364-vacansii). Здесь представлены все вакансии, доступные на портале с описанием, зарплатой, регионом и прочими деталями. Далее мы распарсили описания и выделили из них навыки. Это отдельное исследование и этой статьей не покрывается. Однако уже размеченные данные можно также взять с API [hh.ru](http://hh.ru). 
 
Таким образом исходные данные представлены матрицей со значениями 0/1, в которой `X` - навыки, а объекты - вакансии. Всего 164 признака и 841 объект:




### Подбор метода поиска групп навыков

При выборе методы я основывался на предположении, что одна вакансия может иметь несколько специализаций. А также исходил из допущения, что конкретный навык может относиться только к одной специализации. Раскрою карты, это допущение требовалось для работы алгоритмов, использующих результаты данного исследования.

Решая задачу в лоб, можно предположить, что если одна группа навыков встречается у одной группы вакансий, а другая группа навыков - у другой группы вакансий, то это группа навыков и есть специализация. И можно разделить навыки с помощью метрических методов (k-means и модификации). Но проблема как раз была в том что, одна вакансия могла иметь несколько специализаций и в итоге, как бы мы не крутили алгоритм, он относил 90% навыков к одному кластеру и еще с десяток кластеров по 1-2 навыка. Поразмыслив, решил переписать k-means под задачу и вместо классического евклидово расстояния выбрал меру смежности навыков, то есть частоту встречаемости двух навыков:

```r
grid<-as.data.table(expand.grid(skill_1=c(1:ncol(skills_clust)),skill_2=c(1:ncol(skills_clust))))
for (i in c(1:nrow(grid))){
  grid$value[i]<-sum(skills_clust[,grid$skill_1[i],with=F]*skills_clust[,grid$skill_2[i],with=F])
}
```

Но, к счастью, вовремя пришла идея представить задачу как задачу поиска сообществ в графах. И пришлось вспоминать теорию графов благополучно забытую после второго курса университета. 




### Построение и анализ графа навыков

Для того чтобы построить граф мы воспользуемся пакетом `igraph` (такой же есть и в python) и прежде все мы создадим матрицу смежности из таблицы, которую мы начали считать для k-means (`grid`). Затем отнормируем смежность навыков в диапазоне от 0 до 1: 

```r
grid_clean<-grid[grid$value>0 & (grid$skill_1 != grid$skill_2),]
grid_cast<-dcast(grid_clean,skill_1~skill_2)
grid_cast[,1]<-NULL
grid_cast_norm<-grid_cast/colSums(grid_cast,na.rm=T)
grid_cast_norm[is.na(grid_cast_norm)]<-0
grid_cast_norm<-as.matrix(grid_cast_norm)
```

Смежность навыка `i` и навыка `j` мы нормируем как долю от общей встречаемости навыка `i`. Изначально я нормировало как долю от максимальной встречаемости всех навыков, но затем перешел к этой формуле. Идея в том, что, к примеру, навык `i` встречается с навыком `j` 10 раз, и больше ни с каким другим навыком не встречается. Можно предположить, что такая связь будет более весомой (например 100%), чем если бы мы смотрели эту встречаемость от максимальной в данной матрице (например, 100 - 10%).

Затем мы создаем ненаправленный взвешенный граф:

```r
skills_graph<-graph_from_adjacency_matrix(grid_cast_norm, mode = "undirected",weighted=T)
plot(skills_graph, vertex.size=5)
```

![skills_no_cluster](https://raw.githubusercontent.com/alexakimenko/alexakimenko.github.io/master/images/skills_no_cluster.png "skills no cluster")

`igraph` нам также позволяет посчитать основы статистики по вершинам:

```r
closeness(skills_graph) # Центральность вершины на основании расстояния до других вершин
betweenness(skills_graph) # Количество самых коротких путей, проходящих через вершину
degree(skills_graph) # Количество связанных вершин с данной вершиной
```

Затем можем вывести лист смежности для каждого навыка. Этот лист в дальнейшем может стать основой рекомендательной системы для выбора новых навыков:

```r
get.adjlist(skills_graph)
```




### Выделение сообществ методом Multilevel

На тему поиска сообществ в графах мне попалась отличная [работа Славнова Константина](http://www.machinelearning.ru/wiki/images/6/60/2015_417_SlavnovKA.pdf). Эта статья рассладывает по полочкам основные метрики качества выделения сообществ, методы выделения сообществ и аггрегирования результатов работы этих методов.

Когда истинное разбиение на сообщества не известно для оценки качества используется значение функционала модулярности (modularity). Это самая популярная и общепризнанная мера качества для данной задачи. Функционал был предложен Ньюманом и Гирваном в ходе разработки алгоритма кластеризации вершин графа.

Для оптимизации данного функционала чаще всего используется алгоритм Multilevel, предложенный в [статье](https://arxiv.org/abs/0803.0476). Во-первых, из-за хорошего качества оптимизации, во-вторых, из-за скорости (в данной задаче это не требовалось, но все же), в-третьих, алгоритм достаточно интуитивно понятен.

В пакте `igraph` этот алгоритм реализуется функцией `cluster_louvain()`:

```r
fit_cluster<-cluster_louvain(skills_graph)
V(skills_graph)$group <- fit_cluster$membership
```




### Результаты


![skills_cluster](https://raw.githubusercontent.com/alexakimenko/alexakimenko.github.io/master/images/skills_cluster.png "skills cluster")

Как мы видим, удалось выявить 11 специализаций. Все специализации (за исключением выбросов) тесно взаимосвязаны. Это объясняется нашим основным предположением, что одна вакансия может иметь несколько специализаций. 

Также есть пару навыков, которые логически не вписываются в специализацию. Так, например, знание языка Swift и разработка мобильных приложений на iOS попали в кластер «Передача данных». Объясняется это просто: шум в данных из-за субъективного понимания значения отдельных навыков и нехватка размеченных данных.


<div id="htmlwidget_container">
  <div id="htmlwidget-fd9380bdcec20d98c374" style="width:960px;height:500px;" class="visNetwork html-widget html-widget-static-bound"><select class="dropdown" id="nodeSelecthtmlwidget-fd9380bdcec20d98c374" style="display: none;"></select><hr style="height:0px; visibility:hidden; margin-bottom:-1px;"><select class="dropdown" id="selectedByhtmlwidget-fd9380bdcec20d98c374" style="display: none;"></select><div id="maindivhtmlwidget-fd9380bdcec20d98c374" style="height:95%"><div id="graphhtmlwidget-fd9380bdcec20d98c374" style="float:right; width:100%;height:100%"><div class="vis-network" tabindex="900" style="position: relative; overflow: hidden; touch-action: pan-y; user-select: none; -webkit-user-drag: none; -webkit-tap-highlight-color: rgba(0, 0, 0, 0); width: 100%; height: 100%;"><canvas width="960" height="475" style="position: relative; touch-action: none; user-select: none; -webkit-user-drag: none; -webkit-tap-highlight-color: rgba(0, 0, 0, 0); width: 100%; height: 100%;"></canvas><div class="vis-network-tooltip" style="left: 5px; top: 151px; visibility: hidden;"><p>Навык:наличие водительских прав<br>Кластер:Обслуживание серверов и сетей<br>Количество соседей:3</p></div></div><div style="position: fixed; visibility: hidden; padding: 5px; white-space: nowrap; font-family: verdana; font-size: 14px; background-color: rgb(245, 244, 237); border-radius: 3px; border: 1px solid rgb(128, 128, 116); box-shadow: rgba(0, 0, 0, 0.2) 3px 3px 10px; top: 239px; left: 702px;"><p>Навык:наличие водительских прав<br>Кластер:Обслуживание серверов и сетей<br>Количество соседей:3</p></div></div></div></div>
</div>




<div id="disqus_thread"></div>
<script>

/**
 *  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
 *  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables */
/*
var disqus_config = function () {
    this.page.url = PAGE_URL;  // Replace PAGE_URL with your page's canonical URL variable
    this.page.identifier = PAGE_IDENTIFIER; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
};
*/
(function() { // DON'T EDIT BELOW THIS LINE
    var d = document, s = d.createElement('script');
    s.src = '//https-alexakimenko-github-io.disqus.com/embed.js';
    s.setAttribute('data-timestamp', +new Date());
    (d.head || d.body).appendChild(s);
})();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
