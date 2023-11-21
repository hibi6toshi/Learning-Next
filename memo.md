# Chapter1

- `/app`: アプリケーションのすべてのルート、コンポーネント、ロジックが含まれています。主にここから作業します。
- `/app/lib`: 再利用可能なユーティリティ関数やデータ取得関数など、アプリケーションで使用される関数が含まれます。
- `/app/ui`: カード、テーブル、フォームなど、アプリケーションのすべての UI コンポーネントが含まれます。時間を節約するために、これらのコンポーネントは事前にスタイル設定されています。
- `/public`: 画像など、アプリケーションのすべての静的アセットが含まれます。
- `/script`: 後の章でデータベースにデータを設定するために使用するシード スクリプトが含まれています。
- 設定ファイル`next.config.js`: アプリケーションのルートなどに設定ファイルがあることにも気づきます。これらのファイルのほとんどは、`create-next-app` を使用して新しいプロジェクトを開始するときに作成され、事前設定されます。このコースではそれらを変更する必要はありません。

# Chapter2

`global.css`をアプリケーション内の任意のコンポーネントにインポートできますが、通常はそれを最上位コンポーネントに追加することをお勧めします。Next.js では、これはroot layoutです(これについては後で詳しく説明します)。

`/app/layout.tsx`ファイルに移動して`global.css`をインポートすることで、グローバルスタイルをアプリケーションに追加します。

## CSS module を追加する

`app/ui/`にhome.module.css を追加し、そこにcssの設定を追加。
`app/page.tsx`でファイルをインポートして、スタイルを適用する。

**CSSモジュールの利点**
CSSクラスをデフォルトでコンポーネントにローカルにスコープする方法を提供し、モジュール性を向上させ、スタイルの競合のリスクを軽減します。

## clsx ライブラリをクラスネームを切り替えるために追加する

clsx はクラス名を簡単に切り替えることができるライブラリです。
次のように条件付きでクラスを適用できます。

```TypeScript: /app/ui/invoices/status.tsx
import clsx from 'clsx';

export default function InvoiceStatus({ status }: { status: string }) {
  return (
    <span
      className={clsx(
        'inline-flex items-center rounded-full px-2 py-1 text-sm',
        {
          'bg-gray-100 text-gray-500': status === 'pending',
          'bg-green-500 text-white': status === 'paid',
        },
      )}
    >
    // ...
)}
```

# Chapter3

Next.jsは、`next/font`モジュールを使用するしたときアプリケーション内のフォントを自動的で最適化します。ビルド時にフォントファイルをダウンロードし、他の静的アセットとともにホストします。これは、ユーザーがアプリケーションにアクセスしたときに、パフォーマンスに影響を与えるようなフォントに対する追加のネットワーク要求がないことを意味します。

## プライマリフォントの追加

`/app/ui`フォルダー内に`fonts.ts`という名前の新しいファイルを作成します。このファイルを使用して、アプリケーション全体で使用されるフォントを保存します。

`next/font/google`モジュールから`Inter`フォントをインポートします。これがプライマリフォントになります。次に、どのサブセットかを指定しますロードしたいと考えています。この場合、`latin`

## 画像の最適化

Next.jsは、画像などの静的アセットを最上位フォルダー`/public`の下に提供できます。`/public`内部のファイルはアプリケーションで参照できます。

通常の HTML では、次のように画像を追加します。

```HTML
<img
  src="/hero.png"
  alt="Screenshots of the dashboard project showing desktop version"
/>
```

ただし、これは手動で以下を行う必要があることを意味します。

- 画像がさまざまな画面サイズで応答することを確認します。
- さまざまなデバイスの画像サイズを指定します。
- 画像の読み込み時にレイアウトがずれるのを防ぎます。
- ユーザーのビューポートの外にある画像を遅延読み込みします。

これらの最適化を手動で実装する代わりに、`next/image`コンポーネントを使用して画像を自動的に最適化できます。

## コンポーネント<Image>

コンポーネント<Image>は HTML<img>タグの拡張であり、次のような自動画像最適化機能が付属しています。

- 画像読み込み時に自動的にレイアウトがずれるのを防ぎます。
- 小さなビューポートを備えたデバイスに大きな画像が送信されるのを避けるために、画像のサイズを変更します。
- デフォルトで画像を遅延読み込みします (画像はビューポートに入るときに読み込まれます)。
- WebPなどの最新の形式で画像を提供するとAVIFブラウザがサポートしている場合。

# Chapter4

## ネストされたルーティング

Next.js はファイル システム ルーティングを使用し、フォルダーを使用してネストされたルートを作成します。各フォルダーは、URL セグメントにマップされるルートセグメントを表します。
![nested_routing](folders-to-url-segments.avif)

`layout.tsx`および`page.tsx`ファイルを使用して、ルートごとに個別のUIを作成できます。

`page.tsx`はReact ンポーネントをエクスポートする特別な Next.js ファイルであり、ルートにアクセスできるようにするために必要です。アプリケーションには、すでにページファイル`/app/page.tsx`があります。これは、ルートに関連付けられたホームページです。

## ダッシュボードレイアウトの作成

ダッシュボードには、複数のページにわたって共有されるある種のナビゲーションがあります。Next.js では、特別な`layout.tsx`ファイルを使用して、複数のページ間で共有される UI を作成できます。

`<SideNav />`まず、コンポーネントをレイアウトにインポートします。このファイルにインポートするコンポーネントはすべてレイアウトの一部になります。

`<Layout />`コンポーネントはchildren propを受け取ります。このchildrenはページまたは別のレイアウトのいずれかになります。`/dashboard`内部のページは<Layout />に自動的にネストされます。
![nested_layout](shared-layout.avif)

Next.js でレイアウトを使用する利点の 1つは、ナビゲーション時にページ コンポーネントのみが更新され、レイアウトは再レンダリングされないことです。これは部分レンダリングと呼ばれます。
![partial-rendering](partial-rendering-dashboard.avif)

# Chapter5

## なぜナビゲーションを最適化するのでしょうか?

ページ間をリンクするには、従来は<a>HTML 要素を使用していました。現時点では、サイドバーのリンクには<a>要素が使用されていますが、ブラウザーでホーム、請求書、顧客のページ間を移動するとどうなるかに注目してください。

各ページ ナビゲーションでページ全体が更新されます。

## コンポーネント<Link>

Next.js では、<Link />コンポーネントを使用してアプリケーション内のページ間をリンクできます。JavaScriptを使用してクライアント側のナビゲーション<Link>を実行できるようになります。

`<Link />`コンポーネントを使用するには、 `/app/ui/dashboard/nav-links.tsx`を開き、Linkコンポーネントを`next/link`からインポートします。次に、<a>タグを<Link>に置き換えます。

## 自動コード分割とプリフェッチ

ナビゲーション エクスペリエンスを向上させるために、Next.js はアプリケーションをルート セグメントごとに自動的にコード分割します。これは従来の React SPAとは異なります、ブラウザーは初期ロード時にすべてのアプリケーション コードをロードします。

コードをルートごとに分割すると、ページが分離されます。特定のページでエラーが発生しても、アプリケーションの残りの部分は引き続き動作します。

さらに、運用環境では、<Link>ブラウザのビューポートにコンポーネントが表示されるたびに、Next.js がリンクされたルートのコードをバックグラウンドで自動的にプリフェッチします。ユーザーがリンクをクリックするまでに、宛先ページのコードはすでにバックグラウンドで読み込まれており、これによりページがほぼ瞬時に遷移します。

## アクティブなリンクを表示する

`usePathname()`はフックなので、`nav-links.tsx`をクライアントコンポーネントに変える必要があります。React の`"use client"`ディレクティブをファイルの先頭に追加し、`next/navigation`を`usePathname()`からインポートします。

# Chapter7

## サーバーコンポーネントを使用したデータのフェッチ

デフォルトでは、Next.js アプリケーションは**React Server Components**を使用します。サーバーコンポーネントを使用したデータの取得は比較的新しいアプローチであり、サーバーコンポーネントを使用することにはいくつかの利点があります。

- サーバーコンポーネントは Promise をサポートし、データのフェッチなどの非同期タスクに対するよりシンプルなソリューションを提供します。`useEffect``useState`やデータ取得ライブラリ`async/await`に手を伸ばさずに構文を使用できます。
- サーバーコンポーネン トはサーバー上で実行されるため、高価なデータのフェッチとロジックをサーバー上に保持し、結果のみをクライアントに送信できます。
- 前述したように、サーバーコンポーネントはサーバー上で実行されるため、追加のAPIレイヤーを使用せずにデータベースに直接クエリを実行できます。

## SQLの使用

Vercel Postgres SDK はSQLインジェクションに対する保護を提供します。

`/app/lib/data.ts`に移動すると、@vercel/postgresからsql機能 をインポートしていることがわかります。この関数を使用すると、データベースにクエリを実行できます。

```javascript:
import { sql } from '@vercel/postgres';

// ...
```

任意のサーバー コンポーネント内でsqlを呼び出すことができます。ただし、コンポーネントをより簡単にナビゲートできるように、すべてのデータ クエリを`data.ts`ファイル内に保持しており、それらをコンポーネントにインポートできます。

## ダッシュボード概要ページのデータの取得

ただし...注意しなければならないことが 2 つあります。

- データ リクエストは意図せずに相互にブロックし、リクエストウォーターフォールを作成します。
- デフォルトでは、Next.js はパフォーマンスを向上させるためにルートを事前レンダリングします。これは静的レンダリングと呼ばれます。したがって、データが変更されても、ダッシュボードには反映されません。

