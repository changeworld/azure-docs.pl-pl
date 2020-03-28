---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/04/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c590b61ee1424d32d83dc5f758682fde37492c3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057123"
---
1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.

1. Na stronie **Nowy** wybierz pozycję Aplikacja funkcji **obliczeniowych** > **Function App**.

1. Na stronie **Podstawy** użyj ustawień aplikacji funkcji określonych w poniższej tabeli.

    | Ustawienie      | Sugerowana wartość  | Opis |
    | ------------ | ---------------- | ----------- |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której jest tworzona ta nowa aplikacja funkcji. |
    | **[Grupa zasobów](../articles/azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Nazwa nowej grupy zasobów, w której ma zostać utworzona aplikacja funkcji. |
    | **Nazwa aplikacji funkcji** | Nazwa unikatowa w skali globalnej | Nazwa identyfikująca nową aplikację funkcji. Prawidłowe `a-z` znaki są (bez uwzględniania wielkości liter), `0-9`i `-`.  |
    |**Publikowanie**| Code | Opcja publikowania plików kodu lub kontenera Docker. |
    | **Stos środowiska uruchomieniowego** | Preferowany język | Wybierz środowisko uruchomieniowe, które obsługuje ulubiony język programowania funkcji. Wybierz **.NET Core** dla funkcji C# i F#. |
    |**Wersja**| Numer wersji | Wybierz wersję zainstalowanego środowiska uruchomieniowego.  |
    |**Region**| Preferowany region | Wybierz [region](https://azure.microsoft.com/regions/) w swojej okolicy lub w pobliżu innych usług, do których Twoje funkcje uzyskują dostęp. |

    ![Podstawy](./media/functions-create-function-app-portal/function-app-create-basics.png)

1. **Wybierz dalej : Hosting**. Na stronie **Hosting** wprowadź następujące ustawienia.

    | Ustawienie      | Sugerowana wartość  | Opis |
    | ------------ | ---------------- | ----------- |
    | **[Konto magazynu](../articles/storage/common/storage-account-create.md)** |  Nazwa unikatowa w skali globalnej |  Utwórz konto magazynu używane przez aplikację funkcji. Nazwy kont magazynu muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery. Można również użyć istniejącego konta, które musi spełniać [wymagania konta magazynu](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    |**System operacyjny**| Preferowany system operacyjny | System operacyjny jest wstępnie wybrany na podstawie wyboru stosu środowiska wykonawczego, ale w razie potrzeby można zmienić to ustawienie. |
    | **[Plan](../articles/azure-functions/functions-scale.md)** | **Zużycie (bez użycia serwera)** | Plan hostingu określający sposób przydzielania zasobów do aplikacji funkcji. W domyślnym planie **zużycia** zasoby są dodawane dynamicznie zgodnie z wymaganiami funkcji. W tym hostingu [bezserwerowym](https://azure.microsoft.com/overview/serverless-computing/) płacisz tylko za czas uruchamiania funkcji. W przypadku uruchomienia w ramach planu usługi App Service musisz zarządzać [skalowaniem aplikacji funkcji](../articles/azure-functions/functions-scale.md).  |

    ![Hosting](./media/functions-create-function-app-portal/function-app-create-hosting.png)

1. **Wybierz dalej : Monitorowanie**. Na stronie **Monitorowanie** wprowadź następujące ustawienia.

    | Ustawienie      | Sugerowana wartość  | Opis |
    | ------------ | ---------------- | ----------- |
    | **[Wgląd w aplikacje](../articles/azure-functions/functions-monitoring.md)** | Domyślne | Tworzy zasób usługi Application Insights o tej samej *nazwie aplikacji* w najbliższym obsługiwanym regionie. Rozwijając to ustawienie, można zmienić **nazwę nowego zasobu** lub wybrać inną **lokalizację** w lokalizacji [platformy Azure,](https://azure.microsoft.com/global-infrastructure/geographies/) w której chcesz przechowywać dane. |

    ![Monitorowanie](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

1. Wybierz **pozycję Przejrzyj + utwórz,** aby przejrzeć wybrane ustawienia konfiguracji aplikacji.

1. Na stronie **Recenzja + tworzenie** przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz,** aby aprowizować i wdrożyć aplikację funkcji.

1. Wybierz ikonę Powiadomienie w prawym górnym rogu portalu i poszukaj komunikatu **Wdrażanie zakończone pomyślnie**.

1. Wybierz pozycję **Przejdź do zasobu**, aby wyświetlić nową aplikację funkcji. Można również wybrać **pozycję Przypnij do pulpitu nawigacyjnego**. Przypinanie ułatwia powrót do tego zasobu aplikacji funkcji z pulpitu nawigacyjnego.

    ![Powiadomienie dotyczące wdrożenia](./media/functions-create-function-app-portal/function-app-create-notification2.png)