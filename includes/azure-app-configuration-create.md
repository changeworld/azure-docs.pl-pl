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
ms.openlocfilehash: 9b86f2e05e2cb42470061bd6398b4200607f2418
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60202249"
---
1. Aby utworzyć nowy magazyn konfiguracji aplikacji, zaloguj się do [witryny Azure portal](https://aka.ms/azconfig/portal). W lewym górnym rogu strony wybierz **+ Utwórz zasób**. W **Przeszukaj witrynę Marketplace** wprowadź **konfiguracji aplikacji** i naciśnij klawisz Enter.

    ![Wyszukiwanie usługi App Configuration](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-new.png)

2. Wybierz **konfiguracji aplikacji** z wyników wyszukiwania, a następnie wybierz **Utwórz**.

3. Na **konfiguracji aplikacji** > **Utwórz** strony, wprowadź następujące ustawienia.

    | Ustawienie | Sugerowana wartość | Opis |
    |---|---|---|
    | **Nazwa zasobu** | Nazwa unikatowa w skali globalnej | Wprowadź unikatową nazwę zasobu magazynu konfiguracji aplikacji. Nazwa musi być ciągiem od 1 do 63 znaków i może zawierać tylko cyfry, litery i znak `-`. Nazwa nie może zaczynać ani kończyć `-` znak i następujących po sobie `-` znaki są nieprawidłowe.  |
    | **Subskrypcja** | Twoja subskrypcja | Wybierz subskrypcję platformy Azure, której chcesz użyć do testowania usługi App Configuration. Jeśli Twoje konto ma tylko jedną subskrypcję, automatycznie jest zaznaczone i **subskrypcji** listy rozwijanej nie są wyświetlane. |
    | **Grupa zasobów** | *AppConfigTestResources* | Wybierz lub utwórz grupę zasobów dla zasobu magazynu konfiguracji aplikacji. Ta grupa jest przydatne do organizowania wielu zasobów, które można usunąć w tym samym czasie przez usunięcie grupy zasobów. Więcej informacji można znaleźć w temacie [Używanie grup zasobów do zarządzania zasobami platformy Azure](/azure/azure-resource-manager/resource-group-overview). |
    | **Lokalizacja** | *Środkowe stany USA* | Użyj opcji **Lokalizacja**, aby określić lokalizację geograficzną, w której będzie hostowany magazyn konfiguracji aplikacji. Aby uzyskać najlepszą wydajność Utwórz zasób w tym samym regionie, co inne składniki aplikacji. |

    ![Tworzenie zasobu magazynu konfiguracji aplikacji](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-create.png)

4. Wybierz pozycję **Utwórz**. Wdrożenie może potrwać kilka minut.

5. Po zakończeniu wdrożenia wybierz **ustawienia** > **klucze dostępu**. Zanotuj albo tylko do odczytu lub podstawowy odczytu i zapisu parametry połączenia klucza podstawowego. Użyj tych parametrów połączenia jest później skonfigurować aplikację do komunikowania się z utworzonego magazynu konfiguracji aplikacji.