## リクエスト ウォーターフォールとは何ですか?

「ウォーターフォール」とは、前のリクエストの完了に依存する一連のネットワークリクエストを指します。データフェッチの場合、各リクエストは、前のリクエストがデータを返した後にのみ開始できます。
![](sequential-parallel-data-fetching.avif)

たとえば、実行を開始するfetchRevenue()前に実行を待つ必要があるfetchLatestInvoices()などです。

```javascript:app/dashboard/page.tsx
const revenue = await fetchRevenue();
const latestInvoices = await fetchLatestInvoices(); // wait for fetchRevenue() to finish
const {
  numberOfInvoices,
  numberOfCustomers,
  totalPaidInvoices,
  totalPendingInvoices,
} = await fetchCardData(); // wait for fetchLatestInvoices() to finish
```

## 並列データフェッチ

ウォーターフォールを回避する一般的な方法は、すべてのデータ要求を同時に、つまり並行して開始することです。
JavaScriptでは、すべての Promise を同時に開始するために`Promise.all()`または`Promise.allSettled()`が使えます。たとえば、 `data.ts`では、次の`Promise.all()` `fetchCardData()`関数を使用しています。

````javascript:app/lib/data.ts
export async function fetchCardData() {
  try {
    const invoiceCountPromise = sql`SELECT COUNT(*) FROM invoices`;
    const customerCountPromise = sql`SELECT COUNT(*) FROM customers`;
    const invoiceStatusPromise = sql`SELECT
         SUM(CASE WHEN status = 'paid' THEN amount ELSE 0 END) AS "paid",
         SUM(CASE WHEN status = 'pending' THEN amount ELSE 0 END) AS "pending"
         FROM invoices`;

    const data = await Promise.all([
      invoiceCountPromise,
      customerCountPromise,
      invoiceStatusPromise,
    ]);
    // ...
  }
}```
````

このパターンを使用すると、次のことが可能になります。

- すべてのデータフェッチの実行を同時に開始すると、パフォーマンスの向上につながる可能性があります。
- 任意のライブラリまたはフレームワークに適用できるネイティブ JavaScript パターンを使用します。

ただし、この JavaScriptパターンのみに依存することには欠点が1つあります。1つのデータクエストが他のすべてのデータリクエストよりも遅い場合はどうなるでしょうか。

#　Chapter8

## 静的レンダリングとは何ですか?

静的レンダリングでは、データのフェッチとレンダリングがビルド時(デプロイ時)または再検証中にサーバー上で行われます。結果はコンテンツ配信ネットワーク(CDN)に配布およびキャッシュできます。
![](static-site-generation.avif)
ユーザーがアプリケーションにアクセスするたびに、キャッシュされた結果が提供されます。静的レンダリングにはいくつかの利点があります。

- Webサイトの高速化: 事前にレンダリングされたコンテンツをキャッシュし、グローバルに配布できます。これにより、世界中のユーザーがより迅速かつ確実に Web サイトのコンテンツにアクセスできるようになります。
- サーバー負荷の軽減: コンテンツがキャッシュされるため、サーバーはユーザーのリクエストごとにコンテンツを動的に生成する必要がありません。
- SEO: 事前にレンダリングされたコンテンツは、ページの読み込み時にすでに利用可能であるため、検索エンジンのクローラーにとってインデックス付けが容易です。これにより、検索エンジンのランキングが向上する可能性があります。

静的レンダリングは、データのないUI、または静的なブログ投稿や製品ページなど、ユーザー間で共有されるデータのUIに役立ちます。定期的に更新されるパーソナライズされたデータを含むダッシュボードには適さない可能性があります。

## ダイナミックレンダリングとは何ですか?

動的レンダリングでは、リクエスト時(ユーザーがページにアクセスしたとき)に、各ユーザーのコンテンツがサーバー上でレンダリングされます。動的レンダリングにはいくつかの利点があります。

- リアルタイムデータ: 動的レンダリングにより、アプリケーションはリアルタイムまたは頻繁に更新されるデータを表示できます。これは、データが頻繁に変更されるアプリケーションに最適です。
- ユーザー固有のコンテンツ: ダッシュボードやユーザー プロファイルなどのパーソナライズされたコンテンツを提供し、ユーザー インタラクションに基づいてデータを更新することが簡単になります。
- リクエスト時の情報: 動的レンダリングを使用すると、Cookie や URL 検索パラメータなど、リクエスト時にのみ知ることができる情報にアクセスできます。

デフォルトでは、`@vercel/postgres`独自のキャッシュセマンティクスを設定しません。これにより、フレームワークは独自の静的および動的動作を設定できるようになります。

サーバーコンポーネント内で呼び出される Next.js API`unstable_noStore`またはデータ取得関数を使用して、静的レンダリングをオプトアウトできます。これを追加しましょう。

## 遅いデータフェッチのシミュレーション

遅いデータフェッチをシミュレートするために人為的に 3 秒の遅延を追加しています。その結果、データがフェッチされている間、ページ全体がブロックされることになります。

動的レンダリングを使用すると、**アプリケーションは最も遅いデータフェッチと同じ速度でしか動作しません。**

# Chapter9

## ストリーミング

前の章では、ダッシュボード ページを動的にしましたが、データのフェッチが遅いことがアプリケーションのパフォーマンスにどのような影響を与える可能性があるかについて説明しました。データ要求が遅い場合にユーザー エクスペリエンスを向上させる方法を見てみましょう。

## ストリーミングとは何ですか?

ストリーミングは、ルートをより小さな「チャンク」に分割し、準備が整ったらサーバーからクライアントに段階的にストリーミングできるデータ転送技術です。
![](server-rendering-with-streaming.avif)

ストリーミングすることで、遅いデータ要求によってページ全体がブロックされるのを防ぐことができます。これにより、ユーザーは、UI がユーザーに表示される前にすべてのデータが読み込まれるのを待たずに、ページの一部を表示して操作できるようになります。
![](server-rendering-with-streaming-chart.avif)

各コンポーネントはチャンクとみなすことができるため、ストリーミングは React のコンポーネント モデルとうまく連携します。

Next.js でストリーミングを実装する方法は 2 つあります。

- ページレベルで、`loading.tsx`ファイルとともに。
- 特定のコンポーネントの場合は、`<Suspense>`。

## ページ全体をストリーミングするloading.tsx

`/app/dashboard`フォルダー内に、次の名前の新しいファイルを作成します`loading.tsx`。

いくつかのことが起こっています。

1. `loading.tsx`は Suspense 上に構築された特別な Next.js ファイルで、ページコンテンツの読み込み中に代替として表示するフォールバック UI を作成できます。
2. `<Sidebar>`は静的であるため、`<Sidebar>`はすぐに表示されます。ユーザーは、動的コンテンツの読み込み中に対話できます。
3. ユーザーは、ページの読み込みが完了するまで待つ必要はありません (これを中断可能なナビゲーションと呼びます)。

## ロードスケルトンの追加

読み込みスケルトンは、UIの簡略化されたバージョンです。多くの Webサイトは、コンテンツが読み込まれていることをユーザーに示すプレースホルダー (またはフォールバック) としてこれらを使用します。埋め込む UIは`loading.tsx`静的ファイルの一部として埋め込まれ、最初に送信されます。その後、残りの動的コンテンツがサーバーからクライアントにストリーミングされます。

## ルートグループでのスケルトンの読み込みバグを修正

現時点では、読み込み中のスケルトンは請求書と顧客ページにも適用されます。

`loading.tsx`はファイルシステムよりも上位のレベルであるため、`/invoices/page.tsx` `/customers/page.tsx`のページにも適用されます。

これはルートグループで変更できます。ダッシュボードフォルダー内に`/(overview)`という新しいフォルダーを作成します。次に、`loading.tsx`ファイルと`page.tsx`ファイルをフォルダー内に移動します。
![](route-group.avif)

これで、`loading.tsx`ファイルはダッシュボードの概要ページにのみ適用されます。

ルートグループを使用すると、URLパス構造に影響を与えることなく、ファイルを論理グループに編成できます。かっこ`()`を使用して新しいフォルダーを作成すると、その名前はURLパスに含まれません。なので`/dashboard/(overview)/page.tsx` は`/dashboard`のURLに対応します。

ここでは、ルートグループを使用して、loading.tsxをダッシュボードの概要ページにのみ適用されるようにしています。ただし、ルート グループを使用して、アプリケーションをセクション ((marketing)ルートと(shop)ルートなど) に分割したり、大規模なアプリケーションの場合はチームごとに分割したりすることもできます。

(こういうことを言っている。)
![](route-group-organisation.avif)
[Route Groups](https://nextjs.org/docs/app/building-your-application/routing/route-groups)

## コンポーネントのストリーミング

これまでのところ、ページ全体をストリーミングしています。ただし、代わりに、`React Suspense` を使用すると、より詳細に特定のコンポーネントをストリーミングできます。

サスペンスを使用すると、何らかの条件が満たされるまで (データがロードされるなど)、アプリケーションのレンダリング部分を延期できます。動的コンポーネントをサスペンスでラップできます。次に、動的コンポーネントのロード中に表示するフォールバックコンポーネントを渡します。

遅いデータリクエスト`fetchRevenue()`を覚えていると思いますが、これはページ全体の速度を低下させているリクエストです。ページをブロックする代わりに、`Suspense`を使用してこのコンポーネントのみをストリーミングし、ページの残りの UIをすぐに表示できます。

そのためには、データのフェッチをコンポーネントに移動する必要があります。コードを更新して、それがどのようになるかを確認してみましょう。

`fetchRevenue()`のすべてのインスタンスとそのデータを`/dashboard/(overview)/page.tsx`から削除します。

次に、`<Suspense>`をReact からインポートして、`<RevenueChart />` をラップします。フォールバックコンポーネントに`<RevenueChartSkeleton>`を渡すことができます。

→Suspence はchildrenのPromiseの状態を監視していて、読み込み中ならfallbackを、完了したらchildrenを表示する感じっぽい。

## サスペンスの境界線をどこに置くかを決める

サスペンスの境界をどこに置くかは、いくつかの要素によって決まります。

1. ストリーミング中にユーザーにページをどのように体験してもらいたいか。
2. どのコンテンツを優先したいか。
3. コンポーネントがデータの取得に依存している場合。

- `loading.tsx`で行ったようにページ全体をストリーミングすることもできますが、コンポーネントの 1つでデータの取得が遅い場合、読み込み時間が長くなる可能性があります。

- すべてのコンポーネントを個別にストリーミングすることもできますが、準備が整うとUIが画面に表示される可能性があります。

- ページセクションをストリーミングすることによって、千鳥状の効果を作成することもできます。ただし、ラッパーコンポーネントを作成する必要があります。

サスペンス境界をどこに置くかは、アプリケーションによって異なります。一般に、データのフェッチを必要なコンポーネントまで移動し、それらのコンポーネントを Suspense でラップすることをお勧めします。ただし、アプリケーションが必要とする場合は、セクションまたはページ全体をストリーミングしても問題はありません。

# Chapter10

Partial Prerendering (部分的なプリレンダリング)

## 静的コンテンツと動的コンテンツの結合

現在、ルート内で動的関数(たとえば`noStore()`、`cookies()`など)を呼び出すと、ルート全体が動的になります。

これは、今日のほとんどの Web アプリの構築方法と一致しており、アプリケーション全体または特定のルートに対して**静的レンダリング**と**動的レンダリング**のいずれかを選択します。

ただし、ほとんどのルートは完全に静的または動的ではありません。静的コンテンツと動的コンテンツの両方を含むルートがある場合があります。たとえば、ソーシャル メディア フィードがあるとします。投稿は静的ですが、投稿に対する「いいね！」は動的です。または、 eコマースサイトでは、商品の詳細は静的ですが、ユーザーのカートは動的であります。

## 部分プリレンダリングとは何ですか?

Next.js 14 には、**Partial Prerendering**と呼ばれる新しいレンダリングモデルのプレビューがあります。部分的な事前レンダリングは、一部の部分を動的に保ちながら、静的な読み込みシェルでルートをレンダリングできるようにする実験的な機能です。つまり、ルートの動的部分を分離できます。例えば：
![](thinking-in-ppr.avif)
ユーザーがルートを訪問すると:

- 静的ルートシェルが提供されるため、初期ロードが高速になります。
- シェルには、動的コンテンツが非同期で読み込まれる穴が残ります。
- 非同期ホールは並行して読み込まれるため、ページの全体的な読み込み時間が短縮されます。

これは、ルート全体が完全に静的または動的である現在のアプリケーションの動作とは異なります。

部分プリレンダリングは、超高速の静的エッジ配信と完全な動的機能を組み合わせたもので、 Webアプリケーションのデフォルトのレンダリングモデルになる可能性があると考えています。静的サイト生成と動的配信の長所を組み合わせます。

## 部分的なプリレンダリングはどのように機能しますか?

部分的な事前レンダリングは React の同時 API を活用しますサスペンスを使用します何らかの条件が満たされるまで (データがロードされるなど)、アプリケーションのレンダリング部分を延期します。

フォールバックは、他の静的コンテンツとともに初期静的ファイルに埋め込まれます。ビルド時 (または再検証中) に、ルートの静的な部分が事前にレンダリングされ、残りの部分はユーザーがルートを要求するまで延期されます。

コンポーネントを Suspense でラップしても、コンポーネント自体が動的になるのではなく (以前にunstable_noStoreでこの動作を実現していたことを思い出してください)、Suspense はルートの静的部分と動的部分の間の境界として使用されることに注意してください。

部分事前レンダリングの優れた点は、それを使用するためにコードを変更する必要がないことです。Suspense を使用してルートの動的部分をラップしている限り、Next.js はルートのどの部分が静的でどの部分が動的であるかを認識します。

## まとめ

要約すると、アプリケーションでのデータ取得を最適化するために次のことを行いました。

1. サーバーとデータベース間の待ち時間を短縮するために、アプリケーション コードと同じリージョンにデータベースを作成しました。
2. React Server Components を使用してサーバー上のデータを取得しました。これにより、高価なデータのフェッチとロジックをサーバー上に保持し、クライアント側の JavaScript バンドルを削減し、データベースの秘密がクライアントに公開されるのを防ぐことができます。
3. SQL を使用して必要なデータのみを取得することで、リクエストごとに転送されるデータ量と、メモリ内のデータを変換するために必要な JavaScript の量を削減しました。
4. JavaScript を使用してデータの取得を並列化します。そうすることが合理的である場合。
5. 遅いデータ要求によってページ全体がブロックされるのを防ぎ、ユーザーがすべてが読み込まれるのを待たずに UI の操作を開始できるようにするために、ストリーミングを実装しました。
6. データのフェッチを必要なコンポーネントに移動し、部分的な事前レンダリングに備えてルートのどの部分を動的にする必要があるかを分離します。

# Chapter11

検索とページネーションの追加

- `<Search/>`ユーザーは特定の請求書を検索できます。
- `<Pagination/>`ユーザーが請求書のページ間を移動できるようにします。
- `<Table/>`請求書を表示します。

検索機能はクライアントとサーバーにまたがります。ユーザーがクライアント上で請求書を検索すると、URL パラメーターが更新され、サーバー上でデータが取得され、新しいデータを使用してテーブルがサーバー上で再レンダリングされます。

## なぜ URL 検索パラメータを使用するのでしょうか?

上で述べたように、URL 検索パラメータを使用して検索状態を管理します。クライアント側の状態を使用することに慣れている場合、このパターンは新しいかもしれません。

URL パラメータを使用して検索を実装すると、次のような利点があります。

- ブックマーク可能および共有可能な URL: 検索パラメータはURL内にあるため、ユーザーは、将来の参照や共有のために、検索クエリやフィルタを含むアプリケーションの現在の状態をブックマークできます。
- サーバー側レンダリングと初期ロード: URLパラメーターをサーバー上で直接使用して初期状態をレンダリングできるため、サーバーレンダリングの処理が容易になります。
- 分析と追跡: URL に検索クエリとフィルターを直接含めることで、追加のクライアント側ロジックを必要とせずに、ユーザーの行動を追跡することが容易になります。

## 検索機能の追加

これらは、検索機能を実装するために使用する Next.js クライアントフックです。

- `useSearchParams`: 現在のURLのパラメータにアクセスできます。たとえば、`/dashboard/invoices?page=1&query=pending`このURLの検索パラメータは次のようになります`{page: '1', query: 'pending'}`。
- `usePathname`: 現在のURLのパス名を読み取ることができます。たとえば、route`/dashboard/invoices` の場合、`usePathname`は`dashboard/invoices`を返します。
- `useRouter`: クライアント コンポーネント内のルート間のナビゲーションをプログラムで有効にします。使用できる方法は複数あります。
  実装手順の概要を次に示します。

ユーザーの入力をキャプチャします。
検索パラメータを使用して URL を更新します。
URL を入力フィールドと同期させてください。
検索クエリを反映するようにテーブルを更新します。

## 1.ユーザーの入力をキャプチャする

`/app/ui/search.tsx`の`<Search>`コンポーネントに移動すると、次のことがわかります。

- `"use client"`- これはクライアント コンポーネントです。つまり、イベント スナーとフックを使用できます。
- `<input>`- これは検索入力です。

新しい`handleSearch`関数を作成し、`<input>`要素に`onChange`リスナーを追加します。入力値が変化するたびに`onChange`が`handleSearch`を呼び出します。

## 2.検索パラメータを使用してURLを更新します

`'next/navigation'`から`useSearchParams`フックをインポートし、変数に割り当てます。

`handleSearch`の内部で、新しい`searchParams`変数を使用する、`URLSearchParams`を新規作成します。

```javascript:
  function handleSearch(term: string) {
    const params = new URLSearchParams(searchParams);
  }
