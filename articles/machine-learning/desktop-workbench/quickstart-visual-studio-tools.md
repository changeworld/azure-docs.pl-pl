---
title: "Artykuł Szybki start dotyczący narzędzi Visual Studio Tools dla usługi Machine Learning na platformie Azure | Microsoft Docs"
description: "W tym artykule opisano rozpoczynanie korzystania z narzędzi Visual Studio Tools dla usługi Machine Learning, począwszy od tworzenia eksperymentu, uczenia modelu i operacjonalizowania usługi internetowej."
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/15/2017
ms.openlocfilehash: bbcb2ea5a7ceeb976f590393608cc29c67d9a49e
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2018
---
# <a name="visual-studio-tools-for-ai"></a>Visual Studio Tools for AI
Visual Studio Tools for AI to rozszerzenie programistyczne do tworzenia, testowania i wdrażania rozwiązań uczenia głębokiego/sztucznej inteligencji. Obejmuje ono bezproblemową integrację z usługą Azure Machine Learning. Szczególnie dotyczy to widoku historii uruchamiania zawierającej szczegółowe dane o wydajności poprzednich cykli uczenia i metryki niestandardowe. Oferuje ono przykładowy widok eksploratora pozwalający na przeglądanie i uruchamianie nowego projektu za pomocą [zestawu narzędzi Microsoft Cognitive Toolkit (wcześniej znanego jako CNTK)](http://www.microsoft.com/en-us/cognitive-toolkit), [Google TensorFlow](https://www.tensorflow.org) i innych platform uczenia głębokiego. Ponadto zapewnia ono eksploratora dla celów obliczeń, który umożliwia przesyłanie zadań uczenia modeli w zdalnych środowiskach, takich jak usługi Azure Virtual Machines lub serwery systemu Linux z procesorem GPU. Dodatkowo ułatwia ono dostęp do rozwiązania [Azure Batch AI (w wersji zapoznawczej)](https://docs.microsoft.com/azure/batch-ai/).
 
## <a name="getting-started"></a>Wprowadzenie 
Aby rozpocząć, musisz najpierw pobrać i zainstalować program [Visual Studio](https://www.visualstudio.com/downloads/). Po otwarciu programu Visual Studio wykonaj następujące czynności:
1. Kliknij pasek menu programu Visual Studio i wybierz pozycję „Rozszerzenia i aktualizacje...”.
2. Kliknij kartę „Online” i wybierz polecenie „Przeszukaj witrynę Visual Studio Marketplace”.
3. Wyszukaj frazę „Visual Studio for AI”. 
3. Kliknij przycisk **Pobierz**. 
4. Po zakończeniu instalacji uruchom ponownie program Visual Studio. 

Po ponownym załadowaniu programu Visual Studio nastąpi uaktywnienie rozszerzenia. [Dowiedz się więcej na temat wyszukiwania rozszerzeń](hhttps://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions).

> [!NOTE]
> Narzędzia Visual Studio Tools for AI wymagają programu Visual Studio 2015 lub 2017 w wersji Professional lub Enterprise. Wersja dla systemu OSX firmy Apple nie jest obsługiwana. 


## <a name="exploring-project-samples"></a>Przykłady eksplorowania projektu
Narzędzia Visual Studio Tools for AI są dostarczane wraz z eksploratorem przykładów. Eksplorator przykładów ułatwia wykrywanie przykładów i ich próbowanie za pomocą tylko kilku kliknięć. Aby otworzyć eksploratora, wykonaj następujące czynności:   
1. Na pasku menu kliknij pozycję **Narzędzia SI**.
2. Kliknij pozycję „Galeria usługi Azure Machine Learning”.

Zostanie otwarta karta z wszystkimi przykładami użycia usługi Azure ML.

## <a name="creating-a-new-project-from-the-sample-explorer"></a>Tworzenie nowego projektu za pomocą eksploratora przykładów 
Różne przykłady można przeglądać i uzyskiwać więcej informacji o nich. Teraz wyszukajmy przykład „Klasyfikowanie irysów”. Aby utworzyć nowy projekt na podstawie tego przykładu, wykonaj następujące czynności:
1. Kliknij przycisk **Zainstaluj** w przykładowym projekcie. Zostanie otwarte nowe okno dialogowe. 
2. Wybierz grupę zasobów, konto i obszar roboczy.
3. Pozostaw ogólny typ projektu.
4. Wprowadź ścieżkę i nazwę projektu, a następnie naciśnij klawisz Enter. 
5. Pojawi się okno dialogowe z monitem o zapisanie rozwiązania. Kliknij przycisk Zapisz. 

Po wykonaniu tych czynności nowy projekt zostanie otwarty w nowym wystąpieniu programu Visual Studio. 

> [!TIP]
> Aby uzyskać dostęp do swoich zasobów platformy Azure, trzeba się zalogować. W osadzonym terminalu wprowadź polecenie „az login”, a następnie postępuj zgodnie z instrukcjami. 

## <a name="submitting-experiment-with-the-new-project"></a>Przesyłanie eksperymentu za pomocą nowego projektu
Po otwarciu nowego projektu w programie Visual Studio prześlij zadanie do obliczeniowego elementu docelowego (lokalnego lub maszyny wirtualnej z rozwiązaniem Docker).
Aby przesłać zadanie, wykonaj następujące czynności: 
1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy plik, który chcesz przesłać, i wybierz polecenie **Ustaw jako plik startowy**.
2. Wybierz nazwę projektu, kliknij ją prawym przyciskiem myszy i wybierz polecenie **Prześlij zadanie...**
3. Zostanie otwarte nowe okno dialogowe umożliwiające wybranie klastra (lub obliczeniowego elementu docelowego) do wykonania skryptu.
4. Kliknij pozycję **Prześlij**.

Po przesłaniu zadania osadzony terminal wyświetla postęp przebiegów.

## <a name="view-list-of-jobs"></a>Wyświetlanie listy zadań
Po przesłaniu zadania możesz wyświetlić listę zadań z historii uruchamiania.
1. W **Eksploratorze serwera** kliknij pozycję **Narzędzia SI**.
2. Wybierz pozycję **Azure Machine Learning**.
3. Kliknij menu **Zadania**.

Eksplorator zadań zawiera listę wszystkich przesłanych eksperymentów w tym projekcie. 

## <a name="view-job-details"></a>Wyświetlanie szczegółów zadania
Przy otwartym widoku eksploratora zadań kliknij pierwszy przebieg na liście.
Zostaną załadowane panele Podsumowanie zadania oraz Dzienniki i dane wyjściowe.

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [How to configure Azure Machine Learning to work with an IDE (Konfigurowanie usługi Azure Machine Learning do współpracy ze środowiskiem IDE)](./how-to-configure-your-IDE.md)
