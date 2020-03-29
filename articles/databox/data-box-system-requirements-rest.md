---
title: Wymagania dotyczące magazynu obiektów blob w usłudze Microsoft Azure Data Box| Dokumenty firmy Microsoft
description: Dowiedz się więcej o obsługiwanych wersjach interfejsów API, zestawów SDK i bibliotek klienckich dla magazynu obiektów blob usługi Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 71e0ebf7d7851ae65a6fba67a1695d755fd98bb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61436498"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Wymagania dotyczące magazynu obiektów blob usługi Azure Data Box

W tym artykule wymieniono wersje interfejsów API platformy Azure, bibliotek klienta platformy Azure i narzędzi obsługiwanych w magazynie obiektów blob pola danych. Magazyn obiektów blob pola danych zapewnia funkcje zarządzania obiektami blob z semantyką spójną z platformą Azure. W tym artykule podsumowano również znane różnice magazynu obiektów blob usługi Azure Data Box z usługami usługi Azure Storage.

Zaleca się dokładne zapoznanie się z informacjami przed nawiązaniem połączenia z magazynem obiektów Blob pola danych, a następnie w razie potrzeby odwołać się do niego.


## <a name="storage-differences"></a>Różnice w pamięci masowej

|     Funkcja                                             |     Azure Storage                                     |     Magazyn obiektów blob usługi Data Box |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure File Storage                                   |    Obsługiwane udziały plików SMB oparte na chmurze              |    Nieobsługiwane      |
|    Szyfrowanie usług danych w restytami                  |    256-bitowe szyfrowanie AES                             |    256-bitowe szyfrowanie AES |
|    Typ konta magazynu                                 |    Konta magazynu obiektów blob ogólnego przeznaczenia i platformy Azure    |    Tylko wersja 1 ogólnego przeznaczenia|
|    Nazwa obiektu blob                                            |    1024 znaki (2 048 bajtów)                     |    880 znaków (1760 bajtów)|
|    Maksymalny rozmiar bloku obiektu blob                              |    4,75 TB (100 MB x 50 000 bloków)                   |    4,75 TB (100 MB x 50 000 bloków) dla usługi Azure Data Box v 1.8.|
|    Maksymalny rozmiar obiektu blob strony                               |    8 TB                                               |    1 TB                   |
|    Rozmiar strony obiektu blob                                  |    512 bajtów                                          |    4 KB                   |

## <a name="supported-api-versions"></a>Obsługiwane wersje interfejsu API

Następujące wersje interfejsów API usługi Azure Storage są obsługiwane z magazynem obiektów blob pola danych:

Azure Data Box 1.8 i dalej

- [2017-11-09](/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](/rest/api/storageservices/version-2017-07-29)
- [17.04.2017](/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](/rest/api/storageservices/version-2015-04-05)|
## <a name="supported-azure-client-libraries"></a>Obsługiwane biblioteki klientów platformy Azure

