---
title: Certyfikaty programu Microsoft Azure dla programu SAP | Dokumentacja firmy Microsoft
description: Zaktualizowaną listę bieżącej konfiguracji i certyfikatów SAP na platformie Azure.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/12/2017
ms.author: rclaus
ms.custom: ''
ms.openlocfilehash: 96a3b6f8f8a03019f0446aa7ba42f53298e21009
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Certyfikaty SAP i konfiguracje uruchomiony w systemie Microsoft Azure

SAP i Microsoft już od długiego czasu pracy ze sobą silne partnerskie mającej wzajemne korzyści klientom. Microsoft stale aktualizuje platformy i przesyłanie nowych szczegółów certyfikacji SAP w celu zapewnienia Microsoft Azure jest najlepszą platformą, na którym ma być uruchamiany obciążeń SAP. Poniższe tabele przedstawiają naszych obsługiwanych konfiguracji i lista rośnie certyfikaty. 

## <a name="sap-hana-certifications"></a>Certyfikaty SAP HANA
Odwołania:

- [Uwaga SAP 2316233 - SAP HANA w systemie Microsoft Azure (wystąpienia duże)](https://launchpad.support.sap.com/#/notes/2316233) obejmujące wystąpień dużych HANA dotyczące pomocy technicznej SAP HANA.
- [SAP HANA certyfikowane platform IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Amazon%20Web%20Services%2CMicrosoft%20Azure) dla SAP HANA obsługę macierzysty maszynach wirtualnych platformy Azure.

| Produkt SAP | Obsługiwane systemy operacyjne | Oferty platformy Azure |
| --- | --- | --- |
| SAP HANA Developer Edition (włącznie z oprogramowaniem klienta HANA obejmuje SQLODBC, ODBO — Windows, ODBC, sterowniki JDBC, HANA studio i HANA bazy danych) | Red Hat Enterprise Linux, SUSE Linux Enterprise | Maszyna wirtualna D-Series rodziny |
| Jeden na HANA biznesowa | SUSE Linux Enterprise | DS14_v2 |
| SAP HANA S/4 |Red Hat Enterprise Linux, SUSE Linux Enterprise | Dostępność kontrolowane GS5, SAP HANA na platformie Azure (wystąpienia duże) |
| Pakiet dla oprogramowania HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5 wdrożeń jednego węzła w scenariuszach nieprodukcyjnych SAP HANA na platformie Azure (wystąpienia duże) |
| Oprogramowanie HANA Enterprise dla rozwiązań BW, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5 wdrożeń jednowęzłowy SAP HANA na platformie Azure (wystąpienia duże) |
| SAP HANA BW/4 | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5 wdrożeń jednowęzłowy SAP HANA na platformie Azure (wystąpienia duże) |

## <a name="sap-netweaver-certifications"></a>Certyfikaty na oprogramowanie SAP NetWeaver
System Microsoft Azure posiada certyfikaty dla następujących produktów SAP z pełnym wsparciem firm Microsoft i SAP.
Odwołania:

- [1928533 — aplikacje SAP na platformie Azure: typy obsługiwanych produktów i maszyny Wirtualnej Azure](https://launchpad.support.sap.com/#/notes/1928533) wszystkie aplikacje SAP NetWeaver na podstawie, w tym SAP TREX, SAP LiveCache i serwer zawartości SAP. I wszystkie bazy danych, z wyłączeniem SAP HANA.


| Produkt SAP | System operacyjny gościa | RDBMS | Rodzaje maszyn wirtualnych |
| --- | --- | --- | --- |
| SAP Business Suite Software |Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |Program SQL Server, Oracle (z systemem Windows i Oracle Linux), bazy danych DB2, SAP ASE |A5 do A11, D11 do D14, DS11 do DS14, DS11_v2 do DS15_v2, GS1 do GS5, D2s_v3 do D64s_v3, E2s_v3 do E64s_v3, M64s do M128ms |
| SAP Business All-in-One |Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux |Program SQL Server, Oracle (z systemem Windows i Oracle Linux), bazy danych DB2, SAP ASE |A5 do A11, D11 do D14, DS11 do DS14, DS11_v2 do DS15_v2, GS1 do GS5, D2s_v3 do D64s_v3, E2s_v3 do E64s_v3, M64s do M128ms |
| SAP BusinessObjects BI |Windows |ND |A5 do A11, D11 do D14, DS11 do DS14, DS11_v2 do DS15_v2, GS1 do GS5, D2s_v3 do D64s_v3, E2s_v3 do E64s_v3, M64s do M128ms |
| SAP NetWeaver |Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux |Program SQL Server, Oracle (z systemem Windows i Oracle Linux), bazy danych DB2, SAP ASE |A5 do A11, D11 do D14, DS11 do DS14, DS11_v2 do DS15_v2, GS1 do GS5, D2s_v3 do D64s_v3, E2s_v3 do E64s_v3, M64s do M128ms |

## <a name="other-sap-workload-supported-on-azure"></a>Inne obciążenia SAP obsługiwane na platformie Azure

| Produkt SAP | System operacyjny gościa | RDBMS | Rodzaje maszyn wirtualnych |
| --- | --- | --- | --- |
| SAP Business jeden na serwer SQL Server | Windows  | Oprogramowanie SQL Server | Wszystkie NetWeaver certyfikowane typów maszyny Wirtualnej |
| SAP BPC 10.01 MS SP08 | Windows | | Wszystkie typy NetWeaver certyfikowane VM<br /> Uwaga SAP #2451795 |
| SAP Business obiektów BI platformy | Windows | | Uwaga SAP #2145537 |
| Usługi danych SAP 4.2 | | | Uwaga SAP #2288344 |
| Platforma handlowa Hybris SAP 5.x i 6.x | Windows | SQL Server, Oracle | Wszystkie NetWeaver certyfikowane typów maszyny Wirtualnej<br /> [Witryna typu Hybris Wiki](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud) |