```

`URLSearchParams`は、URLクエリパラメーターを操作するためのユーティリティメソッドを提供する Web API です。複雑な文字列リテラルを作成する代わりに、それを使用して のような params 文字列を取得できます`?page=1&query=a`。

次に、ユーザーの入力に基づいた params 文字列を`set`します。入力が空の場合は、次のように`delete`します。

```javascript:
if (term) {
  params.set("query", term);
} else {
  params.delete("query");
}
```

これでクエリ文字列が得られました。Next.jsuseRouterとusePathnameフックを使用して URL を更新できます。

`useRouter`と`usePathname`を`next/navigation`からインポートし、`handleSearch`の中で`useRouter()`の`replace`メソッドを使用します。

何が起こっているかの内訳は次のとおりです。

- `${pathname}`あなたの場合、現在のパスです`"/dashboard/invoices"`。
- ユーザーが検索バーに入力すると、入力が`params.toString()`で URLに適した形式に変換されます。
- `replace(${pathname}?${params.toString()})`でURLをユーザーの検索データで更新します。たとえば、ユーザーが「Lee」を検索したとすると、URLは`/dashboard/invoices?query=lee`になります。
- Next.js のクライアント側ナビゲーションのおかげで、URL はページをリロードしなくても更新されます (これについては、ページ間のナビゲーションに関する章で学習しました)。

## URL と入力の同期を維持する

入力フィールドがURLと同期し、共有時にデータが入力されるようにするには、`defaultValue`以下から読み取ることで`searchParams`を`input`に渡すことができます。

## テーブルの更新

最後に、検索クエリを反映するようにテーブル コンポーネントを更新する必要があります。

請求書ページに戻ります。
ページ コンポーネントはという prop を受け入れるsearchParamsため、現在の URL パラメータを<Table>コンポーネントに渡すことができます。

クライアントサイドでparamsを読み込みたい場合は、useSearchParamsフックを使う。

## ベストプラクティス: デバウンス

おめでとう！Next.js で検索を実装しました。しかし、それを最適化するためにできることはあります。

`handleSearch`関数内に`console.log`を追加します。

キーストロークごとに URL を更新するため、キーストロークごとにデータベースにクエリを実行することになります。私たちのアプリケーションは小さいため、これは問題ではありませんが、アプリケーションに数千人のユーザーがいて、各ユーザーがキーストロークごとに新しいリクエストをデータベースに送信すると想像してください。

```JavaScript:
function handleSearch(term: string) {
  console.log(`searching... ${term}`);

  const params = new URLSearchParams(searchParams);

  // ...
}
```

デバウンスは、関数が起動できる速度を制限するプログラミング手法です。この例では、ユーザーが入力をやめたときにのみデータベースにクエリを実行する必要があります。

デバウンスの仕組み:

1. トリガーイベント: デバウンスする必要があるイベント (検索ボックスのキーストロークなど) が発生すると、タイマーが開始します。
2. 待機: タイマーが期限切れになる前に新しいイベントが発生すると、タイマーはリセットされます。
3. 実行: タイマーがカウントダウンの終わりに達すると、デバウンス関数が実行されます。

デバウンスは、独自のデバウンス関数を手動で作成するなど、いくつかの方法で実装できます。物事を簡単にするために、`use-debounce`
というライブラリを使用します。

```
npm i use-debounce
```

```javascript:
// ...
import { useDebouncedCallback } from "use-debounce";

