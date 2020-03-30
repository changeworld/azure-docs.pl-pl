---
title: Tworzenie i klonowanie notesów jupytera — usługa Azure Notebooks Preview
description: Projekty usługi Azure Notebooks Preview zarządzają kolekcją notesów i powiązanych plików, które można utworzyć nowe lub sklonować z innego źródła.
ms.topic: how-to
ms.date: 02/25/2019
ms.openlocfilehash: b29ff336c09a3bbf05a57c8a3a503b1875b76e54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280575"
---
# <a name="create-and-clone-projects-in-azure-notebooks-preview"></a>Tworzenie i klonowanie projektów w usłudze Azure Notebooks Preview

Notesy platformy Azure organizują notesy jupytera i powiązane pliki w logiczne grupy zwane *projektami*. Najpierw utworzysz projekt jako kontener, a następnie utworzysz lub sklonujesz jeden lub więcej notesów w folderze obok innych plików projektu. (Ten proces jest pokazany w [samouczku](tutorial-create-run-jupyter-notebook.md).)

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Projekt przechowuje również metadane i inne ustawienia konfiguracji, które mają wpływ na serwer, na którym działają notesy, w tym kroki konfiguracji niestandardowej i instalacja pakietu. Aby uzyskać więcej informacji, zobacz [Zarządzanie projektami i konfigurowanie ich](configure-manage-azure-notebooks-projects.md).

## <a name="use-the-my-projects-dashboard"></a>Korzystanie z pulpitu nawigacyjnego Moje projekty

Pulpit nawigacyjny Moje `https://notebooks.azure.com/<userID>/projects` **projekty** to miejsce, w którym można wyświetlać projekty, zarządzać nimi i tworzyć je:

