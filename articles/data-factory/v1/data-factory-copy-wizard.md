---
title: Łatwo skopiować dane przy użyciu Kreatora kopiowania — Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu używania Kreatora kopiowania usługi Data Factory do kopiowania danych z obsługiwanych źródeł danych do ujścia.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: f904972f-cd33-48db-9755-2b3196ae4168
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 90f78428601d7b039d00d39c1ca8339ab3ace9ba
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60487984"
---
# <a name="copy-or-move-data-easily-with-azure-data-factory-copy-wizard"></a>Kopiowanie lub przenoszenie danych za pomocą Kreatora kopiowania usługi Azure Data Factory
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli korzystasz z bieżącej wersji usługi Data Factory, zobacz [samouczek dotyczący działania kopiowania](../quickstart-create-data-factory-dot-net.md). 


Kreatora kopiowania usługi Azure Data Factory jest jej obsługi ułatwiają realizację procesu pozyskiwania danych, zwykle jest to pierwszy krok w scenariuszu integracji danych end-to-end. Podczas przechodzenia za pomocą Kreatora kopiowania usługi Azure Data Factory, nie musisz zrozumieć żadnych definicji JSON dla połączonej usługi, zestawy danych i potoki. Jednak po ukończeniu wszystkich czynności kreatora, Kreator automatycznie tworzy potok, aby skopiować dane z wybranego źródła danych w wybranym miejscu docelowym. Ponadto Kreator kopiowania ułatwia sprawdzanie poprawności danych w czasie tworzenia zawartości, w trakcie pozyskiwania, co pozwoli zaoszczędzić wiele czasu, szczególnie gdy można są dane wprowadzane po raz pierwszy ze źródła danych. Aby uruchomić Kreatora kopiowania, kliknij przycisk **kopiowanie danych** kafelków na stronie głównej fabryki danych.

