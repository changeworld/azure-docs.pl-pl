---
title: Dostępność SAP HANA na maszynach wirtualnych Azure — omówienie | Dokumentacja firmy Microsoft
description: W tym artykule opisano SAP HANA operacje na maszynach wirtualnych Azure macierzystego.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7049a4b5159687ab928cda7ddc6b1a35959529ac
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32187161"
---
# <a name="sap-hana-high-availability-for-azure-virtual-machines"></a>SAP HANA wysokiej dostępności dla maszyn wirtualnych platformy Azure

Wiele możliwości Azure umożliwia wdrażanie krytycznym baz danych, takie jak SAP HANA na maszynach wirtualnych Azure. Ten artykuł zawiera wskazówki dotyczące sposobu uzyskania dostępności SAP HANA wystąpień, które są obsługiwane w maszynach wirtualnych platformy Azure. W artykule opisano kilka scenariuszy, które można implementować przy użyciu infrastruktury platformy Azure, aby zwiększyć dostępność SAP HANA na platformie Azure. 

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że znasz infrastruktura jako usługa (IaaS) podstawowe informacje o platformie Azure, w tym: 

- Jak wdrożyć maszyny wirtualne lub sieci wirtualnych za pośrednictwem portalu Azure lub programu PowerShell.
- Przy użyciu platformy Azure i platform interfejsu wiersza polecenia (Azure CLI), w tym możliwość użycia szablonów JavaScript Object Notation (JSON).

W tym artykule zakłada również, że znasz z instalowaniem SAP HANA wystąpień oraz administrowania i działających wystąpień SAP HANA. Jest to szczególnie ważne, należy zapoznać się z instalacją i operacje HANA replikacji systemu. Dotyczy to również zadania, takie jak Kopia zapasowa i przywracanie baz danych SAP HANA.

Te artykuły zawierają omówienie na platformie Azure przy użyciu SAP HANA:

- [Instalacja ręczna pojedynczym wystąpieniem SAP HANA na maszynach wirtualnych Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Konfigurowanie replikacji systemu SAP HANA w maszynach wirtualnych platformy Azure](sap-hana-high-availability.md)
- [Wykonaj kopię zapasową SAP HANA na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)

Jest również dobrym pomysłem jest należy zapoznać się z tych artykułów na temat SAP HANA:

- [Wysoka dostępność dla programu SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)
- [— Często zadawane pytania: Wysoką dostępność SAP HANA](https://archive.sap.com/documents/docs/DOC-66702)
- [Przeprowadzenia replikacji systemu SAP HANA](https://archive.sap.com/documents/docs/DOC-47702)
- [SAP HANA 2.0 SPS 01 co w nowych: wysokiej dostępności](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/)
- [Zalecenia dotyczące sieci dla replikacji systemu SAP HANA](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)
- [Replikacji systemu SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
- [SAP HANA usługi automatycznego ponownego uruchamiania](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)
- [Konfigurowanie replikacji systemu SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html)

Poza trwa zapoznać się z wdrożeniem maszyn wirtualnych na platformie Azure, przed zdefiniowaniem architektury dostępności na platformie Azure, zaleca się przeczytanie [Zarządzaj dostępnością maszyn wirtualnych systemu Windows na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability).

## <a name="service-level-agreements-for-azure-components"></a>Umowy dotyczące poziomu usług dla składników platformy Azure

Platforma Azure ma inną dostępności umowy SLA dla różnych składników, takich jak sieci, magazynu i maszyn wirtualnych. Wszystkie umowy SLA są udokumentowane. Aby uzyskać więcej informacji, zobacz [umowy dotyczące poziomu usług Microsoft Azure](https://azure.microsoft.com/support/legal/sla/). 

[Umowa SLA dla maszyn wirtualnych](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) opisano dwie różne umowy SLA dla dwóch różnych konfiguracji:

- Jednej maszyny Wirtualnej używającej [Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage) dla dysku systemu operacyjnego i wszystkich dysków danych. Ta opcja umożliwia miesięczny czas bez przestoju 99,9%.
- Wiele (co najmniej dwa) maszyn wirtualnych, które są zorganizowane w [zestawu dostępności Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Ta opcja umożliwia miesięczny czas bez przestoju 99,95%.

Zmierz wymagań dostępności względem umów SLA, które udostępniają składniki platformy Azure. Następnie wybierz scenariuszy dla SAP HANA do osiągnięcia wymagany poziom dostępności.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [SAP HANA dostępności w ramach jednego regionu Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region).
- Dowiedz się więcej o [SAP HANA dostępność w regionach platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions). 















  


