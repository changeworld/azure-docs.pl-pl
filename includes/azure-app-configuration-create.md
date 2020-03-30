---
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: include
ms.date: 1/31/2020
ms.openlocfilehash: 089a199da08503e13dbd42518970c3b7ed8a984d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77619286"
---
1. Aby utworzyć nowy sklep konfiguracji aplikacji, zaloguj się do [witryny Azure portal](https://portal.azure.com). W lewym górnym rogu strony głównej wybierz pozycję **Utwórz zasób**. W polu **Wyszukaj w portalu Marketplace** wprowadź **pozycję Konfiguracja aplikacji** i wybierz pozycję Wprowadź.

    ![Wyszukiwanie usługi App Configuration](media/azure-app-configuration-create/azure-portal-search.png)

1. Wybierz **pozycję Konfiguracja aplikacji** z wyników wyszukiwania, a następnie wybierz pozycję **Utwórz**.

    ![Wybierz pozycję Utwórz](media/azure-app-configuration-create/azure-portal-app-configuration-create.png)

1. W okienku**Tworzenie** **konfiguracji** > aplikacji wprowadź następujące ustawienia:

    | Ustawienie | Sugerowana wartość | Opis |
    |---|---|---|
    | **Nazwa zasobu** | Nazwa unikatowa w skali globalnej | Wprowadź unikatową nazwę zasobu do użycia w zasobie magazynu konfiguracji aplikacji. Nazwa musi być ciągiem od 5 do 50 znaków i `-` zawierać tylko cyfry, litery i znak. Nazwa nie może rozpoczynać się `-` ani kończyć znakiem.  |
    | **Subskrypcja** | Twoja subskrypcja | Wybierz subskrypcję platformy Azure, której chcesz użyć do testowania usługi App Configuration. Jeśli Twoje konto ma tylko jedną subskrypcję, jest ono automatycznie zaznaczone, a lista **Subskrypcja** nie jest wyświetlana. |
    | **Grupa zasobów** | *AppConfigTestResources* | Wybierz lub utwórz grupę zasobów dla zasobu sklepu konfiguracji aplikacji. Ta grupa jest przydatna do organizowania wielu zasobów, które można usunąć w tym samym czasie, usuwając grupę zasobów. Aby uzyskać więcej informacji, zobacz [Zarządzanie zasobami platformy Azure za pomocą grup zasobów](/azure/azure-resource-manager/resource-group-overview). |
    | **Lokalizacja** | *Środkowe stany USA* | Użyj opcji **Lokalizacja**, aby określić lokalizację geograficzną, w której będzie hostowany magazyn konfiguracji aplikacji. Aby uzyskać najlepszą wydajność, należy utworzyć zasób w tym samym regionie, co inne składniki aplikacji. |
    | **Warstwa cenowa** | *Bezpłatna* | Wybierz żądaną warstwę cenową. Aby uzyskać więcej informacji, zobacz [stronę z cenami konfiguracji aplikacji](https://azure.microsoft.com/pricing/details/app-configuration/).

    ![Tworzenie zasobu sklepu konfiguracji aplikacji](media/azure-app-configuration-create/azure-portal-app-configuration-create-settings.png)

1. Wybierz **pozycję Utwórz**. Wdrożenie może potrwać kilka minut.

1. Po zakończeniu wdrażania wybierz pozycję **Ustawienia** > **klawiszy dostępu**. Zanotuj podstawowy ciąg połączenia klucza tylko do odczytu. Ten ciąg połączenia użyjesz później, aby skonfigurować aplikację do komunikowania się z utworzonym sklepem konfiguracji aplikacji.
