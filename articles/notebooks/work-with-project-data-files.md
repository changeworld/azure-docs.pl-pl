---
title: Importowanie i eksportowanie danych z projektami za pomocą notesów usługi Azure
description: Jak przenieść dane do projektu notesy platformy Azure ze źródeł zewnętrznych, a sposób eksportowania danych z projektu.
ms.topic: article
ms.date: 12/04/2018
ms.openlocfilehash: bd7ba27859e9d05c0d57c2f78b6449c2bc48ca33
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277379"
---
# <a name="work-with-data-files-in-azure-notebook-projects"></a>Praca z plikami danych w projektach notesu platformy Azure

Dane są lifeblood wiele notesów programu Jupyter, szczególnie notesów używane do analizy danych. Za pomocą notesów usługi Azure można łatwo importować z wielu źródeł do projektu, a następnie użyć tych danych z notesami. Może również mieć notesów Generowanie danych przechowywanych w projekcie, który następnie można pobrać do użycia w innym miejscu.

**Danych** menu w uruchomionej notesu zawiera także **przekazywanie** i **Pobierz** poleceń, które działają z plików w projekcie, a także pliki tymczasowe dla bieżącego Sesja notesu.

Umożliwia także kodu w notesie dostępu z różnych źródeł danych bezpośrednio do dołączania plików w ramach projektu. Można również uzyskać dostęp dowolne dane za pomocą poleceń w komórce kodu. Ponieważ te dane są przechowywane w zmiennych w ramach sesji Notes, nie zostaną zapisane w projekcie chyba że specjalnie wygenerować plików projektu za pomocą kodu.

