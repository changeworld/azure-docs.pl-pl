---
title: Dostawca pamięci podręcznej danych wyjściowych platformy ASP.NET w pamięci podręcznej
description: Dowiedz się, jak buforowanie danych wyjściowych strony ASP.NET przy użyciu usługi Azure Cache dla pamięci podręcznej Redis
services: cache
documentationcenter: na
author: yegu-ms
manager: jhubbard
editor: tysonn
ms.assetid: 78469a66-0829-484f-8660-b2598ec60fbf
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 02/14/2017
ms.author: yegu
ms.openlocfilehash: 3aa2e9e773eb0c07b5f10a57dabf1138b9f3f288
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232912"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>Dostawca pamięci podręcznej danych wyjściowych platformy ASP.NET dla usługi Azure Cache dla usługi Redis
Dostawca Redis wyjściowej pamięci podręcznej jest mechanizm magazynu spoza procesu do danych w pamięci podręcznej danych wyjściowych. Dane te są specjalnie dla pełnej odpowiedzi HTTP (stronie buforowanie danych wyjściowych). Dostawca podłącza się do nowego dane wyjściowe pamięci podręcznej dostawcy rozszerzeń punktu wprowadzone w programie ASP.NET 4.

Aby użyć dostawca Redis wyjściowej pamięci podręcznej, najpierw skonfigurować pamięć podręczną, a następnie skonfiguruj aplikację ASP.NET przy użyciu pakietu NuGet dostawcy pamięci podręcznej danych wyjściowych usługi Redis. Ten temat zawiera wskazówki na temat konfigurowania aplikacji do użycia dostawca Redis wyjściowej pamięci podręcznej. Aby uzyskać więcej informacji na temat tworzenia i konfigurowania usługi Azure Cache dla wystąpienia pamięci podręcznej Redis, zobacz [Tworzenie pamięci podręcznej](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Store danych wyjściowych strony ASP.NET w pamięci podręcznej
Aby skonfigurować aplikację klienta w programie Visual Studio dla pakietu Redis NuGet stanu sesji przy użyciu usługi Azure Cache, kliknij przycisk **Menedżera pakietów NuGet**, **Konsola Menedżera pakietów** z **narzędzia**  menu.

W oknie `Package Manager Console` uruchom następujące polecenie.
    
```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

Pakiet NuGet dostawcy pamięci podręcznej danych wyjściowych Redis ma zależność od pakietu StackExchange.Redis.StrongName. Jeśli pakiet StackExchange.Redis.StrongName nie jest obecny w projekcie, jest zainstalowana. Aby uzyskać więcej informacji na temat pakietu Redis NuGet dostawcy pamięci podręcznej danych wyjściowych, zobacz [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) stronę pakietu NuGet.

>[!NOTE]
>Oprócz pakietu StackExchange.Redis.StrongName o silnej nazwie jest również wersja niż-o silnej nazwie StackExchange.Redis. Jeśli projektu używa wersji StackExchange.Redis innych niż-o silnej nazwie, należy go odinstalować, w przeciwnym razie możesz uzyskać konfliktów nazw w projekcie. Aby uzyskać więcej informacji o tych paloetach, zobacz [.NET Konfigurowanie klientów pamięci podręcznej](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

Pakiet NuGet pobiera i dodaje wymagane odwołania do zestawu i dodanie sekcji poniżej do pliku web.config. Ta sekcja zawiera wymaganej konfiguracji dla aplikacji ASP.NET do używania dostawcy pamięci podręcznej danych wyjściowych usługi Redis.

```xml
<caching>
  <outputCache defaultProvider="MyRedisOutputCache">
    <providers>
      <!-- For more details check https://github.com/Azure/aspnet-redis-providers/wiki -->
      <!-- Either use 'connectionString' OR 'settingsClassName' and 'settingsMethodName' OR use 'host','port','accessKey','ssl','connectionTimeoutInMilliseconds' and 'operationTimeoutInMilliseconds'. -->
      <!-- 'databaseId' and 'applicationName' can be used with both options. -->
      <!--
      <add name="MyRedisOutputCache" 
        host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
        databaseId = "0" [number]
        applicationName = "" [String]
        connectionTimeoutInMilliseconds = "5000" [number]
        operationTimeoutInMilliseconds = "1000" [number]
        connectionString = "<Valid StackExchange.Redis connection string>" [String]
        settingsClassName = "<Assembly qualified class name that contains settings method specified below. Which basically return 'connectionString' value>" [String]
        settingsMethodName = "<Settings method should be defined in settingsClass. It should be public, static, does not take any parameters and should have a return type of 'String', which is basically 'connectionString' value.>" [String]
        loggingClassName = "<Assembly qualified class name that contains logging method specified below>" [String]
        loggingMethodName = "<Logging method should be defined in loggingClass. It should be public, static, does not take any parameters and should have a return type of System.IO.TextWriter.>" [String]
        redisSerializerType = "<Assembly qualified class name that implements Microsoft.Web.Redis.ISerializer>" [String]
      />
      -->
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider"
           host=""
           accessKey=""
           ssl="true" />
    </providers>
  </outputCache>
</caching>
```

Komentarze sekcja zawiera przykład atrybutów i przykładowych ustawień dla każdego atrybutu.

Konfigurowanie atrybutów przy użyciu wartości z bloku usługi pamięci podręcznej w portalu Microsoft Azure i innych wartości zostaną skonfigurowane zgodnie z potrzebami. Aby uzyskać instrukcje dotyczące uzyskiwania dostępu do właściwości pamięci podręcznej, zobacz [Konfigurowanie usługi Azure Cache ustawień pamięci podręcznej Redis](cache-configure.md#configure-azure-cache-for-redis-settings).

* **host** — Określ punkt końcowy usługi pamięci podręcznej.
* **port** — port bez protokołu SSL lub port SSL, w zależności od ustawień protokołu ssl.
* **accessKey** — klucz podstawowy lub pomocniczy na użytek pamięci podręcznej.
* **Protokół SSL** — wartość true, jeśli chcesz zabezpieczyć komunikację klienta/pamięci podręcznej przy użyciu protokołu ssl; w przeciwnym razie wartość false. Należy określić prawidłowy port.
  * Port bez obsługi protokołu SSL jest domyślnie wyłączony w przypadku nowych pamięci podręcznych. Określ wartość true dla tego ustawienia używał portu protokołu SSL. Aby uzyskać więcej informacji na temat włączania portu bez protokołu SSL, zobacz [porty dostępu](cache-configure.md#access-ports) sekcji [Konfigurowanie pamięci podręcznej](cache-configure.md) tematu.
* **databaseId** — określoną bazę danych, która do użycia dla pamięci podręcznej dane wyjściowe. Jeśli nie zostanie określony, jest używana domyślna wartość 0.
* **applicationName** — klucze są przechowywane w pamięci podręcznej redis jako `<AppName>_<SessionId>_Data`. Ten schemat nazewnictwa umożliwia wielu aplikacjom na udostępnianie tego samego klucza. Ten parametr jest opcjonalny, a jeśli nie zostanie określona wartość domyślna jest używana.
* **connectionTimeoutInMilliseconds** — to ustawienie pozwala zastąpić ustawienie connectTimeout klienta StackExchange.Redis. Jeśli nie zostanie określony, używane jest domyślne ustawienie connectTimeout 5000. Aby uzyskać więcej informacji, zobacz [modelu konfiguracji StackExchange.Redis](https://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** — to ustawienie pozwala zastąpić ustawienie syncTimeout klienta StackExchange.Redis. Jeśli nie zostanie określony, używane jest domyślne ustawienie syncTimeout 1000. Aby uzyskać więcej informacji, zobacz [modelu konfiguracji StackExchange.Redis](https://go.microsoft.com/fwlink/?LinkId=398705).

Dodaj dyrektywę OutputCache do każdej strony, dla którego chcesz buforować dane wyjściowe.

```
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

W poprzednim przykładzie dane strony pamięci podręcznej pozostaje w pamięci podręcznej 60 sekund, a inna wersja strony są buforowane dla każdej kombinacji parametrów. Aby uzyskać więcej informacji na temat dyrektywy OutputCache zobacz [ @OutputCache ](https://go.microsoft.com/fwlink/?linkid=320837).

Gdy te kroki są wykonywane, aplikacja jest skonfigurowana do używania dostawcy pamięci podręcznej danych wyjściowych usługi Redis.

## <a name="next-steps"></a>Kolejne kroki
Zapoznaj się z [dostawca stanu sesji ASP.NET dla usługi Azure Cache dla usługi Redis](cache-aspnet-session-state-provider.md).

