---
title: Konfigurowanie ciągu połączenia
titleSuffix: Azure Storage
description: Konfigurowanie ciągu połączenia dla konta magazynu platformy Azure. Parametry połączenia zawiera informacje potrzebne do autoryzacji dostępu do konta magazynu z aplikacji w czasie wykonywania przy użyciu autoryzacji klucza udostępnionego.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 12/20/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f617beec8a53570ede7755040cfbb92a7d1712b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268355"
---
# <a name="configure-azure-storage-connection-strings"></a>Konfiguracja parametrów połączenia usługi Azure Storage

Parametry połączenia zawiera informacje o autoryzacji wymagane dla aplikacji, aby uzyskać dostęp do danych w usłudze Azure Storage w czasie wykonywania przy użyciu autoryzacji klucza udostępnionego. Parametry połączenia można skonfigurować w celu:

* Połącz się z emulatorem magazynu platformy Azure.
* Dostęp do konta magazynu na platformie Azure.
* Dostęp do określonych zasobów na platformie Azure za pośrednictwem sygnatury dostępu współdzielonego (SAS).

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-and-copy-a-connection-string"></a>Wyświetlanie i kopiowanie ciągu połączenia

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

## <a name="store-a-connection-string"></a>Przechowywanie ciągu połączenia

Aplikacja musi uzyskać dostęp do ciągu połączenia w czasie wykonywania, aby autoryzować żądania wykonane w usłudze Azure Storage. Istnieje kilka opcji przechowywania ciągu połączenia:

* Ciąg połączenia można przechowywać w zmiennej środowiskowej.
* Aplikacja uruchomiona na pulpicie lub urządzeniu może przechowywać parametry połączenia w pliku **app.config** lub **web.config.** Dodaj ciąg połączenia do sekcji **AppSettings** w tych plikach.
* Aplikacja uruchomiona w usłudze w chmurze platformy Azure może przechowywać parametry połączenia w [pliku schematu konfiguracji usługi Platformy Azure (cscfg).](https://msdn.microsoft.com/library/ee758710.aspx) Dodaj parametry połączenia do sekcji **Konfiguracjestawy** pliku konfiguracji usługi.

Przechowywanie ciągu połączenia w pliku konfiguracyjnym ułatwia aktualizowanie ciągu połączenia w celu przełączania się między emulatorem magazynu a kontem magazynu platformy Azure w chmurze. Wystarczy edytować ciąg połączenia, aby wskazać środowisko docelowe.

Za pomocą [programu Microsoft Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) można uzyskać dostęp do ciągu połączenia w czasie wykonywania, niezależnie od tego, gdzie aplikacja jest uruchomiona.

## <a name="configure-a-connection-string-for-the-storage-emulator"></a>Konfigurowanie ciągu połączenia dla emulatora magazynu

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Aby uzyskać więcej informacji na temat emulatora magazynu, zobacz [Używanie emulatora magazynu platformy Azure do tworzenia i testowania.](storage-use-emulator.md)

## <a name="configure-a-connection-string-for-an-azure-storage-account"></a>Konfigurowanie ciągu połączenia dla konta magazynu platformy Azure

Aby utworzyć parametry połączenia dla konta magazynu platformy Azure, użyj następującego formatu. Określ, czy chcesz połączyć się z kontem magazynu `myAccountName` za pomocą protokołu HTTPS (zalecane) czy HTTP, zastąp nazwą konta magazynu i zastąp `myAccountKey` kluczem dostępu do konta:

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

Na przykład parametry połączenia mogą wyglądać podobnie do:

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

Chociaż usługa Azure Storage obsługuje zarówno protokół HTTP, jak i protokół HTTPS w ciągu połączenia, *protokół HTTPS jest wysoce zalecany.*

> [!TIP]
> Parametry połączenia konta magazynu można znaleźć w [witrynie Azure portal](https://portal.azure.com). Przejdź **SETTINGS** > do**pozycji Ustawienia klucze dostępu** w bloku menu konta magazynu, aby wyświetlić parametry połączenia dla kluczy dostępu podstawowego i pomocniczego.
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Tworzenie ciągu połączenia przy użyciu sygnatury dostępu współdzielonego

[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="create-a-connection-string-for-an-explicit-storage-endpoint"></a>Tworzenie ciągu połączenia dla jawnego punktu końcowego magazynu

Można określić jawne punkty końcowe usługi w ciągu połączenia zamiast przy użyciu domyślnych punktów końcowych. Aby utworzyć parametry połączenia określające jawny punkt końcowy, określ pełny punkt końcowy usługi dla każdej usługi, w tym specyfikację protokołu (HTTPS (zalecane) lub HTTP) w następującym formacie:

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
FileEndpoint=myFileEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

Jednym ze scenariuszy, w którym można określić jawny punkt końcowy, jest mapowanie punktu końcowego magazynu obiektów Blob na [domenę niestandardową.](../blobs/storage-custom-domain-name.md) W takim przypadku można określić niestandardowy punkt końcowy dla magazynu obiektów Blob w ciągu połączenia. Opcjonalnie można określić domyślne punkty końcowe dla innych usług, jeśli aplikacja używa ich.

Oto przykład ciągu połączenia, który określa jawny punkt końcowy dla usługi obiektów Blob:

```
# Blob endpoint only
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
AccountName=storagesample;
AccountKey=<account-key>
```

W tym przykładzie określono jawne punkty końcowe dla wszystkich usług, w tym domeny niestandardowej dla usługi obiektów Blob:

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

Wartości punktu końcowego w ciągu połączenia są używane do konstruowania identyfikatorów URI żądania do usług magazynu i dyktować formę wszelkich identyfikatorów URI, które są zwracane do kodu.

Jeśli zamapowane punktu końcowego magazynu do domeny niestandardowej i pominąć tego punktu końcowego z ciągu połączenia, a następnie nie będzie można użyć tego ciągu połączenia, aby uzyskać dostęp do danych w tej usłudze z kodu.

> [!IMPORTANT]
> Wartości punktu końcowego usługi w ciągu połączeń muszą być `https://` dobrze sformułowanymi `http://`identyfikatorami URI, w tym (zalecane) lub . Ponieważ usługa Azure Storage nie obsługuje jeszcze protokołu HTTPS dla domen niestandardowych, *należy określić* `http://` dla każdego identyfikatora URI punktu końcowego, który wskazuje domenę niestandardową.
>

### <a name="create-a-connection-string-with-an-endpoint-suffix"></a>Tworzenie ciągu połączenia z sufiksem punktu końcowego

Aby utworzyć parametry połączenia dla usługi magazynu w regionach lub wystąpieniach z różnymi sufiksami punktu końcowego, takimi jak dla platformy Azure China 21Vianet lub Azure Government, użyj następującego formatu ciągu połączenia. Określ, czy chcesz połączyć się z kontem magazynu `myAccountName` za pośrednictwem protokołu HTTPS `myAccountKey` (zalecane) czy HTTP, `mySuffix` zastąść nazwą konta magazynu, zastąp kluczem dostępu do konta i zastąp sufiks identyfikatora URI:

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

Oto przykładowy ciąg połączenia dla usług magazynu w usłudze Azure China 21Vianet:

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>Analizowanie ciągu połączenia

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>Następne kroki

* [Używanie emulatora magazynu platformy Azure do tworzenia i testowania](storage-use-emulator.md)
* [Eksploratorzy usługi Azure Storage](storage-explorers.md)
* [Używanie sygnatur dostępu współdzielonego (SAS)](storage-sas-overview.md)
