# Terraform

- [文法](./grammer.md)

## Installation

```bash
$ brew tap hashicorp/tap
$ brew install hashicorp/tap/terraform
# upgrade
# $ brew upgrade hashicorp/tap/terraform
$ terraform -install-autocomplete  # shellにautocompleteが設定される
```

## Commands

`terraform` コマンドを使って操作する

```bash
$ terraform init  # main.tf の terraform ブロックの設定を見て必要なパッケージをインストール
$ terraform apply  # インフラ構成を反映. 最初に起動する時も変更を反映する時もこのコマンド
$ terraform fmt  # .tf ファイルのフォーマッティング
$ terraform validate  # .tf ファイルの文法チェック
$ terraform show  # インフラ構成の確認
$ terraform state  ...  # インフラ構成をマニュアル操作
$ terraform output  # outputsに登録した値の確認
$ terraform apply -var 'variable_name="..."'  # 変数を上書きしてapply
```

## Usage

1. `.tf` ファイルを書く
    - `main.tf` は必須
1. `terraform init` (`.tf`ファイルに書いた定義から必要なパッケージをインストールしてくる)
    - `.terraform` ディレクトリにインストールされる
    - lockファイルも作成され、パッケージののバージョンを管理してくれる
1. `terraform apply`
    - `terraform.tfstate` ファイルが作成され、ここでインフラ構成の状態管理がされる。配布などに注意
    - 本番環境では、[Terraform Cloud](https://learn.hashicorp.com/tutorials/terraform/cloud-migrate?in=terraform/cloud) へ状態を保存しておくことを推奨
    - 設定を変更しても `terraform apply` で変更を反映できる
1. 利用し終わったら `terraform destroy`

## Store Remote State

ref. [Store Remote State with AWS](https://learn.hashicorp.com/tutorials/terraform/aws-remote?in=terraform/aws-get-started)

- [Terraform CLoud](https://app.terraform.io/app) にログイン
- `main.tf` の`terraform` ブロックに以下を記述

```terraform
terraform {
  backend "remote" {
    organization = "ORGANIZATION_NAME"
    workspaces {
      name = "WORKSPACE_NAME"
    }
  }
  ...
}
```

- AWSを利用する場合、AWSのapp_idとsecret_keyをorganizationのEnvironment Variables に登録
- (ローカルに残っている場合)`.tfstate` ファイルを削除し、`terraform init` & `terraform apply`

## Memo

- aws で利用するためには `aws-cli` をインストール済みである必要がある
- `terraform fmt` でフォーマッティングができる
- `terraform validate` で文法チェックができる
- `terraform show` で現在のインフラの状態を確認できる
  - ここで確認できる情報は各種クラウドベンダーなどから取得している
  - ここで取得できる値(outputs?)を利用して他のインフラの構成を作成することができる
- `terraform state ...` でインフラの状態をマニュアル操作できる
- `terraform apply -var 'variable_name="..."'` で変数を上書きしてデプロイできる。
  - これで環境ごとの設定でデプロイするかんじなはず
  - 実際はコマンドラインにベタ書きしていくのではなく、[Customize Terraform Configuration with Variables.](https://learn.hashicorp.com/tutorials/terraform/variables?in=terraform/configuration-language) に書いてある手法で指定した変数名を保存してデプロイしていく

## References

- [公式](https://www.terraform.io/)
- [チュートリアル](https://learn.hashicorp.com/terraform)
- [provider reference](https://www.terraform.io/docs/providers/index.html)