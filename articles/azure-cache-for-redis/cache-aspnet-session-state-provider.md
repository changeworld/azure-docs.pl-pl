---
title: Dostawca stanu sesji ASP.NET dla pamięci podręcznej | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przechowywanie stanu sesji ASP.NET dla usługi Redis za pomocą usługi Azure Cache
services: cache
documentationcenter: na
author: yegu-ms
manager: jhubbard
editor: tysonn
ms.assetid: 192f384c-836a-479a-bb65-8c3e6d6522bb
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 05/01/2017
ms.author: yegu
ms.openlocfilehash: 4a51040ecdbf22af03ce1e6edaaa0ff577bbc076
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793241"
---
# <a name="aspnet-session-state-provider-for-azure-cache-for-redis"></a>Dostawca stanu sesji ASP.NET dla usługi Azure Cache dla usługi Redis

Pamięć podręczna systemu Azure dla usługi Redis zawiera dostawca stanu sesji, który służy do przechowywania swojej sesji stanu w pamięci z pamięcią podręczną Azure Redis zamiast bazy danych programu SQL Server. Aby korzystać z pamięci podręcznej dostawcy stanu sesji, najpierw skonfigurować pamięć podręczną, a następnie skonfiguruj aplikację ASP.NET przy użyciu usługi Azure Cache dla pakietu Redis NuGet stanu sesji w pamięci podręcznej.

