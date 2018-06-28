Confluence の table で合計を出す
=====

js を confluence の html マクロで埋め込む

```javascript
<script type="text/javascript" >
AJS.toInit(function ($) {

    var list = AJS.$('table.confluenceTable tbody th:contains("#sum")');
    list.each(function(index, item) {
        var elem = AJS.$(item);
        var parentRow = elem.parent();
        var parentTbody = parentRow.parent();
        var column = parentRow.find('th').index(elem);
        var sum = 0;
        var totalColumns = parentRow.find('th').length;

        parentTbody.find('tr').each(function(index, row) {
            var elem = AJS.$(row);
            var td = elem.find('td');
            if (td.length > 0) {
                var value = td.eq(column).text();
                if (!/\u00A0/.test(value)) {
                    sum += 1;
                }
            }
        });
        elem.text(sum);
    });
});
</script>
```

confluence table で合計を出したい列を見出し行(`th`) にして、`#sum` にする

## 参考

- https://gist.github.com/jkubos/b1217eb456b68dce79dc
