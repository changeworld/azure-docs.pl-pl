---
title: Jednostek żądania i Szacowanie przepływności - DB rozwiązania Cosmos Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu zrozumieć, określ i oszacować wymagania dotyczące jednostki żądania w usłudze Azure DB rozwiązania Cosmos.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: rimman
ms.openlocfilehash: 160ff4e09f70036fd261c07fa59e13772bc00660
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37053331"
---
# <a name="request-units-in-azure-cosmos-db"></a>Żądanie jednostki w usłudze Azure DB rozwiązania Cosmos

[Azure DB rozwiązania Cosmos](https://azure.microsoft.com/services/cosmos-db/) jest Microsoft globalnie rozproszone multimodel bazy danych. Z bazy danych rozwiązania Cosmos platformy Azure nie trzeba wynajmować maszyn wirtualnych, wdrażania oprogramowania lub monitora bazy danych. Azure DB rozwiązania Cosmos jest obsługiwane i stale monitorowane przez górny pracownicy firmy Microsoft do dostarczania światowej klasy dostępność, wydajność i ochronę danych. Można uzyskać dostęp do danych za pomocą interfejsów API wybranych przez użytkownika, takie jak [SQL](documentdb-introduction.md), [MongoDB](mongodb-introduction.md), i [tabeli](table-introduction.md) interfejsów API i wykres za pośrednictwem [Gremlin API](graph-introduction.md). Obsługiwane są wszystkie natywnie wszystkich interfejsów API. 

Waluta bazy danych Azure rozwiązania Cosmos jest *jednostek żądania (RU)*. W przypadku żądania nie trzeba zarezerwować możliwości odczytu/zapisu lub udostępnić procesora CPU, pamięci i IOPS. Azure DB rozwiązania Cosmos obsługuje różne interfejsów API, które mają różne operacje, począwszy od prostego odczytuje i zapisuje wykres złożonych zapytań. Ponieważ nie wszystkie żądania są takie same, żądania są przypisywane znormalizowane liczby jednostek żądania na podstawie ilości obliczeń wymaganych do obsłużenia żądania. Liczba jednostek żądania dla operacji jest deterministyczna. Można śledzić liczbę jednostek żądania, które są używane przez żadnych operacji w usłudze Azure DB rozwiązania Cosmos za pośrednictwem nagłówka odpowiedzi. 

Zapewnienie przewidywalnej wydajności zarezerwować przepływności w jednostkach 100 RU/sekundę. Możesz [oszacować przepustowość sieci musi](request-units.md#estimating-throughput-needs) za pomocą usługi Azure DB rozwiązania Cosmos [Kalkulator jednostki żądania](https://www.documentdb.com/capacityplanner).

![Kalkulator przepływności][5]

Po przeczytaniu tego artykułu, będziesz mieć możliwość odpowiedzieć na następujące pytania:

* Jakie są jednostek żądania i żądania opłat w usłudze Azure DB rozwiązania Cosmos?
* Jak określić pojemność jednostki żądania dla kontenera lub zestawu kontenerów w usłudze Azure DB rozwiązania Cosmos?
* Jak oszacować musi jednostki żądania Moja aplikacja
* Co się stanie, jeśli I przekracza pojemność jednostki żądania dla kontenera lub zestawu kontenerów w usłudze Azure DB rozwiązania Cosmos?

Ponieważ bazy danych Azure rozwiązania Cosmos jest multimodel bazy danych, to należy pamiętać, że ten artykuł ma zastosowanie do wszystkich modeli danych i interfejsów API w usłudze Azure DB rozwiązania Cosmos. W tym artykule wykorzystano ogólnego warunków, takich jak *kontenera* objęty odnoszących się do kolekcji lub wykres i *elementu* objęty odnoszących się do tabeli, dokument, węzeł lub jednostki.

## <a name="request-units-and-request-charges"></a>Jednostek żądania i żądania opłat

Azure DB rozwiązania Cosmos szybkie, oferuje przewidywalną wydajność przez zarezerwowanie zasobów, aby zaspokoić potrzeby przepływność aplikacji. Aplikacja wzorce dostępu i obciążenia zmiany w czasie. Azure DB rozwiązania Cosmos może pomóc w prosty sposób zwiększyć lub zmniejszyć ilość zarezerwowaną przepływnością dostępne dla aplikacji.

Z bazy danych Azure rozwiązania Cosmos zarezerwowaną przepływnością jest określane w przeliczeniu na jednostki żądania przetwarzania na sekundę. Jednostki żądania można traktować jako walutę przepływności. Należy zarezerwować liczba jednostek żądania gwarantowane mają być dostępne dla aplikacji na podstawie na sekundę. Każdej operacji w usłudze Azure DB rozwiązania Cosmos, w tym zapisywania dokumentu, wykonywania zapytania i aktualizowanie dokumentu, korzysta z Procesora, pamięci i IOPS. Oznacza to, że każdej operacji wiąże bezpłatnie żądania w jednostkach żądań. Po zapoznaniu czynników, które mają wpływ na koszty jednostki żądania i wymagania dotyczące przepływności aplikacji, można uruchomić aplikacji jako koszt skutecznie, jak to możliwe. 

Aby pomóc Ci rozpocząć, Azure Menedżera programów DB rozwiązania Cosmos Andrew Liu omówiono jednostki żądania w poniższym klipie wideo (Brak pomocniczej Literówka w przykładzie jednostki wideo na żądanie. W przypadku 1 KB danych przy użyciu 100 000 rekordów całkowita ilość miejsca jest 100 MB, a nie 100 GB): <br /><br />

> [!VIDEO https://www.youtube.com/embed/stk5WSp5uX0]
> 
> 

## <a name="throughput-isolation-in-globally-distributed-databases"></a>Izolacja przepływności w rozproszonych globalnie baz danych

Jeśli baza danych jest replikowana do więcej niż jeden region, bazy danych Azure rozwiązania Cosmos zapewnia izolację przepływności, aby upewnić się, że obciążenie jednostki żądania w jednym regionie nie ma wpływu na żądanie jednostki użycia w innym regionie. Na przykład, jeśli zapisu danych do jednego regionu i odczytywanie danych z innego regionu, jednostki żądania, które są używane do wykonywania operacji zapisu w regionie, A nie wykonuj przeciwną jednostki żądania, które są używane dla operacji odczytu w regionie żądania B. jednostek nie są podzielić akro ss regionów, w których została wdrożona bazy danych. Każdego regionu, w której są replikowane bazy danych ma pełnej liczby jednostek żądania obsługi administracyjnej. Aby uzyskać więcej informacji na temat globalnej replikacji, zobacz [sposobu dystrybucji danych globalnie z bazy danych Azure rozwiązania Cosmos](distribute-data-globally.md).

## <a name="request-unit-considerations"></a>Zagadnienia dotyczące jednostki żądania
Szacowaną liczbę jednostek żądania udzielenia należy wziąć pod uwagę następujące zmienne:

* **Rozmiar elementu**. Jak zwiększa rozmiar, zwiększa się również liczbę jednostek żądania używane do odczytywania lub zapisywania danych
* **Liczba właściwości elementu**. Zakładając, że domyślna indeksowania wszystkich właściwości, jednostki używane do zapisania wzrost dokument, węzeł lub jednostki wraz ze wzrostem liczby właściwości.
* **Spójność danych**. Gdy używasz modeli spójności danych jak silne lub ograniczonych nieaktualności jednostek dodatkowe żądania są używane do odczytu elementów.
* **Właściwości indeksowanych**. Zasady indeksu na każdego kontenera określa właściwości, które są indeksowane domyślnie. Można zmniejszyć użycia jednostki żądania dla operacji zapisu, przez ograniczenie liczby właściwości indeksowanych lub włączenie indeksowanie z opóźnieniem.
* **Indeksowanie dokumentów**. Domyślnie każdy element jest indeksowany automatycznie. Jeśli wybierzesz nie zindeksować niektórych elementów wykorzystywać się mniej jednostek żądania.
* **Zapytanie wzorce**. Złożoność kwerendy wpływa na liczbę jednostek żądania są używane dla operacji. Liczba wyników zapytania, liczba predykatów, rodzaj predykaty, liczbę funkcji zdefiniowanej przez użytkownika, rozmiar źródła danych i projekcje wszystkie mają wpływ na koszty operacje zapytań.
* **Użycie skryptu**. Podobnie jak w przypadku zapytań, procedury składowane i wyzwalaczy wykorzystywać jednostki żądania, zależnie od stopnia złożoności wykonywania operacji. Podczas opracowywania aplikacji, sprawdź, czy nagłówek opłat żądania, aby lepiej zrozumieć, jak każdej operacji zużywa pojemność jednostki żądania.

## <a name="estimating-throughput-needs"></a>Planowania potrzeb w zakresie przepustowości
Jednostka żądania jest znormalizowane miara kosztu przetwarzania żądania. Jednostka pojedyncze żądanie reprezentuje możliwości przetwarzania, które są wymagane do odczytu (za pośrednictwem łączy własnych lub identyfikator) jeden element 1 KB, który składa się z 10 unikatowe wartości (z wyjątkiem właściwości systemu). Żądanie utworzenia (Wstaw), Zamień lub usuń takie same elementu wykorzystuje więcej przetwarzania przez usługę, a tym samym wymaga większej liczby jednostek żądania. 

> [!NOTE]
> Linia bazowa jednostka żądania 1 dla elementu 1 KB odpowiada proste GET przez łącze własne lub identyfikator elementu.
> 
> 

Na przykład, w tym miejscu jest tabeli, która przedstawia liczbę jednostek żądania udzielenia dla elementów z trzech różnych rozmiarach (1 KB, 4 KB do 64 KB) i dwa różne poziomy wydajności (odczyty 500 na sekundę 100 zapisy na sekundę i 500 odczyty/sekundę + 500 zapisy na sekundę). W tym przykładzie ustawiono spójności danych **sesji**, a zasady indeksowania jest ustawiona na **Brak**.

| Rozmiar elementu | Odczyty/sekundę | Zapisy na sekundę | Jednostki żądania
| --- | --- | --- | --- |
| 1 KB | 500 | 100 | (500 * 1) + (100 * 5) = 1 000 RU/s
| 1 KB | 500 | 500 | (500 * 1) + (500 * 5) = 3 000 RU/s
| 4 KB | 500 | 100 | (500 * 1,3) + (100 * 7) = 1,350 RU/s
| 4 KB | 500 | 500 | (500 * 1,3) + (500 * 7) = 4,150 RU/s
| 64 KB | 500 | 100 | (500 * 10) + (100 * 48) = 9,800 RU/s
| 64 KB | 500 | 500 | (500 * 10) + (500 * 48) = 29,000 RU/s


### <a name="use-the-request-unit-calculator"></a>Użycie kalkulatora jednostki żądania
Ułatwiają dostosowywanie Twojej ocen przepływności, można użyć opartą na sieci web [Kalkulator jednostki żądania](https://www.documentdb.com/capacityplanner). Kalkulator może pomóc oszacować wymagania dotyczące jednostki żądania dla operacji typowych, w tym:

* Element tworzy (zapisy)
* Odczytuje element
* Usuwa element
* Element aktualizacji

Narzędzie obejmuje również obsługę Szacowanie oparte na przykładowych elementów, które należy podać wymagania dotyczące przechowywania danych.

Aby użyć narzędzia:

1. Należy przekazać co najmniej jeden element reprezentatywny (na przykład dokument JSON przykładowe).
   
    ![Przekaż elementów do Kalkulatora jednostki żądania][2]
2. Aby oszacować wymagania dotyczące magazynu danych, wprowadź całkowitą liczbę elementów (na przykład dokumentów, wierszy lub wierzchołków), które powinny być przechowywane.
3. Wprowadź liczbę tworzenia, odczytu, aktualizacji i operacji delete, które są potrzebne (na podstawie na sekundę). Aby oszacować opłat jednostki żądania elementu operacje aktualizacji, należy przekazać kopię elementu próbki z kroku 1 zawiera pole typowe aktualizacje. Na przykład, jeśli element aktualizacje zwykle zmodyfikuj dwie właściwości o nazwie *lastLogin* i *userVisits*, skopiuj element przykładowych, zaktualizuj wartości dla tych dwóch właściwości, a następnie przekaż skopiowany element.
   
    ![Wprowadź wymagania dotyczące przepływności w Kalkulator jednostki żądania][3]
4. Wybierz **Calculate**, a następnie przejrzyj wyniki.
   
    ![Wyniki Kalkulator jednostki żądania][4]

> [!NOTE]
> Jeśli masz typów elementów, które różnią się znacznie pod względem rozmiaru i liczby właściwości indeksowanych, Przekaż przykładowe każdego *typu* z typowych elementu do narzędzia, a następnie obliczyć wyniki.
> 
> 

### <a name="use-the-azure-cosmos-db-request-charge-response-header"></a>Użyj nagłówka odpowiedzi opłat żądania bazy danych Azure rozwiązania Cosmos
Każdy odpowiedź z usługi Azure DB rozwiązania Cosmos zawiera niestandardowy nagłówek (`x-ms-request-charge`) zawiera jednostki żądania używane dla danego żądania. Ten nagłówek można także przejść za pomocą zestawów SDK z bazy danych Azure rozwiązania Cosmos. W zestawie SDK .NET **RequestCharge** jest właściwością **ResourceResponse** obiektu. Dla zapytań Eksploratora danych Azure DB rozwiązania Cosmos w portalu Azure informacje żądania opłaty dotyczące wykonywane zapytania. Informacje na temat sposobu uzyskania i przepływności zestawu przy użyciu różnych wiele modeli interfejsów API, zobacz [Ustawianie i pobieranie przepływności w usłudze Azure DB rozwiązania Cosmos](set-throughput.md) artykułu.

Jedną z metod oszacowania zarezerwowaną przepływnością wymagane przez aplikację jest rekord opłata jednostki żądania skojarzonego z typowymi operacjami uruchamiania reprezentatywny elementu, który jest używany przez aplikację. Następnie oszacować liczbę operacji, które planujesz do wykonania w ciągu sekundy. Należy również miar i to typowe zapytania i użycie skryptu bazy danych Azure rozwiązania Cosmos.

> [!NOTE]
> Jeśli masz typów elementów, które różnią się znacznie pod względem rozmiaru i liczby właściwości indeksowanych rejestrowania opłata jednostki żądanie dotyczy operacji związanych z każdym *typu* typowe elementu.
> 
> 

Na przykład są kroki, które może wykonać:

1. Zarejestruj opłata jednostki żądania tworzenia (Wstawianie) typowe elementu. 
2. Rekord opłata jednostki żądanie odczytu typowe elementu.
3. Rekord opłata jednostki żądania aktualizowania typowych elementu.
4. Rekord opłata jednostki żądania elementu typowe, typowe zapytań.
5. Zarejestruj za jednostki żądania użytkownika skrypty (procedury składowane, wyzwalacze, funkcje zdefiniowane przez użytkownika) używanych przez aplikację.
6. Oblicz jednostki żądania wymagane podane szacowaną liczbę operacji, które planujesz do uruchomienia w ciągu sekundy.

## <a name="a-request-unit-estimate-example"></a>W przykładzie szacowania jednostki żądania
Należy wziąć pod uwagę następujące dokument, który jest o rozmiarze około 1 KB:

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
> Dokumenty są zminimalizowany w usłudze Azure DB rozwiązania Cosmos, dlatego rozmiar dokumentu powyżej obliczone przez system jest nieco mniej niż 1 KB.
> 
> 

W poniższej tabeli przedstawiono przybliżone żądania jednostki opłat za typowymi operacjami na tym elemencie. (Opłata jednostki żądania przybliżonej przyjęto założenie, że poziomu spójności konta jest ustawiona na **sesji** i że wszystkie elementy są automatycznie indeksowane.)

| Operacja | Opłata jednostki żądania |
| --- | --- |
| Utwórz element |~15 RU |
| Odczytu elementu |~1 RU |
| Element zapytania według Identyfikatora |~2.5 RU |

W poniższej tabeli przedstawiono przybliżone żądania jednostki opłat za typowe zapytania używane w aplikacji:

| Zapytanie | Opłata jednostki żądania | Liczba zwróconych elementów |
| --- | --- | --- |
| Wybierz żywności według Identyfikatora |~2.5 RU |1 |
| Wybierz żywności według producenta |~7 RU |7 |
| Wybierz grupy żywności i kolejność według wagi |~70 RU |100 |
| Zaznacz górny żywności 10 w grupie żywności |~10 RU |10 |

> [!NOTE]
> Opłat jednostki żądania się różnić w zależności od liczby elementów zwróconych.
> 
> 

Dzięki tym informacjom można oszacować wymagania dotyczące jednostki żądania dla tej aplikacji liczby operacji i zapytań spodziewać się na sekundę:

| Operacja/zapytania | Szacowaną liczbę na sekundę | Żądanie wymagane jednostki |
| --- | --- | --- |
| Utwórz element |10 |150 |
| Odczytu elementu |100 |100 |
| Wybierz żywności według producenta |25 |175 |
| Wybierz grupy żywności |10 |700 |
| Wybierz 10 pierwszych |15 |Łącznie 150 |

W takim przypadku spodziewasz się wymóg średniej przepływności 1,275 RU/sekundę. Zaokrąglenie do najbliższej 100, może udostępnić 1,300 RU/sekundę dla tej aplikacji kontenera (lub zbiór kontenery).

## <a id="RequestRateTooLarge"></a> Przekraczanie limitów zarezerwowaną przepływnością w usłudze Azure DB rozwiązania Cosmos
Zużycie jednostka żądania jest oceniany szybkością na sekundę. Dla aplikacji, które przekroczyć współczynnika jednostki elastycznie żądania żądania są ograniczone szybkość dopóki częstotliwość spadnie poniżej poziomu udostępnionej przepływności. Gdy żądanie jest ograniczone szybkości, serwer preemptively kończy się żądanie z `RequestRateTooLargeException` (kod stanu HTTP 429) i zwraca `x-ms-retry-after-ms` nagłówka. Nagłówek wskazuje czas (w milisekundach), które użytkownik musi czekać przed podjęciem próby wykonania żądania.

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Jeśli używasz zestawu SDK klienta usługi .NET i LINQ zapytań, w większości przypadków, nie trzeba postępowania w przypadku tego wyjątku. Bieżąca wersja zestawu SDK klienta .NET niejawnie przechwytuje tej odpowiedzi, szanuje określony serwer ponownych prób po nagłówka i ma automatycznie ponawiać próbę żądania. Następna ponowna próba powiedzie się, chyba że Twoje konto jest uzyskiwany jednocześnie przez wielu klientów.

Jeśli masz więcej niż jednego klienta zbiorczo operacyjnego powyżej liczby żądań, domyślne zachowanie ponawiania mogą być niewystarczające i zgłasza klienta `DocumentClientException` ze stanem kodu 429 do aplikacji. W takich sytuacjach można wziąć pod uwagę obsługi logiki w procedury obsługi błędów aplikacji i zachowanie ponów próbę lub zwiększyć przepływność udostępnione dla kontenera (lub zbiór kontenery).

## <a name="next-steps"></a>Kolejne kroki
 
- Dowiedz się, jak [Ustawianie i pobieranie przepływności w usłudze Azure DB rozwiązania Cosmos](set-throughput.md) przy użyciu portalu Azure i zestawy SDK.
- Dowiedz się więcej o [wydajności i skalowania testowania z bazy danych Azure rozwiązania Cosmos](performance-testing.md).
- Aby dowiedzieć się więcej na temat zarezerwowaną przepływnością z bazami danych bazy danych Azure rozwiązania Cosmos, zobacz [ceny bazy danych Azure rozwiązania Cosmos](https://azure.microsoft.com/pricing/details/cosmos-db/) i [partycjonowanie danych w usłudze Azure DB rozwiązania Cosmos](partition-data.md).
- Aby dowiedzieć się więcej na temat bazy danych rozwiązania Cosmos Azure, zobacz [dokumentacji bazy danych Azure rozwiązania Cosmos](https://azure.microsoft.com/documentation/services/cosmos-db/). 

[2]: ./media/request-units/RUEstimatorUpload.png
[3]: ./media/request-units/RUEstimatorDocuments.png
[4]: ./media/request-units/RUEstimatorResults.png
[5]: ./media/request-units/RUCalculator2.png

