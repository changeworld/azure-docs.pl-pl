---
title: Dostawcy pamięci podręcznej pamięci podręcznej danych wyjściowych programu ASP.NET
description: Dowiedz się, jak i pamięci podręcznej danych wyjściowych strony ASP.NET przy użyciu pamięci podręcznej Redis Azure
services: redis-cache
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: tysonn
ms.assetid: 78469a66-0829-484f-8660-b2598ec60fbf
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 02/14/2017
ms.author: wesmc
ms.openlocfilehash: 81c95949971d54833ca7a15ec5148116c94767f7
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2018
ms.locfileid: "27909826"
---
# <a name="aspnet-output-cache-provider-for-azure-redis-cache"></a>Dostawcy pamięci podręcznej danych wyjściowych programu ASP.NET na platformie Azure pamięci podręcznej Redis
Dostawcy Redis wyjściowej pamięci podręcznej jest mechanizmem poza procesem magazynu danych w pamięci podręcznej danych wyjściowych. Dane te są specjalnie z myślą o pełnej odpowiedzi HTTP (strona buforowanie danych wyjściowych). Dostawca podłącza się do nowego dane wyjściowe pamięci podręcznej dostawcy rozszerzeń punktu wprowadzone w programie ASP.NET 4.

Aby użyć dostawcy Redis wyjściowej pamięci podręcznej, najpierw skonfigurować pamięć podręczną, a następnie skonfiguruj aplikację ASP.NET przy użyciu pakietu NuGet dostawcy Redis pamięci podręcznej danych wyjściowych. W tym temacie znajdują się wskazówki dotyczące konfigurowania aplikacji umożliwiająca użycie dostawcy Redis wyjściowej pamięci podręcznej. Aby uzyskać więcej informacji na temat tworzenia i konfigurowania wystąpienia pamięci podręcznej Redis Azure, zobacz [tworzenia pamięci podręcznej](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Przechowywanie w pamięci podręcznej danych wyjściowych strony ASP.NET
Kliknij, aby skonfigurować aplikację klienta w programie Visual Studio przy użyciu pakietu NuGet stanu sesji pamięci podręcznej Redis **Menedżera pakietów NuGet**, **Konsola Menedżera pakietów** z **narzędzia** menu.

W oknie `Package Manager Console` uruchom następujące polecenie.
    
```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

Pakiet NuGet dostawcy pamięci podręcznej danych wyjściowych Redis ma zależność od pakietu StackExchange.Redis.StrongName. Jeśli w projekcie nie ma pakietu StackExchange.Redis.StrongName, jest zainstalowana. Aby uzyskać więcej informacji o pakiecie NuGet dostawcy Redis pamięci podręcznej danych wyjściowych, zobacz [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) NuGet strony.

>[!NOTE]
>Oprócz pakietu StackExchange.Redis.StrongName o silnych nazwach jest także wersja z systemem innym niż-o silnych nazwach programie StackExchange.Redis. Jeśli projekt korzysta z wersji programie StackExchange.Redis nie-o silnych nazwach, należy go odinstalować, w przeciwnym razie możesz pobrać konfliktów nazw w projekcie. Aby uzyskać więcej informacji na temat tych pakietów, zobacz [.NET Konfigurowanie klientów pamięci podręcznej](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

Pakiet NuGet pliki do pobrania i dodaje odwołania do wymaganych zestawów i dodaje następującą sekcję do pliku web.config. Ta sekcja zawiera wymagana konfiguracja dla aplikacji ASP.NET do używania dostawcy Redis wyjściowej pamięci podręcznej.

```xml
<caching>
  <outputCachedefault Provider="MyRedisOutputCache">
    <providers>
      <!--
      <add name="MyRedisOutputCache"
        host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
        databaseId = "0" [number]
        applicationName = "" [String]
        connectionTimeoutInMilliseconds = "5000" [number]
        operationTimeoutInMilliseconds = "5000" [number]
      />
      -->
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider" host="127.0.0.1" accessKey="" ssl="false"/>
    </providers>
  </outputCache>
</caching>
```

Komentarze sekcji przedstawiono przykład atrybutów i przykładowe ustawienia dla każdego atrybutu.

Skonfiguruj atrybuty wartościami z Twojej blok pamięci podręcznej w portalu Microsoft Azure oraz innych wartości zgodnie z potrzebami. Aby uzyskać instrukcje dotyczące uzyskiwania dostępu do właściwości pamięci podręcznej, zobacz [Redis skonfigurować ustawienia pamięci podręcznej](cache-configure.md#configure-redis-cache-settings).

* **host** — Określ punktu końcowego pamięci podręcznej.
* **port** — port bez protokołu SSL lub portem SSL, w zależności od ustawień protokołu ssl.
* **accessKey** — Użyj klucz podstawowy lub pomocniczy dla pamięci podręcznej.
* **Protokół SSL** — wartość true, jeśli chcesz zabezpieczyć komunikację pamięci podręcznej/klienta przy użyciu protokołu ssl; w przeciwnym razie wartość false. Należy określić właściwego portu.
  * Port bez obsługi protokołu SSL jest domyślnie wyłączony w przypadku nowych pamięci podręcznych. Określ wartość true dla tego ustawienia używał portu protokołu SSL. Aby uzyskać więcej informacji na temat włączania portu bez protokołu SSL, zobacz [porty dostępu](cache-configure.md#access-ports) sekcji [skonfigurować pamięć podręczną](cache-configure.md) tematu.
* **databaseId** — określonej bazy danych do użycia na potrzeby pamięci podręcznej dane wyjściowe. Jeśli nie zostanie określony, zostanie użyta domyślna wartość 0.
* **applicationName** — klucze są przechowywane w pamięci podręcznej redis jako `<AppName>_<SessionId>_Data`. Ten schemat nazewnictwa umożliwia wielu aplikacji do udostępniania tego samego klucza. Ten parametr jest opcjonalny i jeśli nie zostanie określona wartość domyślna jest używany.
* **connectionTimeoutInMilliseconds** — to ustawienie pozwala zastąpić ustawienie connectTimeout w programie StackExchange.Redis klienta. Jeśli nie zostanie określona, używane jest domyślne ustawienie connectTimeout 5000. Aby uzyskać więcej informacji, zobacz [model konfiguracji w programie StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** — to ustawienie pozwala zastąpić ustawienie syncTimeout w programie StackExchange.Redis klienta. Jeśli nie zostanie określona, używane jest domyślne ustawienie syncTimeout 1000. Aby uzyskać więcej informacji, zobacz [model konfiguracji w programie StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).

Dodaj dyrektywy OutputCache na każdej stronie, dla którego chcesz pamięci podręcznej danych wyjściowych.

```
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

W poprzednim przykładzie dane buforowane strony pozostaje w pamięci podręcznej przez 60 sekund i inną wersję strony są buforowane dla każdej kombinacji parametrów. Aby uzyskać więcej informacji na temat dyrektywie OutputCache zobacz [ @OutputCache ](http://go.microsoft.com/fwlink/?linkid=320837).

Gdy te kroki są wykonywane, aplikacja jest skonfigurowana do używania dostawcy Redis wyjściowej pamięci podręcznej.

## <a name="next-steps"></a>Kolejne kroki
Zapoznaj się z [dostawcę stanu sesji ASP.NET dla pamięci podręcznej Redis Azure](cache-aspnet-session-state-provider.md).

