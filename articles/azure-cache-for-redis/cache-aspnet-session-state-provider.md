---
title: Dostawca stanu ASP.NET pamięci podręcznej
description: Dowiedz się, jak przechowywać ASP.NET stan sesji w pamięci przy użyciu usługi Azure Cache for Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 05/01/2017
ms.openlocfilehash: 8083efe833ec80290713fc14d9cb89acd8263fa2
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010906"
---
# <a name="aspnet-session-state-provider-for-azure-cache-for-redis"></a>Dostawca stanu sesji ASP.NET dla usługi Azure Cache for Redis

Usługa Azure Cache for Redis udostępnia dostawcę stanu sesji, którego można użyć do przechowywania stanu sesji w pamięci za pomocą usługi Azure Cache for Redis zamiast bazy danych programu SQL Server. Aby użyć dostawcy stanu sesji buforowania, najpierw skonfiguruj pamięć podręczną, a następnie skonfiguruj aplikację ASP.NET do pamięci podręcznej przy użyciu pakietu NuGet stanu sesji Usługi Azure dla programu Redis Session.

Często nie jest praktyczne w rzeczywistych aplikacji w chmurze, aby uniknąć przechowywania niektórych form stanu dla sesji użytkownika, ale niektóre podejścia wpływają na wydajność i skalowalność więcej niż inne. Jeśli musisz przechowywać stan, najlepszym rozwiązaniem jest utrzymanie małej ilości stanu i przechowywanie jej w plikach cookie. Jeśli nie jest to możliwe, następnym najlepszym rozwiązaniem jest użycie ASP.NET stanu sesji z dostawcą rozproszonej pamięci podręcznej w pamięci podręcznej. Najgorszym rozwiązaniem z punktu widzenia wydajności i skalowalności jest użycie dostawcy stanu sesji kopii zapasowej bazy danych. Ten temat zawiera wskazówki dotyczące korzystania z dostawcy stanu ASP.NET sesji dla usługi Azure Cache for Redis. Aby uzyskać informacje na temat innych opcji stanu sesji, zobacz [ASP.NET opcje stanu sesji](#aspnet-session-state-options).

## <a name="store-aspnet-session-state-in-the-cache"></a>Przechowywanie stanu sesji programu ASP.NET w pamięci podręcznej

Aby skonfigurować aplikację kliencką w programie Visual Studio przy użyciu pakietu NuGet stanu sesji usługi Azure dla programu Redis Session, kliknij polecenie **Menedżer pakietów NuGet**, **konsola Menedżera pakietów** z menu **Narzędzia.**

W oknie `Package Manager Console` uruchom następujące polecenie.
    

```powershell
Install-Package Microsoft.Web.RedisSessionStateProvider
```

> [!IMPORTANT]
> Jeśli używasz funkcji klastrowania z warstwy premium, należy użyć [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 lub nowszej lub wyjątek. Przejście na 2.0.1 lub wyższe jest przełomową zmianą; Aby uzyskać więcej informacji, zobacz [v2.0.0 Breaking Change Details](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details). W momencie aktualizacji tego artykułu bieżąca wersja tego pakietu to 2.2.3.
> 
> 

Pakiet Dostawcy stanu sesji Redis NuGet ma zależność od pakietu StackExchange.Redis.StrongName. Jeśli pakiet StackExchange.Redis.StrongName nie jest obecny w projekcie, jest zainstalowany.

>[!NOTE]
>Oprócz pakietu StackExchange.StrongName o silnej nazwie istnieje również wersja StackExchange.Redis o niesilniej nazwanych. Jeśli projekt jest przy użyciu wersji StackExchange.Redis o nazwie non-strong należy ją odinstalować, w przeciwnym razie w projekcie pojawią się konflikty nazewnictwa. Aby uzyskać więcej informacji na temat tych pakietów, zobacz [Konfigurowanie klientów pamięci podręcznej .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

Pakiet NuGet pobiera i dodaje wymagane odwołania do zestawu i dodaje następującą sekcję do pliku web.config. Ta sekcja zawiera wymaganą konfigurację dla aplikacji ASP.NET do korzystania z usługi Azure Cache dla dostawcy stanu sesji Redis.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
  <providers>
    <!-- Either use 'connectionString' OR 'settingsClassName' and 'settingsMethodName' OR use 'host','port','accessKey','ssl','connectionTimeoutInMilliseconds' and 'operationTimeoutInMilliseconds'. -->
    <!-- 'throwOnError','retryTimeoutInMilliseconds','databaseId' and 'applicationName' can be used with both options. -->
    <!--
      <add name="MySessionStateStore" 
        host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
        throwOnError = "true" [true|false]
        retryTimeoutInMilliseconds = "5000" [number]
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
    <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider"
         host=""
         accessKey=""
         ssl="true" />
  </providers>
</sessionState>
```

Skomentowana sekcja zawiera przykład atrybutów i ustawień przykładowych dla każdego atrybutu.

Skonfiguruj atrybuty z wartościami z bloku pamięci podręcznej w witrynie Microsoft Azure portal i skonfiguruj inne wartości zgodnie z potrzebami. Aby uzyskać instrukcje dotyczące uzyskiwania dostępu do właściwości pamięci podręcznej, zobacz [Konfigurowanie pamięci podręcznej azure dla ustawień Redis](cache-configure.md#configure-azure-cache-for-redis-settings).

* **host** – określ punkt końcowy pamięci podręcznej.
* **portu** — w zależności od ustawień TLS należy użyć portu innego niż TLS/SSL lub portu TLS/SSL.
* **accessKey** — użyj klucza podstawowego lub pomocniczego dla pamięci podręcznej.
* **ssl** - to prawda, jeśli chcesz zabezpieczyć komunikację pamięci podręcznej /klienta z TLS; w przeciwnym razie fałszywe. Pamiętaj, aby określić prawidłowy port.
  * Port nieobjęty TLS jest domyślnie wyłączony dla nowych pamięci podręcznych. Określ wartość true dla tego ustawienia, aby użyć portu TLS. Aby uzyskać więcej informacji na temat włączania portu nienawiązanego TLS, zobacz sekcję [Porty dostępu](cache-configure.md#access-ports) w temacie [Konfigurowanie pamięci podręcznej.](cache-configure.md)
* **throwOnError** — true, jeśli chcesz wyjątek, który ma zostać zgłoszony, jeśli występuje błąd lub false, jeśli chcesz, aby operacja nie powiodła się po cichu. Można sprawdzić błąd, sprawdzając statyczne Microsoft.Web.Redis.RedisSessionStateProvider.LastException właściwości. Wartość domyślna jest true.
* **ponów TimeoutInMilliseconds** — operacje, które nie powiodą się w tym przedziale czasu, określone w milisekundach. Pierwsze ponawianie próby występuje po 20 milisekundach, a następnie ponownych prób występuje co sekundę, dopóki nie wygaśnie interwału retryoutInMilliseconds. Natychmiast po tym przedziale operacja jest ponawiana po raz ostatni. Jeśli operacja nadal nie powiedzie się, wyjątek jest zgłaszany z powrotem do wywołującego, w zależności od throwOnError ustawienie. Wartość domyślna to 0, co oznacza brak ponownych prób.
* **databaseId** — określa, która baza danych ma być używana dla danych wyjściowych pamięci podręcznej. Jeśli nie zostanie określona, używana jest wartość domyślna 0.
* **applicationName** – Klucze są `{<Application Name>_<Session ID>}_Data`przechowywane w redis jako . Ten schemat nazewnictwa umożliwia wielu aplikacjom współużytkowanie tego samego wystąpienia Redis. Ten parametr jest opcjonalny, a jeśli go nie podasz, używana jest wartość domyślna.
* **connectionTimeoutInMilliseconds** — to ustawienie umożliwia zastąpienie connectTimeout ustawienie w StackExchange.Redis klienta. Jeśli nie zostanie określony, używane jest domyślne ustawienie connectTimeout 5000. Aby uzyskać więcej informacji, zobacz [Model konfiguracji StackExchange.Redis](https://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** — to ustawienie umożliwia zastąpienie ustawienia syncTimeout w kliencie StackExchange.Redis. Jeśli nie zostanie określony, używane jest domyślne ustawienie syncTimeout 1000. Aby uzyskać więcej informacji, zobacz [Model konfiguracji StackExchange.Redis](https://go.microsoft.com/fwlink/?LinkId=398705).
* **redisSerializerType** — to ustawienie umożliwia określenie niestandardowej serializacji zawartości sesji wysyłanej do redis. Określony typ musi `Microsoft.Web.Redis.ISerializer` implementować i musi zadeklarować publiczny konstruktor bez parametrów. Domyślnie `System.Runtime.Serialization.Formatters.Binary.BinaryFormatter` jest używany.

Aby uzyskać więcej informacji na temat tych właściwości, zobacz oryginalne ogłoszenie wpisu w blogu [w: Announcing ASP.NET Session State Provider for Redis](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

Nie zapomnij skomentować standardowej sekcji dostawcy stanu sesji InProc w witrynie web.config.

```xml
<!-- <sessionState mode="InProc"
     customProvider="DefaultSessionProvider">
     <providers>
        <add name="DefaultSessionProvider"
              type="System.Web.Providers.DefaultSessionStateProvider,
                    System.Web.Providers, Version=1.0.0.0, Culture=neutral,
                    PublicKeyToken=31bf3856ad364e35"
              connectionStringName="DefaultConnection" />
      </providers>
</sessionState> -->
```

Po wykonaniu tych kroków aplikacja jest skonfigurowana do używania usługi Azure Cache dla dostawcy stanu sesji Redis. Podczas korzystania ze stanu sesji w aplikacji jest on przechowywany w pamięci podręcznej platformy Azure dla wystąpienia Redis.

> [!IMPORTANT]
> Dane przechowywane w pamięci podręcznej muszą być serializowalne, w przeciwieństwie do danych, które mogą być przechowywane w domyślnej w pamięci ASP.NET dostawcy stanu sesji. Gdy dostawca stanu sesji dla redis jest używany, upewnij się, że typy danych, które są przechowywane w stanie sesji są serializable.
> 
> 

## <a name="aspnet-session-state-options"></a>Opcje stanu ASP.NET sesji

* W dostawcy stanu sesji pamięci — ten dostawca przechowuje stan sesji w pamięci. Zaletą korzystania z tego dostawcy jest to proste i szybkie. Jednak nie można skalować aplikacji sieci Web, jeśli używasz w dostawcy pamięci, ponieważ nie jest on dystrybuowany.
* Dostawca stanu sesji programu SQL Server — ten dostawca przechowuje stan sesji w programie Sql Server. Użyj tego dostawcy, jeśli chcesz przechowywać stan sesji w magazynie trwałym. Można skalować aplikację sieci Web, ale przy użyciu programu Sql Server for Session ma wpływ na wydajność aplikacji sieci Web. Tego dostawcy można również użyć [z konfiguracją OLTP w pamięci,](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/11/28/asp-net-session-state-with-sql-server-in-memory-oltp/) aby zwiększyć wydajność.
* Dostawca stanu sesji rozproszonej pamięci, taki jak Usługa Azure Cache dla dostawcy stanu sesji Redis — ten dostawca zapewnia najlepsze z obu światów. Aplikacja sieci Web może mieć prostego, szybkiego i skalowalnego dostawcę stanu sesji. Ponieważ ten dostawca przechowuje session stanu w pamięci podręcznej, aplikacja musi wziąć pod uwagę wszystkie cechy skojarzone podczas rozmowy z rozproszonej pamięci podręcznej pamięci podręcznej, takich jak przejściowe awarie sieci. Aby uzyskać najlepsze rozwiązania dotyczące korzystania z pamięci podręcznej, zobacz [wskazówki dotyczące buforowania](../best-practices-caching.md) z wzorców firmy Microsoft & [wskazówki dotyczące projektowania aplikacji w chmurze i implementacji usługi Azure.](https://github.com/mspnp/azure-guidance)

Aby uzyskać więcej informacji na temat stanu sesji i innych sprawdzonych rozwiązań, zobacz [Najważniejsze wskazówki dotyczące tworzenia sieci Web (tworzenie rzeczywistych aplikacji w chmurze za pomocą platformy Azure).](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices)

## <a name="third-party-session-state-providers"></a>Zewnętrzni dostawcy stanu sesji

* [Ncache](https://www.alachisoft.com/ncache/session-index.html)
* [Apache zapalić](https://apacheignite-net.readme.io/docs/aspnet-session-state-caching)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [dostawcą pamięci podręcznej ASP.NET danych wyjściowych dla usługi Azure Cache for Redis](cache-aspnet-output-cache-provider.md).
