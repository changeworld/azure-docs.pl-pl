---
title: Konfigurowanie środowiska źródłowego dla odzyskiwania po awarii maszyn wirtualnych VMware na platformę Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania środowiska w środowisku lokalnym na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware na platformę Azure za pomocą usługi Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/29/2018
ms.author: ramamill
ms.openlocfilehash: 44b1cac461fa49b4bea19b7cf98f6038eb264492
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230730"
---
# <a name="set-up-the-source-environment-for-disaster-recovery-of-vmware-vms-to-azure"></a>Konfigurowanie środowiska źródłowego dla odzyskiwania po awarii maszyn wirtualnych programu VMware do platformy Azure

W tym artykule opisano sposób konfigurowania środowiska źródłowego w środowisku lokalnym replikowania maszyn wirtualnych programu VMware do platformy Azure. Obejmuje kroki wybierania scenariusza replikacji konfigurowanie na komputerze lokalnym jako serwera konfiguracji usługi Site Recovery, a automatyczne odnajdowanie lokalnych maszyn wirtualnych. 

## <a name="prerequisites"></a>Wymagania wstępne

Tego artykułu przyjęto założenie, że masz już:

- Zaplanowane wdrożenie za pomocą [planista wdrażania usługi Azure Site Recovery](site-recovery-deployment-planner.md). Dzięki temu można przydzielić odpowiednią przepustowość zgodnie z częstotliwością dziennych zmian danych, aby spełnić swoje żądany cel punktu odzyskiwania (RPO).
- [Konfigurowanie zasobów](tutorial-prepare-azure.md) w [witryny Azure portal](http://portal.azure.com).
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
3. [Pobierz](vmware-azure-deploy-configuration-server.md#download-the-template) i [zaimportować](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) szablon OVA, aby skonfigurować lokalnych zasobów programu VMware maszynę Wirtualną, która działa na serwerze konfiguracji. Licencja dostarczane z szablonem jest licencji ewaluacyjnej i jest ważna przez 180 dni. Post tego okresu klient musi wykonać aktywację systemu windows za pomocą licencji uzyskiwanych.
4. Włącz na maszynie Wirtualnej programu VMware i [go zarejestrować](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services) w usługi Recovery Services vault.

## <a name="azure-site-recovery-folder-exclusions-from-antivirus-program"></a>Usługa Azure Site Recovery wykluczenia folderów z programu antywirusowego

### <a name="if-antivirus-software-is-active-on-source-machine"></a>Jeśli oprogramowanie antywirusowe jest aktywna na maszynie źródłowej

Jeśli maszyna źródłowa ma aktywne oprogramowania antywirusowego, należy wykluczyć folder instalacji. Dlatego wykluczyć folder *C:\ProgramData\ASR\agent* smooth replikacji.

### <a name="if-antivirus-software-is-active-on-configuration-server"></a>Jeśli oprogramowanie antywirusowe jest aktywna na serwerze konfiguracji

Wyklucz następujące foldery z oprogramowania antywirusowego do sprawnego replikacji i aby uniknąć problemów z łącznością

 1. Agent C:\Program Files\Microsoft Azure Recovery Services.
 2. Dostawca usługi C:\Program Files\Microsoft Azure Site Recovery
 3. C:\Program Files\Microsoft Azure Site Recovery Configuration Manager 
 4. Narzędzie do C:\Program Files\Microsoft usługi Azure Site Recovery błąd zbierania 
 5. C:\thirdparty
 6. C:\Temp
 7. C:\strawberry
 8. C:\ProgramData\MySQL
 9. C:\Program pliki (x86) \MySQL
 10. C:\ProgramData\ASR
 11. C:\ProgramData\Microsoft Azure Site Recovery
 12. C:\ProgramData\ASRLogs
 13. C:\ProgramData\ASRSetupLogs
 14. C:\ProgramData\LogUploadServiceLogs
 15. C:\Inetpub
 16. Katalog instalacji serwera usługi ASR. Na przykład: pliki E:\Program (x86) \Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-scale-out-process-servermaster-target"></a>Jeśli oprogramowanie antywirusowe jest aktywny w skali w poziomie przetwarzania server/Master Target

Wyklucz następujące foldery z oprogramowania antywirusowego

1. Agent usług C:\Program Files\Microsoft Azure Recovery Services
2. C:\ProgramData\ASR
3. C:\ProgramData\ASRLogs
4. C:\ProgramData\ASRSetupLogs
5. C:\ProgramData\LogUploadServiceLogs
6. C:\ProgramData\Microsoft Azure Site Recovery
7. Funkcja automatycznego odzyskiwania systemu ze zrównoważonym obciążeniem: katalog instalacji serwera przetwarzania, przykład: C:\Program Files (x86) \Microsoft Azure Site Recovery

## <a name="common-issues"></a>Typowe problemy
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>Kolejne kroki
[Konfigurowanie środowiska docelowego](./vmware-azure-set-up-target.md) na platformie Azure.
