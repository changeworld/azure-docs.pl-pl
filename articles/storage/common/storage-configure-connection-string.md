---
title: Konfigurowanie parametrów połączenia dla usługi Azure Storage | Dokumentacja firmy Microsoft
description: Skonfiguruj parametry połączenia dla konta magazynu platformy Azure. Parametry połączenia zawierają informacje wymagane do uwierzytelniania dostępu do konta magazynu z poziomu aplikacji w czasie wykonywania.
services: storage
documentationcenter: ''
author: craigshoemaker
manager: jeconnoc
editor: tysonn
ms.assetid: ecb0acb5-90a9-4eb2-93e6-e9860eda5e53
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: cshoe
ms.openlocfilehash: dee0fd68ccaf80ff6e44ee10fa24bb19e220d0d5
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
ms.locfileid: "30240833"
---
# <a name="configure-azure-storage-connection-strings"></a>Konfiguracja parametrów połączenia usługi Azure Storage

Parametry połączenia zawiera informacje dotyczące uwierzytelniania wymagane dla aplikacji dostęp do danych na koncie magazynu Azure w czasie wykonywania. Można skonfigurować parametry połączenia do:

* Podłącz do emulatora magazynu Azure.
* Dostęp do konta magazynu na platformie Azure.
* Dostęp do określonych zasobów na platformie Azure za pomocą sygnatury dostępu współdzielonego (SAS).

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="storing-your-connection-string"></a>Przechowywanie parametrów połączenia
Aplikacja musi uzyskać dostępu do parametrów połączenia w czasie wykonywania do uwierzytelniania żądań wysyłanych do usługi Azure Storage. Istnieje kilka opcji do przechowywania parametrów połączenia:

