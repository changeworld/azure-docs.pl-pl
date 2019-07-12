---
title: Rozwiązania firmy Oracle w systemie Microsoft Azure | Dokumentacja firmy Microsoft
description: Informacje o opcjach wdrażania aplikacji Oracle i rozwiązań w systemie Microsoft Azure, takimi jak uruchomiony w całości w infrastrukturze platformy Azure lub przy użyciu połączenia wielu chmur przy użyciu infrastruktury chmury Oracle (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.openlocfilehash: b8bfa0dfa82f73cad010a608150eac48c7f3d4c8
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707447"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Omówienie aplikacji Oracle i rozwiązania na platformie Azure

W tym artykule przedstawiono możliwości do uruchamiania rozwiązań Oracle przy użyciu infrastruktury platformy Azure. Zobacz również szczegółowe wprowadzenia do dostępnych [obrazów maszyn wirtualnych Oracle](oracle-vm-solutions.md) w portalu Azure Marketplace i możliwości w wersji zapoznawczej [połączenie platformy Azure przy użyciu infrastruktury chmury Oracle (OCI)](oracle-oci-overview.md).

## <a name="oracle-databases-on-azure-infrastructure"></a>Baz danych Oracle w infrastrukturze platformy Azure

Uruchom baz danych Oracle w infrastrukturze platformy Azure przy użyciu obrazów systemu Linux dostępnych w witrynie Azure Marketplace:

* Oracle Database 12.1 12.2 i 18.3 Enterprise Edition 

* Oracle Database 12.1 12.2 i 18.3 Standard Edition 

Możesz również utworzyć rozwiązanie na obraz niestandardowy, możesz utworzyć od podstaw na platformie Azure lub przekazanie niestandardowego obrazu ze środowiska lokalnego.

Opcjonalnie skonfigurować przy użyciu wielu dołączonych dysków i zwiększyć wydajność bazy danych przez zainstalowanie bazy danych Oracle zautomatyzowane Storage Management (ASM).

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Aplikacje w systemie Oracle Linux i WebLogic Server

Uruchamianie aplikacji przedsiębiorstwa na platformie Azure w obsługiwanych systemach operacyjnych Oracle. W portalu Azure Marketplace dostępne są następujące obrazy:

* Oracle WebLogic Server 12.1.2

* Oracle Linux (UEK) 6.8 6,9, 6.10, 7.3, 7.4, 7.5 i 7.6

## <a name="high-availability-and-disaster-recovery-options"></a>Opcje odzyskiwania wysoką dostępność i odzyskiwanie po awarii

* Konfigurowanie środowiska Oracle Data Guard Active Data Guard i GoldenGate w infrastrukturze platformy Azure w połączeniu z [strefy dostępności](../../../availability-zones/az-overview.md) wysokiej dostępności.

* Użyj [usługi Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) umożliwia organizowanie i zarządzać odzyskiwaniem po awarii dla maszyn wirtualnych systemu Linux Oracle na platformie Azure i Twojej lokalnej lub serwerów fizycznych. 

* Włącz Oracle rzeczywistych aplikacji klastrów dostępu (RAC) w systemie Azure za pomocą [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="integration-of-azure-with-oci-preview"></a>Integracja platformy Azure dzięki programowi OCI (wersja zapoznawcza)

Uruchamianie aplikacji oprogramowania Oracle w infrastrukturze platformy Azure, nawiązanie połączenia bazy danych zaplecza w infrastrukturze chmury Oracle (OCI). To rozwiązanie korzysta z następujących możliwości: 

* **Połączenia sieciowe między chmurami** — Użyj bezpośrednie połączenie między usługi Azure ExpressRoute i Oracle FastConnect do ustanawiania połączeń o wysokiej przepustowości, prywatne i niskich opóźnieniach między aplikacją i warstwę bazy danych.
* **Zintegrowane tożsamości** — Konfigurowanie tożsamości federacyjnych między usługą Azure AD i IDCS Oracle, aby utworzyć źródło jednej tożsamości dla rozwiązań. Włącz logowanie jednokrotne do zarządzania zasobami w OCI i na platformie Azure.

### <a name="deploy-oracle-applications-on-azure"></a>Wdrażanie aplikacji Oracle na platformie Azure

Szablony programu Terraform umożliwia skonfigurowanie infrastruktury platformy Azure i zainstalowanie Oracle sprawdzone, a obsługiwane uruchamianie aplikacji w konfiguracji wielu chmur:

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Aplikacji handlu detalicznego Oracle
* Zarządzania finansami Hyperion Oracle

Również wdrażać niestandardowe aplikacje na platformie Azure, łączących się z OCI i innymi usługami platformy Azure.

### <a name="set-up-oracle-databases-in-oci"></a>Konfigurowanie baz danych Oracle w OCI

Używać Oracle bazy danych usług w chmurze (autonomicznej bazy danych, RAC, Exadata, DBaaS, jednym węźle) w połączeniu z aplikacji Oracle działające na platformie Azure. Dowiedz się więcej o [opcje bazy danych OCI](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm). 
 

## <a name="licensing"></a>Licencjonowanie

Wdrażanie aplikacji Oracle na platformie Azure jest oparty na modelu "bring your own license". Zakłada się, że są poprawnie licencjonowane oprogramowanie Oracle i mieć bieżącej umowy dotyczącej pomocy technicznej w miejscu z firmą Oracle. Oracle, ma gwarancję funkcji przeniesienia licencji w ze środowiska lokalnego do platformy Azure. Zobacz platformę Azure Oracle [— często zadawane pytania](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o wdrażaniu [obrazów maszyn wirtualnych Oracle](oracle-vm-solutions.md) w infrastrukturze platformy Azure.

* Dowiedz się więcej na temat [połączenie platformy Azure przy użyciu OCI](oracle-oci-overview.md).