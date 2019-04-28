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
ms.openlocfilehash: 57407606214d8d3a305476cfbfdabca9eee937e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60690318"
---
1. Aby utworzyć nowy zasób usługi Azure SignalR Service, najpierw zaloguj się do [witryny Azure Portal](https://portal.azure.com). W lewej górnej części strony kliknij polecenie **+ Utwórz zasób**. W polu tekstowym **Przeszukaj witrynę Marketplace** wpisz ciąg **SignalR Service** i naciśnij klawisz **Enter**.

2. Kliknij pozycję **SignalR Service** w wynikach, a następnie kliknij polecenie **Utwórz**.

3. Na nowej stronie ustawień usługi **SignalR** dodaj następujące ustawienia dla nowego zasobu usługi SignalR:

    | Nazwa | Zalecana wartość | Opis |
    | ---- | ----------------- | ----------- |
    | Nazwa zasobu | *testsignalr* | Podaj unikatową nazwę zasobu do użycia dla zasobu usługi SignalR. Nazwa musi być ciągiem od 1 do 63 znaków i może zawierać tylko cyfry, litery i znak `-`. Na początku ani na końcu nazwy nie może występować znak `-`, a następujące po sobie znaki `-` nie są prawidłowe.|
    | Subskrypcja | Wybierz subskrypcję |  Wybierz subskrypcję platformy Azure, której chcesz użyć do testowania usługi SignalR. Jeśli konto ma tylko jedną subskrypcję, zostanie ona automatycznie wybrana i lista rozwijana **Subskrypcja** nie będzie wyświetlana.|
    | Grupa zasobów | Utwórz nową grupę zasobów o nazwie *SignalRTestResources*| Wybierz lub utwórz grupę zasobów dla zasobu usługi SignalR. Ta grupa jest przydatna do organizowania wielu zasobów, umożliwiając ich jednoczesne usunięcie przez usunięcie grupy zasobów. Więcej informacji można znaleźć w temacie [Używanie grup zasobów do zarządzania zasobami platformy Azure](../articles/azure-resource-manager/resource-group-overview.md). |
    | Lokalizacja | *Wschodnie stany USA* | Użyj opcji **Lokalizacja**, aby określić lokalizację geograficzną, w której będzie hostowany zasób usługi SignalR. Aby uzyskać najlepszą wydajność, zalecamy utworzenie zasobu w tym samym regionie, co inne składniki aplikacji. |
    | Warstwa cenowa | *Bezpłatna* | Aktualnie są dostępne opcje **Bezpłatna** i **Standardowa**. |
    | Przypnij do pulpitu nawigacyjnego | ✔ | Zaznacz to pole, aby przypiąć zasób do pulpitu nawigacyjnego, dzięki czemu można łatwiej go znaleźć. |

4. Kliknij przycisk **Utwórz**. Wdrożenie może potrwać kilka minut.

5. Po zakończeniu wdrożenia kliknij pozycję **Klucze** w polu **USTAWIENIA**. Skopiuj parametry połączenia klucza podstawowego. Później użyjesz ich do skonfigurowania aplikacji pod kątem używania zasobu usługi Azure SignalR Service.

    Parametry połączenia mają następującą postać:
    
        Endpoint=<service_endpoint>;AccessKey=<access_key>;
