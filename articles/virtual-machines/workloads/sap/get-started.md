---
title: Rozpoczynanie pracy z rozwiązaniami SAP na maszynach wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat rozwiązania SAP, które są uruchamiane na maszynach wirtualnych (VM) w systemie Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/10/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c22715434693abd5cebdc5ffd196a0e39b227d1b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835330"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Azure umożliwia hostowanie i uruchamianie scenariusze obciążeń SAP

Korzystając z Microsoft Azure, możesz niezawodne uruchamianie na platformie skalowalne, zgodne i sprawdzonym działaniu w przedsiębiorstwach obciążeń SAP i scenariuszy o kluczowym znaczeniu. Korzystaj ze skalowalności, elastyczności i oszczędności platformy Azure. Dzięki rozszerzonemu partnerstwu między firmami Microsoft i SAP umożliwia uruchamianie aplikacji SAP w środowiskach programowania i testowania oraz produkcyjnych na platformie Azure i być w pełni obsługiwane. Od oprogramowania SAP NetWeaver na SAP S/4HANA, SAP BI w systemie Linux, aby Windows i oprogramowania SAP HANA, SQL, mamy potrzebne.

Oprócz hostowania oprogramowania SAP NetWeaver scenariuszy obejmujących różne systemy DBMS na platformie Azure, może obsługiwać inne scenariusze obciążeń SAP, takich jak SAP BI na platformie Azure. 

Unikatowość platformy Azure dla oprogramowania SAP HANA to oferta, która ustawia Azure od siebie. Umożliwia hostowanie więcej pamięci i procesora CPU wymagających zasobów SAP scenariusze, które obejmują oprogramowanie SAP HANA, platforma Azure oferuje użycie dedykowanego klienta sprzętu bez systemu operacyjnego. Użyj tego rozwiązania, aby uruchomić wdrożenia oprogramowania SAP HANA, które wymagają do 24 TB (120 TB skalowalnego w poziomie) pamięci dla oprogramowania S/4HANA lub innych obciążeń SAP HANA. 

Scenariusze obciążeń SAP na platformie Azure obsługującego również tworzyć wymagania dotyczące integracji tożsamości i logowania jednokrotnego. Taka sytuacja może wystąpić, gdy używasz usługi Azure Active Directory (Azure AD) do łączenia różnych składników SAP i SAP software-as-a-service (SaaS) i platforma jako usługa (PaaS) oferty. Listę takich scenariuszy pojedynczego logowania jednokrotnego z usługą Azure AD i integracji i SAP jednostek jest opisane opisano w sekcji "Integracja tożsamości usługi AAD, SAP i logowanie jednokrotne".

## <a name="latest-changes"></a>Najnowsze zmiany

- Wersja [dużych wystąpień HANA na platformie Azure kontrolować za pośrednictwem witryny Azure portal](hana-li-portal.md)

- Wersja [wysoką dostępność środowiska SAP NetWeaver na maszynach wirtualnych platformy Azure w systemie SUSE Linux Enterprise Server za pomocą usługi Azure Files NetApp dla aplikacji SAP](high-availability-guide-suse-netapp-files.md)

- Wyjaśnienia **net.ipv4.tcp_timestamps parametrów systemu operacyjnego Linux** ustawienia w połączeniu z platformą Azure Usługa równoważenia obciążenia

- Wersja [konfiguracje obciążeń SAP ze strefami dostępności platformy Azure](sap-ha-availability-zones.md)

- Wersja [kontrolną planowania i wdrażania obciążeń SAP](sap-deployment-checklist.md)




## <a name="sap-hana-on-azure-large-instances"></a>Oprogramowanie SAP HANA na platformie Azure (duże wystąpienia)

Seria dokumentów prowadzi za pośrednictwem platformy SAP HANA na platformie Azure (duże wystąpienia) lub w skrócie, dużych wystąpień HANA. Aby uzyskać informacje dotyczące następujących obszarów dużych wystąpień HANA zobacz:

