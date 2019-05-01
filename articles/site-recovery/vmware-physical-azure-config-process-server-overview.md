---
title: Informacje konfiguracji usługi Azure Site Recovery, procesy i główne serwery docelowe | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie konfiguracji, procesy i główne serwery docelowe przy użyciu podczas konfigurowania odzyskiwania po awarii lokalnych maszyn wirtualnych programu VMware na platformę Azure za pomocą usługi Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: 78aed4fceab83d942927486cd7ae46a32dd6ca09
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927555"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>O składniki usługi Site Recovery (Konfiguracja, przetwarzania, główny serwer docelowy)

W tym artykule opisano konfigurację, procesy i główne serwery docelowe używanych podczas replikowania maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure za pomocą [Site Recovery](site-recovery-overview.md) usługi.

## <a name="configuration-server"></a>Serwer konfiguracji

Odzyskiwanie po awarii lokalnych maszyn wirtualnych z programu VMware i serwerów fizycznych na potrzeby należy odzyskiwania lokacji serwera konfiguracji wdrożone lokalnie.

**Ustawienie** | **Szczegóły** | **Łącza**
--- | --- | ---
**Składniki**  | Konfiguracja komputera serwera uruchamia wszystkie składniki usługi Site Recovery w środowisku lokalnym, które obejmują serwer konfiguracji, serwer przetwarzania oraz główny serwer docelowy.<br/><br/> Wszystkie składniki są instalowane automatycznie podczas konfigurowania serwera konfiguracji. | [Odczyt](vmware-azure-common-questions.md#configuration-server) serwera konfiguracji — często zadawane pytania.
**Rola** | Serwer konfiguracji służy do koordynowania komunikacji między środowiskiem lokalnym i platformą Azure oraz do zarządzania replikacją danych. | Dowiedz się więcej na temat architektury [VMware](vmware-azure-architecture.md) i [serwera fizycznego](physical-azure-architecture.md) odzyskiwania po awarii na platformie Azure.
**Wymagania oprogramowania VMware** | Do odzyskiwania po awarii lokalnych maszyn wirtualnych programu VMware należy zainstalować i uruchomić serwer konfiguracji jako lokalne, maszyna wirtualna VMware o wysokiej dostępności. | [Dowiedz się więcej o](vmware-azure-deploy-configuration-server.md#prerequisites) wymagań wstępnych.
**Wdrożenia oprogramowania VMware** | Zaleca się wdrożenie serwera konfiguracji, za pomocą pobranego szablonu OVA. Ta metoda zapewnia po prostu sposobem na skonfigurowanie serwera konfiguracji, który spełnia wszystkie wymagania i wymagania wstępne.<br/><br/> Jeśli z jakiegoś powodu nie możesz wdrożyć maszynę Wirtualną VMware przy użyciu szablonu usługi OVA, maszyn serwera konfiguracji można skonfigurować ręcznie, zgodnie z opisem poniżej do odzyskiwania po awarii maszyny fizycznej. | [Wdrażanie](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template) przy użyciu szablonu OVA.
**Wymagania dotyczące serwera fizycznego** | Do odzyskiwania po awarii na lokalnych serwerach fizycznych możesz ręcznie wdrożyć serwer konfiguracji. | [Dowiedz się więcej o](/physical-azure-set-up-source.md#prerequisites) wymagań wstępnych.
**Wdrażanie serwera fizycznego** | Jeśli nie można zainstalować jako Maszynę wirtualną VMware, można zainstalować go na serwerze fizycznym. | [Wdrażanie](physical-azure-set-up-source.md#set-up-the-source-environment) serwera konfiguracji ręcznie.


## <a name="process-server"></a>Serwer przetwarzania

**Ustawienie** | **Szczegóły** | **Łącza**
--- | --- | ---
**Wdrożenie**  | Do odzyskiwania po awarii replikacji lokalnych maszyn wirtualnych z programu VMware i serwerów fizycznych należy serwer przetwarzania w środowisku lokalnym. Domyślnie serwer przetwarzania jest zainstalowany na serwerze konfiguracji, podczas jego wdrażania. | [Dowiedz się więcej](vmware-azure-architecture.md?#architectural-components).
**Rola (w środowisku lokalnym** | -Odbiera dane replikacji z maszyn włączona replikacja.<br/> -Optymalizuje dane replikacji z pamięci podręcznej, kompresji i szyfrowania, a następnie wysyła je do usługi Azure Storage.<br/> -Wykonuje instalacji wypychanej usługi mobilności usługi Site Recovery na lokalnych maszynach wirtualnych z programu VMware i serwerów fizycznych, które mają być replikowane.<br/> -Przeprowadza automatyczne odnajdywanie maszyn lokalnych. | [Dowiedz się więcej](vmware-physical-azure-config-process-server-overview.md#process-server). 
**Rola (powrót po awarii z platformy Azure)** | Po przejściu w tryb failover z lokacji lokalnej możesz skonfigurować serwer przetwarzania na platformie Azure, jako Maszynę wirtualną platformy Azure, do obsługi powrotu po awarii w lokalizacji lokalnej.<br/><br/> Serwer przetwarzania na platformie Azure jest tymczasowe. Po zakończeniu powrotu po awarii można usunąć maszyny Wirtualnej platformy Azure. | [Dowiedz się więcej](vmware-azure-set-up-process-server-azure.md).
**Skalowanie** | W przypadku większych wdrożeń w środowisku lokalnym, możesz skonfigurować dodatkowe proces skalowania w poziomie serwerów. Dodatkowe serwery skalowanie pojemności, dzięki obsłudze większej liczby replikowanych maszyn i większe woluminy ruch związany z replikacją.<br/><br/> Maszyny można przenosić między dwoma serwerami procesu, aby załadować saldo ruch związany z replikacją. | [Dowiedz się więcej](vmware-azure-set-up-process-server-scale.md),


## <a name="master-target-server"></a>Główny serwer docelowy

Główny serwer docelowy służy do obsługi replikacji danych podczas powrotu po awarii z platformy Azure.

- Jest instalowana domyślnie na serwerze konfiguracji.
- W przypadku dużych wdrożeń możesz dodać dodatkowe, oddzielny główny serwer docelowy do powrotu po awarii.


## <a name="next-steps"></a>Kolejne kroki
- Przegląd [architektury](/vmware-azure-architecture.md) do odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych.
- Przegląd [wymogi i wymagania wstępne](vmware-physical-azure-support-matrix.md) do odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure. 