// Inside the Search Component...
const handleSearch = useDebouncedCallback((term) => {
  console.log(`Searching... ${term}`);

  const params = new URLSearchParams(searchParams);
  if (term) {
    params.set("query", term);
  } else {
    params.delete("query");
  }
  replace(`${pathname}?${params.toString()}`);
}, 300);
```

この関数は`handleSearch`の内容をラップし、ユーザーが入力をやめてから特定の時間 (300 ミリ秒) が経過した後にのみコードを実行します。

次に、検索バーに再度入力し、開発ツールでコンソールを開きます。次の内容が表示されるはずです。

## ページネーションの追加

検索機能を導入すると、表には一度に 6件の請求書しか表示されないことがわかります。これは、`data.ts` の`fetchFilteredInvoices()`関数が1ページあたり最大6つの請求書を返すためです。

ページネーションを追加すると、ユーザーはさまざまなページを移動してすべての請求書を表示できるようになります。検索の場合と同じように、URLパラメータを使用してページネーションを実装する方法を見てみましょう。

コンポーネントに移動すると、それがクライアントコンポーネントであることがわかります。クライアント上でデータをフェッチすると、データベースの秘密が漏洩してしまうため、これは望ましくありません (API レイヤーを使用していないことに注意してください)。代わりに、サーバー上のデータをフェッチし、それを prop としてコンポーネントに渡すことができます。

`/dashboard/invoices/page.tsx`で、 `fetchInvoicesPages`という新しい関数をインポートし、 `searchParams`から`query`を引数として渡します。

次に、`totalPagesprop` を`<Pagination/>`コンポーネントに渡します。

`<Pagination/>`コンポーネントに移動し、`usePathname`フックと`useSearchParams`フックをインポートします。これを使用して現在のページを取得し、新しいページを設定します。

# Chapter12

## サーバーアクションとは何ですか?

React Server Actions を使用すると、サーバー上で非同期コードを直接実行できます。データを変更するために APIエンドポイントを作成する必要がなくなります。代わりに、サーバー上で実行され、クライアント コンポーネントまたはサーバー コンポーネントから呼び出すことができる非同期関数を作成します。

Web アプリケーションはさまざまな脅威に対して脆弱になる可能性があるため、セキュリティは最優先事項です。ここでサーバー アクションが登場します。サーバー アクションは、さまざまな種類の攻撃から保護し、データを保護し、承認されたアクセスを保証する、効果的なセキュリティ ソリューションを提供します。サーバー アクションは、POST リクエスト、暗号化されたクロージャ、厳格な入力チェック、エラー メッセージのハッシュ化、ホスト制限などの技術を通じてこれを実現し、すべて連携してアプリの安全性を大幅に強化します。

## サーバーアクションでのフォームの使用

React では、`<form>`要素内の`action`属性を使用してアクションを呼び出すことができます。アクションはキャプチャされたデータを含むネイティブ`FormData`を自動的に受け取ります。

```JavaScript:
// Server Component
export default function Page() {
  // Action
  async function create(formData: FormData) {
    'use server';

    // Logic to mutate data...
  }

  // Invoke the action using the "action" attribute
  return <form action={create}>...</form>;
}
```

サーバー コンポーネント内でサーバー アクションを呼び出すことの利点は、段階的な機能拡張です。クライアントで JavaScript が無効になっている場合でもフォームは機能します。

## 請求書の作成

新しい請求書を作成する手順は次のとおりです。

1. ユーザーの入力を取得するフォームを作成します。
2. サーバー アクションを作成し、フォームから呼び出します。
3. サーバー アクション内で、オブジェクトからデータを抽出しますformData。
4. データベースに挿入するデータを検証して準備します。
5. データを挿入し、エラーがあれば処理します。
6. キャッシュを再検証し、ユーザーを請求書ページにリダイレクトします。

### 2.サーバーアクションの作成

フォームの送信時に呼び出されるサーバー アクションを作成しましょう。

`lib`ディレクトリに移動し、 `actions.ts`という名前の新しいファイルを作成します。このファイルの先頭に、React `use server`を追加します。

`'use server'`を追加すると、ファイル内のエクスポートされたすべての関数がサーバー関数としてマークされます。これらのサーバー機能はクライアント コンポーネントとサーバー コンポーネントにインポートできるため、非常に多用途なものになります。

アクション内に「use server」を追加することで、サーバー コンポーネント内にサーバー アクションを直接記述することもできます。ただし、このコースでは、それらをすべて別のファイルに整理します。

```JavaScript:
import { createInvoice } from '@/app/lib/actions';