W przypadku magazynu obiektów blob pola danych istnieją określone biblioteki klienta i określone wymagania dotyczące sufiksu punktu końcowego. Punkty końcowe magazynu obiektów Blob pola danych nie mają pełnej parzystości z najnowszą wersją interfejsu API REST usługi Azure Blob Storage, zobacz [obsługiwane wersje usługi Azure Data Box 1.8.](#supported-api-versions) W przypadku bibliotek klienta magazynu należy pamiętać o wersji zgodnej z interfejsem API REST.

### <a name="azure-data-box-18-onwards"></a>Azure Data Box 1.8 i dalej

| Biblioteka kliencka     |Wersja obsługiwana przez magazyn obiektów Blob data box     | Link   |     Specyfikacja punktu końcowego      |
|--------------------|--------------------------------------------|--------|---------------------------------|
|    .NET                |    9.2.0                                           |    Pakiet Nuget:https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0    <br>Wydanie GitHub:https://github.com/Azure/azure-storage-net/releases/tag/v9.2.0                                                                                                                                                                                               |    plik app.config                 |
|    Java                |    7.0.0                                           |    Pakiet Maven:https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0   <br>Wydanie GitHub:https://github.com/Azure/azure-storage-java/releases/tag/v7.0.0                                                                                                                                                                              |    Konfiguracja ciągu połączenia         |
|    Node.js             |    2.8.3                                           |    Łącze https://www.npmjs.com/package/azure-storage NPM: `npm install azure-storage@2.7.0`(Uruchom: )   <br>Wydanie GitHub:https://github.com/Azure/azure-storage-node/releases/tag/v2.8.3                                                                                                                                                                        |    Deklaracja wystąpienia usługi    |
|    C++                 |    5.2.0                                           |    Pakiet Nuget:https://www.nuget.org/packages/wastorage.v140/5.2.0   <br>Wydanie GitHub:https://github.com/Azure/azure-storage-cpp/releases/tag/v5.2.0                                                                                                                                                                                                     |    Konfiguracja ciągu połączenia         |
|    PHP                 |    1.2.0                                           |    Wydanie GitHub:<br>Wspólne:https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-common   <br>Blob:https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-blob      <br>Zainstaluj za pośrednictwem programu Composer (Aby dowiedzieć się więcej, zobacz szczegóły poniżej).                                                                                                             |    Konfiguracja ciągu połączenia         |
|    Python              |    1.1.0                                           |    Wydanie GitHub:<br>Wspólne:https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common <br>Blob:https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-blob                                                                                                                                                                          |    Deklaracja wystąpienia usługi    |
|    Ruby                |    1.0.1                                           |    Pakiet RubyGems:<br>Wspólne:https://rubygems.org/gems/azure-storage-common/versions/1.0.1   <br>Blob:https://rubygems.org/gems/azure-storage-blob/versions/1.0.1         <br>Wydanie GitHub:<br>Wspólne:https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common   <br>Blob:https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob          |    Konfiguracja ciągu połączenia         |



### <a name="install-php-client-via-composer---current"></a>Zainstaluj klienta PHP za pośrednictwem Composer - prąd

Aby zainstalować za pośrednictwem composer: (weź blob jako przykład).
1. Utwórz plik o nazwie composer.json w katalogu głównym projektu z następującym kodem:

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. Pobierz `composer.phar` do katalogu głównego projektu.

3. Uruchom: php composer.phar zainstalować.

### <a name="endpoint-declaration"></a>Deklaracja punktu końcowego

Punkt końcowy magazynu obiektów Blob usługi Azure data box zawiera dwie części: nazwę regionu i domenę data box. W SDK magazynu obiektów blob pola danych `\<serial no. of the device>.microsoftdatabox.com`domyślnym punktem końcowym jest .  Aby uzyskać więcej informacji na temat punktu końcowego usługi obiektów blob, przejdź do [połącz za pośrednictwem magazynu obiektów Blob pola danych](data-box-deploy-copy-data-via-rest.md).
 
## <a name="examples"></a>Przykłady

### <a name="net"></a>.NET

W przypadku magazynu obiektów blob pola danych sufiks punktu końcowego `app.config` jest określony w pliku:

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

W przypadku magazynu obiektów blob pola danych sufiks punktu końcowego jest określony w konfiguracji ciągu połączenia:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

W przypadku magazynu obiektów blob pola danych sufiks punktu końcowego jest określony w wystąpieniu deklaracji:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

W przypadku magazynu obiektów blob pola danych sufiks punktu końcowego jest określony w konfiguracji ciągu połączenia:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

W przypadku magazynu obiektów blob pola danych sufiks punktu końcowego jest określony w konfiguracji ciągu połączenia:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

W przypadku magazynu obiektów blob pola danych sufiks punktu końcowego jest określony w wystąpieniu deklaracji:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

W przypadku magazynu obiektów blob pola danych sufiks punktu końcowego jest określony w konfiguracji ciągu połączenia:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>Następne kroki

* [Wdrażanie skrzynki danych platformy Azure](data-box-deploy-ordered.md)