Praca z kodem w danych najlepiej wystąpił w uruchomionej Notes, sama: w tym celu zapoznaj się [wprowadzenie do danych w notesie przykładowe notesów usługi Azure](https://notebooks.azure.com/Microsoft/projects/samples/html/Getting%20to%20your%20Data%20in%20Azure%20Notebooks.ipynb).

W dalszej części tego artykułu zawiera szczegółowe informacje dotyczące operacji na plikach na poziomie projektu.

## <a name="import-data"></a>Importowanie danych

Przenoszenie plików do projektu na pulpicie nawigacyjnym projektu lub w ramach notesu uruchomionego za pomocą **danych** menu lub poleceń, takich jak `curl`.

### <a name="import-files-from-the-project-dashboard"></a>Importowanie plików z poziomu pulpitu nawigacyjnego projektu

1. W projekcie przejdź do folderu, w której chcesz zaimportować pliki.

1. Wybierz **przekazywanie** polecenia, a następnie opcję **z adresu URL** lub **z komputera** i odpowiednie szczegóły danych, który chcesz zaimportować do projektu:

   - **Z adresu URL**: Wprowadź adres źródła w **adres URL pliku** pola i nazwę pliku, aby przypisać do Notes w projekcie w **nazwy pliku** pola. Następnie wybierz pozycję **+ Dodaj plik** można dodać adres URL do listy przekazywania. Powtórz te czynności dla wszelkich dodatkowych adresów URL, a następnie wybierz **gotowe**.

     ![Przekaż z adresu URL okna podręcznego](media/quickstarts/upload-from-url-popup.png)

   - **Z poziomu komputera**: przeciągnij i upuść pliki w okienku wyskakującym lub wybierz **Wybieranie plików**, a następnie wyszukaj i wybierz pliki danych, który chcesz zaimportować. Można usunąć lub wybrać dowolną liczbę plików dowolnego typu i formatu, ponieważ to Ty kodu w notesie, otwórz plik i analizować swoje dane.

     ![Przekaż z komputera okna podręcznego](media/quickstarts/upload-from-computer-popup.png)

1. Po zaimportowaniu plików są wyświetlane na pulpicie nawigacyjnym projekt i są dostępne w ramach kodu notesu przy użyciu nazw względnych ścieżek do folderu zawierającego.

### <a name="import-files-from-the-file-menu-in-a-notebook"></a>Importowanie plików z menu plik w notesie

1. W uruchomionej Notes, wybierz **pliku** > **przekazywanie** polecenia:

    ![Plik polecenia menu przekazywania w ramach notesu](media/file-menu-upload.png)

1. W wyświetlonym oknie dialogowym Przejdź do, a następnie zaznacz pliki, które chcesz przekazać. Możesz wybrać dowolną liczbę plików dowolnego typu. Wybierz **Otwórz** po zakończeniu.

1. W **stan przekazywania** okna podręcznego, który jest wyświetlany, wybierz **Folder docelowy** z listy rozwijanej:

    - Folder sesji ( *~/* ): przesyła pliki do bieżącej sesji Notes, ale nie tworzy pliki w projekcie. Folder sesji jest równorzędny folder projektu, ale nie jest zachowana po zakończeniu sesji. Aby uzyskać dostęp do plików sesji w kodzie, prefiks nazw plików ze ścieżką względną *... /* .

        Za pomocą folderu sesji jest przydatne do eksperymentowania i pozwala uniknąć przeładowania projekt z plikami, może być lub może nie być potrzebna w dłuższym okresie. Można również przekazać pliki do folderu sesji, który miało identyczne nazwy plików w projekcie, bez powodowania konfliktów i bez konieczności zmień nazwy plików. Na przykład, załóżmy, że masz jedną wersję *data.csv* w projekcie, ale chcesz eksperymentować z inną wersję *data.csv*. Przekazując plik do folderu, w sesji, można uruchomić Notes, przy użyciu danych w przekazanym pliku (odwołując się do niego w kodzie za pomocą *... /Data.csv*) zamiast dane w pliku projektu.

    - Folder projektu ( */project*): przekazywanie plików do projektu, gdzie można uzyskać dostęp przy użyciu nazw względnych ścieżek w kodzie. Próba przekazania pliku do tego folderu jest taka sama jak przekazać plik na pulpicie nawigacyjnym projekt. Plik jest zapisywany z projektem i jest dostępny w nowszych sesji.

        Przekazywanie zakończy się niepowodzeniem, jeśli zostanie podjęta próba przekazania pliku o takiej samej nazwie jako jedna, która już istnieje w projekcie. Aby zastąpić plik, Przekaż nowy plik na pulpicie nawigacyjnym projektu zamiast tego, które zapewnia opcję zastąpienia.

1. Wybierz **Rozpocznij przekazywanie** aby ukończyć proces.

### <a name="create-or-import-files-using-commands"></a>Tworzenie lub importowanie plików za pomocą poleceń

Można użyć polecenia w terminalu lub w komórce kodu języka Python do tworzenia plików w folderach projektu i sesji. Na przykład poleceń, takich jak `curl` i `wget` bezpośredniego pobrania plików z Internetu.

Aby pobrać pliki w terminalu, zaznacz **terminalu** polecenia na pulpicie nawigacyjnym projekt, a następnie wprowadź odpowiednie polecenia:

```bash
curl https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv

wget https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv
```

Korzystając z języka Python komórce kodu w notesie, prefiks poleceń z `!`.

Folder projektu jest domyślnym folderem, więc określanie Nazwa pliku docelowego, takie jak *oil_price.csv* tworzy plik w projekcie. Aby utworzyć plik sesji, należy przed nazwą *... /* jak *... /oil_price.csv*.

### <a name="create-files-in-code"></a>Tworzenie plików w kodzie

Korzystając z kodu, który tworzy plik, takich jak pandas `write_csv` funkcji, nazwy ścieżek są zawsze względne wobec folderu projektu. Za pomocą *... /* tworzy plik sesji, który jest pomijany, jeśli notes jest zostało zatrzymane i zamknięte.

## <a name="export-files"></a>Pliki eksportu

Możesz wyeksportować dane na pulpicie nawigacyjnym projektu lub z poziomu notesu.

## <a name="export-files-from-the-project-dashboard"></a>Eksportuj pliki na pulpicie nawigacyjnym projektu

Na pulpicie nawigacyjnym projektu kliknij prawym przyciskiem myszy plik, a następnie wybierz pozycję **Pobierz**:

![Pobierz polecenia menu kontekstowego elementu projektu](media/download-command.png)

Możesz również wybrać plik i użyj **Pobierz** polecenia (skrót klawiaturowy: d) na pulpicie nawigacyjnym:

![Pobierz polecenia paska narzędzi na pulpit nawigacyjny projektu](media/download-command-toolbar.png)

## <a name="export-files-from-the-data-menu-in-a-notebook"></a>Eksportuj pliki z menu dane w notesie

1. Wybierz **pliku** > **Pobierz** polecenia menu:

    ![Polecenia menu pobierania danych w ramach notesu](media/file-menu-download.png)

1. Okno podręczne zostanie wyświetlone foldery w sesji; *projektu* folder zawiera pliki projektu:

    ![Polecenie popup pobierania danych, w którym można wybrać pliki i foldery](media/file-menu-download-popup.png)

1. Zaznacz pola wyboru po lewej stronie, plików i folderów, które chcesz pobrać, a następnie wybierz **Pobierz wybrane**.

1. Notes przygotowuje pojedynczej *zip* plik zawierający wybrane pliki, które można następnie Zapisz jako użytkownik zwykle wykonać z poziomu przeglądarki. Tworzy notes *zip* plików, nawet wtedy, gdy pobieranie pojedynczego pliku.

## <a name="next-steps"></a>Następne kroki

- [Dostęp do danych w chmurze w notesie](access-data-resources-jupyter-notebooks.md)
