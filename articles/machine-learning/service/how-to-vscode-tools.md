---
title: Za pomocą narzędzi Visual Studio Code dla sztucznej Inteligencji rozszerzenia za pomocą usługi Azure Machine Learning
description: Więcej informacji na temat narzędzia Visual Studio Code dla sztucznej Inteligencji i Rozpocznij szkolenie i wdrażanie usługi machine learning i głębokiego uczenia modeli za pomocą usługi Azure Machine Learning w programie VS Code.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: jmartens
author: j-martens
ms.reviewer: jmartens
ms.date: 10/1/2018
ms.openlocfilehash: a2f37cffb37ce7008c3e372763784240e0d4250b
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945556"
---
# <a name="vs-code-tools-for-ai-get-started-with-azure-machine-learning-from-visual-studio-code"></a>Program VS Code Tools for AI: rozpoczynanie pracy z usługą Azure Machine Learning z programu Visual Studio Code

W tym artykule dowiesz się o rozszerzeniu programu Visual Studio Code (VS Code) **Tools for AI**oraz jak uruchomić szkolenia i wdrażanie usługi machine learning i głębokiego uczenia modeli za pomocą usługi Azure Machine Learning w programie VS Code.

Użyj rozszerzenia Tools for AI w programie Visual Studio code przeznaczonego do przygotowania danych, szkolenie i test uczenia Maszynowego za pomocą usługi Azure Machine Learning models na lokalnych i zdalnych celów obliczeń, wdrażać te modele i Śledź niestandardowe metryki i eksperymentów.

## <a name="prerequisite"></a>Wymagania wstępne

