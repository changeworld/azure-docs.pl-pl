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
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/27/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 64aa6dcd9d21d2102c5d61dbc050fc9aa07aa534
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2020
ms.locfileid: "78163866"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Korzystanie z platformy Azure do hostowania i uruchamiania scenariuszy obciążeń SAP

W przypadku korzystania z Microsoft Azure można w niezawodny sposób uruchamiać obciążenia i scenariusze o kluczowym znaczeniu SAP w skalowalnej, zgodnej i korporacyjnej platformie. Uzyskasz skalowalność, elastyczność i oszczędność kosztów platformy Azure. Dzięki rozbudowanym partnerstwu między firmą Microsoft i SAP można uruchamiać aplikacje SAP w ramach scenariuszy deweloperskich i testowych oraz produkcyjnych na platformie Azure i być w pełni obsługiwane. W przypadku oprogramowania SAP NetWeaver do SAP S/4HANA, SAP BI w systemie Linux do systemu Windows i SAP HANA do bazy danych SQL.

Oprócz hostowania scenariuszy SAP NetWeaver z różnymi systemami DBMS na platformie Azure, można hostować inne scenariusze obciążeń SAP, takie jak SAP BI na platformie Azure. 

Unikatowość platformy Azure dla SAP HANA to oferta, która umożliwia rozróżnianie platformy Azure. Aby umożliwić hostowanie większej ilości pamięci i scenariuszy SAP wymagających zasobów procesora, które obejmują SAP HANA, platforma Azure umożliwia korzystanie z sprzętu bez systemu operacyjnego przeznaczonego dla klientów. To rozwiązanie służy do uruchamiania wdrożeń SAP HANA, które wymagają do 24 TB do skalowania w poziomie (120-TB) pamięci dla usługi S/4HANA lub innych obciążeń SAP HANA. 

Scenariusze obsługi obciążeń SAP na platformie Azure mogą również stworzyć wymagania integracji tożsamości i logowania jednokrotnego. Taka sytuacja może wystąpić w przypadku korzystania z Azure Active Directory (Azure AD) w celu łączenia różnych składników SAP oraz ofert oprogramowania SAP jako usługi (SaaS) lub platformy jako usługi (PaaS). Lista takich integracji i scenariuszy logowania jednokrotnego za pomocą usługi Azure AD i jednostek SAP została opisana i udokumentowana w sekcji "Integracja tożsamości i logowanie jednokrotne w usłudze AAD SAP".

## <a name="changes-to-the-sap-workload-section"></a>Zmiany w sekcji obciążenia SAP
Zmiany w dokumentach w sekcji obciążenia SAP na platformie Azure są wymienione na końcu tego artykułu. Wpisy w dzienniku zmian są przechowywane przez około 180 dni.

## <a name="you-want-to-know"></a>Chcesz wiedzieć
Jeśli masz określone pytania, przejdźmy do określonych dokumentów lub przepływów w tej sekcji na stronie startowej. Chcesz poznać następujące informacje:

- Obsługiwane jednostki maszyn wirtualnych platformy Azure i usługi HANA, dla których wydano oprogramowanie SAP i wersje systemu operacyjnego. Zapoznaj się z dokumentem [jakie oprogramowanie SAP jest obsługiwane w przypadku wdrażania na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) na potrzeby odpowiedzi i proces znajdowania informacji
- Scenariusze wdrażania SAP są obsługiwane przez maszyny wirtualne platformy Azure i duże wystąpienia HANA. Informacje o obsługiwanych scenariuszach można znaleźć w dokumentach:
    - [Scenariusze obsługi obciążeń SAP w maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)
    - [Obsługiwane scenariusze dla dużego wystąpienia HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario)

 
## <a name="sap-hana-on-azure-large-instances"></a>Oprogramowanie SAP HANA na platformie Azure (duże wystąpienia)

