---
title: Kreator kopiowania Azure fabryki danych | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu korzystania z Kreatora kopiowania Azure fabryki danych można skopiować danych z obsługiwanych źródeł danych do ujścia.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 0974eb40-db98-4149-a50d-48db46817076
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 6330854f4ee9b2db1597d916c815bb6646da6a29
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37052753"
---
# <a name="azure-data-factory-copy-wizard"></a>Kreator kopiowania fabryki danych Azure
> [!NOTE]
> Ten artykuł dotyczy wersji 1 fabryki danych. 

Kreator kopiowania fabryki danych Azure ułatwia proces wprowadzania danych, który zazwyczaj jest to pierwszy krok w scenariuszu integracji danych na całej trasie. Podczas przechodzenia za pomocą Kreatora kopiowania fabryki danych Azure, nie należy zrozumieć żadnych definicji JSON połączonej usługi, zestawy danych i potoki. Kreator automatycznie tworzy potoku można skopiować danych z wybranego źródła danych do wybranej lokalizacji docelowej. Ponadto Kreator kopiowania pomaga sprawdzić poprawności danych jest pozyskanych w czasie tworzenia. Pozwala to zaoszczędzić czas, szczególnie gdy użytkownik są pobierania danych po raz pierwszy ze źródła danych. Aby uruchomić Kreatora kopiowania, kliknij przycisk **skopiować dane** kafelka na stronie głównej w fabryce danych.