Często nie jest praktyczne w chmurze w rzeczywistych warunkach aplikacji bez przechowywania pewnego rodzaju stanu dla sesji użytkownika, ale niektóre podejścia wpłynąć na wydajność i skalowalność więcej niż inne. W przypadku przechowywania stanu, najlepszym rozwiązaniem jest zachowywanie małych ilości stanu i zapisz go w plikach cookie. Jeśli nie jest to możliwe, dalej najlepszym rozwiązaniem jest do użytku z dostawcą rozproszona, wewnątrzpamięciowa pamięć podręczna stanu sesji programu ASP.NET. Najgorszy rozwiązania z punktu widzenia skalowalności i wydajności jest korzystanie z bazy danych kopii dostawca stanu sesji. Ten temat zawiera wskazówki na temat używania dostawca stanu sesji ASP.NET dla usługi Azure Cache dla usługi Redis. Aby uzyskać informacje na temat innych opcji stanu sesji, zobacz [opcji stanu sesji ASP.NET](#aspnet-session-state-options).

## <a name="store-aspnet-session-state-in-the-cache"></a>Przechowywanie stanu sesji programu ASP.NET w pamięci podręcznej

Aby skonfigurować aplikację klienta w programie Visual Studio dla pakietu Redis NuGet stanu sesji przy użyciu usługi Azure Cache, kliknij przycisk **Menedżera pakietów NuGet**, **Konsola Menedżera pakietów** z **narzędzia**  menu.

W oknie `Package Manager Console` uruchom następujące polecenie.
    

```powershell
Install-Package Microsoft.Web.RedisSessionStateProvider
```

> [!IMPORTANT]
> Jeśli używasz funkcję klastrowania z warstwy premium, należy użyć [pakietu RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 lub wyższą lub wyjątek, który jest generowany. Przenoszenie 2.0.1 lub wyższej jest zmianą przerywającą; Aby uzyskać więcej informacji, zobacz [v2.0.0 istotne szczegóły zmian](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details). W czasie tej aktualizacji artykułu bieżącą wersję tego pakietu jest 2.2.3.
> 
> 

Pakiet NuGet dostawcy stanu sesji Redis ma zależność od pakietu StackExchange.Redis.StrongName. Jeśli pakiet StackExchange.Redis.StrongName nie jest obecny w projekcie, jest zainstalowana.

>[!NOTE]
>Oprócz pakietu StackExchange.Redis.StrongName o silnej nazwie jest również wersja niż-o silnej nazwie StackExchange.Redis. Jeśli projektu używa wersji StackExchange.Redis innych niż-o silnej nazwie, należy go odinstalować, w przeciwnym razie możesz uzyskać konfliktów nazw w projekcie. Aby uzyskać więcej informacji o tych paloetach, zobacz [.NET Konfigurowanie klientów pamięci podręcznej](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

Pakiet NuGet pobiera i dodaje wymagane odwołania do zestawu i dodanie sekcji poniżej do pliku web.config. Ta sekcja zawiera wymaganej konfiguracji dla aplikacji ASP.NET na potrzeby pamięci podręcznej Azure redis Cache dostawca stanu sesji.

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
</sessionState>
```

Komentarze sekcja zawiera przykład atrybutów i przykładowych ustawień dla każdego atrybutu.

Konfigurowanie atrybutów przy użyciu wartości z bloku usługi pamięci podręcznej w portalu Microsoft Azure i innych wartości zostaną skonfigurowane zgodnie z potrzebami. Aby uzyskać instrukcje dotyczące uzyskiwania dostępu do właściwości pamięci podręcznej, zobacz [Konfigurowanie usługi Azure Cache ustawień pamięci podręcznej Redis](cache-configure.md#configure-azure-cache-for-redis-settings).

* **host** — Określ punkt końcowy usługi pamięci podręcznej.
* **port** — port bez protokołu SSL lub port SSL, w zależności od ustawień protokołu ssl.
* **accessKey** — klucz podstawowy lub pomocniczy na użytek pamięci podręcznej.
* **Protokół SSL** — wartość true, jeśli chcesz zabezpieczyć komunikację klienta/pamięci podręcznej przy użyciu protokołu ssl; w przeciwnym razie wartość false. Należy określić prawidłowy port.
  * Port bez obsługi protokołu SSL jest domyślnie wyłączony w przypadku nowych pamięci podręcznych. Określ wartość true dla tego ustawienia używał portu protokołu SSL. Aby uzyskać więcej informacji na temat włączania portu bez protokołu SSL, zobacz [porty dostępu](cache-configure.md#access-ports) sekcji [Konfigurowanie pamięci podręcznej](cache-configure.md) tematu.
* **throwOnError** — jest to wartość true, jeśli chcesz, aby zgłoszenie wyjątku w przypadku awarii, lub FAŁSZ, jeśli chcesz, aby zakończyć się niepowodzeniem dyskretnie operacji. Możesz sprawdzić awarię, sprawdzając właściwość statyczna Microsoft.Web.Redis.RedisSessionStateProvider.LastException. Wartość domyślna to true.
* **retryTimeoutInMilliseconds** — operacje, które nie zostaną ponowione w danym przedziale czasu w milisekundach. Pierwszym ponowieniem próby występuje po 20 MS, a następnie ponowne próby są wykonywane co sekundę, do czasu wygaśnięcia interwału retryTimeoutInMilliseconds. Natychmiast po tym okresie jeden raz końcowego jest próba powtórzenia operacji. Jeśli nadal kończy się niepowodzeniem, wyjątek jest zgłaszany ponownie do obiektu wywołującego, w zależności od ustawienia throwOnError. Wartość domyślna to 0, co oznacza brak ponownych prób.
* **databaseId** — Określa, które bazy danych na potrzeby pamięci podręcznej danych wyjściowych. Jeśli nie zostanie określony, jest używana domyślna wartość 0.
* **applicationName** — klucze są przechowywane w pamięci podręcznej redis jako `{<Application Name>_<Session ID>}_Data`. Ten schemat nazewnictwa umożliwia wielu aplikacjom na udostępnianie tego samego wystąpienia usługi Redis. Ten parametr jest opcjonalny, a jeśli nie zostanie określona wartość domyślna jest używana.
* **connectionTimeoutInMilliseconds** — to ustawienie pozwala zastąpić ustawienie connectTimeout klienta StackExchange.Redis. Jeśli nie zostanie określony, używane jest domyślne ustawienie connectTimeout 5000. Aby uzyskać więcej informacji, zobacz [modelu konfiguracji StackExchange.Redis](https://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** — to ustawienie pozwala zastąpić ustawienie syncTimeout klienta StackExchange.Redis. Jeśli nie zostanie określony, używane jest domyślne ustawienie syncTimeout 1000. Aby uzyskać więcej informacji, zobacz [modelu konfiguracji StackExchange.Redis](https://go.microsoft.com/fwlink/?LinkId=398705).
* **redisSerializerType** — to ustawienie umożliwia określenie niestandardowej serializacji zawartości sesji, które są wysyłane do usługi Redis. Określony typ musi implementować `Microsoft.Web.Redis.ISerializer` i zadeklarować publicznego konstruktora bez parametrów. Domyślnie `System.Runtime.Serialization.Formatters.Binary.BinaryFormatter` jest używany.

Aby uzyskać więcej informacji o tych właściwościach, zobacz oryginalnego wpis na blogu w [ogłoszenie dostawca stanu sesji ASP.NET dla usługi Redis](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

Nie zapomnij o komentarz standardowa InProc sesji stan dostawcy sekcji w z pliku web.config.

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

Gdy te kroki są wykonywane, aplikacja jest skonfigurowana na potrzeby pamięci podręcznej Azure redis Cache dostawca stanu sesji. Stan sesji jest używany w Twojej aplikacji, jest przechowywany w pamięci podręcznej Azure dla wystąpienia usługi Redis.

> [!IMPORTANT]
> Dane przechowywane w pamięci podręcznej musi być możliwy do serializacji, w przeciwieństwie do danych, które mogą być przechowywane w domyślnym sesja programu ASP.NET w pamięci stan dostawcy. Gdy jest używany dostawca stanu sesji dla pamięci podręcznej Redis, można się, że typy danych, które są przechowywane w stanie sesji są możliwe do serializacji.
> 
> 

## <a name="aspnet-session-state-options"></a>Opcje stanu sesji platformy ASP.NET

* W dostawcy stanu sesji w pamięci — ten dostawca przechowuje stanu sesji w pamięci. Zaletą używania tego dostawcy jest to proste i szybkie. Jednak nie można skalować aplikacji sieci Web, jeśli używasz dostawcy pamięci, ponieważ nie będzie on rozpowszechniany.
* Dostawca stanu sesji programu SQL Server — ten dostawca przechowuje stanu sesji w programie Sql Server. Użyj tego dostawcy, aby przechowywanie stanu sesji w magazynie trwałym. Umożliwia skalowanie aplikacji sieci Web, ale przy użyciu programu Sql Server dla sesji ma wpływ na wydajność Twojej aplikacji sieci Web. Możesz również użyć tego dostawcy z [konfiguracji OLTP w pamięci](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/11/28/asp-net-session-state-with-sql-server-in-memory-oltp/) do poprawy wydajności.
* Rozproszone w pamięci dostawcy stanu sesji takich jak pamięci podręcznej Azure redis Cache dostawca stanu sesji — ten dostawca zapewnia najlepsze cechy obu światów. Twoja aplikacja sieci Web może mieć prosty, szybkich i skalowalnych dostawca stanu sesji. Ponieważ ten dostawca przechowuje stanu sesji w pamięci podręcznej, aplikacja musi wziąć pod uwagę w wszystkich właściwości, które są skojarzone po rozmowie z dystrybucji w pamięci podręcznej, takie jak błędy przejściowe problemy z siecią. Aby uzyskać wskazówki na temat korzystania z pamięci podręcznej, zobacz [wskazówki dotyczące buforowania](../best-practices-caching.md) z Microsoft Patterns & wskazówki [projektowania aplikacji w chmurze platformy Azure i wytyczne dotyczące implementacji](https://github.com/mspnp/azure-guidance).

Aby uzyskać więcej informacji o stanie sesji i innych najlepszych rozwiązań, zobacz [najlepsze praktyki programowania aplikacji sieci Web (tworzenie rzeczywistych aplikacji w chmurze dzięki platformie Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices).

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z [dostawca wyjściowej pamięci podręcznej ASP.NET dla usługi Azure Cache dla usługi Redis](cache-aspnet-output-cache-provider.md).