[![Pulpit nawigacyjny Moje projekty w notesach platformy Azure](media/my-projects-dashboard.png)](media/my-projects-dashboard.png#lightbox)

To, co możesz zrobić na pulpicie nawigacyjnym, zależy od tego, czy zalogujesz się za pomocą konta, które jest właścicielem identyfikatora użytkownika:

| Polecenie | Dostępne dla | Opis |
| --- | --- | --- |
| **Run** | Właściciel | Uruchamia serwer projektu i otwiera folder projektu w jupyter. (Częściej częściej najpierw nawigujesz do folderu projektu, a następnie uruchamiasz notes). |
| **Pobierz** | Dowolny użytkownik | Pobiera kopię wybranego projektu jako plik ZIP. |
| **Share** | Dowolny użytkownik | Wyświetla okno podręczne udostępniania, za pomocą którego można uzyskać adres URL do wybranego projektu, udostępnić go w mediach społecznościowych, wysłać wiadomość e-mail z adresem URL i uzyskać kod HTML lub Markdown z plakietką "notes uruchamiania" (zobacz [uzyskiwanie plakietki uruchamiania)](#obtain-a-launch-badge)z adresem URL. |
| **Usuwanie** | Właściciel | Usuwa wybrany projekt. Tej operacji nie można cofnąć. |
| **Terminal** | Właściciel | Uruchamia serwer projektu, a następnie otwiera nowe okno przeglądarki z terminalem bash dla tego serwera. |
| **+ Nowy projekt** | Właściciel | Tworzy nowy projekt. Zobacz [Tworzenie nowego projektu](#create-a-new-project). |
| **Prześlij Repo GitHub** | Właściciel | Importuje projekt z usługi GitHub. [Importowanie projektu z usługi GitHub](#import-a-project-from-github). |
| **Klonowanie** | Dowolny użytkownik | Kopiuje wybrany projekt na własne konto. Monituje o zalogowanie się, jeśli jeszcze nie. Zobacz [Klonowanie projektu](#clone-a-project). |

### <a name="obtain-a-launch-badge"></a>Uzyskiwanie plakietki startowej

Korzystając z polecenia **Udostępnij** i wybierając kartę **Osadź,** możesz skopiować kod HTML lub markdown, który tworzy plakietkę "Notes uruchamiania":

![Uruchamianie plakietki notesu](https://notebooks.azure.com/launch.png)

Jeśli nie masz projektu notesów platformy Azure, możesz utworzyć łącze, które klonuje z usługi GitHub bezpośrednio przy użyciu następujących szablonów, zastępując odpowiednie nazwy użytkownika i nazwy repozytorium:

```html
<a href="https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>"><img src="https://notebooks.azure.com/launch.png" /></a>
```

```markdown
[![Azure Notebooks](https://notebooks.azure.com/launch.png)](https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>)
```

## <a name="create-a-new-project"></a>Tworzenie nowego projektu

Podczas korzystania z polecenia **+ Nowy projekt,** Notesy platformy Azure wyświetla **tworzenie nowego projektu** popup. W tym wyskakującym okienku wprowadź następujące informacje, a następnie wybierz pozycję **Utwórz:**

| Pole | Opis |
| --- | --- |
| Project name (Nazwa projektu) | Przyjazna nazwa projektu, który używa notesów platformy Azure do celów wyświetlania. Na przykład "Mój projekt notesu". |
| Identyfikator projektu | Niestandardowy identyfikator, który staje się częścią adresu URL używanego `https://notebooks.azure.com/<user_id>/projects/<project_id>`do udostępniania projektu (formularz jest ). Ten identyfikator może używać tylko liter, cyfr i łączników, jest ograniczony do 30 znaków i nie może być [zastrzeżonym identyfikatorem projektu.](#reserved-project-ids) Jeśli nie masz pewności, czego użyć, wspólną konwencją jest użycie małych liter nazwy projektu, w której spacje są przekształcane w łączniki, takie jak "mój notes-project" (obcięty, jeśli to konieczne, aby dopasować limit długości). |
| Public | Jeśli jest ustawiona, umożliwia każdemu, kto ma łącze, dostęp do projektu. Podczas tworzenia projektu prywatnego wyczyść tę opcję. |
| Inicjowanie tego projektu za pomocą pliku README | Jeśli jest ustawiona, tworzy domyślny plik *README.md* w projekcie. Plik *README.md* to miejsce, w którym w razie potrzeby dostarczasz dokumentację dla projektu. |

### <a name="reserved-project-ids"></a>Zarezerwowane identyfikatory projektów

Następujące słowa zastrzeżone nie mogą być używane przez siebie jako identyfikatory projektu. Te słowa zastrzeżone mogą być jednak używane jako część dłuższych identyfikatorów projektu.

| | | | | | |
| --- | --- | --- | --- | --- | --- |
| informacje | account | administracja | api | blog | klasa |
| content | pulpit nawigacyjny | odkrywanie | FAQ | Pomoc | html |
| strona główna | import | biblioteka | zarządzanie | new | Notebook |
| notesy | pdf | podgląd | Ceny | profil | search |
| status | pomoc techniczna | test | | | |

Jeśli spróbujesz użyć jednego z tych słów jako identyfikatora projektu, w oknach podręcznych **Utwórz nowy projekt** i **Ustawienia projektu** wskaż "Identyfikator biblioteki jest identyfikatorem zastrzeżonym".

Ponieważ identyfikator projektu jest również częścią adresu URL projektu, oprogramowanie blokujące reklamy może blokować używanie niektórych słów kluczowych, takich jak "reklama". W takich przypadkach należy użyć innego wyrazu w identyfikatorze projektu.

## <a name="import-a-project-from-github"></a>Importowanie projektu z usługi GitHub

Możesz łatwo zaimportować całe publiczne repozytorium GitHub jako projekt, w tym wszelkie dane i *pliki README.md.* Użyj polecenia **Przekaż repozytorium GitHub,** podaj następujące szczegóły w wyskakującym okienku, a następnie wybierz pozycję **Importuj:**

| Pole | Opis |
| --- | --- |
| Repozytorium GitHub | Nazwa repozytorium źródłowego w github.com. Na przykład, aby sklonować notesy Jupyter [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks)dla usługi Azure Cognitive Services w programie , wprowadź "Microsoft/cognitive-services-notebooks".  |
| Klonuj rekursywnie | Repozytoria GitHub mogą zawierać wiele repozytoriów podrzędnych. Ustaw tę opcję, jeśli chcesz sklonować repozytorium nadrzędne i wszystkie jego wiązki podrzędne. Ponieważ repozytorium może mieć wiele dzieściń, pozostaw tę opcję jako czystą, chyba że wiesz, że jej potrzebujesz. |
| Project name (Nazwa projektu) | Przyjazna nazwa projektu, który używa notesów platformy Azure do celów wyświetlania. |
| Identyfikator projektu | Niestandardowy identyfikator, który staje się częścią adresu URL używanego `https://notebooks.azure.com/<user_id>/projects/<project_id>`do udostępniania projektu (formularz jest ). Ten identyfikator może używać tylko liter, cyfr i łączników, jest ograniczony do 30 znaków i nie może być [zastrzeżonym identyfikatorem projektu.](#reserved-project-ids) Jeśli nie masz pewności, czego użyć, wspólną konwencją jest użycie małych liter nazwy projektu, w której spacje są przekształcane w łączniki, takie jak "mój notes-project" (obcięty, jeśli to konieczne, aby dopasować limit długości). |
| Public | Jeśli jest ustawiona, umożliwia każdemu, kto ma łącze, dostęp do projektu. Podczas tworzenia projektu prywatnego wyczyść tę opcję. |

Importowanie repozytorium z gitHub również importuje jego historię. Można użyć standardowych poleceń Git z terminala, aby zatwierdzić nowe zmiany, ściągać zmiany z GitHub i tak dalej.

## <a name="clone-a-project"></a>Klonowanie projektu

Klonowanie tworzy kopię istniejącego projektu na własnym koncie, gdzie można następnie uruchomić i zmodyfikować dowolny notes lub inny plik w projekcie. Klonowanie można również używać do tworzenia kopii własnych projektów, w których wykonujesz eksperymenty lub inne prace bez zakłócania oryginalnego projektu.

Aby sklonować projekt:

1. Na pulpicie nawigacyjnym **Moje projekty** kliknij prawym przyciskiem myszy żądany projekt i wybierz polecenie **Klonuj** (skrót klawiaturowy: c).

    ![Polecenie Klonuj w menu kontekstowym projektu](media/clone-command.png)

1. W wyskakującym okienku **Sklonowanie projektu** wprowadź nazwę i identyfikator klonu i określ, czy klon jest publiczny. Te ustawienia są takie same jak w przypadku [nowego projektu](#create-a-new-project).

    ![Okno podręczne Klonuj projekt](media/clone-project.png)

1. Po wybraniu przycisku **Klonuj** notesy platformy Azure przechodzi bezpośrednio do kopii.

## <a name="next-steps"></a>Następne kroki

- [Explore sample notebooks (Eksplorowanie przykładowych notesów)](azure-notebooks-samples.md)
- [Jak: Konfigurowanie projektów i zarządzanie nimi](configure-manage-azure-notebooks-projects.md)
- [Jak: Instalowanie pakietów z poziomu notesu](install-packages-jupyter-notebook.md)
- [Jak: Prezentowanie pokazu slajdów](present-jupyter-notebooks-slideshow.md)
- [Jak: Praca z plikami danych](work-with-project-data-files.md)
- [Jak: Dostęp do zasobów danych](access-data-resources-jupyter-notebooks.md)
- [Jak: Korzystanie z usługi Azure Machine Learning](use-machine-learning-services-jupyter-notebooks.md)
