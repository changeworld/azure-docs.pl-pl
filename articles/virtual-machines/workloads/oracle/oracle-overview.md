---
title: Rozwiązania firmy Oracle na Microsoft Azure | Microsoft Docs
description: Dowiedz się więcej na temat opcji wdrażania aplikacji i rozwiązań dla oprogramowania Oracle na Microsoft Azure, w tym uruchamiania w całości w infrastrukturze platformy Azure lub łączności między chmurą i usługą Oracle Cloud Infrastructure (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.openlocfilehash: e9e37c54668ec0343cbfd45e51e90216955b46c4
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100020"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Omówienie aplikacji i rozwiązań firmy Oracle na platformie Azure

W tym artykule przedstawiono możliwości uruchamiania rozwiązań firmy Oracle przy użyciu infrastruktury platformy Azure. Zobacz także szczegółowe wprowadzenie do dostępnych [obrazów maszyn wirtualnych Oracle](oracle-vm-solutions.md) w portalu Azure Marketplace oraz możliwości wersji zapoznawczej w celu [łączenia się z platformą Azure przy użyciu infrastruktury chmury firmy Oracle (OCI)](oracle-oci-overview.md).

## <a name="oracle-databases-on-azure-infrastructure"></a>Bazy danych Oracle w infrastrukturze platformy Azure

Uruchamiaj bazy danych Oracle w infrastrukturze platformy Azure przy użyciu obrazów systemu Linux dostępnych w witrynie Azure Marketplace:

* Oracle Database 12,1, 12,2 i 18,3 Enterprise Edition 

* Oracle Database 12,1, 12,2 i 18,3 Standard Edition 

Możesz również utworzyć rozwiązanie na podstawie niestandardowego obrazu tworzonego od podstaw na platformie Azure lub przekazać obraz niestandardowy ze środowiska lokalnego.

Opcjonalnie można skonfigurować z wieloma dołączonymi dyskami i zwiększyć wydajność bazy danych, instalując automatyczne zarządzanie magazynem (ASM) firmy Oracle.

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Aplikacje na serwerach Oracle Linux i WebLogic

Uruchamiaj aplikacje dla przedsiębiorstw na platformie Azure w obsługiwanych systemach operacyjnych Oracle. Następujące obrazy są dostępne w portalu Azure Marketplace:

* Oracle webWebLogicc Server 12.1.2

* Oracle Linux (UEK) 6,8, 6,9, 6,10, 7,3, 7,4, 7,5 i 7,6

## <a name="high-availability-and-disaster-recovery-options"></a>Opcje wysokiej dostępności i odzyskiwania po awarii

* Skonfiguruj funkcję Oracle Data Guard, aktywną ochronę danych lub GoldenGate w infrastrukturze platformy Azure w połączeniu z [strefy dostępności](../../../availability-zones/az-overview.md) w celu zapewnienia wysokiej dostępności.

* Użyj [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) , aby organizować i zarządzać odzyskiwaniem po awarii dla maszyn wirtualnych Oracle Linux na platformie Azure oraz na serwerach lokalnych lub fizycznych. 

* Włącz obsługę systemu Oracle Real Applicationing (RAC) na platformie Azure przy użyciu [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="integration-of-azure-with-oci-preview"></a>Integracja z platformą Azure za pomocą OCI (wersja zapoznawcza)

Uruchom aplikacje Oracle w infrastrukturze platformy Azure, połączone z bazami danych zaplecza w infrastrukturze chmury firmy Oracle (OCI). To rozwiązanie używa następujących możliwości: 

* **Obsługa sieci** z obsługą wielu chmur — umożliwia korzystanie z bezpośrednich połączeń między platformami Azure ExpressRoute i Oracle FastConnect w celu ustanawiania dużych przepustowości, prywatnych i małych opóźnień między aplikacją a warstwą bazy danych.
* **Zintegrowana tożsamość** — Konfigurowanie tożsamości federacyjnej między usługami Azure AD i Oracle IDCS w celu utworzenia jednego źródła tożsamości dla rozwiązań. Włącz logowanie jednokrotne, aby zarządzać zasobami w systemach OCI i na platformie Azure.

### <a name="deploy-oracle-applications-on-azure"></a>Wdrażanie aplikacji Oracle na platformie Azure

Użyj szablonów Terraform, aby skonfigurować infrastrukturę platformy Azure i zainstalować aplikacje Oracle zweryfikowane i obsługiwane do uruchamiania w konfiguracji obejmującej wiele chmur:

* Pakiet E-Business
* JD Edwards EnterpriseOne
* PeopleSoft
* Aplikacje sieci sprzedaży firmy Oracle
* Zarządzanie finansowe Oracle Hyperion

Wdrażaj także aplikacje niestandardowe na platformie Azure, które łączą się z użyciem OCI i innych usług platformy Azure.

### <a name="set-up-oracle-databases-in-oci"></a>Konfigurowanie baz danych Oracle w systemie OCI

Użyj Oracle Database Cloud Services (autonomiczna baza danych, certyfikat RAC, Exadata, DBaaS, pojedynczy węzeł) w połączeniu z aplikacjami Oracle działającymi na platformie Azure. Dowiedz się więcej na temat [opcji bazy danych OCI](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm). 
 

## <a name="licensing"></a>Licencjonowanie

Wdrażanie aplikacji Oracle na platformie Azure jest oparte na modelu "Przenieś własną licencję". Przyjęto założenie, że masz licencję na korzystanie z oprogramowania Oracle i że masz aktualną umowę pomocy technicznej z firmą Oracle. Firma Oracle gwarantuje mobilność licencji ze środowiska lokalnego na platformę Azure. Zobacz [często zadawane pytania dotyczące](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)programu Oracle-Azure.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o wdrażaniu [obrazów maszyn wirtualnych Oracle](oracle-vm-solutions.md) w infrastrukturze platformy Azure.

* Dowiedz się więcej na temat sposobu [łączenia z platformą Azure za pomocą OCI](oracle-oci-overview.md).