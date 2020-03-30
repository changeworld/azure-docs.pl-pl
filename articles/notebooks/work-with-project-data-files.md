---
title: Importowanie i eksportowanie danych za pomocą projektów w wersji Zapoznawczej notesów platformy Azure
description: Dowiedz się, jak przenieść dane do projektu usługi Azure Notebooks Preview ze źródeł zewnętrznych i jak eksportować dane z projektu.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: e1d4a52ab7f4ad2ca3438af4bc87bec0b79f34d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646980"
---
# <a name="work-with-data-files-in-azure-notebooks-preview-projects"></a>Praca z plikami danych w projektach usługi Azure Notebooks Preview

Dane są życiodajną krwi wielu notebooków Jupyter, zwłaszcza notebooków używanych do nauki o danych. Za pomocą notesów platformy Azure można łatwo importować z różnych źródeł do projektu, a następnie używać tych danych z notesów. Notesy mogą również generować dane przechowywane w projekcie, które można następnie pobrać do użycia w innym miejscu.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Menu **Dane** w uruchomionym notesie udostępnia również polecenia **Przekazywanie** i **Pobieranie,** które działają z plikami w projekcie, a także z plikami tymczasowymi dla bieżącej sesji notesu.

Można również użyć kodu w notesie, aby uzyskać bezpośredni dostęp do różnych źródeł danych, w tym plików w projekcie. Można również uzyskać dostęp do dowolnych danych za pomocą poleceń w komórce kodu. Ponieważ takie dane są przechowywane w zmiennych w sesji notesu, nie zostaną zapisane w projekcie, chyba że używasz kodu do specjalnie generowania plików projektu.

Praca z kodem w danych jest najlepiej doświadczony w samym uruchomionym notesie: w tym celu zapoznaj się [z przykładowym notesem Uzyskiwanie do danych w notesie notesu usługi Azure Notess.](https://notebooks.azure.com/Microsoft/projects/samples/html/Getting%20to%20your%20Data%20in%20Azure%20Notebooks.ipynb)

Poniżej dalsza część artykułu Zawiera szczegółowe informacje na temat operacji na plikach na poziomie projektu.

## <a name="import-data"></a>Importowanie danych

Pliki można przenosić do projektu z pulpitu nawigacyjnego projektu lub w uruchomionym notesie za pomocą menu **Dane** lub polecenia, takiego jak `curl`.

### <a name="import-files-from-the-project-dashboard"></a>Importowanie plików z pulpitu nawigacyjnego projektu

1. W projekcie przejdź do folderu, w którym chcesz zaimportować pliki.

1. Wybierz polecenie **Przekaż,** a następnie **z adresu URL** lub z **komputera** i wyświetlaj niezbędne szczegóły dla danych, które chcesz zaimportować:

   - **Z adresu URL:** Wprowadź adres źródłowy w polu **Adres URL pliku** i nazwę pliku, aby przypisać go do notesu w projekcie w polu Nazwa **pliku.** Następnie wybierz **pozycję + Dodaj plik,** aby dodać adres URL do listy przekazywania. Powtórz proces dla dodatkowych adresów URL, a następnie wybierz pozycję **Gotowe**.

     ![Prześlij z wyskakujących okienków adresu URL](media/quickstarts/upload-from-url-popup.png)

   - **Z komputera**: Przeciągnij i upuść pliki do okna podręcznego lub wybierz **pozycję Wybierz pliki,** a następnie przejdź do i wybierz pliki danych, które chcesz zaimportować. Można upuścić lub wybrać dowolną liczbę plików dowolnego typu i formatu, ponieważ do kodu w notesie, aby otworzyć plik i przeanalizować jego dane.

     ![Prześlij z okna podręcznego komputera](media/quickstarts/upload-from-computer-popup.png)

1. Po zaimportowaniu pliki są wyświetlane na pulpicie nawigacyjnym projektu i są dostępne w kodzie notesu przy użyciu względnych nazwacianów ścieżek do folderu zawierającego.

### <a name="import-files-from-the-file-menu-in-a-notebook"></a>Importowanie plików z menu Plik w notesie

1. W uruchomionym notesie wybierz polecenie**Przekazywanie** **pliku:** > 

    ![Polecenie menu Przekazywanie pliku w notesie](media/file-menu-upload.png)

1. W oknie dialogowym, które się otworzy, przejdź do i wybierz pliki, które chcesz przekazać. Można wybrać dowolną liczbę plików dowolnego typu. Wybierz **otwórz** po zakończeniu.

1. W wyświetlonym okienku **stanu przekazywania** wybierz **folder docelowy** z listy rozwijanej:

    - Folder sesji*~/* ( ): Przesyła pliki do bieżącej sesji notesu, ale nie tworzy plików w projekcie. Folder sesji jest elementem równorzędnym folderu projektu, ale nie utrzymywala się po zakończeniu sesji. Aby uzyskać dostęp do plików sesji w kodzie, przedrostek nazwy plików ze ścieżką względną *.. /*.

        Korzystanie z folderu sesji jest przydatne do eksperymentowania i pozwala uniknąć zaśmiecania projektu plikami, które mogą lub nie mogą być potrzebne w dłuższej perspektywie. Można również przekazać pliki do folderu sesji, które mają identyczne nazwy do plików w projekcie bez powodowania konfliktów i bez konieczności zmiany nazwy plików. Załóżmy na przykład, że masz już jedną wersję *pliku data.csv* w projekcie, ale chcesz eksperymentować z inną wersją *pliku data.csv*. Przesyłając plik do folderu sesji, można uruchomić notes przy użyciu danych w przekazanym pliku (odnosząc się do niego w kodzie za pomocą *.. /data.csv*), a nie dane w pliku projektu.

    - Folder projektu (*/project*): przesyła pliki do projektu, do którego można uzyskać dostęp przy użyciu względnych nazwacianów ścieżek w kodzie. Przekazywanie pliku do tego folderu jest takie samo jak przekazywanie pliku na pulpicie nawigacyjnym projektu. Plik jest zapisywany wraz z projektem i jest dostępny w późniejszych sesjach.

        Przekazywanie kończy się niepowodzeniem, jeśli spróbujesz przekazać plik o takiej samej nazwie jak ten, który już istnieje w projekcie. Aby zastąpić plik, przekaż nowy plik z pulpitu nawigacyjnego projektu, co daje możliwość nadpisywania.

