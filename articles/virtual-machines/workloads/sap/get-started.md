---
title: Wprowadzenie do oprogramowania SAP na maszynach wirtualnych platformy Azure | Microsoft Docs
description: Dowiedz się więcej o rozwiązaniach SAP uruchamianych na maszynach wirtualnych w Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/24/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8f4352a1f6a065d1e9be78f01bde95c8f00593a3
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479769"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Korzystanie z platformy Azure do hostowania i uruchamiania scenariuszy obciążeń SAP

W przypadku korzystania z Microsoft Azure można w niezawodny sposób uruchamiać obciążenia i scenariusze o kluczowym znaczeniu SAP w skalowalnej, zgodnej i korporacyjnej platformie. Uzyskasz skalowalność, elastyczność i oszczędność kosztów platformy Azure. Dzięki rozbudowanym partnerstwu między firmą Microsoft i SAP można uruchamiać aplikacje SAP w ramach scenariuszy deweloperskich i testowych oraz produkcyjnych na platformie Azure i być w pełni obsługiwane. W przypadku oprogramowania SAP NetWeaver do SAP S/4HANA, SAP BI w systemie Linux do systemu Windows i SAP HANA do bazy danych SQL.

Oprócz hostowania scenariuszy SAP NetWeaver z różnymi systemami DBMS na platformie Azure, można hostować inne scenariusze obciążeń SAP, takie jak SAP BI na platformie Azure. 

Unikatowość platformy Azure dla SAP HANA to oferta, która umożliwia rozróżnianie platformy Azure. Aby umożliwić hostowanie większej ilości pamięci i scenariuszy SAP wymagających zasobów procesora, które obejmują SAP HANA, platforma Azure umożliwia korzystanie z sprzętu bez systemu operacyjnego przeznaczonego dla klientów. To rozwiązanie służy do uruchamiania wdrożeń SAP HANA, które wymagają do 24 TB do skalowania w poziomie (120-TB) pamięci dla usługi S/4HANA lub innych obciążeń SAP HANA. 

Scenariusze obsługi obciążeń SAP na platformie Azure mogą również stworzyć wymagania integracji tożsamości i logowania jednokrotnego. Taka sytuacja może wystąpić w przypadku korzystania z Azure Active Directory (Azure AD) w celu łączenia różnych składników SAP oraz ofert oprogramowania SAP jako usługi (SaaS) lub platformy jako usługi (PaaS). Lista takich integracji i scenariuszy logowania jednokrotnego za pomocą usługi Azure AD i jednostek SAP została opisana i udokumentowana w sekcji "Integracja tożsamości i logowanie jednokrotne w usłudze AAD SAP".

## <a name="change-log"></a>Dziennik zmian

- Zmiany w [klastrze wystąpienie SAP ASCS/SCS w klastrze trybu failover systemu Windows przy użyciu udziału plików na platformie Azure](sap-high-availability-guide-wsfc-file-share.md) w celu odzwierciedlenia obsługi miejsca do magazynowania bezpośrednio przez usługi Azure Site Recovery
- Wersja [grup umieszczania bliskości platformy Azure w celu uzyskania optymalnego opóźnienia sieci przy użyciu aplikacji SAP](sap-proximity-placement-scenarios.md)
- Wydanie nowego przewodnika dla programu [IBM DB2 HADR Cluster na platformie Red Hat Enterprise Server](high-availability-guide-rhel-ibm-db2-luw.md)
- Wydanie [wysokiej dostępności dla oprogramowania SAP NetWeaver na Red Hat Enterprise Linux z Azure NetApp Files dla aplikacji SAP](high-availability-guide-rhel-netapp-files.md)
- Wprowadzenie ExpressRoute szybkiej ścieżki i Global Reach dla dużych wystąpień platformy HANA w [SAP HANA (duże wystąpienia) Architektura sieci](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture) i powiązane dokumenty
- [Kontrola dużych wystąpień usługi Azure Hana za pomocą Azure Portal](hana-li-portal.md)
- Wydanie [wysokiej dostępności dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server z Azure NetApp Files dla aplikacji SAP](high-availability-guide-suse-netapp-files.md)







## <a name="sap-hana-on-azure-large-instances"></a>Oprogramowanie SAP HANA na platformie Azure (duże wystąpienia)

Seria dokumentów prowadzi użytkownika przez SAP HANA na platformie Azure (duże wystąpienia) lub w przypadku krótkich dużych wystąpień usługi HANA. Aby uzyskać informacje dotyczące następujących obszarów dużych wystąpień usługi HANA, zobacz:

