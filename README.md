# renovate-config

a2-ito の全リポジトリで共有する Renovate の設定。

## ファイル

| ファイル | 役割 |
| --- | --- |
| `default.json` | 基本ルール。各リポジトリの `renovate.json` から `github>a2-ito/renovate-config` で読み込む |
| `org-inherited-config.json` | 現在は効いていない（後述） |

## 使い方

各リポジトリの `renovate.json` で読み込む。

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>a2-ito/renovate-config"]
}
```

`renovate.json` の無いリポジトリには基本ルールが効かない。

## 基本ルール

- 毎週月曜 9 時 (JST) より前に PR を作る
- minor / patch はすべて 1 つの PR にまとめ、major は個別の PR にする
- Terraform（`*.tf` の provider・module・`required_version`、`.terraform-version`、Terragrunt、TFLint プラグイン）の minor / patch は、plan を確認しやすいように別の 1 つの PR にまとめる
- PR には `dependencies` ラベルを付ける。脆弱性の修正 PR は時間を問わず即座に作り、`security-dependency-update` も付ける
- 上流が未対応のため、`eslint` は 10 未満に留める
- Dependency Dashboard（更新の一覧を載せた Issue）を各リポジトリに作る

## リポジトリごとの上書き

共通ルールには、全リポジトリに当てはまるものだけを置く。特定のリポジトリの事情による制限
（例: cloud-scope の `typescript` を 7 未満に留める）は、そのリポジトリの `renovate.json` に書く。

そのリポジトリだけのルールは、`extends` と同じ `renovate.json` に書く。基本ルールの上に重ねて適用される。

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>a2-ito/renovate-config"],
  "packageRules": [
    {
      "matchUpdateTypes": ["major"],
      "enabled": false
    }
  ]
}
```

## org-inherited-config.json は効いていない

Renovate には、同じオーナーの `renovate-config` にある `org-inherited-config.json` を
全リポジトリに継承させる仕組み（inherited config）がある。これを使えば各リポジトリに
`renovate.json` を置かずに済むはずだった。

実際には読まれていない。Mend のジョブログ（2026-09-23）を見ると、Mend が渡す
グローバル設定に `inheritConfig` が無く、このリポジトリを読みにいった形跡も無い。
Organization ではなく個人アカウントにインストールしているためと考えている。

ファイルは害が無いので残している。Mend 側で有効になったら、`renovate.json` を
置いていないリポジトリにも基本ルールが効くようになる。

## 検証

```bash
npx --package renovate -- renovate-config-validator --strict --no-global default.json
```

`org-inherited-config.json` を同じように検証すると、`onboarding` と `requireConfig` が「global 専用のオプション」としてエラーになる。validator がこのファイルを通常のリポジトリ設定として扱うためで、継承設定の中では両方とも使える。