Seria dokumentów prowadzi użytkownika przez SAP HANA na platformie Azure (duże wystąpienia) lub w przypadku krótkich dużych wystąpień usługi HANA. Aby uzyskać informacje o dużych wystąpieniach platformy HANA, zapoznaj się z [omówieniem dokumentu i architekturą SAP HANA na platformie Azure (duże wystąpienia)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) i przejdź przez powiązaną dokumentację w sekcji duże wystąpienie usługi Hana



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA w usłudze Azure Virtual Machines
W tej części dokumentacji omówiono różne aspekty SAP HANA. Jako warunek wstępny należy zapoznać się z głównymi usługami platformy Azure, które zapewniają podstawowe usługi platformy Azure IaaS. W związku z tym potrzebna jest znajomość zasobów obliczeniowych, magazynu i sieci platformy Azure. Wiele z tych tematów jest obsługiwanych w [przewodniku planowania platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)związanego z programem SAP NetWeaver. 

Aby uzyskać informacje na temat platformy HANA na platformie Azure, zobacz następujące artykuły i ich podartykuły:

- [Szybki Start: Instalacja ręczna SAP HANA pojedynczego wystąpienia na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
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

- [Samouczek: integracja Azure Active Directory z chmurą SAP dla klienta](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Samouczek: integracja Azure Active Directory z uwierzytelnianiem tożsamości platformy SAP Cloud Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Samouczek: integracja Azure Active Directory z platformą SAP w chmurze](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Samouczek: integracja Azure Active Directory z oprogramowaniem SAP NetWeaver](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Samouczek: integracja Azure Active Directory z oprogramowaniem SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Samouczek: integracja Azure Active Directory z SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Środowisko S/4HANA: Fiori programu Launchpad — Logowanie jednokrotne w usłudze Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Aby uzyskać informacje na temat integracji usług platformy Azure z składnikami SAP, zobacz:

- [Używanie oprogramowania SAP HANA w programie Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [Tryb DirectQuery i oprogramowanie SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Używanie łącznika SAP BW Connector w programie Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Usługa Azure Data Factory oferuje integrację danych oprogramowania SAP HANA i Business Warehouse](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>Dziennik zmiany
- 02/27/2020: zmiana [wysokiej dostępności dla oprogramowania SAP NW na maszynach wirtualnych platformy Azure w systemie SLES for SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [wysoka dostępność dla oprogramowania SAP NW na maszynach wirtualnych platformy Azure na platformie SLES z ANF for SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) i [wysoką dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure w systemie SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) , aby dostosować parametr klastra "po niepowodzeniu"
- 02/26/2020: Zmień [konfigurację magazynu maszyn wirtualnych platformy Azure na SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) , aby wyjaśnić wybór systemu plików dla platformy Hana na platformie Azure
- 02/26/2020: zmiana [architektury i scenariuszy wysokiej dostępności dla SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) w celu uwzględnienia linku do ha dla SAP NetWeaver na maszynach wirtualnych platformy Azure w RHEL
- 02/26/2020: zmiana [wysokiej dostępności dla oprogramowania SAP NW na maszynach wirtualnych platformy Azure w systemie SLES for SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [wysoka dostępność dla oprogramowania SAP NW na maszynach wirtualnych platformy Azure na platformie SLES z ANF dla aplikacji SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), [maszyny wirtualne platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) o wysokiej dostępności dla oprogramowania SAP NetWeaver na RHEL i [maszyn wirtualnych platformy Azure o wysokiej dostępności dla oprogramowania SAP NetWeaver w Azure NetApp Files RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)
- 02/26/2020: wydanie [wysokiej dostępności dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure w przewodniku dotyczącym WIELOSID RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid) w celu dodania linku do przewodnika po klastrze z obsługą wiele identyfikatorów SID
- 02/25/2020: zmiana [architektury i scenariuszy wysokiej dostępności dla SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) w celu dodania linków do NOWSZYCH artykułów ha
- 02/25/2020: zmiana [wysokiej dostępności programu IBM DB2 LUW na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server z Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm) , aby wskazać dokument, który opisuje dostęp do publicznego punktu końcowego za pomocą standardowego modułu równoważenia obciążenia platformy Azure
- 02/21/2020: Pełna wersja artykułu [SAP ASE Azure Virtual Machines wdrożenia systemu DBMS dla obciążenia SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- 02/21/2020: Zmień [konfigurację magazynu maszyn wirtualnych platformy Azure na SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) , aby reprezentować nowe zalecenie w rozmiarze rozłożonym dla/Hana/Data i dodać ustawienie harmonogramu we/wy
- 02/21/2020: zmiany w dokumentach w dużych wystąpieniach platformy HANA do reprezentowania nowo certyfikowanych jednostek SKU z S224 i S224m
- 02/21/2020: zmiana [wysokiej dostępności maszyn wirtualnych platformy Azure dla oprogramowania SAP NetWeaver na RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) i [maszyn wirtualnych platformy Azure o wysokiej dostępności dla oprogramowania SAP NetWeaver na RHEL przy użyciu Azure NetApp Files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) , aby dostosować ograniczenia klastra dla architektury replikacji serwera w kolejce 2 (ENSA2)
- 02/20/2020: zmiana [wysokiej dostępności dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure w przewodniku dotyczącym usługi SLES z obsługą wiele identyfikatorów SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) w celu dodania linku do podręcznika klastra z obsługą wiele identyfikatorów SID
- 02/13/2020: zmiany w [usłudze Azure Virtual Machines planowanie i wdrażanie oprogramowania SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) w celu zaimplementowania linków do nowych dokumentów
- 02/13/2020: dodano nowy dokument [obciążenie SAP w scenariuszu obsługiwanym przez maszynę wirtualną platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)
- 02/13/2020: dodano nowy dokument, [jakie oprogramowanie SAP jest obsługiwane w przypadku wdrażania platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)
- 02/13/2020: zmiana [wysokiej dostępności programu IBM DB2 LUW na maszynach wirtualnych platformy Azure na serwerze Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-ibm-db2-luw) , aby wskazać dokument, który opisuje dostęp do publicznego punktu końcowego za pomocą standardowego modułu równoważenia obciążenia platformy Azure
- 02/13/2020: Dodaj nowe typy maszyn wirtualnych do [certyfikatów SAP i konfiguracji uruchomionych w Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)
- 02/13/2020: Dodaj nowe obciążenia SAP uwagi [SAP na platformie Azure: planowanie i wdrażanie listy kontrolnej](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- 02/13/2020: zmiana na [maszynach wirtualnych platformy Azure o wysokiej dostępności dla oprogramowania SAP NetWeaver na RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) i [maszynach wirtualnych platformy Azure o wysokiej dostępności dla oprogramowania SAP NetWeaver na RHEL przy użyciu Azure NetApp Files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) , aby wyrównać limity czasu zasobów klastra do zaleceń dotyczących limitu czasu Red Hat
- 02/11/2020: wydanie [SAP HANA w ramach migracji dużych wystąpień platformy Azure do platformy azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-large-instance-virtual-machine-migration)
- 02/07/2020: zmiana [łączności publicznej punktu końcowego dla maszyn wirtualnych przy użyciu usługi Azure Standard ILB w scenariuszach dotyczących oprogramowania SAP ha](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections) do aktualizowania przykładowego ZRZUTu sieciowej grupy zabezpieczeń
- 02/03/2020: zmiana [wysokiej dostępności dla oprogramowania SAP NW na maszynach wirtualnych platformy Azure w systemie SLES for SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse) i [wysokiej dostępności dla oprogramowania SAP NW na maszynach wirtualnych platformy Azure na platformie SLES z ANF for SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) , aby usunąć ostrzeżenie dotyczące używania łącznika w nazwach hostów węzłów klastra na SLES
- 01/28/2020: Zmień [wysoką dostępność SAP HANA na maszynach wirtualnych platformy Azure w systemie RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel) , aby wyrównać limity czasu SAP HANA zasobów klastra do zaleceń dotyczących limitu czasu Red Hat
- 01/17/2020: zmiana [grup umieszczania bliskości platformy Azure w celu uzyskania optymalnego opóźnienia sieci przy użyciu aplikacji SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) aby zmienić sekcję przenoszenie istniejących maszyn wirtualnych do grupy umieszczania w sąsiedztwie
- 01/17/2020: Zmień [konfiguracje obciążeń SAP za pomocą strefy dostępności platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones) , aby wskazać procedurę, która automatyzuje pomiary opóźnienia między strefy dostępności
- 01/16/2020: Zmień [sposób instalowania i konfigurowania SAP HANA (duże wystąpienia) na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation) , aby dostosowywać wersje systemu operacyjnego do katalogu sprzętowego Hana IaaS
- 01/16/2020: zmiany [wysokiej dostępności dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure w przewodniku dotyczącym usługi SLES z obsługą wiele identyfikatorów SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) , aby dodać instrukcje dla systemów SAP przy użyciu kolejki Server 2 (ENSA2)
- 01/10/2020: zmiany w [SAP HANA skalowanie w poziomie za pomocą węzła stan gotowości na maszynach wirtualnych platformy Azure z Azure NetApp Files na SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) i w [SAP HANA skalowanie w poziomie za pomocą węzła stan gotowości na maszynach wirtualnych platformy Azure z Azure NetApp Files na RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) , aby dodać instrukcje dotyczące ciągłego wprowadzania zmian `nfs4_disable_idmapping`.
- 01/10/2020: zmiany [wysokiej dostępności dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure na platformie SLES z Azure NetApp Files dla aplikacji SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) oraz na [platformie Azure Virtual Machines wysokiej dostępności dla oprogramowania SAP NetWeaver na RHEL z aplikacjami Azure NetApp files for SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) , aby dodać instrukcje dotyczące instalowania Azure NetApp Files woluminów NFSv4.
- 12/23/2019: wydanie [wysokiej dostępności dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure w przewodniku dotyczącym usługi SLES z obsługą wiele identyfikatorów SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)
- 12/18/2019: wydawanie [SAP HANA skalowanie w poziomie z węzłem gotowości na maszynach wirtualnych platformy Azure z Azure NetApp Files na RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- 11/21/2019: zmiany w [SAP HANA skalowanie w poziomie za pomocą węzła wstrzymywania na maszynach wirtualnych platformy Azure z Azure NetApp Files na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) upraszczają konfigurację mapowania identyfikatorów systemu plików NFS i umożliwiają zmianę zalecanego podstawowego interfejsu sieciowego na uproszczenie routingu.
- 11/15/2019: niewielkie zmiany [dotyczące wysokiej dostępności dla oprogramowania SAP NetWeaver na SUSE Linux Enterprise Server z Azure NetApp Files dla aplikacji SAP](high-availability-guide-suse-netapp-files.md) i [wysokiej dostępności dla oprogramowania sap NetWeaver na Red Hat Enterprise Linux z Azure NetApp files for SAP Applications](high-availability-guide-rhel-netapp-files.md) w celu wyjaśnienia ograniczeń rozmiaru puli pojemności i usunięcia instrukcji, że obsługiwana jest tylko wersja NFSv3.
- 11/12/2019: wydanie [wysokiej dostępności dla oprogramowania SAP NetWeaver w systemie Windows z Azure NetApp Files (SMB)](high-availability-guide-windows-netapp-files-smb.md)
- 11/08/2019: zmiany dotyczące [wysokiej dostępności SAP HANA na maszynach wirtualnych platformy Azure w SUSE Linux Enterprise Server](sap-hana-high-availability.md), [konfigurowania replikacji systemu SAP HANA na maszynach wirtualnych platformy Azure](sap-hana-high-availability-rhel.md), na [platformie Azure Virtual Machines wysokiej dostępności dla oprogramowania SAP NETWEAVER na SUSE Linux Enterprise Server dla aplikacji SAP](high-availability-guide-suse.md), platforma Azure Virtual Machines wysoką dostępność dla oprogramowania SAP [NetWeaver na SUSE Linux Enterprise Server](high-availability-guide-suse-netapp-files.md)Azure NetApp Files [ ](high-availability-guide-rhel.md), [Platforma Azure Virtual Machines wysoką dostępność dla oprogramowania SAP NetWeaver na Red Hat Enterprise Linux z Azure NetApp Files](high-availability-guide-rhel-netapp-files.md), [wysoką dostępnością dla systemu plików NFS na maszynach wirtualnych platformy Azure na platformie SUSE Linux Enterprise Server](high-availability-guide-suse-nfs.md), [GlusterFS na maszynach wirtualnych platformy Azure na Red Hat Enterprise Linux dla oprogramowania SAP NetWeaver](high-availability-guide-rhel-glusterfs.md) , aby zalecamy korzystanie z usługi Azure Standard Load  
- 11/08/2019: zmiany dotyczące [planowania obciążeń SAP i listy kontrolnej wdrożenia](sap-deployment-checklist.md) w celu wyjaśnienia zalecenia dotyczące szyfrowania  
- 11/04/2019: zmiany [SUSE Linux Enterprise Server dotyczące konfigurowania Pacemaker na platformie Azure](high-availability-guide-suse-pacemaker.md) w celu utworzenia klastra bezpośrednio z konfiguracją emisji pojedynczej  
- 10/29/2019: wydanie [łączności między publicznymi punktami końcowymi dla Virtual Machines przy użyciu usługi Azure usługa Load Balancer w warstwie Standardowa w scenariuszach wysokiej dostępności SAP](high-availability-guide-standard-load-balancer-outbound-connections.md)
- 10/25/2019: zmiany [konfiguracji magazynu maszyn wirtualnych SAP HANA platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) i [SAP HANA skalowanie w poziomie za pomocą węzła wstrzymywania na maszynach wirtualnych platformy Azure z Azure NetApp Files na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) do wyjaśnienia protokołu NFS dla woluminu/Hana/Shared
- 10/22/2019: Zmień [wysoką dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server dla aplikacji SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [wysokiej dostępności dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy azure na platformie SUSE Linux Enterprise Server z Azure NETAPP Files dla aplikacji SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), wysokiej dostępności dla [systemu plików NFS SUSE Linux Enterprise Server na](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)maszynach wirtualnych platformy azure na [platformie Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)na [SUSE Linux Enterprise Server z Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm), i [wysoka dostępność SAP HANA na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) na potrzeby ochrony przed wykrywaniem modułu równoważenia obciążenia platformy Azure
- Zmiany sekcji ANF sekcja i nagłówek w [SAP HANA konfiguracjach magazynu maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- 10/21/2019: wydawanie [SAP HANA skalowanie w poziomie z węzłem gotowości na maszynach wirtualnych platformy Azure z Azure NetApp Files na SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- 10/16/2019: napraw przerwane linki w [kopiach zapasowych i przywracania](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore)
- 10/16/2019: Zmień minimalną zalecaną wersję systemu operacyjnego z SLES 12 SP3 na SLES 12 SP4 w [przypadku wysokiej dostępności usług IBM DB2 LUW na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server za pomocą Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)
- 10/11/2019: zmiany konfiguracji magazynu Ultra Disk i wprowadzenia ANF [SAP HANA w konfiguracji magazynu maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- 10/01/2019: zmiana w grafice [grup umieszczania bliskości platformy Azure w celu uzyskania optymalnego opóźnienia sieci przy użyciu aplikacji SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) w celu zwiększenia przejrzystości
- 10/01/2019: Zmień [konfiguracje infrastruktury SAP HANA i operacje na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) , aby skorygować instrukcje dotyczące udziału NFS o wysokiej dostępności dla/Hana/Shared. 
- 09/28/2019: zmiana [konfiguracji Pacemaker Red Hat Enterprise Linux na platformie Azure w](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) celu wyjaśnienia SBD jako mechanizmu ogrodzenia nie jest obsługiwane w KLASTRAch RHEL  
- 09/17/2019: Zmień w przewodniku planowania i wdrażania NetWeaver, aby ujednolicić warunki dotyczące rozszerzenia maszyny wirtualnej dla oprogramowania SAP  
- 08/22/2019: zmiany dotyczące [konfigurowania Pacemaker SUSE Linux Enterprise Server na platformie Azure w](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) celu zaktualizowania adresów URL na potrzeby tworzenia ról niestandardowych  
- 08/16/2019: zmiany [Red Hat Enterprise Linux dotyczące konfigurowania Pacemaker na platformie Azure w](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) celu przypomnienia klientów o aktualizacji akcji w roli niestandardowej, jeśli aktualizacja nowej wersji agenta usługi Azure ogrodzenia  
- 08/15/2019: zmiany [konfiguracji magazynu maszyn wirtualnych SAP HANA platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) w celu odzwierciedlenia ogólnej dostępności Ultra Disk (dawniej SSD w warstwie Ultra)
- 08/01/2019: zmiany dotyczące [konfigurowania Pacemaker SUSE Linux Enterprise Server na platformie Azure w](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) celu integracji zmian przeznaczonych dla SLES 15 