export default function Form({
  customers,
}: {
  customers: customerField[];
}) {
  return (
    // createInvoice関数は、formDataを受け取れるのでこのまま渡せる。
    <form action={createInvoice}>
      // ...
  )
}
```

サーバー アクションは舞台裏で`POST`API エンドポイントを作成します。これが、サーバー アクションを使用するときに API エンドポイントを手動で作成する必要がない理由です。

### 3. `formData`からデータを抽出

`actions.ts`ファイルに戻り、 `formData`の値を抽出する必要があります。いくつかの方法があります。この例では`.get(name)`方法を使います。。
[FormData API](https://developer.mozilla.org/en-US/docs/Web/API/FormData/append)

ヒント:多くのフィールドがあるフォームを使用している場合は、JavaScriptの`Object.fromEntries()`の`entries()` を使えます。例えば：
const rawFormData = Object.fromEntries(formData.entries())

### 4.データを検証して準備する

**型の検証と強制**
フォームからのデータがデータベース内の予期される型と一致していることを検証することが重要です。たとえば、console.logアクション内に を追加すると、次のようになります。

```
console.log(typeof rawFormData.amount);
```

この結果から、`amount`の型が`String`であって、`Number`ではないことがわかります。これは、`input`要素が例え、`type="number"`であってもstringを返すためです。

型の検証を処理するには、いくつかのオプションがあります。型を手動で検証することもできますが、型検証ライブラリを使用すると時間と労力を節約できます。例として、`Zod` を使用します。、このタスクを簡素化できる TypeScript ファーストの検証ライブラリです。

`actions.ts`ファイルで`Zod` をインポートし、フォームオブジェクトの形状に一致するスキーマを定義します。このスキーマは、データベースに保存する前に`formData`を検証します。

この`amount`フィールドは、文字列から数値に強制 (変更) すると同時に、その型を検証するように具体的に設定されています。

次に、`rawFormData`を渡して`CreateInvoice`型を検証できます。

**値をセント単位で保存する**
通常、JavaScript 浮動小数点エラーを排除し、精度を高めるために、データベースに通貨値をセント単位で保存することをお勧めします。

金額をセントに変換しましょう。

最後に、請求書の作成日として「YYYY-MM-DD」の形式で新しい日付を作成しましょう。

### 5.データベースへのデータの挿入

SQLクエリを作成して新しい請求書をデータベースに挿入し、変数を渡すことができます。
現時点ではエラーは処理されていません。次の章でそれを行います。とりあえずは次のステップに進みましょう。

### 6.再検証とリダイレクト

Next.js には、ユーザーのブラウザーにルートセグメントを一時的に保存するクライアント側ルーターキャッシュがあります。このキャッシュは、プリフェッチと併せて、サーバーに対するリクエストの数を減らしながら、ユーザーがルート間を迅速に移動できるようにします。

請求書ルートに表示されるデータを更新しているため、このキャッシュをクリアして、サーバーへの新しいリクエストをトリガーしたいと考えています。Next.js の`revalidatePath`関数を使用してこれを行うことができます。

データベースが更新されると、`/dashboard/invoices`パスが再検証され、新しいデータがサーバーからフェッチされます。

この時点で、ユーザーを`/dashboard/invoices`ページにリダイレクトすることもできます。Next.js の`redirect`関数を使用してこれを行うことができます。

## 請求書の更新

請求書更新フォームは請求書作成フォームと似ていますが、データベース内のレコードを更新するために請求書`id`を渡す必要がある点が異なります。請求書`id`を取得して渡す方法を見てみましょう。

請求書を更新するには次の手順を実行します。

1. 請求書`id`を使用して新しい動的ルート セグメントを作成します。
2. ページパラメータから請求書`id`を読み取ります。
3. データベースから特定の請求書を取得します。
4. フォームに請求書のデータを事前に入力します。
5. データベース内の請求書データを更新します。

### 1. 請求書`id`を使用して新しい動的ルート セグメントを作成します。

Next.jsを使用すると、正確なセグメント名が分からず、データに基づいてルートを作成したい場合に、動的ルート セグメントを作成できます。これは、ブログ投稿のタイトル、製品ページなどです。フォルダー名を角括弧で囲むことによって、動的ルート セグメントを作成できます。たとえば、 [id]、 [post]、[slug]。

`/invoices`フォルダー内に`[id]`という新しい動的ルートを作成し、次に`page.tsx`ファイルを含む新しいルート`edit`を作成します。ファイル構造は次のようになります。
![](edit-invoice-route.avif)

### 2. ページパラメータから請求書`id`を読み取ります。

別のフォームを (ファイルから) インポートする点を除いて、`/create`請求書ページと`edit-form.tsx`がどのように似ているかに注目してください。このフォームには、顧客の名前、請求金額、およびステータスを事前に入力する必要があります。`defaultValue`フォームフィールドに事前に入力するには、 `id`を使用して特定の請求書を取得する必要があります。

`searchParams`に加えて、ページ コンポーネントは、 `params`という`props`にアクセスするできます。そして`params`を使って、`id`にアクセスできます。プロパティを受け取るように`<Page>`コンポーネントを更新します。

### 3. データベースから特定の請求書を取得します。

- `fetchInvoiceById`という新しい関数をインポートし、`id`を引数として渡します。
- `fetchCustomers`関数をインポートしてドロップダウンの顧客名を取得します。

**UUID と自動インクリメントキー**

インクリメントキー (1、2、3 など) の代わりに UUID を使用します。これにより URL が長くなります。ただし、UUID は ID 衝突のリスクを排除し、グローバルに一意であり、列挙型攻撃のリスクを軽減するため、大規模なデータベースに最適です。
ただし、よりクリーンな URL を好む場合は、自動インクリメントキーを使用することをお勧めします。

### 4. idをサーバーアクションに渡す

最後に、データベース内の適切なレコードを更新できるように、を`id`サーバーアクションに渡します。次のように `id`を引数として渡すことはできません。

```JavaScript: /app/ui/invoices/edit-form.tsx
// Passing an id as argument won't work
<form action={updateInvoice(id)}>
```

代わりに、JS の`bind`を使用して`id`をサーバー アクションに渡すことができます。これにより、サーバーアクションに渡されるすべての値が確実にエンコードされます。

```JavaScript: /app/ui/invoices/edit-form.tsx
// ...
import { updateInvoice } from '@/app/lib/actions';

export default function EditInvoiceForm({
  invoice,
  customers,
}: {
  invoice: InvoiceForm;
  customers: CustomerField[];
}) {
  const updateInvoiceWithId = updateInvoice.bind(null, invoice.id);

  return (
    <form action={updateInvoiceWithId}>
      <input type="hidden" name="id" value={invoice.id} />
    </form>
  );
}
```

注:フォーム内で非表示の入力フィールドを使用することもできます (例<input type="hidden" name="id" value={invoice.id} />)。ただし、値は HTML ソースにフルテキストとして表示されるため、ID などの機密データには理想的ではありません。

`actions.ts`ファイル内に新しいアクションを作成します`updateInvoice`。

`createInvoice`アクションと同様に、次のようになります。

1. `formData`からデータを抽出しています。
2. Zod を使用して型を検証します。
3. 金額をセントに変換します。
4. 変数を SQL クエリに渡します。
5. `revalidatePath`でクライアントキャッシュをクリアし、新しいサーバーリクエストを行うために呼び出します。
6. `redirect`をユーザーを請求書のページにリダイレクトするために呼び出します。

## 請求書の削除

サーバー アクションを使用して請求書を削除するには、`<form>`要素で削除ボタンをラップし、`bind`を使用して`id`をサーバー アクションに渡します。

```JavaScript: /app/ui/invoices/buttons.tsx
import { deleteInvoice } from '@/app/lib/actions';

// ...

export function DeleteInvoice({ id }: { id: string }) {
  const deleteInvoiceWithId = deleteInvoice.bind(null, id);

  return (
    <form action={deleteInvoiceWithId}>
      <button className="rounded-md border p-2 hover:bg-gray-100">
        <span className="sr-only">Delete</span>
        <TrashIcon className="w-4" />
      </button>
    </form>
  );
}
```

`actions.tsx`に`deleteInvoice`を定義します。

```JavaScript
export async function deleteInvoice(id: string) {
  await sql`DELETE FROM invoices WHERE id = ${id}`;
  revalidatePath('/dashboard/invoices');
}
```

このアクションは`/dashboard/invoices`パス内で呼び出されているため、 `redirect`を呼び出す必要はありません。`revalidatePath`を呼び出すと、新しいサーバー要求がトリガーされ、テーブルが再レンダリングされます。

# Chapter13

エラーハンドリング

## サーバーアクションへの`try/catch`の追加

まず、JavaScript のtry/catchステートメントをサーバーアクションに追加して、エラーを適切に処理できるようにしましょう。

`redirect`が`try/catch`ブロックの外でどのように呼び出されているかに注意してください。`try`が成功した場合にのみ`redirect`は到達可能になります。

## すべてのエラーを処理する`error.tsx`

この`error.tsx`ファイルは、ルート セグメントの UI 境界を定義するために使用できます。これは、予期しない**エラー**に対するキャッチオールとして機能し、ユーザーにフォールバック UI を表示できるようにします。

`/dashboard/invoices`フォルダー内に新しいファイル`error.tsx`を作成します。

error.tsxのコードについては、いくつか気づくことがあります。

- 「クライアントを使用」 -error.tsxクライアント コンポーネントである必要があります。
- 2つのprops受け入れます。
  - error: このオブジェクトは JavaScript のネイティブのErrorインスタンスです。
  - reset: エラー境界をリセットする機能です。実行されると、関数はルートセグメントの再レンダリングを試みます。

## 　`notFound`関数による404エラーの処理

エラーを適切に処理するもう 1 つの方法は、`notFound`関数を使用することです。`error.tsx`はすべてのエラーを捕捉するのに便利ですが、`notFound`は存在しないリソースを取得しようとするときにも使用できます。

より具体的にしたい場合は、404 エラーを表示して、アクセスしようとしているリソースが見つからなかったことをユーザーに伝えることができます。
リソースが見つからなかったことを確認するには、 `data.ts`の`fetchInvoiceById`関数に入り、返された`invoice`の内容をコンソールに記録します。

請求書がデータベースに存在しないことがわかったので、`notFound`を使用して処理しましょう。`/dashboard/invoices/[id]/edit/page.tsx`に移動し、`'next/navigation'`から`{ notFound }`をインポートします。

`<Page>`で特定の請求書が見つからない場合にエラーがスローされるようになりました。次にユーザーにエラーUIを表示します。`/edit`フォルダー内に`not-found.tsx`ファイルを作成します。
![](not-found-file.avif)

これは心に留めておくべきことであり、`notFound`は`error.tsx`より優先されるため、より具体的なエラーを処理したい場合にこれを利用できます。

# Chapter14

アクセシビリティの向上

前の章では、エラー (404 エラーを含む) を捕捉し、ユーザーにフォールバックを表示する方法について説明しました。ただし、パズルのもう1つのピースであるフォーム検証についてはまだ議論する必要があります。`useFormState`アクセシビリティを念頭に置きながら、サーバーアクションを使用してサーバー側の検証を実装する方法と、フックを使用してフォームエラーを表示する方法を見てみましょう。

## アクセシビリティとは何ですか?

アクセシビリティとは、障害のある人を含む誰もが使用できる Web アプリケーションを設計および実装することを指します。これは、キーボード ナビゲーション、セマンティック HTML、画像、色、ビデオなど、多くの領域をカバーする広大なトピックです。

このコースではアクセシビリティについては詳しく説明しませんが、Next.js で利用できるアクセシビリティ機能と、アプリケーションのアクセシビリティを高めるための一般的な実践方法について説明します。

## Next.js で ESLint アクセシビリティ プラグインを使用する

デフォルトでは、Next.js には`eslint-plugin-jsx-a11y`が含まれています。これはアクセシビリティの問題を早期に発見するのに役立つプラグインです。たとえば、このプラグインは、`alt`テキストのない画像がある場合や `aria-*` や `role` 属性が間違って使用されている場合などに警告します。

これがどのように機能するかを見てみましょう!

`package.json`ファイルに`next lint`をスクリプトとして追加します 。

```json
"scripts": {
    "build": "next build",
    "dev": "next dev",
    "seed": "node -r dotenv/config ./scripts/seed.js",
    "start": "next start",
    "lint": "next lint"
},
```

次に、ターミナルで次のコマンド`npm run lint`を実行します。

```
npm run lint
```

## フォームのアクセシビリティの向上

フォームのアクセシビリティを向上させるために、すでに次の 3 つのことを行っています。

- セマンティック HTML :`<div>`の代わりにセマンティック要素 ( `<input>`、`<option>`など) を使用します。これにより、支援技術 (AT) が入力要素に焦点を当て、適切なコンテキスト情報をユーザーに提供できるようになり、フォームのナビゲーションと理解が容易になります。
- ラベリング: `<label>`や、`htmlFor`属性を含むことで、各フォーム フィールドに説明的なテキスト ラベルが付けられます。これにより、コンテキストが提供されることで AT サポートが向上し、ユーザーがラベルをクリックして対応する入力フィールドにフォーカスできるようになり、使いやすさも向上します。
- フォーカスアウトライン: フィールドは、フォーカスがあるときにアウトラインを表示するように適切にスタイル設定されます。これは、ページ上のアクティブな要素を視覚的に示し、キーボード ユーザーとスクリーン リーダー ユーザーの両方がフォーム上のどこにいるかを理解するのに役立つため、アクセシビリティにとって重要です。これを確認するには、`tab` を押します。

## フォームの検証

http://localhost:3000/dashboard/invoices/createに移動します。をクリックし、空のフォームを送信します。何が起こりますか？
エラーが発生します。これは、空のフォーム値をサーバー アクションに送信しているためです。これを防ぐには、クライアントまたはサーバーでフォームを検証します。

## クライアント側の検証

クライアントでフォームを検証するには、いくつかの方法があります。最も簡単な方法は、フォーム内の`<input>`や`<select>`要素に`required`属性を追加することにより、ブラウザーが提供するフォーム検証に依存することです。
例えば：

```JavaScript: app/ui/invoices/create-form.tsx
<input
  id="amount"
  name="amount"
  type="number"
  placeholder="Enter USD amount"
  className="peer block w-full rounded-md border border-gray-200 py-2 pl-10 text-sm outline-2 placeholder:text-gray-500"
  // add
  required
