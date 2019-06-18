---
title: Migrowanie aplikacji Managed Cache Service do usługi Redis — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przeprowadzić migrację usługi Managed Cache Service i pamięć podręczna oparta na roli aplikacji do usługi Azure Cache dla pamięci podręcznej Redis
services: cache
documentationcenter: na
author: yegu-ms
manager: jhubbard
editor: tysonn
ms.assetid: 041f077b-8c8e-4d7c-a3fc-89d334ed70d6
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 05/30/2017
ms.author: yegu
ms.openlocfilehash: 116e54fd39af801cf8941a974da2b72c483097dc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60830299"
---
# <a name="migrate-from-managed-cache-service-to-azure-cache-for-redis"></a>Migrowanie z usługi Managed Cache Service do usługi Azure Cache dla usługi Redis
Migrowanie aplikacji korzystających z usługi Azure Managed Cache Service do usługi Azure Cache dla usługi Redis można osiągnąć przy minimalnych zmianach do aplikacji, w zależności od funkcji Managed Cache Service, używanych przez aplikację pamięci podręcznej. Gdy interfejsy API są dokładnie takie same, są one podobne, a większość swojej istniejący kod, który używa usługi Managed Cache Service do dostępu do pamięci podręcznej mogą być ponownie używane przy minimalnych zmianach. W tym artykule przedstawiono sposób wprowadzania niezbędną konfigurację i aplikacji zmieni się na migrację aplikacji Managed Cache Service na potrzeby pamięci podręcznej Azure redis Cache i pokazuje, jak niektóre funkcje pamięci podręcznej Redis Azure może służyć do implementowania funkcji pamięć podręczna usługi Managed Cache Service.

>[!NOTE]
>Managed Cache Service i pamięć podręczna oparta na roli zostały [wycofane](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/) 30 listopada 2016 r. W przypadku wszystkich wdrożeń pamięć podręczna oparta na roli, które chcesz przeprowadzić migrację do usługi Azure Cache dla usługi Redis może postępuj zgodnie z instrukcjami w tym artykule.

## <a name="migration-steps"></a>Kroki migracji
Poniższe kroki są wymagane do migracji aplikacji Managed Cache Service, na potrzeby pamięci podręcznej Azure redis Cache.

* Mapowanie funkcji Managed Cache Service do usługi Azure Cache dla pamięci podręcznej Redis
* Wybierz oferty pamięci podręcznej
* Tworzenie pamięci podręcznej
* Konfigurowanie klientów pamięci podręcznej
  * Usuwanie konfiguracji usługi zarządzana pamięć podręczna
  * Konfigurowanie klienta pamięci podręcznej przy użyciu pakietu NuGet StackExchange.Redis
* Migrowanie kodu usługi Managed Cache Service
  * Łączenie z pamięcią podręczną przy użyciu klasy ConnectionMultiplexer
  * Dostęp pierwotne typy danych w pamięci podręcznej
  * Praca z obiektami platformy .NET w pamięci podręcznej
* Migracja stanu sesji platformy ASP.NET, a dane wyjściowe pamięci podręcznej do usługi Azure Cache dla usługi Redis 

## <a name="map-managed-cache-service-features-to-azure-cache-for-redis"></a>Mapowanie funkcji Managed Cache Service do usługi Azure Cache dla pamięci podręcznej Redis
Usługa Azure Managed Cache Service i Azure pamięci podręcznej Redis są podobne, ale implementowane niektóre swoje funkcje na różne sposoby. W tej sekcji opisano niektóre różnice i zawiera wskazówki dotyczące implementowania funkcji Managed Cache Service w pamięci podręcznej Azure dla usługi Redis.

