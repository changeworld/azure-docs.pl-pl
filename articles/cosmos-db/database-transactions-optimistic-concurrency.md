---
title: Transakcje bazy danych i optymistyczna kontrola współbieżności w usłudze Azure Cosmos DB
description: W tym artykule opisano transakcje bazy danych i kontrolę współbieżności optymistycznej w usłudze Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/04/2019
ms.reviewer: sngun
ms.openlocfilehash: d453bb4071c4a6972e01b8f7e90375181caf6d01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74806528"
---
# <a name="transactions-and-optimistic-concurrency-control"></a>Transakcje i optymistyczna kontrola współbieżności

Transakcje bazy danych zapewniają bezpieczny i przewidywalny model programowania do czynienia z równoczesnych zmian danych. Tradycyjne relacyjne bazy danych, takie jak SQL Server, umożliwiają pisanie logiki biznesowej przy użyciu procedur przechowywanych i/lub wyzwalaczy, wysyłanie jej do serwera do wykonania bezpośrednio w silniku bazy danych. W przypadku tradycyjnych relacyjnych baz danych wymagane jest do czynienia z dwoma różnymi językami programowania (nietransakcyjnych) język programowania aplikacji, takich jak JavaScript, Python, C#, Java, itp.

Aparat bazy danych w usłudze Azure Cosmos DB obsługuje pełne acid (niepodzielność, spójność, izolacja, trwałość) zgodne transakcje z izolacją migawki. Wszystkie operacje bazy danych w zakresie [partycji logicznej](partition-data.md) kontenera są transakcyjnie wykonywane w ramach aparatu bazy danych, który jest obsługiwany przez replikę partycji. Operacje te obejmują zarówno zapisu (aktualizowanie jednego lub więcej elementów w partycji logicznej) i operacji odczytu. W poniższej tabeli przedstawiono różne operacje i typy transakcji:

| **Operacji**  | **Typ operacji** | **Transakcja pojedyncza lub wielouchowucheładowa** |
|---------|---------|---------|
| Wstaw (bez wyzwalacza przed/słupka) | Zapisywanie | Transakcja pojedynczego towaru |
| Wstaw (za pomocą wyzwalacza przed/słupka) | Pisanie i odczytywanie | Transakcja wielouchłowa |
| Wymień (bez wyzwalacza przed/słupka) | Zapisywanie | Transakcja pojedynczego towaru |
| Zamień (na wyzwalacz przed/post) | Pisanie i odczytywanie | Transakcja wielouchłowa |
| Upsert (bez wyzwalacza przed/post) | Zapisywanie | Transakcja pojedynczego towaru |
| Upsert (z wyzwalaczem przed/postem) | Pisanie i odczytywanie | Transakcja wielouchłowa |
| Usuń (bez wyzwalacza przed/post) | Zapisywanie | Transakcja pojedynczego towaru |
| Usuń (za pomocą wyzwalacza przed/wpis) | Pisanie i odczytywanie | Transakcja wielouchłowa |
| Wykonywanie procedury składowanej | Pisanie i odczytywanie | Transakcja wielouchłowa |
| System zainicjował wykonanie procedury scalania | Zapisywanie | Transakcja wielouchłowa |
| System zainicjował wykonanie usuwania elementów na podstawie wygaśnięcia (TTL) towaru | Zapisywanie | Transakcja wielouchłowa |
| Odczyt | Odczyt | Transakcja z jednym towarem |
| Zestawienie zmian | Odczyt | Transakcja wielouchłowa |
| Odczyt na paginated | Odczyt | Transakcja wielouchłowa |
| Zapytanie nagiezonowe | Odczyt | Transakcja wielouchłowa |
| Wykonywanie UDF jako część kwerendy na strony | Odczyt | Transakcja wielouchłowa |

## <a name="multi-item-transactions"></a>Transakcje wielouchłowe

Usługa Azure Cosmos DB umożliwia pisanie [procedur przechowywanych, wyzwalaczy przed/post, zdefiniowanych przez użytkownika funkcji (UDF)](stored-procedures-triggers-udfs.md) i procedur scalania w języku JavaScript. Usługa Azure Cosmos DB natywnie obsługuje wykonywanie języka JavaScript wewnątrz aparatu bazy danych. Można zarejestrować procedury przechowywane, wyzwalacze przed/post, funkcje zdefiniowane przez użytkownika (UDFs) i procedury scalania w kontenerze, a później wykonać je transakcyjnie w ramach aparatu bazy danych usługi Azure Cosmos. Logika aplikacji pisania w języku JavaScript umożliwia naturalne wyrażenie przepływu sterowania, zmienne zakres, przypisanie i integracji wyjątków obsługi umamiwy w transakcjach bazy danych bezpośrednio w języku JavaScript.

Procedury przechowywane oparte na języku JavaScript, wyzwalacze, pliki UDF i procedury scalania są zawijane w ramach transakcji ACID otoczenia z izolacją migawki we wszystkich elementach w partycji logicznej. W trakcie jego wykonywania, jeśli program JavaScript zgłasza wyjątek, cała transakcja zostanie przerwana i wycofana. Powstały model programowania jest prosty, ale potężny. Deweloperzy JavaScript uzyskać trwały model programowania, podczas gdy nadal przy użyciu ich znanych konstrukcji językowych i pierwotnych biblioteki.