![Kreator kopiowania](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="an-intuitive-wizard-for-copying-data"></a>Intuicyjny Kreator kopiowania danych
Ten kreator umożliwia łatwe przenoszenie danych z wielu źródeł do miejsc docelowych w ciągu kilku minut. Po przejściu w kreatorze, potok z działaniem kopiowania jest automatycznie tworzone wraz z zależnych jednostek usługi Data Factory (połączone usług i zestawów danych). Żadne dodatkowe kroki są wymagane do utworzenia potoku.   

![Wybieranie źródła danych](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Zobacz [samouczku Kreator kopiowania](data-factory-copy-data-wizard-tutorial.md) artykuł, aby uzyskać instrukcje krok po kroku instrukcje tworzenia przykładowego potoku do skopiowania danych z usługi Azure blob do tabeli usługi Azure SQL Database. 
> 
> 

Kreator zaprojektowano z danymi big data na uwadze od samego początku. Jest proste i efektywne tworzenie potoków usługi Data Factory, które przenoszenia setek folderów, pliki lub tabele za pomocą Kreatora kopiowania danych. Kreator obsługuje następujących trzech funkcji: Automatyczne danych w wersji zapoznawczej, przechwytywania schematu i mapowanie i filtrowanie danych. 

## <a name="automatic-data-preview"></a>Automatyczne dane (wersja zapoznawcza)
Kreator kopiowania umożliwia przeglądanie część dane z wybranego źródła danych należy sprawdzić, czy dane są odpowiednie dane, które mają zostać skopiowane. Ponadto jeśli źródło danych znajduje się w pliku tekstowym, Kreator kopiowania analizuje plik tekstowy, aby dowiedzieć się więcej wierszy i kolumn ograniczniki i schemat automatycznie. 

![Ustawienia formatu pliku](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Przechwytywanie schematu i mapowanie
Schemat danych wejściowych nie może być zgodny ze schematem, danych wyjściowych, w niektórych przypadkach. W tym scenariuszu należy mapować kolumny z schematu źródła kolumn ze schematu docelowego. 

Kreator kopiowania automatycznie mapuje kolumn w schemacie źródła do kolumn w schemacie docelowego. Można zastąpić mapowania za pomocą listy rozwijanej (lub) określ, czy kolumna musi być pominięte podczas kopiowania danych.   

![mapowanie schematu](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrowanie danych
Kreator umożliwia filtrowanie danych źródłowych, aby wybrać tylko dane, które muszą być skopiowane do docelowego/ujścia magazynu danych. Filtrowanie zmniejsza ilość danych, które mają być kopiowane do magazynu danych ujścia i w związku z tym zwiększa przepływność operacji kopiowania. Zapewnia elastyczny sposób, aby filtrować dane w relacyjnej bazie danych przy użyciu SQL zapytanie języka (lub) plików w folderze obiektu blob platformy Azure przy użyciu [zmiennych i funkcji usługi fabryka danych](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtrowanie danych w bazie danych
W tym przykładzie korzysta z zapytania SQL `Text.Format` funkcji i `WindowStart` zmiennej. 

![Sprawdź poprawność wyrażenia](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtrowanie danych w folderze obiektu blob platformy Azure
Zmienne w ścieżce folderu służy do kopiowania danych z folderu, który jest określana w czasie wykonywania na podstawie [zmiennych systemowych](data-factory-functions-variables.md#data-factory-system-variables). Są obsługiwane zmienne: **{year}** , **{month}** , **{dzień}** , **{godzina}** , **{minuta}** i **{niestandardowych}** . Przykład: inputfolder / {year} / {month} / {day}.

Załóżmy, że należy wprowadzić folderów w następującym formacie:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Kliknij przycisk **Przeglądaj** przycisku **pliku lub folderu**, przejdź do jednej z tych folderów (na przykład, 2016 -> 03 -> 01 -> 02) i kliknij przycisk **wybierz**. Powinien zostać wyświetlony `2016/03/01/02` w polu tekstowym. Teraz Zastąp **2016** z **{year}** , **03** z **{month}** , **01** z **{dzień}** , i **02** z **{godzina}** , i naciśnij klawisz Tab. Powinny zostać wyświetlone list rozwijanych, aby wybrać format dla tych czterech zmiennych:

![Za pomocą zmiennych systemowych](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Jak pokazano na poniższym zrzucie ekranu, można również użyć **niestandardowe** zmiennej i wszystkie [obsługiwanych ciągów formatu](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Aby wybrać folder o tej struktury, użyj **Przeglądaj** najpierw przycisk. Następnie zastąp wartość składającą się z **{niestandardowych}** , i naciśnij klawisz Tab, aby wyświetlić pole tekstowe, w którym można wpisać w formacie ciągu.     

![Korzystanie ze zmienną niestandardową](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="support-for-diverse-data-and-object-types"></a>Obsługa różnych danych i typy obiektów
Za pomocą Kreatora kopiowania, może wydajnie przenosić setki folderów, pliki lub tabele.

![Wybieranie tabel do skopiowania danych](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

## <a name="scheduling-options"></a>Opcje harmonogramu
Możesz uruchomić operacji kopiowania raz lub zgodnie z harmonogramem (co godzinę, codziennie, i tak dalej). Obie te opcje może służyć do całodobowej łączników w środowisku lokalnym, chmurze i lokalną kopię pulpitu.

Operacja jednorazowa kopia umożliwia przenoszenie danych ze źródła do miejsca docelowego tylko raz. Dotyczy to dane, dowolnego rozmiaru i dowolnego obsługiwanego formatu. Zaplanowaną kopię umożliwia kopiowanie danych w wyznaczonym cyklu. Ustawienia zaawansowane (np. Ponów próbę, limit czasu i alertami) umożliwiają skonfigurowanie zaplanowaną kopię.

![Właściwości harmonogramu](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać szybki Przewodnik umożliwia tworzenie potoku za pomocą działania kopiowania przy użyciu Kreatora kopiowania usługi Data Factory, zobacz [samouczka: Tworzenie potoku przy użyciu Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md).

