---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 07/17/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: a0f6ca72e6d3e487e85d16a6860e0d84003a9aa8
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2018
ms.locfileid: "40258872"
---
1. Wybierz przycisk **Nowy** znajdujący się w lewym górnym rogu witryny Azure Portal, a następnie wybierz pozycje **Obliczenia** > **Aplikacja funkcji**. 

    ![Tworzenie aplikacji funkcji w witrynie Azure Portal](./media/functions-create-function-app-portal/function-app-create-flow.png)

2. Użyj ustawień aplikacji funkcji podanych w tabeli pod obrazem.

    ![Definiowanie nowych ustawień aplikacji funkcji](./media/functions-create-function-app-portal/function-app-create-flow2.png)

    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nazwa aplikacji** | Nazwa unikatowa w skali globalnej | Nazwa identyfikująca nową aplikację funkcji. Prawidłowe znaki to `a-z`, `0-9` i `-`.  | 
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której jest tworzona ta nowa aplikacja funkcji. | 
    | **[Grupa zasobów](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Nazwa nowej grupy zasobów, w której ma zostać utworzona aplikacja funkcji. | 
    | **OS** | Windows | Hosting bezserwerowy jest obecnie dostępny tylko w przypadku uruchamiania w systemie Windows. Aby uzyskać informacje o hostingu w systemie Linux, zobacz [Tworzenie pierwszej funkcji działającej w systemie Linux z poziomu interfejsu wiersza polecenia platformy Azure](../articles/azure-functions/functions-create-first-azure-function-azure-cli-linux.md). |
    | **[Plan hostingu](../articles/azure-functions/functions-scale.md)** | Plan Zużycie | Plan hostingu określający sposób przydzielania zasobów do aplikacji funkcji. W domyślnym planie (**Plan Zużycie**) zasoby są dodawane dynamicznie zgodnie z wymaganiami funkcji. W przypadku tego hostingu [bezserwerowego](https://azure.microsoft.com/overview/serverless-computing/) opłaty są naliczane tylko wtedy, gdy funkcje są uruchomione. W przypadku uruchomienia w ramach planu usługi App Service musisz zarządzać [skalowaniem aplikacji funkcji](../articles/azure-functions/functions-scale.md).  |
    | **Lokalizacja** | Europa Zachodnia | Wybierz [region](https://azure.microsoft.com/regions/) w swojej okolicy lub w pobliżu innych usług, do których Twoje funkcje uzyskują dostęp. |
    | **[Konto magazynu](../articles/storage/common/storage-quickstart-create-account.md)** |  Nazwa unikatowa w skali globalnej |  Nazwa nowego konta magazynu używanego przez aplikację funkcji. Nazwy kont usługi Storage muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery. Możesz również użyć istniejącego konta, które musi spełniać [wymagania dotyczące konta magazynu](../articles/azure-functions/functions-scale.md#storage-account-requirements). |

3. Wybierz pozycję **Utwórz**, aby zaaprowizować aplikację funkcji i wdrożyć ją.

4. Wybierz ikonę Powiadomienie w prawym górnym rogu portalu i poszukaj komunikatu **Wdrażanie zakończone pomyślnie**. 

    ![Definiowanie nowych ustawień aplikacji funkcji](./media/functions-create-function-app-portal/function-app-create-notification.png)

5. Wybierz pozycję **Przejdź do zasobu**, aby wyświetlić nową aplikację funkcji.

> [!TIP]
> Jeśli masz problem ze znalezieniem aplikacji funkcji w portalu, spróbuj [dodać aplikacje funkcji do ulubionych w witrynie Azure Portal](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#favorite).   
