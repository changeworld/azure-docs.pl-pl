---
title: Konfigurowanie zestawu skalowania maszyny wirtualnej przy pomocą istniejącego modułu równoważenia obciążenia platformy Azure — witryna Azure portal
description: Dowiedz się, jak skonfigurować zestaw skalowania maszyny wirtualnej przy pomocą istniejącego modułu równoważenia obciążenia platformy Azure.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: b2a83b226a4b2ddbbd554783de6a2b5c85c92f0a
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349722"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-portal"></a>Konfigurowanie zestawu skalowania maszyny wirtualnej przy użyciu istniejącego modułu równoważenia obciążenia platformy Azure przy użyciu portalu Azure

W tym artykule dowiesz się, jak skonfigurować zestaw skalowania maszyny wirtualnej przy pomocą istniejącego modułu równoważenia obciążenia platformy Azure. 

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure.
- Istniejący standardowy moduł równoważenia obciążenia sku w subskrypcji, w której zostanie wdrożony zestaw skalowania maszyny wirtualnej.
- Sieć wirtualna platformy Azure dla zestawu skalowania maszyny wirtualnej.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do witryny Azure portal w [https://portal.azure.com](https://portal.azure.com).



## <a name="deploy-virtual-machine-scale-set-with-existing-load-balancer"></a>Wdrażanie zestawu skalowania maszyny wirtualnej z istniejącym modułem równoważenia obciążenia

W tej sekcji utworzysz zestaw skalowania maszyny wirtualnej w witrynie Azure portal z istniejącym modułem równoważenia obciążenia platformy Azure.

> [!NOTE]
> W poniższych krokach przyjęto założenie, że sieć wirtualna o nazwie **myVNet** i moduł równoważenia obciążenia platformy Azure o nazwie **myLoadBalancer** został wcześniej wdrożony.

1. W lewym górnym rogu ekranu kliknij pozycję Utwórz**zestaw skalowania maszyny wirtualnej** **obliczeniowej** >  **zasobu** > lub wyszukaj **skalę maszyny wirtualnej ustawioną** w wyszukiwarce marketplace.

2. Wybierz **pozycję Utwórz**.

3. W **obszarze Tworzenie zestawu skalowania maszyny wirtualnej**wprowadź lub wybierz te informacje na karcie **Podstawy:**

    | Ustawienie                        | Wartość                                                                                                 |
    |--------------------------------|-------------------------------------------------------------------------------------------------------|
    | **Szczegóły projektu**            |                                                                                                       |
    | Subskrypcja                   | Wybierz subskrypcję platformy Azure                                                                        |
    | Grupa zasobów                 | Wybierz pozycję Utwórz nowy, wprowadź **myResourceGroup**, a następnie wybierz przycisk OK lub wybierz istniejącą grupę zasobów. |
    | **Szczegóły zestawu skalowania**          |                                                                                                       |
    | Nazwa zestawu skalowania maszyn wirtualnych | Wprowadź **myVMSS**                                                                                      |
    | Region                         | Wybierz **wschodnie stany USA 2**                                                                                    |
    | Strefa dostępności              | Wybierz **opcję Brak**                                                                                       |
    | **Szczegóły wystąpienia**           |                                                                                                       |
    | Image (Obraz)                          | Wybierz **serwer Ubuntu 18.04 LTS**                                                                    |
    | Wystąpienie spot platformy Azure            | Wybierz **nie**                                                                                         |
    | Rozmiar                           | Zostaw domyślnie                                                                                      |
    | **Konto administratora**      |                                                                                                       |
    | Typ uwierzytelniania            | Wybierz **hasło**                                                                                   |
    | Nazwa użytkownika                       | Wpisz nazwę użytkownika administratora        |
    | Hasło                       | Wprowadź hasło administratora    |
    | Potwierdź hasło               | Ponowne wniesienie hasła administratora |


    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-01.png" alt-text="Utwórz zestaw skalowania maszyny wirtualnej." border="true":::

4. Wybierz kartę **Sieć.**

5. Wprowadź lub wybierz te informacje na karcie **Sieć:**

     Ustawienie                           | Wartość                                                    |
    |-----------------------------------|----------------------------------------------------------|
    | **Konfiguracja sieci wirtualnej** |                                                          |
    | Sieć wirtualna                   | Wybierz **myVNet** lub istniejącą sieć wirtualną.      |
    | **Równoważenie obciążenia**                |                                                          |
    | Używanie modułu równoważenia obciążenia               | Wybierz **tak**                                           |
    | **Ustawienia równoważenia obciążenia**       |                                                          |
    | Opcje równoważenia obciążenia            | Wybierz **moduł równoważenia obciążenia platformy Azure**                           |
    | Wybieranie modułu równoważenia obciążenia            | Wybierz **myLoadBalancer** lub istniejący moduł równoważenia obciążenia |
    | Wybieranie puli wewnętrznej bazy danych             | Wybierz **myBackendPool** lub istniejącą pulę zaplecza.  |

    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-02.png" alt-text="Utwórz zestaw skalowania maszyny wirtualnej." border="true":::

6. Wybierz kartę **Zarządzanie.**

7. Na karcie **Zarządzanie** ustaw **diagnostykę rozruchu** na **Wyłącz**.

8. Wybierz niebieski przycisk **Recenzja + utwórz.**

9. Przejrzyj ustawienia i wybierz przycisk **Utwórz.**

## <a name="next-steps"></a>Następne kroki

W tym artykule wdrożono zestaw skalowania maszyny wirtualnej z istniejącym modułem równoważenia obciążenia platformy Azure.  Aby dowiedzieć się więcej o zestawach skalowania maszyn wirtualnych i równoważącym obciążenia, zobacz:

- [Co to jest usługa Azure Load Balancer?](load-balancer-overview.md)
- [Co to są zestawy skalowania maszyn wirtualnych?](../virtual-machine-scale-sets/overview.md)
