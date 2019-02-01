---
title: Wprowadzenie do narzędzia programistyczne dotyczące magazynu usługi Azure Stack | Dokumentacja firmy Microsoft
description: Wskazówki, aby rozpocząć pracę przy użyciu narzędzia programistyczne dotyczące magazynu usługi Azure Stack
services: azure-stack
author: mattbriggs
ms.author: mabrigg
ms.date: 12/03/2018
ms.topic: get-started-article
ms.service: azure-stack
manager: femila
ms.reviewer: xiaofmao
ms.lastreviewed: 12/03/2018
ms.openlocfilehash: 857e12664defb1fc0106dd0d3012b77a89f826c2
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55495109"
---
# <a name="get-started-with-azure-stack-storage-development-tools"></a>Wprowadzenie do narzędzia programistyczne dotyczące magazynu usługi Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Microsoft Azure Stack zapewnia zestaw usług magazynu, który zawiera obiekt blob, tabela i queue storage.

Umożliwia tego artykułu jako przewodnika rozpoczęcie korzystania z narzędzia programistyczne dotyczące magazynu usługi Azure Stack. W odpowiednich samouczki usługi Azure storage można znaleźć więcej szczegółowych informacji i przykładowy kod.

> [!NOTE]
> Są to znane różnice między magazynu usługi Azure Stack i Azure storage, w tym szczególne wymagania dotyczące każdej platformy. Na przykład istnieje biblioteki określonego klienta i określonego punktu końcowego sufiks wymagania dotyczące usługi Azure Stack. Aby uzyskać więcej informacji, zobacz [magazynu usługi Azure Stack: Różnice i zagadnienia](azure-stack-acs-differences.md).

## <a name="azure-client-libraries"></a>Biblioteki klienckie systemu Azure

Dla biblioteki klienta magazynu należy pamiętać o wersji, która jest zgodna z interfejsu API REST. Należy także określić punkt końcowy usługi Azure Stack w kodzie.

### <a name="1811-update-or-newer-versions"></a>1811 aktualizacji lub nowsze wersje

