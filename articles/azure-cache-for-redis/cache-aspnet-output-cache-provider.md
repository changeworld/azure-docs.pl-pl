---
title: Dostawca pamięci podręcznej danych wyjściowych platformy ASP.NET dla usługi Azure Cache dla usługi Redis
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
ms.date: 04/22/2018
ms.author: yegu
ms.openlocfilehash: a93d21b07dc486f743694ee99f60018ed4ef517c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64943870"
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
>Oprócz pakietu StackExchange.Redis.StrongName o silnej nazwie jest również wersja niż-o silnej nazwie StackExchange.Redis. Jeśli projekt używa wersji StackExchange.Redis innych niż-o silnej nazwie należy odinstalować je obsługiwać. w przeciwnym razie wystąpią konflikty nazewnictwa w projekcie. Aby uzyskać więcej informacji o tych paloetach, zobacz [.NET Konfigurowanie klientów pamięci podręcznej](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

Pakiet NuGet pobiera i dodaje wymagane odwołania do zestawu i dodanie sekcji poniżej do pliku web.config. Ta sekcja zawiera wymaganej konfiguracji dla aplikacji ASP.NET do używania dostawcy pamięci podręcznej danych wyjściowych usługi Redis.

```xml
<caching>
  <outputCache defaultProvider="MyRedisOutputCache">
    <providers>
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider"
           host=""
           accessKey=""
           ssl="true" />
    </providers>
  </outputCache>
</caching>
```

Konfigurowanie atrybutów przy użyciu wartości z bloku usługi pamięci podręcznej w portalu Microsoft Azure i innych wartości zostaną skonfigurowane zgodnie z potrzebami. Aby uzyskać instrukcje dotyczące uzyskiwania dostępu do właściwości pamięci podręcznej, zobacz [Konfigurowanie usługi Azure Cache ustawień pamięci podręcznej Redis](cache-configure.md#configure-azure-cache-for-redis-settings).

| Atrybut | Typ | Domyślne | Opis |
| --------- | ---- | ------- | ----------- |
| *host* | string | "localhost" | Usługa Redis server nazwę lub adres IP hosta |
| *port* | dodatnia liczba całkowita | 6379 (non-SSL)<br/>6380 (SSL) | Port serwera redis |
| *accessKey* | string | "" | Hasło serwera redis, po włączeniu autoryzacji usługi Redis. Wartość jest pusty ciąg, domyślnie, co oznacza, że dostawca stanu sesji nie będzie żadnych hasło używane podczas nawiązywania połączenia z serwerem Redis. **Jeśli serwer pamięci podręcznej Redis znajduje się w publicznie dostępnej sieci, takich jak usługi Azure Redis Cache, należy włączyć autoryzacji pamięci podręcznej Redis zwiększyć bezpieczeństwo, a następnie podaj bezpieczne hasło.** |
| *ssl* | wartość logiczna | **false** | Określa, czy nawiązać połączenie z serwerem Redis za pomocą protokołu SSL. Ta wartość jest **false** domyślnie ponieważ pamięci podręcznej Redis nie obsługuje protokołu SSL gotowe. **Jeśli używasz usługi Azure Redis Cache obsługujący protokół SSL gotowych, pamiętaj Ustaw na wartość true, aby poprawić bezpieczeństwo.**<br/><br/>Port bez obsługi protokołu SSL jest domyślnie wyłączony w przypadku nowych pamięci podręcznych. Określ **true** dla tego ustawienia używał portu protokołu SSL. Aby uzyskać więcej informacji na temat włączania portu bez protokołu SSL, zobacz [porty dostępu](cache-configure.md#access-ports) sekcji [Konfigurowanie pamięci podręcznej](cache-configure.md) tematu. |
| *databaseIdNumber* | dodatnia liczba całkowita | 0 | *Ten atrybut może być określona tylko za pośrednictwem plik web.config lub AppSettings.*<br/><br/>Określ bazę danych usługi Redis do użycia. |
| *connectionTimeoutInMilliseconds* | dodatnia liczba całkowita | Dostarczone przez StackExchange.Redis | Używane do ustawiania *ConnectTimeout* podczas tworzenia StackExchange.Redis.ConnectionMultiplexer. |
| *operationTimeoutInMilliseconds* | dodatnia liczba całkowita | Dostarczone przez StackExchange.Redis | Używane do ustawiania *SyncTimeout* podczas tworzenia StackExchange.Redis.ConnectionMultiplexer. |
| *element connectionString* (StackExchange.Redis prawidłowe parametry połączenia) | string | *n/d* | Parametr odwołania do AppSettings pliku web.config, lub w przeciwnym razie prawidłowymi parametrami połączenia StackExchange.Redis. Ten atrybut można podać wartości *hosta*, *portu*, *accessKey*, *ssl*i inne atrybuty StackExchange.Redis. Dla bliżej przyjrzeć *connectionString*, zobacz [ustawienie connectionString](#setting-connectionstring) w [atrybutu uwagi](#attribute-notes) sekcji. |
| *settingsClassName*<br/>*settingsMethodName* | string<br/>string | *n/d* | *Te atrybuty można określić wyłącznie za pośrednictwem plik web.config lub AppSettings.*<br/><br/>Użyj tych atrybutów, aby podać parametry połączenia. *settingsClassName* powinna być nazwa klasy kwalifikowanej zestawu zawierającego metodę określone przez *settingsMethodName*.<br/><br/>Metoda wskazywana przez *settingsMethodName* powinny być publiczne, statycznych i void (nie przyjmuje żadnych parametrów), z typem zwracanym **ciąg**. Ta metoda zwraca rzeczywistych parametrów połączenia. |
| *loggingClassName*<br/>*loggingMethodName* | string<br/>string | *n/d* | *Te atrybuty można określić wyłącznie za pośrednictwem plik web.config lub AppSettings.*<br/><br/>Użyj tych atrybutów do debugowania aplikacji, zapewniając dzienniki z pamięci podręcznej stanu sesji/wyjściowych oraz Dzienniki z StackExchange.Redis. *loggingClassName* powinna być nazwa klasy kwalifikowanej zestawu zawierającego metodę określone przez *loggingMethodName*.<br/><br/>Metoda wskazywana przez *loggingMethodName* powinny być publiczne, statycznych i void (nie przyjmuje żadnych parametrów), z typem zwracanym **System.IO.TextWriter**. |
| *applicationName* | string | Nazwa modułu bieżącego procesu lub "/" | *Tylko SessionStateProvider*<br/>*Ten atrybut może być określona tylko za pośrednictwem plik web.config lub AppSettings.*<br/><br/>Prefiks nazwy aplikacji do użycia w pamięci podręcznej Redis. Klient może używać tej samej pamięci podręcznej redis Cache do różnych celów. Aby upewnić się, że nie powodują konfliktu kluczy sesji, mogą być poprzedzone nazwą aplikacji. |
| *throwOnError* | wartość logiczna | true | *Tylko SessionStateProvider*<br/>*Ten atrybut może być określona tylko za pośrednictwem plik web.config lub AppSettings.*<br/><br/>Określa, czy zgłosić wyjątek, gdy wystąpi błąd.<br/><br/>Aby uzyskać więcej informacji na temat *throwOnError*, zobacz [notatki na *throwOnError* ](#notes-on-throwonerror) w [atrybutu uwagi](#attribute-notes) sekcji. |>*Microsoft.Web.Redis.RedisSessionStateProvider.LastException*. |
| *retryTimeoutInMilliseconds* | dodatnia liczba całkowita | 5000 | *Tylko SessionStateProvider*<br/>*Ten atrybut może być określona tylko za pośrednictwem plik web.config lub AppSettings.*<br/><br/>Jak długo, aby ponowić próbę, gdy operacja nie powiedzie się. Jeśli ta wartość jest mniejsza niż *operationTimeoutInMilliseconds*, dostawca nie podejmie kolejnej próby.<br/><br/>Aby uzyskać więcej informacji na temat *retryTimeoutInMilliseconds*, zobacz [notatki na *retryTimeoutInMilliseconds* ](#notes-on-retrytimeoutinmilliseconds) w [atrybutu uwagi](#attribute-notes) sekcji. |
| *redisSerializerType* | string | *n/d* | Określa nazwę zestawu kwalifikowanego typu klasy, która implementuje Microsoft.Web.Redis. ISerializer i który zawiera logikę niestandardową do serializacji i deserializacji wartości. Aby uzyskać więcej informacji, zobacz [o *redisSerializerType* ](#about-redisserializertype) w [atrybutu uwagi](#attribute-notes) sekcji. |
|

## <a name="attribute-notes"></a>Uwagi dotyczące atrybutów

### <a name="setting-connectionstring"></a>Ustawienie *parametry połączenia*

Wartość *connectionString* jest używany jako klucz można pobrać rzeczywistych parametrów połączenia z AppSettings, jeśli istnieje taki ciąg AppSettings. Jeśli nie zostanie znaleziony w AppSettings, wartość *connectionString* posłuży jako klucz można pobrać rzeczywistymi parametrami połączenia z pliku web.config **ConnectionString** sekcji, jeśli istnieje w tej sekcji. Jeśli ciąg połączenia nie istnieje w AppSettings lub pliku web.config **ConnectionString** sekcji wartość literału *connectionString* będzie służyć jako parametry połączenia, podczas tworzenia StackExchange.Redis.ConnectionMultiplexer.

W poniższych przykładach pokazano sposób *connectionString* jest używany.

#### <a name="example-1"></a>Przykład 1

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

W `web.config`, użyj powyżej klucza jako wartość parametru zamiast rzeczywistych wartości.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-2"></a>Przykład 2

```xml
<appSettings>
    <add key="MyRedisConnectionString" value="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</appSettings>
```

W `web.config`, użyj powyżej klucza jako wartość parametru zamiast rzeczywistych wartości.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-3"></a>Przykład 3

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False"/>
    </providers>
</sessionState>
```

### <a name="notes-on-throwonerror"></a>Informacje o sprawie *throwOnError*

Obecnie Jeśli wystąpi błąd podczas operacji sesji, dostawca stanu sesji spowoduje zgłoszenie wyjątku. Zamyka aplikację.

To zachowanie został zmodyfikowany w sposób, który obsługuje oczekiwania istniejących użytkowników dostawcy stanu sesji platformy ASP.NET, a jednocześnie zapewniając możliwość działania na wyjątki, jeśli to konieczne. Domyślne zachowanie nadal zgłasza wyjątek, gdy wystąpi błąd, zgodne z innymi dostawcami stanu sesji ASP.NET; istniejący kod powinien działać tak jak wcześniej.

Jeśli ustawisz *throwOnError* do **false**, a następnie zamiast zgłaszać wyjątek, gdy wystąpi błąd, zakończy się niepowodzeniem dyskretnie. Aby zobaczyć, jeśli wystąpił błąd, a jeśli tak, można wykryć wyjątek to, sprawdź właściwość statyczna *Microsoft.Web.Redis.RedisSessionStateProvider.LastException*.

### <a name="notes-on-retrytimeoutinmilliseconds"></a>Informacje o sprawie *retryTimeoutInMilliseconds*

Dzięki temu niektóre Logika ponawiania, aby uprościć przypadek, gdzie pewnej operacji sesji powinna ponowić w przypadku niepowodzenia z powodu elementów, takich jak błąd sieci, umożliwiając także do kontrolowania limitu czasu ponawiania lub całkowicie zrezygnować ponownych prób.

Jeśli ustawisz *retryTimeoutInMilliseconds* do liczby, na przykład 2000, następnie w przypadku niepowodzenia operacji sesji ponowi próbę do 2000 MS zanim zostanie to potraktowane jako błąd. Dlatego aby dostawca stanu sesji, aby zastosować tę logikę ponawiania, po prostu limit czasu. Pierwszy ponowna próba odbędzie się po 20 MS, który jest wystarczające w większości przypadków sytuacji błędem sieci. Po tym ponowi próbę co sekundę, dopóki nie upłynie limit czasu. Od razu po limit czasu ponowi próbę jeszcze raz, aby upewnić się, że jej nie będzie odcięte limitu czasu przez (maksymalnie) sekundy.

Jeśli nie potrzebujesz ponów próbę wykonania (na przykład po uruchomieniu serwera Redis na tym samym komputerze co aplikacja) lub jeśli chcesz obsługiwać Logika ponawiania, samodzielnie, ustaw *retryTimeoutInMilliseconds* na 0.

### <a name="about-redisserializertype"></a>Temat *redisSerializerType*

Domyślnie serializacji do przechowywania wartości na podstawie usługi Redis odbywa się w formacie binarnym, dostarczone przez **elementu** klasy. Użyj *redisSerializerType* do określenia nazwy zestawu kwalifikowanego typu klasy, która implementuje **Microsoft.Web.Redis.ISerializer** i niestandardowej logiki do serializacji i deserializacji wartości. Na przykład poniżej przedstawiono klasy serializator Json przy użyciu struktury JSON.NET:

```cs
namespace MyCompany.Redis
{
    public class JsonSerializer : ISerializer
    {
        private static JsonSerializerSettings _settings = new JsonSerializerSettings() { TypeNameHandling = TypeNameHandling.All };

        public byte[] Serialize(object data)
        {
            return Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(data, _settings));
        }

        public object Deserialize(byte[] data)
        {
            if (data == null)6t6
            {
                return null;
            }
            return JsonConvert.DeserializeObject(Encoding.UTF8.GetString(data), _settings);
        }
    }
}
```

Zakładając, że ta klasa jest zdefiniowana w zestawie o nazwie **MyCompanyDll**, można ustawić parametru *redisSerializerType* z niego korzystać:

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvider"
             name = "MySessionStateStore"
             redisSerializerType = "MyCompany.Redis.JsonSerializer,MyCompanyDll"
             ... />
    </providers>
</sessionState>
```

## <a name="output-cache-directive"></a>Dane wyjściowe pamięci podręcznej — dyrektywa

Dodaj dyrektywę OutputCache do każdej strony, dla którego chcesz buforować dane wyjściowe.

```xml
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

W poprzednim przykładzie dane strony pamięci podręcznej pozostaje w pamięci podręcznej 60 sekund, a inna wersja strony są buforowane dla każdej kombinacji parametrów. Aby uzyskać więcej informacji na temat dyrektywy OutputCache zobacz [ @OutputCache ](https://go.microsoft.com/fwlink/?linkid=320837).

Gdy te kroki są wykonywane, aplikacja jest skonfigurowana do używania dostawcy pamięci podręcznej danych wyjściowych usługi Redis.

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z [dostawca stanu sesji ASP.NET dla usługi Azure Cache dla usługi Redis](cache-aspnet-session-state-provider.md).
