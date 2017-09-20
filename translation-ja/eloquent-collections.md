# Eloquent：コレクション

- [イントロダクション](#introduction)
- [使用できるメソッド](#available-methods)
- [カスタムコレクション](#custom-collections)

<a name="introduction"></a>
## イントロダクション

`get`メソッドであれリレーションによるものであれ、Eloquentが複数のレコードをリターンする場合`Illuminate\Database\Eloquent\Collection`オブジェクトが返されます。EloquentコレクションオブジェクトはLaravelの[ベースコレクション](collections)を継承しているので、Eloquentモデルの裏にある配列をスムーズに操作するために継承した多くのメソッドがもちろん使用できます。

当然ながら全コレクションはイテレーターとしても動作し、シンプルなPHP配列のようにループで取り扱うことができます。

    $users = App\User::where('active', 1)->get();

    foreach ($users as $user) {
        echo $user->name;
    }

しかし、コレクションは配列よりもパワフルで直感的なインターフェイスを使ったメソッドチェーンにより、マッピングや要素の省略操作を行うことができます。例としてアクティブでないモデルを削除し、残ったユーザーのファーストネームを集めてみましょう。

    $users = App\User::where('active', 1)->get();

    $names = $users->reject(function ($user) {
        return $user->active === false;
    })
    ->map(function ($user) {
        return $user->name;
    });

> {note} ほとんどのEloquentコレクションは新しいEloquentコレクションのインスタンスを返しますが、`pluck`、`keys`、`zip`、`collapse`、`flatten`、`flip`メソッドは[ベースのコレクション](collections)インスタンスを返します。Eloquentモデルをまったく含まないコレクションを返す`map`操作のような場合、自動的にベースコレクションへキャストされます。

<a name="available-methods"></a>
## 使用できるメソッド

### ベースのコレクション

全Eloquentコレクションはベースの[Laravelコレクション](collections)オブジェクトを拡張しており、そのためにベースコレクションクラスが提供しているパワフルなメソッドを全部継承しています。

<style>
    #collection-method-list > p {
        column-count: 3; -moz-column-count: 3; -webkit-column-count: 3;
        column-gap: 2em; -moz-column-gap: 2em; -webkit-column-gap: 2em;
    }

    #collection-method-list a {
        display: block;
    }
</style>

<div id="collection-method-list" markdown="1">

[all](collections#method-all)
[average](collections#method-average)
[avg](collections#method-avg)
[chunk](collections#method-chunk)
[collapse](collections#method-collapse)
[combine](collections#method-combine)
[concat](collections#method-concat)
[contains](collections#method-contains)
[containsStrict](collections#method-containsstrict)
[count](collections#method-count)
[crossJoin](collections#method-crossjoin)
[dd](collections#method-dd)
[diff](collections#method-diff)
[diffKeys](collections#method-diffkeys)
[dump](collections#method-dump)
[each](collections#method-each)
[eachSpread](collections#method-eachspread)
[every](collections#method-every)
[except](collections#method-except)
[filter](collections#method-filter)
[first](collections#method-first)
[flatMap](collections#method-flatmap)
[flatten](collections#method-flatten)
[flip](collections#method-flip)
[forget](collections#method-forget)
[forPage](collections#method-forpage)
[get](collections#method-get)
[groupBy](collections#method-groupby)
[has](collections#method-has)
[implode](collections#method-implode)
[intersect](collections#method-intersect)
[isEmpty](collections#method-isempty)
[isNotEmpty](collections#method-isnotempty)
[keyBy](collections#method-keyby)
[keys](collections#method-keys)
[last](collections#method-last)
[map](collections#method-map)
[mapInto](collections#method-mapinto)
[mapSpread](collections#method-mapspread)
[mapToGroups](collections#method-maptogroups)
[mapWithKeys](collections#method-mapwithkeys)
[max](collections#method-max)
[median](collections#method-median)
[merge](collections#method-merge)
[min](collections#method-min)
[mode](collections#method-mode)
[nth](collections#method-nth)
[only](collections#method-only)
[pad](collections#method-pad)
[partition](collections#method-partition)
[pipe](collections#method-pipe)
[pluck](collections#method-pluck)
[pop](collections#method-pop)
[prepend](collections#method-prepend)
[pull](collections#method-pull)
[push](collections#method-push)
[put](collections#method-put)
[random](collections#method-random)
[reduce](collections#method-reduce)
[reject](collections#method-reject)
[reverse](collections#method-reverse)
[search](collections#method-search)
[shift](collections#method-shift)
[shuffle](collections#method-shuffle)
[slice](collections#method-slice)
[sort](collections#method-sort)
[sortBy](collections#method-sortby)
[sortByDesc](collections#method-sortbydesc)
[splice](collections#method-splice)
[split](collections#method-split)
[sum](collections#method-sum)
[take](collections#method-take)
[tap](collections#method-tap)
[toArray](collections#method-toarray)
[toJson](collections#method-tojson)
[transform](collections#method-transform)
[union](collections#method-union)
[unique](collections#method-unique)
[uniqueStrict](collections#method-uniquestrict)
[unless](collections#method-unless)
[values](collections#method-values)
[when](collections#method-when)
[where](collections#method-where)
[whereStrict](collections#method-wherestrict)
[whereIn](collections#method-wherein)
[whereInStrict](collections#method-whereinstrict)
[whereNotIn](collections#method-wherenotin)
[whereNotInStrict](collections#method-wherenotinstrict)
[zip](collections#method-zip)

</div>

<a name="custom-collections"></a>
## カスタムコレクション

自分で拡張したメソッドを含むカスタム「コレクション」オブジェクトを使いたい場合は、モデルの`newCollection`メソッドをオーバーライドしてください。

    <?php

    namespace App;

    use App\CustomCollection;
    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * 新しいEloqunetコレクションインスタンスの生成
         *
         * @param  array  $models
         * @return \Illuminate\Database\Eloquent\Collection
         */
        public function newCollection(array $models = [])
        {
            return new CustomCollection($models);
        }
    }

`newCollection`メソッドを定義すれば、Eloquentがそのモデルの「コレクション」インスタンスを返す場合にいつでもカスタムコレクションのインスタンスを受け取れます。アプリケーションの全モデルでカスタムコレクションを使いたい場合は、全モデルが拡張しているモデルのベースクラスで`newCollection`メソッドをオーバーライドしてください。
