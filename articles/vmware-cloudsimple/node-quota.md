---
title: Rozwiązanie programu VMware na platformie Azure przez CloudSimple - CloudSimple przydziału węzła
description: Opis limitów przydziału dla węzłów CloudSimple oraz zażądać zwiększenia limitu przydziału
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fd1416befb74a7299136ea497eccc8a06b7f0f6a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164863"
---
# <a name="cloudsimple-node-quota-limits"></a>Limity przydziału węzła CloudSimple

Cztery węzły jest domyślna ilość dostępnych do inicjowania obsługi, jeśli Twoja subskrypcja została włączona dla usługi CloudSimple.  Można udostępnić dowolne [typ węzła](cloudsimple-node.md) z witryny Azure portal.  Co najmniej trzy węzły tej samej jednostki SKU są wymagane do utworzenia chmury prywatnej.  Jeśli aprowizowaniu węzłów, zobaczysz błąd przy próbie aprowizować dodatkowe węzły.

## <a name="quota-increase"></a>Zwiększenia limitu przydziału

Aby zwiększyć limit przydziału węzła, należy przesłać żądanie pomocy technicznej. Zespół operacyjny usługi oceny żądania i współpraca z Tobą w celu zwiększenia limitu węzła.  Po otwarciu nowego biletu, należy wybrać następujące opcje:

* Typ problemu: **Technical Preview**
* Subskrypcja: **Identyfikator subskrypcji**
* Typ usługi: **VMware Solution by CloudSimple**
* Typ problemu: **Dedykowane węzły limitu przydziału**
* Podtyp problemu: **Zwiększ limit przydziału wynoszący węzły dedykowane**
* Temat: **Zwiększenia limitu przydziału**

W obszarze szczegółów bilet pomocy technicznej zapewniają wymaganą liczbę węzłów i węzła jednostki SKU.

Możesz również skontaktować się ze swoim przedstawicielem firmy Microsoft w [ azurevmwaresales@microsoft.com ](mailto:azurevmwaresales@microsoft.com) Aby zwiększyć limit przydziału węzła w ramach Twojej subskrypcji.  Musisz podać:

* Identyfikator subskrypcji
* Węzeł jednostki SKU
* Liczba kolejnych węzłów, dla których one żądanie zwiększenia limitu przydziału

## <a name="next-steps"></a>Kolejne kroki

* [Aprowizacja węzłów](create-nodes.md)
* [Omówienie węzłów CloudSimple](cloudsimple-node.md)