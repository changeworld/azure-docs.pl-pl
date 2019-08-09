---
title: Usuwanie rozwiązania Azure VMware według chmury prywatnej CloudSimple
description: Opisuje, jak usunąć chmurę prywatną CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8a47968ec252f628da8a1a36570fb06eb4bb10bf
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68886951"
---
# <a name="delete-a-cloudsimple-private-cloud"></a>Usuń chmurę prywatną CloudSimple

CloudSimple zapewnia elastyczność usuwania chmury prywatnej.  Chmura prywatna składa się z co najmniej jednego klastra vSphere. Każdy klaster może zawierać od 3 do 16 węzłów. Po usunięciu chmury prywatnej wszystkie klastry zostaną usunięte. 

## <a name="before-you-begin"></a>Przed rozpoczęciem

Usunięcie chmury prywatnej spowoduje usunięcie całej chmury prywatnej.  Wszystkie składniki chmury prywatnej zostaną usunięte.  Aby zachować jakiekolwiek dane, należy wykonać kopię zapasową danych w magazynie lokalnym lub w usłudze Azure Storage. 

Składniki chmury prywatnej obejmują:

* CloudSimple węzły
* Maszyny wirtualne
* Sieci VLAN/podsieci
* Wszystkie dane użytkownika przechowywane w chmurze prywatnej
* Wszystkie załączniki reguły zapory do sieci VLAN/podsieci


## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-a-private-cloud"></a>Usuń chmurę prywatną 

1. [Dostęp do portalu CloudSimple](access-cloudsimple-portal.md).

2. Otwórz stronę **zasoby** .

3. Kliknij chmurę prywatną, którą chcesz usunąć.

4. Na stronie Podsumowanie kliknij przycisk **Usuń**.

    ![Usuń chmurę prywatną](media/delete-private-cloud.png)

5. Na stronie Potwierdzenie wprowadź nazwę chmury prywatnej, a następnie kliknij przycisk **Usuń**. 

    ![Usuń chmurę prywatną — Potwierdź](media/delete-private-cloud-confirm.png)


Chmura prywatna jest oznaczona do usunięcia.  Proces usuwania rozpocznie się po trzech godzinach i usuwa chmurę prywatną.

> [!CAUTION]
> Węzły należy usunąć po usunięciu chmury prywatnej.  Pomiary węzłów będą kontynuowane do momentu usunięcia ich z subskrypcji.


## <a name="next-steps"></a>Następne kroki

* [Usuń węzły](delete-nodes.md)
