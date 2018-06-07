---
title: Kopiuj dane narzędzia fabryki danych Azure | Dokumentacja firmy Microsoft
description: Informacje na temat narzędzia kopii danych w interfejsie użytkownika fabryki danych Azure
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
ms.openlocfilehash: 5180a490a543b6cfc2a8129423f0e663de2e06e1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34619426"
---
# <a name="copy-data-tool-in-azure-data-factory"></a>Skopiuj narzędzie dane z fabryki danych Azure
Narzędzie Kopia fabryki danych Azure ułatwia i optymalizuje proces wprowadzania danych w usłudze data lake, który zazwyczaj jest to pierwszy krok w scenariuszu integracji danych na całej trasie.  Zaoszczędzić czas, zwłaszcza jeśli używasz usługi fabryka danych Azure do pozyskiwania danych ze źródła danych po raz pierwszy. Korzyści wynikające ze stosowania tego narzędzia, należą:

- Podczas korzystania z narzędzia Kopia fabryki danych Azure, nie wymagają znasz definicje fabryki danych połączone usługi, zestawy danych, potoki, działań i wyzwalaczy. 
- Przepływ narzędzie kopii danych jest intuicyjne dla ładowania danych do usługi data lake. Narzędzie tworzy automatycznie wszystkie niezbędne zasoby fabryki danych można skopiować danych z magazynu wybranego źródła danych w magazynie wybrane miejsce docelowe/ujście danych. 
- Narzędzie kopii danych pomaga sprawdzić poprawność danych jest trwa pozyskanych w czasie tworzenia, który pomaga uniknąć potencjalnych błędów na początku sam.
- Jeśli musisz wdrożyć logikę biznesową złożone, aby załadować dane do usługi data lake możesz edytować zasoby fabryki danych utworzone przez narzędzie kopii danych za pomocą tworzenia na działanie w interfejsie użytkownika z fabryki danych. 

W poniższej tabeli przedstawiono wskazówki dotyczące kiedy należy używać narzędzia kopii danych a tworzenia poszczególnych działań w interfejsie użytkownika z fabryki danych: 

| Narzędzie do kopiowania danych | Na tworzenia działania (działanie kopiowania) |
| -------------- | -------------------------------------- |
| Aby łatwe tworzenie danych podczas ładowania zadania bez szkoleniowe dotyczące jednostek fabryki danych Azure (połączone usługi, zestawy danych, potoki itp.) | Należy wdrożyć logikę złożone i elastyczne podczas ładowania danych do lake. |
| Chcesz szybko załadować dużą liczbę artefaktów danych do usługi data lake. | Chcesz łańcucha działanie kopiowania z kolejnych działań czyszczenia lub przetwarzania danych. |

Aby uruchomić narzędzie kopii danych, kliknij przycisk **kopiowania danych** kafelka na stronie głównej w fabryce danych.

![Strony wprowadzenie — link do narzędzia Kopia danych](./media/copy-data-tool/get-started-page.png)


## <a name="intuitive-flow-for-loading-data-into-a-data-lake"></a>Przepływ intuicyjne dla ładowania danych do usługi data lake
To narzędzie można łatwo przenosić dane z różnych źródeł do miejsc docelowych w minutach, z przepływem intuicyjnego:  

1. Skonfiguruj ustawienia dla **źródła**.
2. Skonfiguruj ustawienia dla **docelowego**. 
3. Skonfiguruj **Zaawansowane ustawienia** operacji kopiowania, takie jak mapowanie kolumn, ustawienia wydajności i odporności na uszkodzenia ustawienia. 
4. Określ **harmonogram** danych podczas ładowania zadania. 
5. Przegląd **podsumowania** jednostek fabryki danych ma zostać utworzony. 
6. **Edytuj** potoku można zaktualizować ustawień dla działania kopiowania, zgodnie z potrzebami. 

 Narzędzie zaprojektowano z danymi big data na uwadze od początku, obsługę różnych danych i typów obiektów. Można go przenieść setki folderów, plików i tabele. Narzędzie obsługuje Podgląd danych, przechwytywania schematu i automatyczne mapowanie i danych oraz filtrowania.

![Narzędzie do kopiowania danych](./media/copy-data-tool/copy-data-tool.png)

## <a name="automatic-data-preview"></a>Podgląd danych
Można wyświetlić podgląd część danych z magazynu wybranego źródła danych, dzięki czemu można sprawdzić poprawności danych, które są kopiowane. Ponadto jeśli źródło danych jest w pliku tekstowym, narzędzia kopii danych analizuje pliku tekstowego automatycznie wykryć ogranicznik wiersza i kolumny, a schemat.

![Ustawienia pliku](./media/copy-data-tool/file-format-settings.png)

Po wykryciu:

![Wykryto plik ustawień i w wersji zapoznawczej](./media/copy-data-tool/after-detection.png)

## <a name="schema-capture-and-automatic-mapping"></a>Przechwytywanie schematu i automatyczne mapowanie
Schematu źródła danych nie może być taka sama jak schemat miejsce docelowe danych w wielu przypadkach. W tym scenariuszu należy mapować kolumn z schematu źródła do kolumn z schematu docelowego.

