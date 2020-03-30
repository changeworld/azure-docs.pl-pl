---
title: Dostępność SAP HANA na maszynach wirtualnych platformy Azure — omówienie | Dokumenty firmy Microsoft
description: Opisuje operacje SAP HANA na natywnych maszynach wirtualnych platformy Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bccaf45cf617bd31a584b6c73f3dd08877bc8587
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71266072"
---
# <a name="sap-hana-high-availability-for-azure-virtual-machines"></a>Wysoka dostępność sap hana dla maszyn wirtualnych platformy Azure

Można użyć wielu możliwości platformy Azure do wdrażania baz danych o znaczeniu krytycznym, takich jak SAP HANA na maszynach wirtualnych platformy Azure. Ten artykuł zawiera wskazówki dotyczące sposobu osiągnięcia dostępności dla wystąpień SAP HANA, które są hostowane na maszynach wirtualnych platformy Azure. W tym artykule opisano kilka scenariuszy, które można zaimplementować przy użyciu infrastruktury platformy Azure w celu zwiększenia dostępności sap HANA na platformie Azure. 

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że znasz podstawy infrastruktury jako usługi (IaaS) na platformie Azure, w tym: 

- Jak wdrożyć maszyny wirtualne lub sieci wirtualne za pośrednictwem witryny Azure portal lub programu PowerShell.
- Korzystanie z interfejsu wiersza polecenia platformy Azure (Azure CLI), w tym opcji używania szablonów notacji obiektów javascript (JSON).

W tym artykule przyjęto również założenie, że użytkownik jest zaznajomiony z instalowaniem wystąpień SAP HANA oraz z administrowaniem i obsługiwaniem wystąpień SAP HANA. Szczególnie ważne jest, aby zapoznać się z konfiguracją i operacjami replikacji systemu HANA. Obejmuje to zadania, takie jak tworzenie kopii zapasowych i przywracanie baz danych SAP HANA.

Te artykuły zawierają dobre omówienie korzystania z sap HANA na platformie Azure:

- [Ręczna instalacja systemu SAP HANA jednomandatowego na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Konfigurowanie replikacji systemu SAP HANA na maszynach wirtualnych platformy Azure](sap-hana-high-availability.md)
- [Tworzenie kopii zapasowych platformy SAP HANA na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)

Jest to również dobry pomysł, aby zapoznać się z tych artykułów na temat SAP HANA:

- [Wysoka dostępność dla SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)
- [Często zadawane pytania: Wysoka dostępność dla SAP HANA](https://archive.sap.com/documents/docs/DOC-66702)
- [Wykonywanie replikacji systemu dla systemu SAP HANA](https://archive.sap.com/documents/docs/DOC-47702)
- [SAP HANA 2.0 SPS 01 Co nowego: Wysoka dostępność](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/)
- [Zalecenia sieciowe dotyczące replikacji systemu SAP HANA](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)
- [Replikacja systemu SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
- [Automatyczne ponowne uruchamianie usługi SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)
- [Konfigurowanie replikacji systemu SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html)

Oprócz zapoznania się z wdrażaniem maszyn wirtualnych na platformie Azure, przed zdefiniowaniem architektury dostępności na platformie Azure, zalecamy [przeczytanie polecenia Zarządzanie dostępnością maszyn wirtualnych z systemem Windows na platformie Azure.](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)

## <a name="service-level-agreements-for-azure-components"></a>Umowy dotyczące poziomu usług dla składników platformy Azure

Platforma Azure ma różne łasce dostępności dla różnych składników, takich jak sieci, magazynu i maszyn wirtualnych. Wszystkie ujednolice są udokumentowane. Aby uzyskać więcej informacji, zobacz [Umowy dotyczące poziomu usług platformy Microsoft Azure](https://azure.microsoft.com/support/legal/sla/). 

[La dla maszyn wirtualnych](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) opisuje trzy różne umowy SLA dla trzech różnych konfiguracji:

- Pojedyncza maszyna wirtualna, która używa [dysków SSD w wersji Premium platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) dla dysku systemu operacyjnego i wszystkich dysków z danymi. Ta opcja zapewnia miesięczny czas pracy na 99,9 procent.
- Wiele (co najmniej dwóch) maszyn wirtualnych, które są zorganizowane w [zestawie dostępności platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Ta opcja zapewnia miesięczny czas pracy na 99,95 procent.
- Wiele (co najmniej dwóch) maszyn wirtualnych, które są zorganizowane w [strefie dostępności](https://docs.microsoft.com/azure/availability-zones/az-overview). Ta opcja zapewniała miesięczny czas pracy na 99,99 procent.

Zmierz wymagania dotyczące dostępności względem instaltów SLA, które mogą zapewnić składniki platformy Azure. Następnie wybierz scenariusze dla sap HANA, aby osiągnąć wymagany poziom dostępności.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [dostępności SAP HANA w jednym regionie platformy Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- Dowiedz się więcej o [dostępności SAP HANA w regionach platformy Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 















  


