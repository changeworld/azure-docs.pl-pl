---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/31/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: ebcbe7e8af7e01a9b7e75282be4e4f4ceb80b806
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279451"
---
## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Podstawowe narzędzia usługi Azure Functions umożliwiają uruchamianie projektu usługi Azure Functions na lokalnym komputerze deweloperskim.

1. Aby przetestować funkcję, ustaw punkt przerwania w kodzie funkcji, a następnie naciśnij klawisz F5, aby uruchomić projekt aplikacji funkcji. Dane wyjściowe z pakietu Core Tools są wyświetlane na panelu **terminalu**.

1. W panelu **terminalu** skopiuj punkt końcowy adresu URL funkcji wyzwalanej przez protokół HTTP. 

    ![Lokalne dane wyjściowe platformy Azure](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Wklej adres URL żądania HTTP w pasku adresu przeglądarki. Dołącz ciąg zapytania `?name=<yourname>` do tego adresu URL i wykonaj żądanie. Wykonywanie zostanie wstrzymane po osiągnięciu punktu przerwania.

1. Jeśli będziesz kontynuować wykonywanie, poniżej przedstawiono odpowiedź w przeglądarce na żądanie GET:

    ![Odpowiedź hosta localhost funkcji wyświetlona w przeglądarce](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Aby zatrzymać debugowanie, naciśnij klawisze Shift+F5.
