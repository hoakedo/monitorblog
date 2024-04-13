---
title: "Azure Monitor のリソースにおける、日本語 でリソース名を設定することによる影響"
date: 2024-04-13
tags:
  - AME
toc:
  enabled: true
  min_depth: 1
  max_depth: 4
  list_number: false
---
 

こんにちは、Azure Monitor サポートの山口です。
今回は、Azure Monitor のリソースにて、リソース名を日本語で設定したことによって発生する事象をご案内いたします。

 
#はじめに
サブスクリプション名、リソース グループ名、およびリソース名を日本語で作成することは可能ですが、推奨されておりません。
以下、過去の弊社 Azure サポート チームのブログのリンクもご参照ください。
[jpaztech1.z11.web.core.windows.net/リソースグループ名の制限について](https://jpaztech1.z11.web.core.windows.net/%E3%83%AA%E3%82%BD%E3%83%BC%E3%82%B9%E3%82%B0%E3%83%AB%E3%83%BC%E3%83%97%E5%90%8D%E3%81%AE%E5%88%B6%E9%99%90%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6.html)

リソースの命名規則についての公開情報もございますので、併せてご確認ください。
[リソースの名前付けに関する制限事項 - Azure Resource Manager](https://learn.microsoft.com/ja-jp/azure/azure-resource-manager/management/resource-name-rules)

上記より、リソース名が日本語の場合は製品が正しく動作しない可能性がございますため、Azure リソースの命名には 英数字のみでの設定をご検討いただけますと幸いです
なお、リソース名は基本的に変更することが出来ないため、既に日本語を設定している場合はリソースを再作成いただく必要がございます。

また過去事例として、弊 Monitor サポートにお問い合わせいただく事象や類似事例がない事象が発生した場合に、調査を進めた結果、最終的には日本語のリソース名が製品不具合の要因であったことが事例としてございます。

本記事では、現在確認できている、日本語でリソース名を設定することにより製品が正常に動作しない事例についてご紹介いたします。 

#目次
## 1. Azure Monitor エージェントにおけるログ収集が失敗することがある
## 2. アラート ルールの発報が失敗することがある
## 3. 診断設定が消えることがある


## 1. Azure Monitor エージェントにおけるログ収集が失敗することがある
リソース ID に日本語文字が含まれる場合、正常にログが収集されない場合がございます。 

Azure Monitor エージェントの前提条件として、マネージド ID が有効化されている必要がありますが、日本語文字が含まれることにより、正常に認証が行われず、その結果としてエージェントが正常に動作しない場合がございます。

その結果、エージェントがインストールされ、前提条件を満たしていてもログ、メトリックが収集できないといった事例が確認されております。
 
## 2. アラート ルールの発報が失敗することがある
日本語文字の関係で、アラート ルールが正常に動作せず、しきい値を満たしたにもかかわらず発報されなかった事例を複数確認しております。
なお、本事象は必ず発生するわけではないため、現在正常に動作している場合であっても、今後発生する場合がございます。

そのため、可能であれば同じ条件で日本語を含まないアラート ルールに変更することをご検討ください。

## 3. 診断設定が消えることがある
日本語を含む、英数字以外の文字がリソース名に含まれる場合、診断設定を作成しても設定が消えることがございます。

上記事象については以下の公開情報にも記載がございますとおり、診断設定が英数字以外の文字をサポートしないために発生する事象であり、製品の不具合ではございません。
[resourceID の 非 ASCII 文字が原因で設定が消える]
(https://learn.microsoft.com/ja-jp/azure/azure-monitor/essentials/create-diagnostic-settings?tabs=portal#setting-disappears-because-of-non-ascii-characters-in-resourceid)


※ なお、現在は診断設定名を設定すると以下のようにメッセージが表示されます。
![png1.png](/.attachments/png1-adf95137-df2e-4e5b-a84b-cda3d77d6ad4.png)



ここでは、自動スケーリングの診断設定を例にしてご説明いたします。

<自動スケーリングの診断設定を例>
1. Azure Portal の言語設定が日本語の場合、カスタム自動スケーリングを設定すると、自動スケーリング設定の名前が日本語として作成されます。
![png2.png](/.attachments/png2-9e53c67a-864b-401a-b7fb-1fad4c3d5af5.png)

2. 上記状態で保存し、診断設定を以下のように作成します。
![png3.png](/.attachments/png3-458e1165-79de-4817-8f6d-b967f2ebc5b4.png)


3. 作成に成功し、表示上も正しく作成されたように見えます。
![png3.png](/.attachments/png3-fefeefa1-3da3-4b0f-a5ae-3390c89069ce.png)


4. 1 時間経過後に確認してみると、診断設定が消えてしまいます。
![png5.png](/.attachments/png5-7a868bac-e081-447e-bde2-ff75ee343c99.png)


例は以上です。
上述のように、日本語文字が含まれる場合、設定が消えてしまう場合がございます。

なお、今回設定している自動スケーリング設定の診断設定を作成する場合、既定で入力される自動スケーリング設定の名前を英数字に入力し直していただく必要があります。

 

#まとめ

今回は Azure Monitor のリソースにおける、日本語 でリソース名を設定することによる影響についてご案内しました。

日本語での設定自体は可能でございますが、使用することにより発生する事象が複数ございますので、英数字での設定をご検討ください。

※本情報の内容（添付文書、リンク先などを含む）は、作成日時点でのものであり、予告なく変更される場合があります。

 