- [Omówienie SAP HANA na platformie Azure (duże wystąpienia)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Architektura SAP HANA na platformie Azure (duże wystąpienia)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [Infrastruktura i łączność do SAP HANA na platformie Azure (duże wystąpienia)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [Instalowanie SAP HANA na platformie Azure (duże wystąpienia)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [Wysoka dostępność i odzyskiwanie po awarii dla SAP HANA na platformie Azure (duże wystąpienia)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [Rozwiązywanie problemów i monitorowanie SAP HANA na platformie Azure (duże wystąpienia)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

Kolejne kroki:

- Przeczytaj [Omówienie i architekturę SAP HANA na platformie Azure (duże wystąpienia)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA w usłudze Azure Virtual Machines
W tej części dokumentacji omówiono różne aspekty SAP HANA. Jako warunek wstępny należy zapoznać się z głównymi usługami platformy Azure, które zapewniają podstawowe usługi platformy Azure IaaS. W związku z tym potrzebna jest znajomość zasobów obliczeniowych, magazynu i sieci platformy Azure. Wiele z tych tematów jest obsługiwanych w [przewodniku planowania platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)związanego z programem SAP NetWeaver. 

Aby uzyskać informacje na temat platformy HANA na platformie Azure, zobacz następujące artykuły i ich podartykuły:

- [Szybki start: Ręczna instalacja SAP HANA pojedynczego wystąpienia na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Wdrażanie oprogramowania SAP S/4HANA lub BW/4HANA na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [Konfiguracje infrastruktury SAP HANA i operacje na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [SAP HANA wysokiej dostępności dla maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [SAP HANA dostępność w ramach jednego regionu świadczenia usługi Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Dostępność SAP HANA w różnych regionach świadczenia usługi Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Wysoka dostępność SAP HANA na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Przewodnik dotyczący tworzenia kopii zapasowych SAP HANA na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA Azure Backup na poziomie pliku](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [SAP HANA kopii zapasowej na podstawie migawek magazynu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>Oprogramowanie SAP NetWeaver wdrożone na maszynach wirtualnych platformy Azure
Ta sekcja zawiera listę dokumentacji dotyczącej planowania i wdrażania dla oprogramowania SAP NetWeaver i Business One na platformie Azure. Dokumentacja dotyczy podstaw i używania baz danych innych niż HANA z obciążeniem SAP na platformie Azure. Dokumenty i artykuły dotyczące wysokiej dostępności są również podstawą dla wysokiej dostępności HANA na platformie Azure, na przykład:

- [Oprogramowanie SAP Business One na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Wdrażanie programu SAP środowisk IDE EHP7 SP3 dla oprogramowania SAP ERP 6,0 na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [Uruchamianie oprogramowania SAP NetWeaver na maszynach wirtualnych Microsoft Azure z systemem SUSE Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [Planowanie i wdrażanie Virtual Machines platformy Azure dla oprogramowania SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Ochrona wielowarstwowego wdrożenia aplikacji SAP NetWeaver przy użyciu Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Łącznik SAP LaMa dla platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Aby uzyskać informacje na temat baz danych innych niż HANA w obciążeniu SAP na platformie Azure, zobacz:

- [Zagadnienia dotyczące wdrażania systemu Azure Virtual Machines DBMS dla obciążeń SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SQL Server wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [Wdrażanie systemu DBMS usługi Azure Virtual Machines oprogramowania Oracle dla obciążenia SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [Wdrażanie systemu DBMS usługi Azure Virtual Machines programu IBM DB2 dla obciążenia SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [Wdrażanie systemu DBMS usługi Azure Virtual Machines produktu SAP ESE dla obciążenia SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [Wdrażanie oprogramowania SAP MaxDB, pamięci podręcznej na żywo i serwera zawartości na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Aby uzyskać informacje na temat SAP HANA baz danych na platformie Azure, zobacz sekcję "SAP HANA w usłudze Azure Virtual Machines".

Aby uzyskać informacje na temat wysokiej dostępności obciążeń SAP na platformie Azure, zobacz:

- [Azure Virtual Machines wysoka dostępność dla oprogramowania SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

Ten dokument wskazuje różne inne dokumenty o architekturze i scenariuszach. W późniejszych dokumentach scenariuszy linki do szczegółowych dokumentów technicznych, które wyjaśniają wdrożenie i konfigurację różnych metod wysokiej dostępności są dostępne. Różne dokumenty pokazujące sposób ustanawiania i konfigurowania wysokiej dostępności dla obciążeń SAP NetWeaver obejmują systemy operacyjne Linux i Windows.


Aby uzyskać informacje na temat integracji między usługami Azure Active Directory (Azure AD) i SAP oraz logowaniem jednokrotnym, zobacz:

- [Samouczek: Azure Active Directory integrację z chmurą SAP dla klienta](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Samouczek: Azure Active Directory integrację z uwierzytelnianiem tożsamości platformy SAP Cloud Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Samouczek: Azure Active Directory integrację z platformą SAP w chmurze](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Samouczek: Integracja Azure Active Directory z oprogramowaniem SAP NetWeaver](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Samouczek: Azure Active Directory integrację z oprogramowaniem SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Samouczek: Azure Active Directory integrację z usługą SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Środowisko S/4HANA: Fiori programu Launchpad — Logowanie jednokrotne w usłudze Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Aby uzyskać informacje na temat integracji usług platformy Azure z składnikami SAP, zobacz:

- [Używanie oprogramowania SAP HANA w programie Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [Tryb DirectQuery i oprogramowanie SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Używanie łącznika SAP BW Connector w programie Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Usługa Azure Data Factory oferuje integrację danych oprogramowania SAP HANA i Business Warehouse](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)




