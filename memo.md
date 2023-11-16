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