Możliwość wykonywania języka JavaScript bezpośrednio w ramach aparatu bazy danych zapewnia wydajność i transakcyjne wykonywanie operacji bazy danych względem elementów kontenera. Ponadto ponieważ aparat bazy danych usługi Azure Cosmos natywnie obsługuje JSON i JavaScript, nie ma niezgodności impedancji między systemami typów aplikacji i bazy danych.

## <a name="optimistic-concurrency-control"></a>Optymistyczna kontrola współbieżności

Optymistyczna kontrola współbieżności pozwala zapobiegać utraconym aktualizacjom i usuwaniu. Równoczesne, sprzeczne operacje są poddawane regularne pesymistyczne blokowanie aparatu bazy danych obsługiwanych przez partycję logiczną, która jest właścicielem elementu. Gdy dwie równoczesne operacje próbują zaktualizować najnowszą wersję elementu w partycji logicznej, jedna z nich wygra, a druga zakończy się niepowodzeniem. Jednak jeśli jedna lub dwie operacje próbujące jednocześnie zaktualizować ten sam element wcześniej odczytywał starszą wartość elementu, baza danych nie wie, czy poprzednio odczyt wartości przez jedną lub obie operacje powodujące konflikt był rzeczywiście najnowszą wartość elementu. Na szczęście tę sytuację można wykryć za pomocą **optymistycznego kontroli współbieżności (OCC)** przed pozwoleniem dwie operacje wprowadzić granicę transakcji wewnątrz aparatu bazy danych. OCC chroni dane przed przypadkowym zastąpieniem zmian wprowadzonych przez inne osoby. Zapobiega to również przypadkowemu zastąpieniu przez inne osoby własnych zmian.

Równoczesne aktualizacje elementu są poddawane OCC przez warstwę protokołu komunikacyjnego usługi Azure Cosmos DB. Baza danych usługi Azure Cosmos zapewnia, że wersja po stronie klienta elementu, który aktualizujesz (lub usuwanie) jest taka sama jak wersja elementu w kontenerze usługi Azure Cosmos. Gwarantuje to, że zapisy są chronione przed nadpisywane przypadkowo przez zapisy innych i odwrotnie. W środowisku wielu użytkowników optymistyczny formant współbieżności chroni przed przypadkowym usunięciem lub zaktualizowaniem niewłaściwej wersji elementu. W związku z tym elementy są chronione przed niesławnymi problemami z "utraconą aktualizacją" lub "utraconym usunięciem".

Każdy element przechowywany w kontenerze usługi `_etag` Azure Cosmos ma właściwość zdefiniowaną przez system. Wartość `_etag` jest automatycznie generowana i aktualizowana przez serwer za każdym razem, gdy element jest aktualizowany. `_etag`może być używany z `if-match` nagłówkiem żądania dostarczonego przez klienta, aby umożliwić serwerowi podjęcie decyzji, czy element może być warunkowo zaktualizowany. Wartość nagłówka `if-match` jest zgodna z `_etag` wartością na serwerze, element jest następnie aktualizowany. Jeśli wartość nagłówka `if-match` żądania nie jest już aktualna, serwer odrzuca operację z komunikatem odpowiedzi "Błąd warunku wstępnego HTTP 412". Klient następnie można ponownie pobrać element, aby uzyskać bieżącą wersję elementu na serwerze lub zastąpić wersję `_etag` elementu na serwerze z własną wartością dla elementu. Ponadto może `_etag` służyć z `if-none-match` nagłówkiem, aby ustalić, czy refetch zasobu jest potrzebne.

`_etag` Wartość elementu zmienia się za każdym razem, gdy element jest aktualizowany. W przypadku operacji `if-match` zastępowania towaru musi być jawnie wyrażona jako część opcji żądania. Na przykład zobacz przykładowy kod w [usłudze GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L578-L674). `_etag`wartości są niejawnie sprawdzane dla wszystkich elementów pisemnych dotkniętych procedurą składowaną. Jeśli zostanie wykryty konflikt, procedura składowana wycofa transakcję i zda wyjątek. Za pomocą tej metody wszystkie lub nie zapisy w ramach procedury składowanej są stosowane niepodzielnie. Jest to sygnał do aplikacji, aby ponownie zastosować aktualizacje i ponów próbę oryginalnego żądania klienta.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o transakcjach bazy danych i optymistycznej kontroli współbieżności w następujących artykułach:

- [Praca z bazami danych, kontenerami i elementami usługi Azure Cosmos](databases-containers-items.md)
- [Poziomy spójności](consistency-levels.md)
- [Typy konfliktów i zasady ich rozwiązywania](conflict-resolution-policies.md)
- [Procedury przechowywane, wyzwalacze i funkcje zdefiniowane przez użytkownika](stored-procedures-triggers-udfs.md)
