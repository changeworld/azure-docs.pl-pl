---
title: Łatwo skopiować dane z Kreatora kopiowania - Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu korzystania z Kreatora kopiowania fabryki danych można skopiować danych z obsługiwanych źródeł danych do ujścia.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: f904972f-cd33-48db-9755-2b3196ae4168
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 1ef94f5cfa91422dddf9c684c2dc1b6eada754f1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621296"
---
# <a name="copy-or-move-data-easily-with-azure-data-factory-copy-wizard"></a>Skopiować lub przenieść dane z Kreatora kopiowania fabryki danych Azure
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi Data Factory, która jest w wersji zapoznawczej, zapoznaj się z [dokumentacją samouczka działania kopiowania w wersji 2](../quickstart-create-data-factory-dot-net.md). 


Kreator kopiowania fabryki danych Azure jest upraszczają proces wprowadzania danych, który zazwyczaj jest to pierwszy krok w scenariuszu integracji danych na całej trasie. Podczas przechodzenia za pomocą Kreatora kopiowania fabryki danych Azure, nie należy zrozumieć żadnych definicji JSON połączonej usługi, zestawy danych i potoki. Jednak po ukończeniu wszystkich czynności kreatora, Kreator automatycznie tworzy potoku można skopiować danych z wybranego źródła danych do wybranej lokalizacji docelowej. Ponadto Kreator kopiowania ułatwia sprawdzania poprawności danych jest pozyskanych w czasie tworzenia, która zapisuje większość czasu, szczególnie gdy użytkownik są pobierania danych po raz pierwszy ze źródła danych. Aby uruchomić Kreatora kopiowania, kliknij przycisk **skopiować dane** kafelka na stronie głównej w fabryce danych.

![Kreator kopiowania](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="an-intuitive-wizard-for-copying-data"></a>Intuicyjne Kreator kopiowania danych
Ten kreator pozwala łatwo przenosić dane z różnych źródeł do miejsc docelowych w minutach. Po przejściu przez kreatora, potoku z działaniem kopiowania jest utworzony automatycznie wraz z zależnych jednostek fabryki danych (połączonych usług i zbiory danych). Żadne dodatkowe kroki są wymagane do utworzenia potoku.   

![Wybierz źródło danych](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Zobacz [Samouczek Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) artykułu, aby uzyskać instrukcje krok po kroku utworzyć potok próbki można skopiować obiektu blob danych z platformy Azure do tabeli bazy danych SQL Azure. 
> 
> 

Kreator zaprojektowano z danymi big data na uwadze od początku. To proste i efektywne tworzenie fabryki danych potoki, łączące setki folderów, plików lub tabel za pomocą Kreatora kopiowania danych. Kreator obsługuje następujących trzech funkcji: Podgląd danych, przechwytywania schematu i mapowania i filtrowanie danych. 

## <a name="automatic-data-preview"></a>Podgląd danych
Kreator kopiowania umożliwia przeglądanie część danych z wybranego źródła danych można sprawdzić, czy dane są odpowiednie dane, które chcesz skopiować. Ponadto jeśli źródło danych jest w pliku tekstowym, Kreator kopiowania analizuje pliku tekstowego, aby dowiedzieć się więcej wierszy i kolumn ograniczniki oraz schemat automatycznie. 

![Ustawienia formatu pliku](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Przechwytywanie schematu i mapowanie
Schemat danych wejściowych może nie odpowiadać schemat danych wyjściowych w niektórych przypadkach. W tym scenariuszu należy mapować kolumn z schematu źródła do kolumn z schematu docelowego. 

Kreator kopiowania mapuje automatycznie kolumn w schemacie źródła do kolumn w schemacie docelowego. Można zastąpić mapowania za pomocą listy rozwijanej (lub) określ, czy kolumny musi być pominięte podczas kopiowania danych.   

![Mapowanie schematu](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrowanie danych
Kreator umożliwia filtrowanie źródła danych w celu wybrania tylko dane, które muszą mają być kopiowane do docelowej/ujścia magazynu danych. Filtrowanie zmniejsza ilość danych, które ma zostać skopiowany do ujścia magazynu danych i w związku z tym zwiększa przepływność operacji kopiowania. Zapewnia elastyczny sposób, aby odfiltrować dane relacyjnej bazy danych przy użyciu SQL zapytań języka (lub) plików w folderze obiektów blob platformy Azure przy użyciu [funkcji fabryki danych i zmiennych](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtrowanie danych w bazie danych
W tym przykładzie zapytanie SQL używa `Text.Format` funkcji i `WindowStart` zmiennej. 

![Sprawdzanie poprawności wyrażenia](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtrowanie danych w folderze obiektów blob platformy Azure
Zmienne w ścieżce folderu służy do kopiowania danych z folderu, który jest określana w czasie wykonywania na podstawie [zmienne systemowe](data-factory-functions-variables.md#data-factory-system-variables). Są obsługiwane zmienne: **{year}**, **{month}**, **{day}**, **{godzina}**, **{minutę}**, i **{niestandardowych}**. Przykład: inputfolder / {year} / {month} / {day}.

Załóżmy, że podania folderów w następującym formacie:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Kliknij przycisk **Przeglądaj** przycisk dla **pliku lub folderu**, przejdź do jednej z tych folderów (na przykład 2016 -> 03 -> 01 -> 02) i kliknij przycisk **wybierz**. Powinny pojawić się `2016/03/01/02` w polu tekstowym. Teraz, Zastąp **2016** z **{year}**, **03** z **{month}**, **01** z **{day}**, i **02** z **{godzina}**, i naciśnij klawisz Tab. Powinny pojawić się listy rozwijanej, aby wybrać format tych zmiennych:

![Używanie zmiennych systemu](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Jak pokazano na poniższym zrzucie ekranu, można również użyć **niestandardowych** zmiennej i wszystkie [obsługiwane ciągi formatujące](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Aby wybrać folder o tej struktury, użyj **Przeglądaj** najpierw przycisk. Następnie zastąp wartości z **{niestandardowych}**, i naciśnij klawisz Tab, aby zobaczyć pole tekstowe, w którym można wpisać ciąg formatu.     

![Użycie niestandardowej zmiennej](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="support-for-diverse-data-and-object-types"></a>Obsługa różnych danych i typów obiektów
Za pomocą Kreatora kopiowania, może wydajnie przenosić setki folderów, plików i tabele.

![Wybierz tabele, z którego można skopiować danych](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

## <a name="scheduling-options"></a>Opcje harmonogramu
Można wykonać operacji kopiowania raz lub zgodnie z harmonogramem (co godzinę, codziennie, i tak dalej). Obie te opcje mogą służyć do szerokość łączników między lokalnymi, chmurze i lokalnej kopii pulpitu.

Operacja kopiowania jednorazowe umożliwia przenoszenie danych ze źródła do miejsca docelowego tylko raz. Ma to zastosowanie do danych o dowolnym rozmiarze i dowolnego obsługiwanego formatu. Zaplanowanych kopii umożliwia kopiowanie danych na wyznaczonych cyklu. Zaawansowane ustawienia (np. Ponów próbę, limit czasu i alertami) umożliwia konfigurowanie zaplanowanych kopii.

![Planowanie właściwości](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Kolejne kroki
Przewodnik szybkiego za pomocą Kreatora kopiowania fabryki danych można utworzyć potoku o aktywności kopiowania, zobacz [samouczek: tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md).

