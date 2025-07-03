---
template: permanent
title: Shiniseチームconfig
date_captured: 2025-07-02
source: 
tags:
  - Shinise
status: pending
---


#Shinise 
## Argo CD

[https://argocd.shinise.local](https://argocd.shinise.local)
```
# login user name
admin

# password
FHxvB7GfFDsdAFzF
```

## PostgreSQL
```
# shinise
psql 

# 営業在庫

```


## Rabbit MQ
[http://10.11.15.233:15672/](http://10.11.15.233:15672/) 
```
# login user name
admin 

# password
admin
```

## Grafana
[http://db-monitoring.shinise.local/login](http://db-monitoring.shinise.local/login) 
```
# login user name
admin 

# login user name
prom-operator
```


## CoreSaver
```
# ssh command
ssh root@10.2.3.210 

# password
20047kub

# 数理技研が作ったコマンド
to_staging_env

# podにbashで入る
k exec -it tc-db-0-0 bash 

# オリジナルクエリ
tmdb_search 0.MtOrg code=27 org_typeid=5
```
- 0.\*はマスタを表すらしい。大体0, 1で事足りる（李峰さん曰く）

GCPのprojectは`tcmd`



# 環境変数避難

```

export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion

[ -f "/Users/kodamaitsuki/.ghcup/env" ] && . "/Users/kodamaitsuki/.ghcup/env" # ghcup-env
# The next line updates PATH for the Google Cloud SDK.
if [ -f '/Users/kodamaitsuki/google-cloud-sdk/path.zsh.inc' ]; then . '/Users/kodamaitsuki/google-cloud-sdk/path.zsh.inc'; fi

# The next line enables shell command completion for gcloud.
if [ -f '/Users/kodamaitsuki/google-cloud-sdk/completion.zsh.inc' ]; then . '/Users/kodamaitsuki/google-cloud-sdk/completion.zsh.inc'; fi

# Orbstack
export PATH="/opt/orbstack/bin:$PATH"

# Shinise
export HTTP_PORT=8081
export QUARKUS_PROFILE=dev,rec,local
export DEVELOPER_NAME=stock-service-test

# GKE-DB
export DB_HOST_MASTER=db-common.shinise.local
export DB_HOST_REPLICA=db-common.shinise.local
export GCE_DB_HOST_MASTER=db-common.shinise.local
export DB_BASE_URL=jdbc:postgresql://db-common.shinise.local:5432/
export DB_READ_BASE_URL=jdbc:postgresql://db-common.shinise.local:5432/
export DB_USERNAME=shinise
export DB_PASSWORD=neY66EnG
export DB_MIGRATE=validate
export DB_HOST=db-common.shinise.local

# RabbitMQ
export RABBITMQ_HOST=localhost
export RABBITMQ_PORT=5672
export RABBITMQ_USERNAME=guest
export RABBITMQ_PASSWORD=guest

# Shinise
#export ENV=hiraishi
export CORESAVER_JOURNAL_POD_URL=http://app.shinise.local:9008/journal-import

# DBバックアップ設定
export ENV=dev
export BACKUP_STANZA=db-business3-cluster
export ENABLE_ASYNC_ARCHIVE_WAL=false

# PostgreSQL
export POSTGRES_USER=postgres
export POSTGRES_PASSWORD=",iaS~4;F8hBp."

# mise
export PATH=$PATH:/Users/kodamaitsuki/.local/share/mise/installs/quarkus/3.8.5/bin
export PATH=$PATH:/Users/kodamaitsuki/.local/share/mise/installs/quarkus/2.16.4.Final/bin


# Load custom functions
for file in ~/.zsh/*.zsh; do
  source "$file"
done

#kubernates
alias k=kubectl
alias kc=kubectx
alias kn=kubens

# Linux Command
alias ls="eza -F"
alias ll="eza -al --git --icons"
eval "$(mise activate zsh)"

```