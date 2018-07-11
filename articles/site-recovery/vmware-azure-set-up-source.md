---
title: Konfigurowanie środowiska źródłowego dla oprogramowania VMware do platformy Azure replikację za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania środowiska lokalnego do replikowania maszyn wirtualnych programu VMware do platformy Azure za pomocą usługi Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: ramamill
ms.openlocfilehash: 1380c1bc820a815fae317a86fcd0ee4f46dd9aa5
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952658"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>Konfigurowanie środowiska źródłowego dla oprogramowania VMware do platformy Azure replikacji

W tym artykule opisano sposób konfigurowania środowiska źródłowego w środowisku lokalnym replikowania maszyn wirtualnych programu VMware do platformy Azure. Obejmuje kroki wybierania scenariusza replikacji konfigurowanie na komputerze lokalnym jako serwera konfiguracji usługi Site Recovery, a automatyczne odnajdowanie lokalnych maszyn wirtualnych. 

## <a name="prerequisites"></a>Wymagania wstępne

Tego artykułu przyjęto założenie, że masz już:
- [Konfigurowanie zasobów](tutorial-prepare-azure.md) w [witryny Azure portal](http://portal.azure.com).
- [Konfigurowanie lokalnego wdrożenia oprogramowania VMware](vmware-azure-tutorial-prepare-on-premises.md), łącznie z dedykowanego konta do automatycznego odnajdowania.



## <a name="choose-your-protection-goals"></a>Wybranie celów ochrony

1. W witrynie Azure portal przejdź do **usługi Recovery Services** bloku magazyn, a następnie wybierz swój magazyn.
2. W menu zasobów magazynu, przejdź do **wprowadzenie** > **Site Recovery** > **krok 1: Przygotowanie infrastruktury**  >  **Cel ochrony**.

    ![Wybieranie celów](./media/vmware-azure-set-up-source/choose-goals.png)
3. W **cel ochrony**, wybierz opcję **na platformę Azure**i wybierz polecenie **tak, przy użyciu oprogramowania VMware vSphere Hypervisor**. Następnie kliknij przycisk **OK**.

    ![Wybieranie celów](./media/vmware-azure-set-up-source/choose-goals2.png)

## <a name="set-up-the-configuration-server"></a>Konfigurowanie serwera konfiguracji

Możesz skonfigurować serwer konfiguracji jako maszyny Wirtualnej VMware w środowisku lokalnym za pomocą szablonu usługi Open wirtualizacji aplikacji (OVA). [Dowiedz się więcej](concepts-vmware-to-azure-architecture.md) o składnikach, które zostaną zainstalowane na maszynie Wirtualnej programu VMware.

1. Dowiedz się więcej o [wymagania wstępne](vmware-azure-deploy-configuration-server.md#prerequisites) dla wdrożenia serwera konfiguracji.
2. [Sprawdź pojemność liczby](vmware-azure-deploy-configuration-server.md#capacity-planning) dla wdrożenia.
3. [Pobierz](vmware-azure-deploy-configuration-server.md#download-the-template) i [zaimportować](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) szablon OVA, aby skonfigurować lokalnych zasobów programu VMware maszynę Wirtualną, która działa na serwerze konfiguracji. Licencja dostarczane z szablonem jest licencji ewaluacyjnej i jest ważna przez 180 dni. Post tego okresu klient musi wykonać aktywację systemu windows za pomocą licencji uzyskiwanych.
4. Włącz na maszynie Wirtualnej programu VMware i [go zarejestrować](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services) w usługi Recovery Services vault.


## <a name="add-the-vmware-account-for-automatic-discovery"></a>Dodaj konto programu VMware do automatycznego odnajdowania

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

## <a name="connect-to-the-vmware-server"></a>Połączenie z serwerem VMware

Aby zezwolić na usługi Azure Site Recovery w celu odnalezienia maszyn wirtualnych działających w środowisku w środowisku lokalnym, należy podłączyć serwer VMware vCenter lub hostami vSphere ESXi z usługą Site Recovery.

Wybierz **+ vCenter** można uruchomić połączenia serwera VMware vCenter lub hostem VMware vSphere ESXi.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>Typowe problemy
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Kolejne kroki
[Konfigurowanie środowiska docelowego](./vmware-azure-set-up-target.md) na platformie Azure.
