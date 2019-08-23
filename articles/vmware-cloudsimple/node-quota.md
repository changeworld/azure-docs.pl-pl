---
title: Rozwiązanie VMware platformy Azure według przydziału węzła CloudSimple-CloudSimple
description: Opisuje limity przydziału dla węzłów CloudSimple oraz sposób żądania zwiększenia limitu przydziału
author: sharaths-cs
ms.author: dikamath
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 12fecfc3a20d91b9324e1662f3f937340b7ff5c4
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972451"
---
# <a name="cloudsimple-node-quota-limits"></a>Limity przydziału węzłów CloudSimple

Cztery węzły są domyślną ilością dostępną do zakupu, gdy subskrypcja jest włączona dla usługi CloudSimple.  Każdy [Typ węzła](cloudsimple-node.md) można zakupić z poziomu Azure Portal.  Aby można było utworzyć chmurę prywatną, wymagane są co najmniej trzy węzły tej samej jednostki SKU.  Jeśli węzły zostały kupione, podczas próby zakupu dodatkowych węzłów może zostać wyświetlony komunikat o błędzie.

## <a name="quota-increase"></a>Zwiększenie limitu przydziału

Limit przydziału węzłów można zwiększyć, przesyłając żądanie pomocy technicznej. Zespół operacyjny usługi oceni żądanie i skontaktuje się z nim, aby zwiększyć przydział węzła.  Wybierz poniższe opcje, aby otworzyć nowy bilet:

* Typ problemu: **Naukow**
* Subskrypcja: **Identyfikator subskrypcji**
* Typ usługi: **Rozwiązanie VMware według CloudSimple**
* Typ problemu: **Limit przydziału węzłów dedykowanych**
* Podtyp problemu: **Zwiększenie przydziału węzłów dedykowanych**
* Temat: **Zwiększenie limitu przydziału**

W obszarze Szczegóły biletu pomocy technicznej Podaj wymaganą liczbę węzłów i jednostkę SKU węzła.

Możesz również skontaktować się z przedstawicielem [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) konto Microsoft w celu zwiększenia przydziału węzła w ramach subskrypcji.  Musisz podać:

* Identyfikator subskrypcji
* Jednostka SKU węzła
* Liczba dodatkowych węzłów, dla których żądasz zwiększenia limitu przydziału

## <a name="next-steps"></a>Następne kroki

* [Kup węzły](create-nodes.md)
* [Przegląd węzłów CloudSimple](cloudsimple-node.md)
