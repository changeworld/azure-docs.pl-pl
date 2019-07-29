---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 0493de7374cffcc40f0434d84facf50b6a708c7b
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592858"
---
1. Aby uruchomić funkcję, naciśnij klawisz **F5**. Może być konieczne włączenie wyjątku zapory, aby narzędzia mogły obsługiwać żądania HTTP. Poziomy autoryzacji nigdy nie są wymuszane w przypadku uruchamiania lokalnego.

2. Skopiuj adres URL funkcji z danych wyjściowych środowiska uruchomieniowego usługi Azure Functions.

    ![Lokalne środowisko uruchomieniowe platformy Azure](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. Wklej adres URL żądania HTTP w pasku adresu przeglądarki. Dołącz ciąg zapytania `?name=<YOUR_NAME>` do tego adresu URL i wykonaj żądanie. Na poniższym obrazie przedstawiono wyświetloną w przeglądarce odpowiedź na lokalne żądanie GET zwróconą przez funkcję: 

    ![Odpowiedź hosta localhost funkcji wyświetlona w przeglądarce](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. Aby zatrzymać debugowanie, naciśnij klawisze **Shift+F5**.