---
title: Rozwiązanie Azure VMware według cloudSimple — przydzielanie publicznych adresów IP
description: W tym artykule opisano sposób przydzielania publicznych adresów IP dla maszyn wirtualnych w środowisku private cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 565c9ad0fbd37b026a1ba555d83b9032d2efcba4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024300"
---
# <a name="allocate-public-ip-addresses-for-private-cloud-environment"></a>Przydzielanie publicznych adresów IP dla środowiska private cloud

Otwórz kartę Publiczne adresy IP na stronie Sieć, aby przydzielić publiczne adresy IP dla maszyn wirtualnych w środowisku chmury prywatnej.

1. [Przejdź do portalu CloudSimple](access-cloudsimple-portal.md) i wybierz polecenie **Sieć** w menu bocznym.
2. Wybierz **publiczne wiadomości IP**.
3. Kliknij **pozycję Nowy publiczny adres IP**.

    ![Strona publiczne wiadomości IP](media/public-ips-page.png)

4. Wprowadź nazwę, aby zidentyfikować wpis adresu IP.
5. Zachowaj domyślną lokalizację.
6. Użyj suwaka, aby w razie potrzeby zmienić limit czasu bezczynnego.
7. Wprowadź lokalny adres IP, do którego chcesz przypisać publiczny adres IP.
8. Wprowadź skojarzoną nazwę DNS.
9. Kliknij **przycisk Prześlij**.

![Przydzielanie publicznych usług IP](media/network-public-ip-allocate.png)

Rozpoczyna się zadanie przydzielania publicznego adresu IP. Stan zadania można sprawdzić na stronie **Działanie > Zadania.** Po zakończeniu alokacji nowy wpis jest wyświetlany na stronie Publiczne wiadomości IP.
