---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-app-configuration
author: yegu
ms.service: azure-app-configuration
ms.topic: include
ms.date: 01/22/2019
ms.author: yegu
ms.custom: include file
ms.openlocfilehash: c98a17be394887ef4e008b079467c85d4ded7e09
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393298"
---
1. Aby utworzyć nowy magazyn konfiguracji aplikacji, zaloguj się do [witryny Azure portal](https://portal.azure.com). W lewym górnym rogu okienka wybierz **+ Utwórz zasób**. W **Przeszukaj witrynę Marketplace** wprowadź **konfiguracji aplikacji** i naciśnij klawisz Enter.

    ![Wyszukiwanie usługi App Configuration](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-new.png)

1. Wybierz **konfiguracji aplikacji** z wyników wyszukiwania, a następnie wybierz **Utwórz**.

1. Na **konfiguracji aplikacji** > **Utwórz** okienku, wprowadź następujące ustawienia:

    | Ustawienie | Sugerowana wartość | Opis |
    |---|---|---|
    | **Nazwa zasobu** | Nazwa unikatowa w skali globalnej | Wprowadź nazwę unikatowy zasób do użycia dla zasobu magazynu konfiguracji aplikacji. Nazwa musi być ciągiem od 1 do 63 znaków i może zawierać tylko cyfry, litery i znak `-`. Nazwa nie może zaczynać ani kończyć `-` znak i następujących po sobie `-` znaki są nieprawidłowe.  |
    | **Subskrypcja** | Twoja subskrypcja | Wybierz subskrypcję platformy Azure, której chcesz użyć do testowania usługi App Configuration. Jeśli Twoje konto ma tylko jedną subskrypcję, automatycznie jest zaznaczone i **subskrypcji** lista nie jest wyświetlana. |
    | **Grupa zasobów** | *AppConfigTestResources* | Wybierz lub Utwórz grupę zasobów dla zasobu magazynu konfiguracji aplikacji. Ta grupa jest przydatne do organizowania wielu zasobów, które można usunąć w tym samym czasie przez usunięcie grupy zasobów. Więcej informacji można znaleźć w temacie [Używanie grup zasobów do zarządzania zasobami platformy Azure](/azure/azure-resource-manager/resource-group-overview). |
    | **Lokalizacja** | *Środkowe stany USA* | Użyj opcji **Lokalizacja**, aby określić lokalizację geograficzną, w której będzie hostowany magazyn konfiguracji aplikacji. Aby uzyskać najlepszą wydajność Utwórz zasób w tym samym regionie, co inne składniki aplikacji. |

    ![Utwórz zasób magazynu konfiguracji aplikacji](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-create.png)

1. Wybierz pozycję **Utwórz**. Wdrożenie może potrwać kilka minut.

1. Po zakończeniu wdrożenia wybierz **ustawienia** > **klucze dostępu**. Zanotuj albo tylko do odczytu lub podstawowy odczytu i zapisu parametry połączenia klucza podstawowego. Te parametry połączenia będą używane do konfigurowania aplikacji do komunikowania się z magazynu konfiguracji aplikacji utworzone później.
