---
title: Azure VMware Solutions (Automatyczna synchronizacja) — przydzielanie publicznych adresów IP
description: Opisuje sposób przydzielenia publicznych adresów IP maszyn wirtualnych w środowisku chmury prywatnej automatycznej synchronizacji
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 87133f5efb9f096d3fdb0956aab1caac58b4bd94
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024300"
---
# <a name="allocate-public-ip-addresses-for-avs-private-cloud-environment"></a>Przydziel publiczne adresy IP do automatycznej synchronizacji środowiska chmury prywatnej

Otwórz kartę publiczne adresy IP na stronie sieci, aby przydzielić publiczne adresy dla maszyn wirtualnych w środowisku chmury prywatnej automatycznej synchronizacji.

1. [Uzyskaj dostęp do portalu automatycznej synchronizacji](access-cloudsimple-portal.md) i wybierz pozycję **Sieć** w menu po stronie.
2. Wybierz pozycję **publiczne adresy IP**.
3. Kliknij pozycję **Nowy publiczny adres IP**.

    ![Strona publiczne adresy IP](media/public-ips-page.png)

4. Wprowadź nazwę identyfikującą wpis adresu IP.
5. Zachowaj lokalizację domyślną.
6. Użyj suwaka, aby zmienić limit czasu bezczynności, jeśli jest to konieczne.
7. Wprowadź lokalny adres IP, dla którego chcesz przypisać publiczny adres IP.
8. Wprowadź skojarzoną nazwę DNS.
9. Kliknij przycisk **Prześlij**.

![Przydziel publiczne adresy IP](media/network-public-ip-allocate.png)

Zostanie rozpoczęte zadanie alokowania publicznego adresu IP. Stan zadania można sprawdzić na stronie **zadania > zadań** . Po zakończeniu alokacji nowy wpis zostanie wyświetlony na stronie publiczne adresy IP.
