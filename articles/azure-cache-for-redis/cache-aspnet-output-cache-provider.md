---
title: dostawca pamięci podręcznej ASP.NET dla pamięci podręcznej Azure dla redis
description: Dowiedz się, jak buforować ASP.NET danych wyjściowych strony przy użyciu usługi Azure Cache for Redis. Dostawca pamięci podręcznej wyjścia Redis jest mechanizmem magazynu poza procesem dla danych pamięci podręcznej wyjściowej.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 04/22/2018
ms.openlocfilehash: f1d8189068278b46e3ec3ea66875d79bb91e5e16
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010209"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>dostawca pamięci podręcznej ASP.NET dla pamięci podręcznej Azure dla redis

Dostawca pamięci podręcznej wyjścia Redis jest mechanizmem magazynu poza procesem dla danych pamięci podręcznej wyjściowej. Te dane są przeznaczone specjalnie dla pełnych odpowiedzi HTTP (buforowanie danych wyjściowych strony). Dostawca podłącza się do nowego punktu rozszerzalności dostawcy pamięci podręcznej wyjściowej, który został wprowadzony w ASP.NET 4.

Aby użyć dostawcy pamięci podręcznej wyjścia Redis, najpierw skonfiguruj pamięć podręczną, a następnie skonfiguruj aplikację ASP.NET przy użyciu pakietu NuGet dostawcy pamięci podręcznej Wyjścia Redis. W tym temacie przedstawiono wskazówki dotyczące konfigurowania aplikacji do używania dostawcy pamięci podręcznej wyjścia Redis. Aby uzyskać więcej informacji na temat tworzenia i konfigurowania pamięci podręcznej platformy Azure dla wystąpienia Redis, zobacz [Tworzenie pamięci podręcznej](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Przechowywanie ASP.NET danych wyjściowych strony w pamięci podręcznej

Aby skonfigurować aplikację kliencką w programie Visual Studio przy użyciu pakietu NuGet stanu sesji usługi Azure dla programu Redis Session, kliknij polecenie **Menedżer pakietów NuGet**, **konsola Menedżera pakietów** z menu **Narzędzia.**

W oknie `Package Manager Console` uruchom następujące polecenie.

```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

Pakiet NuGet dostawcy pamięci podręcznej wyjścia Redis ma zależność od pakietu StackExchange.Redis.StrongName. Jeśli pakiet StackExchange.Redis.StrongName nie jest obecny w projekcie, jest zainstalowany. Aby uzyskać więcej informacji na temat dostawcy pamięci podręcznej Wyjścia Redis NuGet pakietu, zobacz [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) NuGet strony.

>[!NOTE]
>Oprócz pakietu StackExchange.StrongName o silnej nazwie istnieje również wersja StackExchange.Redis o niesilniej nazwanych. Jeśli projekt używa wersji StackExchange.Redis o nieprzysmocnej nazwie, należy ją odinstalować; w przeciwnym razie wystąpią konflikty nazewnictwa w projekcie. Aby uzyskać więcej informacji na temat tych pakietów, zobacz [Konfigurowanie klientów pamięci podręcznej .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

Pakiet NuGet pobiera i dodaje wymagane odwołania do zestawu i dodaje następującą sekcję do pliku web.config. Ta sekcja zawiera wymaganą konfigurację dla aplikacji ASP.NET do korzystania z dostawcy pamięci podręcznej wyjścia Redis.

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

Skonfiguruj atrybuty z wartościami z bloku pamięci podręcznej w witrynie Microsoft Azure portal i skonfiguruj inne wartości zgodnie z potrzebami. Aby uzyskać instrukcje dotyczące uzyskiwania dostępu do właściwości pamięci podręcznej, zobacz [Konfigurowanie pamięci podręcznej azure dla ustawień Redis](cache-configure.md#configure-azure-cache-for-redis-settings).

| Atrybut | Typ | Domyślne | Opis |
| --------- | ---- | ------- | ----------- |
| *Hosta* | ciąg | "Localhost" | Adres IP serwera Redis lub nazwa hosta |
| *Portu* | dodatnia wartość całkowita | 6379 (inne niż TLS/SSL)<br/>6380 (TLS/SSL) | Port serwera Redis |
| *Accesskey* | ciąg | "" | Hasło serwera Redis po włączeniu autoryzacji Redis. Wartość jest domyślnie pusty ciąg, co oznacza, że dostawca stanu sesji nie będzie używać hasła podczas łączenia się z serwerem Redis. **Jeśli serwer Redis znajduje się w publicznie dostępnej sieci, takiej jak Pamięć podręczna Usługi Azure Redis, należy włączyć autoryzację Redis, aby zwiększyć bezpieczeństwo i podać bezpieczne hasło.** |
| *Ssl* | wartość logiczna | **False** | Czy połączyć się z serwerem Redis za pośrednictwem protokołu TLS. Ta wartość **jest** false domyślnie, ponieważ Redis nie obsługuje TLS po wyjęciu z pola. **Jeśli używasz usługi Azure Redis Cache, która obsługuje ssl po wyjęciu z pudełka, należy ustawić to na true, aby zwiększyć bezpieczeństwo.**<br/><br/>Port nieobjęty TLS jest domyślnie wyłączony dla nowych pamięci podręcznych. Określ **wartość true** dla tego ustawienia, aby użyć portu TLS. Aby uzyskać więcej informacji na temat włączania portu nienawiązanego TLS, zobacz sekcję [Porty dostępu](cache-configure.md#access-ports) w temacie [Konfigurowanie pamięci podręcznej.](cache-configure.md) |
| *numer danychIdNumber* | dodatnia wartość całkowita | 0 | *Ten atrybut można określić tylko za pośrednictwem web.config lub AppSettings.*<br/><br/>Określ, której bazy danych Redis użyć. |
| *connectionTimeoutInMilliseconds* | dodatnia wartość całkowita | Dostarczone przez StackExchange.Redis | Służy do *ustawiania ConnectTimeout* podczas tworzenia StackExchange.Redis.ConnectionMultiplexer. |
| *operacjaTimeoutInMilliseconds* | dodatnia wartość całkowita | Dostarczone przez StackExchange.Redis | Służy do *ustawiania SyncTimeout* podczas tworzenia StackExchange.Redis.ConnectionMultiplexer. |
| *connectionString* (prawidłowy ciąg połączenia StackExchange.Redis) | ciąg | *N/a* | Odwołanie do parametru AppSettings lub web.config, albo prawidłowy ciąg połączenia StackExchange.Redis. Ten atrybut może dostarczyć wartości dla *hosta,* *portu*, *accessKey*, *ssl*i innych atrybutów StackExchange.Redis. Aby uzyskać bliższe spojrzenie na *connectionString*, zobacz [Ustawianie connectionString](#setting-connectionstring) w sekcji [Notatki atrybutu.](#attribute-notes) |
| *ustawieniaClassName*<br/>*ustawieniaName* | ciąg<br/>ciąg | *N/a* | *Te atrybuty można określić tylko za pośrednictwem web.config lub AppSettings.*<br/><br/>Użyj tych atrybutów, aby zapewnić parametry połączenia. *settingsClassName* powinna być nazwą klasy kwalifikowanej zestawu, która zawiera metodę określoną przez *settingsMethodName*.<br/><br/>Metoda określona przez *ustawieniaMethodName* powinna być publiczna, statyczna i void (nie przyjmuje żadnych parametrów), z typem zwracanym **ciągu**. Ta metoda zwraca rzeczywisty ciąg połączenia. |
| *rejestrowanieClassName*<br/>*logowanieName* | ciąg<br/>ciąg | *N/a* | *Te atrybuty można określić tylko za pośrednictwem web.config lub AppSettings.*<br/><br/>Użyj tych atrybutów do debugowania aplikacji, udostępniając dzienniki ze stanu sesji/pamięci podręcznej wyjściowej wraz z dziennikami z StackExchange.Redis. *loggingClassName* powinien być nazwą klasy kwalifikowanej zestawu, która zawiera metodę określoną przez *logowanieMethodName*.<br/><br/>Metoda określona przez *logowanieMethodName* powinny być publiczne, statyczne i nieważne (nie należy przyjmować żadnych parametrów), z typem zwrotnym **System.IO.TextWriter**. |
| *Applicationname* | ciąg | Nazwa modułu bieżącego procesu lub "/" | *Tylko sessionStateProvider*<br/>*Ten atrybut można określić tylko za pośrednictwem web.config lub AppSettings.*<br/><br/>Prefiks nazwy aplikacji do użycia w pamięci podręcznej Redis. Klient może używać tej samej pamięci podręcznej Redis do różnych celów. Aby zapewnić, że klucze sesji nie zderzają się, może być poprzedzony nazwą aplikacji. |
| *Throwonerror* | wartość logiczna | true | *Tylko sessionStateProvider*<br/>*Ten atrybut można określić tylko za pośrednictwem web.config lub AppSettings.*<br/><br/>Czy zgłosić wyjątek, gdy wystąpi błąd.<br/><br/>Aby uzyskać więcej informacji na temat *throwOnError*, zobacz [uwagi na *throwOnError* ](#notes-on-throwonerror) w informacji [o atrybucie notatek](#attribute-notes) sekcji. |>*Microsoft.web.redis.redissessionStateProvider.LastException*. |
| *ponów czasomierzewpołowe* | dodatnia wartość całkowita | 5000 | *Tylko sessionStateProvider*<br/>*Ten atrybut można określić tylko za pośrednictwem web.config lub AppSettings.*<br/><br/>Jak długo ponowić próbę, gdy operacja nie powiedzie się. Jeśli ta wartość jest mniejsza niż *operationTimeoutInMilliseconds*, dostawca nie ponowi próby.<br/><br/>Aby uzyskać więcej informacji na temat *ponawianiaTimeoutInMilliseconds*, zobacz [uwagi dotyczące *ponawianiaTimeoutInMilliseconds* ](#notes-on-retrytimeoutinmilliseconds) w sekcji [Notatki atrybutu.](#attribute-notes) |
| *redisSerializerType* | ciąg | *N/a* | Określa nazwę typu kwalifikowanego zestawu klasy implementujejs. ISerializer i zawiera niestandardową logikę do serializacji i deserializacji wartości. Aby uzyskać więcej informacji, zobacz [Temat *redisSerializerType* ](#about-redisserializertype) w sekcji [Notatki atrybutu.](#attribute-notes) |

## <a name="attribute-notes"></a>Uwagi dotyczące atrybutów

### <a name="setting-connectionstring"></a>Ustawianie *połączeniaString*

Wartość *connectionString* jest używany jako klucz do pobierania rzeczywistego ciągu połączenia z AppSettings, jeśli taki ciąg istnieje w AppSettings. Jeśli nie znaleziono wewnątrz AppSettings, wartość *connectionString* będzie używany jako klucz do pobierania rzeczywistego ciągu połączenia z web.config **ConnectionString** sekcji, jeśli ta sekcja istnieje. Jeśli parametry połączenia nie istnieje w AppSettings lub web.config **ConnectionString** sekcji, dosłowna wartość *connectionString* będzie używany jako ciąg połączenia podczas tworzenia StackExchange.Redis.ConnectionMultiplexer.

Poniższe przykłady ilustrują sposób *connectionString* jest używany.

#### <a name="example-1"></a>Przykład 1

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

W `web.config`polu użyj powyższego klucza jako wartości parametru zamiast wartości rzeczywistej.

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

W `web.config`polu użyj powyższego klucza jako wartości parametru zamiast wartości rzeczywistej.

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

### <a name="notes-on-throwonerror"></a>Uwagi dotyczące *throwOnError*

Obecnie jeśli wystąpi błąd podczas operacji sesji, dostawca stanu sesji zgłosić wyjątek. Spowoduje to wyłączenie aplikacji.

To zachowanie zostało zmodyfikowane w sposób, który obsługuje oczekiwania istniejących użytkowników dostawcy stanu ASP.NET sesji, zapewniając jednocześnie możliwość działania na wyjątki, w razie potrzeby. Domyślne zachowanie nadal zgłasza wyjątek, gdy wystąpi błąd, zgodne z innymi dostawcami stanu ASP.NET sesji; istniejący kod powinien działać tak samo jak poprzednio.

Jeśli ustawisz *throwOnError* na **false**, a następnie zamiast zgłaszania wyjątku, gdy wystąpi błąd, zakończy się niepowodzeniem dyskretnie. Aby sprawdzić, czy wystąpił błąd, a jeśli tak, dowiedzieć się, jaki był wyjątek, sprawdź właściwość statyczną *Microsoft.Web.Redis.RedisSessionStateProvider.LastException*.

### <a name="notes-on-retrytimeoutinmilliseconds"></a>Uwagi dotyczące *ponawianiatimeoutInMilliseconds*

Zapewnia to pewną logikę ponawiania, aby uprościć przypadek, w którym niektóre operacje sesji należy ponowić próbę awarii z powodu takich rzeczy jak usterka sieci, a także pozwala kontrolować limit czasu ponawiania lub zrezygnować z ponownych prób całkowicie.

Jeśli *ustawisz retryTimeoutInMilliseconds* na liczbę, na przykład 2000, a następnie, gdy operacja sesji nie powiedzie się, ponowi próbę na 2000 milisekund przed potraktowaniem go jako błąd. Aby mieć dostawcę stanu sesji, aby zastosować tę logikę ponawiania, po prostu skonfiguruj limit czasu. Pierwsza ponowna próby nastąpi po 20 milisekundach, co jest wystarczające w większości przypadków, gdy wystąpi usterka sieci. Następnie ponowi próbę co sekundę, aż do upływu czasu. Zaraz po upływie limitu czasu ponowi próbę jeszcze raz, aby upewnić się, że nie odetnie limitu czasu o (co najwyżej) jedną sekundę.

Jeśli uważasz, że nie trzeba ponowić próbę (na przykład podczas uruchamiania serwera Redis na tym samym komputerze co aplikacja) lub jeśli chcesz obsługiwać logikę ponawiania samodzielnie, ustaw *retryTimeoutInMilliseconds* do 0.

### <a name="about-redisserializertype"></a>Informacje o *redisSerializerType*

Domyślnie serializacji do przechowywania wartości na Redis odbywa się w formacie binarnym dostarczonych przez **BinaryFormatter** klasy. Użyj *redisSerializerType,* aby określić nazwę kwalifikowanego typu zestawu klasy, która implementuje **microsoft.Web.Redis.ISerializer** i ma niestandardową logikę do serializacji i deserializacji wartości. Na przykład oto klasa serializatora Json przy użyciu JSON.NET:

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
            if (data == null)
            {
                return null;
            }
            return JsonConvert.DeserializeObject(Encoding.UTF8.GetString(data), _settings);
        }
    }
}
```

Zakładając, że ta klasa jest zdefiniowana w zestawie o nazwie **MyCompanyDll**, można ustawić parametr *redisSerializerType* go używać:

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

## <a name="output-cache-directive"></a>Dyrektywa o pamięci podręcznej danych wyjściowych

Dodaj dyrektywy OutputCache do każdej strony, dla której chcesz buforować dane wyjściowe.

```xml
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

W poprzednim przykładzie buforowane dane strony pozostaje w pamięci podręcznej przez 60 sekund, a inna wersja strony jest buforowana dla każdej kombinacji parametrów. Aby uzyskać więcej informacji na temat [@OutputCache](https://go.microsoft.com/fwlink/?linkid=320837)dyrektywy OutputCache, zobacz .

Po wykonaniu tych kroków aplikacja jest skonfigurowana do używania dostawcy pamięci podręcznej wyjścia Redis.

## <a name="third-party-output-cache-providers"></a>Zewnętrzni dostawcy pamięci podręcznej danych wyjściowych

* [Ncache](https://www.alachisoft.com/blogs/how-to-use-a-distributed-cache-for-asp-net-output-cache/)
* [Apache Zapalić](https://apacheignite-net.readme.io/docs/aspnet-output-caching)


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [dostawcą stanu ASP.NET sesji dla usługi Azure Cache dla programu Redis](cache-aspnet-session-state-provider.md).
