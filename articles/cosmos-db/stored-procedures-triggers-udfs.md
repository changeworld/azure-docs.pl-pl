---
title: Praca z procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika w usłudze Azure Cosmos DB
description: W tym artykule przedstawiono pojęć, takich jak procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika w usłudze Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 53ff318dcc034fb11e2d554f9ad8e8814eb32879
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672589"
---
# <a name="stored-procedures-triggers-and-user-defined-functions"></a>Procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika

Usługa Azure Cosmos DB zapewnia języku zintegrowanym, transakcyjne wykonywanie kodu JavaScript. Korzystając z interfejsu API SQL w usłudze Azure Cosmos DB, można napisać **procedur składowanych**, **wyzwalaczy**, i **funkcje zdefiniowane przez użytkownika (UDF)** w języku JavaScript. Można napisać logikę w języku JavaScript, który wykonywany w aparacie bazy danych. Można tworzyć i uruchamiać wyzwalaczy, procedury składowane i funkcje zdefiniowane przez użytkownika przy użyciu [witryny Azure portal](https://portal.azure.com/), [języka JavaScript zintegrowany interfejs API zapytań w usłudze Azure Cosmos DB](javascript-query-api.md) lub [klienta interfejsu API SQL usługi Cosmos DB Zestawy SDK](how-to-use-stored-procedures-triggers-udfs.md).

## <a name="benefits-of-using-server-side-programming"></a>Korzyści z używania programowanie po stronie serwera

Zapisywanie procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika (UDF) w języku JavaScript umożliwia tworzenie zaawansowanych aplikacji i mają następujące zalety:

* **Proceduralne logiki:** Języka JavaScript jako wysokiego poziomu języka programowania, która zapewnia szerokie i powszechnie znane interfejs do logiki biznesowej express. Możesz wykonać sekwencję złożonych operacji na danych.

* **Transakcje niepodzielne:** Usługa Azure Cosmos DB gwarantuje, że operacje bazy danych, które są wykonywane w ramach jednej procedury składowanej lub wyzwalacza są niepodzielne. Funkcja ta atomic umożliwia aplikacji łączenie powiązanych operacji w jednej partii, tak, aby wszystkie operacje powodzenie lub żadna z nich powiodło się.

* **Wydajność:** Dane JSON wewnętrznie jest mapowany do systemu typów języka JavaScript. Umożliwia to mapowanie szereg optymalizacje, takie jak z opóźnieniem materializacja dokumentów JSON w puli buforów i nadawania dostępne na żądanie na wykonywanie kodu. Istnieją inne korzyści wydajności skojarzone z wysyłki logiki biznesowej w bazie danych, która zawiera:

   * *Przetwarzanie wsadowe:* Można grupować operacje, takie jak operacje wstawiania i prześlij je zbiorczo. Koszty opóźnienie ruchu sieciowego i tworzenie oddzielnych transakcji nakład pracy magazynu są znacznie mniejsze.

   * *Wstępna kompilacja:* Procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika są niejawnie wstępnie skompilowanym formatu kodu bajtów, aby uniknąć kosztów kompilacji podczas każdego wywołania skryptu. Z powodu wstępnej kompilacji wywołania procedur składowanych jest szybkie i ma niewielki rozmiar.

   * *Sekwencjonowanie:* Czasami operacji należy wyzwalająca mechanizm, który może wykonać jedną lub dodatkowe aktualizacje danych. Oprócz niepodzielność dostępne są także korzyści wydajności podczas wykonywania po stronie serwera.

* **Hermetyzacja protokołu:** Procedury składowane może służyć do grupowania logiki w jednym miejscu. Hermetyzacja dodaje warstwę abstrakcji na podstawie danych, co pozwala na rozwój aplikacji niezależnie od danych. Ta warstwa abstrakcji jest przydatne, gdy dane są bez schematu i nie trzeba zarządzać dodanie dodatkowej logiki bezpośrednio do aplikacji. Pozyskiwania umożliwia Twojej zabezpieczać dane przez usprawnienie dostępu ze skryptów.

> [!TIP]
> Procedury składowane są najbardziej odpowiednie dla operacje zapisu duże i wymagają transakcji między wartość klucza partycji. Przy podejmowaniu decyzji, czy ma być używane procedury składowane, optymalizować wokół enkapsulacji maksymalną ilość możliwości zapisu. Ogólnie rzecz biorąc procedur składowanych nie są w sposób najbardziej skuteczny do wykonywania dużej liczby operacji odczytu lub zapytania, więc przy użyciu przechowywanych procedur z dużą liczbą operacji odczytu, aby powrócić do klienta usługi batch nie umożliwia uzyskanie żądanego korzyści. Aby uzyskać najlepszą wydajność te operacje odczycie powinna być podejmowana po stronie klienta, za pomocą zestawu SDK Cosmos. 

## <a name="transactions"></a>Transakcje

Transakcji w typowej bazy danych można zdefiniować za pomocą sekwencji operacji wykonywanych jako pojedynczą jednostką logiczną pracy. Każda transakcja zapewnia **gwarancje właściwości ACID**. ACID jest dobrze znanym akronimem, zawiera: **A**tomicity, **C**spójności, **I**solation, i **D**urability. 

* Niepodzielność gwarantuje, że wszystkie operacje wykonywane w obrębie transakcji są traktowane jako pojedynczą jednostkę, a: wszystkie z nich są zatwierdzone lub żadna z nich są. 

* Spójność gwarantuje, że dane są zawsze w nieprawidłowym stanie na różnych transakcji. 

* Izolacja gwarantuje, że żadne dwa transakcje kolidują ze sobą — wiele komercyjnych systemów udostępniają wiele poziomy izolacji, które mogą służyć odpowiednio do potrzeb aplikacji. 

* Trwałość gwarantuje żadnych zmian, które jest zaangażowana w bazie danych zawsze będzie istnieć.

W usłudze Azure Cosmos DB środowisko uruchomieniowe JavaScript jest hostowana w aparacie bazy danych. W związku z tym żądań wysyłanych w ramach procedury składowane i wyzwalacze są wykonywane w taki sam zakres jak sesją bazy danych. Ta funkcja umożliwia usłudze Azure Cosmos DB gwarantuje właściwości ACID dla wszystkich operacji, które są częścią procedury składowanej lub wyzwalacza. Aby uzyskać przykłady, zobacz [sposób implementowania transakcji](how-to-write-stored-procedures-triggers-udfs.md#transactions) artykułu.

### <a name="scope-of-a-transaction"></a>Zakresu transakcji

Jeśli procedura składowana jest skojarzony z kontenera usługi Azure Cosmos, procedura składowana jest wykonywana w zakresie transakcji klucza partycji logicznej. Każdego wykonania procedury składowanej musi zawierać wartość klucza partycji logicznej, która odnosi się do zakresu transakcji. Aby uzyskać więcej informacji, zobacz [partycjonowanie w usłudze Azure Cosmos DB](partition-data.md) artykułu.

### <a name="commit-and-rollback"></a>Zatwierdź i wycofywania

Transakcje są natywnie zintegrowane modelu programowania usługi Azure Cosmos DB JavaScript. W ramach funkcji JavaScript wszystkie operacje są automatycznie opakowane w pojedynczą transakcję. Jeśli logika JavaScript w procedurze składowanej zakończy się bez żadnych wyjątków, wszystkie operacje w ramach transakcji są zapisane w bazie danych. Instrukcje, takich jak `BEGIN TRANSACTION` i `COMMIT TRANSACTION` (znane relacyjnych baz danych) są niejawne w usłudze Azure Cosmos DB. Jeśli istnieją wyjątki w skrypcie, środowisko uruchomieniowe usługi Azure Cosmos DB JavaScript spowoduje przywrócenie cała transakcja. W efekcie zostanie zgłoszony wyjątek jest skutecznie równoważne `ROLLBACK TRANSACTION` w usłudze Azure Cosmos DB.

### <a name="data-consistency"></a>Spójność danych

Procedury składowane i wyzwalacze są zawsze wykonywane w replice podstawowej z kontenera usługi Azure Cosmos. Ta funkcja pozwala zagwarantować, która odczytuje z procedur składowanych oferty [wysoki poziom spójności](consistency-levels-tradeoffs.md). Zapytania przy użyciu funkcji zdefiniowanych przez użytkownika mogą być wykonywane na podstawowe lub pomocnicze repliki. Procedury składowane i wyzwalacze są przeznaczone do obsługi transakcji zapisu — w międzyczasie logiki tylko do odczytu najlepiej jest zaimplementowany jako logiki po stronie aplikacji i wysyła kwerendę za pomocą [zestawami SDK interfejsu API SQL usługi Azure Cosmos DB](sql-api-dotnet-samples.md), pomoże Ci saturate przepływności bazy danych. 

## <a name="bounded-execution"></a>Wykonanie ograniczonego

Wszystkie operacje usługi Azure Cosmos DB, należy wykonać w ramach określonego limitu czasu. To ograniczenie dotyczy funkcji języka JavaScript — procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika. Jeśli operacja nie została zakończona w wyznaczonym terminie, transakcja zostanie wycofana.

Można albo upewnij się, funkcji JavaScript Zakończ w terminie, lub zaimplementuj model oparty na kontynuację, w celu wykonywania przetwarzania wsadowego/wznowień. Aby uprościć tworzenie procedur składowanych i wyzwalaczy w celu obsługi ograniczeń czasowych, wszystkie funkcje w kontenerze usługi Azure Cosmos (na przykład, tworzenia, odczytu, aktualizacji i usuwania elementów) zwracać wartość logiczną, która informuje, czy ta operacja spowoduje Zakończ. Jeśli ta wartość wynosi false, jest wskazanie, że procedura musi zawinąć wykonywania, ponieważ skrypt zajmuje więcej czasu lub aprowizowana przepływność niż skonfigurowana wartość. Operacje w kolejce przed pierwszą operacją niezaakceptowanych magazynu są gwarantowane do wykonania, jeśli procedura składowana kończy w czasie, a nie umieszcza w kolejce kolejnych żądań. W związku z tym operacji powinno być umieszczonych w kolejce pojedynczo przez zarządzanie skryptu przepływ sterowania za pomocą konwencji wywołania zwrotnego języka JavaScript. Ponieważ skryptów są wykonywane w środowisku po stronie serwera, są one ściśle zarządzane. Skrypty, które naruszają wielokrotnie granice wykonania może być oznaczona jako nieaktywna i nie można wykonać i powinny być odtwarzane respektować granice wykonania.

Funkcje języka JavaScript są również podlegają [aprowizowana przepływność](request-units.md). Funkcje języka JavaScript może potencjalnie znajdą się dużą liczbą jednostek żądania w krótkim czasie i mogą być współczynnik ograniczonej czasowo po osiągnięciu limitu pojemności aprowizowanej przepływności. Należy zauważyć, że skryptów korzysta z dodatkowej przepływności, oprócz przepływności poświęcony na wykonywanie operacje bazy danych, mimo że te operacje bazy danych są nieco mniej kosztowne niż wykonywanie tych samych operacji klienta.

## <a name="triggers"></a>Wyzwalacze

Usługa Azure Cosmos DB obsługuje dwa typy wyzwalaczy:

### <a name="pre-triggers"></a>Wstępne wyzwalaczy

Usługa Azure Cosmos DB udostępnia wyzwalacze, które może być wywoływany przez wykonywania operacji na elemencie usługi Azure Cosmos DB. Na przykład można określić wstępne wyzwalacza, podczas tworzenia elementu. W takim przypadku wstępne wyzwalacz uruchomi przed utworzeniem elementu. Wstępne wyzwalaczy nie może mieć parametrów wejściowych. Jeśli to konieczne, obiekt żądania może służyć do aktualizacji treści dokumentu z oryginalnego żądania. Jeśli zarejestrowano wyzwalaczami, użytkownicy mogą określić operacje, które można uruchomić z. Jeśli wyzwalacz został utworzony za pomocą `TriggerOperation.Create`, oznacza to, że przy użyciu wyzwalacza w operacji zamieniania nie otrzyma zezwolenia. Aby uzyskać przykłady, zobacz [sposobu pisania wyzwalaczy](how-to-write-stored-procedures-triggers-udfs.md#triggers) artykułu.

### <a name="post-triggers"></a>Po wprowadzeniu wyzwalaczy

Podobnie jak wstępnego wyzwalaczy, po wyzwalacze, również są powiązane z operacji na elemencie usługi Azure Cosmos DB i nie wymagają one wszelkie parametry wejściowe. Zakres ich działania *po* operacja została zakończona i mieć dostęp do komunikatu odpowiedzi, które są wysyłane do klienta. Aby uzyskać przykłady, zobacz [sposobu pisania wyzwalaczy](how-to-write-stored-procedures-triggers-udfs.md#triggers) artykułu.

> [!NOTE]
> Zarejestrowany wyzwalaczy nie uruchamiaj automatycznie po ich odpowiednie operacje (Tworzenie / usuwanie / Zastąp i aktualizacji) się zdarzyć. Muszą być jawnie wywołane podczas wykonywania tych operacji. Aby dowiedzieć się więcej, zobacz [sposób uruchamiania wyzwalaczy](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) artykułu.

## <a id="udfs"></a>Funkcje zdefiniowane przez użytkownika

Funkcje zdefiniowane przez użytkownika (UDF) są używane do rozszerzania składni języka zapytań interfejsu API SQL i łatwe Implementowanie niestandardowej logiki biznesowej. One może być wywoływana tylko w ramach zapytania. Funkcje zdefiniowane przez użytkownika nie mają dostępu do obiektu kontekstu i są przeznaczone do służyć jako obliczeń tylko JavaScript. W związku z tym funkcje zdefiniowane przez użytkownika może działać w replikach pomocniczych. Aby uzyskać przykłady, zobacz [jak pisać funkcje zdefiniowane przez użytkownika](how-to-write-stored-procedures-triggers-udfs.md#udfs) artykułu.

## <a id="jsqueryapi"></a>Zapytanie o języku zintegrowanym JavaScript API

Oprócz wysyłania zapytań przy użyciu interfejsu API SQL składni zapytań, [SDK po stronie serwera](https://azure.github.io/azure-cosmosdb-js-server) umożliwia wykonywanie zapytań za pomocą interfejsu języka JavaScript, bez konieczności znajomości języka SQL. Zapytanie interfejsu API języka JavaScript umożliwia programowe tworzenie zapytań, przekazując funkcji predykatu sekwencję wywołań funkcji. Zapytania są analizowane przez środowisko uruchomieniowe JavaScript i są efektywne wykonywane w ramach usługi Azure Cosmos DB. Aby uzyskać informacje dotyczące obsługi zapytań interfejsu API języka JavaScript, zobacz [pracy z językiem JavaScript zintegrowany interfejs API zapytań](javascript-query-api.md) artykułu. Aby uzyskać przykłady, zobacz [sposobu pisania procedur składowanych i wyzwalaczy, przy użyciu interfejsu API języka Javascript zapytania](how-to-write-javascript-query-api.md) artykułu.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak pisać i za pomocą procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika w usłudze Azure Cosmos DB następujące artykuły:

* [Jak napisać procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika](how-to-write-stored-procedures-triggers-udfs.md)

* [Jak korzystać z procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika](how-to-use-stored-procedures-triggers-udfs.md)

* [Praca z języka JavaScript zintegrowany interfejs API zapytań](javascript-query-api.md)
