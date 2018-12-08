---
title: Tworzenie i klonowanie notesy Jupyter notebook na platformie Azure
description: Projekty platformy Azure notesów zarządzanie kolekcją notesów i powiązane pliki, których można tworzyć nowe lub sklonować z innego źródła.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 9b6a49e2-1d71-4c0b-9e5d-16e059427e38
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: c4325f0c9de0d945d1612a3ab4171a2bfe1be25b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53106519"
---
# <a name="create-and-clone-projects"></a>Tworzenie i klonowanie projektów

Notesy platformy Azure organizuje Twoje notesów Jupyter i powiązane pliki w logiczne grupy o nazwie *projektów*. Jako kontener, najpierw Utwórz projekt, a następnie utworzyć lub sklonować co najmniej jeden notesów w folderze wraz z innych plików projektów. (Ten proces jest przedstawiona w [samouczek](tutorial-create-run-jupyter-notebook.md).)

Projekt udostępnia również metadane i pozostałe ustawienia konfiguracyjne, które wpływają na serwer, na które notesów uruchomić, w tym kroki instalacji niestandardowej i instalacji pakietu aktualizacji. Aby uzyskać więcej informacji, zobacz [zarządzanie i konfigurowanie projektów](configure-manage-azure-notebooks-projects.md).

## <a name="use-the-my-projects-dashboard"></a>Pulpit nawigacyjny Moje projekty

Twoje **Moje projekty** pulpit nawigacyjny na `https://notebooks.azure.com/<userID>/projects` pozwala wyświetlać, zarządzanie, i tworzyć projekty:

