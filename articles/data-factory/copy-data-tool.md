---
title: Narzędzie do kopiowania danych usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Informacje na temat narzędzia do kopiowania danych w interfejsie użytkownika usługi Azure Data Factory
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/18/2018
ms.author: yexu
ms.openlocfilehash: 107687c785433f81870449d1445136b5148a4d2c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60787737"
---
# <a name="copy-data-tool-in-azure-data-factory"></a>Narzędzie do kopiowania danych w usłudze Azure Data Factory
Narzędzie kopiowania fabryki danych Azure ułatwia i optymalizuje proces pozyskiwania danych w usłudze data lake, zwykle jest to pierwszy krok w scenariuszu integracji danych end-to-end.  Można zaoszczędzić czas, szczególnie przy za pomocą usługi Azure Data Factory pozyskiwania danych ze źródła danych po raz pierwszy. Niektóre z korzyści z używania tego narzędzia to:

- Podczas korzystania z narzędzia Azure Data Factory kopiowania danych, nie potrzebujesz rozumiesz definicji usługi Data Factory dla połączonych usług, zestawów danych, potoki, działania i wyzwalacze. 
- Przepływ narzędzia do kopiowania danych jest intuicyjna dotyczące ładowania danych do usługi data lake. Narzędzie automatycznie utworzy wszystkie wymagane zasoby usługi Data Factory do kopiowania danych z magazynu wybranego źródła danych do magazynu danych wybranego docelowego/ujścia. 
- Narzędzie do kopiowania danych ułatwia sprawdzanie poprawności danych, pobieranym w czasie tworzenia zawartości, co pomaga uniknąć wszelkich potencjalnych błędów na początku sam.
- Jeśli musisz zaimplementować złożoną logikę biznesową do ładowania danych do usługi data lake możesz edytować zasoby usługi Data Factory, utworzone przez narzędzie do kopiowania danych za pomocą tworzenia dla działania w programie interfejs użytkownika usługi Data Factory. 

W poniższej tabeli przedstawiono wskazówki dotyczące kiedy należy używać narzędzia do kopiowania danych, a dla działania tworzenia w programie interfejs użytkownika usługi Data Factory: 

| Narzędzie do kopiowania danych | Na tworzenie działanie (działanie kopiowania) |
| -------------- | -------------------------------------- |
| Aby w łatwy sposób tworzyć ładowaniem zadania bez zapoznaniu się z jednostek usługi Azure Data Factory (połączone usługi, zestawy danych, potoki itp.) | Chcesz zaimplementować logikę złożone i elastyczne dotyczące ładowania danych do lake. |
| Chcesz szybko załadować dużą liczbę artefaktów danych do usługi data lake. | Możesz chcieć połączyć w łańcuch działania kopiowania przy użyciu kolejne czynności czyszczenia lub przetwarzania danych. |

Aby uruchomić narzędzie do kopiowania danych, kliknij przycisk **kopiowania danych** kafelków na stronie głównej fabryki danych.

![Stronie wprowadzenie — link do narzędzia do kopiowania danych](./media/copy-data-tool/get-started-page.png)


## <a name="intuitive-flow-for-loading-data-into-a-data-lake"></a>Intuicyjne przepływu dotyczące ładowania danych do usługi data lake
To narzędzie umożliwia łatwe przenoszenie danych z wielu różnych źródeł do miejsc docelowych w ciągu kilku minut za pomocą intuicyjnego przepływu:  

1. Skonfiguruj ustawienia dla **źródła**.
2. Skonfiguruj ustawienia dla **docelowy**. 
3. Konfigurowanie **Zaawansowane ustawienia** dla operacji kopiowania, takie jak mapowanie kolumn, ustawienia wydajności i odporności na uszkodzenia ustawienia. 
4. Określ **harmonogram** dla zadań ładowania danych. 
5. Przegląd **podsumowania** jednostek usługi fabryka danych ma zostać utworzony. 
6. **Edytuj** potoku można zaktualizować ustawień dla działania kopiowania, zgodnie z potrzebami. 

   To narzędzie jest przeznaczony z danymi big data na uwadze od samego początku obsługę zróżnicowanych danych i typów obiektów. Służy on do przenoszenia setek folderów, pliki lub tabele. Narzędzie obsługuje automatyczne danych w wersji zapoznawczej, przechwytywania schematu i automatyczne mapowanie i danych filtrowania także.

![Narzędzie do kopiowania danych](./media/copy-data-tool/copy-data-tool.png)

## <a name="automatic-data-preview"></a>Automatyczne dane (wersja zapoznawcza)
Można wyświetlać podgląd część danych z magazynu danych wybranego źródła, co pozwala na sprawdzanie poprawności danych, które są kopiowane. Ponadto jeśli źródło danych znajduje się w pliku tekstowym, narzędzia do kopiowania danych analizuje plik tekstowy, aby automatycznie wykrywać ograniczników wiersza i kolumny, a schemat.

![Ustawienia pliku](./media/copy-data-tool/file-format-settings.png)

Po wykryciu:

![Wykryto plik ustawień i (wersja zapoznawcza)](./media/copy-data-tool/after-detection.png)

## <a name="schema-capture-and-automatic-mapping"></a>Przechwytywanie schematu i automatyczne mapowanie
Schematu źródła danych nie może być taka sama jak schemat miejsce docelowe danych w wielu przypadkach. W tym scenariuszu należy mapować kolumny z schematu źródła kolumn ze schematu docelowego.

