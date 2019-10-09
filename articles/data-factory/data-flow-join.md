---
title: Przekształcanie dołączania przepływu danych Azure Data Factory
description: Przekształcanie dołączania przepływu danych Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: 48cf9d58c8acd85e545a5bcb5104d7069670e349
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029324"
---
# <a name="mapping-data-flow-join-transformation"></a>Mapowanie transformacji przepływu danych



Użyj sprzężenia, aby połączyć dane z dwóch tabel w przepływie danych. Kliknij transformację, która będzie lewą relacją i Dodaj transformację sprzężenia z przybornika. Wewnątrz transformacji sprzężenia wybierz inny strumień danych z przepływu danych, aby uzyskać odpowiednią relację.

(media/data-flow/join.png "Dołącz do") ![przekształcenia transformacji]

## <a name="join-types"></a>Typy sprzężeń

Do przekształcenia sprzężenia jest wymagane wybranie typu sprzężenia.

### <a name="inner-join"></a>Sprzężenie wewnętrzne

Sprzężenie wewnętrzne przejdzie tylko do wierszy, które pasują do warunków kolumny w obu tabelach.

### <a name="left-outer"></a>Lewe zewnętrzne

Wszystkie wiersze ze strumienia po lewej stronie, które nie spełniają warunku sprzężenia, są przekazywane, a kolumny wyjściowe z innej tabeli są ustawione na wartość NULL oprócz wszystkich wierszy zwracanych przez sprzężenie wewnętrzne.

### <a name="right-outer"></a>Prawe zewnętrzne

Wszystkie wiersze z odpowiedniego strumienia, które nie spełniają warunku sprzężenia, są przekazywane przez, a kolumny wyjściowe, które odpowiadają innej tabeli, są ustawione na wartość NULL, oprócz wszystkich wierszy zwracanych przez sprzężenie wewnętrzne.

### <a name="full-outer"></a>Pełny zewnętrzny

Pełny zewnętrzny produkuje wszystkie kolumny i wiersze z obu stron z wartościami NULL dla kolumn, które nie znajdują się w drugiej tabeli.

### <a name="cross-join"></a>Sprzężenie krzyżowe

Określ iloczyn poprzeczny dwóch strumieni z wyrażeniem. Służy do tworzenia niestandardowych warunków sprzężenia.

## <a name="specify-join-conditions"></a>Określ warunki sprzężenia

Warunek sprzężenia w lewo pochodzi ze strumienia danych połączonego z lewej strony sprzężenia. Warunek sprzężenia w prawo jest drugim strumieniem danych podłączonym do sprzężenia u dołu, który będzie bezpośrednim łącznikiem do innego strumienia lub odwołaniem do innego strumienia.

Musisz wprowadzić co najmniej 1 (1.. n) warunki sprzężenia. Mogą to być pola, do których odwołuje się bezpośrednio, wybrane z menu rozwijanego lub wyrażeń.

## <a name="join-performance-optimizations"></a>Dołączanie optymalizacji wydajności

W przeciwieństwie do łączenia scalania w narzędziach takich jak SSIS, sprzężenie w przepływie danych ADF nie jest obowiązkową operacją scalania łączenia. W związku z tym klucze Join nie muszą być sortowane jako pierwsze. Operacja join zostanie wykonana na podstawie optymalnej operacji JOIN w usłudze Spark: sprzężenie po stronie emisji/mapy:

Optymalizacja sprzężenia ![Optymalizacja](media/data-flow/joinoptimize.png "sprzężenia")

Jeśli zestaw danych może pasować do pamięci węzła procesu roboczego, możemy zoptymalizować wydajność przyłączania. Można również określić Partycjonowanie danych w operacji JOIN, aby utworzyć zestawy danych, które mogą być lepiej dopasowane do pamięci na proces roboczy.

## <a name="self-join"></a>Samosprzężenie

Możesz uzyskać warunki samosprzężenia w przepływie danych ADF przy użyciu wybierz transformację do aliasu istniejącego strumienia. Najpierw utwórz "nowe rozgałęzienie" ze strumienia, a następnie Dodaj pozycję Wybierz, aby utworzyć alias całego oryginalnego strumienia.

Samoobsługowe ![samosprzężenie](media/data-flow/selfjoin.png "")

Na powyższym diagramie wybór przekształceń znajduje się u góry. Wszystko to wykonuje aliasowanie oryginalnego strumienia do "OrigSourceBatting". W wyróżnionym przekształceniu Join poniżej można zobaczyć, że używamy tego strumienia SELECT aliasu jako sprzężenia po prawej stronie, umożliwiając nam odwoływanie się do tego samego klucza zarówno w lewej & po prawej stronie sprzężenia wewnętrznego.

## <a name="composite-and-custom-keys"></a>Klucze złożone i niestandardowe

Możesz tworzyć niestandardowe i złożone klucze na bieżąco wewnątrz transformacji sprzężenia. Dodaj wiersze dla dodatkowych kolumn sprzężenia ze znakiem plus (+) obok każdego wiersza relacji. Lub Oblicz nową wartość klucza w Konstruktorze wyrażeń dla wartości sprzężenia na bieżąco.

## <a name="next-steps"></a>Następne kroki

Po dołączeniu danych można [tworzyć nowe kolumny](data-flow-derived-column.md) i [odujścia danych do docelowego magazynu danych](data-flow-sink.md).
