---
title: Architektura urządzenia replikacji usługi Azure Migrate | Dokumentacja firmy Microsoft
description: Zawiera omówienie urządzenia replikacji usługi Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: 4f4dc307bee4190a0e94ace493053e0cfd01150e
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811442"
---
# <a name="replication-appliance"></a>Urządzenie replikacji

W tym artykule opisano urządzenie replikacji używane przez usługę Azure Migrate: Ocena serwera podczas migrowania maszyn wirtualnych VMware, maszyny fizyczne i publicznego i prywatnego chmury maszyn wirtualnych na platformie Azure przy użyciu migracji do usługi oparte na agentach. 

Narzędzie to jest dostępne w [usługi Azure Migrate](migrate-overview.md) koncentratora. Piasta udostępnia natywnego narzędzia do oceny i migracji, a także narzędzi z innymi usługami platformy Azure i innych producentów niezależnych dostawców oprogramowania (ISV).


## <a name="appliance-overview"></a>Przegląd urządzenia

Urządzenie replikacji jest wdrażany jako pojedynczej maszynie, jako Maszynę wirtualną VMware lub serwer fizyczny lokalnych. Działa:
- **Urządzenie replikacji**: Urządzenie replikacji służy do koordynowania komunikacji oraz do zarządzania replikacją danych, dla lokalnych maszyn wirtualnych z programu VMware i serwerów fizycznych replikowanych do platformy Azure.
- **Serwer przetwarzania**: Serwer przetwarzania, który jest instalowany domyślnie na urządzeniu replikacji i wykonuje następujące czynności:
    - **Brama replikacji**: Działa jako brama replikacji. Go odbiera dane replikacji z maszyn włączona replikacja. Optymalizuje dane replikacji z pamięci podręcznej, kompresji i szyfrowania i wysyła je do platformy Azure.
    - **Instalator agenta**: Wykonanie instalacji wypychanej usługi mobilności. Ta usługa musi być zainstalowana i uruchomiona na każdej lokalnej maszyny, które mają być replikowane do migracji.

## <a name="appliance-deployment"></a>Wdrażanie urządzenia

**Wdróż jako** | **Używane dla** | **Szczegóły**
--- | --- |  ---
Maszyna wirtualna VMware | Zwykle używane podczas migracji maszyn wirtualnych VMware przy użyciu narzędzia migracji migracji platformy Azure z migracją oparte na agentach. | Pobierz szablon OVA z Centrum migracji platformy Azure, a następnie zaimportować do programu vCenter Server, aby utworzyć urządzenie maszyny Wirtualnej.
Komputer fizyczny | Używany podczas migracji lokalnych serwerów fizycznych, jeśli nie masz infrastruktury VMware lub jeśli nie można utworzyć maszynę Wirtualną VMware przy użyciu szablonu usługi OVA. | Pobierz instalatora oprogramowania z Centrum migracji platformy Azure, a następnie uruchom go, aby skonfigurować maszynę urządzenia.

## <a name="appliance-deployment-requirements"></a>Wymagania dotyczące wdrażania urządzenia

[Przegląd](migrate-support-matrix-vmware.md#agent-based-migration-replication-appliance-requirements) wymagań związanych z wdrażaniem.



## <a name="appliance-license"></a>Licencja urządzenia
Urządzenie jest powiązana z licencji ewaluacyjnej systemu Windows Server 2016, która jest ważna przez 180 dni. Jeśli okres próbny jest bliski wygaśnięcia, firma Microsoft zaleca, możesz pobrać i wdrożyć nowe urządzenie lub aktywacji licencji systemu operacyjnego urządzenia maszyny Wirtualnej.

## <a name="replication-process"></a>Proces replikacji

1. Po włączeniu replikacji dla maszyny Wirtualnej, rozpoczyna się Replikacja początkowa do usługi Azure storage, za pomocą zasad określonej replikacji. 
2. Ruch są replikowane do usługi Azure storage publicznych punktów końcowych za pośrednictwem Internetu. Replikowanie ruchu za pośrednictwem lokacja lokacja wirtualnej sieci prywatnej (VPN) z lokacji lokalnej do platformy Azure nie jest obsługiwane.
3. Po zakończeniu replikacji początkowej rozpoczyna się replikacja różnicowa. Śledzone zmiany dla maszyny są rejestrowane.
4. Komunikacja odbywa się w następujący sposób:
    - Maszyny wirtualne komunikować się z urządzeniem replikacji na porcie HTTPS 443 dla ruchu przychodzącego na potrzeby zarządzania replikacją.
    - Urządzenie replikacji organizuje replikację za pomocą platformy Azure za pośrednictwem portu HTTPS 443 dla ruchu wychodzącego.
    - Maszyny wirtualne wysyłają dane replikacji do serwera przetwarzania (uruchomionego na urządzeniu replikacji) na porcie HTTPS 9443 dla ruchu przychodzącego. Ten port może być modyfikowany.
    - Serwer przetwarzania odbiera dane replikacji, optymalizuje je szyfruje i wysyła je do usługi Azure storage za pośrednictwem portu 443 wychodzących.
5. Dane replikacji rejestruje pierwszy ziemi na koncie magazynu pamięci podręcznej na platformie Azure. Te dzienniki są przetwarzane i dane są przechowywane na platformie Azure dysku zarządzanego.

![Architektura](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Uaktualnienia urządzenia

Urządzenie jest uaktualnione ręcznie z poziomu Centrum migracji platformy Azure. Firma Microsoft zaleca, zawsze uruchomić najnowszą wersję.

1. W usłudze Azure wykonywana jest migracja > serwery > Usługa Azure Migrate: Ocena serwera, serwery infrastruktury kliknij **serwery konfiguracji**.
2. W **serwery konfiguracji**, pojawi się łącze **wersja agenta** gdy dostępna jest nowa wersja urządzenia replikacji. 
3. Pobierz Instalatora do replikacji maszyny urządzenia i zainstalować uaktualnienia. Instalator wykrywa, wersja bieżącego uruchomiona na urządzeniu.
 
## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się, jak](tutorial-assess-vmware.md#set-up-the-appliance-vm) skonfigurować urządzenie dla oprogramowania VMware.
[Dowiedz się, jak](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) skonfigurować urządzenie dla funkcji Hyper-V.

