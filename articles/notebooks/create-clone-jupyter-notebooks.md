---
title: Tworzenie i klonowanie notesy Jupyter notebook na platformie Azure
description: Projekty platformy Azure notesów zarządzanie kolekcją notesów i powiązane pliki, których można tworzyć nowe lub sklonować z innego źródła.
ms.topic: article
ms.date: 02/25/2019
ms.openlocfilehash: fc5425312637710f0b9f94493b8cfb4a48582236
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277574"
---
# <a name="create-and-clone-projects"></a>Tworzenie i klonowanie projektów

Azure Notebooks organizuje notesy Jupyter i powiązane pliki do grup logicznych nazywanych *projektami*. Jako kontener, najpierw Utwórz projekt, a następnie utworzyć lub sklonować co najmniej jeden notesów w folderze wraz z innych plików projektów. (Ten proces jest zaprezentowany w [samouczku](tutorial-create-run-jupyter-notebook.md)).

Projekt udostępnia również metadane i pozostałe ustawienia konfiguracyjne, które wpływają na serwer, na które notesów uruchomić, w tym kroki instalacji niestandardowej i instalacji pakietu aktualizacji. Aby uzyskać więcej informacji, zobacz [Zarządzanie projektami i Konfigurowanie](configure-manage-azure-notebooks-projects.md)ich.

## <a name="use-the-my-projects-dashboard"></a>Pulpit nawigacyjny Moje projekty

Pulpit nawigacyjny **Moje projekty** w `https://notebooks.azure.com/<userID>/projects` jest miejscem, w którym można wyświetlać projekty i zarządzać nimi:

