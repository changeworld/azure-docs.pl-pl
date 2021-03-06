---
title: Certyfikaty platformy Microsoft Azure dla sap | Dokumenty firmy Microsoft
description: Zaktualizowano listę bieżących konfiguracji i certyfikatów sap na platformie Azure.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/13/2020
ms.author: juergent
ms.custom: ''
ms.openlocfilehash: 43fc2e9369b9d11b1ac0205beddea01b633fb633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598378"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Certyfikaty i konfiguracje SAP uruchomione na platformie Microsoft Azure

Sap i Microsoft mają długą historię współpracy w silnej współpracy, która ma wzajemne korzyści dla swoich klientów. Firma Microsoft stale aktualizuje swoją platformę i przesyła nowe szczegóły certyfikacji do firmy SAP, aby zapewnić, że platforma Microsoft Azure jest najlepszą platformą do uruchamiania obciążeń SAP. W poniższych tabelach opisano obsługiwane konfiguracje platformy Azure i listę rosnących certyfikatów SAP. 

## <a name="sap-hana-certifications"></a>Certyfikaty SAP HANA
Odwołania:

- [Platformy IaaS z certyfikatem SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) dla obsługi sap HANA dla natywnych maszyn wirtualnych platformy Azure i dużych wystąpień HANA.

| Produkt SAP | Obsługiwany system operacyjny | Oferty platformy Azure |
| --- | --- | --- |
| SAP HANA Developer Edition (w tym oprogramowanie klienckie HANA składające się tylko z SQLODBC, ODBO-Windows, sterowników ODBC, JDBC, hana studio i bazy danych HANA) | Red Hat Enterprise Linux, SUSE Linux Enterprise | Rodzina maszyn wirtualnych serii D |
| Business One na HANA | SUSE Linux Enterprise | DS14_v2, M32ts, M32ls, M64ls, M64s <br /> [Platformy IaaS z certyfikatem SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) |
| SAP S/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | Kontrolowana dostępność dla GS5. Pełne wsparcie dla M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2, <br /> Sap HANA na platformie Azure (duże wystąpienia) [Platformy IaaS z certyfikatem SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| Apartament na HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA na platformach Azure (duże wystąpienia) [platformy IaaS z certyfikatem SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| PRZEDSIĘBIORSTWO HANA dla BW, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA na platformach Azure (duże wystąpienia) [platformy IaaS z certyfikatem SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA na platformie Azure (duże wystąpienia) <br /> [Platformy IaaS z certyfikatem SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |

Należy pamiętać, że SAP używa terminu "klastrowanie" w [platformach IaaS z certyfikatem SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) jako synonim "skalowania w poziomie", a NIE dla wysokiej dostępności "klastrowania"

## <a name="sap-netweaver-certifications"></a>Certyfikaty SAP NetWeaver
Platforma Microsoft Azure jest certyfikowana dla następujących produktów SAP, z pełną obsługą firmy Microsoft i SAP.
Odwołania:

- [1928533 — aplikacje SAP na platformie Azure: obsługiwane produkty i typy maszyn wirtualnych platformy Azure](https://launchpad.support.sap.com/#/notes/1928533) dla wszystkich aplikacji opartych na SAP NetWeaver, w tym SAP TREX, SAP LiveCache i SAP Content Server. I wszystkie bazy danych, z wyłączeniem SAP HANA.


| Produkt SAP | System operacyjny gościa | RDBMS | Typy maszyn wirtualnych |
| --- | --- | --- | --- |
| Oprogramowanie SAP Business Suite | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (tylko Windows i Oracle Linux), DB2, SAP ASE |A5 do A11, D11 do D14, DS11 do DS14, DS11_v2 do DS15_v2, GS1 do GS5, D2s_v3 do D64s_v3, D2as_v4 do D64as_v4, E2s_v3 E64s_v3, E2as_v4 do E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2 , M208ms_v2, M416s_v2, M416ms_v2 |
| SAP Business All-in-One | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (tylko Windows i Oracle Linux), DB2, SAP ASE |A5 do A11, D11 do D14, DS11 do DS14, DS11_v2 do DS15_v2, GS1 do GS5, D2s_v3 do D64s_v3, D2as_v4 do D64as_v4, E2s_v3 E64s_v3, E2as_v4 do E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2 , M208ms_v2, M416s_v2, M416ms_v2 |
| SAP BusinessObjects BI | Windows |Nie dotyczy |A5 do A11, D11 do D14, DS11 do DS14, DS11_v2 do DS15_v2, GS1 do GS5, D2s_v3 do D64s_v3, D2as_v4 do D64as_v4, E2s_v3 E64s_v3, E2as_v4 do E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2 , M208ms_v2, M416s_v2, M416ms_v2 |
| SAP NetWeaver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (tylko Windows i Oracle Linux), DB2, SAP ASE |A5 do A11, D11 do D14, DS11 do DS14, DS11_v2 do DS15_v2, GS1 do GS5, D2s_v3 do D64s_v3, D2as_v4 do D64as_v4, E2s_v3 E64s_v3, E2as_v4 do E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2 , M208ms_v2, M416s_v2, M416ms_v2 |

## <a name="other-sap-workload-supported-on-azure"></a>Inne obciążenie SAP obsługiwane na platformie Azure

| Produkt SAP | System operacyjny gościa | RDBMS | Typy maszyn wirtualnych |
| --- | --- | --- | --- |
| SAP Business One na serwerze SQL Server | Windows  | SQL Server | Wszystkie typy maszyn wirtualnych z certyfikatem NetWeaver<br /> [#928839 uwagi SAP](https://launchpad.support.sap.com/#/notes/928839) |
| SAP BPC 10.01 MS SP08 | System Windows i Linux | | Wszystkie typy maszyn wirtualnych z certyfikatem NetWeaver<br /> #2451795 uwaga SAP |
| Platforma BI obiektów biznesowych SAP | System Windows i Linux | | #2145537 uwaga SAP |
| Usługi danych SAP 4.2 | | | #2288344 uwagi SAP |
| Platforma SAP Hybris Commerce 5.x i 6.x | Windows | SQL Server, Oracle | Wszystkie typy maszyn wirtualnych z certyfikatem NetWeaver<br /> [Hybris Wiki](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud) |
