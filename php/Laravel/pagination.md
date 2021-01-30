# Pagination

## simplePaginate

- Laravel で pagination を実現するためには DB クラスまたは Model クラスで `paginate`や`simplePaginate` メソッドを呼べばいい
  - 2つの違いは Laravel で view まで作る時に、ページネーションにページ番号が付くか付かないか
- `where` や `orderBy` の後に呼ぶこともできる

```php
DB::table('table')->simplePaginate(5);
Model::where(...)->simplePaginate(5);
```

- 他にもページネーションのviewを変更するためのテンプレートも作成できる
