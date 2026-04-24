# LAÉSSE 取扱店一覧ページ

## プロジェクト概要
LAÉSSE（化粧品メーカー）の取扱店一覧を表示するランディングページ。
代理店系（エキスパート・認定・代理店・非代理店）と取扱店系（プレステージ・アドバンス・ベーシック・スターター）の全822店舗情報を掲載し、紹介URLを通じて購入導線を提供する。

## Notion プロジェクトページ
- URL: https://www.notion.so/31eaa962599881bb8825e60af85275fd
- 「Notion更新して」と言われたらこのページの進捗ログを更新すること

## GitHub
- リポジトリ: https://github.com/Shuhei0910/laesse-stores.git

## 技術スタック
- HTML / CSS / JavaScript（バニラ、フレームワークなし）
- 単一ファイル構成（`index.html`）
- Google Fonts（Cormorant Garamond）
- レスポンシブ対応

## ディレクトリ構成
```
laesse.stores.260225/
├── index.html                      # メインページ（CSS/JSすべて内包）
├── images/                         # ヒーロー画像など
├── LAÉSSE取扱店一覧_確認用.xlsx      # クライアント確認用Excel（全942件＋個人名除外822件）
└── CLAUDE.md                       # このファイル
```

## 重要な注意点

### 紹介URL（最重要）
- `firstSalonUrl` / `firstPublicUrl` は各店舗固有のURL
- **絶対に間違えてはいけない** — 各店舗の売上に直結する
- フォーマット: `https://member.laesse0715.com/{ID}` / `https://member.laesse0715.com/U{ID}`
- 更新時はExcelのデータとIDベースで厳密マッチングすること

### 店舗データ構造
```javascript
{
  name: '店舗名',
  yomi: 'よみがな',
  prefecture: '都道府県',
  address: '住所',
  mapUrl: 'Google Maps URL',
  rank: 'expert|certified|agency|retailer|prestige|advance|basic|starter',
  area: '地方ブロック（北海道/東北/関東/中部/近畿/中国/四国/九州・沖縄）',
  products: ['cosmetics', 'pinktone', 'jewelry_peel', 'photo'],
  firstSalonUrl: '初回サロン紹介URL',
  firstPublicUrl: '初回一般紹介URL',
  secondSalonUrl: '2回目サロンURL（共通）',
  secondPublicUrl: '2回目一般URL（共通）',
  instagram: 'InstagramURL',
  hotpepper: 'HotPepper/HPのURL',
}
```

### 店舗数内訳（822件）
- 代理店系: エキスパート26 / 認定6 / 代理店49 / 非代理店9
- 取扱店系: プレステージ55 / アドバンス154 / ベーシック469 / スターター54
- 個人名の店舗は除外済み（120件）

### データソース
- 代理店情報はExcelファイル（`代理店URL(2).xlsx`など）で管理されている
- Excel列の「Instagram」と「ホットペッパー/HP」が入れ違いの店舗が存在するので注意（URLのドメインで判別する）
- rankの対応:
  - `エキスパート代理店` → `expert`
  - `認定代理店` → `certified`
  - `代理店` → `agency`
  - `非代理店` → `retailer`（表示名は「非代理店」）
  - `取扱店/ プレステージサロン` → `prestige`
  - `取扱店/ アドバンスサロン` → `advance`
  - `取扱店/ ベーシックサロン` → `basic`
  - `取扱店/ スターターサロン` → `starter`
- 取扱店系は紹介URLがないため、モーダルでは「店舗に直接お問い合わせください」と表示

### クライアント確認用Excel
- `LAÉSSE取扱店一覧_確認用.xlsx` にまとめ版あり
- シート1: 全942件（個人名含む）
- シート2: 個人名除外822件（サイト掲載対象）
- クライアント記入欄（掲載可否・掲載名・URL・備考）は黄色背景
- No.列は `=ROW()-1` で行削除時に自動連番振り直し

