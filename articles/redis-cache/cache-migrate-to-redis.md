---
title: Migrowanie aplikacji usługi zarządzana pamięć podręczna Redis - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przeprowadzić migrację usługi zarządzana pamięć podręczna i pamięci podręcznej w roli aplikacji w pamięci podręcznej Redis Azure
services: redis-cache
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: tysonn
ms.assetid: 041f077b-8c8e-4d7c-a3fc-89d334ed70d6
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 05/30/2017
ms.author: wesmc
ms.openlocfilehash: f499925ecea8ca127c90691f7d92e74e8df68cf9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34639490"
---
# <a name="migrate-from-managed-cache-service-to-azure-redis-cache"></a>Migrowanie z usługi zarządzana pamięć podręczna do pamięci podręcznej Azure Redis
Migrowanie aplikacji korzystających z usługi zarządzana pamięć podręczna Azure z pamięcią podręczną Redis Azure można wykonywać przy minimalnych zmianach w aplikacji, w zależności od funkcji usługi zarządzana pamięć podręczna używana przez aplikację do buforowania. Interfejsy API są dokładnie takie same są podobne, i znacznie istniejący kod korzystającą z usługi zarządzana pamięć podręczna w celu dostępu do pamięci podręcznej mogą być ponownie używane przy minimalnych zmianach. W tym artykule przedstawiono sposób niezbędną konfigurację i zmian w aplikacji do migracji aplikacji usługi zarządzana pamięć podręczna do użycia pamięci podręcznej Redis Azure i pokazuje, jak niektóre funkcje pamięci podręcznej Redis Azure używane do implementowania funkcji Zarządzana pamięć podręczna usługi pamięć podręczna.

>[!NOTE]
>Zarządzane usługi pamięć podręczna i pamięci podręcznej w roli zostały [wycofane](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/) 30 listopada 2016 r. Jeżeli masz wszystkie wdrożenia pamięci podręcznej w roli, które chcesz przeprowadzić migrację do pamięci podręcznej Redis Azure, należy wykonać czynności opisane w tym artykule.

## <a name="migration-steps"></a>Kroki migracji
Poniższe kroki są wymagane do migracji aplikacji usługi zarządzana pamięć podręczna, aby użyć pamięci podręcznej Redis Azure.

* Mapowanie funkcji dla usługi zarządzana pamięć podręczna pamięci podręcznej Redis Azure
* Wybierz ofertę pamięci podręcznej
* Tworzenie pamięci podręcznej
* Konfigurowanie klientów pamięci podręcznej
  * Usuń konfigurację usługi zarządzana pamięć podręczna
  * Konfigurowanie klienta pamięci podręcznej przy użyciu pakietu NuGet w programie StackExchange.Redis
* Migrowanie kodu usługi zarządzana pamięć podręczna
  * Łączenie z pamięcią podręczną, za pomocą klasy ConnectionMultiplexer
  * Typy pierwotne danych programu Access w pamięci podręcznej
  * Praca z obiektami platformy .NET w pamięci podręcznej
* Migracja stanu sesji ASP.NET i buforowanie danych wyjściowych w pamięci podręcznej Redis Azure 

## <a name="map-managed-cache-service-features-to-azure-redis-cache"></a>Mapowanie funkcji dla usługi zarządzana pamięć podręczna pamięci podręcznej Redis Azure
Azure usługi zarządzana pamięć podręczna i pamięcią podręczną Redis Azure są podobne, ale niektóre funkcje ich wdrożenie na różne sposoby. W tej sekcji opisano niektóre różnice i znajdują się wskazówki dotyczące implementowania funkcji usługi zarządzana pamięć podręczna w pamięci podręcznej Redis Azure.