Narzędzie kopii danych monitoruje i uzyskuje informacje o Twojej zachowanie mapowanie kolumn między magazynami źródłowym i docelowym. Po pobranie jednej lub kilku kolumn z magazynu danych źródła i zamapowania ich na schemat miejsca docelowego kopii danych rozpoczyna do analizowania wzorca dla par kolumny, które są pobierane z obu stron. Następnie dotyczy tego samego wzorca rest kolumn. W związku z tym można wyświetlić wszystkie kolumny zostały zamapowane do miejsca docelowego w sposób, który ma zaraz po kilku kliknięć.  Jeśli nie są zadowalające wybór mapowanie kolumn określone przez narzędzie kopii danych, można ją zignorować i kontynuować ręcznie mapowania kolumn. W tym samym czasie narzędzia kopii danych stale uzyskuje informacje o aktualizacji wzorzec i ostatecznie osiągnie wzorcem prawo mapowania kolumn, które chcesz osiągnąć. 

> [!NOTE]
> Podczas kopiowania danych z serwera SQL lub bazy danych SQL Azure do usługi Azure SQL Data Warehouse, jeśli tabela nie istnieje w magazynie docelowym, skopiuj dane narzędzie obsługuje tworzenie tabeli automatycznie za pomocą schematu źródła. 

## <a name="filter-data"></a>Filtrowanie danych
Można filtrować dane źródłowe, aby wybrać tylko dane, które musi być skopiowany do ujścia magazynu danych. Filtrowanie zmniejsza ilość danych, które ma zostać skopiowany do ujścia magazynu danych i w związku z tym zwiększa przepływność operacji kopiowania. Narzędzie do kopiowania danych umożliwia elastyczne można filtrować dane w relacyjnej bazie danych przy użyciu język zapytań SQL lub pliki w folderze obiektów blob platformy Azure. 

### <a name="filter-data-in-a-database"></a>Filtrowanie danych w bazie danych
Poniższy zrzut ekranu przedstawia zapytanie SQL do filtrowania danych.

![Filtrowanie danych w bazie danych](./media/copy-data-tool/filter-data-in-database.png)

### <a name="filter-data-in-an-azure-blob-folder"></a>Filtrowanie danych w folderze obiektów blob platformy Azure
Aby skopiować dane z folderu, można używać zmiennych w ścieżce folderu. Są obsługiwane zmienne: **{year}**, **{month}**, **{day}**, **{godzina}**, i **{minutę}**. Na przykład: inputfolder / {year} / {month} / {day}. 

Załóżmy, że podania folderów w następującym formacie: 

```
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Kliknij przycisk **Przeglądaj** przycisk dla **pliku lub folderu**, przejdź do jednej z tych folderów (na przykład 2016 -> 03 -> 01 -> 02) i kliknij przycisk **wybierz**. Powinny pojawić się 2016/03/01/02 w polu tekstowym. 

Następnie zastąp **2016** z **{year}**, **03** z **{month}**, **01** z **{day}** , i **02** z **{godzina}** i naciśnij klawisz **kartę** klucza. Powinny pojawić się listy rozwijanej, aby wybrać format tych zmiennych:

![Filtr pliku lub folderu](./media/copy-data-tool/filter-file-or-folder.png)

Narzędzie kopii danych generuje parametrów z wyrażenia, funkcje i zmienne systemowe, które mogą służyć do reprezentowania {year}, {month}, {day}, {godzina} i {minuty}, podczas tworzenia potoku. Aby uzyskać więcej informacji, zobacz [jak do odczytu lub zapisu na partycje danych](how-to-read-write-partitioned-data.md) artykułu.

## <a name="scheduling-options"></a>Opcje harmonogramu
Można wykonać operacji kopiowania raz lub zgodnie z harmonogramem (co godzinę, codziennie, i tak dalej). Te opcje może służyć do łączników w różnych środowiskach, w tym lokalnych, chmury i lokalnych pulpitu. 

Operacja kopiowania jednorazowe umożliwia przenoszenie danych ze źródła do miejsca docelowego tylko raz. Ma to zastosowanie do danych o dowolnym rozmiarze i dowolnego obsługiwanego formatu. Zaplanowanych kopii umożliwia kopiowanie danych w cyklu, który określisz. Zaawansowane ustawienia (np. Ponów próbę, limit czasu i alertami) umożliwia konfigurowanie zaplanowanych kopii.

![Opcje harmonogramu](./media/copy-data-tool/scheduling-options.png)


## <a name="next-steps"></a>Kolejne kroki
Wypróbuj te samouczki, które narzędzie kopii danych:

- [Szybki Start: tworzenie fabryki danych za pomocą narzędzia Kopia danych](quickstart-create-data-factory-copy-data-tool.md)
- [Samouczek: kopiowanie danych na platformie Azure przy użyciu narzędzia kopii danych](tutorial-copy-data-tool.md) 
- [Samouczek: kopiowanie danych lokalnych do platformy Azure przy użyciu narzędzia kopii danych](tutorial-hybrid-copy-data-tool.md)
