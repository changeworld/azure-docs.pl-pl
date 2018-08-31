---
title: Jednostki żądania i Szacowanie przepływności — Azure Cosmos DB | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o tym, jak zrozumieć, określ i Szacowanie wymagań jednostki żądań w usłudze Azure Cosmos DB.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: rimman
ms.openlocfilehash: 66beeb2cc724f75d17a4c155f1cdb888153e8fbf
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2018
ms.locfileid: "43286769"
---
# <a name="request-units-in-azure-cosmos-db"></a>Jednostki żądań w usłudze Azure Cosmos DB

[Usługa Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) to globalnie rozproszona wielomodelowa baza danych Microsoft. Usługa Azure Cosmos DB nie trzeba wynajmować maszyn wirtualnych, wdrażania oprogramowania lub monitorowanie baz danych. Usługa Azure Cosmos DB jest obsługiwane i są stale monitorowane przez najlepszych inżynierów firmy Microsoft do świadczenia światowej klasy dostępności, wydajności i ochrony danych. Uzyskiwać dostęp do danych za pomocą interfejsów API w wybranym, takie jak [SQL](documentdb-introduction.md), [bazy danych MongoDB](mongodb-introduction.md), i [tabeli](table-introduction.md) interfejsów API i grafu przy użyciu [interfejs API Gremlin](graph-introduction.md). Wszystkie interfejsy API wszystkie obsługiwane. 

Waluta usługi Azure Cosmos DB jest *jednostki żądań (RU)*. Przy użyciu jednostek żądania nie należy zarezerwować możliwości odczytu/zapisu lub aprowizacja procesora CPU, pamięci i operacji We/Wy. Usługa Azure Cosmos DB obsługuje różne interfejsy API, które mają różne operacje, począwszy od prostego odczytuje i zapisuje wykres złożonych zapytań. Ponieważ nie wszystkie żądania są równe, żądania są przypisywane znormalizowane liczbę jednostek żądania na podstawie ilości wymaganych do obsłużenia żądania obliczeń. Liczbę jednostek żądania dla operacji jest deterministyczna. Można śledzić liczbę jednostek żądania, które są używane przez jakakolwiek operacja w usłudze Azure Cosmos DB przy użyciu nagłówka odpowiedzi. 