| Zarządzane funkcji usługi pamięć podręczna | Obsługa usługi pamięć podręczna zarządzanych | Obsługa pamięci podręcznej Redis Azure |
| --- | --- | --- |
| Bufory o nazwie |Domyślne pamięci podręcznej jest skonfigurowany, a w pamięci podręcznej standardowa i Premium można skonfigurować ofert maksymalnie dziewięć dodatkowe o nazwie pamięci podręcznych w razie potrzeby. |Pamięci podręcznej pamięci podręcznej Redis Azure ma można skonfigurować liczbę baz danych (domyślnie 16), które mogą służyć do zaimplementowania podobne funkcje do nazwanego pamięci podręcznych. Aby uzyskać więcej informacji, zobacz [What are Redis databases?](cache-faq.md#what-are-redis-databases) (Co to są bazy danych Redis?) i [Default Redis server configuration](cache-configure.md#default-redis-server-configuration) (Domyślna konfiguracja serwera Redis). |
| Wysoka dostępność |Zapewnia wysoką dostępność dla elementów w pamięci podręcznej w pamięci podręcznej oferty standardowa i Premium. Jeśli elementy zostaną utracone z powodu błędu, kopie zapasowe elementów w pamięci podręcznej są nadal dostępne. Zapisuje dane w buforze dodatkowej stają się synchronicznie. |Wysoka dostępność jest dostępna w standardowa i Premium pamięci podręcznej oferty, które w konfiguracji podstawowej/repliki dwoma węzłami (parę podstawowy/repliki ma każdego niezależnego fragmentu w pamięci podręcznej Premium). Zapisuje dane w replice są wykonywane asynchronicznie. Aby uzyskać więcej informacji, zobacz [cennik usługi pamięć podręczna Redis Azure](https://azure.microsoft.com/pricing/details/cache/). |
| Powiadomienia |Umożliwia klientom na odbieranie asynchroniczne powiadomienia w przypadku wystąpienia różnych operacji pamięci podręcznej na nazwanym pamięci podręcznej. |Aplikacje klienckie można użyć pamięci podręcznej Redis pub/sub lub [powiadomienia przestrzeni kluczy](cache-configure.md#keyspace-notifications-advanced-settings) do osiągnięcia podobne funkcje powiadomień. |
| Lokalna pamięć podręczna |Przechowuje kopię obiektów buforowane lokalnie na kliencie bardzo szybki dostęp. |Aplikacje klienckie należy zaimplementować tę funkcję za pomocą słownika lub podobne struktury danych. |
| Zasady wykluczania |Brak lub LRU. Domyślna zasada jest LRU. |Pamięć podręczna Redis Azure obsługuje następujące zasady wykluczania: volatile lru, allkeys lru, losowe volatile, allkeys — losowe ttl volatile, noeviction. Domyślna zasada jest volatile lru. Aby uzyskać więcej informacji, zobacz [Redis domyślna konfiguracja serwera](cache-configure.md#default-redis-server-configuration). |
| Zasady wygasania |Domyślne zasady wygasania jest bezwzględną i wygaśnięcia domyślny interwał to 10 minut. Dostępne są również zasady przedłużanie i Never. |Domyślnie nie wygasają elementów w pamięci podręcznej, ale wygaśnięcia można skonfigurować dla poszczególnych zapisu przy użyciu pamięci podręcznej zestawu przeciążenia. |
| Regiony i znakowanie |Regiony są podgrupy dla elementów w pamięci podręcznej. Regiony obsługuje również adnotacji elementów pamięci podręcznej z dodatkowe ciągi opisową o nazwie tagów. Regiony obsługuje możliwość wykonywania operacji wyszukiwania na wszystkie elementy oznakowanych w tym regionie. Wszystkie elementy w obrębie regionu znajdują się w jednym węźle klastra, pamięci podręcznej. |Pamięć podręczna Redis składa się z jednego węzła (o ile nie włączono klaster Redis) tak, aby nie stosować pojęcie regiony usługi zarządzana pamięć podręczna. Podczas pobierania kluczy, więc może być osadzony wewnątrz nazwy klucza i używane do pobierania elementów później opisowe tagi redis obsługuje przeszukiwanie i operacje symboli wieloznacznych. Przykład wdrażania rozwiązania znakowania przy użyciu pamięci podręcznej Redis, zobacz [implementacja pamięci podręcznej znakowanie z pamięci podręcznej Redis](http://stackify.com/implementing-cache-tagging-redis/). |
| Serializacja |Zarządzana pamięć podręczna obsługuje NetDataContractSerializer, elementu BinaryFormatter między elementami i stosowania niestandardowych serializatorów. Wartość domyślna to NetDataContractSerializer. |Jest zobowiązany do serializacji obiektów .NET przed umieszczenie ich w pamięci podręcznej z wyborem serializatora do Deweloper aplikacji klienta aplikacji klienckiej. Aby uzyskać więcej informacji i przykładowy kod, zobacz [Praca z obiektami .NET w pamięci podręcznej](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache). |
| Emulator pamięci podręcznej |Zarządzana pamięć podręczna zawiera emulator lokalnej pamięci podręcznej. |Pamięć podręczna Redis Azure ma emulatora, ale można [uruchamianie kompilacji MSOpenTech redis server.exe lokalnie](cache-faq.md#cache-emulator) zapewnienie obsługi emulatora. |

## <a name="choose-a-cache-offering"></a>Wybierz ofertę pamięci podręcznej
Usługa Microsoft Azure Redis Cache jest dostępna w następujących warstwach:

* **Podstawowa** — jeden węzeł. Wiele rozmiarów do 53 GB.
* **Standardowa** — dwa węzły (węzeł podstawowy i węzeł repliki). Wiele rozmiarów do 53 GB. Umowa SLA na poziomie 99,9%.
* **Premium** — dwa węzły (węzeł podstawowy i węzeł repliki) zawierające do 10 fragmentów. Wiele rozmiarów od 6 GB do 530 GB. Wszystkie funkcje warstwy Standardowej i dodatkowe funkcje, m.in. obsługa [klastra Redis](cache-how-to-premium-clustering.md), [stanu trwałego pamięci podręcznej Redis](cache-how-to-premium-persistence.md) oraz usługi [Azure Virtual Network](cache-how-to-premium-vnet.md). Umowa SLA na poziomie 99,9%.

Poszczególne warstwy różnią się od siebie pod względem funkcji i cen. Funkcje zostały omówione w dalszej części tego przewodnika, a także aby uzyskać więcej informacji o cenach, zobacz [szczegóły cennika pamięci podręcznej](https://azure.microsoft.com/pricing/details/cache/).

Punkt początkowy dla migracji jest wybierz rozmiar równy rozmiarowi poprzedniej pamięci podręcznej usługi zarządzana pamięć podręczna, a następnie skalować w górę lub w dół w zależności od wymagań aplikacji. Aby uzyskać więcej informacji o wyborze prawo oferty usługi pamięć podręczna Redis Azure, zobacz [jakie oferty pamięć podręczna Redis i rozmiar użyć](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## <a name="create-a-cache"></a>Tworzenie pamięci podręcznej
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>Konfigurowanie klientów pamięci podręcznej
Po pamięci podręcznej zostało utworzone i skonfigurowane, następnym krokiem jest do usunięcia konfiguracji usługi zarządzana pamięć podręczna, a następnie dodaj konfigurację pamięci podręcznej Redis Azure i odwołuje się tak, aby klienci pamięci podręcznej mogą uzyskiwać dostęp do pamięci podręcznej.

* Usuń konfigurację usługi zarządzana pamięć podręczna
* Konfigurowanie klienta pamięci podręcznej przy użyciu pakietu NuGet w programie StackExchange.Redis

### <a name="remove-the-managed-cache-service-configuration"></a>Usuń konfigurację usługi zarządzana pamięć podręczna
Aplikacje klienckie można skonfigurować dla pamięci podręcznej Redis Azure, istniejącą konfigurację usługi zarządzana pamięć podręczna i odwołania do zestawów muszą zostać usunięte przez odinstalowanie pakietu NuGet usługi Service pamięci podręcznej zarządzane.

Aby odinstalować pakiet zarządzania pamięci podręcznej NuGet usługi, kliknij prawym przyciskiem myszy projekt klienta w **Eksploratora rozwiązań** i wybierz polecenie **Zarządzaj pakietami NuGet**. Wybierz **zainstalowane pakiety** węzła i typu W**indowsAzure.Caching** w polu wyszukiwania zainstalowanych pakietów. Wybierz **Windows** **pamięć podręczna Azure** (lub **Windows** **buforowanie Azure** w zależności od wersji pakietu NuGet), kliknij przycisk **Odinstaluj**, a następnie kliknij przycisk **Zamknij**.

![Odinstaluj pakiet NuGet usługi zarządzana pamięć podręczna Azure](./media/cache-migrate-to-redis/IC757666.jpg)

Odinstalowywanie pakietu NuGet usługi Service pamięci podręcznej zarządzane usuwa usługi zarządzana pamięć podręczna zestawów i wpisy usługi zarządzana pamięć podręczna w pliku app.config lub web.config aplikacji klienckiej. Ponieważ niektóre ustawienia dostosowane nie mogą zostać usunięte, podczas odinstalowywania pakietu NuGet, otwórz plik web.config lub app.config i upewnij się, że następujące elementy są usuwane.

Upewnij się, że `dataCacheClients` wpis zostanie usunięty z `configSections` elementu. Nie usuwaj całą `configSections` element; po prostu usuwa `dataCacheClients` wpisu, jeśli jest obecny.

```xml
<configSections>
  <!-- Existing sections omitted for clarity. -->
  <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
</configSections>
```

Upewnij się, że `dataCacheClients` sekcji zostanie usunięta. `dataCacheClients` Sekcji będzie wyglądać podobnie do poniższego przykładu.

```xml
<dataCacheClients>
  <dataCacheClientname="default">
    <!--To use the in-role flavor of Azure Cache, set identifier to be the cache cluster role name -->
    <!--To use the Azure Managed Cache Service, set identifier to be the endpoint of the cache cluster -->
    <autoDiscoverisEnabled="true"identifier="[Cache role name or Service Endpoint]"/>

    <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
    <!--Use this section to specify security settings for connecting to your cache. This section is not required if your cache is hosted on a role that is a part of your cloud service. -->
    <!--<securityProperties mode="Message" sslEnabled="true">
      <messageSecurity authorizationInfo="[Authentication Key]" />
    </securityProperties>-->
  </dataCacheClient>
</dataCacheClients>
```

Po usunięciu konfiguracji usługi zarządzana pamięć podręczna klienta pamięci podręcznej można skonfigurować zgodnie z opisem w poniższej sekcji.

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>Konfigurowanie klienta pamięci podręcznej przy użyciu pakietu NuGet w programie StackExchange.Redis
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Migrowanie kodu usługi zarządzana pamięć podręczna
Interfejs API w programie StackExchange.Redis pamięci podręcznej klienta jest podobne do usługi zarządzana pamięć podręczna. Ta sekcja zawiera omówienie różnic.

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>Łączenie z pamięcią podręczną, za pomocą klasy ConnectionMultiplexer
W przypadku usługi zarządzana pamięć podręczna połączeń z pamięci podręcznej zostały obsłużone przez `DataCacheFactory` i `DataCache` klasy. W pamięci podręcznej Redis Azure, te połączenia są zarządzane przez `ConnectionMultiplexer` klasy.

Dodaj następujący kod za pomocą instrukcji u góry każdego pliku, z którego chcesz uzyskać dostępu do pamięci podręcznej.

```csharp
using StackExchange.Redis
```

Jeśli nie rozwiąże ten obszar nazw, należy się upewnić, że dodano pakiet NuGet w programie StackExchange.Redis zgodnie z opisem w [Szybki Start: użycie pamięci podręcznej Redis Azure przy użyciu aplikacji .NET](cache-dotnet-how-to-use-azure-redis-cache.md).

> [!NOTE]
> Należy pamiętać, że klient programie StackExchange.Redis wymaga programu .NET Framework 4 lub nowszej.
> 
> 

Aby połączyć się z wystąpieniem usługi pamięć podręczna Redis Azure, należy wywołać statycznych `ConnectionMultiplexer.Connect` — metoda i przekaż punktu końcowego i klucz. Jednym z rozwiązań w zakresie udostępniania wystąpienia klasy `ConnectionMultiplexer` w aplikacji jest korzystanie z właściwości statycznej, która zwraca połączone wystąpienie podobnie jak w poniższym przykładzie. Takie podejście umożliwia bezpieczne wątkowo zainicjować pojedynczego połączenia `ConnectionMultiplexer` wystąpienia. W tym przykładzie `abortConnect` ma wartość false, co oznacza, że wywołanie powiedzie się, nawet jeśli nie zostanie nawiązane połączenie z pamięci podręcznej. Kluczowa funkcja klasy `ConnectionMultiplexer` polega na automatycznym przywracaniu łączności z pamięcią podręczną po rozwiązaniu problemu z siecią lub usunięciu innych przyczyn.

```csharp
private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
{
    return ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
});

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

Punkt końcowy, klucze i porty, można je uzyskać z **pamięci podręcznej Redis** bloku dla swojego wystąpienia w pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [właściwości pamięci podręcznej Redis](cache-configure.md#properties).

Po nawiązaniu połączenia zwraca odwołanie do bazy danych z pamięci podręcznej Redis, wywołując `ConnectionMultiplexer.GetDatabase` metody. Obiekt zwracany z metody `GetDatabase` jest lekkim obiektem przekazującym i nie wymaga przechowywania.

```csharp
IDatabase cache = Connection.GetDatabase();

// Perform cache operations using the cache object...
// Simple put of integral data types into the cache
cache.StringSet("key1", "value");
cache.StringSet("key2", 25);

// Simple get of data types from the cache
string key1 = cache.StringGet("key1");
int key2 = (int)cache.StringGet("key2");
```

Klient programie StackExchange.Redis używa `RedisKey` i `RedisValue` typy do uzyskiwania dostępu i przechowywanie elementów w pamięci podręcznej. Te typy mapowania na najbardziej pierwotne typy język, w tym ciągu, a często nie są używane bezpośrednio. Redis ciągów są najbardziej podstawowa rodzaj wartości Redis i może zawierać wiele typów danych, w tym serializacji strumienie binarne, i gdy nie mogą bezpośrednio używać tego typu, będzie używać metod, które zawierają `String` w nazwie. W przypadku najbardziej pierwotnych typów danych, przechowywania i pobierania elementów z pamięci podręcznej przy użyciu `StringSet` i `StringGet` metody, chyba że kolekcje lub inne typy danych Redis są przechowywane w pamięci podręcznej. 

`StringSet` i `StringGet` są podobne do usługi zarządzana pamięć podręczna `Put` i `Get` metody z jednym z głównych różnica że przed ustawić i pobierania obiektu platformy .NET do pamięci podręcznej można musi serializować ją najpierw. 

Podczas wywoływania metody `StringGet`, jeśli obiekt nie istnieje, jest zwracany, a jeśli nie, zwracana jest wartość null. W takim przypadku można pobrać wartości ze źródła żądanych danych i zapisz go w pamięci podręcznej do późniejszego wykorzystania. Ten wzorzec jest określany jako wzorzec Zarezerwuj pamięci podręcznej.

Aby określić wygaśnięcie elementu w pamięci podręcznej, użyj parametru `TimeSpan` metody `StringSet`.

```csharp
cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));
```

Pamięć podręczna Redis Azure może współpracować z obiektów platformy .NET, a także typy pierwotne danych, ale przed obiektu .NET mogą być buforowane musi być serializowany. Ta serializacji jest odpowiedzialny za Deweloper aplikacji i zapewnia elastyczność deweloperów w wyborze serializatora. Aby uzyskać więcej informacji i przykładowy kod, zobacz [Praca z obiektami .NET w pamięci podręcznej](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-redis-cache"></a>Migracja stanu sesji ASP.NET i buforowanie danych wyjściowych w pamięci podręcznej Redis Azure
Pamięć podręczna Redis Azure ma dostawców dla stanu sesji ASP.NET i buforowania danych wyjściowych strony. Aby dokonać migracji aplikacji, która używa wersji usługi zarządzana pamięć podręczna tych dostawców, najpierw usuń istniejące sekcje z pliku web.config, a następnie skonfiguruj wersje pamięć podręczna Redis Azure dostawców. Instrukcje dotyczące używania dostawców ASP.NET pamięci podręcznej Redis Azure, zobacz [dostawcę stanu sesji ASP.NET dla pamięci podręcznej Redis Azure](cache-aspnet-session-state-provider.md) i [dostawcy wyjściowej pamięci podręcznej programu ASP.NET dla pamięci podręcznej Redis Azure](cache-aspnet-output-cache-provider.md).

## <a name="next-steps"></a>Kolejne kroki
Eksploruj [dokumentacji pamięć podręczna Redis Azure](https://azure.microsoft.com/documentation/services/cache/) samouczki, przykłady, klipów wideo i.

