---
title: Jednostek żądania & Szacowanie przepływności - DB rozwiązania Cosmos Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu zrozumieć, określ i oszacować wymagania dotyczące jednostki żądania w usłudze Azure DB rozwiązania Cosmos.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: d0a3c310-eb63-4e45-8122-b7724095c32f
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: sngun
ms.openlocfilehash: ab85591ce4ffadeba4c1336efea0bd6945d46ec3
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="request-units-in-azure-cosmos-db"></a>Żądanie jednostki w Azure rozwiązania Cosmos bazy danych

[Azure DB rozwiązania Cosmos](https://azure.microsoft.com/services/cosmos-db/) jest globalnie rozproszone wielu modelu bazy danych firmy Microsoft. Z bazy danych rozwiązania Cosmos platformy Azure nie trzeba wynajmować maszyn wirtualnych, wdrażania oprogramowania lub monitora bazy danych. Azure DB rozwiązania Cosmos jest obsługiwane i stale monitorowane przez górny pracownicy firmy Microsoft do dostarczania światowej klasy ochrony dostępności, wydajności i danych. Można uzyskać dostępu do danych przy użyciu interfejsów API wybranych przez użytkownika, takich jak [interfejsu API SQL](documentdb-introduction.md), [API bazy danych MongoDB](mongodb-introduction.md), [API tabeli](table-introduction.md)i Gremlin za pośrednictwem [interfejsu API programu Graph](graph-introduction.md) - Wszystkie obsługiwane. Waluta Azure DB rozwiązania Cosmos jest jednostka żądań (RU). Z RUs nie należy do zarezerwowania możliwości odczytu/zapisu lub udostępnić procesora CPU, pamięci i IOPS.

Azure DB rozwiązania Cosmos obsługuje kilka interfejsów API z różnych operacji — od prostych odczytuje i zapisuje wykres złożonych zapytań. Ponieważ nie wszystkie żądania są takie same, są przypisane znormalizowane ilość **jednostek żądania** na podstawie ilości obliczeń wymaganych do obsłużenia żądania. Liczba jednostek żądania dla operacji jest deterministyczna, a można śledzić liczbę jednostek żądania używane przez żadnych operacji w usłudze Azure DB rozwiązania Cosmos za pośrednictwem nagłówka odpowiedzi. 

Zapewnienie przewidywalnej wydajności, należy zarezerwować przepływności w jednostkach 100 RU/sekundę. Możesz [oszacować przepustowość sieci musi](request-units.md#estimating-throughput-needs) za pomocą usługi Azure DB rozwiązania Cosmos [Kalkulator jednostki żądania](https://www.documentdb.com/capacityplanner).

![Kalkulator przepływności][5]

Po przeczytaniu tego artykułu, będziesz mieć możliwość odpowiedzieć na następujące pytania:  

* Co to są jednostek żądania i żądania opłat?
* Jak określić pojemność jednostki żądania kontener?
* Jak oszacować musi jednostki żądania Moja aplikacja
* Co się stanie, jeśli I przekracza pojemność jednostki żądania kontener?

Bazy danych Azure rozwiązania Cosmos jest wiele modeli bazy danych, to należy pamiętać, że ten artykuł dotyczy kolekcji/dokumentu, dokument interfejsu API graph/węzeł Graph API i tabeli na jednostkę tabeli interfejsu API. Ten artykuł dotyczy koncepcji zbierania, wykres lub tabeli jako kontenera, a dokument, węzeł lub jednostki jako element.

## <a name="request-units-and-request-charges"></a>Jednostek żądania i żądania opłat
Azure DB rozwiązania Cosmos zapewnia szybkie, przewidywalną wydajność przez *rezerwowania* zasobów do zaspokojenia musi przepływność aplikacji.  Ponieważ aplikacja obciążenia i dostęp do zmiany wzorce wraz z upływem czasu, bazy danych Azure rozwiązania Cosmos umożliwia łatwe zwiększyć lub zmniejszyć ilość zarezerwowaną przepływnością dostępne dla aplikacji.

Z bazy danych Azure rozwiązania Cosmos zarezerwowaną przepływnością jest określane w przeliczeniu na jednostki żądania przetwarzania na sekundę. Można potraktować jednostki żądania jako walutę przepływności, zgodnie z którymi możesz *zarezerwować* ilość gwarantowane żądania jednostki dostępne dla aplikacji na podstawie sekundowym.  Każdej operacji w usłudze Azure DB rozwiązania Cosmos — zapisywanie dokumentu, wykonywania zapytania, aktualizowanie dokumentu — korzysta z Procesora, pamięci i IOPS.  Oznacza to, że każda operacja wiąże się z *żądań bezpłatnie*, który jest wyrażona w *jednostek żądania*.  Opis czynników, które mają wpływ żądania jednostki opłat, wraz z aplikacji wymagania dotyczące przepływności, umożliwia uruchamianie aplikacji jako koszt skutecznie, jak to możliwe. Eksplorator danych w portalu Azure jest również cudowne narzędzie do testowania podstawowej zapytania.

Zalecamy rozpoczęcie pracy od obejrzenia poniższego klipu wideo, w którym Azure Menedżera programów DB rozwiązania Cosmos Andrew Liu omówiono jednostki żądania.

> [!VIDEO https://www.youtube.com/embed/stk5WSp5uX0]
> 
> 

## <a name="specifying-request-unit-capacity-in-azure-cosmos-db"></a>Określanie pojemność jednostki żądania w usłudze Azure DB rozwiązania Cosmos
Podczas uruchamiania nowy kontener określić liczbę jednostek żądań na sekundę (RU na sekundę) mają zastrzeżone. Na podstawie udostępnionej przepływności, bazy danych rozwiązania Cosmos Azure przydziela fizycznej partycji do hosta z kontenera i podziałów/rebalances danych na partycji jako ich przyrostu.

Kontenery DB rozwiązania Cosmos Azure można utworzyć ustalonej lub nieograniczone. Kontenery o stałym rozmiarze są ograniczone do 10 GB, a ich maksymalna przepływność wynosi 10 000 jednostek żądań na sekundę. Aby utworzyć kontener nieograniczone należy określić minimalnej przepustowości 1 000 RU/s i [klucza partycji](partition-data.md). Ponieważ danych może być konieczne można podzielić na wiele partycji, jest konieczne pobranie klucz partycji, który ma dużej kardynalności (od 100 do milionów unikatowe wartości). Wybierając klucza partycji z wielu różnych wartości upewnieniu się, że żądania i wykres kontenera/tabeli mogą być skalowane jednolicie Azure DB rozwiązania Cosmos. 

> [!NOTE]
> Klucz partycji to logiczne granic, a nie jeden fizyczny. W związku z tym nie należy ograniczyć liczbę wartości klucza partycji distinct. W rzeczywistości jest lepiej użyć więcej różne wartości klucza partycji niż mniej, jako bazy danych rozwiązania Cosmos Azure ma więcej opcje równoważenia obciążenia.

Oto fragment kodu dotyczący tworzenia kontenera o 3000 jednostek żądania na drugi przy użyciu zestawu .NET SDK:

```csharp
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000 });
```

Azure DB rozwiązania Cosmos działa modelu rezerwacji przepływności. Oznacza to, że są rozliczane ilości przepływności *zastrzeżone*, niezależnie od tego, jaka część tego przepływności jest aktywnie *używane*. Aplikacją na obciążenia, danych i użycia zmiany wzorce, można łatwo skalowania ilość zastrzeżone RUs za pomocą zestawów SDK lub przy użyciu [Azure Portal](https://portal.azure.com).

Każdy kontener jest mapowany na `Offer` zasobów w usłudze Azure DB rozwiązania Cosmos mającej metadane dotyczące udostępnionej przepływności. Możesz zmienić przydzielone przepływności wyszukiwania odpowiadający jej zasób oferta dla kontenera, a następnie zaktualizowaniem go przy użyciu nowej wartości przepływności. Oto fragment kodu do zmiany przepływności kontenera do 5000 jednostek żądania na drugi przy użyciu zestawu .NET SDK:

```csharp
// Fetch the resource to be updated
Offer offer = client.CreateOfferQuery()
                .Where(r => r.ResourceLink == collection.SelfLink)    
                .AsEnumerable()
                .SingleOrDefault();

// Set the throughput to 5000 request units per second
offer = new OfferV2(offer, 5000);

// Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

Jeśli zmienisz przepływność jest bez zakłócania dostępności z kontenera. Zazwyczaj nowe zarezerwowaną przepływnością obowiązuje w ciągu kilku sekund na stosowanie nowych przepływności.

## <a name="throughput-isolation-in-globally-distributed-databases"></a>Izolacja przepływności w rozproszonych globalnie baz danych

W przypadku bazy danych zostały zreplikowane do więcej niż jeden region, bazy danych Azure rozwiązania Cosmos zapewnia izolację przepływności, aby upewnić się, że użycie RU w jednym regionie nie ma wpływu na RU użycia w innym regionie. Na przykład jeśli zapisu danych do jednego regionu i odczytywanie danych z innego regionu, RUs, używany do wykonywania operacji zapisu w regionie, A nie przyjmują przeciwną RUs używane dla operacji odczytu w regionie, w którym B. RUs nie są dzielone w regionach, w których została wdrożona. Każdego regionu, w której są replikowane bazy danych ma pełne ilość RUs udostępnione. Aby uzyskać więcej informacji na temat globalnej replikacji, zobacz [sposobu dystrybucji danych globalnie z bazy danych Azure rozwiązania Cosmos](distribute-data-globally.md).

## <a name="request-unit-considerations"></a>Zagadnienia dotyczące jednostki żądania
Szacowanie liczby jednostek żądania do zarezerwowania dla Twojej bazy danych Azure rozwiązania Cosmos kontenera, należy wziąć pod uwagę następujące zmienne:

* **Rozmiar elementu**. Jak jednostki używane do odczytu lub zapisu danych zwiększa się również rozmiarze.
* **Liczba właściwości elementu**. Zakładając, że domyślna indeksowania wszystkich właściwości, jednostki używane do zapisania wzrost dokumentu na węzeł/jednostkę wraz ze wzrostem liczby właściwości.
* **Spójność danych**. Dodatkowe jednostki za pomocą poziomów spójności danych silne lub ograniczonych nieaktualności, są używane do odczytu elementów.
* **Właściwości indeksowanych**. Zasady indeksu na każdego kontenera określa właściwości, które są indeksowane domyślnie. Można ograniczyć zużycie jednostki Twoje żądanie, przez ograniczenie liczby właściwości indeksowanych lub włączenie indeksowanie z opóźnieniem.
* **Indeksowanie dokumentów**. Domyślnie każdy element jest indeksowany automatycznie. Jeśli wybierzesz nie może zindeksować niektórych elementów można używać mniejszej liczby jednostek żądania.
* **Zapytanie wzorce**. Złożoność kwerendy wpływa na liczbę jednostek żądania są używane dla operacji. Liczba predykatów, rodzaj predykaty, projekcje liczbę funkcji UDF i rozmiaru zestawu danych źródła wszystkich wpływ kosztów operacji zapytania.
* **Użycie skryptu**.  Podobnie jak w przypadku zapytań, procedury składowane i wyzwalaczy wykorzystywać jednostki żądania, zależnie od stopnia złożoności wykonywania operacji. Podczas opracowywania aplikacji, sprawdź, czy nagłówek opłat żądania, aby lepiej zrozumieć, jak każdej operacji zajmuje pojemność jednostki żądania.

## <a name="estimating-throughput-needs"></a>Planowania potrzeb w zakresie przepustowości
Jednostka żądania jest znormalizowane miara kosztu przetwarzania żądania. Jednostka pojedyncze żądanie reprezentuje możliwości przetwarzania wymagane do odczytu (za pośrednictwem łączy własnych lub identyfikator) pojedynczego 1 KB elementu składające się z 10 unikatowe wartości (z wyjątkiem właściwości systemu). Żądanie utworzenia (Wstaw), Zamień lub Usuń ten sam element będą korzystać z dodatkowych zadań przetwarzania przez usługę i tym samym więcej jednostek żądania.   

> [!NOTE]
> Linia bazowa jednostka żądania 1 dla elementu 1 KB odpowiada proste GET przez łącze własne lub identyfikator elementu.
> 
> 

Na przykład, w tym miejscu jest tabelę, która zawiera liczbę jednostek żądania udzielenia na trzy rozmiary innego elementu (1 KB, 4 KB do 64 KB) i na dwa różne poziomy wydajności (odczyty 500 na sekundę 100 zapisy na sekundę i 500 odczyty/sekundę + 500 zapisy na sekundę). Spójność danych skonfigurowano na sesji, a zasady indeksowania został ustawiony na None.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Rozmiar elementu</strong></p></td>
            <td valign="top"><p><strong>Odczyty/sekundę</strong></p></td>
            <td valign="top"><p><strong>Zapisy na sekundę</strong></p></td>
            <td valign="top"><p><strong>Jednostki żądania</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1) + (100 * 5) = 1 000 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 1) + (500 * 5) = 3 000 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1,3) + (100 * 7) = 1,350 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 1,3) + (500 * 7) = 4,150 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 10) + (100 * 48) = 9,800 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 10) + (500 * 48) = 29,000 RU/s</p></td>
        </tr>
    </tbody>
</table>

### <a name="use-the-request-unit-calculator"></a>Użycie kalkulatora jednostki żądania
Aby ułatwić klientom dostosowywać ich ocen przepływności, brak opartą na sieci web [Kalkulator jednostki żądania](https://www.documentdb.com/capacityplanner) aby oszacować wymagania dotyczące jednostki żądania dla operacji typowych, w tym:

* Element tworzy (zapisy)
* Odczytuje element
* Usuwa element
* Element aktualizacji

Narzędzie obejmuje również obsługę Szacowanie oparte na elementach próbki, podane wymagania dotyczące przechowywania danych.

Za pomocą narzędzia jest prosty:

1. Należy przekazać co najmniej jeden element reprezentatywny.
   
    ![Przekaż elementów do Kalkulatora jednostki żądania][2]
2. Aby oszacować wymagania dotyczące magazynu danych, wprowadź całkowitą liczbę elementów, które chcesz przechowywać.
3. Wprowadź liczbę elementów, które tworzenia, odczytu, aktualizacji i usuwania działań, które wymagają (na podstawie na sekundę). Aby oszacować opłat jednostki żądania operacji aktualizowania elementu, Przekaż kopię elementu próbki z kroku 1 zawiera pole typowe aktualizacje.  Na przykład aktualizacji elementu zmodyfikowania zwykle dwie właściwości o nazwie lastLogin i userVisits, następnie po prostu skopiuj element przykładowych, zaktualizuj wartości dla tych dwóch właściwości i przekaż skopiowany element.
   
    ![Wprowadź wymagania dotyczące przepływności w Kalkulator jednostki żądania][3]
4. Kliknij przycisk Oblicz i przejrzeć wyniki.
   
    ![Wyniki Kalkulator jednostki żądania][4]

> [!NOTE]
> Jeśli masz typów elementów, które różnią się znacznie pod względem rozmiaru i liczby właściwości indeksowanych, Przekaż przykładowe każdego *typu* z typowych elementu do narzędzia i obliczyć wyniki.
> 
> 

### <a name="use-the-azure-cosmos-db-request-charge-response-header"></a>Użyj nagłówka odpowiedzi opłat żądania bazy danych Azure rozwiązania Cosmos
Każdy odpowiedź z usługi Azure DB rozwiązania Cosmos zawiera niestandardowy nagłówek (`x-ms-request-charge`) zawiera jednostki żądania używane dla żądania. Ten nagłówek jest również dostępny za pośrednictwem Azure DB rozwiązania Cosmos z zestawów SDK. W zestawie SDK .NET RequestCharge jest właściwością obiektu ResourceResponse.  Dla zapytań Eksploratora danych Azure DB rozwiązania Cosmos w portalu Azure informacje żądania opłaty dotyczące wykonywane zapytania.

Pamiętając o tym jednej metody w oszacowania zarezerwowaną przepływnością wymagane przez aplikację jest rejestrowanie skojarzone z systemem typowymi operacjami względem elementu reprezentatywny używanych przez aplikację, a następnie Szacowanie opłata jednostki żądania Liczba operacji przewidujesz wykonywania każdej sekundy.  Pamiętaj mierzyć i obejmują typowe zapytania i również użycie skryptu bazy danych Azure rozwiązania Cosmos.

> [!NOTE]
> Jeśli masz typów elementów, które różnią się znacznie pod względem rozmiaru i liczby właściwości indeksowanych rejestrowania opłata jednostki żądanie dotyczy operacji związanych z każdym *typu* typowe elementu.
> 
> 

Na przykład:

1. Zarejestruj opłata jednostki żądania tworzenia (Wstawianie) typowe elementu. 
2. Rekord opłata jednostki żądanie odczytu typowe elementu.
3. Rekord opłata jednostki żądania aktualizowania typowych elementu.
4. Rekord opłata jednostki żądania elementu typowe, typowe zapytań.
5. Zarejestruj opłata jednostki żądania żadnych niestandardowych skryptów (procedury składowane, wyzwalacze, funkcje zdefiniowane przez użytkownika) wykorzystywana przez aplikację
6. Oblicz jednostki żądania wymagane podane szacowaną liczbę operacji, które planujesz do uruchomienia w ciągu sekundy.

## <a id="GetLastRequestStatistics"></a>Za pomocą interfejsu API dla bazy danych MongoDB w GetLastRequestStatistics polecenia
Interfejs API bazy danych MongoDB obsługuje polecenia niestandardowych, *getLastRequestStatistics*, pobierania opłat żądania dla określonej operacji.

Na przykład w powłokę Mongo, należy wykonać chcesz zweryfikować opłata żądania dla operacji.
```
> db.sample.find()
```

Następnie wykonaj polecenie *getLastRequestStatistics*.
```
> db.runCommand({getLastRequestStatistics: 1})
{
    "_t": "GetRequestStatisticsResponse",
    "ok": 1,
    "CommandName": "OP_QUERY",
    "RequestCharge": 2.48,
    "RequestDurationInMilliSeconds" : 4.0048
}
```

Pamiętając o tym jednej metody w oszacowania zarezerwowaną przepływnością wymagane przez aplikację jest rejestrowanie skojarzone z systemem typowymi operacjami względem elementu reprezentatywny używanych przez aplikację, a następnie Szacowanie opłata jednostki żądania Liczba operacji przewidujesz wykonywania każdej sekundy.

> [!NOTE]
> Jeśli masz typów elementów, które różnią się znacznie pod względem rozmiaru i liczby właściwości indeksowanych rejestrowania opłata jednostki żądanie dotyczy operacji związanych z każdym *typu* typowe elementu.
> 
> 

## <a name="use-mongodb-api-portal-metrics"></a>Użyj portalu metryki interfejsu API bazy danych MongoDB
Najprostszym sposobem, aby uzyskać dobrą szacowania żądania opłat jednostki bazy danych MongoDB interfejsu API jest użycie [portalu Azure](https://portal.azure.com) metryki. Z *liczba żądań* i *opłat żądania* wykresy, możesz uzyskać oszacowanie liczbę jednostek żądania, każdy zajmuje operacji i liczbę jednostek żądania zużywają względem siebie.

![Metryki portalu API bazy danych MongoDB][6]

## <a name="a-request-unit-estimation-example"></a>W przykładzie szacowania jednostki żądania
Należy wziąć pod uwagę następujące dokumentu ~ 1 KB:

```json
{
 "id": "08259",
  "description": "Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX",
  "tags": [
    {
      "name": "cereals ready-to-eat"
    },
    {
      "name": "kellogg"
    },
    {
      "name": "kellogg's crispix"
    }
  ],
  "version": 1,
  "commonName": "Includes USDA Commodity B855",
  "manufacturerName": "Kellogg, Co.",
  "isFromSurvey": false,
  "foodGroup": "Breakfast Cereals",
  "nutrients": [
    {
      "id": "262",
      "description": "Caffeine",
      "nutritionValue": 0,
      "units": "mg"
    },
    {
      "id": "307",
      "description": "Sodium, Na",
      "nutritionValue": 611,
      "units": "mg"
    },
    {
      "id": "309",
      "description": "Zinc, Zn",
      "nutritionValue": 5.2,
      "units": "mg"
    }
  ],
  "servings": [
    {
      "amount": 1,
      "description": "cup (1 NLEA serving)",
      "weightInGrams": 29
    }
  ]
}
```

> [!NOTE]
> Dokumenty są zminimalizowany w usłudze Azure DB rozwiązania Cosmos, więc system obliczeniowe rozmiar dokumentu powyżej jest nieco mniej niż 1 KB.
> 
> 

W poniższej tabeli przedstawiono przybliżone żądania jednostki związanych z typowymi operacjami na tym elemencie (opłata jednostki żądania przybliżonej przy założeniu, że poziomu spójności konta jest ustawiona na "Sesja" i że wszystkie elementy są automatycznie indeksowane):

| Operacja | Opłata jednostki żądania |
| --- | --- |
| Utwórz element |~15 RU |
| Odczytu elementu |~1 RU |
| Element zapytania według identyfikatora |~2.5 RU |

Ponadto w poniższej tabeli zamieszczono przybliżonej żądania jednostki opłat za typowe zapytania używane w aplikacji:

| Zapytanie | Opłata jednostki żądania | Liczba zwróconych elementów |
| --- | --- | --- |
| Wybierz żywności według identyfikatora |~2.5 RU |1 |
| Wybierz żywności według producenta |~7 RU |7 |
| Wybierz grupy żywności i kolejność według wagi |~70 RU |100 |
| Zaznacz górny żywności 10 w grupie żywności |~10 RU |10 |

> [!NOTE]
> Opłat RU się różnić w zależności od liczby elementów zwróconych.
> 
> 

Dzięki tym informacjom można oszacować wymagania dotyczące RU dla tej aplikacji liczby operacji i zapytań oczekiwać na sekundę:

| Operacja/zapytania | Szacowaną liczbę na sekundę | Wymagane RUs |
| --- | --- | --- |
| Utwórz element |10 |150 |
| Odczytu elementu |100 |100 |
| Wybierz żywności według producenta |25 |175 |
| Wybierz grupy żywności |10 |700 |
| Wybierz 10 pierwszych |15 |Łącznie 150 |

W takim przypadku spodziewasz się wymóg średniej przepływności 1,275 RU/s.  Zaokrąglenie do najbliższej 100, może udostępnić 1300 RU/s dla tej aplikacji kontenera.

## <a id="RequestRateTooLarge"></a> Przekraczanie limitów zarezerwowaną przepływnością w usłudze Azure DB rozwiązania Cosmos
Odwołaj, że zużycie jednostka żądania jest oceniana jako szybkość na sekundę, jeśli budżetu jest pusta. Dla aplikacji, które przekroczyć współczynnika jednostki żądania elastycznie kontenera żądania do tego kontenera są ograniczane dopóki częstotliwość spadnie poniżej poziomu zastrzeżone. W przypadku ograniczania serwera preemptively kończy żądanie z RequestRateTooLargeException (kod stanu HTTP 429) i zwraca wartość wskazującą czas (w milisekundach), które użytkownik musi czekać przed ponowną próbą wykonania nagłówek x-ms ponawiania — po ms żądanie.

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Jeśli używasz zestawu SDK klienta usługi .NET i LINQ zapytania, a następnie w większości przypadków, nie trzeba uwzględniać tego wyjątku, zgodnie z bieżącą wersją programu .NET SDK klienta niejawnie przechwytuje tej odpowiedzi szanuje określony serwer ponownych prób po nagłówka i ponawia żądanie. Następna ponowna próba powiedzie się, chyba że Twoje konto jest uzyskiwany jednocześnie przez wielu klientów.

Jeśli masz więcej niż jednego klienta zbiorczo operacyjnego powyżej liczby żądań domyślne zachowanie ponownych prób nie mogą być niewystarczające, a klient zgłosi DocumentClientException z kodem stanu 429 do aplikacji. W przypadkach, takich jak ta można rozważyć Obsługa zachowanie ponownych prób i logikę w aplikacji Błąd procedury obsługi lub zwiększenie zarezerwowaną przepływnością kontenera.

## <a id="RequestRateTooLargeAPIforMongoDB"></a> Przekraczanie limitów zarezerwowaną przepływnością w interfejsie API bazy danych MongoDB
Aplikacje, które przekracza jednostek żądania elastycznie kontenera będzie ograniczony, dopóki częstotliwość spadnie poniżej poziomu zastrzeżone. W przypadku przepustnicy wewnętrznej bazy danych preemptively zakończy się żądanie z *16500* kod błędu: - *zbyt wiele żądań*. Domyślnie interfejsu API bazy danych MongoDB ma automatycznie ponawiać próbę maksymalnie 10 razy przed zwróceniem *zbyt wiele żądań* kod błędu. W przypadku otrzymania wiele *zbyt wiele żądań* kody błędów, można rozważyć albo dodanie zachowanie ponownych prób w aplikacji Błąd procedury obsługi lub [zwiększenie zarezerwowaną przepływnością kontenera](set-throughput.md).

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat zarezerwowaną przepływnością z bazami danych bazy danych Azure rozwiązania Cosmos, zapoznaj się z tymi zasobami:

* [Cennik platformy Azure DB rozwiązania Cosmos](https://azure.microsoft.com/pricing/details/cosmos-db/)
* [Partycjonowanie danych w usłudze Azure DB rozwiązania Cosmos](partition-data.md)

Aby dowiedzieć się więcej na temat bazy danych rozwiązania Cosmos Azure, zobacz Azure DB rozwiązania Cosmos [dokumentacji](https://azure.microsoft.com/documentation/services/cosmos-db/). 

Aby rozpocząć testowanie z bazy danych Azure rozwiązania Cosmos wydajności i skalowania, zobacz [wydajności i skalowania testowania z bazy danych Azure rozwiązania Cosmos](performance-testing.md).

[2]: ./media/request-units/RUEstimatorUpload.png
[3]: ./media/request-units/RUEstimatorDocuments.png
[4]: ./media/request-units/RUEstimatorResults.png
[5]: ./media/request-units/RUCalculator2.png
[6]: ./media/request-units/api-for-mongodb-metrics.png
