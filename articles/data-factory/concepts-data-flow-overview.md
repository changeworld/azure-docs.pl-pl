---
title: Mapowanie przepływów danych w Azure Data Factory | Microsoft Docs
description: Omówienie mapowania przepływów danych w Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/7/2019
ms.openlocfilehash: 8d0ad794caee8a06c8d403a981037d6560fb3f43
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030128"
---
# <a name="what-are-mapping-data-flows"></a>Czym są przepływy danych mapowania?

Mapowanie przepływów danych to wizualnie zaprojektowane przekształcenia danych w Azure Data Factory. Przepływy danych umożliwiają inżynierom danych Tworzenie logiki transformacji danych graficznych bez pisania kodu. Wyniki przepływów danych są wykonywane jako działania w ramach potoków Azure Data Factory przy użyciu skalowanych klastrów Spark. Działania związane z przepływem danych mogą być operacyjne za pośrednictwem istniejących Data Factory planowania, kontroli, przepływu i monitorowania.

Mapowanie przepływów danych zapewnia w pełni wizualne środowisko, bez konieczności kodowania. Przepływy danych będą wykonywane na własnym klastrze wykonawczym na potrzeby przetwarzania danych skalowanych w poziomie. Azure Data Factory obsługuje wszystkie tłumaczenia kodu, optymalizację ścieżki i wykonywanie zadań przepływu danych.

## <a name="getting-started"></a>Wprowadzenie

Aby utworzyć przepływ danych, kliknij znak plus w obszarze zasoby fabryki. 

Nowy przepływ(media/data-flow/newdataflow2.png "danych") nowego ![przepływu danych]

Spowoduje to przejście do kanwy przepływu danych, w której można utworzyć logikę transformacji. Kliknij pole "Dodaj Źródło", aby rozpocząć konfigurowanie transformacji źródłowej. Aby uzyskać więcej informacji, zobacz [Źródło transformacji](data-flow-source.md).

## <a name="data-flow-canvas"></a>Kanwa przepływu danych

Kanwa przepływu danych jest podzielony na trzy części: górny pasek, wykres i panel konfiguracja. 

![Canvas](media/data-flow/canvas1.png "Canvas")

### <a name="graph"></a>Graf

Wykres przedstawia strumień transformacji. Pokazuje on dane źródłowe w miarę ich przepływu w jednym lub większej liczbie zlewów. Aby dodać nowe źródło, kliknij pole "Dodaj Źródło". Aby dodać nową transformację, kliknij znak plus w prawym dolnym rogu istniejącej transformacji.

![Canvas](media/data-flow/canvas2.png "Canvas")

### <a name="configuration-panel"></a>Panel konfiguracji

Panel konfiguracja przedstawia ustawienia specyficzne dla aktualnie wybranego przekształcenia lub, jeśli nie wybrano transformacji, przepływ danych. W ogólnej konfiguracji przepływu danych można edytować nazwę i opis na karcie **Ogólne** lub dodać parametry za pomocą karty **Parametry** . Aby uzyskać więcej informacji, zobacz [Mapowanie parametrów przepływu danych](parameters-data-flow.md).

Każda transformacja ma co najmniej cztery karty konfiguracji:

#### <a name="transformation-settings"></a>Ustawienia transformacji

Pierwsza karta w okienku Konfiguracja każdej transformacji zawiera ustawienia specyficzne dla tej transformacji. Więcej informacji można znaleźć na stronie dokumentacji przekształcenia.

Karta ![Ustawienia źródła]karty(media/data-flow/source1.png "ustawienia źródłowe")

#### <a name="optimize"></a>Optymalizowanie

Karta _Optymalizacja_ zawiera ustawienia umożliwiające skonfigurowanie schematów partycjonowania.

![Optymalizuj](media/data-flow/optimize1.png "optymalizację")

Ustawieniem domyślnym jest "Użyj bieżącego partycjonowania", które instruuje Azure Data Factory, aby użyć schematu partycjonowania natywnego dla przepływów danych uruchomionych na platformie Spark. W większości scenariuszy zaleca się zastosowanie tego ustawienia.

Istnieją wystąpienia, w których można dostosować partycjonowanie. Na przykład jeśli chcesz wyprowadzić przekształcenia do pojedynczego pliku w Lake, wybierz pozycję "pojedyncza partycja" w transformacji ujścia.

Innym przypadkiem, w którym można kontrolować schematy partycjonowania, jest Optymalizacja wydajności. Dostosowanie partycjonowania zapewnia kontrolę nad dystrybucją danych między węzłami obliczeniowymi i optymalizacją lokalizacji danych, które mogą mieć pozytywne i negatywne skutki dla ogólnej wydajności przepływu danych. Aby uzyskać więcej informacji, zobacz [Przewodnik po wydajności przepływu danych](concepts-data-flow-performance.md).

