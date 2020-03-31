---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 8c577db3e9f2bff9e86c3a7c37274630f90dd680
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183435"
---
Emulator magazynu obsługuje jedno konto stałe i dobrze znany klucz uwierzytelniania dla uwierzytelniania klucza udostępnionego. To konto i klucz są tylko poświadczenia klucza udostępnionego dozwolone do użycia z emulatorem magazynu. Oto one:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> Klucz uwierzytelniania obsługiwany przez emulator magazynu jest przeznaczony tylko do testowania funkcji kodu uwierzytelniania klienta. Nie służy ona żadnemu celowi bezpieczeństwa. Nie można użyć konta magazynu produkcyjnego i klucza z emulatorem magazynu. Nie należy używać konta dewelopera z danymi produkcyjnymi.
> 
> Emulator magazynu obsługuje połączenie tylko za pośrednictwem protokołu HTTP. Jednak HTTPS jest zalecanym protokołem dostępu do zasobów na produkcyjnym koncie magazynu platformy Azure.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Łączenie się z kontem emulatora za pomocą skrótu
Najprostszym sposobem na połączenie się z emulatorem magazynu z aplikacji jest skonfigurowanie ciągu połączenia w `UseDevelopmentStorage=true`pliku konfiguracyjnym aplikacji, który odwołuje się do skrótu . Oto przykład ciągu połączenia z emulatorem magazynu w pliku *app.config:* 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="connect-to-the-emulator-account-using-the-well-known-account-name-and-key"></a>Łączenie się z kontem emulatora przy użyciu dobrze znanej nazwy konta i klucza
Aby utworzyć ciąg połączenia, który odwołuje się do nazwy konta emulatora i klucza, należy określić punkty końcowe dla każdej z usług, które mają być używane z emulatora w ciągu połączenia. Jest to konieczne, aby ciąg połączenia odwoływał się do punktów końcowych emulatora, które różnią się od tych dla produkcyjnego konta magazynu. Na przykład wartość ciągu połączenia będzie wyglądać następująco:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

Ta wartość jest identyczna ze `UseDevelopmentStorage=true`skrótem pokazanym powyżej, .

#### <a name="specify-an-http-proxy"></a>Określanie serwera proxy HTTP
Można również określić serwer proxy HTTP do użycia podczas testowania usługi względem emulatora magazynu. Może to być przydatne do obserwowania żądań HTTP i odpowiedzi podczas debugowania operacji względem usług magazynu. Aby określić serwer `DevelopmentStorageProxyUri` proxy, dodaj opcję do ciągu połączenia i ustaw jego wartość na identyfikator URI serwera proxy. Na przykład w tym miejscu jest ciąg połączenia, który wskazuje na emulator magazynu i konfiguruje serwer proxy HTTP:

```
UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri
```

