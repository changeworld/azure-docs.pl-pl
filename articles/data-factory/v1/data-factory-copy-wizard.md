---
title: Łatwe kopiowanie danych za pomocą Kreatora kopiowania — Azure
description: Dowiedz się, jak skopiować dane z obsługiwanych źródeł danych do pochłaniania za pomocą Kreatora kopiowania danych.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: f904972f-cd33-48db-9755-2b3196ae4168
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8b872cc30ae66e83274f189138dad6d609e3f536
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74927055"
---
# <a name="copy-or-move-data-easily-with-azure-data-factory-copy-wizard"></a>Łatwe kopiowanie lub przenoszenie danych za pomocą Kreatora kopiowania danych usługi Azure
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli korzystasz z bieżącej wersji usługi Data Factory, zobacz [samouczek dotyczący działania kopiowania](../quickstart-create-data-factory-dot-net.md). 


Kreator kopiowania danych azure jest ułatwienie procesu pozyskiwania danych, który jest zwykle pierwszym krokiem w scenariuszu integracji danych end-to-end. Podczas przechodzenia przez Kreatora kopiowania fabryki danych platformy Azure nie trzeba rozumieć żadnych definicji JSON dla połączonych usług, zestawów danych i potoków. Jednak po wykonaniu wszystkich kroków w kreatorze kreator automatycznie tworzy potok do kopiowania danych z wybranego źródła danych do wybranego miejsca docelowego. Ponadto Kreator kopiowania pomaga sprawdzić poprawność danych, które są pozyskiwane w momencie tworzenia, co pozwala zaoszczędzić dużo czasu, szczególnie podczas połkwania danych po raz pierwszy ze źródła danych. Aby uruchomić Kreatora kopiowania, kliknij kafelek **Kopiuj dane** na stronie głównej fabryki danych.

