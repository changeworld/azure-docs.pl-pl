---
title: Użyj programu Visual Studio Code za pomocą
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak zainstalować usługi Azure Machine Learning dla programu Visual Studio Code i tworzenie prostego eksperymentu w usłudze Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 5e7e405bdb251bbebc5da81253eea2414b434cb8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "57838320"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Rozpoczynanie pracy z usługą Azure Machine Learning dla programu Visual Studio Code

W tym artykule dowiesz się, jak zainstalować **usługi Azure Machine Learning dla programu Visual Studio Code** rozszerzenia i Tworzenie pierwszego eksperymentu w usłudze Azure Machine Learning w programie Visual Studio Code (VS Code).

Użyj rozszerzenia usługi Azure Machine Learning w programie Visual Studio code za pomocą usługi Azure Machine Learning przeznaczonego do przygotowania danych, szkolenie i badania modeli uczenia maszynowego w lokalnych i zdalnych obliczeniowych elementów docelowych, wdrażać te modele i Śledź niestandardowe metryki i eksperymentów.

## <a name="prerequisites"></a>Wymagania wstępne


+ Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

+ Musi być zainstalowany program Visual Studio Code. Program VS Code jest źródło lekki, ale Zaawansowany edytor kodu, działającą na pulpicie. Pochodzi z wbudowaną obsługą języka Python i innych.  [Dowiedz się, jak zainstalować program VS Code](https://code.visualstudio.com/docs/setup/setup-overview).

+ [Zainstaluj język Python 3.5 lub nowszej](https://www.anaconda.com/download/).


## <a name="install-the-azure-machine-learning-for-vs-code-extension"></a>Instalowanie usługi Azure Machine Learning dla rozszerzenia programu VS Code

Po zainstalowaniu **usługi Azure Machine Learning** rozszerzenia dwóch więcej rozszerzeń są automatycznie instalowane (Jeśli masz dostęp do Internetu). Są one [konta platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) rozszerzenia i [Microsoft Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) rozszerzenia

Aby pracować z usługą Azure Machine Learning, musimy włączyć program VS Code w środowisko IDE języka Python. Praca z [języka Python w programie Visual Studio Code](https://code.visualstudio.com/docs/languages/python), wymaga rozszerzenia Microsoft Python, który zostanie automatycznie zainstalowana z rozszerzeniem usługi Azure Machine Learning. Rozszerzenie sprawia, że program VS Code doskonałym środowisku IDE i działa w dowolnym systemie operacyjnym z różnymi rodzajami interpreterów języka Python. Wykorzystuje wszystkie mocy programu VS Code w celu zapewnienia Autouzupełnianie i technologii IntelliSense, Zaznaczanie błędów, debugowania i testowania jednostkowego, wraz z możliwością łatwego przełączania się między środowiskami Python, w tym wirtualnych i środowisk conda. Zapoznaj się z tego przewodnika, edytowania, uruchomiona i debugowanie kodu w języku Python, zobacz [samouczek języka Python Hello World](https://code.visualstudio.com/docs/python/python-tutorial)

**Aby zainstalować rozszerzenia usługi Azure Machine Learning:**

1. Uruchom program VS Code.

1. W przeglądarce odwiedź stronę: [Usługa Azure Machine Learning dla programu Visual Studio Code (wersja zapoznawcza)](https://aka.ms/vscodetoolsforai) rozszerzenia

1. Na tej stronie internetowej kliknij **zainstalować**. 

1. Na karcie rozszerzenia kliknij **zainstalować**.

1. Zapraszamy zostanie otwarta karta w programie VS Code dla rozszerzenia, a symbol platformy Azure jest dodawany do pasku działań.

   ![Usługa Azure ikona na pasku działań programu Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. W oknie dialogowym kliknij **Sign In** i postępuj zgodnie z wyświetlanymi na ekranie po wyświetleniu monitu uwierzytelniania w systemie Azure. 
   
   Rozszerzenie Azure Account programu, który został zainstalowany wraz z usługi Azure Machine Learning dla rozszerzenia programu VS Code, służy do uwierzytelniania przy użyciu konta platformy Azure. Zapoznać się z listą poleceń w [rozszerzenie Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) strony.

> [!Tip] 
> Zapoznaj się z [rozszerzenia IntelliCode dla programu VS Code (wersja zapoznawcza)](https://go.microsoft.com/fwlink/?linkid=2006060). Funkcja IntelliCode zapewnia zestaw funkcji, wspierane przez Sztuczną inteligencję dla technologii IntelliSense w języku Python, takich jak wnioskowanie najistotniejsze automatycznego uzupełniania oparciu o bieżący kontekst kodu.

## <a name="azure-ml-sdk-installation"></a>Zestaw SDK usługi Azure ML instalacji

1. Upewnij się, że języka Python w wersji 3.5 lub nowszej jest zainstalowane i rozpoznawane przez program VS Code. Jeśli zainstalujesz go teraz, uruchom ponownie program VS Code i wybierz interpreter języka Python, zgodnie z instrukcjami przedstawionymi w https://code.visualstudio.com/docs/python/python-tutorial.

1. W oknie terminalu zintegrowanego, należy określić interpreter języka Python do użycia lub mogą trafić **Enter** do użycia z Twojej domyślnej interpreter języka Python.

   ![Wybierz interpreter](./media/vscode-tools-for-ai/python.png)

1. W prawym dolnym rogu okna wyświetlane powiadomienie wskazujące, że zestaw SDK usługi Azure ML jest instalowany automatycznie.    Lokalne prywatne środowisko Python jest tworzony, który ma wymagania wstępne programu Visual Studio Code do pracy z usługą Azure Machine Learning.

   ![Zainstaluj zestaw SDK usługi Azure Machine Learning dla języka Python](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>Rozpocznij pracę z usługą Azure Machine Learning

Przed rozpoczęciem szkolenia i wdrażania modeli uczenia maszynowego przy użyciu programu VS Code, należy utworzyć [obszarze roboczym usługi Azure Machine Learning](concept-azure-machine-learning-architecture.md#workspace) w chmurze, które zawierają modeli i zasobów. Dowiedz się, jak utworzyć i Utwórz swoje pierwsze eksperymentalne rozwiązanie w tym obszarze roboczym.

1. Kliknij ikonę platformy Azure, w pasku działań programu Visual Studio Code. Zostanie wyświetlona na pasku bocznym usługi Azure Machine Learning.

   [![install](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. Kliknij prawym przyciskiem myszy subskrypcji platformy Azure, a następnie wybierz pozycję **Utwórz obszar roboczy**. Zostanie wyświetlona lista. W animowany obraz bezpłatna wersja próbna jest nazwa subskrypcji i obszar roboczy jest "TeamWorkspace". 

1. Wybierz istniejącą grupę zasobów z listy lub Utwórz nowe konto, za pomocą kreatora w palecie poleceń.

1. W polu wpisz unikatowy i wyczyść nazwę nowego obszaru roboczego. Zrzuty ekranu obszar roboczy ma nazwę "TeamWorkspace".

1. Naciśnij klawisz enter, i zostanie utworzony nowy obszar roboczy. Wygląda na to w drzewie poniżej nazwę subskrypcji.

1. Kliknij prawym przyciskiem myszy w węźle eksperymentu, a następnie wybierz **tworzenia eksperymentu** z menu kontekstowego.  Eksperymenty zachować informacje o przebiegów przy użyciu usługi Azure Machine Learning.

1. W polu wprowadź nazwę swojego eksperymentu. Zrzuty ekranu eksperymentu nosi nazwę "Mnist ręcznie ZAPISANYCH".
 
1. Naciśnij klawisz enter i zostanie utworzony nowy eksperyment. Wygląda na to w drzewie poniżej nazwę obszaru roboczego.

1. Można kliknij prawym przyciskiem myszy na eksperyment w obszarze roboczym i wybierz pozycję "Ustaw jako aktywny eksperymentu". **"Aktywny"** eksperymentu jest eksperyment aktualnie używasz, i folderu Otwórz w programie VS Code zostanie połączony z tego eksperymentu w chmurze. Ten folder powinien zawierać lokalnych skryptów w języku Python.

   Teraz każdy eksperymentu działa z eksperymentu, więc wszystkie kluczowe metryki będą zapisywane w historii eksperymentu i modeli, które możesz później uzyskać automatycznie przekazany do usługi Azure Machine Learning i przechowywane w usłudze Dzienniki i metryki eksperymentu.

   [![Dołącz folderu w programie VS Code](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>Używanie skrótów klawiaturowych

Najbardziej programu VS Code funkcje usługi Azure Machine Learning w programie VS Code są dostępne z klawiatury. Najważniejsze kombinację klawiszy, aby wiedzieć, jest Ctrl + Shift + P, co umożliwia wyświetlenie paletę poleceń. W tym miejscu możesz mieć dostęp do wszystkich funkcji programu VS Code, w tym skrótów klawiaturowych dla typowych operacji.

[![Skróty klawiaturowe dla usługi Azure Machine Learning dla programu VS Code](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Kolejne kroki

Można teraz używać programu Visual Studio Code do pracy z usługą Azure Machine Learning.

Dowiedz się, jak [Tworzenie celów obliczeń, uczyć i wdrażać modele w programie Visual Studio Code](how-to-vscode-train-deploy.md).
