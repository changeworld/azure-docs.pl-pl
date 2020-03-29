---
title: Informacje o konfiguracji/przetwarzaniu usługi Azure Site Recovery/głównych serwerach docelowych
description: Ten artykuł zawiera omówienie konfiguracji, procesu i głównych serwerów docelowych używanych podczas konfigurowania odzyskiwania po awarii lokalnych maszyn wirtualnych VMware na platformie Azure za pomocą usługi Azure Site Recovery
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: cd5ded18d1a8f1f5fd96212d37725bb5db13002f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062087"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>Informacje o składnikach odzyskiwania witryny (konfiguracja, proces, główny cel)

W tym artykule opisano konfigurację, proces i główne serwery docelowe używane przez usługę [Site Recovery](site-recovery-overview.md) do replikowania maszyn wirtualnych vmware i serwerów fizycznych na platformie Azure.

## <a name="configuration-server"></a>Serwer konfiguracji

Aby odzyskiwaniu po awarii lokalnych maszyn wirtualnych VMware i serwerów fizycznych, wdrożyć lokalny serwer konfiguracji odzyskiwania lokacji.

**Ustawienie** | **Szczegóły** | **Linki**
--- | --- | ---
**Składniki**  | Komputer serwera konfiguracji uruchamia wszystkie lokalne składniki odzyskiwania lokacji, które obejmują serwer konfiguracji, serwer przetwarzania i główny serwer docelowy.<br/><br/> Podczas konfigurowania serwera konfiguracji wszystkie składniki są instalowane automatycznie. | [Przeczytaj](vmware-azure-common-questions.md#configuration-server) często zadawane pytania dotyczące serwera konfiguracji.
**Roli** | Serwer konfiguracji służy do koordynowania komunikacji między środowiskiem lokalnym i platformą Azure oraz do zarządzania replikacją danych. | Dowiedz się więcej o architekturze odzyskiwania po awarii [vmware](vmware-azure-architecture.md) i [fizycznego serwera](physical-azure-architecture.md) na platformie Azure.
**Wymagania dotyczące VMware** | Aby odzyskiwaniu po awarii lokalnych maszyn wirtualnych VMware, należy zainstalować i uruchomić serwer konfiguracji jako lokalną, wysoce dostępną maszynę wirtualną VMware. | [Dowiedz się więcej o](vmware-azure-deploy-configuration-server.md#prerequisites) wymaganiach wstępnych.
**Wdrożenie VMware** | Zaleca się wdrożenie serwera konfiguracji przy użyciu pobranego szablonu ova. Ta metoda umożliwia proste skonfigurowanie serwera konfiguracji, który spełnia wszystkie wymagania i wymagania wstępne.<br/><br/> Jeśli z jakiegoś powodu nie można wdrożyć maszyny wirtualnej VMware przy użyciu szablonu ova, można skonfigurować maszyny serwera konfiguracji ręcznie, jak opisano poniżej dla odzyskiwania po awarii komputera fizycznego. | [Wdrażanie](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template) za pomocą szablonu ova.
**Wymagania dotyczące serwera fizycznego** | W przypadku odzyskiwania po awarii na lokalnych serwerach fizycznych można wdrożyć serwer konfiguracji ręcznie. | [Dowiedz się więcej o](physical-azure-set-up-source.md#prerequisites) wymaganiach wstępnych.
**Wdrożenie serwera fizycznego** | Jeśli nie można go zainstalować jako maszyny Wirtualnej VMware, można go zainstalować na serwerze fizycznym. | [Wdrażanie](physical-azure-set-up-source.md#set-up-the-source-environment) serwera konfiguracji ręcznie.

## <a name="process-server"></a>Serwer przetwarzania

Serwer przetwarzania obsługuje dane replikacji podczas pracy awaryjnej i powrotu po awarii i instaluje usługę mobilności dla lokalnych maszyn wirtualnych VMware i serwerów fizycznych.

**Ustawienie** | **Szczegóły** | **Linki**
--- | --- | ---
**wdrażania**  | Domyślnie po wdrożeniu serwera konfiguracji serwer przetwarzania jest instalowany. <br/><br/> Lokalny serwer procesów jest potrzebny do odzyskiwania po awarii i replikacji lokalnych maszyn wirtualnych VMware i serwerów fizycznych. | [Dowiedz się więcej](vmware-azure-architecture.md#architectural-components).
**Rola (lokalna)** | Odbiera dane replikacji z maszyn włączonych do replikacji. <br/><br/> Optymalizuje dane replikacji za pomocą buforowania, kompresji i szyfrowania i wysyła je do usługi Azure Storage. <br/><br/> Wykonuje instalację wypychaną usługi Mobilności odzyskiwania lokacji na lokalnych maszynach wirtualnych VMware i serwerach fizycznych, które mają być replikowane. <br/><br/> Wykonuje automatyczne odnajdowanie maszyn lokalnych. | [Dowiedz się więcej](vmware-azure-enable-replication.md).
**Rola (powrót po awarii z platformy Azure)** | Po przebażaniu w błąd z lokacji lokalnej, skonfigurować serwer procesów na platformie Azure, jako maszyny Wirtualnej platformy Azure, do obsługi powrotu po awarii do lokalizacji lokalnej.<br/><br/> Serwer procesów na platformie Azure jest tymczasowy. Maszyna wirtualna platformy Azure można usunąć po zakończeniu powrotu po awarii. | [Dowiedz się więcej](vmware-azure-set-up-process-server-azure.md).
**Skalowanie** | W przypadku większych wdrożeń lokalnie można skonfigurować dodatkowe serwery procesów skalowanych w poziomie. Dodatkowe serwery skalują pojemność w poziomie, obsługując większą liczbę maszyn replikujących i większe ilości ruchu replikacji.<br/><br/> Można przenosić maszyny między dwoma serwerami przetwarzania, aby załadować ruch replikacji równoważenia. | [Dowiedz się więcej](vmware-azure-set-up-process-server-scale.md).

## <a name="master-target-server"></a>Główny serwer docelowy

Główny serwer docelowy służy do obsługi replikacji danych podczas powrotu po awarii z platformy Azure.

- Domyślnie główny serwer docelowy jest instalowany na serwerze konfiguracji.
- W przypadku dużych wdrożeń można dodać dodatkowy, oddzielny serwer docelowy dla powrotu po awarii.

## <a name="next-steps"></a>Następne kroki

- Przejrzyj [architekturę](vmware-azure-architecture.md) odzyskiwania po awarii maszyn wirtualnych vmware i serwerów fizycznych.
- Przejrzyj [wymagania i wymagania wstępne dotyczące](vmware-physical-azure-support-matrix.md) odzyskiwania po awarii maszyn wirtualnych vmware i serwerów fizycznych na platformie Azure.