### アポストロフィ問題（要注意・過去に障害発生）
- `Nail Salon Rin's` や `Nm'Rose` など、名前にアポストロフィを含む店舗はJavaScript内で `\'` エスケープが必要
- `Men's Daryle` は右シングルクォート（U+2019 `'`）を使用しており、JS的には問題なかったが、スクリプトで通常のアポストロフィ（U+0027 `'`）に変換してしまいエスケープ漏れが発生 → **全店舗が表示不能になる障害を引き起こした**
- **教訓: 右シングルクォート（U+2019）はJSのシングルクォート文字列内で安全。むやみに変換しないこと。変換する場合は必ず `\'` にエスケープすること**
- **構文チェック**: `node --check` は Node 20 で `.html` に対し `ERR_UNKNOWN_FILE_EXTENSION` エラーが出るため使用不可。ブラウザで表示確認 or `grep -c "'"` で引用符の対称性を手動確認すること

### デプロイ
- GitHub Pages: https://shuhei0910.github.io/laesse-stores/
- mainブランチへのプッシュで自動デプロイ
- 反映後もブラウザキャッシュが残る場合あり（Cmd+Shift+Rで確認）

### フィルター機能
- **種別フィルター**: 2段構え（代理店系/取扱店系 → 詳細種別ボタン）
- **エリアフィルター**: 地方ブロック8つ → 都道府県の2段階選択（件数表示付き）
- **商品フィルター**: 化粧品/ピンクトーン/jewelry peel研修/フォト卸
- **キーワード検索**: 店舗名・よみがなで検索

### 表示モード
- **全件表示時**: あいうえお順グループ表示（30件/ページ）
- **エリア/都道府県フィルター適用時**: 都道府県グループ表示（30件/ページ）
- ページネーション付き（← 前へ / ページ数表示 / 次へ →）

### 非表示中の機能
- Google Maps（APIキー未取得）: マップセクション・モーダル内「Google マップで見る」ボタンをコメントアウト中

## スプシ連携・定期更新ルール

### スプシ情報
- スプシID: `14djWlkqvOK7tPwBx4oA47BRn4R_me_TwPP_3OrwxfAU`（Google Sheets ネイティブ形式）
- Sheets API で読み取り・更新（gog sheets getは.xlsxに非対応のためAPI直接使用）

### アクセストークン取得方法（重要）
keychainに保存されているのはrefresh_tokenを含むJSON。`security find-generic-password -s gogcli -w` の値はそのままAPIに使えない。以下の手順でアクセストークンを取得すること：

```python
import json, urllib.request, urllib.parse, subprocess

# 1. keychainからrefresh_token取得
result = subprocess.run(['security', 'find-generic-password', '-s', 'gogcli', '-w'], capture_output=True, text=True)
cred = json.loads(result.stdout.strip())
refresh_token = cred['refresh_token']

# 2. client_id/secret取得
with open('/Users/shuheiyoshida/Library/Application Support/gogcli/credentials.json') as f:
    client_creds = json.load(f)

# 3. アクセストークン発行
data = urllib.parse.urlencode({
    'client_id': client_creds['client_id'],
    'client_secret': client_creds['client_secret'],
    'refresh_token': refresh_token,
    'grant_type': 'refresh_token'
}).encode()
req = urllib.request.Request('https://oauth2.googleapis.com/token', data=data, method='POST')
with urllib.request.urlopen(req) as resp:
    access_token = json.loads(resp.read())['access_token']
```

### シート構成（正式名称）
- **シート1 - 代理店URL**（sheetId=422770561）: 全店舗マスタ（999行）
- **追加取扱**（sheetId=671118267）: 変更・追加の取扱店スタッフ入力欄
- **追加代理店**（sheetId=1874686051）: 変更・追加の代理店スタッフ入力欄
- **Sheet1**（sheetId=1208121793）: 別シート（代理店URLとは別物）
- **取扱店**（sheetId=680282645）: 取扱店専用シート

⚠️ rangeに`'Sheet1!...'`と指定すると別シートが参照される。必ず`'シート1 - 代理店URL!...'`を使うこと

### 更新フロー（「スプシ更新して」と言われたら実行）

