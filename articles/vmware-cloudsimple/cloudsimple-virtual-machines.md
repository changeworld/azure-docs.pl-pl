---
title: Omówienie maszyn wirtualnych
titleSuffix: Azure VMware Solution by CloudSimple
description: Dowiedz się więcej o maszynach wirtualnych CloudSimple i ich zaletach.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 436a5ab6fb286ce206f981487b6a29d774ecd1a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024912"
---
# <a name="cloudsimple-virtual-machines-overview"></a>Omówienie maszyn wirtualnych CloudSimple

CloudSimple umożliwia zarządzanie maszynami wirtualnymi VMware (VMs) z witryny Azure portal.  Klaster lub pula zasobów z klastra vSphere jest zarządzana za pośrednictwem platformy Azure, mapując go do subskrypcji.

Aby utworzyć maszynę wirtualną CloudSimple z platformy Azure, szablon maszyny Wirtualnej musi istnieć w centrum wirtualnym private cloud.  Szablon służy do dostosowywania systemu operacyjnego i aplikacji.  Maszyna wirtualna szablonu może zostać wzmocniona w celu spełnienia zasad zabezpieczeń przedsiębiorstwa.  Szablonu można użyć do tworzenia maszyn wirtualnych, a następnie zużywają je z witryny Azure Portal przy użyciu modelu samoobsługi.

## <a name="benefits"></a>Korzyści

Maszyny wirtualne CloudSimple z witryny Azure portal zapewniają użytkownikom mechanizm samoobsługi do tworzenia maszyn wirtualnych VMware i zarządzania nimi.

* Tworzenie maszyny Wirtualnej CloudSimple w centrum vCenter private cloud
* Zarządzanie właściwościami maszyny Wirtualnej
  * Dodawanie/usuwanie dysków
  * Dodawanie/usuwanie kart sieciowych
* Operacje zasilania maszyny Wirtualnej CloudSimple
  * Włączanie i wyłączanie zasilania
  * Resetowanie maszyny wirtualnej
* Usuwanie maszyny wirtualnej

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [korzystać z maszyn wirtualnych VMware na platformie Azure](quickstart-create-vmware-virtual-machine.md)
* Dowiedz się, jak [zamapować subskrypcję platformy Azure](azure-subscription-mapping.md)
