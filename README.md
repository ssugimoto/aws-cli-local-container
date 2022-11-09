# aws-cli-local-container

## インストールされるバージョン
* 2022-11-09 日本時間
```
root ➜ /workspaces $ aws --version
aws-cli/2.8.9 Python/3.9.11 Linux/5.4.72-microsoft-standard-WSL2 exe/x86_64.debian.11 prompt/off
root ➜ /workspaces $ node --version
v16.18.1
root ➜ /workspaces $ python --version
Python 3.8.15
root ➜ /workspaces $ terraform --version
Terraform v1.3.4
on linux_amd64
```



## terraform 使い方例

### EC2インスタンスを作成し起動する
1. 作業ディレクトリ作成
```
mkdir /workspaces/terraform_test
cd /workspaces/terraform_test
```

2. vi main.tf
```
resource "aws_instance" "tes-sugimoto-terraform" {
  ami           = "ami-017c001a88dd93847"
  instance_type = "t2.micro"

  tags = {
    Name      = "sugimoto-terraform",
    CreatedBy = "sugimoto"
    CreatedAt = "2022-11-09 21:00"
  }
}
```

3. terraform init 

```
root ➜ /workspaces/terraform_test $ terraform init

Initializing the backend...

Initializing provider plugins...
- Finding latest version of hashicorp/aws...
- Installing hashicorp/aws v4.38.0...
- Installed hashicorp/aws v4.38.0 (signed by HashiCorp)

Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

4. terraform plan

- aws のクレデンシャル等を設定していない場合は以下のようなエラーになる

```
root ➜ /workspaces/terraform_test $ terraform plan
╷
│ Error: Invalid provider configuration
│ 
│ Provider "registry.terraform.io/hashicorp/aws" requires explicit configuration. Add a provider block to the root module and configure the provider's
│ required arguments as described in the provider documentation.
│ 
╵
╷
│ Error: error configuring Terraform AWS Provider: no valid credential sources for Terraform AWS Provider found.
│ 
│ Please see https://registry.terraform.io/providers/hashicorp/aws
│ for more information about providing credentials.
│ 
│ Error: failed to refresh cached credentials, no EC2 IMDS role found, operation error ec2imds: GetMetadata, request send failed, Get "http://169.254.169.254/latest/meta-data/iam/security-credentials/": dial tcp 169.254.169.254:80: connect: connection refused
│ 
│ 
│   with provider["registry.terraform.io/hashicorp/aws"],
│   on <empty> line 0:
│   (source code not available)
│ 
╵
```

- クレデンシャル設定の一例

```
root ➜ ~/.aws $ pwd
/root/.aws
root ➜ ~/.aws $ ls -l
total 0
-rwxrwxrwx 1 root root 112 Nov  9 10:55 config
-rwxrwxrwx 1 root root 247 Nov  9 10:52 credentials
```

```
root ➜ ~/.aws $ cat config 
[default]
region=ap-northeast-1

[profile develop-my-prof]
region=us-west-1


root ➜ ~/.aws $ cat credentials 
[default]

[develop-my-prof]
aws_access_key_id=AK...
aws_secret_access_key=...
```

- provider.tf を作業ディレクトリに作成して ~/.aws/credentials を使う場合
- 参考 https://registry.terraform.io/providers/hashicorp/aws/latest/docs

```
touch /workspaces/terraform_test/provider.tf
```

vi provider.tf
```
provider "aws" {
  region = "us-west-2"
  profile = "develop-my-prof"
}
```

