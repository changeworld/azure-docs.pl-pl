---
title: Azure VMware Solutions (Automatyczna synchronizacja) — Omówienie maszyn wirtualnych
description: Dowiedz się więcej o maszynach wirtualnych o automatycznej synchronizacji i ich korzyściach.
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0e6cc6d51e37cea98cc553a236295311d21f595a
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024912"
---
# <a name="avs-virtual-machines-overview"></a>Omówienie automatycznej synchronizacji maszyn wirtualnych

Automatyczna synchronizacja umożliwia zarządzanie maszynami wirtualnymi VMware z poziomu Azure Portal. Klaster lub Pula zasobów z klastra vSphere jest zarządzana za pośrednictwem platformy Azure, mapując ją na subskrypcję.

Aby utworzyć maszynę wirtualną o automatycznej synchronizacji na platformie Azure, szablon maszyny wirtualnej musi znajdować się w programie vCenter w chmurze prywatnej chmury. Szablon służy do dostosowywania systemu operacyjnego i aplikacji. Szablon maszyny wirtualnej może być zaostrzony w celu spełnienia zasad zabezpieczeń przedsiębiorstwa. Przy użyciu szablonu można tworzyć maszyny wirtualne, a następnie korzystać z nich z Azure Portal przy użyciu modelu samoobsługowego.

## <a name="benefits"></a>Korzyści

Automatyczna synchronizacja maszyn wirtualnych z Azure Portal udostępnia mechanizm samoobsługowy do tworzenia maszyn wirtualnych VMware i zarządzania nimi.

* Tworzenie maszyny wirtualnej o automatycznej synchronizacji w chmurze prywatnej chmury programu vCenter
* Zarządzanie właściwościami maszyny wirtualnej
  * Dodawanie/usuwanie dysków
  * Dodawanie/usuwanie kart sieciowych
* Operacje na maszynie wirtualnej automatycznej synchronizacji
  * Włącz i Wyłącz zasilanie
  * Resetuj maszynę wirtualną
* Usuń maszynę wirtualną

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [korzystać z maszyn wirtualnych VMware na platformie Azure](quickstart-create-vmware-virtual-machine.md)
* Dowiedz się, jak [mapować swoją subskrypcję platformy Azure](azure-subscription-mapping.md)
