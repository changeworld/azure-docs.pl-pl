---
title: Zmniejsz chmurę prywatną rozwiązań Azure VMware (Automatyczna synchronizacja)
description: Opisuje, jak zmniejszyć chmurę prywatną.
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0ea764081cd0b4d5c6d44cd7364d1e9a89a3cec3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014270"
---
# <a name="shrink-an-avs-private-cloud"></a>Zmniejsz swoją chmurę prywatną

Funkcja automatycznej synchronizacji zapewnia elastyczność dynamicznego zmniejszania automatycznej synchronizacji w chmurze prywatnej. Chmura prywatna do automatycznej synchronizacji składa się z co najmniej jednego klastra vSphere. Każdy klaster może zawierać od 3 do 16 węzłów. Podczas zmniejszania chmury prywatnej automatycznej synchronizacji należy usunąć węzeł z istniejącego klastra lub usunąć cały klaster. 

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed skróceniem chmury prywatnej automatycznej synchronizacji należy spełnić następujące warunki. Klaster zarządzania (pierwszy klaster) jest tworzony podczas tworzenia chmury prywatnej automatycznej synchronizacji. Nie można go usunąć.

* Klaster vSphere musi mieć trzy węzły. Nie można zmniejszyć klastra z tylko trzema węzłami.
* Łączny zużyty magazyn nie powinien przekraczać całkowitej pojemności po zmniejszeniu poziomu klastra.
* Sprawdź, czy jakiekolwiek reguły usługi Distributed Resource Scheduler (DRS) uniemożliwiają vMotion maszyny wirtualnej. Jeśli istnieją reguły, wyłącz lub Usuń reguły. Reguły DRS obejmują reguły koligacji maszyny wirtualnej z hostem.


## <a name="sign-in-to-azure"></a>Zaloguj się w usłudze Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="shrinking-an-avs-private-cloud"></a>Zmniejszanie automatycznej synchronizacji w chmurze prywatnej

1. [Uzyskaj dostęp do portalu automatycznej synchronizacji](access-cloudsimple-portal.md).

2. Otwórz stronę **zasoby** .

3. Kliknij chmurę prywatną automatycznej synchronizacji, którą chcesz zmniejszyć

4. Na stronie Podsumowanie kliknij przycisk **Zmniejsz**.

    ![Zmniejsz redyskową chmurę prywatną](media/shrink-private-cloud.png)

5. Wybierz klaster, który chcesz zmniejszyć lub usunąć. 

    ![Zmniejsz swoją chmurę prywatną do automatycznej synchronizacji — wybierz klaster](media/shrink-private-cloud-select-cluster.png)

6. Wybierz pozycję **Usuń jeden węzeł** lub **Usuń cały klaster**. 

7. Weryfikowanie pojemności klastra

8. Kliknij pozycję **Prześlij** , aby zmniejszyć chmurę prywatną do automatycznej synchronizacji.

Rozpocznie się zmniejszanie liczby operacji w chmurze prywatnej automatycznej. Postęp można monitorować w zadaniach. Proces zmniejszania może potrwać kilka godzin w zależności od danych, które muszą zostać ponownie zsynchronizowane w sieci vSAN.

> [!NOTE]
> 1. Jeśli zmniejszasz chmurę prywatną przez usunięcie ostatniego lub jedynego klastra w centrum danych, centrum danych nie zostanie usunięte.
> 2. W przypadku wystąpienia naruszenia reguły DRS węzeł nie zostanie usunięty z klastra, a w opisie zadania zostanie wyświetlona reguła DRS w klastrze.    


## <a name="next-steps"></a>Następne kroki

* [Korzystanie z maszyn wirtualnych VMware na platformie Azure](quickstart-create-vmware-virtual-machine.md)
* Dowiedz się więcej o [automatycznej synchronizacji chmur prywatnych](cloudsimple-private-cloud.md)
