# api の一括取得数

gitlab api で帰ってくる項目数はdefault で 20 に制限されている

https://docs.gitlab.com/ee/api/#pagination
>  Number of items to list per page (default: 20, max: 100)

一度に 20 以上返してもらうためには `&per_page=100` (1-100) をつける。  
それでも足りないときはページ送り `&page={1..X}` を行う
