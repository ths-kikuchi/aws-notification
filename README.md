# aws-notification
awsに関連する通知処理

## AWS CEのmetricsに関して
```
BlendedCost:
ブレンドされたコストは、AWSリソースの使用に関連する料金の総額です。AWSの異なるリージョン間での料金の均等な分割を示します。

UnblendedCost:
ブレンドされていないコストは、AWSリソースの使用に関連する料金の総額です。この料金は、各リソースが発生させた料金を正確に反映します。

UsageQuantity:
リソースの使用量を示します。例えば、EC2インスタンスの場合、これはインスタンスが実際に実行されていた時間、またはS3バケットの場合、ストレージの使用量などです。

NormalizedUsageAmount:
正規化された使用量は、特定のサービスの単位に正規化された使用量です。これは、異なるサービス間で比較可能なデータを提供します。

AmortizedCost:
会計期間中に発生したコストを、期間全体に均等に分配して表示します。たとえば、RI（Reserved Instance）の支払いを分割して表示する場合に使用されます。

NetAmortizedCost:
指定された期間における、会計期間中の料金の純額を表示します。
```

# KMSに関して
LambdaからLineへメッセージ送信する時に使用するチャネルトークンとUserIdを環境変数に置く必要があり
その変数をKMSにて管理する事を検討した為、メモを記載。

1. kmsにてカスタマーkeyを作成
2. 許可に対象lambdaにアタッチされているロールを指定
3. 対象lambdaにdecryptのポリシーアタッチ
4. lambdaの環境変数に値を設定して、暗号化をクリック
5. 上記プラス「転送時...」にもチェックを入れる
```python
# kmsで暗号化された物を複合化するコード
from base64 import b64decode
def decrypt(encrypt):
    client = boto3.client('kms')
    decode = b64decode(encrypt)
    return client.decrypt(
        CiphertextBlob=decode,
        EncryptionContext={
            'LambdaFunctionName': '関数名'
        }
    )['Plaintext'].decode('utf-8')
```
