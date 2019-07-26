---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: jeffhollan
ms.service: azure-functions
ms.topic: include
ms.date: 04/01/2019
ms.author: jehollan, glenga
ms.custom: include file
ms.openlocfilehash: 9bac92bc1cc94b88dc694b468b795049db4ac9df
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443989"
---
1. Przejdź do witryny [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **+ Utwórz zasób** po lewej stronie, a następnie wybierz pozycję **aplikacja funkcji**.

1. W obszarze **Plan hostingu**wybierz opcję **Plan App Service**, a następnie wybierz pozycję **App Service plan/lokalizacja**.

    ![Tworzenie aplikacji funkcji](./media/functions-premium-create/create-function-app-resource.png)

1. Wybierz **pozycję Utwórz nowy**, wpisz **nazwę planu App Service** , wybierz **lokalizację** w [regionie](https://azure.microsoft.com/regions/) blisko siebie lub w niemal innych usługach, do których ma dostęp funkcja, a następnie wybierz pozycję **warstwa cenowa**.

    ![Tworzenie planu usługi App Service](./media/functions-premium-create/new-app-service-plan.png)

1. Wybierz plan **EP1** (Elastic Premium), a następnie wybierz pozycję **Zastosuj**.

    ![Wybierz plan Premium](./media/functions-premium-create/hosting-plan.png) 

1. Wybierz **przycisk OK** , aby utworzyć plan, a następnie użyj pozostałych ustawień aplikacji funkcji, jak określono w tabeli poniżej obrazu. 

    ![Zakończono plan usługi App Service](./media/functions-premium-create/create-function-app.png)  

    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nazwa aplikacji** | Nazwa unikatowa w skali globalnej | Nazwa identyfikująca nową aplikację funkcji. Prawidłowe znaki to `a-z`, `0-9` i `-`.  | 
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której jest tworzona ta nowa aplikacja funkcji. |
    | **[Grupa zasobów](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Nazwa nowej grupy zasobów, w której ma zostać utworzona aplikacja funkcji. Możesz również użyć sugerowanej wartości. |
    | **OS** | Preferowany system operacyjny | W planie Premium są obsługiwane systemy Linux i Windows. |
    | **Stos środowiska uruchomieniowego** | Preferowany język | Wybierz środowisko uruchomieniowe, które obsługuje ulubiony język programowania funkcji. Wybierz **.NET** dla funkcji w językach C# i F#. Wyświetlane są tylko języki obsługiwane w wybranym **systemie operacyjnym** . |
    | **[Storage](../articles/storage/common/storage-quickstart-create-account.md)** |  Nazwa unikatowa w skali globalnej |  Utwórz konto magazynu używane przez aplikację funkcji. Nazwy kont usługi Storage muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery. Możesz również użyć istniejącego konta, które musi spełniać [wymagania dotyczące konta magazynu](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Domyślny | Tworzy zasób Application Insights o tej samej *nazwie aplikacji* w najbliższym obsługiwanym regionie. Rozszerzając to ustawienie, można zmienić **nazwę nowego zasobu** lub wybrać inną **lokalizację w lokalizacji** geograficznej [platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/) , w której mają być przechowywane dane. |

1. Po sprawdzeniu poprawności ustawień wybierz pozycję **Utwórz**.

1. Wybierz ikonę Powiadomienie w prawym górnym rogu portalu i poszukaj komunikatu **Wdrażanie zakończone pomyślnie**.

    ![Definiowanie nowych ustawień aplikacji funkcji](./media/functions-premium-create/function-app-create-notification.png)

1. Wybierz pozycję **Przejdź do zasobu**, aby wyświetlić nową aplikację funkcji. Możesz również wybrać pozycję **Przypnij do pulpitu nawigacyjnego**. Przypinanie ułatwia powrót do tego zasobu aplikacji funkcji z pulpitu nawigacyjnego.