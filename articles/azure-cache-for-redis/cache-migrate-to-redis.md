---
title: Migrowanie aplikacji usługi zarządzanej pamięci podręcznej do usługi Redis — Azure
description: Dowiedz się, jak migrować aplikacje usługi zarządzanej pamięci podręcznej i pamięci podręcznej w roli do pamięci podręcznej platformy Azure dla programu Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 05/30/2017
ms.author: yegu
ms.openlocfilehash: 9596b8cb771f114cb09c5d6c6ae33b4fc4a8cada
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74122683"
---
# <a name="migrate-from-managed-cache-service-to-azure-cache-for-redis"></a>Migrowanie z usługi Managed Cache Service do usługi Azure Cache for Redis
Migracja aplikacji korzystających z usługi Azure Managed Cache Service do usługi Azure Cache for Redis może zostać dokonana przy minimalnych zmianach w aplikacji, w zależności od funkcji usługi zarządzanej pamięci podręcznej używanych przez aplikację buforowania. Podczas gdy interfejsy API nie są dokładnie takie same, są podobne, a wiele z istniejącego kodu, który używa usługi zarządzanej pamięci podręcznej, aby uzyskać dostęp do pamięci podręcznej, można ponownie użyć przy minimalnych zmianach. W tym artykule pokazano, jak wprowadzić niezbędne zmiany konfiguracji i aplikacji w celu migracji aplikacji usługi zarządzanej pamięci podręcznej w celu użycia usługi Azure Cache for Redis, a także pokazano, jak niektóre funkcje usługi Azure Cache for Redis mogą być używane do implementacji funkcji usługi pamięci podręcznej usługi zarządzanej pamięci podręcznej.

>[!NOTE]
>Usługa zarządzanej pamięci podręcznej i pamięć podręczna w roli zostały [wycofane](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/) 30 listopada 2016 r. Jeśli masz żadnych wdrożeń w pamięci podręcznej roli, które chcesz przeprowadzić migrację do usługi Azure Cache for Redis, możesz wykonać kroki opisane w tym artykule.

## <a name="migration-steps"></a>Etapy migracji
Poniższe kroki są wymagane do migracji aplikacji usługi zarządzanej pamięci podręcznej do korzystania z usługi Azure Cache dla redis.

* Funkcje usługi zarządzanej pamięci podręcznej map do pamięci podręcznej platformy Azure dla programu Redis
* Wybieranie oferty pamięci podręcznej
* Tworzenie pamięci podręcznej
* Konfigurowanie klientów pamięci podręcznej
  * Usuwanie konfiguracji usługi zarządzanej pamięci podręcznej
  * Konfigurowanie klienta pamięci podręcznej przy użyciu pakietu StackExchange.Redis NuGet
* Migrowanie kodu usługi zarządzanej pamięci podręcznej
  * Łączenie się z pamięcią podręczną przy użyciu connectionmultiplexer klasy
  * Dostęp do pierwotnych typów danych w pamięci podręcznej
  * Praca z obiektami platformy .NET w pamięci podręcznej
* Migrowanie ASP.NET stanu sesji i buforowania danych wyjściowych do pamięci podręcznej platformy Azure dla usługi Redis 

## <a name="map-managed-cache-service-features-to-azure-cache-for-redis"></a>Funkcje usługi zarządzanej pamięci podręcznej map do pamięci podręcznej platformy Azure dla programu Redis
Usługa Azure Managed Cache service i usługa Azure Cache for Redis są podobne, ale implementują niektóre z ich funkcji na różne sposoby. W tej sekcji opisano niektóre różnice i zawiera wskazówki dotyczące implementowania funkcji usługi zarządzanej pamięci podręcznej w pamięci podręcznej platformy Azure dla programu Redis.

