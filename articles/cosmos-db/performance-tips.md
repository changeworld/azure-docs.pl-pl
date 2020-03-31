---
title: Wskazówki dotyczące wydajności usługi Azure Cosmos DB dla platformy .NET
description: Poznaj opcje konfiguracji klienta, aby zwiększyć wydajność usługi Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: sngun
ms.openlocfilehash: 68a9a7d5f90831bb2e0c9284cb71ae4b92edffad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131417"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Porady dotyczące wydajności usługi Azure Cosmos DB i platformy .NET

> [!div class="op_single_selector"]
> * [Java (asynchroniczny)](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Usługa Azure Cosmos DB to szybka i elastyczna rozproszona baza danych, która bezproblemowo skaluje się z gwarantowanym opóźnieniem i przepływnością. Nie trzeba wprowadzać istotnych zmian architektury lub napisać złożony kod, aby skalować bazę danych za pomocą usługi Azure Cosmos DB. Skalowanie w górę i w dół jest tak proste, jak wykonywanie pojedynczego wywołania interfejsu API. Aby dowiedzieć się więcej, zobacz [jak aprowizować przepływność kontenera](how-to-provision-container-throughput.md) lub [jak aprowizować przepływność bazy danych](how-to-provision-database-throughput.md). Ale ponieważ usługa Azure Cosmos DB jest dostępna za pośrednictwem wywołań sieciowych, istnieją optymalizacje po stronie klienta, które można wprowadzić, aby osiągnąć najwyższą wydajność podczas korzystania z [modułu SQL .NET SDK](sql-api-sdk-dotnet-standard.md).

Dlatego, jeśli próbujesz poprawić wydajność bazy danych, rozważ następujące opcje:

## <a name="hosting-recommendations"></a>Rekomendacje hostingu

**W przypadku obciążeń intensywnie korzystających z zapytań należy używać systemu Windows 64-bitowego zamiast przetwarzania hosta w systemie Linux lub Windows 32-bitowym**

Firma Microsoft zaleca windows 64-bitowego hosta przetwarzania dla lepszej wydajności. Zestaw SDK SQL zawiera natywną bibliotekę ServiceInterop.dll do analizowania i optymalizowania zapytań lokalnie. ServiceInterop.dll jest obsługiwany tylko na platformie Windows x64. W przypadku systemu Linux i innych nieobsługiconych platform, na których serviceinterop.dll nie jest dostępny, do bramy nawiązywane jest dodatkowe wywołanie sieciowe w celu uzyskania zoptymalizowanego zapytania. Następujące typy aplikacji domyślnie używają 32-bitowego przetwarzania hosta. Aby zmienić przetwarzanie hosta na przetwarzanie 64-bitowe, wykonaj następujące kroki, w zależności od typu aplikacji:

- W przypadku aplikacji wykonywalnych można zmienić przetwarzanie hosta, ustawiając [obiekt docelowy platformy](https://docs.microsoft.com/visualstudio/ide/how-to-configure-projects-to-target-platforms?view=vs-2019) na **x64** w oknie **Właściwości projektu** na karcie **Kompilacja.**

- W przypadku projektów testowych opartych na testach vstest można zmienić przetwarzanie hosta, wybierając **domyślną** > architekturę procesora**testowego** > **testową jako X64** w menu **testu** programu Visual Studio.

- W przypadku lokalnie wdrożonych ASP.NET aplikacji sieci Web można zmienić przetwarzanie hosta, wybierając pozycję **Użyj 64-bitowej wersji programu IIS Express dla witryn sieci Web i projektów** w obszarze Opcje **narzędzi** > **Options** > **projekty sieci** > Web i**projekty sieci Web**.

- W przypadku ASP.NET aplikacji sieci web wdrożonych na platformie Azure można zmienić przetwarzanie hosta, wybierając platformę **64-bitową** w **ustawieniach aplikacji** w witrynie Azure portal.

> [!NOTE] 
> Domyślnie nowe projekty programu Visual Studio są ustawione na **dowolny procesor.** Zaleca się ustawienie projektu na **x64,** aby nie przełączyć się na **x86**. Projekt ustawiony na **Dowolny procesor CPU** można łatwo przełączyć się na **x86,** jeśli zostanie dodana zależność tylko dla x86.<br/>
> ServiceInterop.dll musi znajdować się w folderze, z których jest wykonywana biblioteka DLL SDK. Powinno to dotyczyć tylko wtedy, gdy ręcznie kopiujesz biblioteki DLL lub masz niestandardowe systemy kompilacji/wdrażania.
    
**Włączanie wyrzucania elementów bezużytecznych po stronie serwera (GC)**

Zmniejszenie częstotliwości wyrzucania elementów bezużytecznych może pomóc w niektórych przypadkach. W .NET ustaw [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) na `true`.

**Skalowanie w poziomie obciążenia klienta**

Jeśli testujesz na wysokim poziomie przepływności (więcej niż 50 000 ru/s), aplikacja kliencka może stać się wąskim gardłem ze względu na ograniczenie komputera w zakresie wykorzystania procesora LUB sieci. Jeśli osiągniesz ten punkt, możesz kontynuować wypychanie konta usługi Azure Cosmos DB, skalując aplikacje klienckie w poziomie na wielu serwerach.

> [!NOTE] 
> Wysokie użycie procesora CPU może spowodować zwiększone opóźnienie i wyjątki limitu czasu żądania.

## <a name="networking"></a>Obsługa sieci
<a id="direct-connection"></a>

**Zasady połączenia: użyj trybu bezpośredniego połączenia**

Sposób, w jaki klient łączy się z usługą Azure Cosmos DB ma istotne implikacje wydajności, szczególnie w przypadku obserwowanego opóźnienia po stronie klienta. Istnieją dwa kluczowe ustawienia konfiguracji do konfigurowania zasad połączenia klienta: *tryb* połączenia i *protokół*połączenia .  Dwa dostępne tryby to:

   * Tryb bramy
      
     Tryb bramy jest obsługiwany na wszystkich platformach SDK i jest skonfigurowanym ustawieniem domyślnym dla [sdk microsoft.Azure.DocumentDB](sql-api-sdk-dotnet.md). Jeśli aplikacja działa w sieci firmowej ze ścisłymi ograniczeniami zapory, tryb bramy jest najlepszym wyborem, ponieważ używa standardowego portu HTTPS i jednego punktu końcowego. Kompromisem wydajności jest jednak to, że tryb bramy obejmuje dodatkowy przeskok sieciowy za każdym razem, gdy dane są odczytywane lub zapisywane w usłudze Azure Cosmos DB. Tak więc tryb bezpośredni oferuje lepszą wydajność, ponieważ jest mniej przeskoków sieciowych. Zaleca się również tryb połączenia bramy podczas uruchamiania aplikacji w środowiskach, które mają ograniczoną liczbę połączeń gniazd.

     Korzystając z zestawu SDK w usłudze Azure Functions, szczególnie w [planie zużycia,](../azure-functions/functions-scale.md#consumption-plan)należy pamiętać o bieżących [limitach połączeń.](../azure-functions/manage-connections.md) W takim przypadku tryb bramy może być lepszy, jeśli pracujesz również z innymi klientami opartymi na protokoście z protokołem HTTP w aplikacji usługi Azure Functions.

   * Tryb bezpośredni

     Tryb bezpośredni obsługuje łączność za pośrednictwem protokołu TCP i jest domyślnym trybem łączności, jeśli używasz [sdk Microsoft.Azure.Cosmos/.NET V3](sql-api-sdk-dotnet-standard.md).

W trybie bramy usługa Azure Cosmos DB używa portów 443 i portów 10250, 10255 i 10256 podczas korzystania z interfejsu API bazy danych usługi Azure Cosmos dla usługi MongoDB. Port 10250 jest mapowane do domyślnego wystąpienia MongoDB bez replikacji geograficznej. Porty 10255 i 10256 są mapowane na wystąpienie mongodb z replikacją geograficzną.
     
Korzystając z protokołu TCP w trybie bezpośrednim, oprócz portów bramy, należy upewnić się, że zakres portów między 10000 a 20000 jest otwarty, ponieważ usługa Azure Cosmos DB używa dynamicznych portów TCP. Jeśli te porty nie są otwarte i spróbujesz użyć protokołu TCP, zostanie wyświetlony błąd 503 Usługa niedostępna. W tej tabeli przedstawiono tryby łączności dostępne dla różnych interfejsów API i porty usługi używane dla każdego interfejsu API:

|Tryb połączenia  |Obsługiwany protokół  |Obsługiwane sdk  |Port INTERFEJSU API/Usługi  |
|---------|---------|---------|---------|
|Brama  |   HTTPS    |  Wszystkie SDK    |   SQL (443), MongoDB (10250, 10255, 10256), Tabela (443), Kasandra (10350), Wykres (443)    |
|Direct    |     TCP    |  Zestaw SDK .NET    | Porty w zakresie od 10000 do 20000 |

Usługa Azure Cosmos DB oferuje prosty, otwarty model programowania RESTful za pośrednictwem protokołu HTTPS. Ponadto oferuje wydajny protokół TCP, który jest również restful w swoim modelu komunikacji i jest dostępny za pośrednictwem pliku SDK klienta .NET. Protokół TCP używa protokołu SSL do uwierzytelniania początkowego i szyfrowania ruchu. Aby uzyskać najlepszą wydajność, użyj protokołu TCP, jeśli to możliwe.

W przypadku SDK V3 tryb połączenia jest `CosmosClient` konfiguracyjny podczas tworzenia wystąpienia w pliku `CosmosClientOptions`. Pamiętaj, że tryb bezpośredni jest domyślny.

```csharp
var serviceEndpoint = new Uri("https://contoso.documents.net");
var authKey = "your authKey from the Azure portal";
CosmosClient client = new CosmosClient(serviceEndpoint, authKey,
new CosmosClientOptions
{
    ConnectionMode = ConnectionMode.Gateway // ConnectionMode.Direct is the default
});
```

W przypadku zestawu SDK microsoft.Azure.DocumentDB można skonfigurować `DocumentClient` tryb połączenia `ConnectionPolicy` podczas konstruowania wystąpienia przy użyciu parametru. Jeśli używasz trybu bezpośredniego, można `Protocol` również `ConnectionPolicy` ustawić za pomocą parametru.

```csharp
var serviceEndpoint = new Uri("https://contoso.documents.net");
var authKey = "your authKey from the Azure portal";
DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct, // ConnectionMode.Gateway is the default
   ConnectionProtocol = Protocol.Tcp
});
```

Ponieważ protokół TCP jest obsługiwany tylko w trybie bezpośrednim, jeśli używasz trybu bramy, `Protocol` protokół `ConnectionPolicy` HTTPS jest zawsze używany do komunikowania się z bramą, a wartość jest ignorowana.

![Zasady połączeń usługi Azure Cosmos DB](./media/performance-tips/connection-policy.png)

**Wywołanie OpenAsync, aby uniknąć opóźnienia uruchamiania przy pierwszym żądaniu**

Domyślnie pierwsze żądanie ma większe opóźnienie, ponieważ musi pobrać tabelę routingu adresów. Podczas korzystania z [SDK V2](sql-api-sdk-dotnet.md), wywołać `OpenAsync()` raz podczas inicjowania, aby uniknąć tego opóźnienia uruchamiania na pierwsze żądanie:

    await client.OpenAsync();

> [!NOTE] 
> `OpenAsync`wygeneruje żądania, aby uzyskać tabelę routingu adresów dla wszystkich kontenerów na koncie. Dla kont, które mają wiele kontenerów, ale `OpenAsync` których aplikacja uzyskuje dostęp do podzbioru z nich, wygenerowałby niepotrzebną ilość ruchu, co spowoduje, że inicjowanie jest powolne. Dlatego `OpenAsync` użycie może nie być przydatne w tym scenariuszu, ponieważ spowalnia uruchamianie aplikacji.

   <a id="same-region"></a>
**Aby uzyskać wydajność, kolokwuj klientów w tym samym regionie platformy Azure**

Jeśli to możliwe, umieść wszystkie aplikacje, które wywołują usługę Azure Cosmos DB w tym samym regionie co baza danych usługi Azure Cosmos DB. Oto przybliżone porównanie: wywołania usługi Azure Cosmos DB w tym samym regionie ukończone w ciągu 1 ms do 2 ms, ale opóźnienie między zachodnim i wschodnim wybrzeżem STANÓW Zjednoczonych jest więcej niż 50 ms. To opóźnienie może się różnić od żądania do żądania, w zależności od trasy podjęte przez żądanie, jak przechodzi z klienta do granicy centrum danych platformy Azure. Można uzyskać najniższe możliwe opóźnienie, zapewniając, że aplikacja wywołująca znajduje się w tym samym regionie platformy Azure jako aprowizowanym punktem końcowym usługi Azure Cosmos DB. Aby uzyskać listę dostępnych regionów, zobacz [Regiony platformy Azure](https://azure.microsoft.com/regions/#services).

![Zasady](./media/performance-tips/same-region.png) połączeń usługi Azure Cosmos DB<a id="increase-threads"></a>

**Zwiększenie liczby wątków/zadań**

Ponieważ wywołania usługi Azure Cosmos DB są dokonywane za pośrednictwem sieci, może być konieczne zróżnicowanie stopnia równoległości żądań, tak aby aplikacja kliencka spędzała minimalny czas oczekiwania między żądaniami. Na przykład jeśli używasz [biblioteki równoległej zadań](https://msdn.microsoft.com//library/dd460717.aspx).NET , utwórz w kolejności setek zadań, które odczytują z usługi Azure Cosmos DB lub zapisują je w usłudze Azure Cosmos DB.

**Włączanie przyspieszonej sieci**
 
 Aby zmniejszyć opóźnienia i jitter procesora CPU, zaleca się włączenie przyspieszonej sieci na maszynach wirtualnych klienta. Zobacz [Tworzenie maszyny wirtualnej systemu Windows z przyspieszoną siecią](../virtual-network/create-vm-accelerated-networking-powershell.md) lub [Tworzenie maszyny wirtualnej systemu Linux z przyspieszoną siecią](../virtual-network/create-vm-accelerated-networking-cli.md).

## <a name="sdk-usage"></a>SDK usage (Użycie zestawu SDK)
**Instalowanie najnowszego pakietu SDK**

Zestawy SDK usługi Azure Cosmos DB są stale ulepszane, aby zapewnić najlepszą wydajność. Zobacz strony [SDK usługi Azure Cosmos DB,](sql-api-sdk-dotnet-standard.md) aby określić najnowsze zestaw SDK i przejrzeć ulepszenia.

**Korzystanie z interfejsów API strumienia**

[.NET SDK V3](sql-api-sdk-dotnet-standard.md) zawiera interfejsy API strumienia, które mogą odbierać i zwracać dane bez serializacji. 

Aplikacje warstwy środkowej, które nie zużywają odpowiedzi bezpośrednio z sdk, ale przekazywania ich do innych warstw aplikacji mogą korzystać z interfejsów API strumienia. Zobacz przykłady [zarządzania towarami,](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement) aby zapoznać się z przykładami obsługi strumienia.

**Używanie klienta usługi Azure Cosmos DB w celu uzyskania okresu istnienia aplikacji**

Każde `DocumentClient` `CosmosClient` wystąpienie jest bezpieczne dla wątków i wykonuje efektywne zarządzanie połączeniami i buforowanie adresów podczas pracy w trybie bezpośrednim. Aby umożliwić wydajne zarządzanie połączeniami i lepszą wydajność klienta SDK, zaleca się użycie jednego wystąpienia na `AppDomain` przez cały okres istnienia aplikacji.

   <a id="max-connection"></a>

**Zwiększanie System.Net MaxConnections na hosta podczas korzystania z trybu bramy**

Żądania usługi Azure Cosmos DB są dokonywane za pośrednictwem protokołu HTTPS/REST podczas korzystania z trybu bramy. Są one poddawane domyślnemu limitowi połączenia na adres hosta lub adres IP. Może być konieczne `MaxConnections` ustawienie wyższej wartości (100 do 1000), aby biblioteka klienta mogła używać wielu jednoczesnych połączeń z usługą Azure Cosmos DB. W pliku .NET SDK 1.8.0 lub nowszym wartość domyślna dla [servicepointmanager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) wynosi 50. Aby zmienić tę wartość, można ustawić [wartość Documents.Client.ConnectionPolicy.MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) na wyższą.

**Dostrajanie zapytań równoległych dla kolekcji podzielonych na partycje**

SQL .NET SDK 1.9.0 i nowsze obsługują zapytania równoległe, które umożliwiają równoległe wykonywanie zapytań o partycjonowane kolekcję. Aby uzyskać więcej informacji, zobacz [przykłady kodu](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) związane z pracą z zestawami SDK. Zapytania równoległe są zaprojektowane w celu zapewnienia lepszego opóźnienia zapytania i przepływności niż ich odpowiednik szeregowy. Zapytania równoległe zawierają dwa parametry, które można dostroić do wymagań: 
- `MaxDegreeOfParallelism`steruje maksymalną liczbą partycji, które mogą być wyszukiwane równolegle. 
- `MaxBufferedItemCount`kontroluje liczbę wstępnie pobranych wyników.

***Dostrajanie stopnia równoległości***

Kwerenda równoległa działa przez wykonywanie zapytań o wiele partycji równolegle. Ale dane z pojedynczej partycji są pobierane szeregowo w odniesieniu do kwerendy. Ustawienie `MaxDegreeOfParallelism` w [SDK V2](sql-api-sdk-dotnet.md) lub `MaxConcurrency` W [SDK V3](sql-api-sdk-dotnet-standard.md) do liczby partycji ma największe szanse na osiągnięcie najbardziej wydajne zapytanie, pod warunkiem, że wszystkie inne warunki systemowe pozostają takie same. Jeśli nie znasz liczby partycji, można ustawić stopień równoległości do dużej liczby. System wybierze minimalną (liczbę partycji, dane wejściowe dostarczone przez użytkownika) jako stopień równoległości.

Należy zauważyć, że zapytania równoległe dają największe korzyści, jeśli dane są równomiernie rozłożone na wszystkie partycje w odniesieniu do kwerendy. Jeśli kolekcja podzielona na partycje jest podzielona na partycje, tak aby wszystkie lub większość danych zwracanych przez kwerendę jest skoncentrowana na kilku partycjach (jedna partycja jest najgorszym przypadkiem), te partycje będą wąskie gardło wydajność kwerendy.

***Dostrajanie MaxBufferedItemCount***
    
Kwerenda równoległa jest przeznaczona do wstępnego pobierania wyników, podczas gdy bieżąca partia wyników jest przetwarzana przez klienta. To pobieranie wstępne pomaga poprawić ogólne opóźnienie kwerendy. Parametr `MaxBufferedItemCount` ogranicza liczbę wstępnie pobranych wyników. Ustaw `MaxBufferedItemCount` oczekiwaną liczbę zwracanych wyników (lub większą liczbę), aby umożliwić kwerendzie otrzymanie maksymalnej korzyści z pobierania wstępnego.

Pobieranie wstępne działa w ten sam sposób, niezależnie od stopnia równoległości i istnieje pojedynczy bufor dla danych ze wszystkich partycji.  

**Zaimplementuj wycofywanie w ponowienie po interwałach**

Podczas testowania wydajności należy zwiększyć obciążenie, dopóki nie zostaną ograniczone małe żądania. Jeśli żądania są ograniczane, aplikacja kliencka powinna wycofać się na przepustnicy dla interwału ponawiania określonych przez serwer. Respektowanie backoff zapewnia spędzać minimalną ilość czasu oczekiwania między ponownych prób. 

Obsługa zasad ponawiania próby jest uwzględniona w tych zestawiech SDK:
- Wersja 1.8.0 i nowsze [.NET SDK dla SQL](sql-api-sdk-dotnet.md) i [Java SDK dla SQL](sql-api-sdk-java.md)
- Wersja 1.9.0 i [nowsze sdk node.js dla sql](sql-api-sdk-node.md) i [python SDK dla języka SQL](sql-api-sdk-python.md)
- Wszystkie obsługiwane wersje [podstawowych](sql-api-sdk-dotnet-core.md) sdk .NET 

Aby uzyskać więcej informacji, zobacz [Ponów próbępo .](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx)
    
W wersji 1.19 i nowszej .NET SDK istnieje mechanizm rejestrowania dodatkowych informacji diagnostycznych i rozwiązywania problemów z opóźnieniami, jak pokazano w poniższym przykładzie. Można rejestrować ciąg diagnostyczny dla żądań, które mają wyższe opóźnienie odczytu. Przechwycony ciąg diagnostyczny pomoże Ci zrozumieć, ile razy odebrano 429 błędów dla danego żądania.

```csharp
ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
readDocument.RequestDiagnosticsString 
```

**Identyfikatory identyfikatorów URI dokumentu pamięci podręcznej dla mniejszego opóźnienia odczytu**

Identyfikatory identyfikatorów URI dokumentu pamięci podręcznej, gdy tylko jest to możliwe, aby uzyskać najlepszą wydajność odczytu. Należy zdefiniować logikę do buforowania identyfikatora zasobu podczas tworzenia zasobu. Wyszukiwania oparte na identyfikatorach zasobów są szybsze niż wyszukiwania oparte na nazwach, więc buforowanie tych wartości zwiększa wydajność.

   <a id="tune-page-size"></a>
**Dostrajanie rozmiaru strony dla zapytań/kanałów informacyjnych w celu uzyskania lepszej wydajności**

Po wykonaniu zbiorczego odczytu dokumentów przy użyciu funkcji `ReadDocumentFeedAsync`dodawania danych odczytu (na przykład) lub po wydaniu kwerendy SQL, wyniki są zwracane w sposób podzielony na segmenty, jeśli zestaw wyników jest zbyt duży. Domyślnie wyniki są zwracane w fragmentach 100 elementów lub 1 MB, w zależności od tego, który limit zostanie osiągnięty jako pierwszy.

Aby zmniejszyć liczbę rund sieciowych wymaganych do pobrania wszystkich odpowiednich wyników, można zwiększyć rozmiar strony za pomocą [x-ms-max-item-count,](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) aby zażądać aż 1000 nagłówków. Jeśli trzeba wyświetlić tylko kilka wyników, na przykład, jeśli interfejs użytkownika lub interfejs API aplikacji zwraca tylko 10 wyników naraz, można również zmniejszyć rozmiar strony do 10, aby zmniejszyć przepływność zużywaną dla odczytów i zapytań.

> [!NOTE] 
> Właściwość `maxItemCount` nie powinna być używana tylko do podziałania na strony. Jego głównym zastosowaniem jest zwiększenie wydajności zapytań poprzez zmniejszenie maksymalnej liczby elementów zwróconych na jednej stronie.  

Można również ustawić rozmiar strony przy użyciu dostępnych zestawów SDK usługi Azure Cosmos DB. [Właściwość MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount?view=azure-dotnet) w `FeedOptions` umożliwia ustawienie maksymalnej liczby elementów, które mają być zwracane w operacji wyliczenia. Gdy `maxItemCount` jest ustawiona na -1, zestaw SDK automatycznie znajduje optymalną wartość, w zależności od rozmiaru dokumentu. Przykład:
    
```csharp
IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
```
    
Po wykonaniu kwerendy wynikowe dane są wysyłane w ramach pakietu TCP. Jeśli określisz zbyt niską wartość dla `maxItemCount`, liczba przejazdów wymaganych do wysłania danych w pakiecie TCP jest wysoka, co wpływa na wydajność. Więc jeśli nie masz pewności, jaką `maxItemCount` wartość ustawić dla właściwości, najlepiej ustawić ją na -1 i pozwolić zestawowi SDK wybrać wartość domyślną.

**Zwiększenie liczby wątków/zadań**

Zobacz [Zwiększanie liczby wątków/zadań](#increase-threads) w sekcji Sieć tego artykułu.

## <a name="indexing-policy"></a>Zasady indeksowania
 
**Wykluczanie nieużywanych ścieżek z indeksowania w celu przyspieszenia operacji zapisu**

Zasady indeksowania usługi Azure Cosmos DB umożliwiają również określenie, które ścieżki dokumentu mają być uwzględniane w indeksowaniu lub wykluczane z indeksowania przy użyciu ścieżek indeksowania (IndexingPolicy.IncludedPaths i IndexingPolicy.ExcludedPaths). Ścieżki indeksowania można poprawić wydajność zapisu i zmniejszyć magazyn indeksu dla scenariuszy, w których wzorce zapytań są znane wcześniej. Dzieje się tak, ponieważ koszty indeksowania są skorelowane bezpośrednio z liczbą indeksowanych ścieżek unikatowych. Na przykład ten kod pokazuje, jak wykluczyć całą sekcję dokumentów (poddrzewo) z indeksowania przy użyciu symbolu wieloznacznego "*":

```csharp
var collection = new DocumentCollection { Id = "excludedPathCollection" };
collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);
```

Aby uzyskać więcej informacji, zobacz [zasady indeksowania usługi Azure Cosmos DB](index-policy.md).

## <a name="throughput"></a>Przepływność
<a id="measure-rus"></a>

**Mierzenie i dostrajanie dla niższych jednostek żądań/drugie użycie**

Usługa Azure Cosmos DB oferuje bogaty zestaw operacji bazy danych. Operacje te obejmują relacyjne i hierarchiczne zapytania z UDF, procedury przechowywane i wyzwalacze, wszystkie działające na dokumentach w kolekcji bazy danych. Koszt skojarzony z każdą z tych operacji różni się w zależności od procesora CPU, we/wy i pamięci wymaganej do ukończenia operacji. Zamiast myśleć o zasobach sprzętowych i zarządzać nimi, można myśleć o jednostce żądania (RU) jako o pojedynczej miary dla zasobów wymaganych do wykonywania różnych operacji bazy danych i obsługi żądania aplikacji.

Przepływność jest aprowizowana na podstawie liczby [jednostek żądań ustawionych](request-units.md) dla każdego kontenera. Zużycie jednostki żądania jest obliczane jako stawka na sekundę. Aplikacje, które przekraczają aprowizowaną stawkę jednostki żądania dla ich kontenera są ograniczone, dopóki stawka spadnie poniżej poziomu aprowizacji dla kontenera. Jeśli aplikacja wymaga wyższego poziomu przepływności, można zwiększyć przepływność, inicjując inicjowanie obsługi administracyjnej dodatkowych jednostek żądań.

Złożoność kwerendy wpływa na liczbę jednostek żądań są używane dla operacji. Liczba predykatów, charakter predykatów, liczba plików UDF i rozmiar źródłowego zestawu danych mają wpływ na koszt operacji kwerendy.

Aby zmierzyć obciążenie każdej operacji (tworzenie, aktualizowanie lub usuwanie), sprawdź nagłówek [x-ms-request-charge](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (lub równoważną `RequestCharge` właściwość w `ResourceResponse\<T>` pliku .NET SDK lub `FeedResponse\<T>` w programie .NET), aby zmierzyć liczbę jednostek żądań zużywanych przez operacje:

```csharp
// Measure the performance (Request Units) of writes
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
// Measure the performance (Request Units) of queries
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
while (queryable.HasMoreResults)
    {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
        Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
    }
```             

Opłata za żądanie zwrócona w tym nagłówku jest ułamek aprowizowanej przepływności (czyli 2000 udr/ sekundę). Na przykład jeśli poprzednia kwerenda zwraca 1000 dokumentów 1 KB, koszt operacji wynosi 1000. Tak więc w ciągu jednej sekundy serwer honoruje tylko dwa takie żądania przed ograniczeniem szybkości późniejszych żądań. Aby uzyskać więcej informacji, zobacz [Jednostki żądań](request-units.md) i [Kalkulator jednostki żądań](https://www.documentdb.com/capacityplanner).
<a id="429"></a>

**Zbyt duża szybkość obchodzenia się z ograniczeniem/żądaniem**

Gdy klient próbuje przekroczyć przepływność zarezerwowane dla konta, nie ma pogorszenia wydajności na serwerze i nie ma użycia przepustowości poza poziomem zastrzeżonym. Serwer prewuwnie zakończy żądanie za pomocą RequestRateTooLarge (kod stanu HTTP 429). Zwróci nagłówek [x-ms-retry-after-ms,](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) który wskazuje czas w milisekundach, który użytkownik musi poczekać przed ponowną próbą żądania.

    HTTP Status 429,
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Wszystkie SDK wszystkie niejawnie przechwytują tę odpowiedź, respektują nagłówek ponawiania po próbie określone przez serwer i ponów próbę żądania. Jeśli konto nie jest dostępne jednocześnie przez wielu klientów, następna ponowna próby zakończy się pomyślnie.

Jeśli masz więcej niż jednego klienta łącznie działającego konsekwentnie powyżej szybkości żądania, domyślna liczba ponownych prób aktualnie ustawiona na 9 wewnętrznie przez klienta może nie wystarczyć. W takim przypadku klient zgłasza DocumentClientException z kodem stanu 429 do aplikacji. 

Domyślną liczbę ponownych prób można `RetryOptions` zmienić, ustawiając je w wystąpieniu. `ConnectionPolicy` Domyślnie DocumentClientException z kodem stanu 429 jest zwracany po skumulowanym czasie oczekiwania 30 sekund, jeśli żądanie nadal działa powyżej stawki żądania. Ten błąd zwraca nawet wtedy, gdy bieżąca liczba ponownych prób jest mniejsza niż maksymalna liczba ponownych prób, niezależnie od tego, czy bieżąca wartość jest wartością domyślną 9, czy wartością zdefiniowaną przez użytkownika.

Automatyczne zachowanie ponawiania pomaga zwiększyć odporność i użyteczność dla większości aplikacji. Ale to może nie być najlepsze zachowanie, gdy robisz testy porównawcze wydajności, zwłaszcza podczas pomiaru opóźnienia. Obserwowane przez klienta opóźnienie będzie skok, jeśli eksperyment uderza przepustnicy serwera i powoduje, że zestaw SDK klienta po cichu ponowić próbę. Aby uniknąć skoków opóźnień podczas eksperymentów wydajności, zmierz opłatę zwróconą przez każdą operację i upewnij się, że żądania działają poniżej stawki zarezerwowane żądanie. Aby uzyskać więcej informacji, zobacz [Żądania jednostek](request-units.md).

**Aby uzyskać wyższą przepustowość, projektowanie mniejszych dokumentów**

Opłata za żądanie (czyli koszt przetwarzania żądania) danej operacji jest skorelowana bezpośrednio z rozmiarem dokumentu. Operacje na dużych dokumentach kosztują więcej niż operacje na małych dokumentach.

## <a name="next-steps"></a>Następne kroki
Przykładowa aplikacja używana do oceny usługi Azure Cosmos DB pod kątem scenariuszy o wysokiej wydajności na kilku komputerach klienckich można znaleźć w [przypadku testowania wydajności i skalowania za pomocą usługi Azure Cosmos DB.](performance-testing.md)

Aby dowiedzieć się więcej na temat projektowania aplikacji pod kątem skali i wysokiej wydajności, zobacz [Partycjonowanie i skalowanie w usłudze Azure Cosmos DB.](partition-data.md)