- [Omówienie środowiska SAP Hana na platformie Azure (duże wystąpienia)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Architektura środowiska SAP Hana na platformie Azure (duże wystąpienia)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [Infrastruktura i łączność do platformy SAP HANA na platformie Azure (duże wystąpienia)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [Instalowanie platformy SAP HANA na platformie Azure (duże wystąpienia)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [Wysoka dostępność i odzyskiwanie po awarii oprogramowania SAP Hana na platformie Azure (duże wystąpienia)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [Rozwiązywanie problemów i monitorowanie oprogramowania SAP HANA na platformie Azure (duże wystąpienia)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

Następne kroki:

- Odczyt [omówienie i architektura oprogramowania SAP HANA na platformie Azure (duże wystąpienia)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)



## <a name="sap-hana-on-azure-virtual-machines"></a>Oprogramowanie SAP HANA na maszynach wirtualnych platformy Azure
Ten rozdział dokumentacji opisano różne aspekty platformy SAP HANA. Jako warunek wstępny należy zapoznać się z nazwy głównej usługi platformy Azure, które zapewniają podstawowe usługi infrastruktury IaaS platformy Azure. Dlatego należy wiedzy obliczeniowych platformy Azure, Magazyn i sieć. Wiele z tych tematów są obsługiwane w oprogramowanie SAP NetWeaver powiązane [Azure przewodnik planowania](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide). 

Aby uzyskać informacje na platformie HANA na platformie Azure zobacz następujące artykuły i ich subarticles:

- [Szybki start: Ręczna instalacja jednego wystąpienia oprogramowania SAP HANA na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Wdrażanie rozwiązania SAP S/4HANA lub BW/4HANA na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [Konfiguracje infrastruktury SAP HANA i operacje na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [SAP HANA wysoką dostępność dla maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [SAP HANA dostępności w obrębie jednego regionu platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Dostępność platformy SAP HANA w regionach platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Wysoka dostępność środowiska SAP HANA na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Przewodniku kopia zapasowa dla oprogramowania SAP HANA na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA usługi Azure Backup na poziomie plików](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Kopia zapasowa oprogramowania SAP HANA na podstawie migawek magazynu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>Oprogramowanie SAP NetWeaver wdrożonych na maszynach wirtualnych platformy Azure
Ta sekcja zawiera dokumentację planowania i wdrażania oprogramowania SAP NetWeaver i Business One na platformie Azure. Dokumentacja koncentruje się na z podstawowymi informacjami i korzystanie z baz danych innych niż HANA z obciążeniami SAP na platformie Azure. Dokumenty i artykuły dotyczące wysokiej dostępności są również podstawą dla platformy HANA wysoką dostępność na platformie Azure, takich jak:

- [SAP Business One na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Wdrażanie oprogramowania SAP IDES EHP7 SP3 dla oprogramowania SAP ERP 6.0 na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [Uruchamianie oprogramowania SAP NetWeaver na maszynach wirtualnych systemu Linux SUSE platformy Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [Azure Virtual Machines, planowania i implementacji środowiska SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Wdrażania maszyn wirtualnych platformy Azure dla oprogramowania SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Ochrona wdrożenie wielowarstwowej aplikacji SAP NetWeaver przy użyciu usługi Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Łącznik SAP LaMa dla platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Aby uzyskać informacji na temat bazy danych bez HANA obciążenia SAP na platformie Azure zobacz:

- [Zagadnienia dotyczące wdrażania systemu DBMS na maszynach wirtualnych platformy Azure w przypadku obciążeń SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [Wdrożenie programu SQL Server Azure Virtual Machines DBMS dla oprogramowania SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [Wdrażanie systemu DBMS usługi Azure Virtual Machines oprogramowania Oracle dla obciążenia SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [Wdrażanie systemu DBMS usługi Azure Virtual Machines programu IBM DB2 dla obciążenia SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [Wdrażanie systemu DBMS usługi Azure Virtual Machines produktu SAP ESE dla obciążenia SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [SAP MaxDB, na żywo z pamięci podręcznej i serwer zawartości wdrożenia na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Aby uzyskać informacji na temat baz danych SAP HANA na platformie Azure zobacz sekcję "Platformy SAP HANA na maszynach wirtualnych platformy Azure."

Aby uzyskać informacje na temat wysokiej dostępności obciążeń SAP na platformie Azure zobacz:

- [Azure maszyny wirtualne wysokiej dostępności dla oprogramowania SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

W tym dokumencie wskazuje różnych innych dokumentów architektury i scenariusza. W nowszych dokumentach scenariusz znajdują się linki do szczegółowych dokumentów technicznych, które wyjaśniają, wdrażania i konfigurowania różnych metod o wysokiej dostępności. Różne dokumenty, które pokazują, jak i skonfigurować wysoką dostępność w przypadku obciążeń SAP NetWeaver obejmują systemów operacyjnych Linux i Windows.


Aby uzyskać informacji na temat integracji między usługi Azure Active Directory (Azure AD) i usług SAP oraz logowanie jednokrotne zobacz:

- [Samouczek: Usługa Azure integracji usługi Active Directory z rozwiązaniem SAP Cloud klienta](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Samouczek: Integracja usługi Azure Active Directory za pomocą uwierzytelniania tożsamości platformy SAP w chmurze](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Samouczek: Integracja usługi Azure Active Directory za pomocą platformy SAP Cloud Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Samouczek: Integracja usługi Azure Active Directory z oprogramowaniem SAP NetWeaver](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Samouczek: Integracja usługi Azure Active Directory z oprogramowaniem SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Samouczek: Integracja usługi Azure Active Directory z platformą SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [S/4HANA środowiska: Fiori Launchpad SAML logowania jednokrotnego z usługą Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Aby uzyskać informacji na temat integracji usług platformy Azure na składniki SAP zobacz:

- [Używanie oprogramowania SAP HANA w programie Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [Tryb DirectQuery i oprogramowanie SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Używanie łącznika SAP BW Connector w programie Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Usługa Azure Data Factory oferuje integrację danych oprogramowania SAP HANA i Business Warehouse](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)




