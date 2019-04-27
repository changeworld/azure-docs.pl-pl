---
title: Kreator kopiowania usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o sposobie używania Kreatora kopiowania usługi Azure Data Factory do kopiowania danych z obsługiwanych źródeł danych do ujścia.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 0974eb40-db98-4149-a50d-48db46817076
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d13e304b0d10e8bd34d306426f1f9164bcc6be94
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60567677"
---
# <a name="azure-data-factory-copy-wizard"></a>Kreator kopiowania usługi Azure Data Factory
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. 

Kreatora kopiowania usługi Azure Data Factory ułatwia proces pozyskiwania danych, zwykle jest to pierwszy krok w scenariuszu integracji danych end-to-end. Podczas przechodzenia za pomocą Kreatora kopiowania usługi Azure Data Factory, nie musisz zrozumieć żadnych definicji JSON dla połączonej usługi, zestawy danych i potoki. Kreator automatycznie tworzy potok, aby skopiować dane z wybranego źródła danych w wybranym miejscu docelowym. Ponadto Kreator kopiowania ułatwia sprawdzanie poprawności danych w trakcie pozyskiwania w czasie tworzenia. Pozwala to zaoszczędzić czas, szczególnie gdy można są dane wprowadzane po raz pierwszy ze źródła danych. Aby uruchomić Kreatora kopiowania, kliknij przycisk **kopiowanie danych** kafelków na stronie głównej fabryki danych.