| Funkcja usługi zarządzanej pamięci podręcznej | Obsługa usługi zarządzanej pamięci podręcznej | Obsługa usługi Azure Cache for Redis |
| --- | --- | --- |
| Nazwane pamięci podręczne |Domyślna pamięć podręczna jest skonfigurowana, a w ofercie pamięci podręcznej Standardowa i Premium można skonfigurować maksymalnie dziewięć dodatkowych nazwanych pamięci podręcznych. |Usługa Azure Cache for Redis ma konfigurowalną liczbę baz danych (domyślnie 16), które mogą być używane do implementowania funkcji podobnej do nazwanych pamięci podręcznych. Aby uzyskać więcej informacji, zobacz [What are Redis databases?](cache-faq.md#what-are-redis-databases) (Co to są bazy danych Redis?) i [Default Redis server configuration](cache-configure.md#default-redis-server-configuration) (Domyślna konfiguracja serwera Redis). |
| Wysoka dostępność |Zapewnia wysoką dostępność elementów w pamięci podręcznej w standardowej i premium oferty pamięci podręcznej. Jeśli elementy zostaną utracone z powodu awarii, kopie zapasowe elementów w pamięci podręcznej są nadal dostępne. Zapisy w pomocniczej pamięci podręcznej są dokonywane synchronicznie. |Wysoka dostępność jest dostępna w standardowej i premium pamięci podręcznej oferty, które mają dwa węzły konfiguracji podstawowych/replik (każdy fragment w pamięci podręcznej premium ma pary podstawowej/repliki). Zapisy do repliki są asynchroniczne. Aby uzyskać więcej informacji, zobacz [Azure Cache for Redis cennika](https://azure.microsoft.com/pricing/details/cache/). |
| Powiadomienia |Umożliwia klientom odbieranie powiadomień asynchronicznych, gdy różne operacje pamięci podręcznej występują w nazwanej pamięci podręcznej. |Aplikacje klienckie mogą używać powiadomień Redis pub/sub lub [Keyspace](cache-configure.md#keyspace-notifications-advanced-settings) w celu uzyskania podobnej funkcjonalności do powiadomień. |
| Lokalna pamięć podręczna |Przechowuje kopię obiektów buforowanych lokalnie na kliencie w celu uzyskania bardzo szybkiego dostępu. |Aplikacje klienckie należy zaimplementować tę funkcję przy użyciu słownika lub podobnej struktury danych. |
| Zasady eksmisji |Brak lub LRU. Domyślną zasadą jest LRU. |Usługa Azure Cache for Redis obsługuje następujące zasady eksmisji: volatile-lru, allkeys-lru, volatile-random, allkeys-random, volatile-ttl, noeviction. Domyślna zasada to volatile-lru. Aby uzyskać więcej informacji, zobacz [Domyślna konfiguracja serwera Redis](cache-configure.md#default-redis-server-configuration). |
| Zasady wygasania |Domyślna zasada wygasania to Absolute, a domyślny interwał wygaśnięcia wynosi 10 minut. Dostępne są również zasady przesuwane i nigdy. |Domyślnie elementy w pamięci podręcznej nie wygasają, ale wygaśnięcia można skonfigurować na podstawie zapisu przy użyciu przeciążenia zestawu pamięci podręcznej. |
| Regiony i tagowanie |Regiony są podgrupami dla elementów buforowanych. Regiony obsługują również adnotację elementów buforowanych z dodatkowymi ciągami opisowymi zwanymi tagami. Regiony obsługują możliwość wykonywania operacji wyszukiwania na wszystkich oznakowanych elementów w tym regionie. Wszystkie elementy w regionie znajdują się w jednym węźle klastra pamięci podręcznej. |Usługa Azure Cache for Redis składa się z jednego węzła (chyba że włączony jest klaster Redis), więc koncepcja regionów usługi zarządzanej pamięci podręcznej nie ma zastosowania. Redis obsługuje operacje wyszukiwania i symboli wieloznacznych podczas pobierania kluczy, dzięki czemu znaczniki opisowe mogą być osadzone w nazwach kluczy i używane do pobierania elementów później. Na przykład zaimplementowania rozwiązania do znakowania przy użyciu funkcji Redis zobacz [Implementowanie tagowania pamięci podręcznej za pomocą funkcji Redis](https://stackify.com/implementing-cache-tagging-redis/). |
| Serializacja |Zarządzana pamięć podręczna obsługuje netdataContractSerializer, BinaryFormatter i użycie niestandardowych serializatorów. Wartość domyślna to NetDataContractSerializer. |Jest odpowiedzialny za aplikację kliencką do serializacji obiektów .NET przed umieszczeniem ich w pamięci podręcznej, z wyborem serializatora do dewelopera aplikacji klienckiej. Aby uzyskać więcej informacji i przykładowy kod, zobacz [Praca z obiektami .NET w pamięci podręcznej](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache). |
| Emulator pamięci podręcznej |Zarządzana pamięć podręczna udostępnia emulator lokalnej pamięci podręcznej. |Usługa Azure Cache for Redis nie ma emulatora, ale można [uruchomić kompilację MSOpenTech redis-server.exe lokalnie,](cache-faq.md#cache-emulator) aby zapewnić środowisko emulatora. |

## <a name="choose-a-cache-offering"></a>Wybieranie oferty pamięci podręcznej
Pamięć podręczna Microsoft Azure cache for Redis jest dostępna w następujących warstwach:

* **Podstawowa** — jeden węzeł. Wiele rozmiarów do 53 GB.
* **Standardowa** — dwa węzły (węzeł podstawowy i węzeł repliki). Wiele rozmiarów do 53 GB. Umowa SLA na poziomie 99,9%.
* **Premium** — dwa węzły (węzeł podstawowy i węzeł repliki) zawierające do 10 fragmentów. Wiele rozmiarów od 6 GB do 1,2 TB. Wszystkie funkcje warstwy Standardowej i dodatkowe funkcje, m.in. obsługa [klastra Redis](cache-how-to-premium-clustering.md), [stanu trwałego pamięci podręcznej Redis](cache-how-to-premium-persistence.md) oraz usługi [Azure Virtual Network](cache-how-to-premium-vnet.md). Umowa SLA na poziomie 99,9%.

Poszczególne warstwy różnią się od siebie pod względem funkcji i cen. Funkcje zostały omówione w dalszej części tego przewodnika, a więcej informacji na temat cen można znaleźć w szczegółów [cennika pamięci podręcznej](https://azure.microsoft.com/pricing/details/cache/).

Punktem wyjścia dla migracji jest wybranie rozmiaru odpowiadającego rozmiarowi poprzedniej pamięci podręcznej usługi zarządzanej pamięci podręcznej, a następnie skalowanie w górę lub w dół w zależności od wymagań aplikacji. Aby uzyskać więcej informacji na temat wybierania odpowiedniej oferty usługi Azure Cache for Redis, [zobacz, jakiej oferty i rozmiaru usługi Azure Cache for Redis należy użyć.](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

## <a name="create-a-cache"></a>Tworzenie pamięci podręcznej
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>Konfigurowanie klientów pamięci podręcznej
Po utworzeniu i skonfigurowaniu pamięci podręcznej następnym krokiem jest usunięcie konfiguracji usługi zarządzanej pamięci podręcznej i dodanie konfiguracji i odwołań do pamięci podręcznej Azure Cache dla redis, aby klienci pamięci podręcznej mogli uzyskać dostęp do pamięci podręcznej.

* Usuwanie konfiguracji usługi zarządzanej pamięci podręcznej
* Konfigurowanie klienta pamięci podręcznej przy użyciu pakietu StackExchange.Redis NuGet

### <a name="remove-the-managed-cache-service-configuration"></a>Usuwanie konfiguracji usługi zarządzanej pamięci podręcznej
Zanim aplikacje klienckie mogą być skonfigurowane dla usługi Azure Cache dla redis, istniejące konfiguracja zarządzanej usługi pamięci podręcznej i odwołania do zestawu muszą zostać usunięte przez odinstalowanie pakietu NuGet usługi zarządzanej pamięci podręcznej.

Aby odinstalować pakiet NuGet usługi zarządzanej pamięci podręcznej, kliknij prawym przyciskiem myszy projekt klienta w **Eksploratorze rozwiązań** i wybierz polecenie **Zarządzaj pakietami NuGet**. Wybierz węzeł **Zainstalowane pakiety** i wpisz**w polu Wyszukaj** zainstalowane pakiety. Wybierz pozycję Pamięć **podręczna** **systemu Windows** Azure (lub **buforowanie systemu Windows** **Azure** w zależności od wersji pakietu NuGet), kliknij przycisk **Odinstaluj**, a następnie kliknij przycisk **Zamknij**.

![Odinstalowywanie pakietu NuGet usługi zarządzanej pamięci podręcznej platformy Azure](./media/cache-migrate-to-redis/IC757666.jpg)

Odinstalowanie pakietu NuGet usługi zarządzanej pamięci podręcznej powoduje usunięcie zestawów usługi zarządzanej pamięci podręcznej i wpisów usługi zarządzanej pamięci podręcznej w pliku app.config lub web.config aplikacji klienckiej. Ponieważ niektóre dostosowane ustawienia mogą nie zostać usunięte podczas odinstalowywania pakietu NuGet, otwórz web.config lub app.config i upewnij się, że następujące elementy są usuwane.

Upewnij się, że `dataCacheClients` wpis `configSections` jest usuwany z elementu. Nie usuwaj `configSections` całego elementu; po prostu `dataCacheClients` usuń wpis, jeśli jest obecny.

```xml
<configSections>
  <!-- Existing sections omitted for clarity. -->
  <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
</configSections>
```

Upewnij się, że `dataCacheClients` sekcja została usunięta. Sekcja `dataCacheClients` będzie podobna do poniższego przykładu.

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

Po usunięciu konfiguracji usługi zarządzanej pamięci podręcznej można skonfigurować klienta pamięci podręcznej zgodnie z opisem w poniższej sekcji.

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>Konfigurowanie klienta pamięci podręcznej przy użyciu pakietu StackExchange.Redis NuGet
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Migrowanie kodu usługi zarządzanej pamięci podręcznej
Interfejs API dla klienta StackExchange.Azure Cache for Redis jest podobny do usługi zarządzanej pamięci podręcznej. Ta sekcja zawiera omówienie różnic.

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>Łączenie się z pamięcią podręczną przy użyciu connectionmultiplexer klasy
W managed cache service połączenia z pamięcią `DataCacheFactory` podręczną były obsługiwane przez i `DataCache` klas. W pamięci podręcznej Azure dla redis `ConnectionMultiplexer` te połączenia są zarządzane przez klasę.

Dodaj następującą instrukcję using do górnej części dowolnego pliku, z którego chcesz uzyskać dostęp do pamięci podręcznej.

```csharp
using StackExchange.Redis
```

Jeśli ten obszar nazw nie rozwiąże problemu, upewnij się, że dodano pakiet StackExchange.Redis NuGet zgodnie z opisem w [przewodniku Szybki start: Użyj pamięci podręcznej Azure dla programu Redis z aplikacją .NET.](cache-dotnet-how-to-use-azure-redis-cache.md)

> [!NOTE]
> Należy zauważyć, że StackExchange.Redis klient wymaga .NET Framework 4 lub nowszej.
> 
> 

Aby połączyć się z pamięcią podręczną `ConnectionMultiplexer.Connect` platformy Azure dla wystąpienia Redis, wywołaj metodę statyczną i przekaż w punkcie końcowym i kluczu. Jednym z rozwiązań w zakresie udostępniania wystąpienia klasy `ConnectionMultiplexer` w aplikacji jest korzystanie z właściwości statycznej, która zwraca połączone wystąpienie podobnie jak w poniższym przykładzie. Takie podejście zapewnia sposób bezpieczne dla wątków, aby zainicjować pojedyncze wystąpienie połączone. `ConnectionMultiplexer` W tym `abortConnect` przykładzie jest ustawiona na false, co oznacza, że wywołanie zakończy się pomyślnie, nawet jeśli połączenie z pamięcią podręczną nie jest ustanawiane. Kluczowa funkcja klasy `ConnectionMultiplexer` polega na automatycznym przywracaniu łączności z pamięcią podręczną po rozwiązaniu problemu z siecią lub usunięciu innych przyczyn.

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

Punktu końcowego pamięci podręcznej, klucze i porty można uzyskać z **usługi Azure Cache dla redis** bloku dla wystąpienia pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [Azure Cache for Redis właściwości](cache-configure.md#properties).

Po nawiązaniu połączenia, zwraca odwołanie do usługi Azure Cache `ConnectionMultiplexer.GetDatabase` dla bazy danych Redis, wywołując metodę. Obiekt zwracany z metody `GetDatabase` jest lekkim obiektem przekazującym i nie wymaga przechowywania.

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

StackExchange.Redis klient używa `RedisKey` i `RedisValue` typów do uzyskiwania dostępu i przechowywania elementów w pamięci podręcznej. Te typy mapują na większość typów języków pierwotnych, w tym ciąg, i często nie są używane bezpośrednio. Ciągi Redis są najbardziej podstawowym rodzajem redis wartości i może zawierać wiele typów danych, w tym serializowane strumienie binarne, a gdy nie można używać typu bezpośrednio, użyjesz metod, które zawierają `String` w nazwie. Dla większości typów danych pierwotnych, można przechowywać i `StringSet` pobierać `StringGet` elementy z pamięci podręcznej przy użyciu i metody, chyba że są przechowywane kolekcje lub inne typy danych Redis w pamięci podręcznej. 

`StringSet`i `StringGet` są podobne do `Put` usługi `Get` zarządzanej pamięci podręcznej i metod, z jedną główną różnicą jest to, że przed ustawieniem i uzyskać obiekt .NET w pamięci podręcznej należy serializować go najpierw. 

Podczas `StringGet`wywoływania , jeśli obiekt istnieje, jest zwracany, a jeśli nie, null jest zwracany. W takim przypadku można pobrać wartość z żądanego źródła danych i przechowywać ją w pamięci podręcznej do późniejszego użycia. Ten wzorzec jest znany jako wzorzec na bok pamięci podręcznej.

Aby określić wygaśnięcie elementu w pamięci podręcznej, użyj parametru `TimeSpan` metody `StringSet`.

```csharp
cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));
```

Usługa Azure Cache for Redis może pracować z obiektami .NET, a także z typami danych pierwotnych, ale zanim obiekt .NET będzie można buforować, musi być szeregowany. Ta serializacja jest odpowiedzialny za dewelopera aplikacji i daje deweloperowi elastyczność w wyborze serializatora. Aby uzyskać więcej informacji i przykładowy kod, zobacz [Praca z obiektami .NET w pamięci podręcznej](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-cache-for-redis"></a>Migrowanie ASP.NET stanu sesji i buforowania danych wyjściowych do pamięci podręcznej platformy Azure dla usługi Redis
Usługa Azure Cache for Redis ma dostawców zarówno ASP.NET stanu sesji, jak i buforowania danych wyjściowych strony. Aby przeprowadzić migrację aplikacji korzystającej z wersji usługi zarządzanej pamięci podręcznej tych dostawców, najpierw usuń istniejące sekcje z witryny web.config, a następnie skonfiguruj pamięć podręczną Azure dla wersji redis dostawców. Aby uzyskać instrukcje dotyczące korzystania z usługi Azure Cache dla dostawców ASP.NET redis, zobacz [ASP.NET dostawcy stanu sesji dla pamięci podręcznej azure dla redis](cache-aspnet-session-state-provider.md) i dostawcy pamięci [podręcznej ASP.NET danych wyjściowych dla pamięci podręcznej azure dla programu Redis](cache-aspnet-output-cache-provider.md).

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z [dokumentacją usługi Azure Cache for Redis,](https://azure.microsoft.com/documentation/services/cache/) aby uzyskać samouczki, przykłady, klipy wideo i inne.

