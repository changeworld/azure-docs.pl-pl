---
title: Transakcje bazy danych i mechanizmu kontroli optymistycznej współbieżności w usłudze Azure Cosmos DB
description: W tym artykule opisano transakcji bazy danych i mechanizmu kontroli optymistycznej współbieżności w usłudze Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 568f47aacf39793d4c2da46798682abc002ca33b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59279514"
---
# <a name="transactions-and-optimistic-concurrency-control"></a>Transakcje i optymistyczna kontrola współbieżności

Transakcje bazy danych zapewniają bezpieczny i przewidywalne model programowania do czynienia z równoczesnych zmian danych. Tradycyjnych relacyjnych baz danych, takich jak SQL Server umożliwiają zapisanie logiki biznesowej przy użyciu procedur składowanych i/lub wyzwalacze, wysłać ją do serwera w celu wykonania bezpośrednio wewnątrz aparatu bazy danych. Przy użyciu tradycyjnych relacyjnych baz danych jest wymagane, aby poradzić sobie z dwóch różnych języków programowania aplikacji (nietransakcyjnej), języka, takich jak JavaScript, Python, programowania C#, Java itp. oraz transakcyjnych programowania (język takie jak T-SQL), natywnie wykonywane przez bazy danych.

Aparat bazy danych Azure Cosmos DB obsługuje pełne zgodne transakcje ACID (niepodzielność, spójność, izolacja, trwałość), z użyciem izolacji migawki. Wszystkie operacje bazy danych w zakresie kontenera [partycji logicznej](partition-data.md) transakcyjnie w ramach aparatu bazy danych, który jest hostowany przez replikę partycji. Operacje te obejmują zarówno zapis (Aktualizowanie jednego lub więcej elementów w ramach partycji logicznej) i operacji odczytu. W poniższej tabeli przedstawiono różne operacje i typy transakcji:

| **Operacja**  | **Typ operacji** | **Jednego lub wielu elementów** |
|---------|---------|---------|
| Wstaw (bez wyzwalacz pre lub używanego po nim) | Zapisywanie | Pojedynczy element transakcji |
| Wstaw (z wyzwalaczem pre lub używanego po nim) | Zapisu i odczytu | Transakcji wielu elementów |
| Zastąp (bez wyzwalacz pre lub używanego po nim) | Zapisywanie | Pojedynczy element transakcji |
| Zamień (wyzwalacz pre lub używanego po nim) | Zapisu i odczytu | Transakcji wielu elementów |
| UPSERT (bez wyzwalacz pre lub używanego po nim) | Zapisywanie | Pojedynczy element transakcji |
| UPSERT (z wyzwalaczem pre lub używanego po nim) | Zapisu i odczytu | Transakcji wielu elementów |
| Usuń (bez wyzwalacz pre lub używanego po nim) | Zapisywanie | Pojedynczy element transakcji |
| Usuń (z wyzwalaczem pre lub używanego po nim) | Zapisu i odczytu | Transakcji wielu elementów |
| Wykonaj procedurę składowaną | Zapisu i odczytu | Transakcji wielu elementów |
| System zainicjowane wykonywania procedury scalania | Zapisywanie | Transakcji wielu elementów |
| System zainicjowane wykonywania usuwania elementów w oparciu o wygaśnięcia (TTL) elementu | Zapisywanie | Transakcji wielu elementów |
| Odczyt | Odczyt | Pojedynczą pozycją transakcji |
| Kanał informacyjny zmian | Odczyt | Transakcji wielu elementów |
| Odczyt z podziałem na strony | Odczyt | Transakcji wielu elementów |
| Zapytanie z podziałem na strony | Odczyt | Transakcji wielu elementów |
| Wykonanie funkcji zdefiniowanej przez użytkownika jako część zapytania z podziałem na strony | Odczyt | Transakcji wielu elementów |

## <a name="multi-item-transactions"></a>Transakcje wielu elementów

Usługa Azure Cosmos DB pozwala na zapis [procedury składowane, wyzwalacze pre lub używanego po nim — funkcje zdefiniowane przez użytkownika — (przez użytkownika UDF)](stored-procedures-triggers-udfs.md) dane i scal procedur w języku JavaScript. Usługa Azure Cosmos DB natywnie obsługuje wykonywanie skryptów JavaScript w jego aparatu bazy danych. Można zarejestrować procedury składowane, należy wstępnie lub używanego po nim wyzwalaczy, — funkcje zdefiniowane przez użytkownika — (przez użytkownika UDF) i procedur scalania w kontenerze i nowszych wykonać je transakcyjnie w ramach aparatu bazy danych Azure Cosmos. Pisanie logiki aplikacji w języku JavaScript umożliwia naturalne wyrażanie przepływ sterowania, zmiennej zakresu, przypisania i integracji w ramach transakcji bazy danych bezpośrednio w języku JavaScript w nim elementów podstawowych obsługi wyjątków.

