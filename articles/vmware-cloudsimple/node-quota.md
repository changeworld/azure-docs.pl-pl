---
title: Rozwiązanie VMware platformy Azure według przydziału węzła CloudSimple-CloudSimple
description: Opisuje limity przydziału dla węzłów CloudSimple oraz sposób żądania zwiększenia limitu przydziału
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e765d7c87f2f941a5e2d558b71c4e5a71d2df9b1
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816673"
---
# <a name="cloudsimple-node-quota-limits"></a>Limity przydziału węzłów CloudSimple

Cztery węzły są domyślną ilością dostępną do aprowizacji, gdy subskrypcja jest włączona dla usługi CloudSimple.  Można zainicjować obsługę dowolnego [typu węzła](cloudsimple-node.md) z Azure Portal.  Do utworzenia chmury prywatnej wymagane jest co najmniej trzy węzły tej samej jednostki SKU.  Jeśli Zainicjowano obsługę węzłów, podczas próby aprowizacji dodatkowych węzłów może zostać wyświetlony komunikat o błędzie.

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

## <a name="next-steps"></a>Kolejne kroki

* [Inicjowanie obsługi węzłów](create-nodes.md)
* [Przegląd węzłów CloudSimple](cloudsimple-node.md)