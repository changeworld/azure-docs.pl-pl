---
title: Usuwanie rozwiązania VMware platformy Azure przez chmurę prywatną CloudSimple
description: W tym artykule opisano sposób usuwania cloudSimple private cloud.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6bc3e7030c500ea2d6072a1cce0f0b3d9fc62801
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024759"
---
# <a name="delete-a-cloudsimple-private-cloud"></a>Usuwanie chmury prywatnej CloudSimple

CloudSimple zapewnia elastyczność, aby usunąć private cloud.  Chmura prywatna składa się z jednego lub więcej klastrów vSphere. Każdy klaster może mieć od 3 do 16 węzłów. Po usunięciu chmury prywatnej wszystkie klastry zostaną usunięte.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Usunięcie chmury prywatnej powoduje usunięcie całej chmury prywatnej.  Wszystkie składniki chmury prywatnej zostaną usunięte.  Jeśli chcesz zachować dane, upewnij się, że masz kopię zapasową danych do magazynu lokalnego lub magazynu platformy Azure.

Składniki chmury prywatnej obejmują:

* Węzły CloudSimple
* Maszyny wirtualne
* Sieci VLAN/podsieci
* Wszystkie dane użytkownika przechowywane w chmurze prywatnej
* Wszystkie załączniki reguł zapory do sieci VLAN/Podsieci

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure portal w [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-a-private-cloud"></a>Usuwanie chmury prywatnej

1. [Uzyskaj dostęp do portalu CloudSimple](access-cloudsimple-portal.md).

2. Otwórz stronę **Zasoby.**

3. Kliknij chmurę prywatną, którą chcesz usunąć

4. Na stronie podsumowania kliknij pozycję **Usuń**.

    ![Usuwanie chmury prywatnej](media/delete-private-cloud.png)

5. Na stronie potwierdzenia wprowadź nazwę chmury prywatnej i kliknij przycisk **Usuń**. 

    ![Usuwanie chmury prywatnej - potwierdź](media/delete-private-cloud-confirm.png)

Chmura prywatna jest oznaczona do usunięcia.  Proces usuwania rozpoczyna się po trzech godzinach i usuwa private cloud.

> [!CAUTION]
> Węzły muszą zostać usunięte po usunięciu chmury prywatnej.  Pomiar węzłów będzie kontynuowany do węzłów są usuwane z subskrypcji.

## <a name="next-steps"></a>Następne kroki

* [Usuwanie węzłów](delete-nodes.md)
