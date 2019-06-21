---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 8c577db3e9f2bff9e86c3a7c37274630f90dd680
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183435"
---
Emulator magazynu obsługuje jednego stałego konta i klucz uwierzytelniania dobrze znanych na potrzeby uwierzytelniania klucza wspólnego. Tego konta i klucz są dozwolone do użytku z emulatora magazynu tylko poświadczenia klucza wspólnego. Oto one:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> Klucz uwierzytelniania, obsługiwanych przez emulator magazynu jest przeznaczony tylko do celów testowych funkcjonalność kod uwierzytelniania klienta. Nie ma żadnych celów bezpieczeństwa. Nie można użyć konta magazynu w środowisku produkcyjnym i klucz za pomocą emulatora magazynu. Nie należy używać konta programowania z danymi produkcyjnymi.
> 
> Emulator magazynu obsługuje tylko połączenie za pośrednictwem protokołu HTTP. Jednak protokół HTTPS jest zalecane protokołu do uzyskiwania dostępu do zasobów w środowisku produkcyjnym konta magazynu platformy Azure.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Łączenie się z kontem emulator przy użyciu skrótu
Najprostszym sposobem, aby nawiązać połączenie z emulatora magazynu z poziomu aplikacji jest skonfigurowanie parametrów połączenia w pliku konfiguracyjnym aplikacji, która odwołuje się skrót `UseDevelopmentStorage=true`. Oto przykład parametrów połączenia z emulatorem magazynu w *app.config* pliku: 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="connect-to-the-emulator-account-using-the-well-known-account-name-and-key"></a>Łączenie się z kontem emulatora, korzystając z dobrze znaną nazwę konta i klucza
Aby utworzyć parametry połączenia, który odwołuje się do emulatora nazwy i klucza konta, należy określić punkty końcowe dla poszczególnych usług, którą chcesz korzystać z emulatora w parametrach połączenia. Jest to konieczne, tak aby parametry połączenia będzie odwoływać się do emulatora punktów końcowych, które są inne niż konto magazynu w środowisku produkcyjnym. Na przykład wartość parametrów połączenia będzie wyglądać następująco:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

Ta wartość jest taka sama jak skrótów pokazanych powyżej `UseDevelopmentStorage=true`.

#### <a name="specify-an-http-proxy"></a>Określ serwer proxy HTTP
Można również określić serwer proxy HTTP do użycia podczas testowania usługi względem emulatora magazynu. Może to być przydatne do obserwacji żądań i odpowiedzi HTTP podczas debugowania operacji dotyczących usług magazynu. Aby określić serwer proxy, należy dodać `DevelopmentStorageProxyUri` opcji Parametry połączenia, a następnie ustaw dla niego wartość identyfikatora URI serwera proxy. Na przykład poniżej przedstawiono parametry połączenia, który wskazuje emulatora magazynu i konfiguruje serwer proxy HTTP:

```
UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri
```

