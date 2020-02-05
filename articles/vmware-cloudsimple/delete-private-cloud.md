---
title: Usuń chmurę prywatną rozwiązań Azure VMware (Automatyczna synchronizacja)
description: Opisuje, jak usunąć chmurę prywatną do automatycznej synchronizacji.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 77cbfb19c3861bac517142f7491e6b1a5fb4ca27
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024759"
---
# <a name="delete-an-avs-private-cloud"></a>Usuń chmurę prywatną do automatycznej synchronizacji

Funkcja automatycznej synchronizacji zapewnia elastyczność usuwania chmury prywatnej automatycznej synchronizacji. Chmura prywatna do automatycznej synchronizacji składa się z co najmniej jednego klastra vSphere. Każdy klaster może zawierać od 3 do 16 węzłów. Po usunięciu chmury prywatnej automatycznej synchronizacji wszystkie klastry zostaną usunięte.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Usunięcie chmury prywatnej automatycznej synchronizacji spowoduje usunięcie całej chmury prywatnej automatycznej synchronizacji. Wszystkie składniki chmury prywatnej automatycznej synchronizacji zostaną usunięte. Aby zachować jakiekolwiek dane, należy wykonać kopię zapasową danych w magazynie lokalnym lub w usłudze Azure Storage.

Składniki chmury prywatnej do automatycznej synchronizacji obejmują:

* Automatyczna synchronizacja węzłów
* Maszyny wirtualne
* Sieci VLAN/podsieci
* Wszystkie dane użytkownika przechowywane w chmurze prywatnej automatycznej synchronizacji
* Wszystkie załączniki reguły zapory do sieci VLAN/podsieci

## <a name="sign-in-to-azure"></a>Zaloguj się w usłudze Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-an-avs-private-cloud"></a>Usuń chmurę prywatną do automatycznej synchronizacji

1. [Uzyskaj dostęp do portalu automatycznej synchronizacji](access-cloudsimple-portal.md).

2. Otwórz stronę **zasoby** .

3. Kliknij chmurę prywatną automatycznej synchronizacji, którą chcesz usunąć

4. Na stronie Podsumowanie kliknij przycisk **Usuń**.

    ![Usuń swoją chmurę prywatną](media/delete-private-cloud.png)

5. Na stronie Potwierdzenie wprowadź nazwę chmury prywatnej automatycznej synchronizacji, a następnie kliknij przycisk **Usuń**. 

    ![Usuń swoją chmurę prywatną do automatycznej synchronizacji — Potwierdź](media/delete-private-cloud-confirm.png)

Chmura prywatna w wersji zaautomatycznej jest oznaczona do usunięcia. Proces usuwania rozpocznie się po trzech godzinach i usuwa chmurę prywatną automatycznej synchronizacji.

> [!CAUTION]
> Węzły należy usunąć po usunięciu chmury prywatnej automatycznej synchronizacji. Pomiary węzłów będą kontynuowane, dopóki węzły nie zostaną usunięte z subskrypcji.

## <a name="next-steps"></a>Następne kroki

* [Usuń węzły](delete-nodes.md)
