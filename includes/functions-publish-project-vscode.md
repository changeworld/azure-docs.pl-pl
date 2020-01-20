---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/16/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 30a6d8556a251ba76dff77e004fb864f3eaf04cf
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279548"
---
## <a name="publish-the-project-to-azure"></a>Publikowanie projektu na platformie Azure

Program Visual Studio Code umożliwia publikowanie projektu usługi Functions bezpośrednio na platformie Azure. W ramach tego procesu tworzysz aplikację funkcji i powiązane zasoby w subskrypcji platformy Azure. Aplikacja funkcji zapewnia kontekst wykonywania dla Twoich funkcji. Projekt jest pakowany i wdrażany do nowej aplikacji funkcji w ramach subskrypcji platformy Azure.

Domyślnie Visual Studio Code tworzy wszystkie zasoby platformy Azure wymagane do utworzenia aplikacji funkcji. Nazwy tych zasobów są zależne od wybranej nazwy aplikacji funkcji. Jeśli musisz mieć pełną kontrolę nad utworzonymi zasobami, możesz zamiast tego [opublikować przy użyciu opcji zaawansowanych](../articles/azure-functions/functions-develop-vs-code.md#enable-publishing-with-advanced-create-options).

W tej sekcji założono, że tworzysz nową aplikację funkcji na platformie Azure.

> [!IMPORTANT]
> Publikowanie do istniejącej aplikacji funkcji spowoduje zastąpienie zawartości tej aplikacji na platformie Azure.

1. W Visual Studio Code naciśnij klawisz F1, aby otworzyć paletę poleceń. W palecie poleceń Wyszukaj i wybierz `Azure Functions: Deploy to function app...`.

1. Jeśli nie jest zalogowany, zostanie wyświetlony monit o **zalogowanie się do platformy Azure**. Możesz również **utworzyć bezpłatne konto platformy Azure**. Po pomyślnym zalogowaniu się w przeglądarce Wróć do Visual Studio Code. 

1. Jeśli masz wiele subskrypcji, **Wybierz subskrypcję** dla aplikacji funkcji, a następnie wybierz pozycję **+ Utwórz nowe aplikacja funkcji na platformie Azure**.

1. Wpisz unikatową w skali globalnej nazwę identyfikującą aplikację funkcji, a następnie naciśnij klawisz Enter. Prawidłowe znaki dla nazwy aplikacji funkcji to `a-z`, `0-9` i `-`.

    Naciśnięcie klawisza ENTER powoduje utworzenie następujących zasobów platformy Azure w ramach subskrypcji:

    * **[Grupa zasobów](../articles/azure-resource-manager/management/overview.md)** : zawiera wszystkie utworzone zasoby platformy Azure. Nazwa jest oparta na nazwie aplikacji funkcji.
    * **[Konto magazynu](../articles/storage/common/storage-account-create.md)** : konto magazynu w warstwie Standardowa jest tworzone z unikatową nazwą, która jest oparta na nazwie aplikacji funkcji.
    * **[Plan hostingu](../articles/azure-functions/functions-scale.md)** : plan zużycia jest tworzony w regionie zachodnie stany USA w celu hostowania aplikacji funkcji bezserwerowej.
    * **Aplikacja funkcji**: projekt jest wdrażany do i uruchamiany w tej nowej aplikacji funkcji.

    Po utworzeniu aplikacji funkcji i zastosowaniu pakietu wdrożeniowego zostanie wyświetlone powiadomienie. Wybierz pozycję **Wyświetl dane wyjściowe** w tym powiadomieniu, aby wyświetlić wyniki tworzenia i wdrażania, w tym utworzone zasoby platformy Azure.

1. Wróć do obszaru **Azure: Functions** , a następnie rozwiń nową aplikację funkcji w ramach subskrypcji. Rozwiń węzeł **funkcje**, kliknij prawym przyciskiem myszy pozycję **HttpTrigger**, a następnie wybierz polecenie **Kopiuj adres URL funkcji**.

    ![Skopiuj adres URL funkcji dla nowego wyzwalacza HTTP](./media/functions-publish-project-vscode/function-copy-endpoint-url.png)