[![pulpit nawigacyjny moje projekty w programie Azure Notebooks](media/my-projects-dashboard.png)](media/my-projects-dashboard.png#lightbox)

Co można zrobić na pulpicie nawigacyjnym, zależy od tego, czy po zarejestrowaniu się przy użyciu konta, który jest właścicielem identyfikator użytkownika:

| Polecenie | Dostępna dla | Opis |
| --- | --- | --- |
| **Wykonane** | Właściciel | Uruchamia serwer project server i otwiera folder projektu w programie Jupyter. (Częściej, możesz najpierw przejdź do folderu projektu, a następnie uruchomić Notes, w tym miejscu.) |
| **Pobieranie** | Każda osoba | Pobiera kopię wybranego projektu jako plik ZIP. |
| **Share** | Każda osoba | Wyświetla okno podręczne udostępniania, za pomocą którego można uzyskać adres URL wybranego projektu, udostępnić do mediów społecznościowych, wysłać wiadomość e-mail z adresem URL oraz uzyskać kod HTML lub promocji dla programu za pomocą wskaźnika "Uruchom Notes" (zobacz temat [Uzyskiwanie wskaźnika uruchamiania](#obtain-a-launch-badge)) przy użyciu adresu URL. |
| **Usuwanie** | Właściciel | Usuwa wybranego projektu. Tej operacji nie można cofnąć. |
| **Terminal** | Właściciel | Rozpoczyna się na serwerze project server, a następnie zostanie otwarte nowe okno przeglądarki, przy użyciu programu bash terminalu dla tego serwera. |
| **+ Nowy projekt** | Właściciel | Tworzy nowy projekt. Zobacz [Tworzenie nowego projektu](#create-a-new-project). |
| **Przekaż repozytorium GitHub** | Właściciel | Importuje projektu z usługi GitHub. [Importuj projekt z usługi GitHub](#import-a-project-from-github). |
| **Klon** | Każda osoba | Kopiuje wybrany projekt do własnego konta. Monituje o Zaloguj się w przypadku jeszcze nie jest zalogowany. Zobacz [klonowanie projektu](#clone-a-project). |

### <a name="obtain-a-launch-badge"></a>Uzyskaj wskaźnik uruchamiania

W przypadku użycia polecenia **Udostępnij** i wybrania karty **osadzania** można skopiować kod HTML lub przeznaczenie, które tworzy znaczek "Uruchom Notes":

![Uruchamianie notesu wskaźnika](https://notebooks.azure.com/launch.png)

Jeśli nie masz projektu notesów usługi Azure można utworzyć łącze, które klony z serwisu GitHub bezpośrednio przy użyciu następujących szablonów, podstawiając odpowiednie nazwy użytkownika i nazwy repozytoriów:

```html
<a href="https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>"><img src="https://notebooks.azure.com/launch.png" /></a>
```

```markdown
[![Azure Notebooks](https://notebooks.azure.com/launch.png)](https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>)
```

## <a name="create-a-new-project"></a>Tworzenie nowego projektu

W przypadku korzystania z polecenia **+ Nowy projekt** Azure Notebooks wyświetla okno podręczne **Utwórz nowy projekt** . W tym oknie podręcznym Wprowadź poniższe informacje, a następnie wybierz pozycję **Utwórz**:

| Pole | Opis |
| --- | --- |
| Project name (Nazwa projektu) | Przyjazna nazwa dla projektu, które korzysta z notesów usługi Azure w celach wyświetlania. Na przykład "mój projekt notesu". |
| Identyfikator projektu | Niestandardowy identyfikator, który jest częścią adresu URL, który jest używany do udostępniania projektu (formularz jest `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Ten identyfikator może korzystać tylko z liter, cyfr i łączników, jest ograniczony do 30 znaków i nie może być [zarezerwowanym identyfikatorem projektu](#reserved-project-ids). Jeśli masz pewności co do użycia, typową Konwencją jest użyć małe wersji nazwa projektu, w którym spacje są przekształcane w łączników, np. "Moja notesu projekt" (obcięty, jeśli to konieczne dopasować limit długości). |
| Public | Jeśli ustawiona, umożliwia każdemu z linkiem do dostępu do projektu. Podczas tworzenia projektu prywatny, usuń zaznaczenie tej opcji. |
| Inicjowanie projektu z pliku README | Jeśli ta wartość jest ustawiona, program tworzy domyślny plik *README.MD* w projekcie. Plik *README.MD* to miejsce, w którym podano dokumentację projektu, w razie potrzeby. |

### <a name="reserved-project-ids"></a>Zarezerwowane identyfikatory projektów

Następujące słowa zastrzeżone nie mogą być używane przez siebie jako identyfikatory projektu. Te słowa zastrzeżone mogą jednak być używane jako część dłuższego identyfikatora projektu.

| | | | | | |
| --- | --- | --- | --- | --- | --- |
| o | account | administracja | api | wpisów | miejsca |
| content | pulpit nawigacyjny | przeglądać | FAQ | pomoc | html |
| mowa | zaimportować | biblioteki | zarządzanie | nowe | notesu |
| notesy | formatach | wersja zapoznawcza | cennik | profile | search |
| status | Pomocy | test | | | |

Jeśli spróbujesz użyć jednego z tych słów jako identyfikatora projektu, okna podręczne **Utwórz nowy projekt** i **Ustawienia projektu** wskazują, "identyfikator biblioteki jest zarezerwowanym identyfikatorem".

Ponieważ identyfikator projektu jest również częścią adresu URL projektu, oprogramowanie usługi AD Blocker może blokować użycie niektórych słów kluczowych, takich jak "anons". W takich przypadkach należy użyć innego wyrazu w IDENTYFIKATORze projektu.

## <a name="import-a-project-from-github"></a>Importowanie projektu z usługi GitHub

Możesz łatwo zaimportować cały publiczny repozytorium GitHub jako projekt obejmujący wszystkie pliki danych i *README.MD* . Użyj polecenia **Przekaż repozytorium GitHub** , podaj następujące informacje w oknie podręcznym, a następnie wybierz pozycję **Importuj**:

| Pole | Opis |
| --- | --- |
| Repozytorium GitHub | Nazwa repozytorium źródłowe w witrynie github.com. Na przykład aby sklonować notesy Jupyter dla usługi Azure Cognitive Services w [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks), wprowadź "Microsoft/poznawcze-Services-Notess".  |
| Rekursywnie Klonuj | Repozytoria GitHub może zawierać wiele repozytoriów podrzędnych. Ustaw tę opcję, jeśli chcesz klonować repozytorium nadrzędnego i jego elementów podrzędnych. Ponieważ jest możliwe w przypadku repozytorium można mieć wiele podrzędnych, pozostaw tę opcję, wyczyść, chyba że wiesz, że będą potrzebne. |
| Project name (Nazwa projektu) | Przyjazna nazwa dla projektu, które korzysta z notesów usługi Azure w celach wyświetlania. |
| Identyfikator projektu | Niestandardowy identyfikator, który jest częścią adresu URL, który jest używany do udostępniania projektu (formularz jest `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Ten identyfikator może korzystać tylko z liter, cyfr i łączników, jest ograniczony do 30 znaków i nie może być [zarezerwowanym identyfikatorem projektu](#reserved-project-ids). Jeśli masz pewności co do użycia, typową Konwencją jest użyć małe wersji nazwa projektu, w którym spacje są przekształcane w łączników, np. "Moja notesu projekt" (obcięty, jeśli to konieczne dopasować limit długości). |
| Public | Jeśli ustawiona, umożliwia każdemu z linkiem do dostępu do projektu. Podczas tworzenia projektu prywatny, usuń zaznaczenie tej opcji. |

Importowanie repozytorium z serwisu GitHub importuje także jego historię. Można użyć standardowych poleceń usługi Git z poziomu terminalu Zatwierdź nowych zmian, ściąganie zmian z usługi GitHub i tak dalej.

## <a name="clone-a-project"></a>Klonowanie projektu

Klonowanie tworzy kopię istniejącego projektu w własnego konta, w którym można uruchomić i zmodyfikować wszelkie Notatnika lub innego pliku w projekcie. Umożliwia także klonowania, aby utworzyć kopie własnych projektów, w których wykonasz eksperymentów lub inne prace, bez naruszania oryginalnego projektu.

Klonowanie projektu:

1. Na pulpicie nawigacyjnym **Moje projekty** kliknij prawym przyciskiem myszy żądany projekt i wybierz polecenie **Klonuj** (skrót klawiaturowy: c).

    ![Polecenia klonowania w menu kontekstowym projektu](media/clone-command.png)

1. W oknie podręcznym **klonowanie projektu** wprowadź nazwę i Identyfikator klonu i określ, czy klon jest publiczny. Te ustawienia są takie same jak dla [nowego projektu](#create-a-new-project).

    ![Okno podręczne projektu klonowania](media/clone-project.png)

1. Po wybraniu przycisku **klonuj** Azure Notebooks nawiguje bezpośrednio do kopii.

## <a name="next-steps"></a>Następne kroki

- [Explore sample notebooks (Eksplorowanie przykładowych notesów)](azure-notebooks-samples.md)
- [Instrukcje: Konfigurowanie projektów i zarządzanie nimi](configure-manage-azure-notebooks-projects.md)
- [Instrukcje: Instalowanie pakietów z poziomu notesu](install-packages-jupyter-notebook.md)
- [Instrukcje: prezentowanie pokazu slajdów](present-jupyter-notebooks-slideshow.md)
- [Instrukcje: korzystanie z plików danych](work-with-project-data-files.md)
- [Instrukcje: dostęp do zasobów danych](access-data-resources-jupyter-notebooks.md)
- [Instrukcje: korzystanie z Azure Machine Learning](use-machine-learning-services-jupyter-notebooks.md)