+ Musi być zainstalowany program Visual Studio Code. Program VS Code jest źródło lekki, ale Zaawansowany edytor kodu, działającą na pulpicie. Pochodzi z wbudowaną obsługą języka Python i innych.  [Dowiedz się, jak zainstalować program VS Code](https://code.visualstudio.com/docs/setup/setup-overview).

+ [Zainstaluj język Python 3.5 lub nowszej](https://www.anaconda.com/download/).

+ Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="install-vs-code-tools-for-ai-extension"></a>Instalowanie narzędzi programu VS Code dla sztucznej Inteligencji rozszerzenia

Po zainstalowaniu **Tools for AI** rozszerzenia dwóch więcej rozszerzeń są automatycznie instalowane (Jeśli masz dostęp do Internetu). Są one [konta platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) rozszerzenia i [Microsoft Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) rozszerzenia

Aby pracować z usługą Azure Machine Learning, musimy włączyć program VS Code w środowisko IDE języka Python. Praca z [języka Python w programie Visual Studio Code](https://code.visualstudio.com/docs/languages/python), wymaga rozszerzenia Microsoft Python, który pobiera instalowana z narzędziami Tools for AI automatycznie. Rozszerzenie sprawia, że program VS Code doskonałym środowisku IDE i działa w dowolnym systemie operacyjnym z różnymi rodzajami interpreterów języka Python. Wykorzystuje wszystkie mocy programu VS Code w celu zapewnienia Autouzupełnianie i technologii IntelliSense, Zaznaczanie błędów, debugowania i testowania jednostkowego, wraz z możliwością łatwego przełączania się między środowiskami Python, w tym wirtualnych i środowisk conda. Zapoznaj się z tego przewodnika, edytowania, uruchomiona i debugowanie kodu w języku Python, zobacz [samouczek języka Python Hello World](https://code.visualstudio.com/docs/python/python-tutorial)

**Aby zainstalować rozszerzenia Tools for AI:**

1. Uruchom program VS Code.

1. W przeglądarce, odwiedź stronę: http://aka.ms/vscodetoolsforai. 

1. Na tej stronie internetowej kliknij **zainstalować**. 

1. Na karcie rozszerzenia kliknij **zainstalować**.

1. Zapraszamy zostanie otwarta karta w programie VS Code dla rozszerzenia, a symbol platformy Azure jest dodawany do pasku działań.

   ![Usługa Azure ikona na pasku działań programu Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. W oknie dialogowym kliknij **Sign In** i postępuj zgodnie z wyświetlanymi na ekranie po wyświetleniu monitu uwierzytelniania w systemie Azure. 
   
   Rozszerzenie Azure Account programu, który został zainstalowany wraz z programu VS Code Tools for AI, służy do uwierzytelniania przy użyciu konta platformy Azure. Zapoznać się z listą poleceń w [rozszerzenie Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) strony.

> [!Tip] 
> Zapoznaj się z [rozszerzenia IntelliCode dla programu VS Code (wersja zapoznawcza)](https://go.microsoft.com/fwlink/?linkid=2006060). Funkcja IntelliCode zapewnia zestaw funkcji, wspierane przez Sztuczną inteligencję dla technologii IntelliSense w języku Python, takich jak wnioskowanie najistotniejsze automatycznego uzupełniania oparciu o bieżący kontekst kodu.

## <a name="install-the-sdk"></a>Instalacja zestawu SDK

1. Upewnij się, że języka Python w wersji 3.5 lub nowszej jest zainstalowane i rozpoznawane przez program VS Code. Jeśli zainstalujesz go teraz, uruchom ponownie program VS Code i wybierz interpreter języka Python, zgodnie z instrukcjami przedstawionymi w https://code.visualstudio.com/docs/python/python-tutorial.

1. W programie VS Code, należy otworzyć paletę poleceń **Ctrl + Shift + P**.

1. Typ "Instalowanie usługi Azure ML" Aby znaleźć publiczny adres IP można zainstalować zestawu SDK polecenia dla zestawu SDK. Lokalne prywatne środowisko Python jest tworzony, który ma wymagania wstępne programu Visual Studio Code do pracy z usługą Azure Machine Learning.
   ![Zainstaluj](./media/vscode-tools-for-ai/install-sdk.png)

1. W oknie terminalu zintegrowanego, należy określić interpreter języka Python do użycia lub mogą trafić **Enter** do użycia z Twojej domyślnej interpreter języka Python.

   ![Zainstaluj](./media/vscode-tools-for-ai/python.png)

## <a name="get-started-with-azure-machine-learning"></a>Rozpocznij pracę z usługą Azure Machine Learning

Przed rozpoczęciem szkolenia i wdrażania modeli uczenia Maszynowego za pomocą programu VS Code, należy utworzyć [obszaru roboczego usługi Azure Machine Learning](concept-azure-machine-learning-architecture.md#workspace) w chmurze, które zawierają modeli i zasobów. Dowiedz się, jak utworzyć i Utwórz swoje pierwsze eksperymentalne rozwiązanie w tym obszarze roboczym.

1. Kliknij ikonę platformy Azure, w pasku działań programu Visual Studio Code. Azure: Pasku bocznym usługi Machine Learning jest wyświetlana.

   ![Zainstaluj](./media/vscode-tools-for-ai/createworkspace.gif)

1. Kliknij prawym przyciskiem myszy subskrypcji platformy Azure, a następnie wybierz pozycję **Utwórz obszar roboczy**. Zostanie wyświetlona lista. W animowany obraz 'OpenMind Studio' jest nazwa subskrypcji i obszar roboczy jest "MyWorkspace". 

1. Wybierz istniejącą grupę zasobów z listy lub Utwórz nowe konto, za pomocą kreatora w palecie poleceń.

1. W polu wpisz unikatowy i wyczyść nazwę nowego obszaru roboczego. Zrzuty ekranu obszar roboczy ma nazwę "MyWorkspace".

1. Naciśnij klawisz enter, i zostanie utworzony nowy obszar roboczy. Wygląda na to w drzewie poniżej nazwę subskrypcji.

1. Kliknij prawym przyciskiem myszy nazwę obszaru roboczego i wybierz polecenie **tworzenia eksperymentu** z menu kontekstowego.  Eksperymenty zachować informacje o przebiegów przy użyciu usługi Azure Machine Learning.

1. W polu wprowadź nazwę swojego eksperymentu. Zrzuty ekranu eksperymentu nosi nazwę "Mnist ręcznie ZAPISANYCH".
 
1. Naciśnij klawisz enter i zostanie utworzony nowy eksperyment. Wygląda na to w drzewie poniżej nazwę obszaru roboczego.

1. Kliknij prawym przyciskiem myszy nazwę eksperymentu, a następnie wybierz **dołączyć folder lokalny**. Ten folder powinien zawierać lokalnych skryptów w języku Python. Folder jest następnie łączony z eksperymentu w chmurze. 

   Teraz każdy eksperymentu działa z eksperymentu więc wszystkie kluczowe metryki będą zapisywane w historii eksperymentu i modeli, które możesz później uzyskać automatycznie przekazany do usługi Azure Machine Learning i przechowywane w usłudze Dzienniki i metryki eksperymentu.

   [![Dołącz folderu w programie VS Code](./media/vscode-tools-for-ai/attachfolder.gif)](./media/vscode-tools-for-ai/attachfolder.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>Używanie skrótów klawiaturowych

Najbardziej programu VS Code funkcje usługi Azure Machine Learning w programie VS Code są dostępne z klawiatury. Najważniejsze kombinację klawiszy, aby wiedzieć, jest Ctrl + Shift + P, co umożliwia wyświetlenie paletę poleceń. W tym miejscu możesz mieć dostęp do wszystkich funkcji programu VS Code, w tym skrótów klawiaturowych dla typowych operacji.

[![Skróty klawiaturowe dla programu VS Code Tools for AI](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Kolejne kroki

Można teraz używać programu Visual Studio Code do pracy z usługą Azure Machine Learning.

Dowiedz się, jak [Tworzenie celów obliczeń, uczyć i wdrażać modele w programie Visual Studio Code](how-to-vscode-train-deploy.md).