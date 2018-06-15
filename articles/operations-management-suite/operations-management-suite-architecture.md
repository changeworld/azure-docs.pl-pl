---
title: Architektura pakietu Operations Management Suite (OMS) | Microsoft Docs
description: Pakiet Microsoft Operations Management Suite (OMS) to oparte na chmurze rozwiązanie firmy Microsoft do zarządzania systemami IT, które ułatwia zarządzanie infrastrukturą lokalną i chmurową oraz jej ochronę.  W tym artykule opisano różne usługi w ramach pakietu OMS i umieszczono linki do szczegółowych treści na ich temat.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 40e41686-7e35-4d85-bbe8-edbcb295a534
ms.service: operations-management-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: bwren
ms.openlocfilehash: fb4e9150c1069d48399fb217f865b294ccd317dc
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2017
ms.locfileid: "26683269"
---
# <a name="oms-architecture"></a>Architektura pakietu OMS
[Pakiet Operations Management Suite (OMS)](https://azure.microsoft.com/documentation/services/operations-management-suite/) to zbiór opartych na chmurze usług zarządzania środowiskami lokalnymi i chmurowymi.  W tym artykule opisano różne lokalne i chmurowe składniki pakietu OMS oraz podano ogólny opis ich architektury przetwarzania w chmurze.  Podano też linki do dokumentacji zawierającej dodatkowe szczegóły dotyczące każdej usługi.

## <a name="log-analytics"></a>Log Analytics
Wszystkie dane zebrane przez usługę [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) są przechowywane w repozytorium pakietu OMS, które jest hostowane na platformie Azure.  Dane zbierane w repozytorium pakietu OMS są generowane przez połączone źródła.  Obecnie istnieją trzy typy obsługiwanych połączonych źródeł.

* Agent zainstalowany na komputerze z systemem [Windows](../log-analytics/log-analytics-windows-agent.md) lub [Linux](../log-analytics/log-analytics-linux-agents.md) podłączony bezpośrednio do pakietu OMS.
* Grupa zarządzania programu System Center Operations Manager (SCOM) [połączona z usługą Log Analytics](../log-analytics/log-analytics-om-agents.md).  Agenci SCOM nadal komunikują się z serwerami zarządzania, które przekazują zdarzenia i dane dotyczące wydajności do usługi Log Analytics.
* [Konto usługi Azure Storage](../log-analytics/log-analytics-azure-storage.md), które zbiera dane usługi [Diagnostyka Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) z roli Proces roboczy, roli Sieć Web lub z maszyny wirtualnej na platformie Azure.

Źródła danych definiują dane, które usługa Log Analytics zbiera z połączonych źródeł, takich jak dzienniki zdarzeń i liczniki wydajności.  Rozwiązania powodują dodanie funkcji do pakietu OMS i mogą być łatwo dodawane do obszaru roboczego z [Galerii rozwiązań pakietu OMS](../log-analytics/log-analytics-add-solutions.md).  Niektóre rozwiązania mogą wymagać bezpośredniego połączenia z usługą Log Analytics z agentów SCOM, podczas gdy inne mogą wymagać zainstalowania dodatkowego agenta.

Usługa Log Analytics udostępnia oparty na sieci Web portal, który umożliwia zarządzanie zasobami pakietu OMS, dodawanie i konfigurowanie rozwiązań pakietu OMS oraz wyświetlanie i analizowanie danych w repozytorium pakietu OMS.

![Architektura wysokiego poziomu usługi Log Analytics](media/operations-management-suite-architecture/log-analytics.png)

## <a name="azure-automation"></a>Azure Automation
[Elementy Runbook usługi Azure Automation](http://azure.microsoft.com/documentation/services/automation) są wykonywane w chmurze Azure i mogą uzyskiwać dostęp do zasobów znajdujących się na platformie Azure, w innych usługach w chmurze lub dostępnych w publicznym Internecie.  Można również przydzielić komputery lokalne w lokalnym centrum danych za pomocą [hybrydowego procesu roboczego elementu Runbook](../automation/automation-hybrid-runbook-worker.md) w taki sposób, aby elementy Runbook mogły uzyskać dostęp do zasobów lokalnych.

[Konfiguracje DSC](../automation/automation-dsc-overview.md) przechowywane w usłudze Azure Automation mogą być stosowane bezpośrednio do maszyn wirtualnych platformy Azure.  Inne maszyny fizyczne i wirtualne mogą żądać dostępu do konfiguracji z serwera ściągania usługi Azure Automation DSC.

Usługa Azure Automation obejmuje rozwiązanie pakietu OMS, które wyświetla statystyki i linki do uruchomienia witryny Azure Portal dla wszystkich operacji.

![Architektura wysokiego poziomu usługi Azure Automation](media/operations-management-suite-architecture/automation.png)

## <a name="azure-backup"></a>Azure Backup
Chronione dane w usłudze [Azure Backup](http://azure.microsoft.com/documentation/services/backup) są przechowywane w magazynie kopii zapasowych, znajdującym się w określonym regionie geograficznym.  Dane są replikowane w obrębie tego samego regionu, a następnie, w zależności od typu magazynu, mogą być również replikowane do innego regionu w celu osiągnięcia dalszej nadmiarowości.

Usługa Azure Backup ma trzy podstawowe scenariusze.

* Komputer z systemem Windows z agentem usługi Azure Backup.  W tym scenariuszu z dowolnego serwera lub klienta systemu Windows można tworzyć kopie zapasowe plików i folderów bezpośrednio do magazynu kopii zapasowych Azure.  
* System Center Data Protection Manager (DPM) lub serwer usługi Microsoft Azure Backup. W tym scenariuszu można użyć programu DPM lub usługi Microsoft Azure Backup Server do tworzenia kopii zapasowych plików i folderów, a także obciążeń aplikacji, takich jak SQL i SharePoint, w magazynie lokalnym, a następnie do replikowania ich do magazynu kopii zapasowych platformy Azure.
* Rozszerzenia maszyny wirtualnej platformy Azure.  W tym scenariuszu można tworzyć kopie zapasowe maszyn wirtualnych platformy Azure w magazynie kopii zapasowych platformy Azure.

Usługa Azure Backup obejmuje rozwiązanie pakietu OMS, które wyświetla statystyki i linki do uruchomienia witryny Azure Portal dla wszystkich operacji.

![Architektura wysokiego poziomu usługi Azure Backup](media/operations-management-suite-architecture/backup.png)

## <a name="azure-site-recovery"></a>Azure Site Recovery
Usługa [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) organizuje replikację, pracę w trybie failover i powrót po awarii maszyn wirtualnych i serwerów fizycznych. Dane replikacji są wymieniane między hostami funkcji Hyper-V, funkcjami hypervisor programu VMware i serwerami fizycznymi w podstawowych i dodatkowych centrach danych lub między centrum danych i magazynem Azure.  Usługa Site Recovery przechowuje metadane w magazynach znajdujących się w określonym regionie geograficznym świadczenia usługi Azure. Żadne replikowane dane nie są przechowywane w usłudze Site Recovery.

Usługa Azure Site Recovery ma trzy podstawowe scenariusze replikacji.

**Replikacja maszyn wirtualnych funkcji Hyper-V**

* Jeśli maszyny wirtualne funkcji Hyper-V są zarządzane w chmurach programu VMM, można replikować do dodatkowego centrum danych lub do magazynu Azure.  Replikacja do platformy Azure odbywa się za pośrednictwem bezpiecznego połączenia internetowego.  Replikacja do dodatkowego centrum danych odbywa się za pośrednictwem sieci LAN.
* Jeśli maszyny wirtualne funkcji Hyper-V nie są zarządzane przez program VMM, można replikować tylko do magazynu Azure.  Replikacja do platformy Azure odbywa się za pośrednictwem bezpiecznego połączenia internetowego.

**Replikacja maszyn wirtualnych VMWare**

* Maszyny wirtualne VMware można replikować do dodatkowego centrum danych z uruchomionym programem VMware lub do magazynu Azure.  Replikacja do platformy Azure może odbywać się przy użyciu sieci VPN typu „lokacja do lokacji”, usługi Azure ExpressRoute lub bezpiecznego połączenia internetowego. Replikacja do dodatkowego centrum danych odbywa się za pośrednictwem kanału danych InMage Scout.

**Replikacja serwerów fizycznych z systemami Windows i Linux** 

* Serwery fizyczne można replikować do dodatkowego centrum danych lub magazynu Azure. Replikacja do platformy Azure może odbywać się przy użyciu sieci VPN typu „lokacja do lokacji”, usługi Azure ExpressRoute lub bezpiecznego połączenia internetowego. Replikacja do dodatkowego centrum danych odbywa się za pośrednictwem kanału danych InMage Scout.  Usługa Azure Site Recovery obejmuje rozwiązanie pakietu OMS, które wyświetla niektóre statystyki, ale dla wszystkich operacji wymagane jest użycie witryny Azure Portal.

![Architektura wysokiego poziomu usługi Azure Site Recovery](media/operations-management-suite-architecture/site-recovery.png)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o usłudze [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics).
* Dowiedz się więcej o usłudze [Azure Automation](https://azure.microsoft.com/documentation/services/automation).
* Dowiedz się więcej o usłudze [Azure Backup](http://azure.microsoft.com/documentation/services/backup).
* Dowiedz się więcej o usłudze [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery).

