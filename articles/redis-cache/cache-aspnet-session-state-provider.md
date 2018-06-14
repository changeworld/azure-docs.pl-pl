---
title: Dostawca stanu sesji ASP.NET dla pamięci podręcznej | Dokumentacja firmy Microsoft
description: Dowiedz się, jak i przechowywanie stanu sesji ASP.NET przy użyciu pamięci podręcznej Redis Azure
services: redis-cache
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: tysonn
ms.assetid: 192f384c-836a-479a-bb65-8c3e6d6522bb
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 05/01/2017
ms.author: wesmc
ms.openlocfilehash: bb0c53433af8a679811f00bfff2efee94d211a24
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
ms.locfileid: "31518760"
---
# <a name="aspnet-session-state-provider-for-azure-redis-cache"></a>Dostawca stanu sesji ASP.NET dla usługi Azure Redis Cache
Pamięć podręczna Redis Azure udostępnia dostawcę stanu sesji, który służy do przechowywania Twojego sesji stanu w pamięci z pamięci podręcznej Redis zamiast bazy danych programu SQL Server. Aby użyć buforowania dostawcę stanu sesji, najpierw skonfigurować pamięć podręczną, a następnie skonfiguruj aplikację ASP.NET dla pamięci podręcznej przy użyciu pakietu NuGet stanu sesji pamięci podręcznej Redis.

