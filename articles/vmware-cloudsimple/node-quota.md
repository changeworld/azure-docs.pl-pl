---
title: Azure VMware Solutions (Automatyczna synchronizacja) — przydział węzłów automatycznej synchronizacji
description: Opisuje limity przydziałów dla węzłów automatycznej synchronizacji i sposoby żądania wzrostu przydziału
author: sharaths-cs
ms.author: dikamath
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fa1b056c8c96fb09def63ca1cd696fc2da5e9bed
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019642"
---
# <a name="avs-node-quota-limits"></a>Limity przydziału węzłów dla synchronizacji

Cztery węzły są domyślną ilością dostępną do zakupu, gdy subskrypcja jest włączona dla usługi automatycznej synchronizacji. Każdy [Typ węzła](cloudsimple-node.md) można zakupić z poziomu Azure Portal. Do utworzenia chmury prywatnej automatycznej synchronizacji wymagane są co najmniej trzy węzły tej samej jednostki SKU. Jeśli węzły zostały kupione, podczas próby zakupu dodatkowych węzłów może zostać wyświetlony komunikat o błędzie.

## <a name="quota-increase"></a>Zwiększanie limitu przydziału

Limit przydziału węzłów można zwiększyć, przesyłając żądanie pomocy technicznej. Zespół operacyjny usługi oceni żądanie i skontaktuje się z nim, aby zwiększyć przydział węzła. Wybierz poniższe opcje, aby otworzyć nowy bilet:

* Typ problemu: **techniczne**
* Subskrypcja: **Identyfikator subskrypcji**
* Typ usługi: **rozwiązanie VMware przez automatyczna synchronizacja**
* Typ problemu: **limit przydziału węzłów dedykowanych**
* Podtyp problemu: **zwiększenie przydziału węzłów dedykowanych**
* Temat: **zwiększenie limitu przydziału**

W obszarze Szczegóły biletu pomocy technicznej Podaj wymaganą liczbę węzłów i jednostkę SKU węzła.

* Jednostka SKU węzła
* Liczba dodatkowych węzłów, dla których żądasz zwiększenia limitu przydziału

## <a name="next-steps"></a>Następne kroki

* [Kup węzły](create-nodes.md)
* [Przegląd automatycznej synchronizacji węzłów](cloudsimple-node.md)
