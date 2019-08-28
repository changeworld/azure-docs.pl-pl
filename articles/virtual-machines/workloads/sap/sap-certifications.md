---
title: Microsoft Azure certyfikaty dla SAP | Microsoft Docs
description: Zaktualizowana lista bieżących konfiguracji i certyfikatów SAP na platformie Azure.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/15/2019
ms.author: rclaus
ms.custom: ''
ms.openlocfilehash: 38e63d8ca7d9db5247bd5fe07fbe59452a106cf6
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098665"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Certyfikaty i konfiguracje SAP działające w systemie Microsoft Azure

W przypadku oprogramowania SAP i Microsoft istnieje obszerna historia współpracy w silnym partnerstwie, które ma wzajemne korzyści dla klientów. Firma Microsoft stale aktualizuje swoją platformę i przesyła nowe szczegóły certyfikacji do oprogramowania SAP w celu zapewnienia, że Microsoft Azure jest najlepszą platformą, na której będą uruchamiane obciążenia SAP. W poniższych tabelach Zanotuj konfiguracje obsługiwane przez platformę Azure oraz listę rosnących certyfikatów SAP. 

## <a name="sap-hana-certifications"></a>Certyfikaty SAP HANA
Wołują

- [SAP HANA certyfikowane platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) na potrzeby SAP HANA obsługi natywnych maszyn wirtualnych platformy Azure i dużych wystąpień Hana.

| Produkt SAP | Obsługiwane systemy operacyjne | Oferty platformy Azure |
| --- | --- | --- |
| SAP HANA Developer Edition (w tym oprogramowanie klienckie HANA składające się z programu SQLODBC, ODBO-Windows, ODBC, JDBC Drivers, HANA Studio i HANA Database) | Red Hat Enterprise Linux, SUSE Linux Enterprise | Rodzina maszyn wirtualnych z serii D |
| Firma 1 na platformie HANA | SUSE Linux Enterprise | DS14_v2, M32ts, M32ls, M64ls, M64s <br /> [SAP HANA certyfikowane platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) |
| SAP S/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | Kontrolowana dostępność dla GS5. Pełna obsługa M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> SAP HANA na platformie Azure (duże wystąpienia) [SAP HANA certyfikowane platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| Pakiet dla oprogramowania HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, SAP HANA na platformie Azure (duże wystąpienia) <br /> [SAP HANA certyfikowane platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| Oprogramowanie HANA Enterprise dla rozwiązań BW, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> SAP HANA na platformie Azure (duże wystąpienia) [SAP HANA certyfikowane platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> Oprogramowanie SAP HANA na platformie Azure (duże wystąpienia) <br /> [SAP HANA certyfikowane platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |

Należy pamiętać, że SAP używa warunku "klastrowanie" w [SAP HANA certyfikowane platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) jako synonim dla "skalowalności w poziomie", a nie dla "klastrowania" o wysokiej dostępności

## <a name="sap-netweaver-certifications"></a>Certyfikaty na oprogramowanie SAP NetWeaver
System Microsoft Azure posiada certyfikaty dla następujących produktów SAP z pełnym wsparciem firm Microsoft i SAP.
Wołują

- [1928533 — aplikacje SAP na platformie Azure: Obsługiwane produkty i typy](https://launchpad.support.sap.com/#/notes/1928533) maszyn wirtualnych platformy Azure dla wszystkich aplikacji opartych na oprogramowaniu SAP NetWeaver, w tym SAP Trex, SAP LiveCache i SAP Content Server. I wszystkie bazy danych, z wyłączeniem SAP HANA.


| Produkt SAP | System operacyjny gościa | RDBMS | Rodzaje maszyn wirtualnych |
| --- | --- | --- | --- |
| SAP Business Suite Software | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (tylko systemy Windows i Oracle Linux), DB2, SAP ASE |A5 do A11, D11 do D14, DS11 do DS14, DS11_v2 do DS15_v2, GS1 do GS5, D2s_v3 do D64s_v3, E2s_v3 do E64s_v3, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2 |
| SAP Business All-in-One | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (tylko systemy Windows i Oracle Linux), DB2, SAP ASE |A5 do A11, D11 do D14, DS11 do DS14, DS11_v2 do DS15_v2, GS1 do GS5, D2s_v3 do D64s_v3, E2s_v3 do E64s_v3, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2 |
| SAP BusinessObjects BI | Windows |ND |A5 do A11, D11 do D14, DS11 do DS14, DS11_v2 do DS15_v2, GS1 do GS5, D2s_v3 do D64s_v3, E2s_v3 do E64s_v3, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2 |
| SAP NetWeaver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (tylko systemy Windows i Oracle Linux), DB2, SAP ASE |A5 do A11, D11 do D14, DS11 do DS14, DS11_v2 do DS15_v2, GS1 do GS5, D2s_v3 do D64s_v3, E2s_v3 do E64s_v3, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2 |

## <a name="other-sap-workload-supported-on-azure"></a>Inne obciążenie SAP obsługiwane na platformie Azure

| Produkt SAP | System operacyjny gościa | RDBMS | Rodzaje maszyn wirtualnych |
| --- | --- | --- | --- |
| Oprogramowanie SAP Business One on on SQL Server | Windows  | SQL Server | Wszystkie typy certyfikowanych maszyn wirtualnych NetWeaver<br /> [#928839 uwagi SAP](https://launchpad.support.sap.com/#/notes/928839) |
| SAP BPC 10.01 MS SP08 | System Windows i Linux | | Wszystkie typy certyfikowanych maszyn wirtualnych NetWeaver<br /> #2451795 uwagi SAP |
| Oprogramowanie SAP Business Objects BI platform | System Windows i Linux | | #2145537 uwagi SAP |
| Data Services SAP 4,2 | | | #2288344 uwagi SAP |
| SAP Hybris Commerce platform 5. x i 6. x | Windows | SQL Server, Oracle | Wszystkie typy certyfikowanych maszyn wirtualnych NetWeaver<br /> [Hybris wiki](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud) |
