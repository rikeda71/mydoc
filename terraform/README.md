# Terraform

## Installation

```bash
$ brew tap hashicorp/tap
$ brew install hashicorp/tap/terraform
# upgrade
# $ brew upgrade hashicorp/tap/terraform
$ terraform -install-autocomplete  # shellにautocompleteが設定される
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

## Memo

- aws で利用するためには `aws-cli` をインストール済みである必要がある
- `terraform fmt` でフォーマッティングができる
- `terraform validate` で文法チェックができる
- `terraform show` で現在のインフラの状態を確認できる
  - ここで確認できる情報は各種クラウドベンダーなどから取得している
  - ここで取得できる値(outputs?)を利用して他のインフラの構成を作成することができる
- `terraform state ...` でインフラの状態をマニュアル操作できる
- `terraform apply -var 'variable_name="..."'` で変数名を上書きしてデプロイできる。
  - これで環境ごとの設定でデプロイするかんじなはず
  - 実際はコマンドラインにベタ書きしていくのではなく、[Customize Terraform Configuration with Variables.](https://learn.hashicorp.com/tutorials/terraform/variables?in=terraform/configuration-language) に書いてある手法で指定した変数名を保存してデプロイしていく

## 文法

### Terraform Block

- `terraform` ブロックの `required_providers` にデプロイ先にどの媒体を使うか指定する（AWS, GCP, docker etc...）
  - `terraform` ブロックは terraform の設定を書く(ここに必要なプロバイダー指定を含む)
- `source` は、オプションのホスト名、名前空間、およびプロバイダータイプを定義
  - `source` に指定したプロバイダーはデフォルトでは [Terraform Registory](https://registry.terraform.io/) からインストールされる
  - `hashicorp/aws` は `registry.terraform.io/hashicorp/aws` を指す
- `version` は source のバージョンを指定するためのもの。任意だが、指定しておくことが推奨。指定されなければ latest version がインストールされる

```terraform
terraform {
    required_providers {
        aws = {
            source = "hashicorp/aws"
            version = "~> 3.27"
        }
    }
    required_version = ">= 0.14.9"
}
```

### Providers

- `provider` ブロックには `terraform` ブロックで指定した provider の設定を記述する
- `aws` provider の場合は terraform が裏側で利用する aws cli の設定を記述する
- provider を複数定義することもできる。例えば、EC2 とモニタリングツールで設定を分けて定義することもできる

```terraform
provider "aws" {
    profile = "default"
    region = "us-west-2"
}
```

```terraform
provider "google" {

  credentials = file("<NAME>.json")

  project = "<PROJECT_ID>"
  region  = "us-central1"
  zone    = "us-central1-c"
}
```

### Resource

- `resource` ブロックはインフラ構成の定義を記述する
- `resource` の後ろにある2つの文字列はそれぞれリソースの種類(`"aws_instance"`)とリソースの名前を表す（`"aws_server"`）
  - リソースの種類の prefix には provider名 (aws) が付与される
  - リソースの種類と名前で一意のIDになる（aws_instance.aws_server）
- `resource` ブロックにはリソース構成に利用する引数が与えられる
  - マシンサイズ、ディスクイメージ名、vpcidなどを含める
  - 引数については [provider reference](https://www.terraform.io/docs/providers/index.html) を確認して設定する

```terraform
resource "aws_instance" "app_server" {
    ami = "ami-830c94e3"
    instance_type = "t2.micro"

    tags = {
        Name = "ApplicationServerInstance"
    }
}
```

### Variables

- __`variables.tf`__ に定義することができる
- `variable` ブロックを使うことで変数などを再利用・環境によってへんかさせることができる
- `variable` の後ろにつけた名前は変数名になる（`"instance_name"`）
- 変数名を変えたら新しいサーバが立ち上がった（apply しなおしたので注意）
- `terraform apply -var 'variable_name="..."` で変数名はデフォルトから上書きできる
  - 下の例だと `terraform apply -var 'instance_name="..."'` のように実行する
  - 現場では `-var` の後ろにベタ書きはしない

```terraform
# variables.tf
variable "instance_name" {
  description = "Value of the Name tag for the EC2 instance"
  type        = string
  default     = "SoftCodedInstanceName"
}

# main.tf
resource "aws_instance" "app_server" {
   ami           = "ami-08d70e59c07c61a3a"
   instance_type = "t2.micro"

   tags = {
     Name = var.instance_name
   }
 }
```

### Outputs

- __`outputs.tf`__ に定義することができる
- インフラ構成の設定値をterraform側に保存できる
- この値を利用して、他のインフラ構成に接続するための設定を書く

```terraform
output "instance_id" {
  description = "ID of the EC2 instance"
  value = aws_instance.app_server.id
}

output "instance_public_ip" {
  description = "Public IP address of the EC2 instance"
  value = aws_instance.app_server.public_ip
}
```

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

## References

- [公式](https://www.terraform.io/)
- [チュートリアル](https://learn.hashicorp.com/terraform)
- [provider reference](https://www.terraform.io/docs/providers/index.html)