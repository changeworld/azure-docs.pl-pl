---
title: Konfigurowanie ustawień źródła odzyskiwania po awarii VMware na platformie Azure za pomocą usługi Azure Site Recovery
description: W tym artykule opisano sposób konfigurowania środowiska lokalnego w celu replikowania maszyn wirtualnych VMware na platformie Azure za pomocą usługi Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: ramamill
ms.openlocfilehash: ff01aed92669acb193ff149ea9298550134f42a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257058"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>Konfigurowanie środowiska źródłowego replikacji VMware na platformie Azure

W tym artykule opisano sposób konfigurowania środowiska lokalnego źródła w celu replikowania maszyn wirtualnych VMware na platformie Azure. Ten artykuł zawiera kroki dotyczące wybierania scenariusza replikacji, konfigurowania komputera lokalnego jako serwera konfiguracji odzyskiwania lokacji i automatycznego odnajdywania lokalnych maszyn wirtualnych.

## <a name="prerequisites"></a>Wymagania wstępne

W artykule założono, że masz już:

- Planowanie wdrożenia za pomocą [programu Azure Site Recovery Deployment Planner](site-recovery-deployment-planner.md). Pomaga to przydzielić wystarczającą przepustowość, na podstawie dziennej szybkości zmiany danych, aby osiągnąć żądany cel punktu odzyskiwania (RPO).
- [Konfigurowanie zasobów](tutorial-prepare-azure.md) w [witrynie Azure portal](https://portal.azure.com).
- [Skonfiguruj lokalne oprogramowanie VMware,](vmware-azure-tutorial-prepare-on-premises.md)w tym dedykowane konto do automatycznego odnajdowania.

## <a name="choose-your-protection-goals"></a>Wybierz swoje cele ochrony

1. W obszarze **Magazyny usługi Recovery Services** wybierz nazwę magazynu. W tym scenariuszu jest używany magazyn **ContosoVMVault**.
2. W obszarze **Wprowadzenie** wybierz pozycję Site Recovery. Następnie wybierz pozycję **Przygotowywanie infrastruktury**.
3. W **celu** > Ochrona**Gdzie znajdują się twoje maszyny**, wybierz opcję **Lokalnie**.
4. W obszarze **Gdzie chcesz zreplikować maszyny** wybierz pozycję **Na platformę Azure**.
5. W obszarze **Czy maszyny są zwirtualizowane** wybierz pozycję **Tak, z funkcją VMware vSphere Hypervisor**. Następnie wybierz przycisk **OK**.

## <a name="set-up-the-configuration-server"></a>Konfigurowanie serwera konfiguracji

Serwer konfiguracji można skonfigurować jako lokalną maszynę wirtualną VMware za pomocą szablonu OVA (Open Virtualization Application). [Dowiedz się więcej](concepts-vmware-to-azure-architecture.md) o składnikach, które zostaną zainstalowane na maszynie wirtualnej VMware.

1. Dowiedz się więcej o [wymaganiach wstępnych](vmware-azure-deploy-configuration-server.md#prerequisites) wdrożenia serwera konfiguracji.
2. [Sprawdź numery pojemności](vmware-azure-deploy-configuration-server.md#sizing-and-capacity-requirements) dla wdrożenia.
3. [Pobierz](vmware-azure-deploy-configuration-server.md#download-the-template) i [zaimportuj](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) szablon ova, aby skonfigurować lokalną maszynę wirtualną VMware, która uruchamia serwer konfiguracji. Licencja dostarczona z szablonem jest licencją ewaluacją i jest ważna przez 180 dni. Po upływie tego okresu klient musi aktywować okna za pomocą zakupionej licencji.
4. Włącz maszynę wirtualną VMware i [zarejestruj ją](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services) w magazynie usług odzyskiwania.

## <a name="azure-site-recovery-folder-exclusions-from-antivirus-program"></a>Wykluczenia folderów usługi Azure Site Recovery z programu antywirusowego

### <a name="if-antivirus-software-is-active-on-source-machine"></a>Jeśli oprogramowanie antywirusowe jest aktywne na komputerze źródłowym

Jeśli komputer źródłowy ma aktywne oprogramowanie antywirusowe, folder instalacyjny powinien zostać wykluczony. Tak więc, wyklucz folder *C:\ProgramData\ASR\agent* dla replikacji gładkiej.

### <a name="if-antivirus-software-is-active-on-configuration-server"></a>Jeśli oprogramowanie antywirusowe jest aktywne na serwerze konfiguracyjnym

Wyklucz następujące foldery z oprogramowania antywirusowego w celu uzyskania płynnej replikacji i uniknięcia problemów z łącznością

- C:\Program Files\Microsoft Azure Recovery Services Agent.
- C:\Program Files\Dostawca odzyskiwania witryny platformy Microsoft Azure
- C:\Program Files\Microsoft Azure Site Recovery Configuration Manager 
- C:\Program Files\Microsoft Azure Site Recovery Error Collection Tool 
  - C:\trzecia strona
  - C:\temp
  - C:\truskawka
  - C:\ProgramData\MySQL
  - C:\Pliki programu (x86)\MySQL
  - C:\ProgramData\ASR
  - C:\ProgramData\Microsoft Azure Site Recovery
  - C:\ProgramData\ASRLogs
  - C:\ProgramData\ASRSetupLogs
  - C:\ProgramData\LoguploadServiceLogs
  - C:\inetpub
  - Katalog instalacji serwera odzyskiwania lokacji. Na przykład: E:\Pliki programów (x86)\Odzyskiwanie witryny platformy Microsoft Azure

### <a name="if-antivirus-software-is-active-on-scale-out-process-servermaster-target"></a>Jeśli oprogramowanie antywirusowe jest aktywne na skalowawce serwer procesu/główny cel

Wyklucz następujące foldery z oprogramowania antywirusowego

1. C:\Pliki programów\Agent usług odzyskiwania platformy Microsoft Azure
2. C:\ProgramData\ASR
3. C:\ProgramData\ASRLogs
4. C:\ProgramData\ASRSetupLogs
5. C:\ProgramData\LoguploadServiceLogs
6. C:\ProgramData\Microsoft Azure Site Recovery
7. Katalog instalacji serwera serwera z równoważeniem obciążenia usługi Azure Site Recovery, przykład: C:\Program Files (x86)\Microsoft Azure Site Recovery


## <a name="next-steps"></a>Następne kroki
[Konfigurowanie środowiska docelowego](./vmware-azure-set-up-target.md) 
