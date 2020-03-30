---
title: Rozwiązanie Azure VMware by CloudSimple — przydział węzła CloudSimple
description: Zawiera opis limitów przydziałów dla węzłów CloudSimple i sposobu żądania zwiększenia przydziału
author: sharaths-cs
ms.author: dikamath
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 83dc9e26e03eb955d88340d1ed21084d4e685ed8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019642"
---
# <a name="cloudsimple-node-quota-limits"></a>Limity przydziałów węzłów CloudSimple

Cztery węzły to domyślna ilość dostępna do zakupu, gdy subskrypcja jest włączona dla usługi CloudSimple.  Dowolny typ [węzła](cloudsimple-node.md) można kupić w witrynie Azure portal.  Co najmniej trzy węzły tej samej jednostki SKU są wymagane do utworzenia chmury prywatnej.  Jeśli zakupiono węzły, podczas próby zakupu dodatkowych węzłów może zostać wyświetlony błąd.

## <a name="quota-increase"></a>Zwiększanie limitu przydziału

Przydział węzła można zwiększyć, przesyłając żądanie pomocy technicznej. Zespół operacyjny usługi oceni żądanie i będzie współpracować z tobą, aby zwiększyć przydział węzła.  Po otwarciu nowego biletu wybierz następujące opcje:

* Typ problemu: **Techniczne**
* Subskrypcja: **Twój identyfikator subskrypcji**
* Typ usługi: **Rozwiązanie VMware według CloudSimple**
* Typ problemu: **Przydział dedykowanych węzłów**
* Podtyp problemu: **Zwiększenie przydziału dedykowanych węzłów**
* Przedmiot: **Zwiększenie kwoty**

W szczegółach biletu pomocy technicznej podaj wymaganą liczbę węzłów i jednostki SKU węzła.

* Jednostka SKU węzła
* Liczba dodatkowych węzłów, dla których żądasz zwiększenia przydziału

## <a name="next-steps"></a>Następne kroki

* [Kupowanie węzłów](create-nodes.md)
* [Omówienie węzłów CloudSimple](cloudsimple-node.md)
