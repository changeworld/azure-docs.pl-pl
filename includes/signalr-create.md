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
ms.openlocfilehash: 39bff26baea622e6c0ed524ca68c3c8bae4e770d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76021090"
---
1. Aby utworzyć zasób usługi Azure SignalR, należy najpierw zalogować się do [witryny Azure portal](https://portal.azure.com). W lewym górnym rogu strony wybierz pozycję **+ Utwórz zasób**. W polu **tekstowym Wyszukaj w Marketplace** wprowadź pozycję **SignalR Service**.

2. Wybierz **pozycję SignalR Service** w wynikach i wybierz pozycję **Utwórz**.

3. Na nowej stronie ustawień **SignalR** dodaj następujące ustawienia nowego zasobu SignalR:

    | Nazwa | Zalecana wartość | Opis |
    | ---- | ----------------- | ----------- |
    | Nazwa zasobu | *testsignalr* | Podaj unikatową nazwę zasobu do użycia dla zasobu usługi SignalR. Nazwa musi być ciągiem od 1 do 63 znaków i zawierać tylko`-`cyfry, litery i znak łącznika ( ) . Nazwa nie może rozpoczynać się ani kończyć znakiem łącznika, a kolejne znaki łącznika są nieprawidłowe.|
    | Subskrypcja | Wybierz subskrypcję |  Wybierz subskrypcję platformy Azure, której chcesz użyć do testowania usługi SignalR. Jeśli Twoje konto ma tylko jedną subskrypcję, jest ona automatycznie zaznaczona, a wyeksliowane listy rozwijanej **Subskrypcja** nie są wyświetlane.|
    | Grupa zasobów | Tworzenie grupy zasobów o nazwie *SignalRTestResources*| Wybierz lub utwórz grupę zasobów dla zasobu usługi SignalR. Ta grupa jest przydatna do organizowania wielu zasobów, które można usunąć w tym samym czasie, usuwając grupę zasobów. Więcej informacji można znaleźć w temacie [Using resource groups to manage your Azure resources](../articles/azure-resource-manager/management/overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure). |
    | Lokalizacja | *Wschodnie stany USA* | Użyj opcji **Lokalizacja**, aby określić lokalizację geograficzną, w której będzie hostowany zasób usługi SignalR. Aby uzyskać najlepszą wydajność, zalecamy utworzenie zasobu w tym samym regionie, co inne składniki aplikacji. |
    | Warstwa cenowa | *Bezpłatna* | Obecnie dostępne są opcje **bezpłatne** i **standardowe.** |
    | Przypnij do pulpitu nawigacyjnego | ✔ | Zaznacz to pole, aby przypiąć zasób do pulpitu nawigacyjnego, aby łatwiej go było znaleźć. |

4. Wybierz **pozycję Utwórz**. Wdrożenie może potrwać kilka minut.

5. Po zakończeniu wdrażania wybierz pozycję **Klucze** w obszarze **USTAWIENIA**. Skopiuj parametry połączenia dla klucza podstawowego. Ten ciąg będzie używany później, aby skonfigurować aplikację do korzystania z zasobu usługi Azure SignalR.

    Parametry połączenia mają następującą postać:
    
        Endpoint=<service_endpoint>;AccessKey=<access_key>;