Często nie jest praktyczne w aplikacji rzeczywistych chmury, aby uniknąć przechowywania jakiegoś stanu dla sesji użytkownika, ale niektóre podejścia mieć wpływ na wydajność i skalowalność więcej niż inne. Jeśli masz przechowywanie stanu, najlepszym rozwiązaniem jest zachowywanie małych rozmiarów ilość stanu i zapisz go w plikach cookie. Jeśli, który nie jest możliwe, dalej najlepszym rozwiązaniem jest użycie stanu sesji ASP.NET u dostawcy dla rozproszonych, w pamięci podręcznej. Najgorszy rozwiązania z punktu widzenia skalowalności i wydajności jest korzystanie z bazy danych kopii dostawcę stanu sesji. W tym temacie znajdują się wskazówki dotyczące przy użyciu dostawcy stanu sesji ASP.NET dla pamięci podręcznej Redis Azure. Aby uzyskać informacje o innych opcjach stanu sesji, zobacz [opcje stanu sesji ASP.NET](#aspnet-session-state-options).

## <a name="store-aspnet-session-state-in-the-cache"></a>Przechowywanie stanu sesji programu ASP.NET w pamięci podręcznej
Kliknij, aby skonfigurować aplikację klienta w programie Visual Studio przy użyciu pakietu NuGet stanu sesji pamięci podręcznej Redis **Menedżera pakietów NuGet**, **Konsola Menedżera pakietów** z **narzędzia** menu.

W oknie `Package Manager Console` uruchom następujące polecenie.
    
```
Install-Package Microsoft.Web.RedisSessionStateProvider
```

> [!IMPORTANT]
> Jeśli korzystasz z funkcji klastra z warstwy premium, należy użyć [pakietu RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 lub nowszej albo wyjątek zostanie zgłoszony. Przenoszenie 2.0.1 lub nowszy jest istotne zmiany; Aby uzyskać więcej informacji, zobacz [v2.0.0 fundamentalne zmiany szczegółów](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details). Podczas aktualizacji tego artykułu bieżąca wersja tego pakietu jest 2.2.3.
> 
> 

Pakiet NuGet dostawcę stanu sesji Redis ma zależność od pakietu StackExchange.Redis.StrongName. Jeśli w projekcie nie ma pakietu StackExchange.Redis.StrongName, jest zainstalowana.

>[!NOTE]
>Oprócz pakietu StackExchange.Redis.StrongName o silnych nazwach jest także wersja z systemem innym niż-o silnych nazwach programie StackExchange.Redis. Jeśli projekt korzysta z wersji programie StackExchange.Redis nie-o silnych nazwach, należy go odinstalować, w przeciwnym razie możesz pobrać konfliktów nazw w projekcie. Aby uzyskać więcej informacji na temat tych pakietów, zobacz [.NET Konfigurowanie klientów pamięci podręcznej](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

Pakiet NuGet pliki do pobrania i dodaje odwołania do wymaganych zestawów i dodaje następującą sekcję do pliku web.config. Ta sekcja zawiera wymagana konfiguracja dla aplikacji ASP.NET do używania dostawcy stanu sesji pamięci podręcznej Redis.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
    <!--
    <add name="MySessionStateStore"
           host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
        throwOnError = "true" [true|false]
        retryTimeoutInMilliseconds = "0" [number]
        databaseId = "0" [number]
        applicationName = "" [String]
        connectionTimeoutInMilliseconds = "5000" [number]
        operationTimeoutInMilliseconds = "5000" [number]
    />
    -->
    <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false"/>
    </providers>
</sessionState>
```

Komentarze sekcji przedstawiono przykład atrybutów i przykładowe ustawienia dla każdego atrybutu.

Skonfiguruj atrybuty wartościami z Twojej blok pamięci podręcznej w portalu Microsoft Azure oraz innych wartości zgodnie z potrzebami. Aby uzyskać instrukcje dotyczące uzyskiwania dostępu do właściwości pamięci podręcznej, zobacz [Redis skonfigurować ustawienia pamięci podręcznej](cache-configure.md#configure-redis-cache-settings).

* **host** — Określ punktu końcowego pamięci podręcznej.
* **port** — port bez protokołu SSL lub portem SSL, w zależności od ustawień protokołu ssl.
* **accessKey** — Użyj klucz podstawowy lub pomocniczy dla pamięci podręcznej.
* **Protokół SSL** — wartość true, jeśli chcesz zabezpieczyć komunikację pamięci podręcznej/klienta przy użyciu protokołu ssl; w przeciwnym razie wartość false. Należy określić właściwego portu.
  * Port bez obsługi protokołu SSL jest domyślnie wyłączony w przypadku nowych pamięci podręcznych. Określ wartość true dla tego ustawienia używał portu protokołu SSL. Aby uzyskać więcej informacji na temat włączania portu bez protokołu SSL, zobacz [porty dostępu](cache-configure.md#access-ports) sekcji [skonfigurować pamięć podręczną](cache-configure.md) tematu.
* **throwOnError** — jest to wartość PRAWDA, jeśli wyjątek zostanie wygenerowany, jeśli jest błąd, lub FAŁSZ, jeśli chce operację, aby zakończyć się niepowodzeniem w trybie dyskretnym. Z powodu błędu można sprawdzić przez sprawdzenie właściwości statycznej Microsoft.Web.Redis.RedisSessionStateProvider.LastException. Wartość domyślna to true.
* **retryTimeoutInMilliseconds** — operacji, które nie są zwalniane w danym przedziale czasu wyrażona w milisekundach. Pierwsza próba występuje po 20 milisekund, a następnie wykonywane co sekundę do momentu wygaśnięcia interwał retryTimeoutInMilliseconds. Natychmiast po tym okresie jeden raz ostatni jest ponowić próbę operacji. Jeśli nadal kończy się niepowodzeniem, jest zgłaszany wyjątek powrotem do wywołującego, w zależności od ustawienia throwOnError. Wartość domyślna to 0, co oznacza brak ponownych prób.
* **databaseId** — Określa bazę danych do użycia na potrzeby pamięci podręcznej danych wyjściowych. Jeśli nie zostanie określony, zostanie użyta domyślna wartość 0.
* **applicationName** — klucze są przechowywane w pamięci podręcznej redis jako `{<Application Name>_<Session ID>}_Data`. Ten schemat nazewnictwa umożliwia wielu aplikacjom współużytkują to samo wystąpienie pamięci podręcznej Redis. Ten parametr jest opcjonalny i jeśli nie zostanie określona wartość domyślna jest używany.
* **connectionTimeoutInMilliseconds** — to ustawienie pozwala zastąpić ustawienie connectTimeout w programie StackExchange.Redis klienta. Jeśli nie zostanie określona, używane jest domyślne ustawienie connectTimeout 5000. Aby uzyskać więcej informacji, zobacz [model konfiguracji w programie StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** — to ustawienie pozwala zastąpić ustawienie syncTimeout w programie StackExchange.Redis klienta. Jeśli nie zostanie określona, używane jest domyślne ustawienie syncTimeout 1000. Aby uzyskać więcej informacji, zobacz [model konfiguracji w programie StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).

Aby uzyskać więcej informacji na temat tych właściwości, zobacz oryginalnego powiadomienie post blogu w [Announcing ASP.NET dostawcę stanu sesji dla pamięci podręcznej Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

Nie zapomnij o komentarz standardowe InProc sesji stanu dostawcy sekcji w pliku web.config.

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

Gdy te kroki są wykonywane, aplikacja jest skonfigurowana do używania dostawcy stanu sesji pamięci podręcznej Redis. Gdy używasz stanu sesji w aplikacji, jest ona przechowywana w wystąpienia pamięci podręcznej Redis Azure.

> [!IMPORTANT]
> Danych przechowywanych w pamięci podręcznej musi być możliwy do serializacji, w przeciwieństwie do danych, które mogą być przechowywane w domyślnym sesji platformy ASP.NET w pamięci stanu dostawcy. Gdy używany jest dostawca stanu sesji dla pamięci podręcznej Redis, upewnij się że typy danych, które są przechowywane w stanie sesji są możliwy do serializacji.
> 
> 

## <a name="aspnet-session-state-options"></a>Opcje stanu sesji ASP.NET
* Dostawca stanu sesji pamięci — ten dostawca przechowuje stanu sesji w pamięci. Zaletą używania tego dostawcy jest jest proste i szybkie. Jednak nie można skalować aplikacji sieci Web, jeśli używasz w pamięci dostawcy, ponieważ nie będzie on rozpowszechniany.
* Dostawca stanu sesji SQL Server — ten dostawca przechowuje stanu sesji w programie Sql Server. Użyj tego dostawcy, aby przechowywać dane stanu sesji w trwałych. Można skalować aplikacji sieci Web, ale dla sesji przy użyciu programu Sql Server ma negatywny wpływ na wydajność w aplikacji sieci Web. Można również użyć tego dostawcy z [konfiguracji OLTP w pamięci](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/11/28/asp-net-session-state-with-sql-server-in-memory-oltp/) w celu zwiększenia wydajności.
* Rozproszone w pamięci dostawcę stanu sesji takich jak Redis pamięci podręcznej dostawcę stanu sesji — ten dostawca zapewnia najlepsze cechy obu tych środowisk. Aplikacja sieci Web może mieć proste, szybkie i skalowalne dostawcę stanu sesji. Ponieważ ten dostawca przechowuje stanu sesji w pamięci podręcznej, aplikacja musi wziąć pod uwagę w wszystkie charakterystyki skojarzonej po rozmowie z rozproszonych w pamięci podręcznej, takie jak awarie sieci. Najlepsze rozwiązania dotyczące używania pamięci podręcznej, zobacz [buforowanie wskazówki](../best-practices-caching.md) z Microsoft Patterns & rozwiązań [projekt aplikacji w chmurze Azure i wskazówki dotyczące implementacji](https://github.com/mspnp/azure-guidance).

Aby uzyskać więcej informacji o stanie sesji i innych najlepszych rozwiązań, zobacz [Web Development Best Practices (kompilowanie praktyczne aplikacje w chmurze platformy Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices).

## <a name="next-steps"></a>Kolejne kroki
Zapoznaj się z [dostawcy wyjściowej pamięci podręcznej programu ASP.NET dla pamięci podręcznej Redis Azure](cache-aspnet-output-cache-provider.md).

