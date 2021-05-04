# Terraformの文法

## Terraform Block

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

## Providers

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

## Resource

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

## Variables

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

## Outputs

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
