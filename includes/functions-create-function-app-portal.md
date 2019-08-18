---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/05/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 460199c1958e3bb22148624b9f013eadbb707bd2
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69564405"
---
1. Wybierz przycisk **Utwórz zasób** znajdujący się w lewym górnym rogu Azure Portal, a następnie wybierz pozycję obliczeniowe > **aplikacja funkcji**.

    ![Tworzenie aplikacji funkcji w witrynie Azure Portal](./media/functions-create-function-app-portal/function-app-create-flow.png)

2. Użyj ustawień aplikacji funkcji podanych w tabeli pod obrazem.

    ![Definiowanie nowych ustawień aplikacji funkcji](./media/functions-create-function-app-portal/function-app-create-flow2.png)

    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nazwa aplikacji** | Nazwa unikatowa w skali globalnej | Nazwa identyfikująca nową aplikację funkcji. Prawidłowe znaki to `a-z`, `0-9` i `-`.  | 
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której jest tworzona ta nowa aplikacja funkcji. | 
    | **[Grupa zasobów](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Nazwa nowej grupy zasobów, w której ma zostać utworzona aplikacja funkcji. |
    | **OS** | Windows | Dostępne opcje języka zależą od systemu operacyjnego aplikacji funkcji. Na przykład środowisko Python wymaga systemu Linux. |
    | **[Plan hostingu](../articles/azure-functions/functions-scale.md)** | Plan Zużycie | Plan hostingu określający sposób przydzielania zasobów do aplikacji funkcji. W domyślnym planie (**Plan Zużycie**) zasoby są dodawane dynamicznie zgodnie z wymaganiami funkcji. W przypadku tego hostingu [bezserwerowego](https://azure.microsoft.com/overview/serverless-computing/) opłaty są naliczane tylko wtedy, gdy funkcje są uruchomione. W przypadku uruchomienia w ramach planu usługi App Service musisz zarządzać [skalowaniem aplikacji funkcji](../articles/azure-functions/functions-scale.md).  |
    | **Location** | Europa Zachodnia | Wybierz [region](https://azure.microsoft.com/regions/) w swojej okolicy lub w pobliżu innych usług, do których Twoje funkcje uzyskują dostęp. |
    | **Stos środowiska uruchomieniowego** | Preferowany język | Wybierz środowisko uruchomieniowe, które obsługuje ulubiony język programowania funkcji. Wybierz **.NET** dla funkcji w językach C# i F#. |
    | **[Storage](../articles/storage/common/storage-quickstart-create-account.md)** |  Nazwa unikatowa w skali globalnej |  Utwórz konto magazynu używane przez aplikację funkcji. Nazwy kont usługi Storage muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery. Możesz również użyć istniejącego konta, które musi spełniać [wymagania dotyczące konta magazynu](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Domyślny | Tworzy zasób Application Insights o tej samej *nazwie aplikacji* w najbliższym obsługiwanym regionie. Rozszerzając to ustawienie, można zmienić **nazwę nowego zasobu** lub wybrać inną **lokalizację w lokalizacji** geograficznej [platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/) , w której mają być przechowywane dane. |

3. Wybierz pozycję **Utwórz**, aby zaaprowizować aplikację funkcji i wdrożyć ją.

4. Wybierz ikonę Powiadomienie w prawym górnym rogu portalu i poszukaj komunikatu **Wdrażanie zakończone pomyślnie**.

    ![Definiowanie nowych ustawień aplikacji funkcji](./media/functions-create-function-app-portal/function-app-create-notification.png)

5. Wybierz pozycję **Przejdź do zasobu**, aby wyświetlić nową aplikację funkcji. Możesz również wybrać pozycję **Przypnij do pulpitu nawigacyjnego**. Przypinanie ułatwia powrót do tego zasobu aplikacji funkcji z pulpitu nawigacyjnego.
