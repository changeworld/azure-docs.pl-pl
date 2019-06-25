---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: signalr
author: wesmc7777
ms.service: signalr
ms.topic: include
ms.date: 04/17/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 28d003e123069c47d87d81570b4a5b69b3b9d64b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183601"
---
1. Aby utworzyć zasób usługi Azure SignalR Service, najpierw zaloguj się do [witryny Azure portal](https://portal.azure.com). W lewej górnej części strony wybierz **+ Utwórz zasób**. W **Przeszukaj witrynę Marketplace** tekstu wprowadź **SignalR Service**.

2. Wybierz **SignalR Service** w wynikach i wybierz **Utwórz**.

3. W nowym **SignalR** strona Ustawienia, Dodaj następujące ustawienia dla nowego zasobu SignalR:

    | Name (Nazwa) | Zalecana wartość | Opis |
    | ---- | ----------------- | ----------- |
    | Nazwa zasobu | *testsignalr* | Podaj unikatową nazwę zasobu do użycia dla zasobu usługi SignalR. Nazwa musi być ciągiem od 1 do 63 znaków i zawierać tylko cyfry, litery i łącznik (`-`) znaków. Nazwa nie może zaczynać ani kończyć znakiem łącznika i następujących po sobie łączniki są nieprawidłowe.|
    | Subskrypcja | Wybierz subskrypcję |  Wybierz subskrypcję platformy Azure, której chcesz użyć do testowania usługi SignalR. Jeśli Twoje konto ma tylko jedną subskrypcję, automatycznie jest zaznaczone i **subskrypcji** listy rozwijanej nie są wyświetlane.|
    | Grupa zasobów | Utwórz grupę zasobów o nazwie *SignalRTestResources*| Wybierz lub utwórz grupę zasobów dla zasobu usługi SignalR. Ta grupa jest przydatne do organizowania wielu zasobów, które można usunąć w tym samym czasie przez usunięcie grupy zasobów. Więcej informacji można znaleźć w temacie [Using resource groups to manage your Azure resources](../articles/azure-resource-manager/resource-group-overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure). |
    | Lokalizacja | *Wschodnie stany USA* | Użyj opcji **Lokalizacja**, aby określić lokalizację geograficzną, w której będzie hostowany zasób usługi SignalR. Aby uzyskać najlepszą wydajność, zalecamy utworzenie zasobu w tym samym regionie, co inne składniki aplikacji. |
    | Warstwa cenowa | *Bezpłatna* | Obecnie **bezpłatna** i **standardowa** dostępnych opcji. |
    | Przypnij do pulpitu nawigacyjnego | ✔ | Zaznacz to pole do zasobu przypięte do pulpitu nawigacyjnego, aby łatwiej znaleźć. |

4. Wybierz pozycję **Utwórz**. Wdrożenie może potrwać kilka minut.

5. Po zakończeniu wdrożenia wybierz **klucze** w obszarze **ustawienia**. Skopiuj parametry połączenia klucza podstawowego. Ten ciąg będą używane później, aby skonfigurować aplikację do używania zasobów usługi Azure SignalR Service.

    Parametry połączenia mają następującą postać:
    
        Endpoint=<service_endpoint>;AccessKey=<access_key>;
