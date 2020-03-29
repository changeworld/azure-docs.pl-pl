---
title: Kreator kopiowania usługi Azure fabryki danych
description: Dowiedz się, jak używać Kreatora kopiowania usługi Azure Factory do kopiowania danych z obsługiwanych źródeł danych do pochłaniania.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 0974eb40-db98-4149-a50d-48db46817076
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: fef9059700e2bd94029c40bb819870a7174e0812
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930114"
---
# <a name="azure-data-factory-copy-wizard"></a>Kreator kopiowania danych fabrycznych platformy Azure
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. 

Kreator kopiowania danych azure ułatwia proces pozyskiwania danych, który jest zwykle pierwszym krokiem w scenariuszu integracji danych end-to-end. Podczas przechodzenia przez Kreatora kopiowania fabryki danych platformy Azure nie trzeba rozumieć żadnych definicji JSON dla połączonych usług, zestawów danych i potoków. Kreator automatycznie tworzy potok do kopiowania danych z wybranego źródła danych do wybranego miejsca docelowego. Ponadto Kreator kopiowania pomaga sprawdzić poprawność danych, które są pozyskiwania w momencie tworzenia. Pozwala to zaoszczędzić czas, szczególnie podczas pozyskiwania danych po raz pierwszy ze źródła danych. Aby uruchomić Kreatora kopiowania, kliknij kafelek **Kopiuj dane** na stronie głównej fabryki danych.

