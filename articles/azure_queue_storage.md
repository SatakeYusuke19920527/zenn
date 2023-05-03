---
title: 'Azure Queue Storage について'
emoji: '📗'
type: 'tech'
topics: ['Azure', 'AZ-204', 'Service Bus']
published: true
---

## Azure Queue Storage の概要

概要は以下。

- 多数のメッセージを格納できるやつ。
- HTTP もしくは HTTPS で認証された呼び出しが可能
  以下みたいに呼び出せる
  > https://<storage account>.queue.core.windows.net/<queue>
- 最大サイズは 64KB
- 非同期的な処理用に作業のバックログを作成するために使用される。

> Azure Queue storage は、多数のメッセージを格納するためのサービスです。 メッセージには、HTTP または HTTPS を使用して、認証された呼び出しを介して世界中のどこからでもアクセスできます。 キュー メッセージの許容される最大サイズは 64 KB です。 キューには、ストレージ アカウントの総容量の上限を超えない限り、数百万のメッセージを含めることができます。 キューは通常、非同期的な処理用に作業のバックログを作成するために使用されます。

## 機能詳細

#### 使い方

QueueClient クラスを使用すると、Queue Storage に格納されているキューを取得できます。 サービス クライアントを作成する方法の 1 つを次に示します。

- インスタンス作成

```c#
QueueClient queueClient = new QueueClient(connectionString, queueName);
```

- キューの作成

```c#
// Get the connection string from app settings
string connectionString = ConfigurationManager.AppSettings["StorageConnectionString"];

// Instantiate a QueueClient which will be used to create and manipulate the queue
QueueClient queueClient = new QueueClient(connectionString, queueName);

// Create the queue
queueClient.CreateIfNotExists();
```

- キューの挿入

```c#
// Get the connection string from app settings
string connectionString = ConfigurationManager.AppSettings["StorageConnectionString"];

// Instantiate a QueueClient which will be used to create and manipulate the queue
QueueClient queueClient = new QueueClient(connectionString, queueName);

// Create the queue if it doesn't already exist
queueClient.CreateIfNotExists();

if (queueClient.Exists())
{
    // Send a message to the queue
    queueClient.SendMessage(message);
}
```

- 次のメッセージをピークする

```c#
// Get the connection string from app settings
string connectionString = ConfigurationManager.AppSettings["StorageConnectionString"];

// Instantiate a QueueClient which will be used to manipulate the queue
QueueClient queueClient = new QueueClient(connectionString, queueName);

if (queueClient.Exists())
{
    // Peek at the next message
    PeekedMessage[] peekedMessage = queueClient.PeekMessages();
}
```

- キューに配置されたメッセージの内容を変更する

```c#
// Get the connection string from app settings
string connectionString = ConfigurationManager.AppSettings["StorageConnectionString"];

// Instantiate a QueueClient which will be used to manipulate the queue
QueueClient queueClient = new QueueClient(connectionString, queueName);

if (queueClient.Exists())
{
    // Get the message from the queue
    QueueMessage[] message = queueClient.ReceiveMessages();

    // Update the message contents
    queueClient.UpdateMessage(message[0].MessageId,
            message[0].PopReceipt,
            "Updated contents",
            TimeSpan.FromSeconds(60.0)  // Make it invisible for another 60 seconds
        );
}
```

- 次のメッセージをデキューする

```c#
// Get the connection string from app settings
string connectionString = ConfigurationManager.AppSettings["StorageConnectionString"];

// Instantiate a QueueClient which will be used to manipulate the queue
QueueClient queueClient = new QueueClient(connectionString, queueName);

if (queueClient.Exists())
{
    // Get the next message
    QueueMessage[] retrievedMessage = queueClient.ReceiveMessages();

    // Process (i.e. print) the message in less than 30 seconds
    Console.WriteLine($"Dequeued message: '{retrievedMessage[0].Body}'");

    // Delete the message
    queueClient.DeleteMessage(retrievedMessage[0].MessageId, retrievedMessage[0].PopReceipt);
}
```

- キューの長さを取得する

```c#
/// Instantiate a QueueClient which will be used to manipulate the queue
QueueClient queueClient = new QueueClient(connectionString, queueName);

if (queueClient.Exists())
{
    QueueProperties properties = queueClient.GetProperties();

    // Retrieve the cached approximate message count.
    int cachedMessagesCount = properties.ApproximateMessagesCount;

    // Display number of messages.
    Console.WriteLine($"Number of messages in queue: {cachedMessagesCount}");
}
```

- キューを削除する

```c#
/// Get the connection string from app settings
string connectionString = ConfigurationManager.AppSettings["StorageConnectionString"];

// Instantiate a QueueClient which will be used to manipulate the queue
QueueClient queueClient = new QueueClient(connectionString, queueName);

if (queueClient.Exists())
{
    // Delete the queue
    queueClient.Delete();
}
```

## どこで使うの？

Azure Queue Storage は、分散型アプリケーションで使用される、高度にスケーラブルなメッセージング システムです。以下に、Azure Queue Storage を使用する一般的な具体例をいくつか紹介します。

- ワーカープールの管理
  Azure Queue Storage を使用することで、スケーラブルなワーカープールを構築できます。例えば、複数のワーカーがジョブキューからジョブを取得し、それらを処理することができます。ワーカーが完了したジョブは、別のキューに返されます。これにより、ジョブの処理が失敗した場合には、他のワーカーが再試行することができます。

- メッセージングシステム
  Azure Queue Storage は、メッセージングシステムとして使用することができます。たとえば、顧客からの注文を処理するシステムを構築する場合、注文を含むメッセージをキューに入れ、それを順番に処理することができます。

- ログキュー
  Azure Queue Storage は、ログキューとして使用することができます。アプリケーションがログメッセージを生成する場合、ログメッセージをキューに入れ、他のプロセスがログを処理することができます。これにより、アプリケーションの性能が向上するとともに、アプリケーションの状態を監視することができます。

- 通知システム
  Azure Queue Storage は、通知システムとして使用することができます。たとえば、顧客が注文を完了した場合、キューに通知メッセージを入れることができます。別のシステムがこれを監視し、顧客に確認のメッセージを送信することができます。
