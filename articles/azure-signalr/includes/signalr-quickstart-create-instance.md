---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: signalr
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 09/14/2018
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 97b8ef5e260a853ecdffb3c502f8a5051dd0e143
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47006262"
---
## <a name="create-an-azure-signalr-service-instance"></a>Tworzenie wystąpienia usługi Azure SignalR Service

Twoja aplikacja nawiąże połączenie z wystąpieniem usługi SignalR na platformie Azure.

1. Wybierz przycisk Nowy znajdujący się w lewym górnym rogu witryny Azure Portal. Na ekranie Nowy wpisz nazwę *SignalR Service* w polu wyszukiwania, a następnie naciśnij klawisz Enter.

    ![Wyszukiwanie usługi SignalR Service](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-new.png)

1. Wybierz pozycję **SignalR Service** w wynikach wyszukiwania, a następnie wybierz pozycję **Utwórz**.

1. Wprowadź następujące ustawienia.

    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nazwa zasobu** | Nazwa unikatowa w skali globalnej | Nazwa identyfikująca nowe wystąpienie usługi SignalR Service. Prawidłowe znaki to `a-z`, `0-9` i `-`.  | 
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której jest tworzone to nowe wystąpienie usługi SignalR Service. | 
    | **[Grupa zasobów](../../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Nazwa nowej grupy zasobów, w której ma zostać utworzone wystąpienie usługi SignalR Service. | 
    | **Lokalizacja** | Zachodnie stany USA | Wybierz [region](https://azure.microsoft.com/regions/) blisko siebie. |
    | **Warstwa cenowa** | Bezpłatna | Wypróbuj bezpłatnie usługę Azure SignalR Service. |
    | **Liczba jednostek** |  Nie dotyczy | Liczba jednostek określa liczbę połączeń, które może akceptować wystąpienie usługi SignalR Service. Można to skonfigurować tylko w warstwie Standardowa. |

    ![Tworzenie usługi SignalR Service](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-create.png)

1. Wybierz pozycję **Utwórz**, aby rozpocząć wdrażanie wystąpienia usługi SignalR Service.