Narzędzie do kopiowania danych monitoruje i uzyskuje informacje o swoje zachowanie, gdy mapowanie kolumn między magazynami źródłowym i docelowym. Po wybierz jedną lub kilka kolumn z magazynu danych źródła i mapowania ich na schemat docelowy, narzędzia do kopiowania danych rozpoczyna się do analizowania wzorzec pary kolumn, wybrany przez Ciebie z obu stron. Następnie dotyczy ona tego samego wzorca rest kolumn. W związku z tym zobaczysz wszystkie kolumny zostały zmapowane do miejsca docelowego w sposób, w jaki chcesz zaraz po kilku kliknięć.  Jeśli nie jesteś zadowolony z możliwością wybrania mapowanie kolumn określone przez narzędzie do kopiowania danych, możesz go zignorować i kontynuować ręcznie mapowania kolumn. W tym samym czasie narzędzia do kopiowania danych ciągle uczy się i aktualizuje wzorzec i ostatecznie osiągnie prawo wzorzec mapowania kolumn, które chcesz osiągnąć. 

> [!NOTE]
> Podczas kopiowania danych z programu SQL Server lub usługi Azure SQL Database do usługi Azure SQL Data Warehouse, jeśli tabela nie istnieje w magazynie docelowym, narzędzia do kopiowania danych obsługuje tworzenie tabeli automatycznie przy użyciu schematu źródła. 

## <a name="filter-data"></a>Filtrowanie danych
Można filtrować dane źródłowe, aby wybrać tylko dane, które muszą być kopiowane do magazynu danych ujścia. Filtrowanie zmniejsza ilość danych, które mają być kopiowane do magazynu danych ujścia i w związku z tym zwiększa przepływność operacji kopiowania. Narzędzia do kopiowania danych zapewnia elastyczny sposób, aby filtrować dane w relacyjnej bazie danych za pomocą języka zapytania SQL lub plików w folderze obiektu blob platformy Azure. 

### <a name="filter-data-in-a-database"></a>Filtrowanie danych w bazie danych
Poniższy zrzut ekranu przedstawia zapytanie SQL do filtrowania danych.

![Filtrowanie danych w bazie danych](./media/copy-data-tool/filter-data-in-database.png)

### <a name="filter-data-in-an-azure-blob-folder"></a>Filtrowanie danych w folderze obiektu blob platformy Azure
Aby skopiować dane z folderem, można używać zmiennych w ścieżce folderu. Są obsługiwane zmienne: **{year}** , **{month}** , **{dzień}** , **{godzina}** , i **{minuta}** . Na przykład: inputfolder / {year} / {month} / {day}. 

Załóżmy, że należy wprowadzić folderów w następującym formacie: 

```
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Kliknij przycisk **Przeglądaj** przycisku **pliku lub folderu**, przejdź do jednej z tych folderów (na przykład, 2016 -> 03 -> 01 -> 02) i kliknij przycisk **wybierz**. Powinien zostać wyświetlony 2016/03/01/02 w polu tekstowym. 

Następnie zastąp **2016** z **{year}** , **03** z **{month}** , **01** z **{dzień}** , i **02** z **{godzina}** i naciśnij klawisz **kartę** klucza. Powinny zostać wyświetlone list rozwijanych, aby wybrać format dla tych czterech zmiennych:

![Filtr pliku lub folderu](./media/copy-data-tool/filter-file-or-folder.png)

Narzędzie do kopiowania danych generuje parametrów za pomocą wyrażenia, funkcje i zmienne systemowe, które mogą służyć do reprezentowania {year}, {month} {dzień} {godzina} i {minute} podczas tworzenia potoku. Aby uzyskać więcej informacji, zobacz [jak Odczyt lub zapis partycjonowania danych](how-to-read-write-partitioned-data.md) artykułu.

## <a name="scheduling-options"></a>Opcje harmonogramu
Możesz uruchomić operacji kopiowania raz lub zgodnie z harmonogramem (co godzinę, codziennie, i tak dalej). Te opcje może służyć do łączników w różnych środowiskach, w tym w środowisku lokalnym, chmury i pulpit lokalny. 

Operacja jednorazowa kopia umożliwia przenoszenie danych ze źródła do miejsca docelowego tylko raz. Dotyczy to dane, dowolnego rozmiaru i dowolnego obsługiwanego formatu. Zaplanowaną kopię umożliwia kopiowanie danych w cyklu, który określisz. Ustawienia zaawansowane (np. Ponów próbę, limit czasu i alertami) umożliwiają skonfigurowanie zaplanowaną kopię.

![Opcje harmonogramu](./media/copy-data-tool/scheduling-options.png)


## <a name="next-steps"></a>Kolejne kroki
Wypróbuj następujące samouczki, korzystających z narzędzia do kopiowania danych:

- [Szybki Start: tworzenie fabryki danych przy użyciu narzędzia do kopiowania danych](quickstart-create-data-factory-copy-data-tool.md)
- [Samouczek: kopiowanie danych na platformie Azure przy użyciu narzędzia do kopiowania danych](tutorial-copy-data-tool.md) 
- [Samouczek: kopiowanie danych lokalnych do platformy Azure przy użyciu narzędzia do kopiowania danych](tutorial-hybrid-copy-data-tool.md)