| Biblioteka kliencka | Usługa Azure Stack obsługiwana wersja | Link | Specyfikacja punktu końcowego |
|----------------|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET | 8.7.0 | Pakiet Nuget:<br>https://www.nuget.org/packages/WindowsAzure.Storage/8.7.0<br> <br>Wersja usługi GitHub:<br>https://github.com/Azure/azure-storage-net/releases/tag/v8.7.0 | pliku App.config |
| Java | 6.1.0 | Pakiet maven:<br>http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0<br> <br>Wersja usługi GitHub:<br>https://github.com/Azure/azure-storage-java/releases/tag/v6.1.0 | Ustawienia parametrów połączenia |
| Node.js | 2.7.0 | NPM link:<br>https://www.npmjs.com/package/azure-storage<br>(Uruchom: `npm install azure-storage@2.7.0`)<br> <br>Wersja usługi GitHub:<br>https://github.com/Azure/azure-storage-node/releases/tag/v2.7.0 | Deklaracja wystąpienia usługi |
| C++ | 3.1.0 | Pakiet Nuget:<br>https://www.nuget.org/packages/wastorage.v140/3.1.0<br> <br>Wersja usługi GitHub:<br>https://github.com/Azure/azure-storage-cpp/releases/tag/v3.1.0 | Ustawienia parametrów połączenia |
| PHP | 1.0.0 | Wersja usługi GitHub:<br>Wspólne: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-common<br>Obiekt blob: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-blob<br>Kolejka:<br>https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-queue<br>Tabela: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-table<br> <br>Instalowanie za pomocą Composer (Aby uzyskać więcej informacji, [Zobacz szczegóły poniżej](#install-php-client-via-composer---current).) | Ustawienia parametrów połączenia |
| Python | 1.0.0 | Wersja usługi GitHub:<br>Wspólne:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common<br>Blob:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-blob<br>Kolejka:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-queue | Deklaracja wystąpienia usługi |
| Ruby | 1.0.1 | Pakiet RubyGems:<br>Wspólne:<br>https://rubygems.org/gems/azure-storage-common/versions/1.0.1<br>Obiekt blob: https://rubygems.org/gems/azure-storage-blob/versions/1.0.1<br>Kolejka: https://rubygems.org/gems/azure-storage-queue/versions/1.0.1<br>Tabela: https://rubygems.org/gems/azure-storage-table/versions/1.0.1<br> <br>Wersja usługi GitHub:<br>Wspólne: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common<br>Obiekt blob: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob<br>Kolejka: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-queue<br>Tabela: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-table | Ustawienia parametrów połączenia |

#### <a name="install-php-client-via-composer---current"></a>Zainstaluj klienta PHP za pośrednictwem Composer — bieżące

Aby zainstalować za pośrednictwem Composer (kompozytor): (potrwać obiektu blob jako przykład).

1. Utwórz plik o nazwie **composer.json** w katalogu głównym projektu przy użyciu następującego kodu:

    ```json
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.2.0"
      }
    }
    ```

2. Pobierz [composer.phar](http://getcomposer.org/composer.phar) w katalogu głównym projektu.
3. Uruchom: `php composer.phar install`.

### <a name="previous-versions-1802-to-1809-update"></a>Poprzednie wersje (1802 do 1809 aktualizacja)

|Biblioteka kliencka|Usługa Azure Stack obsługiwana wersja|Link|Specyfikacja punktu końcowego|
|---------|---------|---------|---------|
|.NET     |6.2.0|Pakiet Nuget:<br>[https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0](https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0)<br><br>Wersja usługi GitHub:<br>[https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1](https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1)|pliku App.config|
|Java|4.1.0|Pakiet maven:<br>[http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0)<br><br>Wersja usługi GitHub:<br> [https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0](https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0)|Ustawienia parametrów połączenia|
|Node.js     |1.1.0|NPM link:<br>[https://www.npmjs.com/package/azure-storage](https://www.npmjs.com/package/azure-storage)<br>(Uruchom: `npm install azure-storage@1.1.0)`<br><br>Wersja usługi GitHub:<br>[https://github.com/Azure/azure-storage-node/releases/tag/1.1.0](https://github.com/Azure/azure-storage-node/releases/tag/1.1.0)|Deklaracja wystąpienia usługi||C++|2.4.0|Pakiet Nuget:<br>[https://www.nuget.org/packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>Wersja usługi GitHub:<br>[https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Ustawienia parametrów połączenia|
|C++|2.4.0|Pakiet Nuget:<br>[https://www.nuget.org/packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>Wersja usługi GitHub:<br>[https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Ustawienia parametrów połączenia|
|PHP|0.15.0|Wersja usługi GitHub:<br>[https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0](https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0)<br><br>Instalowanie za pomocą Composer (Zobacz szczegóły poniżej)|Ustawienia parametrów połączenia|
|Python     |0.30.0|Pakiet PIP:<br> [https://pypi.python.org/pypi/azure-storage/0.30.0](https://pypi.python.org/pypi/azure-storage/0.30.0)<br>(Uruchom: `pip install -v azure-storage==0.30.0)`<br><br>Wersja usługi GitHub:<br> [https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0](https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0)|Deklaracja wystąpienia usługi|
|Ruby|0.12.1<br>Wersja zapoznawcza|Pakiet RubyGems:<br> [https://rubygems.org/gems/azure-storage/versions/0.12.1.preview](https://rubygems.org/gems/azure-storage/versions/0.12.1.preview)<br><br>Wersja usługi GitHub:<br> [https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1](https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1)|Ustawienia parametrów połączenia|

#### <a name="install-php-client-via-composer---previous"></a>Zainstaluj klienta PHP za pośrednictwem Composer — poprzednie

Aby zainstalować za pośrednictwem Composer (kompozytor): (Wypełnij obiektów blob przykład).

1. Utwórz plik o nazwie **composer.json** w katalogu głównym projektu przy użyciu następującego kodu:

  ```json
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.0.0"
      }
    }
  ```

2. Pobierz [composer.phar](http://getcomposer.org/composer.phar) w katalogu głównym projektu.
3. Uruchom: `php composer.phar install`.

## <a name="endpoint-declaration"></a>Deklarację punktu końcowego

Punkt końcowy usługi Azure Stack zawiera dwie części: Nazwa regionu i domeny usługi Azure Stack.
W usłudze Azure Stack Development Kit jest domyślny punkt końcowy **local.azurestack.external**.
Jeśli nie masz pewności o punkt końcowy usługi, skontaktuj się z administratorem chmury.

## <a name="examples"></a>Przykłady

### <a name="net"></a>.NET

Dla usługi Azure Stack sufiks punktu końcowego jest określona w pliku app.config:

```xml
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=local.azurestack.external;" />
```

### <a name="java"></a>Java

Dla usługi Azure Stack sufiks punktu końcowego jest określony w ustawieniach parametry połączenia:

```java
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=local.azurestack.external";
```

### <a name="nodejs"></a>Node.js

Dla usługi Azure Stack sufiks punktu końcowego jest określony w wystąpieniu deklaracji:

```javascript
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob.local.azurestack.external');
```

### <a name="c"></a>C++

Dla usługi Azure Stack sufiks punktu końcowego jest określony w ustawieniach parametry połączenia:

```cpp
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=local.azurestack.external"));
```

### <a name="php"></a>PHP

Dla usługi Azure Stack sufiks punktu końcowego jest określony w ustawieniach parametry połączenia:

```php
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.local.azurestack.external/;
QueueEndpoint=http:// <storage account name>.queue.local.azurestack.external/;
TableEndpoint=http:// <storage account name>.table.local.azurestack.external/;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Dla usługi Azure Stack sufiks punktu końcowego jest określony w wystąpieniu deklaracji:

```python
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix='local.azurestack.external')
```

### <a name="ruby"></a>Ruby

Dla usługi Azure Stack sufiks punktu końcowego jest określony w ustawieniach parametry połączenia:

```ruby
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=local.azurestack.external
```

## <a name="blob-storage"></a>Blob Storage

Następujące samouczki magazynu obiektów Blob platformy Azure mają zastosowanie do usługi Azure Stack. Należy zwrócić uwagę potrzebę sufiks określonego punktu końcowego usługi Azure Stack, opisane w poprzedniej [przykłady](#examples) sekcji.

* [Rozpoczynanie pracy z usługą Azure Blob Storage przy użyciu platformy .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)
* [Używanie usługi Blob Storage w języku Java](../../storage/blobs/storage-java-how-to-use-blob-storage.md)
* [Używanie usługi Blob Storage w oprogramowaniu Node.js](../../storage/blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Jak używać magazynu obiektów Blob w języku C++](../../storage/blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Używanie usługi Blob Storage w języku PHP](../../storage/blobs/storage-php-how-to-use-blobs.md)
* [Jak używać usługi Azure Blob storage za pomocą języka Python](../../storage/blobs/storage-python-how-to-use-blob-storage.md)
* [Używanie usługi Blob Storage w języku Ruby](../../storage/blobs/storage-ruby-how-to-use-blob-storage.md)

## <a name="queue-storage"></a>Queue Storage

Następujące samouczki magazynu Azure Queue mają zastosowanie do usługi Azure Stack. Należy zwrócić uwagę potrzebę sufiks określonego punktu końcowego usługi Azure Stack, opisane w poprzedniej [przykłady](#examples) sekcji.

* [Rozpoczynanie pracy z usługą Azure Queue Storage przy użyciu platformy .NET](../../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Używanie usługi Queue Storage w języku Java](../../storage/queues/storage-java-how-to-use-queue-storage.md)
* [Używanie usługi Queue Storage w oprogramowaniu Node.js](../../storage/queues/storage-nodejs-how-to-use-queues.md)
* [Jak używać magazynu kolejek w języku C++](../../storage/queues/storage-c-plus-plus-how-to-use-queues.md)
* [Używanie usługi Queue Storage w języku PHP](../../storage/queues/storage-php-how-to-use-queues.md)
* [Używanie usługi Queue Storage w języku Python](../../storage/queues/storage-python-how-to-use-queue-storage.md)
* [Używanie usługi Queue Storage w języku Ruby](../../storage/queues/storage-ruby-how-to-use-queue-storage.md)

## <a name="table-storage"></a>Magazyn tabel

Następujące samouczki magazynu tabel Azure mają zastosowanie do usługi Azure Stack. Należy zwrócić uwagę potrzebę sufiks określonego punktu końcowego usługi Azure Stack, opisane w poprzedniej [przykłady](#examples) sekcji.

* [Rozpoczynanie pracy z usługą Azure Table Storage przy użyciu platformy .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Używanie usługi Table Storage w języku Java](../../cosmos-db/table-storage-how-to-use-java.md)
* [How to use Azure Table storage from Node.js (Używanie usługi Azure Table Storage w oprogramowaniu Node.js)](../../cosmos-db/table-storage-how-to-use-nodejs.md)
* [Jak używać magazynu tabel w języku C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Używanie usługi Table Storage w języku PHP](../../cosmos-db/table-storage-how-to-use-php.md)
* [Jak używać magazynu tabel w języku Python](../../cosmos-db/table-storage-how-to-use-python.md)
* [Używanie usługi Table Storage w języku Ruby](../../cosmos-db/table-storage-how-to-use-ruby.md)

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do usługi Microsoft Azure storage](../../storage/common/storage-introduction.md)