![Kreator kopiowania](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="designed-for-big-data"></a>Przeznaczone dla danych big data
Ten kreator pozwala łatwo przenosić dane z różnych źródeł do miejsc docelowych w minutach. Po przejściu w Kreatorze potoku z działaniem kopiowania jest tworzony automatycznie, oraz jednostki zależne fabryki danych (połączonych usług i zestawy danych). Żadne dodatkowe kroki są wymagane do utworzenia potoku.   

![Wybieranie źródła danych](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Aby uzyskać instrukcje utworzyć potok próbki można skopiować danych z platformy Azure blob do tabeli bazy danych SQL Azure, zobacz [Samouczek Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md).
>
>

Kreator zaprojektowano z danymi big data na uwadze od początku, obsługę różnych danych i typów obiektów. Można tworzyć potoki fabryki danych, łączące setki folderów, plików i tabele. Kreator obsługuje Podgląd danych, przechwytywania schematu i mapowania i filtrowanie danych.

## <a name="automatic-data-preview"></a>Podgląd danych
Można wyświetlić podgląd część danych z wybranego źródła danych, aby zweryfikować, czy dane mają zostać skopiowane. Ponadto jeśli źródło danych jest w pliku tekstowym, Kreator kopiowania analizuje pliku tekstowego, aby dowiedzieć się więcej wierszy i kolumn ograniczniki i schemat automatycznie.

![Ustawienia formatu pliku](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Przechwytywanie schematu i mapowanie
Schemat danych wejściowych może nie odpowiadać schemat danych wyjściowych w niektórych przypadkach. W tym scenariuszu należy mapować kolumn z schematu źródła do kolumn z schematu docelowego.

> [!TIP]
> Podczas kopiowania danych z serwera SQL lub bazy danych SQL Azure do usługi Azure SQL Data Warehouse, jeśli tabela nie istnieje w magazynie docelowym, fabryki danych obsługuje automatycznego tworzenia tabel za pomocą schematu źródła. Dowiedz się więcej o [przenoszenie danych do i z usługi Azure SQL Data Warehouse przy użyciu fabryki danych Azure](./data-factory-azure-sql-data-warehouse-connector.md).
>

Użyj listy rozwijanej, aby wybrać kolumnę z schematu źródła do mapowania kolumn w schemacie docelowego. Kreator kopiowania próbuje zrozumieć deseniu dla mapowania kolumn. Ma to zastosowanie tego samego wzorca z resztą kolumny, dzięki czemu nie musisz wybrać każdej kolumny pojedynczo, aby ukończyć mapowania schematu. Jeśli wolisz, można zastąpić te mapowania za pomocą listy rozwijanej do mapowania kolumn. Wzorzec staje się bardziej dokładne mapy większą liczbę kolumn. Kreator kopiowania stale aktualizuje wzorzec, a ostatecznie osiągnie wzorcem prawo mapowania kolumn, które chcesz osiągnąć.     

![Mapowanie schematu](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrowanie danych
Można filtrować dane źródłowe, aby wybrać tylko dane, które musi być skopiowany do ujścia magazynu danych. Filtrowanie zmniejsza ilość danych, które ma zostać skopiowany do ujścia magazynu danych i w związku z tym zwiększa przepływność operacji kopiowania. Oferuje elastyczny sposób, aby odfiltrować dane relacyjnej bazy danych przy użyciu języka zapytań SQL lub pliki w folderze obiektów blob platformy Azure przy użyciu [funkcji fabryki danych i zmiennych](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtrowanie danych w bazie danych
Poniższy zrzut ekranu przedstawia zapytania SQL za pomocą `Text.Format` funkcji i `WindowStart` zmiennej.

![Sprawdzanie poprawności wyrażenia](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtrowanie danych w folderze obiektów blob platformy Azure
Zmienne w ścieżce folderu służy do kopiowania danych z folderu, który jest określana w czasie wykonywania na podstawie [zmienne systemowe](data-factory-functions-variables.md#data-factory-system-variables). Są obsługiwane zmienne: **{year}**, **{month}**, **{day}**, **{godzina}**, **{minutę}** i **{niestandardowych}**. Na przykład: inputfolder / {year} / {month} / {day}.

Załóżmy, że podania folderów w następującym formacie:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Kliknij przycisk **Przeglądaj** przycisk dla **pliku lub folderu**, przejdź do jednej z tych folderów (na przykład 2016 -> 03 -> 01 -> 02) i kliknij przycisk **wybierz**. Powinny pojawić się `2016/03/01/02` w polu tekstowym. Teraz, Zastąp **2016** z **{year}**, **03** z **{month}**, **01** z **{day}** , i **02** z **{godzina}** i naciśnij klawisz **kartę** klucza. Powinny pojawić się listy rozwijanej, aby wybrać format tych zmiennych:

![Używanie zmiennych systemu](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Jak pokazano na poniższym zrzucie ekranu, można również użyć **niestandardowych** zmiennej i wszystkie [obsługiwane ciągi formatujące](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Aby wybrać folder o tej struktury, użyj **Przeglądaj** najpierw przycisk. Następnie zastąp wartości z **{niestandardowych}** i naciśnij klawisz **kartę** klucza, aby zobaczyć pole tekstowe, w którym można wpisać ciąg formatu.     

![Użycie niestandardowej zmiennej](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="scheduling-options"></a>Opcje harmonogramu
Można wykonać operacji kopiowania raz lub zgodnie z harmonogramem (co godzinę, codziennie, i tak dalej). Obie te opcje może służyć do szerokości łączników w środowiskach, w tym lokalnych, chmury i lokalnej kopii pulpitu.

Operacja kopiowania jednorazowe umożliwia przenoszenie danych ze źródła do miejsca docelowego tylko raz. Ma to zastosowanie do danych o dowolnym rozmiarze i dowolnego obsługiwanego formatu. Zaplanowanych kopii umożliwia kopiowanie danych na wyznaczonych cyklu. Zaawansowane ustawienia (np. Ponów próbę, limit czasu i alertami) umożliwia konfigurowanie zaplanowanych kopii.

![Planowanie właściwości](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Kolejne kroki
Przewodnik szybkiego za pomocą Kreatora kopiowania fabryki danych można utworzyć potoku o aktywności kopiowania, zobacz [samouczek: tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md).
