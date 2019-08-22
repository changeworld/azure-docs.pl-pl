---
title: Rozwiązanie VMware firmy Azure według CloudSimple — Omówienie maszyn wirtualnych
description: Dowiedz się więcej na temat maszyn wirtualnych CloudSimple i ich korzyści.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 213ab51dae20d281a1a0e0f8ea18f4bde888e64d
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877895"
---
# <a name="cloudsimple-virtual-machines-overview"></a>CloudSimple maszyn wirtualnych — Omówienie

CloudSimple umożliwia zarządzanie maszynami wirtualnymi VMware z poziomu Azure Portal.  Klaster lub Pula zasobów z klastra vSphere jest zarządzana za pośrednictwem platformy Azure, mapując ją na subskrypcję.

Aby można było utworzyć maszynę wirtualną z systemem CloudSimple na platformie Azure, w chmurze prywatnej vCenter musi istnieć szablon maszyny wirtualnej.  Szablon służy do dostosowywania systemu operacyjnego i aplikacji.  Szablon maszyny wirtualnej może być zaostrzony w celu spełnienia zasad zabezpieczeń przedsiębiorstwa.  Przy użyciu szablonu można tworzyć maszyny wirtualne, a następnie korzystać z nich z Azure Portal przy użyciu modelu samoobsługowego.

## <a name="benefits"></a>Korzyści

CloudSimple maszyny wirtualne z Azure Portal udostępniają mechanizm samoobsługowy do tworzenia maszyn wirtualnych VMware i zarządzania nimi.

* Tworzenie maszyny wirtualnej CloudSimple w chmurze prywatnej vCenter
* Zarządzanie właściwościami maszyny wirtualnej
  * Dodawanie/usuwanie dysków
  * Dodawanie/usuwanie kart sieciowych
* Operacje na mocy maszyny wirtualnej CloudSimple
  * Włącz i Wyłącz zasilanie
  * Resetuj maszynę wirtualną
* Usuń maszynę wirtualną

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [korzystać z maszyn wirtualnych VMware na platformie Azure](quickstart-create-vmware-virtual-machine.md)
* Dowiedz się, jak [mapować swoją subskrypcję platformy Azure](azure-subscription-mapping.md)