/>
```

フォームを再度送信するさい、空の値を含むフォームを送信しようとすると、ブラウザに警告が表示されます。
一部の AT はブラウザー検証をサポートしているため、このアプローチは通常は問題ありません。
クライアント側の検証の代替手段は、サーバー側の検証です。次のセクションでそれを実装する方法を見てみましょう。ここでは、required属性を追加した場合は削除します。

## サーバー側の検証

サーバー上のフォームを検証することで、次のことが可能になります。

- データをデータベースに送信する前に、データが予期された形式であることを確認してください。
- 悪意のあるユーザーがクライアント側の検証をバイパスするリスクを軽減します。
- 有効なデータとみなされるものについての信頼できる情報源を 1 つ用意します。

`create-form.tsx`コンポーネントで、`react-dom`から`useFormState`フックをインポートします。`useFormState`はフックであるため、`"use client"`ディレクティブを使用してフォームをクライアント コンポーネントに変換する必要があります。

フォームコンポーネント内の`useFormState`フックは次のとおりです。

- 2 つの引数を取ります: (action, initialState)。
- 2 つの値を返します: [state, dispatch]- フォームの状態、およびディスパッチ関数 ( useReducerと同様)）

`createInvoice`アクションを`useFormState`の引数として渡し、`<form action={}>`属性内で`dispatch`を呼び出します。

```JavaScript: /app/ui/invoices/create-form.tsx
// ...
import { useFormState } from 'react-dom';

export default function Form({ customers }: { customers: CustomerField[] }) {
  const [state, dispatch] = useFormState(createInvoice, initialState);

  return <form action={dispatch}>...</form>;
}
```

`initialState`は任意に定義できます。この場合は、`message`と`errors`の 2 つの空のキーを持つオブジェクトを作成します。

```JavaScript: /app/ui/invoices/create-form.tsx
// ...
import { useFormState } from 'react-dom';

export default function Form({ customers }: { customers: CustomerField[] }) {
  const initialState = { message: null, errors: {} };
  const [state, dispatch] = useFormState(createInvoice, initialState);

  return <form action={dispatch}>...</form>;
}
```

サーバー アクションを更新すると、よりわかりやすくなります。
`action.ts`ファイル内で`Zod`を使用してフォームデータを検証できます。`FormSchema`を次のように更新します。

```JavaScript: /app/lib/action.ts
const FormSchema = z.object({
  id: z.string(),
  customerId: z.string({
    invalid_type_error: 'Please select a customer.',
  }),
  amount: z.coerce
    .number()
    .gt(0, { message: 'Please enter an amount greater than $0.' }),
  status: z.enum(['pending', 'paid'], {
    invalid_type_error: 'Please select an invoice status.',
  }),
  date: z.string(),
});
```

- `customerId` - Zod は`string`タイプを予期しているため、顧客フィールドが空の場合はすでにエラーをスローします。ただし、ユーザーが顧客を選択しない場合は、フレンドリーなメッセージを追加しましょう。
- `amount`- 金額タイプを`string`から`number`に強制しているため、文字列が空の場合はデフォルトでゼロになります。`.gt()`関数を使用して、常に 0 より大きい量が必要であることを Zod に伝えましょう。
- `status` - Zod はステータス フィールドが空の場合、「pending」または「paid」を期待しているため、すでにエラーをスローします。ユーザーがステータスを選択しない場合には、フレンドリーなメッセージも追加しましょう。

次に、`createInvoice`が2つのパラメーターを受け入れるようにアクションを更新します。

```JavaScript: /app/lib/actions.ts
// This is temporary until @types/react-dom is updated
export type State = {
  errors?: {
    customerId?: string[];
    amount?: string[];
    status?: string[];
  };
  message?: string | null;
};

export async function createInvoice(prevState: State, formData: FormData) {
  // ...
}
```

- `formData` - 以前と同じ。
- `prevState` - フックから渡された状態が含まれます`useFormState`。この例のアクションでは使用しませんが、必須の `prop` です。

次に、Zod`parse()`関数を`safeParse()`に変更します。

```JavaScript: /app/lib/actions.ts
export async function createInvoice(prevState: State, formData: FormData) {
  // Validate form fields using Zod
  const validatedFields = CreateInvoice.safeParse({
    customerId: formData.get('customerId'),
    amount: formData.get('amount'),
    status: formData.get('status'),
  });

  // ...
}
```

`safeParse()`は `success`または`error`フィールドのいずれかを含むオブジェクトを返します。これにより、このロジックを`try/catch`ブロック内に配置しなくても、検証をより適切に処理できるようになります。

情報をデータベースに送信する前に、フォームフィールドが条件付きで正しく検証されたかどうかを確認してください。

```JavaScript: /app/lib/actions.ts
export async function createInvoice(prevState: State, formData: FormData) {
  // Validate form fields using Zod
  const validatedFields = CreateInvoice.safeParse({
    customerId: formData.get('customerId'),
    amount: formData.get('amount'),
    status: formData.get('status'),
  });

  // If form validation fails, return errors early. Otherwise, continue.
  if (!validatedFields.success) {
    return {
      errors: validatedFields.error.flatten().fieldErrors,
      message: 'Missing Fields. Failed to Create Invoice.',
    };
  }

  // ...
}
```

`validatedFields`成功しない場合は、`Zod`からのエラーメッセージとともに関数を早期に返します。

ヒント: console.logで`validatedFields`を参照し、空のフォームを送信してその形状を確認します。

最後に、`try/catch` ブロックの外側でフォームの検証を個別に処理しているため、データベース エラーに対して特定のメッセージを返すことができ、最終的なコードは次のようになります。

```JavaScript: /app/lib/actions.ts
export async function createInvoice(prevState: State, formData: FormData) {
  // Validate form using Zod
  const validatedFields = CreateInvoice.safeParse({
    customerId: formData.get('customerId'),
    amount: formData.get('amount'),
    status: formData.get('status'),
  });

  // If form validation fails, return errors early. Otherwise, continue.
  if (!validatedFields.success) {
    return {
      errors: validatedFields.error.flatten().fieldErrors,
      message: 'Missing Fields. Failed to Create Invoice.',
    };
  }

  // Prepare data for insertion into the database
  const { customerId, amount, status } = validatedFields.data;
  const amountInCents = amount * 100;
  const date = new Date().toISOString().split('T')[0];

  // Insert data into the database
  try {
    await sql`
      INSERT INTO invoices (customer_id, amount, status, date)
      VALUES (${customerId}, ${amountInCents}, ${status}, ${date})
    `;
  } catch (error) {
    // If a database error occurs, return a more specific error.
    return {
      message: 'Database Error: Failed to Create Invoice.',
    };
  }

  // Revalidate the cache for the invoices page and redirect the user.
  revalidatePath('/dashboard/invoices');
  redirect('/dashboard/invoices');
}
```

フォームコンポーネントにエラーを表示しましょう。`create-form.tsx`コンポーネントに戻ると、フォーム`state`を使用してエラーにアクセスできます。

**特定のエラーをそれぞれチェックする三項演算子**を追加します。たとえば、顧客のフィールドの後に次を追加できます。

```JavaScript: /app/ui/invoices/create-form.tsx
<form action={dispatch}>
  <div className="rounded-md bg-gray-50 p-4 md:p-6">
    {/* Customer Name */}
    <div className="mb-4">
      <label htmlFor="customer" className="mb-2 block text-sm font-medium">
        Choose customer
      </label>
      <div className="relative">
        <select
          id="customer"
          name="customerId"
          className="peer block w-full rounded-md border border-gray-200 py-2 pl-10 text-sm outline-2 placeholder:text-gray-500"
          defaultValue=""
          // add
          aria-describedby="customer-error"
        >
          <option value="" disabled>
            Select a customer
          </option>
          {customerNames.map((name) => (
            <option key={name.id} value={name.id}>
              {name.name}
            </option>
          ))}
        </select>
        <UserCircleIcon className="pointer-events-none absolute left-3 top-1/2 h-[18px] w-[18px] -translate-y-1/2 text-gray-500" />
      </div>
      // add
      <div id="customer-error" aria-live="polite" aria-atomic="true">
        {state.errors?.customerId &&
          state.errors.customerId.map((error: string) => (
            <p className="mt-2 text-sm text-red-500" key={error}>
              {error}
            </p>
          ))}
      </div>
    </div>
    // ...
  </div>
