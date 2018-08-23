---
title: Artykuł Szybki start dla narzędzi Visual Studio Code dla usługi Machine Learning na platformie Azure | Microsoft Docs
description: W tym artykule opisano sposób rozpoczynania korzystania z narzędzi Visual Studio Code dla usługi Machine Learning, poczynając od utworzenia eksperymentu, uczenia modelu i operacjonalizowania usługi internetowej.
services: machine-learning
author: chris-lauren
ms.author: chris.lauren
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/15/2017
ms.openlocfilehash: a215c562ad15b69dcec20c1951fe8bc3fe80c6ff
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2018
ms.locfileid: "42023241"
---
# <a name="visual-studio-code-tools-for-ai"></a>Narzędzia Visual Studio Code dla sztucznej inteligencji
Narzędzia Visual Studio Code dla sztucznej inteligencji to rozszerzenie programistyczne do tworzenia, testowania i wdrażania rozwiązań uczenia głębokiego / sztucznej inteligencji. Obejmuje ono bezproblemową integrację z usługą Azure Machine Learning. Szczególnie dotyczy to widoku historii uruchamiania zawierającej szczegółowe dane o wydajności poprzednich cykli uczenia i metryki niestandardowe. Oferuje ono przykładowy widok eksploratora pozwalający na przeglądanie i uruchamianie nowego projektu za pomocą [zestawu narzędzi Microsoft Cognitive Toolkit (wcześniej znanego jako CNTK)](http://www.microsoft.com/en-us/cognitive-toolkit), [Google TensorFlow](https://www.tensorflow.org) i innych platform uczenia głębokiego. Ponadto zapewnia ono eksploratora dla celów obliczeń, który umożliwia przesyłanie zadań uczenia modeli w zdalnych środowiskach, takich jak usługi Azure Virtual Machines lub serwery systemu Linux z procesorem GPU. 
 
## <a name="getting-started"></a>Wprowadzenie 
Aby rozpocząć, musisz najpierw pobrać i zainstalować program [Visual Studio Code](https://code.visualstudio.com/Download). Po otwarciu programu Visual Studio Code wykonaj następujące czynności:
1. Kliknij ikonę rozszerzenia na pasku działań. 
2. Wyszukaj frazę „Narzędzia Visual Studio Code dla sztucznej inteligencji”. 
3. Kliknij przycisk **Instaluj**. 
4. Po zakończeniu instalacji kliknij przycisk **Załaduj ponownie**. 

Po ponownym załadowaniu programu Visual Studio Code nastąpi uaktywnienie rozszerzenia. [Dowiedz się więcej na temat instalowania rozszerzeń](https://code.visualstudio.com/docs/editor/extension-gallery).

## <a name="exploring-project-samples"></a>Przykłady eksplorowania projektu
Narzędzia Visual Studio Code dla sztucznej inteligencji są dostarczane wraz z eksploratorem przykładów. Eksplorator przykładów ułatwia wykrywanie przykładów i ich próbowanie za pomocą tylko kilku kliknięć. Aby otworzyć eksploratora, wykonaj następujące czynności:   
1. Otwórz paletę poleceń (Widok > **Paleta poleceń** lub **Ctrl+Shift+P**).
2. Wprowadź ciąg „Przykład SI”. 
3. Zostanie wyświetlona rekomendacja „SI: Otwórz eksploratora przykładów usługi Azure ML”. Zaznacz ją i naciśnij klawisz Enter. 

Alternatywnie możesz kliknąć ikonę eksploratora przykładów.

## <a name="creating-a-new-project-from-the-sample-explorer"></a>Tworzenie nowego projektu za pomocą eksploratora przykładów 
Różne przykłady można przeglądać i uzyskiwać więcej informacji o nich. Teraz wyszukajmy przykład „Klasyfikowanie irysów”. Aby utworzyć nowy projekt na podstawie tego przykładu, wykonaj następujące czynności:
1. Kliknij przycisk Instaluj w przykładzie projektu, zwróć uwagę na monity poleceń prowadzące przez kroki tworzenia nowego projektu. 
2. Wybierz nazwę dla projektu, na przykład „Irys”.
3. Wybierz ścieżkę folderu, aby utworzyć własny projekt, a następnie naciśnij klawisz Enter. 
4. Wybierz istniejący obszar roboczy, a następnie naciśnij klawisz Enter.

Zostanie utworzony projekt.

> [!TIP]
> Aby uzyskać dostęp do swoich zasobów platformy Azure, trzeba się zalogować. W osadzonym terminalu wprowadź polecenie „az login”, a następnie postępuj zgodnie z instrukcjami. 

## <a name="submitting-experiment-with-the-new-project"></a>Przesyłanie eksperymentu za pomocą nowego projektu
Nowy projekt jest otwierany w programie Visual Studio Code i możemy przesłać zadanie do naszego innego celu obliczeń (lokalne i wirtualne maszyny z platformą Docker).
Narzędzia Visual Studio Code dla sztucznej inteligencji zapewniają wiele sposobów przesyłania eksperymentu. 
1. Menu kontekstowe (kliknij prawym przyciskiem myszy) — **SI: Prześlij zadanie**.
2. Z palety poleceń: „SI: Prześlij zadanie”.
3. Alternatywnie można uruchomić polecenie bezpośrednio przy użyciu interfejsu wiersza polecenia platformy Azure i poleceń usługi Machine Learning, używając osadzonego terminala.

Otwórz plik iris_sklearn.py, kliknij prawym przyciskiem myszy i wybierz pozycję **SI: Prześlij zadanie**.
1. Wybierz swoją platformę: „Azure Machine Learning”.
2. Wybierz swoją konfigurację uruchamiania: „Docker Python”.

> [!NOTE]
> Jeśli po raz pierwszy przesyłasz zadanie, zostanie wyświetlony komunikat „Nie odnaleziono konfiguracji uczenia maszynowego, trwa tworzenie...”. Zostanie otwarty plik JSON, zapisz go (**Ctrl+S**).

Po przesłaniu zadania osadzony terminal wyświetla postęp przebiegów. 

## <a name="view-list-of-jobs"></a>Wyświetlanie listy zadań
Po przesłaniu zadań możesz wyświetlić listę zadań z historii uruchamiania.
1. Otwórz paletę poleceń (Widok > **Paleta poleceń** lub **Ctrl+Shift+P**).
2. Wprowadź ciąg „Lista SI”.
3. Zostanie wyświetlona rekomendacja „SI: Lista zadań”. Zaznacz ją i naciśnij klawisz Enter.

Zostanie otwarty widok listy zadań, w którym będą wyświetlane wszystkie przebiegi oraz niektóre powiązane informacje.

## <a name="view-job-details"></a>Wyświetlanie szczegółów zadania
Mając nadal otwartą listę zadań, kliknij pierwszy przebieg na liście.
Aby zagłębić się w wyniki zadania, kliknij u góry **identyfikator zadania** w celu wyświetlenia szczegółowych informacji. 

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [How to configure Azure Machine Learning to work with an IDE (Konfigurowanie usługi Azure Machine Learning do współpracy ze środowiskiem IDE)](./how-to-configure-your-IDE.md)
