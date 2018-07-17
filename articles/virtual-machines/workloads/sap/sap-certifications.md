---
title: Certyfikaty programu Microsoft Azure dla rozwiązania SAP | Dokumentacja firmy Microsoft
description: Zaktualizowaną listę bieżącej konfiguracji i certyfikacje SAP na platformie Azure.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/12/2018
ms.author: rclaus
ms.custom: ''
ms.openlocfilehash: d1d2bd74a5118b48f7ed79a77ca2d7b6545607c6
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39072384"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Certyfikacje SAP i konfiguracji, w systemie Microsoft Azure

Firmy SAP i Microsoft ma dawna współpracujących silne partnerstwa, dzięki któremu zapewnia wzajemnego korzyści swoim klientom. Firma Microsoft stale aktualizuje swoją platformę i przesyłanie nowe szczegóły certyfikacji SAP w celu zapewnienia Microsoft Azure to najlepsza platforma, na którym do uruchamiania obciążeń SAP. Następujące konspektu tabel platformy Azure obsługiwane konfiguracje i lista rośnie certyfikacje SAP. 

## <a name="sap-hana-certifications"></a>Certyfikaty SAP HANA
Odwołania:

- [Platform IaaS z certyfikatem SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) obsługi oprogramowania SAP HANA na natywnych maszynach wirtualnych platformy Azure i dużych wystąpień HANA.

| Produkt SAP | Obsługiwane systemy operacyjne | Oferty platformy Azure |
| --- | --- | --- |
| SAP HANA Developer Edition (w tym oprogramowanie klienckie HANA składające się z SQLODBC, ODBO — Windows, ODBC i sterowników JDBC, HANA studio i HANA database) | Red Hat Enterprise Linux, SUSE Linux Enterprise | Rodziny maszyn wirtualnych z serii D |
| Jeden HANA w biznesowych | SUSE Linux Enterprise | DS14_v2 |
| OPROGRAMOWANIE SAP S/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | Kontrolowana dostępność dla maszyn wirtualnych GS5, M64s, M64ms, M128s M128ms, SAP HANA na platformie Azure (duże wystąpienia) |
| Pakiet dla oprogramowania HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5 dla scenariuszy nieprodukcyjnych, M64s M64ms, M128s, M128ms, SAP HANA na platformie Azure (duże wystąpienia) |
| Oprogramowanie HANA Enterprise dla rozwiązań BW, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s M128ms, SAP HANA na platformie Azure (duże wystąpienia) |
| OPROGRAMOWANIE SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s M128ms, SAP HANA na platformie Azure (duże wystąpienia) |

Wszystkie maszyny wirtualne platformy Azure są certyfikowane dla oprogramowania SAP HANA skalowanie w górę do tej pory.

## <a name="sap-netweaver-certifications"></a>Certyfikaty na oprogramowanie SAP NetWeaver
System Microsoft Azure posiada certyfikaty dla następujących produktów SAP z pełnym wsparciem firm Microsoft i SAP.
Odwołania:

- [1928533 — SAP aplikacje na platformie Azure: obsługiwane produkty i maszyny Wirtualnej platformy Azure typy](https://launchpad.support.sap.com/#/notes/1928533) dla wszystkich aplikacji SAP NetWeaver na podstawie, m.in. SAP TREX SAP LiveCache i SAP, serwer zawartości. I wszystkimi bazami danych, z wyjątkiem platformy SAP HANA.


| Produkt SAP | System operacyjny gościa | RDBMS | Rodzaje maszyn wirtualnych |
| --- | --- | --- | --- |
| SAP Business Suite Software | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |Program SQL Server, Oracle (Windows i Oracle Linux), DB2, SAP ASE |A5 do A11, D11 do D14, DS11 do DS14, DS11_v2 do DS15_v2, GS1 do GS5, D2s_v3 do D64s_v3, E2s_v3 do E64s_v3, M64s do M128ms |
| SAP Business All-in-One | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |Program SQL Server, Oracle (Windows i Oracle Linux), DB2, SAP ASE |A5 do A11, D11 do D14, DS11 do DS14, DS11_v2 do DS15_v2, GS1 do GS5, D2s_v3 do D64s_v3, E2s_v3 do E64s_v3, M64s do M128ms |
| SAP BusinessObjects BI | Windows |ND |A5 do A11, D11 do D14, DS11 do DS14, DS11_v2 do DS15_v2, GS1 do GS5, D2s_v3 do D64s_v3, E2s_v3 do E64s_v3, M64s do M128ms |
| SAP NetWeaver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |Program SQL Server, Oracle (Windows i Oracle Linux), DB2, SAP ASE |A5 do A11, D11 do D14, DS11 do DS14, DS11_v2 do DS15_v2, GS1 do GS5, D2s_v3 do D64s_v3, E2s_v3 do E64s_v3, M64s do M128ms |

## <a name="other-sap-workload-supported-on-azure"></a>Inne obciążenia SAP, obsługiwane na platformie Azure

| Produkt SAP | System operacyjny gościa | RDBMS | Rodzaje maszyn wirtualnych |
| --- | --- | --- | --- |
| SAP Business jeden włączony serwer SQL Server | Windows  | Oprogramowanie SQL Server | Wszystkie NetWeaver certyfikowane typy maszyn wirtualnych<br /> [Uwaga SAP #928839](https://launchpad.support.sap.com/#/notes/928839) |
| SAP BPC 10.01 MS SP08 | System Windows i Linux | | Wszystkie typy NetWeaver certyfikowane w maszynie Wirtualnej<br /> Uwaga SAP #2451795 |
| SAP Business obiektów BI platformy | System Windows i Linux | | Uwaga SAP #2145537 |
| Usługi danych SAP 4.2 | | | Uwaga SAP #2288344 |
| SAP Hybris Commerce Platform 5.x i 6.x | Windows | SQL Server, Oracle | Wszystkie NetWeaver certyfikowane typy maszyn wirtualnych<br /> [Hybris witryny typu Wiki](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud) |