![Kreator kopiowania](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="designed-for-big-data"></a>Zaprojektowane z myślą o dużych zbiorach danych
Ten kreator umożliwia łatwe przenoszenie danych z różnych źródeł do miejsc docelowych w ciągu kilku minut. Po przejściu przez kreatora potok z działaniem kopiowania jest tworzony automatycznie dla Ciebie, wraz z zależnymi jednostkami fabryki danych (połączone usługi i zestawy danych). Do utworzenia potoku nie są wymagane żadne dodatkowe kroki.   

![Wybieranie źródła danych](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Aby uzyskać instrukcje krok po kroku dotyczące tworzenia przykładowego potoku w celu skopiowania danych z obiektu blob platformy Azure do tabeli bazy danych SQL azure, zobacz [samouczek Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md).
>
>

Kreator został zaprojektowany z myślą o dużych zbiorach danych od samego początku, z obsługą różnych typów danych i obiektów. Można tworzyć potoki fabryki danych, które przenoszą setki folderów, plików lub tabel. Kreator obsługuje automatyczny podgląd danych, przechwytywanie i mapowanie schematu oraz filtrowanie danych.

## <a name="automatic-data-preview"></a>Automatyczny podgląd danych
Można wyświetlić podgląd części danych z wybranego źródła danych, aby sprawdzić, czy dane są tym, co chcesz skopiować. Ponadto jeśli dane źródłowe są w pliku tekstowym, Kreator kopiowania analizuje plik tekstowy, aby automatycznie nauczyć się ograniczników wierszy i kolumn oraz schematu.

![Ustawienia formatu pliku](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Przechwytywanie i mapowanie schematu
Schemat danych wejściowych może w niektórych przypadkach nie odpowiadać schematowi danych wyjściowych. W tym scenariuszu należy mapować kolumny ze schematu źródłowego do kolumn ze schematu docelowego.

> [!TIP]
> Podczas kopiowania danych z programu SQL Server lub usługi Azure SQL Database do usługi Azure SQL Data Warehouse, jeśli tabela nie istnieje w magazynie docelowym, usługa Data Factory obsługuje automatyczne tworzenie tabel przy użyciu schematu źródłowego. Dowiedz się więcej z [przenoszenia danych do i z usługi Azure SQL Data Warehouse przy użyciu usługi Azure Data Factory](./data-factory-azure-sql-data-warehouse-connector.md).
>

Użyj listy rozwijanej, aby wybrać kolumnę ze schematu źródłowego, aby zamapować kolumnę w schemacie docelowym. Kreator kopiowania próbuje zrozumieć wzorzec mapowania kolumn. Stosuje ten sam wzorzec do pozostałych kolumn, dzięki czemu nie trzeba wybierać każdej z kolumn indywidualnie, aby ukończyć mapowanie schematu. Jeśli wolisz, możesz zastąpić te mapowania za pomocą list rozwijanych do mapowania kolumn jeden po drugim. Wzór staje się bardziej dokładne, jak mapować więcej kolumn. Kreator kopiowania stale aktualizuje wzorzec i ostatecznie osiąga odpowiedni wzorzec dla mapowania kolumn, które chcesz osiągnąć.     

![Mapowanie schematu](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrowanie danych
Można filtrować dane źródłowe, aby wybrać tylko dane, które muszą zostać skopiowane do magazynu danych ujścia. Filtrowanie zmniejsza ilość danych do skopiowania do magazynu danych ujścia i w związku z tym zwiększa przepływność operacji kopiowania. Zapewnia elastyczny sposób filtrowania danych w relacyjnej bazie danych przy użyciu języka zapytań SQL lub plików w folderze obiektów blob platformy Azure przy użyciu [funkcji i zmiennych usługi Data Factory.](data-factory-functions-variables.md)   

### <a name="filtering-of-data-in-a-database"></a>Filtrowanie danych w bazie danych
Poniższy zrzut ekranu przedstawia `Text.Format` kwerendę `WindowStart` SQL przy użyciu funkcji i zmiennej.

![Sprawdzanie poprawności wyrażeń](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtrowanie danych w folderze obiektów blob platformy Azure
Za pomocą zmiennych w ścieżce folderu można kopiować dane z folderu określonego w czasie wykonywania na podstawie [zmiennych systemowych](data-factory-functions-variables.md#data-factory-system-variables). Obsługiwane zmienne to: **{year}**, **{month}**, **{day}**, **{hour}**, **{minute}** i **{custom}**. Na przykład: inputfolder/{year}/{month}/{day}.

Załóżmy, że masz foldery wejściowe w następującym formacie:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Kliknij przycisk **Przeglądaj** **plik lub folder**, przejdź do jednego z tych folderów (na przykład 2016->03->01->02) i kliknij przycisk **Wybierz**. Powinieneś `2016/03/01/02` zobaczyć w polu tekstowym. Teraz zastąp **rok 2016** **{rok},** **03** **{miesiąc}**, **01** **{day}** i **02** **{hour}** i naciśnij klawisz **Tab.** Listy rozwijane powinny zostać wyświetlone, aby wybrać format tych czterech zmiennych:

![Korzystanie ze zmiennych systemowych](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Jak pokazano na poniższym zrzucie ekranu, można również użyć zmiennej **niestandardowej** i [dowolnych obsługiwanych ciągów formatu](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Aby wybrać folder z tą strukturą, należy najpierw użyć przycisku **Przeglądaj.** Następnie zastąp wartość **{custom}** i naciśnij klawisz **Tab,** aby wyświetlić pole tekstowe, w którym można wpisać ciąg formatu.     

![Korzystanie ze zmiennej niestandardowej](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="scheduling-options"></a>Opcje planowania
Operację kopiowania można uruchomić raz lub zgodnie z harmonogramem (co godzinę, codziennie i tak dalej). Obie te opcje mogą służyć do szerokości łączników w środowiskach, w tym w środowisku lokalnym, chmurze i lokalnej kopii pulpitu.

Operacja kopiowania jednorazowego umożliwia przenoszenie danych ze źródła do miejsca docelowego tylko raz. Ma ona zastosowanie do danych o dowolnym rozmiarze i dowolnym obsługiwanym formacie. Zaplanowana kopia umożliwia kopiowanie danych na zalecanym cyklu. Do skonfigurowania zaplanowanej kopii można użyć ustawień rozszerzonych (takich jak ponawianie próby, limit czasu i alerty).

![Właściwości planowania](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Następne kroki
Aby uzyskać szybki instruktaż tworzenia potoku za pomocą Kreatora kopiowania danych za pomocą funkcji Kopiowanie, zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md).
