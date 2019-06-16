---
title: Konfigurowanie parametrów połączenia dla usługi Azure Storage | Dokumentacja firmy Microsoft
description: Skonfiguruj parametry połączenia dla konta usługi Azure storage. Parametry połączenia zawierają informacje wymagane do autoryzowania dostępu do konta magazynu z poziomu aplikacji w czasie wykonywania.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/12/2017
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ef01cf194ea7a0afea033d2888df946208073e41
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65874109"
---
# <a name="configure-azure-storage-connection-strings"></a>Konfiguracja parametrów połączenia usługi Azure Storage

Parametry połączenia zawiera informacje o uwierzytelnianiu wymagane dla aplikacji na dostęp do danych na koncie usługi Azure Storage w czasie wykonywania. Można skonfigurować parametry połączenia do:

* Połącz z emulatorem usługi Azure storage.
* Dostęp do konta magazynu na platformie Azure.
* Dostęp do określonych zasobów na platformie Azure przy użyciu sygnatury dostępu współdzielonego (SAS).

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="storing-your-connection-string"></a>Przechowywanie parametrów połączenia
Aplikacja ma dostęp do parametrów połączenia w czasie wykonywania, aby autoryzować żądania wysyłane do usługi Azure Storage. Istnieje kilka opcji do przechowywania parametrów połączenia:

* Uruchamianie aplikacji na pulpicie lub na urządzeniu mogą przechowywać parametry połączenia w **app.config** lub **web.config** pliku. Dodaj parametry połączenia do **AppSettings** sekcji w tych plikach.
* Aplikacja działająca w usłudze w chmurze platformy Azure można przechowywać parametry połączenia w [plik schematu (cscfg) konfiguracji usługi platformy Azure](https://msdn.microsoft.com/library/ee758710.aspx). Dodaj parametry połączenia do **appSettings** sekcję pliku konfiguracji usługi.
* Parametry połączenia można użyć bezpośrednio w kodzie. Jednak zaleca się przechowywanie parametrów połączenia w pliku konfiguracji w większości scenariuszy.

Przechowywanie parametrów połączenia w pliku konfiguracji można łatwo zaktualizować parametry połączenia, aby przełączać się między emulatora magazynu i konto magazynu platformy Azure w chmurze. Należy zmodyfikować parametry połączenia, aby wskazywał środowisku docelowym.

Możesz użyć [Microsoft Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) na dostęp do parametrów połączenia w czasie wykonywania, niezależnie od tego, gdzie aplikacja jest uruchomiona.

## <a name="create-a-connection-string-for-the-storage-emulator"></a>Utwórz ciąg połączenia dla emulatora magazynu
[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Aby uzyskać więcej informacji na temat emulatora magazynu, zobacz [korzystanie z emulatora usługi Azure storage do programowania i testowania](storage-use-emulator.md).

## <a name="create-a-connection-string-for-an-azure-storage-account"></a>Utwórz ciąg połączenia dla konta usługi Azure storage
Aby utworzyć parametry połączenia dla konta usługi Azure storage, użyj następującego formatu. Wskazuje, czy chcesz się połączyć z kontem magazynu za pośrednictwem protokołu HTTPS (zalecane) lub HTTP, Zastąp `myAccountName` nazwą konta magazynu, i Zastąp `myAccountKey` swoim kluczem dostępu konta:

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

Na przykład parametry połączenia mogą wyglądać podobnie do:

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

Mimo że usługa Azure Storage obsługuje protokołów HTTP i HTTPS, w ciągu połączenia *zdecydowanie zaleca się HTTPS*.

> [!TIP]
> Można znaleźć parametrów połączenia konta magazynu w [witryny Azure portal](https://portal.azure.com). Przejdź do **ustawienia** > **klucze dostępu** w bloku menu konta magazynu, aby wyświetlić parametry połączenia dla obu kluczy dostępu do podstawowego i pomocniczego.
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Tworzenie parametrów połączenia przy użyciu sygnatury dostępu współdzielonego
[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="create-a-connection-string-for-an-explicit-storage-endpoint"></a>Utwórz ciąg połączenia dla punktu końcowego magazynu jawne
Punkty końcowe usługi jawne można określić w ciągu połączenia zamiast domyślnych punktów końcowych. Aby utworzyć parametry połączenia, który określa jawny punkt końcowy, należy określić punkt końcowy usługi ukończone dla każdej usługi, w tym specyfikacji protokołu (HTTP lub HTTPS (zalecane)), w następującym formacie:

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
FileEndpoint=myFileEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

Jeden scenariusz, gdzie użytkownik może chcieć określić jawny punkt końcowy jest, gdy został zamapowany na punkt końcowy usługi Blob storage do [domeny niestandardowej](../blobs/storage-custom-domain-name.md). W takim przypadku można określić niestandardowego punktu końcowego usługi Blob storage w ciągu połączenia. Opcjonalnie można określić domyślne punkty końcowe dla innych usług, jeśli aplikacja korzysta z nich.

Oto przykład parametrów połączenia, który określa jawny punkt końcowy usługi Blob:

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

Wartości punktu końcowego w parametrach połączenia są używane do konstruowania identyfikatorów URI żądania do usług magazynu i dyktowania formularza wszystkie identyfikatory URI, które są zwracane do kodu.

Jeśli zamapowany na domenę niestandardową punktu końcowego magazynu, a pominięto tego punktu końcowego z parametrów połączenia, następnie nie będzie mógł używać tego ciągu połączenia dostępu do danych w tej usłudze w kodzie.

> [!IMPORTANT]
> Wartości punktu końcowego usługi w parametry połączenia muszą być poprawnie sformułowany identyfikatory URI, w tym `https://` (zalecane) lub `http://`. Ponieważ Magazyn Azure nie obsługuje jeszcze HTTPS dla domen niestandardowych możesz *musi* określ `http://` dla dowolnego punktu końcowego identyfikatora URI, który wskazuje na domenę niestandardową.
>

### <a name="create-a-connection-string-with-an-endpoint-suffix"></a>Utwórz ciąg połączenia za pomocą sufiksu punktu końcowego
Aby utworzyć parametry połączenia dla usługi storage w regionach lub wystąpień z innym punktem końcowym sufiksy, takich jak Azure China 21Vianet lub platformy Azure dla instytucji rządowych, użyj następującego formatu ciągu połączenia. Wskazuje, czy chcesz się połączyć z kontem magazynu za pośrednictwem protokołu HTTPS (zalecane) lub HTTP, Zastąp `myAccountName` nazwą konta magazynu, należy zastąpić `myAccountKey` przy użyciu klucza dostępu do konta i Zastąp `mySuffix` sufiks identyfikatora URI:

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

Oto przykładowe parametry połączenia dla usługi storage w Azure China 21Vianet:

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>Analizowania parametrów połączenia
[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>Kolejne kroki
* [Korzystanie z emulatora usługi Azure storage do programowania i testowania](storage-use-emulator.md)
* [Eksploratory usługi Azure Storage](storage-explorers.md)
* [Używanie sygnatur dostępu współdzielonego (SAS)](storage-dotnet-shared-access-signature-part-1.md)