[![](media/my-projects-dashboard.png "Mój pulpit nawigacyjny projekty, w notesach platformy Azure")](media/my-projects-dashboard.png#lightbox)

Co można zrobić na pulpicie nawigacyjnym, zależy od tego, czy po zarejestrowaniu się przy użyciu konta, który jest właścicielem identyfikator użytkownika:

| Polecenie | Dostępna dla | Opis |
| --- | --- | --- |
| **Run** | Właściciel | Uruchamia serwer project server i otwiera folder projektu w programie Jupyter. (Częściej, możesz najpierw przejdź do folderu projektu, a następnie uruchomić Notes, w tym miejscu.) |
| **Pobieranie** | Każda osoba | Pobiera kopię wybranego projektu jako plik ZIP. |
| **Udostępnij** | Każda osoba | Wyświetla udostępniania okna podręcznego, za pomocą którego można uzyskać adres URL do wybranego projektu, Udostępnij w mediach społecznościowych, Wyślij wiadomość e-mail z adresem URL i uzyskać zarówno HTML lub języka znaczników Markdown kod ze wskaźnikiem "uruchamiania notesu" (zobacz [uzyskać wskaźnik uruchamiania](#obtain-a-launch-badge)) za pomocą adresu URL. |
| **Usuwanie** | Właściciel | Usuwa wybranego projektu. Tej operacji nie można cofnąć. |
| **Terminal** | Właściciel | Rozpoczyna się na serwerze project server, a następnie zostanie otwarte nowe okno przeglądarki, przy użyciu programu bash terminalu dla tego serwera. |
| **+ Nowy projekt** | Właściciel | Tworzy nowy projekt. Zobacz [Utwórz nowy projekt](#create-a-new-project). |
| **Przekaż repozytorium Github** | Właściciel | Importuje projektu z usługi GitHub. [Importowanie projektu z repozytorium GitHub](#import-a-project-from-github). |
| **Klon** | Każda osoba | Kopiuje wybrany projekt do własnego konta. Monituje o Zaloguj się w przypadku jeszcze nie jest zalogowany. Zobacz [klonowanie projektu](#clone-a-project). |

### <a name="obtain-a-launch-badge"></a>Uzyskaj wskaźnik uruchamiania

Kiedy używasz **udziału** polecenia, a następnie wybierz pozycję **osadzania** karty, możesz skopiować kod HTML lub języka znaczników Markdown, który tworzy wskaźnik "uruchamiania notesu":

![Uruchamianie notesu wskaźnika ](https://notebooks.azure.com/launch.png)

Jeśli nie masz projektu notesów usługi Azure można utworzyć łącze, które klony z serwisu GitHub bezpośrednio przy użyciu następujących szablonów, podstawiając odpowiednie nazwy użytkownika i nazwy repozytoriów:

```html
<a href="https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>"><img src="https://notebooks.azure.com/launch.png" /></a>
```

```markdown
[![Azure Notebooks](https://notebooks.azure.com/launch.png)](https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>)
```

## <a name="create-a-new-project"></a>Tworzenie nowego projektu

Kiedy używasz **+ nowy projekt** polecenie wyświetla notesów usługi Azure **Utwórz nowy projekt** okna podręcznego. W tym okno podręczne, wprowadź następujące informacje, a następnie wybierz **Utwórz**:

| Pole | Opis |
| --- | --- |
| Project name (Nazwa projektu) | Przyjazna nazwa dla projektu, które korzysta z notesów usługi Azure w celach wyświetlania. Na przykład "Ny notesu projektu". |
| Identyfikator projektu | Niestandardowy identyfikator, który staje się częścią adresu URL, możesz używać do udostępniania projektu. Tego Identyfikatora można użyć tylko litery, cyfry i łączniki i jest ograniczona do 30 znaków. Jeśli masz pewności co do użycia, typową Konwencją jest użyć małe wersji nazwa projektu, w którym spacje są przekształcane w łączników, np. "Moja notesu projekt" (obcięty, jeśli to konieczne dopasować limit długości). |
| Public | Jeśli ustawiona, umożliwia każdemu z linkiem do dostępu do projektu. Podczas tworzenia projektu prywatny, usuń zaznaczenie tej opcji. |
| Inicjowanie projektu z pliku README | Jeśli ustawiona, tworzy domyślną klasę *README.md* pliku w projekcie. A *README.md* plików są określane dokumentacji dla projektu, w razie potrzeby. |

## <a name="import-a-project-from-github"></a>Importowanie projektu z usługi GitHub

Można łatwo importować całego publicznego repozytorium GitHub jako projekt, łącznie z danymi i *README.md* plików. Użyj **repozytorium GitHub, aby przekazać** polecenia, należy podać następujące dane w okienku wyskakującym, a następnie wybierz **importu**:

| Pole | Opis |
| --- | --- |
| Repozytorium serwisu GitHub | Nazwa repozytorium źródłowe w witrynie github.com. Na przykład, aby sklonować notesów programu Jupyter dla usług Azure Cognitive Services na [ https://github.com/Microsoft/cognitive-services-notebooks ](https://github.com/Microsoft/cognitive-services-notebooks), wpisz "Microsoft/cognitive-services notesów".  |
| Rekursywnie Klonuj | Repozytoria GitHub może zawierać wiele repozytoriów podrzędnych. Ustaw tę opcję, jeśli chcesz klonować repozytorium nadrzędnego i jego elementów podrzędnych. Ponieważ jest możliwe w przypadku repozytorium można mieć wiele podrzędnych, pozostaw tę opcję, wyczyść, chyba że wiesz, że będą potrzebne. |
| Project name (Nazwa projektu) | Przyjazna nazwa dla projektu, które korzysta z notesów usługi Azure w celach wyświetlania. |
| Identyfikator projektu | Niestandardowy identyfikator, który staje się częścią adresu URL, możesz używać do udostępniania projektu. Tego Identyfikatora można użyć tylko litery, cyfry i łączniki. |
| Public | Jeśli ustawiona, umożliwia każdemu z linkiem do dostępu do projektu. Podczas tworzenia projektu prywatny, usuń zaznaczenie tej opcji. |

Importowanie repozytorium z serwisu GitHub importuje także jego historię. Można użyć standardowych poleceń usługi Git z poziomu terminalu Zatwierdź nowych zmian, ściąganie zmian z usługi GitHub i tak dalej.

## <a name="clone-a-project"></a>Klonowanie projektu

Klonowanie tworzy kopię istniejącego projektu w własnego konta, w którym można uruchomić i zmodyfikować wszelkie Notatnika lub innego pliku w projekcie. Umożliwia także klonowania, aby utworzyć kopie własnych projektów, w których wykonasz eksperymentów lub inne prace, bez naruszania oryginalnego projektu.

Klonowanie projektu:

1. Na **Moje projekty** pulpitu nawigacyjnego, kliknij prawym przyciskiem myszy żądany projekt i wybierz **klonowania** (skrót klawiaturowy: c).

    ![Polecenia klonowania w menu kontekstowym projektu](media/clone-command.png)

1. W **projektu klonowania** popup, wprowadź nazwę i identyfikator dla klonu i określ, czy klon nie jest publiczny. Te ustawienia są takie same jak w przypadku [nowy projekt](#create-a-new-project).

    ![Okno podręczne projektu klonowania](media/clone-project.png)

1. Po wybraniu **klonowania** przycisku, notesy platformy Azure przechodzi bezpośrednio do skopiowania.

## <a name="next-steps"></a>Kolejne kroki

- [Eksploruj przykładowe notesów](azure-notebooks-samples.md)
- [Porady: Konfigurowanie projektów i zarządzanie nimi](configure-manage-azure-notebooks-projects.md)
- [Porady: Instalowanie pakietów z w ramach notesu](install-packages-jupyter-notebook.md)
- [Porady: obecne pokaz slajdów](present-jupyter-notebooks-slideshow.md)
- [Porady: Praca z plikami danych](work-with-project-data-files.md)
- [Porady: dostęp do zasobów danych](access-data-resources-jupyter-notebooks.md)
- [Porady: Użyj aplikacji Azure Machine Learning Services](use-machine-learning-services-jupyter-notebooks.md)
