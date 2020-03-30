---
title: Tworzenie szablonów za pomocą programu Visual Studio Code
description: Dowiedz się, jak zainstalować i używać programu Visual Studio Code i rozszerzenia Narzędzia Usługi Azure Resource Manager.
author: mumian
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: jgao
ms.openlocfilehash: 585e2773ea5d6af184f85e65b63d39b60d632146
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273633"
---
# <a name="use-visual-studio-code-to-create-azure-resource-manager-templates"></a>Tworzenie szablonów usługi Azure Resource Manager za pomocą kodu programu Visual Studio

Visual Studio Code to lekki, wieloplatformowy edytor open source. Rozszerzenie Narzędzia szablonów usługi Azure Resource Manager to wtyczka do tworzenia szablonów usługi Resource Manager. Rozszerzenie dodaje obsługę języka dla szablonów, aby zapewnić intellisense, wyróżnianie składni, pomoc w wierszu i wiele innych funkcji językowych. Razem zapewniają zalecane środowisko tworzenia szablonów.

## <a name="install-visual-studio-code"></a>Instalowanie programu Visual Studio Code

Program Visual Studio Code obsługuje systemy MacOS, Windows i Linux.  Można go zainstalować z [programu Visual Studio Code](https://code.visualstudio.com/).

## <a name="install-resource-manager-tools-extension"></a>Zainstaluj rozszerzenie Narzędzia Menedżera zasobów

1. Otwórz program Visual Studio Code.
1. Z lewego menu wybierz **rozszerzenia.** Z menu **Widok** wybierz **pozycję Rozszerzenia,** aby otworzyć okienko Rozszerzenia.

    ![Instalowanie rozszerzenia Narzędzia Menedżera zasobów kodu programu Visual Studio](./media/use-vs-code-to-create-template/resource-manager-visual-studio-code-tools-extension.png)
1. Wyszukaj **Menedżera zasobów**.
1. Wybierz **pozycję Zainstaluj** w obszarze Narzędzia Usługi Azure Resource **Manager**.

## <a name="the-extension-features"></a>Funkcje rozszerzenia

### <a name="colorization-for-template-language-expressions"></a>Kolorowanie wyrażeń języka szablonu

Parametry, zmienne, funkcje, nazwy i wyrażenia są oznaczone kolorami, jak pokazano na poniższym zrzucie ekranu:

![Kolorowanie rozszerzeń rozszerzeń programu Visual Studio Code Resource Manager](./media/use-vs-code-to-create-template/resource-manager-tools-extension-colorization.png)

Widok konspektu szablonu ułatwia poruszanie się po dużych szablonach.

### <a name="intellisense"></a>Intellisense

Rozszerzenie szablonu Menedżera zasobów zna możliwe uzupełnienia nazw funkcji, parametrów, zmiennych i odwołań. Sugestie IntelliSense pojawiają się podczas pisania. Jeśli nadal wpisujesz znaki, lista elementów członkowskich (zmiennych, metod itp.) zostanie przefiltrowana tak, aby uwzględniała tylko elementy członkowskie zawierające wpisane znaki. Naciśnięcie **klawisza Tab** lub **Enter** powoduje wstawienie zaznaczonego elementu członkowskiego.

- Wbudowane nazwy funkcji

    ![Narzędzia Programu Visual Studio Code Resource Manager rozszerzają funkcje intellisense](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-functions.png)

- Odwołania do parametrów

    ![Narzędzia Programu Visual Studio Code Resource Manager rozszerzają parametry intellisense](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-parameters.png)

- Odwołania do zmiennych

    ![Visual Studio Code Resource Manager rozszerzenia rozszerzenia intellisense zmiennych](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-variables.png)

- właściwości resourceGroup()

    ![Narzędzia Programu Visual Studio Code Resource Manager rozszerzają funkcje intellisense](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-resourcegroup.png)

Ponadto intellisense współpracuje również z właściwościami subscription() i właściwościami odwołań do zmiennych, które są obiektami.

### <a name="signature-help-for-function-parameters"></a>Pomoc dotycząca podpisu dla parametrów funkcji

Po najechaniu kursorem na nazwy funkcji rozszerzenie pokazuje pomoc podpisu dla parametrów funkcji.

![Visual Studio Code Resource Manager narzędzia rozszerzenia funkcji podpisu](./media/use-vs-code-to-create-template/resource-manager-tools-extension-signature-function.png)

### <a name="go-to-definition-for-variable-and-parameter-references"></a>Przejdź do definicji dla odwołań do zmiennych i parametrów

Można przejść do definicji za pomocą **klawiszy Ctrl+Click**lub menu ![kontekstowego, jak pokazano na zrzucie ekranu: Rozszerzenia narzędzi Programu Visual Studio Code Resource Manager przechodzą do definicji](./media/use-vs-code-to-create-template/resource-manager-tools-extension-context-menu.png)

Definicję można otworzyć z boku za pomocą **klawiszy Ctrl+Alt+Click**.

### <a name="peek-for-variable-and-parameter-definitions"></a>Wgląd do definicji zmiennych i parametrów

Aby otworzyć zaglądanego edytora, użyj menu kontekstowego, jak pokazano na poprzednim zrzucie ekranu.

Poniższy zrzut ekranu przedstawia edytor wglądu:

![Edytor rozszerzeń narzędzi programu Visual Studio Code Resource Manager](./media/use-vs-code-to-create-template/resource-manager-tools-extension-peek-editor.png)

### <a name="find-all-references-for-variables-and-parameters"></a>Znajdź wszystkie odwołania do zmiennych i parametrów

Aby znaleźć wszystkie odwołania, użyj menu kontekstowego, jak pokazano na poprzednim zrzucie ekranu.

Poniższy zrzut ekranu pokazuje, jak wyróżnione są odwołania:

![Rozszerzenia narzędzi Programu Visual Studio Code Resource Manager znajdują wszystkie odwołania](./media/use-vs-code-to-create-template/resource-manager-tools-extension-find-all-references.png)

### <a name="rename-all-references-for-variables-and-parameters"></a>Zmienianie nazwy wszystkich odwołań do zmiennych i parametrów

Aby zmienić nazwę wszystkich odwołań do zmiennych i parametrów, użyj menu kontekstowego, jak pokazano na poprzednim zrzucie ekranu.

### <a name="hover-for-parameter-description"></a>Kursoruj kursorem opisu parametru

![Rozszerzenia myszy na narzędzia Programu Visual Studio Code Resource Manager](./media/use-vs-code-to-create-template/resource-manager-tools-extension-hover-parameters.png)

### <a name="brace-matching"></a>Dopasowywanie nawiasów klamrowych

Pasujące nawiasy są podświetlane, gdy tylko kursor znajduje się w pobliżu jednego z nich. Po kliknięciu nawiasu klamrowego pasujący nawias klamrowy jest również wyróżniony, jak pokazano na poniższym zrzucie ekranu:

![Dopasowanie rozszerzeń do szykowania rozszerzeń programu Visual Studio Code Resource Manager](./media/use-vs-code-to-create-template/resource-manager-tools-extension-brace-matching.png)

### <a name="show-errors-and-warnings"></a>Pokaż błędy i ostrzeżenia

Błędy identyfikowane przez rozszerzenie obejmują:

- Niezdefiniowane odwołania do parametrów
- Niezdefiniowane odwołania do zmiennych
- Nierozpoznane nazwy funkcji
- użycie funkcji reference() w definicji zmiennej
- Nieprawidłowa liczba argumentów w funkcjach

Ostrzeżenia obejmują:

- Nieużywane parametry
- Nieużywane zmienne

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o tworzeniu szablonów usługi Azure Resource Manager, zobacz [Samouczek: Tworzenie i wdrażanie pierwszego szablonu usługi Azure Resource Manager](template-tutorial-create-first-template.md).
- Aby przejść przez przewodnik Szybki start przy użyciu programu Visual Studio Code, zobacz [Szybki start: Tworzenie szablonów usługi Azure Resource Manager przy użyciu kodu programu Visual Studio](quickstart-create-templates-use-visual-studio-code.md)
