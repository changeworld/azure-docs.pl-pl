---
title: Praca z procedurami przechowywanymi, wyzwalaczami i plików UDF w usłudze Azure Cosmos DB
description: W tym artykule przedstawiono pojęcia, takie jak procedury przechowywane, wyzwalacze i funkcje zdefiniowane przez użytkownika w usłudze Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 23a14e7590eca6f63c92acdf6336ffaef8b54381
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065889"
---
# <a name="stored-procedures-triggers-and-user-defined-functions"></a>Procedury przechowywane, wyzwalacze i funkcje zdefiniowane przez użytkownika

Usługa Azure Cosmos DB zapewnia zintegrowane z językiem, transakcyjne wykonywanie kodu języka JavaScript. Korzystając z interfejsu API SQL w usłudze Azure Cosmos DB, można pisać **procedury przechowywane,** **wyzwalacze**i **funkcje zdefiniowane przez użytkownika (UDF)** w języku JavaScript. Logikę można napisać w języku JavaScript i wykonać ją w aparacie bazy danych. Wyzwalacze, procedury przechowywane i pliki UDF można tworzyć i wykonywać za pomocą [portalu Azure Portal](https://portal.azure.com/), interfejsu API [zintegrowanych zapytań języka JavaScript w usłudze Azure Cosmos DB](javascript-query-api.md) lub [zestawów SDK klienta interfejsu API SQL usługi Cosmos DB.](how-to-use-stored-procedures-triggers-udfs.md)

## <a name="benefits-of-using-server-side-programming"></a>Zalety korzystania z programowania po stronie serwera

Zapisywanie procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika (UDF) w języku JavaScript umożliwia tworzenie rozbudowanych aplikacji i mają następujące zalety:

* **Logika proceduralna:** JavaScript jako język programowania wysokiego poziomu, który zapewnia bogaty i znajomy interfejs do wyrażania logiki biznesowej. Można wykonać sekwencję złożonych operacji na danych.

* **Transakcje atomowe:** Usługa Azure Cosmos DB gwarantuje, że operacje bazy danych, które są wykonywane w ramach jednej procedury składowanej lub wyzwalacza są niepodzielne. Ta niepodzielna funkcjonalność umożliwia aplikacji łączenie powiązanych operacji w pojedynczej partii, tak aby wszystkie operacje zakończyć się pomyślnie lub żaden z nich zakończy się pomyślnie.

* **Wydajność:** Dane JSON są wewnętrznie mapowane do systemu typu języka JavaScript. To mapowanie umożliwia szereg optymalizacji, takich jak z opóźnieniem materializacji dokumentów JSON w puli buforów i udostępniając je na żądanie do kodu wykonawczego. Istnieją inne korzyści wydajności związane z logiką biznesowej wysyłki do bazy danych, która obejmuje:

   * *Do partii:* Operacje można grupować, takie jak wstawia i przesyłać je zbiorczo. Koszty opóźnienia ruchu sieciowego i obciążenie magazynu w celu utworzenia oddzielnych transakcji są znacznie zmniejszone.

   * *Wstępna kompilacja:* Procedury przechowywane, wyzwalacze i pliki UDF są niejawnie wstępnie skompilowane do formatu kodu bajtów w celu uniknięcia kosztów kompilacji w czasie każdego wywołania skryptu. Ze względu na wstępną kompilację, wywołanie procedur składowanych jest szybkie i ma niską powierzchnię.

   * *Sekwencjonowanie:* Czasami operacje wymagają mechanizmu wyzwalania, który może wykonać jedną lub dodatkową aktualizację danych. Oprócz niepodzielności istnieją również korzyści wydajności podczas wykonywania po stronie serwera.

* **Hermetyzacja:** Procedury przechowywane mogą służyć do grupowanie logiki w jednym miejscu. Hermetyzacja dodaje warstwę abstrakcji na górze danych, co umożliwia ewoluować aplikacje niezależnie od danych. Ta warstwa abstrakcji jest przydatne, gdy dane są bez schematu i nie trzeba zarządzać dodawanie dodatkowej logiki bezpośrednio do aplikacji. Abstrakcja umożliwia bezpieczne przechowywanie danych przez usprawnienie dostępu ze skryptów.

> [!TIP]
> Procedury przechowywane najlepiej nadają się do operacji, które są ciężkie do zapisu i wymagają transakcji w całej wartości klucza partycji. Przy podejmowaniu decyzji, czy używać procedur przechowywanych, zoptymalizować wokół hermetyzacji maksymalną ilość zapisów możliwe. Ogólnie rzecz biorąc, procedury przechowywane nie są najbardziej efektywnym środkiem do wykonywania dużej liczby operacji odczytu lub kwerendy, więc przy użyciu procedur przechowywanych do partii dużej liczby odczytów, aby powrócić do klienta nie przyniesie pożądanych korzyści. Aby uzyskać najlepszą wydajność, te operacje intensywnej pracy odczytu należy wykonać po stronie klienta przy użyciu sdk usługi Cosmos. 

## <a name="transactions"></a>Transakcje

Transakcję w typowej bazie danych można zdefiniować jako sekwencję operacji wykonywanych jako pojedyncza logiczna jednostka pracy. Każda transakcja zapewnia **gwarancje właściwości ACID**. ACID jest dobrze znany akronim, który oznacza: **Tomicity,** **C**onsistency, **I**solation, i **D**urability. 

* Niepodzielność gwarantuje, że wszystkie operacje wykonywane wewnątrz transakcji są traktowane jako pojedyncza jednostka i albo wszystkie z nich są zatwierdzone lub żaden z nich nie są. 

* Spójność zapewnia, że dane są zawsze w prawidłowym stanie między transakcjami. 

* Izolacja gwarantuje, że żadne dwie transakcje nie kolidują ze sobą — wiele systemów komercyjnych zapewnia wiele poziomów izolacji, które mogą być używane na podstawie potrzeb aplikacji. 

* Trwałość zapewnia, że wszelkie zmiany, które są zatwierdzane w bazie danych zawsze będzie obecny.

W usłudze Azure Cosmos DB środowisko wykonawcze JavaScript jest hostowane wewnątrz aparatu bazy danych. W związku z tym żądania wykonane w ramach procedur składowanych i wyzwalacze są wykonywane w tym samym zakresie co sesja bazy danych. Ta funkcja umożliwia usługi Azure Cosmos DB, aby zagwarantować właściwości ACID dla wszystkich operacji, które są częścią procedury składowanej lub wyzwalacza. Na przykład zobacz [jak zaimplementować transakcje](how-to-write-stored-procedures-triggers-udfs.md#transactions) artykułu.

### <a name="scope-of-a-transaction"></a>Zakres transakcji

Procedury przechowywane są skojarzone z kontenerem usługi Azure Cosmos, a wykonanie procedury składowanej jest ograniczone do klucza partycji logicznej. Procedury przechowywane muszą zawierać wartość klucza partycji logicznej podczas wykonywania, która definiuje partycję logiczną dla zakresu transakcji. Aby uzyskać więcej informacji, zobacz artykuł [partycjonowania usługi Azure Cosmos DB.](partition-data.md)

### <a name="commit-and-rollback"></a>Zatwierdzanie i wycofywanie

Transakcje są natywnie zintegrowane z modelem programowania JavaScript usługi Azure Cosmos DB. W ramach funkcji JavaScript wszystkie operacje są automatycznie zawijane w ramach pojedynczej transakcji. Jeśli logika JavaScript w procedurze składowanej zakończy się bez żadnych wyjątków, wszystkie operacje w ramach transakcji są zobowiązane do bazy danych. Instrukcje `BEGIN TRANSACTION` `COMMIT TRANSACTION` podobne i (znane relacyjne bazy danych) są niejawne w usłudze Azure Cosmos DB. Jeśli istnieją wyjątki od skryptu, środowisko uruchomieniowe Usługi Azure Cosmos DB JavaScript wycofa całą transakcję. Jako takie zgłaszanie wyjątku jest `ROLLBACK TRANSACTION` skutecznie równoważne w usłudze Azure Cosmos DB.

### <a name="data-consistency"></a>Spójność danych

Procedury przechowywane i wyzwalacze są zawsze wykonywane w podstawowej repliki kontenera usługi Azure Cosmos. Ta funkcja gwarantuje, że odczyty z procedur przechowywanych oferują [silną spójność.](consistency-levels-tradeoffs.md) Kwerendy przy użyciu funkcji zdefiniowanych przez użytkownika mogą być wykonywane na repliki podstawowej lub dodatkowej. Procedury przechowywane i wyzwalacze są przeznaczone do obsługi zapisów transakcyjnych — tymczasem logika tylko do odczytu jest najlepiej zaimplementowana jako logika po stronie aplikacji, a kwerendy używające [zestawów SDK interfejsu SQL usługi Azure Cosmos DB DB](sql-api-dotnet-samples.md), ułatwią nasycenie przepływności bazy danych. 

## <a name="bounded-execution"></a>Powiązane wykonywanie

Wszystkie operacje usługi Azure Cosmos DB muszą zostać ukończone w określonym czasie. To ograniczenie dotyczy funkcji JavaScript — procedur przechowywanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika. Jeśli operacja nie zostanie ukończona w tym terminie, transakcja zostanie wycofana.

Można upewnić się, że funkcje JavaScript zakończyć w terminie lub zaimplementować model oparty na kontynuacji do wykonywania partii/wznawiania. Aby uprościć opracowywanie procedur składowanych i wyzwalaczy do obsługi limitów czasowych, wszystkie funkcje w kontenerze usługi Azure Cosmos (na przykład tworzenie, odczytywanie, aktualizowanie i usuwanie elementów) zwracają wartość logiczną, która odpowiada, czy ta operacja będzie Kompletny. Jeśli ta wartość jest false, jest wskazanie, że procedura musi zakończyć wykonanie, ponieważ skrypt zużywa więcej czasu lub aprowizowana przepływność niż skonfigurowana wartość. Operacje w kolejce przed pierwszą operacją magazynu niezaakceptowane są gwarantowane do ukończenia, jeśli procedura składowana zakończy się w czasie i nie kolejkuje więcej żądań. W związku z tym operacje powinny być umieszczane w kolejce po jednym na raz przy użyciu konwencji wywołania zwrotnego JavaScript do zarządzania przepływem sterowania skryptu. Ponieważ skrypty są wykonywane w środowisku po stronie serwera, są ściśle regulowane. Skrypty, które wielokrotnie naruszają granice wykonywania mogą być oznaczone jako nieaktywne i nie mogą być wykonywane, i powinny być odtworzone w celu przestrzegania granic wykonywania.

Funkcje JavaScript również podlegają [aprowizowanej przepustowości.](request-units.md) Funkcje JavaScript może potencjalnie skończyć przy użyciu dużej liczby jednostek żądań w krótkim czasie i może być ograniczona szybkość, jeśli aprowizowana limit przepustowości zostanie osiągnięty. Należy pamiętać, że skrypty zużywają dodatkową przepływność oprócz przepływności wydanej na wykonywanie operacji bazy danych, chociaż te operacje bazy danych są nieco tańsze niż wykonywanie tych samych operacji od klienta.

## <a name="triggers"></a>Wyzwalacze

Usługa Azure Cosmos DB obsługuje dwa typy wyzwalaczy:

### <a name="pre-triggers"></a>Wyzwalacze wykonywane przed operacją

Usługa Azure Cosmos DB udostępnia wyzwalacze, które mogą być wywoływane przez wykonanie operacji w obrębie elementu usługi Azure Cosmos. Można na przykład wybrać wyzwalacz wykonywany przed operacją podczas tworzenia elementu. W takim przypadku wyzwalacz tego typu zostanie uruchomiony przed utworzeniem elementu. Wyzwalacze wykonywane przed operacją nie mogą mieć żadnych parametrów wejściowych. W razie potrzeby obiekt żądania może służyć do aktualizowania treści dokumentu z oryginalnego żądania. Podczas rejestrowania wyzwalaczy użytkownicy mogą określić operacje, z którymi można je uruchamiać. Jeśli wyzwalacz został `TriggerOperation.Create`utworzony za pomocą , oznacza to, że użycie wyzwalacza w operacji wymiany nie będzie dozwolone. Przykłady można znaleźć w artykule [Jak napisać wyzwalacze.](how-to-write-stored-procedures-triggers-udfs.md#triggers)

### <a name="post-triggers"></a>Wyzwalacze wykonywane po operacji

Podobnie jak pre-wyzwalacze, po wyzwalaczach, są również skojarzone z operacją na elemencie usługi Azure Cosmos i nie wymagają żadnych parametrów wejściowych. Są one uruchamiane *po* zakończeniu operacji i mają dostęp do komunikatu odpowiedzi, który jest wysyłany do klienta. Przykłady można znaleźć w artykule [Jak napisać wyzwalacze.](how-to-write-stored-procedures-triggers-udfs.md#triggers)

> [!NOTE]
> Zarejestrowane wyzwalacze nie działają automatycznie, gdy ich odpowiednie operacje (tworzenie / usuwanie / zamienianie / aktualizowanie) mają miejsce. Należy je jawnie wywoływać podczas wykonywania tych operacji. Aby dowiedzieć się więcej, [zobacz, jak uruchomić wyzwalacze](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) artykułu.

## <a name="user-defined-functions"></a><a id="udfs"></a>Funkcje zdefiniowane przez użytkownika

Funkcje zdefiniowane przez użytkownika (UDFs) są używane do rozszerzania składni języka zapytań interfejsu API interfejsu API i łatwego implementowania niestandardowej logiki biznesowej. Można je wywołać tylko w ramach kwerend. Pliki UDF nie mają dostępu do obiektu kontekstowego i mają być używane jako tylko javascript obliczeniowy. W związku z tym pliki UDF można uruchomić na replikach pomocniczych. Na przykład zobacz [Jak napisać funkcje zdefiniowane przez użytkownika](how-to-write-stored-procedures-triggers-udfs.md#udfs) artykułu.

## <a name="javascript-language-integrated-query-api"></a><a id="jsqueryapi"></a>Interfejs API zapytań zintegrowany z językiem JavaScript

Oprócz wystawiania zapytań przy użyciu składni zapytań interfejsu API SQL, [sdk po stronie serwera](https://azure.github.io/azure-cosmosdb-js-server) umożliwia wykonywanie zapytań przy użyciu interfejsu JavaScript bez znajomości języka SQL. Interfejs API zapytań JavaScript umożliwia programowe tworzenie zapytań przez przekazywanie funkcji predykatu do sekwencji wywołań funkcji. Zapytania są analizowane przez środowisko wykonawcze JavaScript i są wykonywane wydajnie w usłudze Azure Cosmos DB. Aby dowiedzieć się więcej o obsłudze interfejsu API zapytań javascript, zobacz [Artykuł interfejsu API zintegrowanego zapytania z językiem JavaScript.](javascript-query-api.md) Na przykład zobacz [Jak napisać procedury przechowywane i wyzwalacze przy użyciu javascript query api](how-to-write-javascript-query-api.md) artykułu.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak pisać i używać procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika w usłudze Azure Cosmos DB, korzystając z następujących artykułów:

* [Jak zapisywać procedury przechowywane, wyzwalacze i funkcje zdefiniowane przez użytkownika](how-to-write-stored-procedures-triggers-udfs.md)

* [Jak korzystać z procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika](how-to-use-stored-procedures-triggers-udfs.md)

* [Praca ze zintegrowanym interfejsem API zapytań języka JavaScript](javascript-query-api.md)