| Zarządzane funkcji usługi Cache Service | Zarządzanej pamięci podręcznej usługi pomocy technicznej. | Pamięć podręczna systemu Azure do obsługi pamięci podręcznej Redis |
| --- | --- | --- |
| Nazwanych pamięci podręcznych |Domyślny pamięci podręcznej jest skonfigurowany, a w pamięci podręcznej Standard i Premium można skonfigurować ofert maksymalnie dziewięć dodatkowych nazwane pamięci podręczne, w razie potrzeby. |Pamięć podręczna systemu Azure dla usługi Redis zawiera konfigurowalną liczbę baz danych (domyślnie 16), które mogą służyć do implementowania podobne funkcje do nazwanych pamięci podręcznych. Aby uzyskać więcej informacji, zobacz [What are Redis databases?](cache-faq.md#what-are-redis-databases) (Co to są bazy danych Redis?) i [Default Redis server configuration](cache-configure.md#default-redis-server-configuration) (Domyślna konfiguracja serwera Redis). |
| Wysoka dostępność |Zapewnia wysoką dostępność dla elementów w pamięci podręcznej w pamięci podręcznej oferty Standard i Premium. Jeśli elementy zostaną utracone z powodu błędu, kopie zapasowe elementów w pamięci podręcznej są nadal dostępne. Operacje zapisu do pomocniczej pamięci podręcznej są wykonywane synchronicznie. |Wysoka dostępność jest dostępna w warstwach standardowa i Premium pamięci podręcznej ofert, które mają konfiguracji podstawowy i węzeł repliki dwoma węzłami (każdego fragmentu w pamięci podręcznej w warstwie Premium ma parę podstawowy i węzeł repliki). Operacje zapisu do repliki są wykonywane asynchronicznie. Aby uzyskać więcej informacji, zobacz [pamięci podręcznej Azure Redis cennik](https://azure.microsoft.com/pricing/details/cache/). |
| Powiadomienia |Zezwala klientom na odbieranie powiadomień asynchronicznych, gdy różne operacje pamięci podręcznej występują na nazwaną pamięć podręczną. |Aplikacje klienckie mogą używać pamięci podręcznej Redis pub/sub lub [powiadomienia przestrzeni kluczy](cache-configure.md#keyspace-notifications-advanced-settings) do osiągnięcia podobne funkcje powiadomień. |
| Lokalna pamięć podręczna |Przechowuje kopię obiektów w pamięci podręcznej lokalnie na kliencie bardzo szybkiego dostępu. |Aplikacje klienckie należy zaimplementować tę funkcję za pomocą słownika lub podobną strukturę danych. |
| Zasady eksmisji |Brak lub LRU. Domyślne zasady to LRU. |Pamięć podręczna systemu Azure dla usługi Redis obsługuje następujące zasady eksmisji: volatile lru, allkeys lru, volatile losowych, allkeys losowe ttl volatile noeviction. Domyślne zasady to volatile lru. Aby uzyskać więcej informacji, zobacz [Redis domyślna konfiguracja serwera](cache-configure.md#default-redis-server-configuration). |
| Zasady wygasania |Domyślne zasady wygasania jest ścieżką bezwzględną, a domyślny interwał wygaśnięcia wynosi 10 minut. Dostępne są także zasady przedłużanie oraz nigdy. |Domyślnie elementów w pamięci podręcznej nie wygasają, ale wygaśnięcia można skonfigurować dla poszczególnych zapisu przy użyciu pamięci podręcznej zestawu przeciążenia. |
| Regiony i znakowanie |Regiony są podgrupy dla elementów pamięci podręcznej. Regiony obsługują również adnotacja elementów pamięci podręcznej za pomocą dodatkowych opisowy ciągów o nazwie tagów. Regiony obsługują możliwość wykonywania operacji wyszukiwania na wszystkie elementy oznakowanych w danym regionie. Wszystkie elementy w danym regionie znajdują się w jednym węźle klastra pamięci podręcznej. |pamięci podręcznej Redis Azure składa się z jednego węzła (chyba że jest włączona opcja klastra pamięci podręcznej Redis) dzięki koncepcji regionów usługi Managed Cache Service nie ma zastosowania. Usługa redis obsługuje wyszukiwanie i operacje dotyczące symboli wieloznacznych, podczas pobierania kluczy, dzięki czemu opisowe znaczniki może osadzone w nazw kluczy i służy do pobierania elementów później. Przykład wdrożenia znakowania rozwiązania przy użyciu pamięci podręcznej Redis, zobacz [Implementowanie znakowanie z pamięcią podręczną Redis cache](https://stackify.com/implementing-cache-tagging-redis/). |
| Serializacja |Zarządzana pamięć podręczna obsługuje NetDataContractSerializer elementu i użycie niestandardowych serializatory. Wartość domyślna to NetDataContractSerializer. |Jest odpowiedzialny za aplikacja kliencka do wykonywania serializacji obiektów .NET przed umieszczeniem ich w pamięci podręcznej, za pomocą możliwość wybrania serializatora klasą deweloperów aplikacji klienta. Aby uzyskać więcej informacji i przykładowy kod, zobacz [Praca z obiektami platformy .NET w pamięci podręcznej](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache). |
| Emulator pamięci podręcznej |Zarządzana pamięć podręczna zawiera emulator lokalnej pamięci podręcznej. |Pamięć podręczna systemu Azure dla usługi Redis nie ma w emulatorze, ale możesz [lokalnie uruchomić kompilację MSOpenTech redis server.exe](cache-faq.md#cache-emulator) celu udostępnienia środowiska emulatora. |

## <a name="choose-a-cache-offering"></a>Wybierz oferty pamięci podręcznej
Microsoft Azure pamięci podręcznej redis Cache jest dostępna w następujących warstwach:

* **Podstawowa** — jeden węzeł. Wiele rozmiarów do 53 GB.
* **Standardowa** — dwa węzły (węzeł podstawowy i węzeł repliki). Wiele rozmiarów do 53 GB. Umowa SLA na poziomie 99,9%.
* **Premium** — dwa węzły (węzeł podstawowy i węzeł repliki) zawierające do 10 fragmentów. Wiele rozmiarów od 6 GB do 530 GB. Wszystkie funkcje warstwy Standardowej i dodatkowe funkcje, m.in. obsługa [klastra Redis](cache-how-to-premium-clustering.md), [stanu trwałego pamięci podręcznej Redis](cache-how-to-premium-persistence.md) oraz usługi [Azure Virtual Network](cache-how-to-premium-vnet.md). Umowa SLA na poziomie 99,9%.

Poszczególne warstwy różnią się od siebie pod względem funkcji i cen. Funkcje zostały omówione w dalszej części tego przewodnika, a aby uzyskać więcej informacji na temat cen, zobacz [redis Cache — cennik](https://azure.microsoft.com/pricing/details/cache/).

Punkt początkowy dla migracji jest wybierz rozmiar który odpowiada rozmiarowi poprzedniego pamięci podręcznej usługi Managed Cache Service, a następnie skalować w górę lub w dół w zależności od wymagań aplikacji. Aby uzyskać więcej informacji o wybieraniu oferty pamięci podręcznej Redis Azure odpowiednie w pamięci podręcznej, zobacz [jakich pamięć podręczna systemu Azure, oferty pamięci podręcznej Redis i rozmiaru należy używać](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).

## <a name="create-a-cache"></a>Tworzenie pamięci podręcznej
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>Konfigurowanie klientów pamięci podręcznej
Po utworzeniu i skonfigurowaniu pamięci podręcznej następnym krokiem jest usunąć konfigurację usługi Managed Cache Service, a następnie dodanie usługi Azure Cache konfiguracji pamięci podręcznej Redis i odwołuje się tak, aby klienci pamięci podręcznej mogą uzyskiwać dostęp do pamięci podręcznej.

* Usuwanie konfiguracji usługi zarządzana pamięć podręczna
* Konfigurowanie klienta pamięci podręcznej przy użyciu pakietu NuGet StackExchange.Redis

### <a name="remove-the-managed-cache-service-configuration"></a>Usuwanie konfiguracji usługi zarządzana pamięć podręczna
Przed skonfigurowaniem aplikacji klienckich dla pamięci podręcznej Azure Redis, istniejącej usługi Managed Cache Service przez odinstalowanie pakietu NuGet usługi zarządzana pamięć podręczna Service należy usunąć odwołania do konfiguracji i zestawu.

Aby odinstalować pakietu NuGet usługi zarządzana pamięć podręczna Service, kliknij prawym przyciskiem myszy projekt klienta w **Eksploratora rozwiązań** i wybierz polecenie **Zarządzaj pakietami NuGet**. Wybierz **zainstalowanych pakietów** węzłem, a typ W**indowsAzure.Caching** w polu wyszukiwania zainstalowanych pakietów. Wybierz **Windows** **pamięć podręczna systemu Azure** (lub **Windows** **pamięć podręczna systemu Azure** w zależności od wersji pakietu NuGet), kliknij przycisk **Odinstaluj**, a następnie kliknij przycisk **Zamknij**.

![Odinstalowywanie pakietu NuGet usługi Azure Managed Cache Service](./media/cache-migrate-to-redis/IC757666.jpg)

Odinstalowywanie pakietu NuGet usługi zarządzana pamięć podręczna Service usuwa zestawy Managed Cache Service i Managed Cache Service wpisy w pliku app.config lub web.config aplikacji klienckiej. Ponieważ nie można usunąć niektórych ustawień niestandardowych, podczas odinstalowywania pakietu NuGet, otwórz plik web.config lub app.config i upewnij się, że następujące elementy są usuwane.

Upewnij się, że `dataCacheClients` wpis zostanie usunięty z `configSections` elementu. Nie należy usuwać całą `configSections` element; po prostu usuń `dataCacheClients` wpis, jeśli jest obecny.

```xml
<configSections>
  <!-- Existing sections omitted for clarity. -->
  <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
</configSections>
```

Upewnij się, że `dataCacheClients` sekcji zostanie usunięty. `dataCacheClients` Sekcji będzie podobny do poniższego przykładu.

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

Po konfiguracji usługi Managed Cache Service zostanie usunięty, można skonfigurować klienta pamięci podręcznej, zgodnie z opisem w poniższej sekcji.

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>Konfigurowanie klienta pamięci podręcznej przy użyciu pakietu NuGet StackExchange.Redis
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Migrowanie kodu usługi Managed Cache Service
Interfejs API klienta pamięci podręcznej Redis w pamięci podręcznej StackExchange.Azure jest podobna do usługi Managed Cache Service. Ta sekcja zawiera omówienie różnic.

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>Łączenie z pamięcią podręczną przy użyciu klasy ConnectionMultiplexer
W przypadku usługi Managed Cache Service, połączenia z pamięcią podręczną zostały obsłużone przez `DataCacheFactory` i `DataCache` klasy. W pamięci podręcznej Azure redis cache, te połączenia są zarządzane przez `ConnectionMultiplexer` klasy.

Dodaj następujące za pomocą instrukcji u góry każdego pliku, z którego mają dostęp do pamięci podręcznej.

```csharp
using StackExchange.Redis
```

Jeśli ta przestrzeń nazw nie jest rozpoznawane, pamiętaj, że dodano pakiet StackExchange.Redis NuGet zgodnie z opisem w [Szybki Start: Na użytek pamięć podręczna systemu Azure redis Cache z poziomu aplikacji .NET](cache-dotnet-how-to-use-azure-redis-cache.md).

> [!NOTE]
> Należy pamiętać, że klient StackExchange.Redis wymaga programu .NET Framework 4 lub nowszy.
> 
> 

Aby połączyć się do wystąpienia pamięci podręcznej Redis Azure Cache, wywołaj statyczną `ConnectionMultiplexer.Connect` metody i przekaż punkt końcowy i klucz. Jednym z rozwiązań w zakresie udostępniania wystąpienia klasy `ConnectionMultiplexer` w aplikacji jest korzystanie z właściwości statycznej, która zwraca połączone wystąpienie podobnie jak w poniższym przykładzie. To podejście oferuje bezpieczny wątkowo sposób na inicjowanie jednego połączonego `ConnectionMultiplexer` wystąpienia. W tym przykładzie `abortConnect` jest ustawiona na false, co oznacza, że wywołanie zakończy się powodzeniem, nawet jeśli nie nawiązano połączenia z pamięcią podręczną. Kluczowa funkcja klasy `ConnectionMultiplexer` polega na automatycznym przywracaniu łączności z pamięcią podręczną po rozwiązaniu problemu z siecią lub usunięciu innych przyczyn.

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

Punkt końcowy pamięci podręcznej, klucze i portów można uzyskać z **pamięci podręcznej Redis Azure** bloku wystąpienia pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [pamięci podręcznej Azure Redis właściwości](cache-configure.md#properties).

Po nawiązaniu połączenia Zwróć odwołanie do usługi Azure Cache dla bazy danych Redis przez wywołanie metody `ConnectionMultiplexer.GetDatabase` metody. Obiekt zwracany z metody `GetDatabase` jest lekkim obiektem przekazującym i nie wymaga przechowywania.

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

Klient StackExchange.Redis korzysta z `RedisKey` i `RedisValue` typów do uzyskiwania dostępu i przechowywanie elementów w pamięci podręcznej. Te typy mapowane na najbardziej pierwotnych typów języka, w tym ciągu, a często nie są używane bezpośrednio. Redis ciągi są Najprostszy rodzaj wartość usługi Redis i może zawierać wiele typów danych, w tym serializowane strumieni binarnych, i gdy nie mogą używać tego typu bezpośrednio, będzie używać metod, które zawierają `String` w nazwie. Dla większości pierwotnych typów danych, przechowywania i pobierania elementów z pamięci podręcznej przy użyciu `StringSet` i `StringGet` metody, chyba że kolekcji lub inne typy danych Redis będą przechowywane w pamięci podręcznej. 

`StringSet` i `StringGet` są podobne do usługi Managed Cache Service `Put` i `Get` metody, z jednym z głównych różnica, możliwe, że przed Ustawianie i pobieranie obiektu platformy .NET w pamięci podręcznej muszą serializację go najpierw. 

Podczas wywoływania `StringGet`, jeśli obiekt istnieje, jest zwracany, a jeśli nie, zwracana jest wartość null. W takim przypadku można pobrać wartość z żądanego źródła danych i zapisać ją w pamięci podręcznej do późniejszego użytku. Ten wzorzec jest nazywany wzorcem odkładania do pamięci podręcznej.

Aby określić wygaśnięcie elementu w pamięci podręcznej, użyj parametru `TimeSpan` metody `StringSet`.

```csharp
cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));
```

Pamięć podręczna systemu Azure dla usługi Redis może pracować z obiektami platformy .NET oraz pierwotne typy danych, ale zanim możliwe buforowanie obiektu platformy .NET, trzeba go serializować. Tej serializacji odpowiada Deweloper aplikacji, a także zapewnia deweloperom elastyczną możliwość wybrania serializatora. Aby uzyskać więcej informacji i przykładowy kod, zobacz [Praca z obiektami platformy .NET w pamięci podręcznej](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-cache-for-redis"></a>Migracja stanu sesji platformy ASP.NET, a dane wyjściowe pamięci podręcznej do usługi Azure Cache dla usługi Redis
Pamięć podręczna systemu Azure dla usługi Redis zawiera dostawców dla stanu sesji platformy ASP.NET i buforowanie danych wyjściowych strony. Aby przeprowadzić migrację aplikacji korzystającej z usługi Managed Cache Service wersje tych dostawców, najpierw usuń istniejące sekcje z Twojego pliku web.config, a następnie skonfiguruj usługi Azure Cache dla pamięci podręcznej Redis wersji dostawcy. Aby uzyskać instrukcje na temat korzystania z usługi Azure Cache dla dostawców usługi Redis ASP.NET, zobacz [dostawca stanu sesji ASP.NET dla usługi Azure Cache dla usługi Redis](cache-aspnet-session-state-provider.md) i [dostawca wyjściowej pamięci podręcznej ASP.NET dla usługi Azure Cache dla usługi Redis](cache-aspnet-output-cache-provider.md).

## <a name="next-steps"></a>Kolejne kroki
Zapoznaj się z [usługi Azure Cache dokumentacja pamięci podręcznej Redis](https://azure.microsoft.com/documentation/services/cache/) do samouczków, przykładów, filmy wideo i inne.

