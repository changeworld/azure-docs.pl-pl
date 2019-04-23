---
title: Konfigurowanie środowiska źródłowego dla oprogramowania VMware do platformy Azure replikację za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania środowiska lokalnego do replikowania maszyn wirtualnych programu VMware do platformy Azure za pomocą usługi Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: ramamill
ms.openlocfilehash: 075f86b24e2915d9689db8097889a830bade74c5
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149363"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>Konfigurowanie środowiska źródłowego dla oprogramowania VMware do platformy Azure replikacji

W tym artykule opisano sposób konfigurowania środowiska źródłowego w środowisku lokalnym replikowania maszyn wirtualnych programu VMware do platformy Azure. Obejmuje kroki wybierania scenariusza replikacji konfigurowanie na komputerze lokalnym jako serwera konfiguracji usługi Site Recovery, a automatyczne odnajdowanie lokalnych maszyn wirtualnych. 

## <a name="prerequisites"></a>Wymagania wstępne

Tego artykułu przyjęto założenie, że masz już:

- Zaplanowane wdrożenie za pomocą [planista wdrażania usługi Azure Site Recovery](site-recovery-deployment-planner.md). Dzięki temu można przydzielić odpowiednią przepustowość zgodnie z częstotliwością dziennych zmian danych, aby spełnić swoje żądany cel punktu odzyskiwania (RPO).
- [Konfigurowanie zasobów](tutorial-prepare-azure.md) w [witryny Azure portal](https://portal.azure.com).
- [Konfigurowanie lokalnego wdrożenia oprogramowania VMware](vmware-azure-tutorial-prepare-on-premises.md), łącznie z dedykowanego konta do automatycznego odnajdowania.

## <a name="choose-your-protection-goals"></a>Wybranie celów ochrony

1. W obszarze **Magazyny usługi Recovery Services** wybierz nazwę magazynu. W tym scenariuszu jest używany magazyn **ContosoVMVault**.
2. W obszarze **Wprowadzenie** wybierz pozycję Site Recovery. Następnie wybierz pozycję **Przygotowywanie infrastruktury**.
3. W obszarze **Cel ochrony** > **Gdzie znajdują się maszyny** wybierz pozycję **Lokalne**.
4. W obszarze **Gdzie chcesz zreplikować maszyny** wybierz pozycję **Na platformę Azure**.
5. W obszarze **Czy maszyny są zwirtualizowane** wybierz pozycję **Tak, z funkcją VMware vSphere Hypervisor**. Następnie wybierz przycisk **OK**.

## <a name="set-up-the-configuration-server"></a>Konfigurowanie serwera konfiguracji

Możesz skonfigurować serwer konfiguracji jako maszyny Wirtualnej VMware w środowisku lokalnym za pomocą szablonu usługi Open wirtualizacji aplikacji (OVA). [Dowiedz się więcej](concepts-vmware-to-azure-architecture.md) o składnikach, które zostaną zainstalowane na maszynie Wirtualnej programu VMware.

1. Dowiedz się więcej o [wymagania wstępne](vmware-azure-deploy-configuration-server.md#prerequisites) dla wdrożenia serwera konfiguracji.
2. [Sprawdź pojemność liczby](vmware-azure-deploy-configuration-server.md#capacity-planning) dla wdrożenia.
3. [Pobierz](vmware-azure-deploy-configuration-server.md#download-the-template) i [zaimportować](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) szablon OVA, aby skonfigurować lokalnych zasobów programu VMware maszynę Wirtualną, która działa na serwerze konfiguracji. Licencja dostarczane z szablonem jest licencją wersji ewaluacyjnej i jest ważna przez 180 dni. Post tego okresu klient musi wykonać aktywację systemu windows za pomocą licencji uzyskiwanych.
4. Włącz na maszynie Wirtualnej programu VMware i [go zarejestrować](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services) w usługi Recovery Services vault.

## <a name="azure-site-recovery-folder-exclusions-from-antivirus-program"></a>Usługa Azure Site Recovery wykluczenia folderów z programu antywirusowego

### <a name="if-antivirus-software-is-active-on-source-machine"></a>Jeśli oprogramowanie antywirusowe jest aktywna na maszynie źródłowej

Jeśli maszyna źródłowa ma aktywne oprogramowania antywirusowego, należy wykluczyć folder instalacji. Dlatego wykluczyć folder *C:\ProgramData\ASR\agent* smooth replikacji.

### <a name="if-antivirus-software-is-active-on-configuration-server"></a>Jeśli oprogramowanie antywirusowe jest aktywna na serwerze konfiguracji

Wyklucz następujące foldery z oprogramowania antywirusowego do sprawnego replikacji i aby uniknąć problemów z łącznością

- Agent C:\Program Files\Microsoft Azure Recovery Services.
- Dostawca usługi C:\Program Files\Microsoft Azure Site Recovery
- C:\Program Files\Microsoft Azure Site Recovery Configuration Manager 
- Narzędzie do C:\Program Files\Microsoft usługi Azure Site Recovery błąd zbierania 
  - C:\thirdparty
  - C:\Temp
  - C:\strawberry
  - C:\ProgramData\MySQL
  - C:\Program Files (x86)\MySQL
  - C:\ProgramData\ASR
  - C:\ProgramData\Microsoft Azure Site Recovery
  - C:\ProgramData\ASRLogs
  - C:\ProgramData\ASRSetupLogs
  - C:\ProgramData\LogUploadServiceLogs
  - C:\Inetpub
  - Katalog instalacji serwera usługi ASR. Na przykład: E:\Program pliki (x86) \Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-scale-out-process-servermaster-target"></a>Jeśli oprogramowanie antywirusowe jest aktywny w skali w poziomie przetwarzania server/Master Target

Wyklucz następujące foldery z oprogramowania antywirusowego

1. Agent usług C:\Program Files\Microsoft Azure Recovery Services
2. C:\ProgramData\ASR
3. C:\ProgramData\ASRLogs
4. C:\ProgramData\ASRSetupLogs
5. C:\ProgramData\LogUploadServiceLogs
6. C:\ProgramData\Microsoft Azure Site Recovery
7. Usługa ASR obciążenia procesu o zrównoważonym obciążeniu katalog instalacji serwera, przykład: C:\Program Files (x86) \Microsoft Azure Site Recovery


## <a name="next-steps"></a>Kolejne kroki
[Konfigurowanie środowiska docelowego](./vmware-azure-set-up-target.md) 
