---
title: VMware Solution by CloudSimple — omówienie maszyn wirtualnych platformy Azure
description: Więcej informacji na temat CloudSimple maszyn wirtualnych i ich zalet.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 77a2901ae2d81f42780110b1576c1f32c7ff397b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65209486"
---
# <a name="cloudsimple-virtual-machines-overview"></a>CloudSimple maszyn wirtualnych — omówienie

CloudSimple umożliwia zarządzanie maszynami wirtualnymi VMware w witrynie Azure portal.  Klastra lub pulę zasobów w klastrze vSphere jest zarządzana za pośrednictwem platformy Azure przez mapowania ich na subskrypcję.  Maszyna wirtualna CloudSimple oferuje Samoobsługowe zarządzanie maszyn wirtualnych programu VMware w witrynie Azure portal.  

Aby utworzyć Maszynę wirtualną CloudSimple z platformy Azure, szablonu maszyny Wirtualnej musi istnieć na vCenter, Chmura prywatna.  Szablon jest używany do dostosowywania systemu operacyjnego i aplikacji.  Szablon maszyny Wirtualnej można trybie zaostrzonym, aby spełnić zasady bezpieczeństwa w przedsiębiorstwie.  Ten szablon umożliwia tworzenie maszyn wirtualnych i używaj ich w witrynie Azure portal przy użyciu modelu samoobsługi.

## <a name="benefits"></a>Korzyści

CloudSimple maszyny wirtualne z witryny Azure portal mechanizm samoobsługowy umożliwiający użytkownikom tworzenie i zarządzanie maszynami wirtualnymi VMware.

* Utwórz Maszynę wirtualną CloudSimple na vCenter, Chmura prywatna
* Zarządzanie właściwościami maszyny Wirtualnej
  * Dodaj/Usuń dyski
  * Dodaj/Usuń karty sieciowe
* Potęgowania CloudSimple maszyny wirtualnej
  * Włączenia i wyłączenia zasilania
  * Resetowanie maszyny Wirtualnej
* Usuwanie maszyny Wirtualnej

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [używanie maszyn wirtualnych VMware na platformie Azure](quickstart-create-vmware-virtual-machine.md)
* Dowiedz się, jak [mapy subskrypcji platformy Azure](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)