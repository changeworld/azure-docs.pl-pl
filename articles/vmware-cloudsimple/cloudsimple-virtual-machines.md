---
title: Rozwiązanie VMware według CloudSimple — Omówienie usługi Azure Virtual Machines
description: Dowiedz się więcej na temat maszyn wirtualnych CloudSimple i ich korzyści.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0f4967bbd12107bf6a04cb80537d4425c75c5f46
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812491"
---
# <a name="cloudsimple-virtual-machines-overview"></a>CloudSimple maszyn wirtualnych — Omówienie

CloudSimple umożliwia zarządzanie maszynami wirtualnymi VMware z poziomu Azure Portal.  Klaster lub Pula zasobów z klastra vSphere jest zarządzana za pośrednictwem platformy Azure, mapując ją na subskrypcję.  CloudSimple maszyna wirtualna umożliwia Samoobsługowe zarządzanie maszynami wirtualnymi VMware z Azure Portal.  

Aby można było utworzyć maszynę wirtualną z systemem CloudSimple na platformie Azure, w chmurze prywatnej vCenter musi istnieć szablon maszyny wirtualnej.  Szablon służy do dostosowywania systemu operacyjnego i aplikacji.  Szablon maszyny wirtualnej może być zaostrzony w celu spełnienia zasad zabezpieczeń przedsiębiorstwa.  Za pomocą szablonu można tworzyć maszyny wirtualne i korzystać z nich z Azure Portal przy użyciu modelu samoobsługowego.

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

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [korzystać z maszyn wirtualnych VMware na platformie Azure](quickstart-create-vmware-virtual-machine.md)
* Dowiedz się, jak [mapować swoją subskrypcję platformy Azure](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)