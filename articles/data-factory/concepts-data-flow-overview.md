---
title: Mapowanie przepływów danych w Azure Data Factory | Microsoft Docs
description: Omówienie mapowania przepływów danych w Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/7/2019
ms.openlocfilehash: 94bde7b2e2a6f3902d83de90b06638035fd34397
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72679124"
---
# <a name="what-are-mapping-data-flows"></a>Co to jest mapowanie przepływów danych?

Mapowanie przepływów danych to wizualnie zaprojektowane przekształcenia danych w Azure Data Factory. Przepływy danych umożliwiają inżynierom danych Tworzenie logiki transformacji danych graficznych bez pisania kodu. Wyniki przepływów danych są wykonywane jako działania w ramach potoków Azure Data Factory, które używają skalowanych klastrów Spark. Działania związane z przepływem danych mogą być operacyjne za pośrednictwem istniejących Data Factory planowania, kontroli, przepływu i monitorowania.

Mapowanie przepływów danych zapewnia w pełni wizualne środowisko, bez konieczności kodowania. Przepływy danych zostaną uruchomione we własnym klastrze wykonawczym na potrzeby przetwarzania danych skalowanych w poziomie. Azure Data Factory obsługuje wszystkie tłumaczenia kodu, optymalizację ścieżki i wykonywanie zadań przepływu danych.

## <a name="getting-started"></a>Wprowadzenie

Aby utworzyć przepływ danych, wybierz znak plus w obszarze **zasoby fabryki**, a następnie wybierz pozycję **przepływ danych**. 

![Nowy przepływ danych](media/data-flow/newdataflow2.png "Nowy przepływ danych")

Spowoduje to przejście do kanwy przepływu danych, w której można utworzyć logikę transformacji. Wybierz pozycję **Dodaj źródło** , aby rozpocząć konfigurowanie transformacji źródłowej. Aby uzyskać więcej informacji, zobacz [Źródło transformacji](data-flow-source.md).

## <a name="data-flow-canvas"></a>Kanwa przepływu danych

Kanwa przepływu danych jest podzielony na trzy części: górny pasek, wykres i panel konfiguracja. 

![Przestrzeń](media/data-flow/canvas1.png "Przestrzeń")

### <a name="graph"></a>Graf

Wykres przedstawia strumień transformacji. Pokazuje on dane źródłowe w miarę ich przepływu w jednym lub większej liczbie zlewów. Aby dodać nowe źródło, wybierz pozycję **Dodaj źródło**. Aby dodać nową transformację, wybierz znak plus w prawym dolnym rogu istniejącej transformacji.

![Przestrzeń](media/data-flow/canvas2.png "Przestrzeń")

### <a name="configuration-panel"></a>Panel konfiguracji

Panel konfiguracja przedstawia ustawienia specyficzne dla aktualnie wybranego przekształcenia. Jeśli żadna transformacja nie jest zaznaczona, przepływ danych zostanie wyświetlony. W ogólnej konfiguracji przepływu danych można edytować nazwę i opis na karcie **Ogólne** lub dodać parametry za pomocą karty **Parametry** . Aby uzyskać więcej informacji, zobacz [Mapowanie parametrów przepływu danych](parameters-data-flow.md).

Każda transformacja ma co najmniej cztery karty konfiguracyjne.

#### <a name="transformation-settings"></a>Ustawienia transformacji

Pierwsza karta w okienku Konfiguracja każdej transformacji zawiera ustawienia specyficzne dla tej transformacji. Aby uzyskać więcej informacji, zobacz stronę dokumentacji przekształcenia.

![Karta Ustawienia źródła](media/data-flow/source1.png "Karta Ustawienia źródła")

#### <a name="optimize"></a>Optymalizowanie

Karta **Optymalizacja** zawiera ustawienia umożliwiające skonfigurowanie schematów partycjonowania.

![Optymalizacja](media/data-flow/optimize1.png "Optymalizowanie")

Ustawieniem domyślnym jest **użycie bieżącego partycjonowania**, które instruuje Azure Data Factory, aby używać schematu partycjonowania natywnego dla przepływów danych uruchomionych w systemie Spark. W większości scenariuszy zalecamy to ustawienie.

Istnieją wystąpienia, w których można chcieć dostosować partycjonowanie. Na przykład jeśli chcesz wyprowadzić przekształcenia do pojedynczego pliku w Lake, wybierz **jedną partycję** w transformacji ujścia.

Innym przypadkiem, gdzie warto kontrolować schematy partycjonowania, jest Optymalizacja wydajności. Dostosowanie partycjonowania zapewnia kontrolę nad dystrybucją danych między węzłami obliczeniowymi i optymalizacją lokalizacji danych, które mogą mieć pozytywne i negatywne skutki dla ogólnej wydajności przepływu danych. Aby uzyskać więcej informacji, zobacz [Przewodnik po wydajności przepływu danych](concepts-data-flow-performance.md).

