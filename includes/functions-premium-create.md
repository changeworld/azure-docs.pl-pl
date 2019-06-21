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
ms.openlocfilehash: 0f3303e7bc87ca0bd29f367405372568ed6da7a7
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183386"
---
1. Przejdź do witryny [Azure Portal](https://portal.azure.com).

1. Wybierz **+ Utwórz zasób** po lewej stronie, następnie wybierz **aplikacji funkcji**.

1. Aby uzyskać **plan hostingu**, wybierz **planu usługi App Service**, a następnie wybierz **plan usługi App Service/lokalizacja**.

    ![Tworzenie aplikacji funkcji](./media/functions-premium-create/create-function-app-resource.png)

1. Wybierz **Utwórz nową**, typ **planu usługi App Service** nazwę, wybierz polecenie **lokalizacji** w [region](https://azure.microsoft.com/regions/) okolicy lub w pobliżu innych usług funkcji dostęp, a następnie wybierz **warstwa cenowa**.

    ![Tworzenie planu usługi App Service](./media/functions-premium-create/new-app-service-plan.png)

1. Wybierz **EP1** (elastyczne Premium) planu, a następnie wybierz **Zastosuj**.

    ![Wybierz plan w warstwie premium](./media/functions-premium-create/hosting-plan.png) 

1. Wybierz **OK** utworzyć plan, a następnie, korzystając z pozostałych ustawień aplikacji funkcji jako określone w tabeli znajdującej się poniżej obrazu. 

    ![Plan usługi gotową aplikację](./media/functions-premium-create/create-function-app.png)  

    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nazwa aplikacji** | Nazwa unikatowa w skali globalnej | Nazwa identyfikująca nową aplikację funkcji. Prawidłowe znaki to `a-z`, `0-9` i `-`.  | 
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której jest tworzona ta nowa aplikacja funkcji. |
    | **[Grupa zasobów](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Nazwa nowej grupy zasobów, w której ma zostać utworzona aplikacja funkcji. Można także użyć sugerowane wartości. |
    | **OS** | Windows | Z planem Premium nie jest obecnie obsługiwane systemu Linux. |
    | **Stos środowiska uruchomieniowego** | Preferowany język | Wybierz środowisko uruchomieniowe, które obsługuje ulubiony język programowania funkcji. Wybierz **.NET** dla funkcji w językach C# i F#. Tylko języki obsługiwane w wybranego **OS** są wyświetlane. |
    | **[Storage](../articles/storage/common/storage-quickstart-create-account.md)** |  Nazwa unikatowa w skali globalnej |  Utwórz konto magazynu używane przez aplikację funkcji. Nazwy kont usługi Storage muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery. Możesz również użyć istniejącego konta, które musi spełniać [wymagania dotyczące konta magazynu](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Domyślne | Tworzy zasób usługi Application Insights w tej samej *nazwy aplikacji* w najbliższym obsługiwanym regionie. Rozwijając tego ustawienia, można zmienić **Nowa nazwa zasobu** lub wybrać inną **lokalizacji** w [lokalizacja geograficzna platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/) potrzebne do przechowywania danych. |

1. Po zweryfikowaniu ustawień wybierz **Utwórz**.

1. Wybierz ikonę Powiadomienie w prawym górnym rogu portalu i poszukaj komunikatu **Wdrażanie zakończone pomyślnie**.

    ![Definiowanie nowych ustawień aplikacji funkcji](./media/functions-premium-create/function-app-create-notification.png)

1. Wybierz pozycję **Przejdź do zasobu**, aby wyświetlić nową aplikację funkcji. Możesz również wybrać **Przypnij do pulpitu nawigacyjnego**. Przypinanie ułatwia powrócić do tego zasobu aplikacji funkcji z poziomu pulpitu nawigacyjnego.