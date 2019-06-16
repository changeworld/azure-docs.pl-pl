---
title: Dostępność platformy SAP HANA na maszynach wirtualnych platformy Azure — omówienie | Dokumentacja firmy Microsoft
description: W tym artykule opisano operacje platformy SAP HANA na natywnych maszynach wirtualnych platformy Azure.
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
ms.openlocfilehash: 1db56ad31991b85ffad415818c7c67f0ee30808d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60708451"
---
# <a name="sap-hana-high-availability-for-azure-virtual-machines"></a>SAP HANA wysoką dostępność dla maszyn wirtualnych platformy Azure

Liczne możliwości platformy Azure można użyć do wdrożenia o kluczowym znaczeniu baz danych, takich jak SAP HANA na maszynach wirtualnych platformy Azure. Ten artykuł zawiera wskazówki dotyczące sposobu uzyskania dostępności dla wystąpień oprogramowania SAP HANA, które są hostowane na maszynach wirtualnych Azure. W artykule opisano kilka scenariuszy, które można zaimplementować przy użyciu infrastruktury platformy Azure, aby zwiększyć dostępność środowiska SAP HANA na platformie Azure. 

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że znasz infrastruktury jako usługi (IaaS) podstawowe informacje o platformie Azure, w tym: 

- Jak wdrożyć maszyny wirtualne lub sieci wirtualnych za pośrednictwem witryny Azure portal lub programu PowerShell.
- Przy użyciu platformy Azure interfejsu wiersza polecenia dla wielu platform (interfejs wiersza polecenia platformy Azure), w tym możliwość użycia szablonów JavaScript Object Notation (JSON).

W tym artykule założono również, że czytelnik zna przy instalowaniu wystąpieniami platformy SAP HANA oraz administrowania i działających wystąpień oprogramowania SAP HANA. Jest to szczególnie ważne, należy zapoznać się z instalacją i operacje replikacji systemu HANA. Dotyczy to również zadania, takie jak Kopia zapasowa i przywracanie baz danych SAP HANA.

Te artykuły zawierają omówienie przy użyciu oprogramowania SAP HANA na platformie Azure:

- [Ręczna instalacja jednego wystąpienia oprogramowania SAP HANA na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Konfigurowanie replikacji systemu SAP HANA na maszynach wirtualnych Azure](sap-hana-high-availability.md)
- [Tworzenie kopii zapasowych oprogramowania SAP HANA na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)

Jest również dobrym pomysłem jest należy zapoznać się z tych artykułów na temat oprogramowania SAP HANA:

- [Wysoka dostępność dla oprogramowania SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)
- [Często zadawane pytania: Wysoka dostępność dla oprogramowania SAP HANA](https://archive.sap.com/documents/docs/DOC-66702)
- [Przeprowadzenia replikacji systemu SAP HANA](https://archive.sap.com/documents/docs/DOC-47702)
- [SAP HANA w wersji 2.0 SPS 01 co firmy nowe: Wysoka dostępność](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/)
- [Zalecenia dotyczące sieci dla replikacji systemu SAP HANA](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)
- [Replikacja systemu SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
- [SAP HANA usługi automatycznego uruchamiania ponownego dla zaplanowanych](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)
- [Konfigurowanie replikacji systemu SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html)

Poza jest zaznajomiony z wdrażania maszyn wirtualnych na platformie Azure, przed zdefiniowaniem architektury dostępności na platformie Azure, zalecamy przeczytanie [Zarządzanie dostępnością maszyn wirtualnych Windows na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability).

## <a name="service-level-agreements-for-azure-components"></a>Umowy dotyczące poziomu usług dla składników platformy Azure

Platforma Azure oferuje różne umów SLA dotyczących dostępności dla różnych składników, takich jak sieć, Magazyn i maszyny wirtualne. Wszystkie umowy SLA są udokumentowane. Aby uzyskać więcej informacji, zobacz [umowy dotyczące poziomu usług Microsoft Azure](https://azure.microsoft.com/support/legal/sla/). 

[Umowa SLA dla maszyn wirtualnych](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) opisano dwie różne umowy SLA dla dwóch różnych konfiguracji:

- Pojedyncza maszyna wirtualna, która używa [Azure — wersja premium SSD](../../windows/disks-types.md) dla dysku systemu operacyjnego i wszystkie dyski z danymi. Ta opcja zapewnia miesięcznego czasu działania gwarantującą dostępność na poziomie 99,9%.
- Wiele (co najmniej dwa) maszyn wirtualnych, które są zorganizowane w [zestawu dostępności platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Ta opcja zapewnia miesięcznego czasu dostępności wynoszącej 99,95 procent.

Zmierz swoje wymagania dostępności względem umów SLA, które udostępniają składniki platformy Azure. Następnie wybierz scenariuszy dla oprogramowania SAP HANA osiągnąć swoje wymagany poziom dostępności.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [dostępności platformy SAP HANA w ramach jednego regionu platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region).
- Dowiedz się więcej o [dostępności platformy SAP HANA w różnych regionach platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions). 















  