Aby zmienić partycjonowanie na dowolnym przekształceniu, kliknij kartę Optymalizacja i wybierz przycisk radiowy Ustaw partycjonowanie. Następnie zostanie wyświetlona seria opcji partycjonowania. Najlepsza Metoda partycjonowania różni się w zależności od ilości danych, kluczy kandydujących, wartości null i kardynalności. Najlepszym rozwiązaniem jest rozpoczęcie od domyślnego partycjonowania, a następnie wypróbowanie innych opcji partycjonowania. Można testować przy użyciu przebiegów debugowania potoku i wyświetlać czas wykonywania oraz korzystać z partycji w każdym grupowaniu transformacji z widoku monitorowanie. Aby uzyskać więcej informacji, zobacz [monitorowanie przepływów danych](concepts-data-flow-monitoring.md).

Poniżej przedstawiono dostępne opcje partycjonowania.

##### <a name="round-robin"></a>Działanie okrężne 

Round Robin to prosta partycja, która automatycznie dystrybuuje dane równomiernie między partycjami. Użyj działania okrężnego, gdy nie masz dobrych kandydatów kluczowych do wdrożenia pełnej, inteligentnej strategii partycjonowania. Można ustawić liczbę partycji fizycznych.

##### <a name="hash"></a>Skrót

Azure Data Factory spowoduje wygenerowanie skrótu kolumn w celu utworzenia jednorodnych partycji, takich jak wiersze o podobnych wartościach, będą się znajdować w tej samej partycji. W przypadku korzystania z opcji skrótu Sprawdź możliwe pochylenie partycji. Można ustawić liczbę partycji fizycznych.

##### <a name="dynamic-range"></a>Zakres dynamiczny

Zakres dynamiczny będzie używać dynamicznych zakresów platformy Spark na podstawie określonych kolumn lub wyrażeń. Można ustawić liczbę partycji fizycznych. 

##### <a name="fixed-range"></a>Stały zakres

Utwórz wyrażenie, które udostępnia stały zakres dla wartości w kolumnach danych partycjonowanych. Przed użyciem tej opcji należy dobrze zrozumieć swoje dane, aby uniknąć pochylenia partycji. Wartości wprowadzone dla wyrażenia będą używane jako część funkcji partycji. Można ustawić liczbę partycji fizycznych.

##### <a name="key"></a>Klucz

Jeśli masz dobrą wiedzę o kardynalności danych, partycjonowanie kluczy może być dobrą strategią partycji. Partycjonowanie kluczy spowoduje utworzenie partycji dla każdej unikatowej wartości w kolumnie. Nie można ustawić liczby partycji, ponieważ liczba będzie oparta na unikatowych wartościach danych.

#### <a name="inspect"></a>Skontrol

Karta _Inspekcja_ umożliwia wyświetlenie metadanych strumienia danych, który jest przekształcany. Widoczne są liczby kolumn, zmienione kolumny, kolumny dodane, typy danych, porządkowanie kolumn i odwołania do kolumn. Inspekcja to widok metadanych w trybie tylko do odczytu. Nie musisz mieć włączonego trybu debugowania, aby wyświetlić metadane w okienku Inspekcja.

![Inspekcja](media/data-flow/inspect1.png "inspekcji")

Gdy zmienisz kształt danych za pomocą transformacji, przepływ zmian metadanych zostanie wyświetlony w okienku Inspekcja. Jeśli w transformacji źródłowej nie ma zdefiniowanego schematu, metadane nie będą widoczne w okienku Inspekcja. Brak metadanych jest powszechny w scenariuszach dryfowania schematu.

#### <a name="data-preview"></a>Podgląd danych

Jeśli tryb debugowania jest włączony, karta _Podgląd danych_ zapewnia interaktywną migawkę danych w każdej transformacji. Aby uzyskać więcej informacji, zobacz [Podgląd danych w trybie debugowania](concepts-data-flow-debug-mode.md#data-preview).

### <a name="top-bar"></a>Górny pasek

Górny pasek zawiera akcje, które mają wpływ na cały przepływ danych, takie jak zapisywanie i walidacja. Można również przełączać się między trybami wykresu i konfiguracji za pomocą przycisków **Pokaż wykres** i **Ukryj wykres** .

![Ukryj]wykres(media/data-flow/hideg.png "Ukryj Graf")

W przypadku ukrycia grafu można przechodzić między węzłami transformacji później za pomocą przycisków **Wstecz** i **dalej** .

![Nawigowanie]po(media/data-flow/showhide.png "nawigacji")

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak utworzyć [transformację źródłową](data-flow-source.md)
* Dowiedz się, jak tworzyć przepływy danych w [trybie debugowania](concepts-data-flow-debug-mode.md)