1. **シート1（代理店URL）の赤セルを確認**
   - 赤セル = スタッフが修正入力済みのサイン
   - **赤の種類は2パターンある：**
     - 赤文字（foregroundColor.red=1）: 特定列の値が変更された
     - 赤塗りつぶし（backgroundColor.red=1）: 行全体または複数セルが変更された
   - **必ずシート全体を検索すること（A1:P1000 など十分な範囲で）**。200行で切ると漏れが発生する
   - index.html の既存データと突き合わせて変更箇所を特定
   - 修正後、Sheets API batchUpdate で赤を白にクリア・赤文字を黒文字に戻す

2. **追加取扱・追加代理店シートのデータを確認**
   - 行2 = ヘッダー、行3以降 = 実データ（例行も含めすべて処理対象）
   - **個人名の店舗は掲載対象外** → 追加しない、必ず報告する
   - 既存 index.html に同名店舗があっても「スプシの情報が正しい」として反映する
   - 既存データとの不一致は必ず報告してから実施

3. **index.html を修正**
   - name / yomi / address / mapUrl / hotpepper / instagram / firstSalonUrl / firstPublicUrl 等を更新
   - mapUrl は `urllib.parse.quote()` でURL生成
   - 修正後は必ず `node --check` で構文チェック

4. **シート1を更新**（Sheets API values:batchUpdate）
   - 追加取扱・追加代理店の変更内容をシート1の対応行に反映

5. **追加取扱・追加代理店を完全クリア**
   - values:clear（値）＋ batchUpdate（書式）を両方実行
   - **例行（行3）も含めて全行クリア**（シート1反映済みなら何も残さない）
   - ヘッダー行（行2）のみ残す

### 確認・報告ルール
- 個人名が含まれていたら報告（掲載しない）
- 既存データと不一致がある場合は報告 → スプシ情報が正しいとして反映
- こうした確認は毎回行う（スタッフミス早期発見のため）

## 更新履歴
- 2026-03-10: 全822店舗に拡張（92件→822件）、取扱店系4カテゴリ追加、非代理店→取次店に名称変更、種別フィルター2段構え化、エリアフィルター地方ブロック化、URL無し店舗の専用UI追加、個人名120件除外、クライアント確認用Excelまとめ版作成
- 2026-03-10: 非代理店9店舗の紹介URL追加、全67店舗にInstagram/HotPepperリンク追加、Men's Daryleのクォート修正、アポストロフィ変換による全店舗表示不能障害→修正
- 2026-03-12: 取次店→非代理店に表示名修正、エリアフィルターを地方→都道府県の2段階選択に変更、Google Maps非表示（APIキー未設定）、ページネーション追加（30件/ページ）、全件表示はかな順・フィルター時は都道府県グループに切り替え
- 2026-04-02: スプシ連携更新フロー確立。住所修正・店舗名変更・URL更新 計6件反映（Beauty Salon gloｗ./Re:born Beauty Salon Neige./サロンニネ/ivy care/Total Beauty Salon Luana/Bellissima）
- 2026-04-03: 店舗情報2件反映。Luce.MBelChic（兵庫県）→LIY に改名・yomi更新。LIY（大阪府）住所変更（野中2-7-6 → 春日丘1-3-20 グレイス春日303）。シート1の赤文字（行82-93 列C）を黒文字に修正。赤塗り（行750-751）を白にクリア。スプシ検索範囲を全行対象に要注意と記録
- 2026-04-08: 店舗住所2件更新。M.clair（山形県）住所変更（東置賜郡高畠町→米沢市金池7-5-21）。Beauty Salon gloｗ.（青森県）住所からフェリシア203を削除。赤塗り行468・936をクリア。アクセストークン取得フロー・シート正式名称をCLAUDE.mdに追記
- 2026-04-09: Skin Care Salon AQUA（神奈川）店舗名・よみがな修正（旧: 株式会社LAESSE Creavia）。スプシ77行目とindex.htmlの不一致を手動チェックで発見。node --check が .html非対応であることを確認しCLAUDE.md修正。keychainコマンドの -a フラグ不要であることも修正
