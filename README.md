# renovate-config

a2-ito の全リポジトリで共有する Renovate の設定。

## ファイル

| ファイル | 役割 |
| --- | --- |
| `org-inherited-config.json` | Mend の Renovate App が全リポジトリに継承させる設定。`default.json` を読み込み、各リポジトリに `renovate.json` が無くても動くようにする |
| `default.json` | 基本ルール。`github>a2-ito/renovate-config` で参照できる |

`org-inherited-config.json` を読ませるには、このリポジトリ名が `renovate-config` でなければならない。

## 基本ルール

- 毎週月曜 9 時 (JST) より前に PR を作る
- minor / patch はすべて 1 つの PR にまとめ、major は個別の PR にする
- PR には `dependencies` ラベルを付ける。脆弱性の修正 PR は時間を問わず即座に作り、`security-dependency-update` も付ける
- 上流が未対応のため、`eslint` は 10 未満、`typescript` は 7 未満に留める
- Dependency Dashboard（更新の一覧を載せた Issue）を各リポジトリに作る

## リポジトリごとの上書き

そのリポジトリだけのルールは、各リポジトリの `renovate.json` に書く。継承された設定の上に重ねて適用される。

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "packageRules": [
    {
      "matchUpdateTypes": ["major"],
      "enabled": false
    }
  ]
}
```

継承された設定の中のプリセットは、リポジトリ側の `ignorePresets` では外せない。

## 検証

```bash
npx --package renovate -- renovate-config-validator --strict --no-global default.json
```

`org-inherited-config.json` を同じように検証すると、`onboarding` と `requireConfig` が「global 専用のオプション」としてエラーになる。validator がこのファイルを通常のリポジトリ設定として扱うためで、継承設定の中では両方とも使える。
