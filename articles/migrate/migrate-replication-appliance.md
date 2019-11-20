---
title: Architektura urządzenia replikacji Azure Migrate
description: Zawiera omówienie urządzenia replikacji Azure Migrate na potrzeby migracji opartej na agentach.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: ba14767bde5d6cdca3a82dbe4e8a115ec25cc911
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186551"
---
# <a name="replication-appliance"></a>Urządzenie replikacji

W tym artykule opisano urządzenie replikacji używane przez Azure Migrate: Ocena serwera podczas migrowania maszyn wirtualnych VMware, maszyn fizycznych i prywatnych/publicznych maszyn wirtualnych do platformy Azure przy użyciu migracji opartej na agentach. 

Narzędzie jest dostępne w centrum [Azure Migrate](migrate-overview.md) . Centrum udostępnia natywne narzędzia do oceny i migracji, a także narzędzia z innych usług platformy Azure oraz niezależnych dostawców oprogramowania (ISV) innych firm.


## <a name="appliance-overview"></a>Przegląd urządzenia

Urządzenie replikacji jest wdrażane jako pojedyncza maszyna lokalna jako maszyna wirtualna VMware lub serwer fizyczny. Działa:
- **Urządzenie replikacji**: urządzenie replikacji koordynuje komunikację i zarządza replikacją danych dla lokalnych maszyn wirtualnych VMware i serwerów fizycznych replikowanych do platformy Azure.
- **Serwer przetwarzania**: serwer przetwarzania, który jest instalowany domyślnie na urządzeniu replikacji, i wykonuje następujące czynności:
    - **Brama replikacji**: działa jako brama replikacji. Odbiera dane replikacji z maszyn, na których włączono replikację. Optymalizuje dane replikacji za pomocą buforowania, kompresji i szyfrowania, a następnie wysyła je do platformy Azure.
    - **Instalator agenta**: wykonuje instalację wypychaną usługi mobilności. Ta usługa musi być zainstalowana i uruchomiona na każdej maszynie lokalnej, która ma zostać zreplikowana do migracji.

## <a name="appliance-deployment"></a>Wdrażanie urządzenia

**Wdróż jako** | **Używane dla** | **Szczegóły**
--- | --- |  ---
Maszyna wirtualna VMware | Zwykle używany podczas migrowania maszyn wirtualnych VMware przy użyciu narzędzia migracji Azure Migrate z migracją opartą na agentach. | Szablon komórki jajowe można pobrać z centrum Azure Migrate i zaimportować do vCenter Server, aby utworzyć maszynę wirtualną urządzenia.
Maszyna fizyczna | Używane podczas migrowania lokalnych serwerów fizycznych, jeśli nie masz infrastruktury VMware lub nie można utworzyć maszyny wirtualnej VMware przy użyciu szablonu komórki jajowe. | Należy pobrać Instalatora oprogramowania z centrum Azure Migrate i uruchomić go w celu skonfigurowania komputera.

## <a name="appliance-deployment-requirements"></a>Wymagania dotyczące wdrażania urządzeń

[Zapoznaj](migrate-support-matrix-vmware.md#agent-based-migration-replication-appliance-requirements) się z wymaganiami dotyczącymi wdrażania.



## <a name="appliance-license"></a>Licencja urządzenia
Urządzenie jest dostarczane z licencją ewaluacyjną systemu Windows Server 2016, która jest ważna przez 180 dni. Jeśli okres próbny zbliża się do wygaśnięcia, zalecamy pobranie i wdrożenie nowego urządzenia albo Aktywowanie licencji na maszynę wirtualną urządzenia.

## <a name="replication-process"></a>Proces replikacji

1. Po włączeniu replikacji dla maszyny wirtualnej rozpocznie się replikacja początkowa do usługi Azure Storage przy użyciu określonych zasad replikacji. 
2. Ruch jest replikowany do publicznych punktów końcowych usługi Azure Storage za pośrednictwem Internetu. Replikowanie ruchu za pośrednictwem wirtualnej sieci prywatnej (VPN) typu lokacja-lokacja z lokacji lokalnej do platformy Azure nie jest obsługiwane.
3. Po zakończeniu replikacji początkowej rozpoczyna się replikacja różnicowa. Śledzone zmiany dla maszyny są rejestrowane.
4. Komunikacja odbywa się w następujący sposób:
    - Maszyny wirtualne komunikują się z urządzeniem replikacji na porcie HTTPS 443 ruchu przychodzącego na potrzeby zarządzania replikacją.
    - Urządzenie replikacji organizuje replikację za pomocą platformy Azure przez port HTTPS 443.
    - Maszyny wirtualne wysyłają dane replikacji do serwera przetwarzania (uruchomionego na urządzeniu replikacji) na porcie HTTPS 9443 w ruchu przychodzącym. Ten port może być modyfikowany.
    - Serwer przetwarzania odbiera dane replikacji, optymalizuje je i szyfruje oraz wysyła do usługi Azure Storage przez port 443 wychodzące.
5. Dane replikacji są najpierw przechowywane na koncie magazynu pamięci podręcznej na platformie Azure. Te dzienniki są przetwarzane, a dane są przechowywane na dysku zarządzanym platformy Azure.

![Architektura](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Uaktualnienia urządzeń

Urządzenie jest uaktualniane ręcznie z centrum Azure Migrate. Zalecamy, aby zawsze była uruchamiana Najnowsza wersja.

1. W Azure Migrate > serwery > Azure Migrate: Ocena serwera, serwery infrastruktury, kliknij przycisk **serwery konfiguracji**.
2. W obszarze **serwery konfiguracji**w **wersji agenta** pojawia się łącze, gdy dostępna jest nowa wersja urządzenia replikacji. 
3. Pobierz instalatora na komputer z urządzeniem replikacji i zainstaluj uaktualnienie. Instalator wykryje bieżącą wersję uruchomioną na urządzeniu.
 
## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się, jak](tutorial-assess-vmware.md#set-up-the-appliance-vm) skonfigurować urządzenie dla programu VMware.
[Dowiedz się, jak](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) skonfigurować urządzenie dla funkcji Hyper-V.

