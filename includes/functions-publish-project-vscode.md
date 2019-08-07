---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 04/16/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: abb8b6bed6766ff0ea85eab1434014a057af4ca3
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68843286"
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

    * **[Grupa zasobów](../articles/azure-resource-manager/resource-group-overview.md)** : Zawiera wszystkie utworzone zasoby platformy Azure. Nazwa jest oparta na nazwie aplikacji funkcji.
    * **[Konto magazynu](../articles/storage/common/storage-quickstart-create-account.md)** : Konto magazynu w warstwie Standardowa jest tworzone z unikatową nazwą, która jest oparta na nazwie aplikacji funkcji.
    * **[Plan hostingu](../articles/azure-functions/functions-scale.md)** : Plan zużycia jest tworzony w regionie zachodnie stany USA w celu hostowania aplikacji funkcji bezserwerowej.
    * **Aplikacja funkcji**: Projekt zostanie wdrożony do i uruchomiony w tej nowej aplikacji funkcji.

    Po utworzeniu aplikacji funkcji i zastosowaniu pakietu wdrożeniowego zostanie wyświetlone powiadomienie. Wybierz pozycję **Wyświetl dane wyjściowe** w tym powiadomieniu, aby wyświetlić wyniki tworzenia i wdrażania, w tym utworzone zasoby platformy Azure.

1. Z powrotem na **platformie Azure: Obszar** funkcje, rozwiń nową aplikację funkcji w ramach subskrypcji. Rozwiń węzeł **funkcje**, kliknij prawym przyciskiem myszy pozycję **HttpTrigger**, a następnie wybierz polecenie **Kopiuj adres URL funkcji**.

    ![Skopiuj adres URL funkcji dla nowego wyzwalacza HTTP](./media/functions-publish-project-vscode/function-copy-endpoint-url.png)