* Uruchamianie aplikacji na pulpicie lub na urządzeniu mogą być przechowywane w ciągu połączenia **app.config** lub **web.config** pliku. Dodaj parametry połączenia do **AppSettings** sekcji w tych plikach.
* Aplikacja działająca w usługi w chmurze platformy Azure można przechowywać w ciągu połączenia [plik schematu (cscfg) konfiguracji usługi Azure](https://msdn.microsoft.com/library/ee758710.aspx). Dodaj parametry połączenia do **appSettings** sekcji pliku konfiguracji usługi.
* Parametry połączenia można użyć bezpośrednio w kodzie. Firma Microsoft zaleca jednak przechowywanie parametrów połączenia w pliku konfiguracji, w większości przypadków.

Przechowywanie parametrów połączenia w pliku konfiguracji ułatwia zaktualizować parametry połączenia w celu przełączania się między emulator magazynu i konto usługi Azure storage w chmurze. Należy zmodyfikować parametry połączenia, aby wskazywał środowiska docelowego.

Można użyć [Microsoft Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) do parametrów połączenia w czasie wykonywania, niezależnie od tego, w którym aplikacja jest uruchomiona.

## <a name="create-a-connection-string-for-the-storage-emulator"></a>Utworzyć parametry połączenia dla emulatora magazynu
[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Aby uzyskać więcej informacji na temat emulator magazynu, zobacz [użyć emulatora magazynu Azure do programowania i testowania](storage-use-emulator.md).

## <a name="create-a-connection-string-for-an-azure-storage-account"></a>Utworzyć parametry połączenia dla konta magazynu platformy Azure
Aby utworzyć parametry połączenia dla konta magazynu Azure, użyj następującego formatu. Wskazuje, czy chcesz się połączyć z kontem magazynu za pośrednictwem protokołu HTTPS (zalecane) lub protokołu HTTP, Zastąp `myAccountName` z nazwą konta magazynu i Zastąp `myAccountKey` kluczem dostępu do konta:

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

Na przykład parametry połączenia mogą wyglądać podobnie do:

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

Mimo że usługa Azure Storage obsługuje protokołów HTTP i HTTPS w parametrach połączenia, *HTTPS jest zdecydowanie zalecane*.

> [!TIP]
> Można znaleźć parametrów połączenia konta magazynu w [portalu Azure](https://portal.azure.com). Przejdź do **ustawienia** > **klucze dostępu** w bloku menu konta magazynu, aby wyświetlić parametry połączenia dla obu kluczy dostępu do podstawowego i pomocniczego.
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Utworzyć parametry połączenia przy użyciu sygnatury dostępu współdzielonego
[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="create-a-connection-string-for-an-explicit-storage-endpoint"></a>Utworzyć parametry połączenia dla punktu końcowego magazynu jawne
Punkty końcowe usługi jawne można określić w ciągu połączenia zamiast domyślnych punktów końcowych. Aby utworzyć parametry połączenia, które określa jawne punktu końcowego, należy określić punkt końcowy usługi ukończenia dla każdej usługi, w tym specyfikacji protokołu (HTTP lub HTTPS (zalecane)), w następującym formacie:

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
FileEndpoint=myFileEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

Jest jeden scenariusz, w którym może chcesz określić jawne punktu końcowego, gdy został zamapowany punktu końcowego magazynu obiektów Blob do [domeny niestandardowej](../blobs/storage-custom-domain-name.md). W takim przypadku można określić niestandardowe punktu końcowego magazynu obiektów Blob w ciągu połączenia. Opcjonalnie można określić domyślne punkty końcowe dla innych usług, jeśli aplikacja korzysta z nich.

Oto przykład parametrów połączenia, które określa jawne punktu końcowego usługi Blob:

```
# Blob endpoint only
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
AccountName=storagesample;
AccountKey=<account-key>
```

W tym przykładzie określa jawne punkty końcowe dla wszystkich usług, w tym domeny niestandardowej dla usługi Blob:

```
# All service endpoints
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
FileEndpoint=https://myaccount.file.core.windows.net;
QueueEndpoint=https://myaccount.queue.core.windows.net;
TableEndpoint=https://myaccount.table.core.windows.net;
AccountName=storagesample;
AccountKey=<account-key>
```

Wartości punktu końcowego w parametrach połączenia są używane do konstruowania żądania identyfikatory URI usługi magazynu i dyktowania formę wszystkie identyfikatory URI, które są zwracane do kodu.

Jeśli zamapowany na domenę niestandardową punktu końcowego magazynu i pominąć ten punkt końcowy z parametrów połączenia, następnie nie będzie mogła użyć tego ciągu połączenia w celu dostępu do danych w tej usłudze w kodzie.

> [!IMPORTANT]
> Wartości punktu końcowego usługi w parametry połączenia muszą być poprawnie sformułowanym identyfikatory URI, w tym `https://` (zalecane) lub `http://`. Ponieważ Magazyn Azure nie obsługuje jeszcze HTTPS dla domen niestandardowych można *musi* określ `http://` dla dowolnego punktu końcowego identyfikator URI, który wskazuje na domenę niestandardową.
>

### <a name="create-a-connection-string-with-an-endpoint-suffix"></a>Utworzyć parametry połączenia z sufiksem punktu końcowego
Aby utworzyć parametry połączenia dla usługi magazynu w regionach lub wystąpień z innym punktem końcowym sufiksów, takich jak dla Chińskiej wersji platformy Azure lub Azure dla instytucji rządowych, użyj następującego formatu ciągu połączenia. Wskazuje, czy chcesz się połączyć z kontem magazynu za pośrednictwem protokołu HTTPS (zalecane) lub protokołu HTTP, Zastąp `myAccountName` z nazwą konta magazynu, należy zastąpić `myAccountKey` z klucz dostępu do konta i Zastąp `mySuffix` wraz z sufiksem identyfikatora URI:

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

Oto przykład parametry połączenia dla usług magazynu w chińskiej wersji platformy Azure:

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>Podczas analizowania parametrów połączenia
[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>Kolejne kroki
* [Użyć emulatora magazynu Azure do programowania i testowania](storage-use-emulator.md)
* [Eksploratory usługi Storage platformy Azure](storage-explorers.md)
* [Przy użyciu sygnatury dostępu współdzielonego (SAS)](storage-dotnet-shared-access-signature-part-1.md)

