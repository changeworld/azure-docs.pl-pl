---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: jeffhollan
ms.service: azure-functions
ms.topic: include
ms.date: 10/25/2019
ms.author: jehollan, glenga
ms.custom: include file
ms.openlocfilehash: fca8c3b0b1bd4d22720a6d15313e297d05b7fac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76021089"
---
1. Otwórz portal Azure z[https://portal.azure.com](https://portal.azure.com)

1. Wybieranie przycisku **Utwórz zasób**

    ![Tworzenie zasobu](./media/functions-create-function-app-portal/function-app-create-resource.png)

1. Wybierz pozycję Aplikacja funkcji **obliczeniowych** > **Function App**.

    ![Tworzenie aplikacji funkcji w witrynie Azure Portal](./media/functions-premium-create/function-app-create-start.png)

1. Użyj ustawień aplikacji funkcji podanych w tabeli pod obrazem.

    ![Podstawy](./media/functions-premium-create/function-app-create-basics.png)

    | Ustawienie      | Sugerowana wartość  | Opis |
    | ------------ | ---------------- | ----------- |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której jest tworzona ta nowa aplikacja funkcji. |
    | **[Grupa zasobów](../articles/azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Nazwa nowej grupy zasobów, w której ma zostać utworzona aplikacja funkcji. |
    | **Nazwa aplikacji funkcji** | Nazwa unikatowa w skali globalnej | Nazwa identyfikująca nową aplikację funkcji. Prawidłowe `a-z` znaki są (bez uwzględniania wielkości liter), `0-9`i `-`.  |
    |**Publikowanie**| Code | Opcja publikowania plików kodu lub kontenera Docker. |
    | **Stos środowiska uruchomieniowego** | Preferowany język | Wybierz środowisko uruchomieniowe, które obsługuje ulubiony język programowania funkcji. Wybierz **.NET** dla funkcji w językach C# i F#. |
    |**Region**| Preferowany region | Wybierz [region](https://azure.microsoft.com/regions/) w swojej okolicy lub w pobliżu innych usług, do których Twoje funkcje uzyskują dostęp. |

    Wybierz przycisk **Dalej : Hosting >.**

1. Wprowadź następujące ustawienia hostingu.

    ![Hosting](./media/functions-premium-create/function-app-premium-create-hosting.png)

    | Ustawienie      | Sugerowana wartość  | Opis |
    | ------------ | ---------------- | ----------- |
    | **[Konto magazynu](../articles/storage/common/storage-account-create.md)** |  Nazwa unikatowa w skali globalnej |  Utwórz konto magazynu używane przez aplikację funkcji. Nazwy kont usługi Magazyn muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery. Można również użyć istniejącego konta, które musi spełniać [wymagania konta magazynu](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    |**System operacyjny**| Preferowany system operacyjny | System operacyjny jest wstępnie wybrany na podstawie wyboru stosu środowiska wykonawczego, ale w razie potrzeby można zmienić to ustawienie. |
    | **[Plan](../articles/azure-functions/functions-scale.md)** | Premium | W polu Typ planu wybierz pozycję **Premium (Wersja zapoznawcza)** i wybierz ustawienia domyślne dla opcji *Plan systemu Windows* i *Sku oraz rozmiar.* |

    Wybierz przycisk **Dalej : Monitorowanie >.**

1. Wprowadź następujące ustawienia monitorowania.

    ![Monitorowanie](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

    | Ustawienie      | Sugerowana wartość  | Opis |
    | ------------ | ---------------- | ----------- |
    | **[Wgląd w aplikacje](../articles/azure-functions/functions-monitoring.md)** | Domyślne | Tworzy zasób usługi Application Insights o tej samej *nazwie aplikacji* w najbliższym obsługiwanym regionie. Rozwijając to ustawienie, można zmienić **nazwę nowego zasobu** lub wybrać inną **lokalizację** w lokalizacji [platformy Azure,](https://azure.microsoft.com/global-infrastructure/geographies/) w której chcesz przechowywać dane. |

    Wybierz **pozycję Przejrzyj + Utwórz,** aby przejrzeć wybrane ustawienia konfiguracji aplikacji.

1. Wybierz pozycję **Utwórz**, aby zaaprowizować aplikację funkcji i wdrożyć ją.