1. Wybierz **pozycję Rozpocznij przesyłanie,** aby zakończyć proces.

### <a name="create-or-import-files-using-commands"></a>Tworzenie lub importowanie plików za pomocą poleceń

Polecenia w terminalu lub w komórce kodu języka Python umożliwiają tworzenie plików zarówno w folderach projektu, jak i sesji. Na przykład polecenia, `curl` `wget` takie jak i pobierają pliki bezpośrednio z Internetu.

Aby pobrać pliki w terminalu, wybierz polecenie **Terminal** na pulpicie nawigacyjnym projektu, a następnie wprowadź odpowiednie polecenia:

```bash
curl https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv

wget https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv
```

Podczas korzystania z komórki kodu języka Python w `!`notesie, prefiks poleceń z .

Folder projektu jest folderem domyślnym, więc określenie docelowej nazwy pliku, takiej jak *oil_price.csv,* powoduje utworzenie pliku w projekcie. Aby utworzyć plik sesji, przedrostek nazwę *.. /* jak w *.. /oil_price.csv*.

### <a name="create-files-in-code"></a>Tworzenie plików w kodzie

Podczas korzystania z kodu, który tworzy `write_csv` plik, takich jak pandas funkcji, nazwy ścieżek są zawsze względem folderu projektu. Korzystanie z *.. /* tworzy plik sesji, który jest odrzucany, gdy notes jest zatrzymywał się i zamykany.

## <a name="export-files"></a>Eksportowanie plików

Dane można eksportować z pulpitu nawigacyjnego projektu lub z poziomu notesu.

## <a name="export-files-from-the-project-dashboard"></a>Eksportowanie plików z pulpitu nawigacyjnego projektu

Na pulpicie nawigacyjnym projektu kliknij prawym przyciskiem myszy plik i wybierz polecenie **Pobierz:**

![Polecenie Pobierz w menu kontekstowym elementu projektu](media/download-command.png)

Można również wybrać plik i użyć polecenia **Pobierz** (skrót klawiaturowy: d) na pulpicie nawigacyjnym:

![Polecenie Pobierz pasek narzędzi na pulpicie nawigacyjnym projektu](media/download-command-toolbar.png)

## <a name="export-files-from-the-data-menu-in-a-notebook"></a>Eksportowanie plików z menu Dane w notesie

1. Wybierz polecenie menu**Pobieranie** **pliku:** > 

    ![Polecenie menu Pobieranie danych w notesie](media/file-menu-download.png)

1. Zostanie wyświetleni okno podręczne z folderami w sesji; folder *projektu* zawiera pliki projektu:

    ![Wyskakujące okienko polecenia Pobieranie danych, w którym wybierasz pliki i foldery](media/file-menu-download-popup.png)

1. Zaznacz pola po lewej stronie plików i folderów, które chcesz pobrać, a następnie wybierz pozycję **Pobierz zaznaczone**.

1. Notes przygotowuje pojedynczy plik *zip* zawierający wybrane pliki, które następnie można zapisać w zwykły sposób z przeglądarki. Notes tworzy plik *zip* nawet po pobraniu pojedynczego pliku.

## <a name="next-steps"></a>Następne kroki

- [Dostęp do danych z chmury w notesie](access-data-resources-jupyter-notebooks.md)