![Kreator kopiowania](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="designed-for-big-data"></a>Przeznaczona dla danych big data
Ten kreator umożliwia łatwe przenoszenie danych z wielu źródeł do miejsc docelowych w ciągu kilku minut. Po przejściu w Kreatorze potoku z działaniem kopiowania jest tworzony automatycznie, wraz z zależnych jednostek usługi Data Factory (połączone usług i zestawów danych). Żadne dodatkowe kroki są wymagane do utworzenia potoku.   

![Wybieranie źródła danych](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Aby uzyskać instrukcje krok po kroku instrukcje tworzenia przykładowego potoku w celu skopiowania danych z usługi Azure blob do tabeli usługi Azure SQL Database, zobacz [samouczku Kreator kopiowania](data-factory-copy-data-wizard-tutorial.md).
>
>

Kreator zaprojektowano z danymi big data na uwadze od samego początku obsługę zróżnicowanych danych i typów obiektów. Można tworzyć potoków usługi Data Factory, które przenoszenia setek folderów, pliki lub tabele. Kreator obsługuje automatyczne danych w wersji zapoznawczej, przechwytywania schematu i mapowanie i filtrowania danych.

## <a name="automatic-data-preview"></a>Automatyczne dane (wersja zapoznawcza)
Możesz wyświetlić podgląd część dane z wybranego źródła danych, aby sprawdzić, czy dane mają zostać skopiowane. Ponadto jeśli źródło danych znajduje się w pliku tekstowym, Kreator kopiowania analizuje plik tekstowy, aby dowiedzieć się więcej wierszy i kolumn ograniczniki i schemat automatycznie.

![Ustawienia formatu pliku](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Przechwytywanie schematu i mapowanie
Schemat danych wejściowych nie może być zgodny ze schematem, danych wyjściowych, w niektórych przypadkach. W tym scenariuszu należy mapować kolumny z schematu źródła kolumn ze schematu docelowego.

> [!TIP]
> Podczas kopiowania danych z programu SQL Server lub usługi Azure SQL Database do usługi Azure SQL Data Warehouse, jeśli tabela nie istnieje w magazynie docelowym, Data Factory obsługuje automatyczne tworzenie tabeli przy użyciu schematu źródła. Dowiedz się więcej z [przenoszenie danych do i z usługi Azure SQL Data Warehouse przy użyciu usługi Azure Data Factory](./data-factory-azure-sql-data-warehouse-connector.md).
>

Użyj listy rozwijanej, aby wybrać kolumny z schematu źródła, aby zamapować do kolumny schematu docelowego. Kreator kopiowania próbuje zrozumieć swoje wzorzec mapowania kolumn. Ma to zastosowanie tego samego wzorca do pozostałych kolumn, dzięki czemu nie musisz wybrać każdej z kolumn pojedynczo, aby wykonać mapowanie schematu. Jeśli wolisz, można zastąpić te mapowania, za pomocą list rozwijanych mapowania kolumn jedno po drugim. Wzorzec staje się bardziej precyzyjne mapy większą liczbę kolumn. Kreator kopiowania stale aktualizuje wzorzec, a ostatecznie osiągnie prawo wzorzec mapowania kolumn, które chcesz osiągnąć.     

![mapowanie schematu](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrowanie danych
Można filtrować dane źródłowe, aby wybrać tylko dane, które muszą być kopiowane do magazynu danych ujścia. Filtrowanie zmniejsza ilość danych, które mają być kopiowane do magazynu danych ujścia i w związku z tym zwiększa przepływność operacji kopiowania. Oferuje elastyczny sposób, aby filtrować dane w relacyjnej bazie danych za pomocą języka zapytania SQL lub pliki w folderze obiektu blob platformy Azure przy użyciu [zmiennych i funkcji usługi fabryka danych](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtrowanie danych w bazie danych
Poniższy zrzut ekranu przedstawia zapytania SQL za pomocą `Text.Format` funkcji i `WindowStart` zmiennej.

![Sprawdź poprawność wyrażenia](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtrowanie danych w folderze obiektu blob platformy Azure
Zmienne w ścieżce folderu służy do kopiowania danych z folderu, który jest określana w czasie wykonywania na podstawie [zmiennych systemowych](data-factory-functions-variables.md#data-factory-system-variables). Są obsługiwane zmienne: **{year}**, **{month}**, **{dzień}**, **{godzina}**, **{minuta}** i **{niestandardowych}**. Na przykład: inputfolder / {year} / {month} / {day}.

Załóżmy, że należy wprowadzić folderów w następującym formacie:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Kliknij przycisk **Przeglądaj** przycisku **pliku lub folderu**, przejdź do jednej z tych folderów (na przykład, 2016 -> 03 -> 01 -> 02) i kliknij przycisk **wybierz**. Powinien zostać wyświetlony `2016/03/01/02` w polu tekstowym. Teraz Zastąp **2016** z **{year}**, **03** z **{month}**, **01** z **{dzień}** , i **02** z **{godzina}** i naciśnij klawisz **kartę** klucza. Powinny zostać wyświetlone list rozwijanych, aby wybrać format dla tych czterech zmiennych:

![Za pomocą zmiennych systemowych](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Jak pokazano na poniższym zrzucie ekranu, można również użyć **niestandardowe** zmiennej i wszystkie [obsługiwanych ciągów formatu](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Aby wybrać folder o tej struktury, użyj **Przeglądaj** najpierw przycisk. Następnie zastąp wartość składającą się z **{niestandardowych}** i naciśnij klawisz **kartę** klawisz, aby zobaczyć pole tekstowe, w którym można wpisać w formacie ciągu.     

![Korzystanie ze zmienną niestandardową](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="scheduling-options"></a>Opcje harmonogramu
Możesz uruchomić operacji kopiowania raz lub zgodnie z harmonogramem (co godzinę, codziennie, i tak dalej). Obie te opcje może służyć do całodobowej łączników w środowiskach, w tym w środowisku lokalnym, chmurze i lokalną kopię pulpitu.

Operacja jednorazowa kopia umożliwia przenoszenie danych ze źródła do miejsca docelowego tylko raz. Dotyczy to dane, dowolnego rozmiaru i dowolnego obsługiwanego formatu. Zaplanowaną kopię umożliwia kopiowanie danych w wyznaczonym cyklu. Ustawienia zaawansowane (np. Ponów próbę, limit czasu i alertami) umożliwiają skonfigurowanie zaplanowaną kopię.

![Właściwości harmonogramu](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać szybki Przewodnik umożliwia tworzenie potoku za pomocą działania kopiowania przy użyciu Kreatora kopiowania usługi Data Factory, zobacz [samouczka: Tworzenie potoku przy użyciu Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md).