</form>
```

**ヒント** :コンポーネント内でconsole.logで`state` を確認し、すべてが正しく接続されているかどうかを確認できます。フォームがクライアント コンポーネントになっているので、開発ツールのコンソールを確認してください。

上記のコードでは、次の aria ラベルも追加しています。

- `aria-describedby="customer-error"` : これにより、`select`要素とエラー メッセージ コンテナーの間の関係が確立されます。これは、`id="customer-error"`のコンテナが`select`要素を説明していることを示します。ユーザー`select`がボックスを操作してエラーを通知すると、スクリーン リーダーがこの説明を読み上げます。
- `id="customer-error"`: この`id`属性は、入力のエラーメッセージを保持する HTML `select`要素を一意に識別します。この関係を確立するためには`aria-describedby`が必要です。
- `aria-live="polite"`: スクリーン リーダーは、`div`要素の内部のエラーが更新されたときにユーザーに丁寧に通知する必要があります。コンテンツが変更されると (たとえば、ユーザーがエラーを修正するときなど)、スクリーンリーダーは変更を通知しますが、それはユーザーがアイドル状態の場合に限り、変更を中断しないようにします。

## ariaラベルを追加する

amount, status, state.messageについてもエラーの表示を行う。

# Chapter15

認証の追加

## 認証とは何ですか?

認証は、今日の多くの Web アプリケーションの重要な部分です。これは、ユーザーが本人であるかどうかをシステムがチェックする方法です。

安全な Web サイトでは、多くの場合、ユーザーの身元を確認するために複数の方法が使用されます。たとえば、ユーザー名とパスワードを入力すると、サイトからデバイスに確認コードが送信されたり、Google Authenticator などの外部アプリが使用されたりすることがあります。この 2 要素認証 (2FA) はセキュリティの向上に役立ちます。たとえ誰かがあなたのパスワードを知ったとしても、あなたの固有のトークンがなければあなたのアカウントにアクセスすることはできません。

## 認証と認可

Web 開発では、認証と認可は異なる役割を果たします。

認証とは、ユーザーが本人であることを確認することです。ユーザー名やパスワードなど、自分が持っているもので自分の身元を証明することになります。
次のステップは認可です。ユーザーの身元が確認されると、認可によってアプリケーションのどの部分の使用が許可されるかが決定されます。
したがって、認証によってユーザーが誰であるかが確認され、認可によってアプリケーション内で実行できる内容やアクセスできる内容が決まります。

## ログインルートの作成

アプリケーションに `/login`という新しいルートを作成し、次のコードを貼り付けます。

```JavaScript: app/login/page.tsx
import AcmeLogo from '@/app/ui/acme-logo';
import LoginForm from '@/app/ui/login-form';

export default function LoginPage() {
  return (
    <main className="flex items-center justify-center md:h-screen">
      <div className="relative mx-auto flex w-full max-w-[400px] flex-col space-y-2.5 p-4 md:-mt-32">
        <div className="flex h-20 w-full items-end rounded-lg bg-blue-500 p-3 md:h-36">
          <div className="w-32 text-white md:w-36">
            <AcmeLogo />
          </div>
        </div>
        <LoginForm />
      </div>
    </main>
  );
}
```

`LoginForm`については後で説明します。

## NextAuth.js

NextAuth.js を使用しますアプリケーションに認証を追加します。NextAuth.js は、セッション、サインインとサインアウト、および認証のその他の側面の管理に伴う複雑さの多くを抽象化します。これらの機能を手動で実装することもできますが、そのプロセスには時間がかかり、エラーが発生しやすくなります。NextAuth.js はプロセスを簡素化し、Next.js アプリケーションでの認証のための統合ソリューションを提供します。

## NextAuth.js のセットアップ

ターミナルで以下を実行.

```bash:
npm install next-auth@beta
```

ここでは、Next.js 14 と互換性のある`beta`バージョンの NextAuth.js をインストールします。

次に、アプリケーションの秘密キーを生成します。このキーは Cookie の暗号化に使用され、ユーザー セッションのセキュリティが確保されます。これを行うには、ターミナルで次のコマンドを実行します。

```bash:
openssl rand -base64 32
```

次に、.envファイル内で、生成されたキーを`AUTH_SECRET`変数に追加します。

```.env
AUTH_SECRET=your-secret-key
```

実稼働環境で認証を機能させるには、Vercel プロジェクトの環境変数も更新する必要があります。このガイドを確認してくださいVercel に環境変数を追加する方法について説明します。
[環境変数を追加](https://vercel.com/docs/projects/environment-variables)

## ページオプションの追加

`authConfig`オブジェクトをエクスポートする`auth.config.ts`ファイルをプロジェクトのルートに作成します。このオブジェクトには、NextAuth.js の構成オプションが含まれます。現時点では、`pages`オプションのみが含まれます。

```JavaScript: /auth.config.ts
import type { NextAuthConfig } from 'next-auth';

export const authConfig = {
  pages: {
    signIn: '/login',
  },
};
```

`pages`オプションを使用して、カスタム サインイン、サインアウト、およびエラーページのルートを指定できます。これは必須ではありませんが、オプション`signIn: '/login'`を`pages`に追加すると、ユーザーは NextAuth.js のデフォルト ページではなく、カスタム ログイン ページにリダイレクトされます。

## Next.jsミドルウェアでルートを保護する

次に、ルートを保護するロジックを追加します。これにより、ユーザーはログインしない限りダッシュボード ページにアクセスできなくなります。

`authorized`コールバックは、リクエストがNext.js Middleware経由でページにアクセスすることを許可されているかどうかを確認するために使用されます。これはリクエストが完了する前に呼び出され、`auth`および`request`プロパティを持つオブジェクトを受け取ります。`auth`プロパティにはユーザーのセッションが含まれ、`request`プロパティには受信リクエストが含まれます。

`providers`オプションは、さまざまなログイン オプションをリストする配列です。現時点では、NextAuth 構成を満たすための空の配列です。詳細については、「資格情報プロバイダーの追加」セクションで説明します。

次に、`authConfig`オブジェクトをミドルウェア ファイルにインポートする必要があります。プロジェクトのルートに `middleware.ts`というファイルを作成し、次のコードを貼り付けます。

```JavaScript: /middleware.ts
import NextAuth from 'next-auth';
import { authConfig } from './auth.config';

export default NextAuth(authConfig).auth;

export const config = {
  // https://nextjs.org/docs/app/building-your-application/routing/middleware#matcher
  matcher: ['/((?!api|_next/static|_next/image|.*\\.png$).*)'],
};
```

こでは、`authConfig`オブジェクトを使用して NextAuth.js を初期化し、`auth`プロパティをエクスポートしています。また、ミドルウェアの`matcher`オプションを使用して、特定のパスで実行するように指定しています。

このタスクにミドルウェアを採用する利点は、ミドルウェアが認証を検証するまで保護されたルートのレンダリングが開始されず、アプリケーションのセキュリティとパフォーマンスの両方が向上することです。

## パスワードのハッシュ化

パスワードを安全に保存するには、パスワードをハッシュする必要があります。このプロセスにより、パスワードがランダムに見える固定長の文字列に変換され、ハッシュが公開された場合でもセキュリティ層が提供されます。

`seed.js`ファイルでは、パスワードをデータベースに保存する前に`bcrypt`によってハッシュ化していました。再度`bcrypt`を使用すると、ユーザーが入力したパスワードがデータベース内のパスワードと一致するかどうかを比較できます。

ただし、`bcrypt`はNode.js API に依存しており、これはNext.js ミドルウェアでは使用できません。これを解決するには、 `bcrypt`をインポートする別のファイルを作成する必要があります。この新しいファイルはミドルウェア ファイルにインポートされません。

`authConfig`オブジェクトを展開する `auth.ts `という名前の新しいファイルを作成します。

```JavaScript: /auth.ts
import NextAuth from 'next-auth';
import { authConfig } from './auth.config';

export const { auth, signIn, signOut } = NextAuth({
  ...authConfig,
});
```

**知っておくとよいこと:**

認証情報プロバイダーを使用していますが、一般的にはOAuthやeメールなどの代替プロバイダーを使用することをお勧めします。NextAuth.js のドキュメントを参照してください。オプションの完全なリストについては、こちらをご覧ください。

## サインイン機能の追加

`authorize`関数を使用して認証ロジックを処理できます。サーバー アクションと同様に、ユーザーがデータベースに存在するかどうかを確認する前に、電子メールとパスワードを検証するために`zod`使用できます。

```JavaScript: /auth.ts
import { z } from 'zod';

