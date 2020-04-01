---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 03/01/2020
ms.author: allensu
ms.custom: include file
ms.openlocfilehash: 81db46b52c9b4fe800f2fbfeadad966995d66e12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78244981"
---
## <a name="create-the-virtual-network"></a>Tworzenie sieci wirtualnej

W tej sekcji utworzysz sieć wirtualną i podsieć.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób > sieć > sieci wirtualnej** lub wyszukaj **sieć wirtualną** w polu wyszukiwania.

2. W **obszarze Tworzenie sieci wirtualnej**wprowadź lub wybierz te informacje na karcie **Podstawy:**

    | **Ustawienie**          | **Wartość**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Szczegóły projektu**  |                                                                 |
    | Subskrypcja     | Wybierz subskrypcję platformy Azure                                  |
    | Grupa zasobów   | Wybierz **pozycję Utwórz nową**, wprowadź ** \<>nazwa grupy zasobów **, a następnie wybierz przycisk OK lub wybierz istniejącą ** \<nazwę grupy zasobów>** na podstawie parametrów. |
    | **Szczegóły wystąpienia** |                                                                 |
    | Nazwa             | Wprowadź ** \<>nazwa sieci wirtualnej**                                    |
    | Region           | Wybierz ** \<>nazwa regionu** |

3. Wybierz kartę **Adresy IP** lub wybierz przycisk **Dalej: Adresy IP** u dołu strony.

4. Na karcie **Adresy IP** wprowadź następujące informacje:

    | Ustawienie            | Wartość                      |
    |--------------------|----------------------------|
    | Przestrzeń adresowa IPv4 | Wprowadź ** \<>IPv4-address-space** |

5. W **obszarze Nazwa podsieci**wybierz słowo **domyślne**.

6. W **podsieci Edytuj**wprowadź te informacje:

    | Ustawienie            | Wartość                      |
    |--------------------|----------------------------|
    | Nazwa podsieci | Wprowadź ** \<>nazwa podsieci** |
    | Zakres adresów podsieci | Wprowadź ** \<>zakresu adresu podsieci**

7. Wybierz **pozycję Zapisz**.

8. Wybierz kartę **Recenzja + utwórz** lub wybierz przycisk **Recenzja + utwórz.**

9. Wybierz **pozycję Utwórz**.