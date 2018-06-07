---
title: Jednostek żądania & Szacowanie przepływności - DB rozwiązania Cosmos Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu zrozumieć, określ i oszacować wymagania dotyczące jednostki żądania w usłudze Azure DB rozwiązania Cosmos.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: rimman
ms.openlocfilehash: b8084008089225c11c8052c60be3afc152881040
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34614836"
---
# <a name="request-units-in-azure-cosmos-db"></a>Żądanie jednostki w Azure rozwiązania Cosmos bazy danych

[Azure DB rozwiązania Cosmos](https://azure.microsoft.com/services/cosmos-db/) jest globalnie rozproszone wielu modelu bazy danych firmy Microsoft. Z bazy danych rozwiązania Cosmos platformy Azure nie trzeba wynajmować maszyn wirtualnych, wdrażania oprogramowania lub monitora bazy danych. Azure DB rozwiązania Cosmos jest obsługiwane i stale monitorowane przez górny pracownicy firmy Microsoft do dostarczania światowej klasy ochrony dostępności, wydajności i danych. Można uzyskać dostępu do danych przy użyciu interfejsów API wybranych przez użytkownika, takich jak [interfejsu API SQL](documentdb-introduction.md), [API bazy danych MongoDB](mongodb-introduction.md), [API tabeli](table-introduction.md)i za pośrednictwem programu graph [Gremlin API](graph-introduction.md) — Wszystkie obsługiwane. 

Waluta bazy danych Azure rozwiązania Cosmos jest **jednostek żądań (RU)**. Z RUs nie należy do zarezerwowania możliwości odczytu/zapisu lub udostępnić procesora CPU, pamięci i IOPS. Azure DB rozwiązania Cosmos obsługuje kilka interfejsów API z różnych operacji — od prostych odczytuje i zapisuje wykres złożonych zapytań. Ponieważ nie wszystkie żądania są takie same, są przypisane znormalizowane ilość **jednostek żądania** na podstawie ilości obliczeń wymaganych do obsłużenia żądania. Liczba jednostek żądania dla operacji jest deterministyczna, a można śledzić liczbę jednostek żądania używane przez żadnych operacji w usłudze Azure DB rozwiązania Cosmos za pośrednictwem nagłówka odpowiedzi. 

Aby zapewnić przewidywalną wydajność, musisz zarezerwować przepływność w jednostkach 100 RU/s. Możesz [oszacować przepustowość sieci musi](request-units.md#estimating-throughput-needs) za pomocą usługi Azure DB rozwiązania Cosmos [Kalkulator jednostki żądania](https://www.documentdb.com/capacityplanner).

![Kalkulator przepływności][5]

Po przeczytaniu tego artykułu, będziesz mieć możliwość odpowiedzieć na następujące pytania:  

* Jakie są jednostek żądania i żądania opłat w usłudze Azure DB rozwiązania Cosmos?
* Jak określić pojemność jednostki żądania dla kontenera lub zestawu kontenerów w usłudze Azure DB rozwiązania Cosmos?
* Jak oszacować musi jednostki żądania Moja aplikacja
* Co się stanie, jeśli I przekracza pojemność jednostki żądania dla kontenera lub zestawu kontenerów w usłudze Azure DB rozwiązania Cosmos?

Bazy danych Azure rozwiązania Cosmos jest wiele modeli bazy danych; należy pamiętać, że w tym artykule jest zastosowanie do wszystkich modeli danych i interfejsów API w usłudze Azure DB rozwiązania Cosmos. W tym artykule wykorzystano terminy ogólne *kontenera* i *elementu* oznacza objęty kolekcji, wykres, lub tabeli i dokumentu, węzeł lub jednostki, odpowiednio.

## <a name="request-units-and-request-charges"></a>Jednostek żądania i żądania opłat
Azure DB rozwiązania Cosmos zapewnia szybkie, przewidywalną wydajność przez *rezerwowania* zasobów do zaspokojenia musi przepływność aplikacji.  Ponieważ aplikacja obciążenia i dostęp do zmiany wzorce wraz z upływem czasu, bazy danych Azure rozwiązania Cosmos umożliwia łatwe zwiększyć lub zmniejszyć ilość zarezerwowaną przepływnością dostępne dla aplikacji.

Z bazy danych Azure rozwiązania Cosmos zarezerwowaną przepływnością jest określane w przeliczeniu na jednostki żądania przetwarzania na sekundę. Można potraktować jednostki żądania jako walutę przepływności, zgodnie z którymi możesz *zarezerwować* ilość gwarantowane żądania jednostki dostępne dla aplikacji na podstawie sekundowym.  Każdej operacji w usłudze Azure DB rozwiązania Cosmos — zapisywanie dokumentu, wykonywania zapytania, aktualizowanie dokumentu — korzysta z Procesora, pamięci i IOPS.  Oznacza to, że każda operacja wiąże się z *żądań bezpłatnie*, który jest wyrażona w *jednostek żądania*.  Opis czynników, które mają wpływ żądania jednostki opłat, wraz z aplikacji wymagania dotyczące przepływności, umożliwia uruchamianie aplikacji jako koszt skutecznie, jak to możliwe. Eksplorator danych w portalu Azure jest również cudowne narzędzie do testowania podstawowej zapytania.

Zalecamy rozpoczęcie pracy od obejrzenia poniższego klipu wideo, w którym Azure Menedżera programów DB rozwiązania Cosmos Andrew Liu omówiono jednostki żądania.

> [!VIDEO https://www.youtube.com/embed/stk5WSp5uX0]
> 
> 

## <a name="throughput-isolation-in-globally-distributed-databases"></a>Izolacja przepływności w rozproszonych globalnie baz danych

W przypadku bazy danych zostały zreplikowane do więcej niż jeden region, bazy danych Azure rozwiązania Cosmos zapewnia izolację przepływności, aby upewnić się, że użycie RU w jednym regionie nie ma wpływu na RU użycia w innym regionie. Na przykład, jeśli zapisu danych do jednego regionu i odczytywanie danych z innego regionu, RUs są używane do wykonywania operacji zapisu w regionie *A* nie przyjmują przeciwną RUs używane dla operacji odczytu w regionie *B*. RUs nie są dzielone w regionach, w których została wdrożona. Każdego regionu, w której są replikowane bazy danych ma pełny numer RUs udostępnione. Aby uzyskać więcej informacji na temat globalnej replikacji, zobacz [sposobu dystrybucji danych globalnie z bazy danych Azure rozwiązania Cosmos](distribute-data-globally.md).

## <a name="request-unit-considerations"></a>Zagadnienia dotyczące jednostki żądania
Szacowanie liczby jednostek żądania udzielenia, należy wziąć pod uwagę następujące zmienne:

* **Rozmiar elementu**. Jak rozmiar zwiększa liczbę zwiększa się również używane do odczytywania lub zapisywania danych jednostki żądania
* **Liczba właściwości elementu**. Zakładając, że domyślna indeksowania wszystkich właściwości, jednostki używane do zapisania wzrost dokumentu na węzeł/jednostkę wraz ze wzrostem liczby właściwości.
* **Spójność danych**. Przy użyciu modeli spójności danych, takich jak silne lub ograniczonych nieaktualności, jednostki dodatkowe żądania są używane do odczytu elementów.
* **Właściwości indeksowanych**. Zasady indeksu na każdego kontenera określa właściwości, które są indeksowane domyślnie. Można zmniejszyć użycia jednostki żądania dla operacji zapisu, przez ograniczenie liczby właściwości indeksowanych lub włączenie indeksowanie z opóźnieniem.
* **Indeksowanie dokumentów**. Domyślnie każdy element jest indeksowany automatycznie. Jeśli wybierzesz nie zindeksować niektórych elementów wykorzystywać się mniej jednostek żądania.
* **Zapytanie wzorce**. Złożoność kwerendy wpływa na liczbę jednostek żądania są używane dla operacji. Liczba wyników zapytania, liczba predykatów, rodzaj predykaty, projekcje liczbę funkcji UDF i rozmiar źródła danych - wszystkie wpływ kosztów operacji zapytania.
* **Użycie skryptu**.  Podobnie jak w przypadku zapytań, procedury składowane i wyzwalaczy wykorzystywać jednostki żądania, zależnie od stopnia złożoności wykonywania operacji. Podczas opracowywania aplikacji, sprawdź, czy nagłówek opłat żądania, aby lepiej zrozumieć, jak każdej operacji zajmuje pojemność jednostki żądania.

## <a name="estimating-throughput-needs"></a>Planowania potrzeb w zakresie przepustowości
Jednostka żądania jest znormalizowane miara kosztu przetwarzania żądania. Jednostka pojedyncze żądanie reprezentuje możliwości przetwarzania wymagane do odczytu (za pośrednictwem łączy własnych lub identyfikator) pojedynczego 1 KB elementu składające się z 10 unikatowe wartości (z wyjątkiem właściwości systemu). Żądanie utworzenia (Wstaw), Zamień lub Usuń ten sam element będą korzystać z dodatkowych zadań przetwarzania przez usługę i tym samym więcej jednostek żądania.   

> [!NOTE]
> Linia bazowa jednostka żądania 1 dla elementu 1 KB odpowiada proste GET przez łącze własne lub identyfikator elementu.
> 
> 

Na przykład, w tym miejscu jest tabeli, która przedstawia liczbę jednostek żądania udzielenia dla elementów z trzech różnych rozmiarach (1 KB, 4 KB do 64 KB) i dwa różne poziomy wydajności (odczyty 500 na sekundę 100 zapisy na sekundę i 500 odczyty/sekundę + 500 zapisy na sekundę). Spójność danych skonfigurowano na *sesji*, i ustawiono zasady indeksowania *Brak*.

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

1. Należy przekazać co najmniej jeden element reprezentatywny (na przykład dokument JSON przykładowe).
   
    ![Przekaż elementów do Kalkulatora jednostki żądania][2]
2. Aby oszacować wymagania dotyczące magazynu danych, wprowadź całkowitą liczbę elementów (na przykład dokumenty, wierszy lub wierzchołków) prawdopodobnie będzie przechowywana.
3. Wprowadź liczbę tworzenia, odczytu, aktualizacji i operacji delete, które są wymagane (na podstawie na sekundę). Aby oszacować opłat jednostki żądania operacji aktualizowania elementu, Przekaż kopię elementu próbki z kroku 1 zawiera pole typowe aktualizacje.  Na przykład, jeśli element aktualizacje zwykle zmodyfikuj dwie właściwości o nazwie *lastLogin* i *userVisits*, następnie skopiuj element przykładowych, zaktualizuj wartości dla tych dwóch właściwości i przekazać skopiowany element.
   
    ![Wprowadź wymagania dotyczące przepływności w Kalkulator jednostki żądania][3]
4. Kliknij przycisk Oblicz i przejrzeć wyniki.
   
    ![Wyniki Kalkulator jednostki żądania][4]

> [!NOTE]
> Jeśli masz typów elementów, które różnią się znacznie pod względem rozmiaru i liczby właściwości indeksowanych, Przekaż przykładowe każdego *typu* z typowych elementu do narzędzia i obliczyć wyniki.
> 
> 

### <a name="use-the-azure-cosmos-db-request-charge-response-header"></a>Użyj nagłówka odpowiedzi opłat żądania bazy danych Azure rozwiązania Cosmos
Każdy odpowiedź z usługi Azure DB rozwiązania Cosmos zawiera niestandardowy nagłówek (`x-ms-request-charge`) zawiera jednostki żądania używane dla danego żądania. Ten nagłówek jest również dostępny za pośrednictwem Azure DB rozwiązania Cosmos z zestawów SDK. W zestawie SDK .NET `RequestCharge` jest właściwością `ResourceResponse` obiektu.  Dla zapytań Eksploratora danych Azure DB rozwiązania Cosmos w portalu Azure informacje żądania opłaty dotyczące wykonywane zapytania.

Pamiętając o tym, jedną z metod do oszacowania ilości zarezerwowaną przepływnością wymagane przez aplikację jest rekord opłata jednostki żądania skojarzonego z typowymi operacjami uruchamiania elementu reprezentatywny używanych przez aplikację i następnie ocenić Liczba operacji, które planujesz do wykonania w ciągu sekundy.  Pamiętaj mierzyć i obejmują typowe zapytania i również użycie skryptu bazy danych Azure rozwiązania Cosmos.

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

## <a name="a-request-unit-estimate-example"></a>W przykładzie szacowania jednostki żądania
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

W poniższej tabeli przedstawiono przybliżone żądania jednostki związanych z typowymi operacjami na tym elemencie (opłata jednostki żądania przybliżonej przyjęto założenie, że poziomu spójności konta jest ustawiona na *sesji* i że wszystkie elementy są automatycznie indeksowane):

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
> Opłaty RU różnią się zależnie od liczby elementów zwróconych.
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

W takim przypadku spodziewasz się wymóg średniej przepływności 1,275 RU/s.  Zaokrąglenie do najbliższej 100, może udostępnić 1300 RU/s dla tej aplikacji kontenera (lub zbiór kontenery).

## <a id="RequestRateTooLarge"></a> Przekraczanie limitów zarezerwowaną przepływnością w usłudze Azure DB rozwiązania Cosmos
Odwołaj się, że zużycie jednostka żądania jest oceniane w szybkość na sekundę. Dla aplikacji, które przekroczyć współczynnika jednostki elastycznie żądania żądania będą ograniczone szybkość dopóki częstotliwość spadnie poniżej poziomu udostępnionej przepływności. Gdy żądanie pobiera współczynnik ograniczone, serwer preemptively kończy się żądanie z `RequestRateTooLargeException` (kod stanu HTTP 429) i zwraca `x-ms-retry-after-ms` nagłówek wskazujący ilość czasu (w milisekundach), które użytkownik musi czekać przed podjęciem próby wykonania żądania.

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Jeśli używasz zestawu SDK klienta usługi .NET i LINQ zapytania, a następnie w większości przypadków, nie trzeba uwzględniać tego wyjątku, zgodnie z bieżącą wersją programu .NET SDK klienta niejawnie przechwytuje tej odpowiedzi szanuje określony serwer ponownych prób po nagłówka i ponawia żądanie automatycznie. Następna ponowna próba powiedzie się, chyba że Twoje konto jest uzyskiwany jednocześnie przez wielu klientów.

Jeśli masz więcej niż jednego klienta zbiorczo operacyjnego powyżej liczby żądań, domyślne zachowanie ponownych prób nie mogą być niewystarczające i klient zgłosi `DocumentClientException` ze stanem kodu 429 do aplikacji. W takich sytuacjach warto wziąć pod uwagę zachowanie ponownych prób i logikę w aplikacji Błąd procedury obsługi lub zwiększyć przepływność udostępnione dla kontenera (lub zbiór kontenery).

## <a name="next-steps"></a>Kolejne kroki
 
Aby dowiedzieć się więcej o sposobie Ustawianie i pobieranie przepływność przy użyciu portalu Azure i zestawu SDK elementu zobacz:

* [Ustawianie i pobieranie przepływności w usłudze Azure DB rozwiązania Cosmos](set-throughput.md)

Aby dowiedzieć się więcej na temat zarezerwowaną przepływnością z bazami danych bazy danych Azure rozwiązania Cosmos, zapoznaj się z tymi zasobami:

* [Cennik platformy Azure DB rozwiązania Cosmos](https://azure.microsoft.com/pricing/details/cosmos-db/)
* [Partycjonowanie danych w usłudze Azure DB rozwiązania Cosmos](partition-data.md)

Aby dowiedzieć się więcej na temat bazy danych rozwiązania Cosmos Azure, zobacz Azure DB rozwiązania Cosmos [dokumentacji](https://azure.microsoft.com/documentation/services/cosmos-db/). 

Aby rozpocząć testowanie z bazy danych Azure rozwiązania Cosmos wydajności i skalowania, zobacz [wydajności i skalowania testowania z bazy danych Azure rozwiązania Cosmos](performance-testing.md).

[2]: ./media/request-units/RUEstimatorUpload.png
[3]: ./media/request-units/RUEstimatorDocuments.png
[4]: ./media/request-units/RUEstimatorResults.png
[5]: ./media/request-units/RUCalculator2.png