export const { auth, signIn, signOut } = NextAuth({
  ...authConfig,
  providers: [
    // 以下を変更
    Credentials({
      async authorize(credentials) {
        const parsedCredentials = z
          .object({ email: z.string().email(), password: z.string().min(6) })
          .safeParse(credentials);
      },
    }),
  ],
});
```

資格情報を検証した後、データベースからユーザーにクエリを実行する新しい関数`getUser`を作成します。

```JavaScript: /auth.ts
import type { User } from '@/app/lib/definitions';
import bcrypt from 'bcrypt';

async function getUser(email: string): Promise<User | undefined> {
  try {
    const user = await sql<User>`SELECT * FROM users WHERE email=${email}`;
    return user.rows[0];
  } catch (error) {
    console.error('Failed to fetch user:', error);
    throw new Error('Failed to fetch user.');
  }
}

export const { auth, signIn, signOut } = NextAuth({
  ...authConfig,
  providers: [
    Credentials({
      async authorize(credentials) {
        const parsedCredentials = z
          .object({ email: z.string().email(), password: z.string().min(6) })
          .safeParse(credentials);

        if (parsedCredentials.success) {
          const { email, password } = parsedCredentials.data;
          const user = await getUser(email);
          if (!user) return null;
        }

        return null;
      },
    }),
  ],
});
```

次に、`bcrypt.compare`を呼び出してパスワードが一致するかどうかを確認します。

```JavaScript: auth.ts
import bcrypt from 'bcrypt';

// ...

export const { auth, signIn, signOut } = NextAuth({
  ...authConfig,
  providers: [
    Credentials({
      async authorize(credentials) {
        // ...

        if (parsedCredentials.success) {
          const { email, password } = parsedCredentials.data;
          const user = await getUser(email);
          if (!user) return null;
          // 以下を追加
          const passwordsMatch = await bcrypt.compare(password, user.password);

          if (passwordsMatch) return user;
        }

        console.log('Invalid credentials');
        return null;
      },
    }),
  ],
});
```

最後に、パスワードが一致する場合はユーザーを返し、そうでない場合はnullユーザーがログインできないように返します。

## ログインフォームの更新

次に、認証ロジックをログイン フォームに接続する必要があります。`actions.ts`ファイル内に、 `authenticate`という新しいアクションを作成します。このアクションでは、`auth.ts`から`signIn`関数をインポートする必要があります。

```JavaScript: /app/lib/actions.ts
import { signIn } from '@/auth';

// ...

export async function authenticate(
  prevState: string | undefined,
  formData: FormData,
) {
  try {
    await signIn('credentials', Object.fromEntries(formData));
  } catch (error) {
    if ((error as Error).message.includes('CredentialsSignin')) {
      return 'CredentialsSignin';
    }
    throw error;
  }
}
```

`'CredentialsSignin'`エラーがある場合は、適切なエラー メッセージを表示できるように、エラーを返す必要があります。

最後に、`login-form.tsx`コンポーネント内で、Reactの`useFormState` を使用してサーバー アクションを呼び出し、フォーム エラーを処理し、フォームの保留状態を処理するために`useFormStatus`を使用できます。

```JavaScript: /app/ui/login-form.tsx
'use client';

import { useFormState, useFormStatus } from 'react-dom';
import { authenticate } from '@/app/lib/actions';

export default function LoginForm() {
  const [state, dispatch] = useFormState(authenticate, undefined);

  return (
    <form action={dispatch} className="space-y-3">
      <div className="flex-1 rounded-lg bg-gray-50 px-6 pb-4 pt-8">
        <h1 className={`${lusitana.className} mb-3 text-2xl`}>
          Please log in to continue.
        </h1>
        <div className="w-full">
          // ...
        </div>
        <LoginButton />
        <div
          className="flex h-8 items-end space-x-1"
          aria-live="polite"
          aria-atomic="true"
        >
          {state === 'CredentialsSignin' && (
            <>
              <ExclamationCircleIcon className="h-5 w-5 text-red-500" />
              <p className="text-sm text-red-500">Invalid credentials</p>
            </>
          )}
        </div>
      </div>
    </form>
  );
}

function LoginButton() {
  const { pending } = useFormStatus();

  return (
    <Button className="mt-4 w-full" aria-disabled={pending}>
      Log in <ArrowRightIcon className="ml-auto h-5 w-5 text-gray-50" />
    </Button>
  );
}
```

## ログアウト機能の追加

ログアウト機能を`<SideNav />`に追加するには、`auth.ts`の`<form>`要素内で`signOut`関数を呼び出します。

```JavaScript: /app/ui/dashboard/sidenav.tsx
import { signOut } from '@/auth';

export default function SideNav() {
  return (
    <div className="flex h-full flex-col px-3 py-4 md:px-2">
      // ...
      <div className="flex grow flex-row justify-between space-x-2 md:flex-col md:space-x-0 md:space-y-2">
        <NavLinks />
        <div className="hidden h-auto w-full grow rounded-md bg-gray-50 md:block"></div>
        <form
          // 以下を追加
          action={async () => {
            'use server';
            await signOut();
          }}
        >
          <button className="flex h-[48px] grow items-center justify-center gap-2 rounded-md bg-gray-50 p-3 text-sm font-medium hover:bg-sky-100 hover:text-blue-600 md:flex-none md:justify-start md:p-2 md:px-3">
            <PowerIcon className="w-6" />
            <div className="hidden md:block">Sign Out</div>
          </button>
        </form>
      </div>
    </div>
  );
}
```

# Chapter16

メタデータの追加

## メタデータとは何ですか?

Web 開発では、メタデータは Web ページに関する追加の詳細を提供します。メタデータは、ページを訪問するユーザーには表示されません。代わりに、ページの HTML 内 (通常は`<head>`要素内) に埋め込まれ、舞台裏で動作します。この隠された情報は、Web ページのコンテンツをより深く理解する必要がある検索エンジンやその他のシステムにとって非常に重要です。

## メタデータはなぜ重要ですか?

メタデータは、Web ページの SEO を強化し、検索エンジンやソーシャル メディア プラットフォームにとって Web ページをよりアクセスしやすく、理解しやすくする上で重要な役割を果たします。適切なメタデータは、検索エンジンが Web ページのインデックスを効果的に作成し、検索結果でのランキングを向上させるのに役立ちます。さらに、Open Graph のようなメタデータにより、ソーシャル メディア上の共有リンクの外観が改善され、コンテンツがユーザーにとってより魅力的で有益なものになります。

## メタデータの種類

メタデータにはさまざまな種類があり、それぞれが独自の目的を果たします。一般的なタイプには次のようなものがあります。

**タイトル メタデータ**: ブラウザー タブに表示される Web ページのタイトルを担当します。これは検索エンジンが Web ページの内容を理解するのに役立つため、SEO にとって非常に重要です。

```HTML:
<title>Page Title</title>
```

**説明メタデータ**: このメタデータは、Web ページのコンテンツの簡単な概要を提供し、多くの場合、検索エンジンの結果に表示されます。

```HTML:
<meta name="description" content="A brief description of the page content." />
```

**キーワード メタデータ**: このメタデータには、Web ページのコンテンツに関連するキーワードが含まれており、検索エンジンがページをインデックスするのに役立ちます。

```HTML:
<meta name="keywords" content="keyword1, keyword2, keyword3" />
```

**Open Graph メタデータ**: このメタデータは、ソーシャル メディア プラットフォームで共有される際の Web ページの表現方法を強化し、タイトル、説明、プレビュー画像などの情報を提供します。

```HTML:
<meta property="og:title" content="Title Here" />
<meta property="og:description" content="Description Here" />
<meta property="og:image" content="image_url_here" />
```

**ファビコン メタデータ**: このメタデータは、ブラウザのアドレス バーまたはタブに表示されるファビコン (小さなアイコン) を Web ページにリンクします。

```HTML:
<link rel="icon" href="path/to/favicon.ico" />
```

## メタデータの追加

Next.js には、アプリケーションのメタデータを定義するために使用できるメタデータ API があります。アプリケーションにメタデータを追加するには、次の 2 つの方法があります。

- 構成ベース: 静的`metadata`オブジェクトまたは動的`generateMetadata`関数を`layout.js`または`page.js`ファイルにエクスポートします。

  - [静的metadata](https://nextjs.org/docs/app/api-reference/functions/generate-metadata#metadata-object)
  - [動的generateMetadata](https://nextjs.org/docs/app/api-reference/functions/generate-metadata#generatemetadata-function)

- ファイルベース: Next.js には、特にメタデータの目的で使用されるさまざまな特殊ファイルがあります。

  - `favicon.ico`、`apple-icon.jpg`、`icon.jpg`: ファビコンとアイコンに使用されます
  - `opengraph-image.jpg`および`twitter-image.jpg`: ソーシャルメディア画像に採用
  - `robots.txt`: 検索エンジンのクロールの手順を説明します。
  - `sitemap.xml`: ウェブサイトの構造に関する情報を提供します

これらのファイルを静的メタデータとして柔軟に使用することも、プロジェクト内でプログラムによって生成することもできます。

これらの両方のオプションを使用すると、Next.js はページに関連する`<head>`要素を自動的に生成します。