Zapewnienie przewidywalnej wydajności, należy zarezerwować przepływność w jednostkach 100 RU/s. Możesz [oszacować przepływność musi](request-units.md#estimating-throughput-needs) przy użyciu usługi Azure Cosmos DB [kalkulatora jednostek żądania](https://www.documentdb.com/capacityplanner).

![Kalkulator przepływności][5]

Po przeczytaniu tego artykułu, będziesz mieć możliwość odpowiedzieć na następujące pytania:

* Co to są jednostki żądania i opłaty żądanie w usłudze Azure Cosmos DB?
* Jak określić żądanie jednostek pojemności dla kontenera lub zestaw kontenerów w usłudze Azure Cosmos DB?
* Jak oszacować musi jednostki żądań w mojej aplikacji?
* Co się stanie, jeśli przekroczę żądania jednostek pojemności dla kontenera lub zestaw kontenerów w usłudze Azure Cosmos DB?

Ponieważ usługi Azure Cosmos DB to wielomodelowa baza danych, to należy pamiętać, że ten artykuł ma zastosowanie do wszystkich modeli danych i interfejsów API w usłudze Azure Cosmos DB. W tym artykule używany ogólny terminy, takie jak *kontenera* do odwoływania się ogólną do kolekcji lub wykres i *elementu* do odwoływania się ogólną do tabeli, dokumentów, węzeł lub jednostki.

## <a name="request-units-and-request-charges"></a>Jednostki żądania i żądania opłaty

Usługa Azure Cosmos DB szybkie, oferuje przewidywalną wydajność, rezerwując zasobów w celu zaspokojenia potrzeb przepływność aplikacji. Zmień aplikacji wzorców obciążenia i dostępu wraz z upływem czasu. Usługa Azure Cosmos DB może pomóc łatwo zwiększyć lub zmniejszyć ilość zarezerwowaną przepływnością, dostępne dla aplikacji.

Za pomocą usługi Azure Cosmos DB zarezerwowaną przepływność jest określane w przeliczeniu na jednostce żądania przetwarzania na sekundę. Jednostki żądania można traktować jako walutę przepływności. Możesz zarezerwować liczbę jednostek żądania gwarantowane mają być dostępne dla aplikacji na podstawie na sekundę. Każda operacja w usłudze Azure Cosmos DB, zapisywania dokumentu, w tym wysyłania zapytań i zaktualizowanie dokumentu, zużywa procesora CPU, pamięci i operacji We/Wy. Oznacza to każdej operacji jest naliczana opłata za żądanie wyrażana, który jest wyrażona w jednostkach żądania. Gdy już poznasz czynników wpływających na opłat za jednostki żądań i wymagań aplikacji w zakresie przepływności, można uruchomić aplikacji jako koszt skutecznie, jak to możliwe. 

## <a name="throughput-isolation-in-globally-distributed-databases"></a>Izolacja przepływność w globalnie rozproszonych baz danych

Jeśli baza danych jest replikowana do więcej niż jeden region, Azure Cosmos DB zapewnia izolację przepływności, aby upewnić się, że użycie jednostek żądań w jednym regionie nie ma wpływu na użycie jednostek żądań w innym regionie. Na przykład, jeśli zapisu danych do jednego regionu i odczytywać dane z innego regionu, jednostek żądania, które są używane do wykonywania operacji zapisu w regionie, A nie wykona od jednostek żądania, które są używane dla operacji odczytu w regionie żądania B. jednostki nie są podziału akro ss regionów, w których wdrożono bazę danych. Każdy region, w którym baza danych jest replikowany ma pełnej liczby jednostek żądania aprowizacji. Aby uzyskać więcej informacji o replikacji globalnej, zobacz [jak dystrybuować dane globalnie za pomocą usługi Azure Cosmos DB](distribute-data-globally.md).

## <a name="request-unit-considerations"></a>Zagadnienia dotyczące jednostki żądania
Podczas szacowania liczby jednostek żądania dla aprowizacji funkcji jest ważne, należy wziąć pod uwagę następujące zmienne:

* **Rozmiar elementu**. W miarę zwiększania rozmiaru zwiększa się również liczbę wykorzystane do odczytu lub zapisu danych jednostki żądania
* **Element właściwości liczba**. Zakładając, że domyślna indeksowanie wszystkich właściwości, wykorzystane do zapisania wzrost dokumentu, węzeł lub jednostki wraz ze wzrostem liczby właściwości jednostki.
* **Wyjaśnienie pojęcia spójności danych**. Gdy używasz modeli spójności danych, takich jak silne lub powiązana nieaktualność, dodatkowych jednostek żądań są używane do odczytu tych elementów.
* **Właściwości indeksowanych**. Zasady indeks na każdy kontener określa właściwości, które są indeksowane domyślnie. Można zmniejszyć swoje użycie jednostek żądania dla operacji zapisu, przez ograniczenie liczby właściwości indeksowanych lub przez włączenie indeksowania z opóźnieniem.
* **Indeksowanie dokumentów**. Domyślnie każdy element jest automatycznie indeksowane. Wykorzystasz mniej jednostek żądania, jeśli zdecydujesz się nie zindeksować niektórych elementów.
* **Zapytanie wzorców**. Złożoność zapytania ma wpływ na liczbę jednostek żądania są używane dla operacji. Liczba wyników zapytania, liczba predykatów, rodzaj predykaty, liczba funkcji zdefiniowanych przez użytkownika, rozmiar źródła danych i projekcji, wszystkie mają wpływ na koszt operacje zapytań.
* **Skrypt użycia**. Podobnie jak w przypadku zapytań, procedur składowanych i wyzwalaczy zużywać jednostki żądania na podstawie stopnia złożoności wykonywanych operacji. Podczas opracowywania aplikacji, sprawdź, czy nagłówek żądania opłat, aby lepiej zrozumieć, jak każda operacja wykorzystuje pojemność jednostki żądania.

## <a name="estimating-throughput-needs"></a>Planowania związanym z przepływnością
Jednostka żądania jest miarą znormalizowane koszty przetwarzania żądania. Jednostka pojedyncze żądanie reprezentuje możliwości przetwarzania, które są wymagane do odczytu (za pośrednictwem łączy własnych lub identyfikator) pojedynczy element 1 KB, który składa się z 10 unikatowych wartości (z wyjątkiem właściwości systemu). Żądanie utworzenia (Wstaw), Zamień lub usuń takie same elementu wykorzystuje przetwarzanie większej ilości danych z usługi i tym samym wymaga większej liczby jednostek żądania. 

> [!NOTE]
> Plan bazowy jednostki 1 żądanie dla elementu 1 KB odnosi się do prostych GET przez łącze własne lub identyfikator elementu.
> 
> 

Na przykład Oto tabelę, która pokazuje liczbę jednostek żądania do aprowizowania dla elementów z trzech różnych rozmiarach (1 KB, 4 KB i 64 KB) i dwa różne poziomy wydajności (500 operacji odczytu na sekundę + 100 operacje zapisu/s i 500 operacji odczytu na sekundę + 500 operacje zapisu na sekundę). W tym przykładzie spójność danych jest ustawiona na **sesji**, a zasady indeksowania jest ustawiona na **Brak**.

| Rozmiar elementu | Odczyty/s | Operacje zapisu na sekundę | Jednostki żądania
| --- | --- | --- | --- |
| 1 KB | 500 | 100 | (500 * 1) + (100 * 5) = 1000 jednostek RU/s
| 1 KB | 500 | 500 | (500 * 1) + (500 * 5) = 3000 jednostek RU/s
| 4 KB | 500 | 100 | (500 * 1.3) + (100 * 7) = równy 1350 jednostek RU/s
| 4 KB | 500 | 500 | (500 * 1.3) + (500 * 7) = 4,150 jednostek RU/s
| 64 KB | 500 | 100 | (500 * 10) + (100 * 48) = 9,800 jednostek RU/s
| 64 KB | 500 | 500 | (500 * 10) + (500 * 48) = 29,000 jednostek RU/s


### <a name="use-the-request-unit-calculator"></a>Skorzystaj z Kalkulatora jednostek żądania
Aby dostosować swoje szacunki przepływności, można użyć opartą na sieci web [kalkulatora jednostek żądania](https://www.documentdb.com/capacityplanner). Kalkulator mogą pomóc szacowania wymagań jednostki żądania dla typowych operacji, w tym:

* Element tworzy (zapisuje)
* Odczytuje element
* Usuwa element
* Element aktualizacji

Narzędzie obejmuje również obsługę dla szacowania, oparte na elementach próbki, które podasz wymagania dotyczące przechowywania danych.

Aby użyć narzędzia:

1. Przekaż jeden lub więcej elementów reprezentatywny (na przykład dokument JSON przykładowy).
   
    ![Przekazywanie elementów do Kalkulatora jednostek żądania][2]
2. Aby oszacować wymagania dotyczące magazynu danych, wprowadź całkowitą liczbę elementów (na przykład dokumenty, wiersze lub wierzchołki), które powinny być przechowywane.
3. Wprowadź liczbę tworzenia, odczytu, aktualizacji i operacje usuwania, które wymagane (na podstawie na sekundę). Oszacowanie opłat za jednostki żądania operacji aktualizacji elementu, należy przekazać kopię elementu próbki z kroku 1, która zawiera aktualizacje typowego pola. Na przykład, jeśli element aktualizacje zwykle zmodyfikować dwie właściwości o nazwie *lastLogin* i *userVisits*, skopiuj element przykładowych, zaktualizuj wartości dla tych dwóch właściwości, a następnie przekaż skopiowany element.
   
    ![Wprowadź wymagań w zakresie przepływności w kalkulatorze jednostki żądania][3]
4. Wybierz **Calculate**, a następnie sprawdź wyniki.
   
    ![Wyniki kalkulatora jednostek żądań][4]

> [!NOTE]
> W przypadku typów elementów, które różnią się znacząco pod względem rozmiaru i liczby właściwości indeksowanych Przekaż przykład każdego *typu* dla typowego elementu do narzędzia, a następnie obliczyć wyniki.
> 
> 

### <a name="use-the-azure-cosmos-db-request-charge-response-header"></a>Nagłówek odpowiedzi usługi Azure Cosmos DB żądania opłata za użycie
Każdej odpowiedzi z usługi Azure Cosmos DB zawiera niestandardowy nagłówek (`x-ms-request-charge`) zawierający jednostek żądania, używane dla danego żądania. Można również uzyskać dostęp tego pliku nagłówkowego, za pomocą zestawów SDK usługi Azure Cosmos DB. W zestawie SDK platformy .NET **RequestCharge** jest właściwością **ResourceResponse** obiektu. Dla zapytań Eksplorator danych usługi Azure Cosmos DB w witrynie Azure portal informacje żądania opłaty dotyczące wykonywanych zapytań. Informacje na temat sposobu uzyskania i ustawianie przepływności za pomocą różnych interfejsów API wielu modeli, zobacz [ustaw i uzyskiwanie informacji o przepływności w usłudze Azure Cosmos DB](set-throughput.md) artykułu.

Jedną z metod do oszacowania ilości zarezerwowanej przepływności wymaganej przez aplikację jest do rejestrowania żądań opłat za jednostkę, związanych z uruchamianiem typowych operacji względem elementu językiem, który jest używany przez aplikację. Następnie należy oszacować liczbę operacji, które planujesz wykonać co sekundę. Należy także zmierzyć i obejmują typowe zapytania i użycie skryptów usługi Azure Cosmos DB.

> [!NOTE]
> W przypadku typów elementów, które różnią się znacząco pod względem rozmiaru i liczby właściwości indeksowanych rejestrowania odpowiednich operacji żądania opłat za jednostkę związany z każdą *typu* dla typowego elementu o wielkości.
> 
> 

Na przykład Oto kroki, które można wykonywać:

1. Zarejestruj opłat za jednostkę żądania tworzenia (Wstawianie) typowego elementu o wielkości. 
2. Rekord opłat za jednostkę żądania odczytu typowego elementu o wielkości.
3. Rekord opłat za jednostkę żądanie aktualizowania typowego elementu o wielkości.
4. Rekord opłat za jednostkę żądań zapytań o elementy typowej, wspólnego.
5. Zapisz żądanie opłata za jednostki niestandardowe skrypty (procedury składowane, wyzwalacze, funkcje zdefiniowane przez użytkownika), których używa aplikacja.
6. Oblicz jednostek żądania wymagany podane szacowana liczba operacji, które planujesz uruchamianie co sekundę.

## <a name="a-request-unit-estimate-example"></a>Przykład oszacowanie jednostki żądania
Należy wziąć pod uwagę następujący dokument, czyli około 1 KB, rozmiar:

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
> Dokumenty są zminimalizowany w usłudze Azure Cosmos DB, dzięki czemu rozmiar obliczone przez system na powyższym dokumencie jest nieco mniej niż 1 KB.
> 
> 

W poniższej tabeli przedstawiono opłat za jednostki żądań przybliżony dla typowych operacji na tym elemencie. (Opłata za jednostkę żądania przybliżony przyjęto założenie, że poziom spójności konta jest ustawiona na **sesji** i że wszystkie elementy są automatycznie indeksowane.)

| Operacja | Opłata za jednostki żądań |
| --- | --- |
| Utwórz element |~15 RU |
| Odczyt elementu |~1 RU |
| Element zapytania według Identyfikatora |~2.5 RU |

W poniższej tabeli przedstawiono przybliżone żądania opłat za jednostki dla typowych zapytań używanych w aplikacji:

| Zapytanie | Opłata za jednostki żądań | Liczba elementów zwróconych |
| --- | --- | --- |
| Wybierz żywności według Identyfikatora |~2.5 RU |1 |
| Wybierz foods według producenta |~7 RU |7 |
| Wybierz grupy żywności i kolejność według wagi |~70 RU |100 |
| Zaznacz górny żywności 10 w grupie żywności |~10 RU |10 |

> [!NOTE]
> Opłaty za jednostki żądań różnią się zależnie od liczby elementów zwracanych.
> 
> 

Dzięki tym informacjom można oszacować wymagania jednostek żądania dla tej aplikacji daną liczbę operacje i zapytania, oczekiwany na sekundę:

| Operacja/zapytania | Szacowana liczba na sekundę | Jednostki żądań wymagane |
| --- | --- | --- |
| Utwórz element |10 |150 |
| Odczyt elementu |100 |100 |
| Wybierz foods według producenta |25 |175 |
| Wybierz grupy żywności |10 |700 |
| Wybierz 10 najważniejszych |15 |Łącznie 150 |

W tym przypadku oczekujesz, że wymóg średniej przepływności 1,275 RU/s. Zaokrąglanie do najbliższych 100, czy aprowizować 1,300 RU/s dla tej aplikacji kontenera (lub zestaw kontenerów).

## <a id="RequestRateTooLarge"></a> Przekraczanie limitów zarezerwowanej przepływności w usłudze Azure Cosmos DB
Użycie jednostek żądania jest oceniany na stawki na sekundę. W przypadku aplikacji, które przekraczają liczba jednostek żądań elastycznie żądania są ograniczone szybkość, aż współczynnik nie spadnie poniżej poziomu aprowizowanej przepływności. Gdy żądanie jest ograniczone szybkość, serwer prewencyjnego kończy żądanie z `RequestRateTooLargeException` (kod stanu HTTP 429) i zwraca `x-ms-retry-after-ms` nagłówka. Nagłówek wskazuje ilość czasu w milisekundach, które użytkownik musi czekać przed ponowieniem próby wykonania żądania.

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Korzystając z zestawu SDK klienta usługi .NET i LINQ zapytania, w większości przypadków, nigdy nie masz do czynienia z tym wyjątkiem. Bieżąca wersja zestawu SDK klienta .NET niejawnie przechwytuje tej odpowiedzi uwzględnia określony serwer nagłówka retry-after i automatycznie ponawiać próbę żądania. Chyba, że Twoje konto jest uzyskiwany współbieżnie przez wielu klientów, następne ponowienie próby zakończy się pomyślnie.

Jeśli masz więcej niż jeden klient łącznie operacyjnego powyżej żądań zakończonych, domyślne zachowanie ponawiania prób mogą być niewystarczające i zgłasza klienta `DocumentClientException` ze stanem kodu 429 do aplikacji. W takich sytuacjach warto wziąć pod uwagę sposób ponawiania i logikę w aplikacji, procedury obsługi błędów lub zwiększ przepływnością aprowizowaną dla kontenera (lub zestaw kontenerów).

## <a name="next-steps"></a>Kolejne kroki
 
- Dowiedz się, jak [ustaw i uzyskiwanie informacji o przepływności w usłudze Azure Cosmos DB](set-throughput.md) przy użyciu witryny Azure portal i zestawów SDK.
- Dowiedz się więcej o [wydajność i skalę, testowanie za pomocą usługi Azure Cosmos DB](performance-testing.md).
- Aby dowiedzieć się więcej na temat zarezerwowaną przepływność z bazami danych Azure Cosmos DB, zobacz [cennika usługi Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) i [partycjonowanie danych w usłudze Azure Cosmos DB](partition-data.md).
- Aby dowiedzieć się więcej na temat usługi Azure Cosmos DB, zobacz [dokumentacji usługi Azure Cosmos DB](https://azure.microsoft.com/documentation/services/cosmos-db/). 

[2]: ./media/request-units/RUEstimatorUpload.png
[3]: ./media/request-units/RUEstimatorDocuments.png
[4]: ./media/request-units/RUEstimatorResults.png
[5]: ./media/request-units/RUCalculator2.png

