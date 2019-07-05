---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 04/16/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 4f3d31fa25ea5781bc3af7297719b77723597e17
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444596"
---
## <a name="publish-the-project-to-azure"></a>Publikowanie projektu na platformie Azure

Program Visual Studio Code umożliwia publikowanie projektu usługi Functions bezpośrednio na platformie Azure. W ramach tego procesu tworzysz aplikację funkcji i powiązane zasoby w subskrypcji platformy Azure. Aplikacja funkcji zapewnia kontekst wykonywania dla Twoich funkcji. Projekt jest pakowany i wdrażany do nowej aplikacji funkcji w ramach subskrypcji platformy Azure.

Domyślnie program Visual Studio tworzy wszystkich zasobów platformy Azure, musisz utworzyć aplikację funkcji. Nazwy tych zasobów są oparte na nazwę aplikacji funkcji, którą wybierzesz. Jeśli musisz mieć pełną kontrolę nad utworzonych zasobów, możesz zamiast tego [opublikować za pomocą zaawansowanych opcji](../articles/azure-functions/functions-develop-vs-code.md#enabled-publishing-with-advanced-create-options).

W tej sekcji założono, że utworzysz nową aplikację funkcji na platformie Azure.

> [!IMPORTANT]
> Publikowanie do istniejącej aplikacji funkcji spowoduje zastąpienie zawartości tej aplikacji na platformie Azure.

1. W programie Visual Studio Code naciśnij klawisz F1, aby otworzyć paletę poleceń. W palecie poleceń, wyszukiwanie i wybieranie `Azure Functions: Deploy to function app...`.

1. Jeśli nie wylogowuj, monit o **logowanie do platformy Azure**. Możesz również **Utwórz bezpłatne konto platformy Azure**. Po pomyślnym zalogowaniu się za pomocą przeglądarki wróć do programu Visual Studio Code. 

1. Jeśli masz wiele subskrypcji, **Wybierz subskrypcję,** dla aplikacji funkcji kliknij **+ Utwórz nową aplikację funkcji na platformie Azure**.

1. Wpisz unikatową w skali globalnej nazwę identyfikującą aplikację funkcji, a następnie naciśnij klawisz Enter. Prawidłowe znaki dla nazwy aplikacji funkcji to `a-z`, `0-9` i `-`.

    Po naciśnięciu klawisza Enter następujących zasobów platformy Azure są tworzone w ramach subskrypcji:

    * **[Grupa zasobów](../articles/azure-resource-manager/resource-group-overview.md)** : Zawiera wszystkie utworzone zasoby platformy Azure. Nazwa opiera się na nazwę swojej aplikacji funkcji.
    * **[Konto magazynu](../articles/storage/common/storage-quickstart-create-account.md)** : Standardowe konto magazynu jest tworzone z unikatową nazwę, która opiera się na nazwę swojej aplikacji funkcji.
    * **[Plan hostingu](../articles/azure-functions/functions-scale.md)** : Plan zużycie zostanie utworzona w regionie zachodnie stany USA do hostowania aplikacji funkcję niewymagającą użycia serwera.
    * **Aplikacja funkcji**: Projekt jest wdrażana i jest uruchamiany w tej nowej aplikacji funkcji.

    Po utworzeniu aplikacji funkcji i zastosowaniu pakietu wdrożeniowego zostanie wyświetlone powiadomienie. Wybierz **wyświetlanie danych wyjściowych** w tym zgłoszeniu do wyświetlania, tworzenia i wynikami wdrożenia, łącznie z zasobów platformy Azure, które zostały utworzone.

1. Ponownie **platformy Azure: Funkcje** obszaru, rozwiń węzeł nowej aplikacji funkcji w ramach Twojej subskrypcji. Rozwiń **funkcje**, kliknij prawym przyciskiem myszy **HttpTrigger**, a następnie wybierz **skopiuj adres URL funkcji**.

    ![Skopiuj adres URL funkcji dla nowego wyzwalacza HTTP](./media/functions-publish-project-vscode/function-copy-endpoint-url.png)