![Kreator kopiowania](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="an-intuitive-wizard-for-copying-data"></a>Intuicyjny kreator kopiowania danych
Ten kreator umożliwia łatwe przenoszenie danych z różnych źródeł do miejsc docelowych w ciągu kilku minut. Po przejściu przez kreatora potok z działaniem kopiowania jest tworzony automatycznie wraz z zależnymi jednostkami fabryki danych (połączone usługi i zestawy danych). Do utworzenia potoku nie są wymagane żadne dodatkowe kroki.   

![Wybieranie źródła danych](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Zobacz artykuł [samouczek Kreatora kopiowania,](data-factory-copy-data-wizard-tutorial.md) aby uzyskać instrukcje krok po kroku, aby utworzyć przykładowy potok do kopiowania danych z obiektu blob platformy Azure do tabeli bazy danych SQL platformy Azure. 
> 
> 

Kreator został zaprojektowany z myślą o dużych zbiorach danych od samego początku. Tworzenie potoków fabryki danych, które przenoszą setki folderów, plików lub tabel za pomocą kreatora Kopiowanie danych, jest proste i wydajne. Kreator obsługuje następujące trzy funkcje: Automatyczny podgląd danych, przechwytywanie i mapowanie schematu oraz filtrowanie danych. 

## <a name="automatic-data-preview"></a>Automatyczny podgląd danych
Kreator kopiowania umożliwia przeglądanie części danych z wybranego źródła danych w celu sprawdzenia, czy dane są właściwymi danymi, które chcesz skopiować. Ponadto jeśli dane źródłowe są w pliku tekstowym, kreator kopiowania analizuje plik tekstowy, aby dowiedzieć się ograniczników wierszy i kolumn, a schemat automatycznie. 

![Ustawienia formatu pliku](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Przechwytywanie i mapowanie schematu
Schemat danych wejściowych może w niektórych przypadkach nie odpowiadać schematowi danych wyjściowych. W tym scenariuszu należy mapować kolumny ze schematu źródłowego do kolumn ze schematu docelowego. 

Kreator kopiowania automatycznie mapuje kolumny w schemacie źródłowym do kolumn w schemacie docelowym. Mapowania można zastąpić za pomocą list rozwijanych (lub) określić, czy kolumna musi zostać pominięta podczas kopiowania danych.   

![Mapowanie schematu](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrowanie danych
Kreator umożliwia filtrowanie danych źródłowych w celu wybrania tylko danych, które muszą zostać skopiowane do magazynu danych docelowych/ujścia. Filtrowanie zmniejsza ilość danych do skopiowania do magazynu danych ujścia i w związku z tym zwiększa przepływność operacji kopiowania. Zapewnia elastyczny sposób filtrowania danych w relacyjnej bazie danych przy użyciu języka zapytań SQL (lub) plików w folderze obiektów blob platformy Azure przy użyciu [funkcji i zmiennych usługi Data Factory.](data-factory-functions-variables.md)   

### <a name="filtering-of-data-in-a-database"></a>Filtrowanie danych w bazie danych
W przykładzie kwerendy SQL `Text.Format` używa `WindowStart` funkcji i zmiennej. 

![Sprawdzanie poprawności wyrażeń](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtrowanie danych w folderze obiektów blob platformy Azure
Za pomocą zmiennych w ścieżce folderu można kopiować dane z folderu określonego w czasie wykonywania na podstawie [zmiennych systemowych](data-factory-functions-variables.md#data-factory-system-variables). Obsługiwane zmienne to: **{year}**, **{month}**, **{day}**, **{hour}**, **{minute}** i **{custom}**. Przykład: inputfolder/{year}/{month}/{day}.

Załóżmy, że masz foldery wejściowe w następującym formacie:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Kliknij przycisk **Przeglądaj** **plik lub folder**, przejdź do jednego z tych folderów (na przykład 2016->03->01->02) i kliknij przycisk **Wybierz**. Powinieneś `2016/03/01/02` zobaczyć w polu tekstowym. Teraz zastąp **rok 2016** **{rok}**, **03** **{miesiąc}**, **01** **{day}** i **02** **{hour}** i naciśnij klawisz Tab. Listy rozwijane powinny zostać wyświetlone, aby wybrać format tych czterech zmiennych:

![Korzystanie ze zmiennych systemowych](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Jak pokazano na poniższym zrzucie ekranu, można również użyć zmiennej **niestandardowej** i [dowolnych obsługiwanych ciągów formatu](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Aby wybrać folder z tą strukturą, należy najpierw użyć przycisku **Przeglądaj.** Następnie zastąp wartość **{custom}** i naciśnij klawisz Tab, aby wyświetlić pole tekstowe, w którym można wpisać ciąg formatu.     

![Korzystanie ze zmiennej niestandardowej](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="support-for-diverse-data-and-object-types"></a>Obsługa różnych typów danych i obiektów
Za pomocą Kreatora kopiowania można skutecznie przenosić setki folderów, plików lub tabel.

![Wybieranie tabel, z których mają być kopiowane dane](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

## <a name="scheduling-options"></a>Opcje planowania
Operację kopiowania można uruchomić raz lub zgodnie z harmonogramem (co godzinę, codziennie i tak dalej). Obie te opcje mogą służyć do szerokości łączników w środowisku lokalnym, chmurze i lokalnej kopii pulpitu.

Operacja kopiowania jednorazowego umożliwia przenoszenie danych ze źródła do miejsca docelowego tylko raz. Ma ona zastosowanie do danych o dowolnym rozmiarze i dowolnym obsługiwanym formacie. Zaplanowana kopia umożliwia kopiowanie danych na zalecanym cyklu. Do skonfigurowania zaplanowanej kopii można użyć ustawień rozszerzonych (takich jak ponawianie próby, limit czasu i alerty).

![Właściwości planowania](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Następne kroki
Aby uzyskać szybki instruktaż tworzenia potoku za pomocą Kreatora kopiowania danych za pomocą funkcji Kopiowanie, zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md).