Oparte na języku JavaScript procedury składowane, wyzwalacze, funkcje zdefiniowane przez użytkownika i procedur scalania są opakowane w ramach transakcji ACID otoczenia z izolacją migawki we wszystkich elementach w ramach partycji logicznej. W trakcie wykonywania Jeśli JavaScript program zgłasza wyjątek, cała transakcja zostaje przerwana i przeniesiona do tyłu. Wynikowy model programowania jest prosty jeszcze zaawansowane. Deweloperów języka JavaScript Uzyskaj trwałe model programowania, chociaż nadal przy użyciu dobrze znanych języka tworzy i biblioteki w nim elementów podstawowych.

Możliwość wykonywania języka JavaScript bezpośrednio wewnątrz aparatu bazy danych zapewnia wydajność i transakcyjne wykonywanie operacji bazy danych przed elementy kontenera. Ponadto ponieważ aparat bazy danych usługi Cosmos Azure natywnie obsługuje JSON i języka JavaScript, nie jest Brak niezgodności impedancji między systemami typu aplikacji i bazy danych.

## <a name="optimistic-concurrency-control"></a>Mechanizmu kontroli optymistycznej współbieżności 

Mechanizmu kontroli optymistycznej współbieżności pozwala uniknąć utraty aktualizacji i usuwa. Konflikt równoczesnych operacji są poddawane regularne pesymistycznego blokowania pracujących na partycji logicznej, która jest właścicielem elementu aparatu bazy danych. Gdy dwie operacje współbieżne próbują zaktualizować najnowszą wersję elementu w obrębie partycji logicznej, jeden z nich zostanie zarejestrowane, a druga zakończy się niepowodzeniem. Jednak jeśli jednego lub dwóch operacji podjęto próbę zaktualizowania jednocześnie tego samego elementu miał poprzednio odczytać starszą wartość elementu, baza danych nie może ustalić, jeśli wcześniej odczytu wartość przez jeden lub oba operacjami wywołującymi konflikt była rzeczywiście najnowszą wartość elementu. Na szczęście, ta sytuacja może zostać wykryte za pomocą **optymistycznej współbieżności kontroli (OCC)** przed wynajmowanie dwie operacje wprowadź granic transakcji w aparacie bazy danych. OCC chroni dane przed przypadkowemu zastąpieniu zmiany wprowadzone przez innych użytkowników. On również uniemożliwia innym użytkownikom przypadkowemu zastąpieniu własnych zmian.

Równoczesne aktualizacje elementu są poddawane OCC przez warstwę protokołu komunikacji usługi Azure Cosmos DB. Bazy danych usługi Azure Cosmos gwarantuje, że element, który jest aktualizacja (lub usuwanie) w wersji po stronie klienta jest taka sama jak wersja elementu w kontenerze usługi Azure Cosmos. Gwarantuje to, że zapisu są chronione przed zastąpieniem przypadkowo za operacje zapisu, inne osoby i na odwrót. W środowisku wielu użytkowników kontroli optymistycznej współbieżności chroni przypadkowemu usunięciu lub aktualizowanie niewłaściwej wersji elementu. Jako takie elementy są chronione przed nikczemnych "utraconej aktualizacji" lub "utracone delete" problemów.

Każdy element, przechowywane w kontenerze usługi Azure Cosmos ma zdefiniowane przez system `_etag` właściwości. Wartość `_etag` jest automatycznie generowany i aktualizowane przez serwer, za każdym razem, gdy element jest aktualizowany. `_etag` mogą być używane z klienta dostarczony `if-match` nagłówek żądania, aby serwer mógł zdecydować, czy element może być aktualizowana warunkowo. Wartość `if-match` nagłówka odpowiada wartości `_etag` na serwerze, element jest następnie aktualizowany. Jeśli wartość `if-match` nagłówek żądania nie jest już aktualna, serwer odrzuca operację, podając "HTTP 412 niepowodzenie warunku wstępnego" komunikat odpowiedzi. Klient następnie można ponownie pobrać element, aby pobrać bieżącą wersję elementu na serwerze lub zastąpić wersję elementu na serwerze za pomocą własnego `_etag` wartość elementu. Ponadto `_etag` mogą być używane z `if-none-match` nagłówek, aby określić, czy ponownie Pobierz zasobu jest wymagana. 

Element `_etag` wartość zmienia się za każdym razem, gdy element jest aktualizowany. Dla elementu zamienianie `if-match` muszą być jawnie wyrażone jako część opcji żądania. Aby uzyskać przykład, zobacz przykładowy kod [GitHub](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs#L398-L446). `_etag` wartości są niejawnie sprawdzane pod kątem wszystkich elementów napisane korzystały z procedury składowanej. W przypadku wykrycia konfliktów procedury składowanej Wycofaj tę transakcję i zwróci wyjątek. Przy użyciu tej metody wszystkie lub nie zapisy w ramach procedury składowanej stosowane są niepodzielne. Jest to sygnał do aplikacji, aby ponownie zastosować aktualizacje, a następnie spróbuj ponownie oryginalne żądanie klienta.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat transakcji bazy danych i mechanizmu kontroli optymistycznej współbieżności w następujących artykułach:

- [Praca z baz danych Azure Cosmos, kontenery i elementów](databases-containers-items.md)
- [Poziomy spójności](consistency-levels.md)
- [Typy konfliktów i zasady ich rozwiązywania](conflict-resolution-policies.md)
- [Procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika](stored-procedures-triggers-udfs.md)