Aby zmienić partycjonowanie na dowolnym przekształceniu, wybierz kartę **Optymalizacja** i wybierz przycisk radiowy **Ustaw partycjonowanie** . Następnie zostanie wyświetlona seria opcji partycjonowania. Najlepsza Metoda partycjonowania różni się w zależności od ilości danych, kluczy kandydujących, wartości null i kardynalności. 

Najlepszym rozwiązaniem jest rozpoczęcie od domyślnego partycjonowania, a następnie wypróbowanie innych opcji partycjonowania. Testy można testować przy użyciu przebiegów debugowania potoku, a także wyświetlać czas wykonywania i korzystać z partycji w każdym grupowaniu transformacji z widoku monitorowanie. Aby uzyskać więcej informacji, zobacz [monitorowanie przepływów danych](concepts-data-flow-monitoring.md).

Dostępne są następujące opcje partycjonowania.

##### <a name="round-robin"></a>Działania okrężne 

Round Robin to prosta partycja, która automatycznie dystrybuuje dane równomiernie między partycjami. Użyj działania okrężnego, gdy nie masz dobrych kandydatów kluczowych do wdrożenia pełnej, inteligentnej strategii partycjonowania. Można ustawić liczbę partycji fizycznych.

##### <a name="hash"></a>Skrót

Azure Data Factory spowoduje wygenerowanie skrótu kolumn w celu utworzenia jednorodnych partycji, takich jak wiersze o podobnych wartościach, będą się znajdować w tej samej partycji. W przypadku korzystania z opcji skrótu Sprawdź możliwe pochylenie partycji. Można ustawić liczbę partycji fizycznych.

##### <a name="dynamic-range"></a>Zakres dynamiczny

Zakres dynamiczny będzie używać dynamicznych zakresów platformy Spark na podstawie określonych kolumn lub wyrażeń. Można ustawić liczbę partycji fizycznych. 

##### <a name="fixed-range"></a>Stały zakres

Utwórz wyrażenie, które udostępnia stały zakres dla wartości w kolumnach danych partycjonowanych. Aby uniknąć pochylenia partycji, przed użyciem tej opcji należy dobrze zrozumieć swoje dane. Wartości wprowadzone dla wyrażenia będą używane jako część funkcji partycji. Można ustawić liczbę partycji fizycznych.

##### <a name="key"></a>Klucz

Jeśli masz dobrą wiedzę o kardynalności danych, partycjonowanie kluczy może być dobrą strategią. Partycjonowanie kluczy spowoduje utworzenie partycji dla każdej unikatowej wartości w kolumnie. Nie można ustawić liczby partycji, ponieważ liczba będzie oparta na unikatowych wartościach danych.

#### <a name="inspect"></a>Skontrol

Karta **Inspekcja** umożliwia wyświetlenie metadanych strumienia danych, który jest przekształcany. Widoczne są liczby kolumn, zmienione kolumny, kolumny dodane, typy danych, porządkowanie kolumn i odwołania do kolumn. **Inspekcja** to widok metadanych w trybie tylko do odczytu. Nie musisz mieć włączonego trybu debugowania, aby wyświetlić metadane w okienku **Inspekcja** .

![Skontrol](media/data-flow/inspect1.png "Skontrol")

Gdy zmienisz kształt danych za pomocą transformacji, przepływ zmian metadanych zostanie wyświetlony w okienku **Inspekcja** . Jeśli w transformacji źródłowej nie ma zdefiniowanego schematu, metadane nie będą widoczne w okienku **Inspekcja** . Brak metadanych jest powszechny w scenariuszach dryfowania schematu.

#### <a name="data-preview"></a>Podgląd danych

Jeśli tryb debugowania jest włączony, karta **Podgląd danych** zapewnia interaktywną migawkę danych w każdej transformacji. Aby uzyskać więcej informacji, zobacz [Podgląd danych w trybie debugowania](concepts-data-flow-debug-mode.md#data-preview).

### <a name="top-bar"></a>Górny pasek

Górny pasek zawiera akcje, które mają wpływ na cały przepływ danych, takie jak zapisywanie i walidacja. Można również przełączać się między trybami wykresu i konfiguracji za pomocą przycisków **Pokaż wykres** i **Ukryj wykres** .

![Ukryj wykres](media/data-flow/hideg.png "Ukryj wykres")

W przypadku ukrycia grafu można przeglądać węzły transformacji później za pomocą przycisków **Wstecz** i **dalej** .

![Przyciski poprzednie i następne](media/data-flow/showhide.png "przyciski poprzednie i następne")

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak utworzyć [transformację źródłową](data-flow-source.md).
* Dowiedz się, jak tworzyć przepływy danych w [trybie debugowania](concepts-data-flow-debug-mode.md).
