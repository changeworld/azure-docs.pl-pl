---
title: Odzyskiwanie po awarii wielu dzierżawców maszyn wirtualnych vm z odzyskiwaniem po awarii usługi Azure Site Recovery
description: Zawiera omówienie obsługi usługi Azure Site Recovery dla odzyskiwania po awarii VMWare na platformie Azure w programie dla środowiska wielodostępnego (CSP).
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 840049265d3b6e4d2fddd794646bfd5691aab9a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083987"
---
# <a name="overview-of-multi-tenant-support-for-vmware-disaster-recovery-to-azure-with-csp"></a>Omówienie obsługi wielu dzierżaw dla odzyskiwania po awarii VMware na platformie Azure z usługą CSP

[Usługa Azure Site Recovery](site-recovery-overview.md) obsługuje środowiska z wieloma dzierżawami dla subskrypcji dzierżawy. Obsługuje również wiele dzierżaw dla subskrypcji dzierżawy, które są tworzone i zarządzane za pośrednictwem programu Microsoft Cloud Solution Provider (CSP).

Ten artykuł zawiera omówienie implementacji replikacji VMware z wieloma dzierżawami i zarządzania nimi.

## <a name="multi-tenant-environments"></a>Środowiska wielodostępne

Istnieją trzy główne modele wielodostępne:

* **Dostawca usług hostingu współdzielonego (HSP)**: Partner jest właścicielem infrastruktury fizycznej i używa zasobów udostępnionych (vCenter, centrów danych, magazynu fizycznego itd.) do hostowania wielu maszyn wirtualnych dzierżawy w tej samej infrastrukturze. Partner może zapewnić zarządzanie odzyskiwaniem po awarii jako usługę zarządzaną lub dzierżawca może być właścicielem odzyskiwania po awarii jako samoobsługowego rozwiązania.

* **Dedykowany dostawca usług hostingowych:** partner jest właścicielem infrastruktury fizycznej, ale używa dedykowanych zasobów (wiele centrów wirtualnych, fizycznych magazynów danych i tak dalej) do hostowania maszyn wirtualnych każdej dzierżawy w oddzielnej infrastrukturze. Partner może zapewnić zarządzanie odzyskiwaniem po awarii jako usługę zarządzaną lub dzierżawca może być właścicielem go jako samoobsługowego rozwiązania.

* **Dostawca usług zarządzanych (MSP)**: Klient jest właścicielem infrastruktury fizycznej, która obsługuje maszyny wirtualne, a partner zapewnia włączenie odzyskiwania po awarii i zarządzania.

## <a name="shared-hosting-services-provider-hsp"></a>Dostawca usług hostingu współdzielonego (HSP)

Pozostałe dwa scenariusze są podzbiory scenariusza hostingu udostępnionego i używają tych samych zasad. Różnice są opisane na końcu wskazówki hostingu współdzielonego.

Podstawowym wymaganiem w scenariuszu wielodostępnym jest, że dzierżawy muszą być izolowane. Jeden dzierżawca nie powinien być w stanie obserwować, co inny dzierżawca hostował. W środowisku zarządzanym przez partnera to wymaganie nie jest tak ważne, jak w środowisku samoobsługowym, gdzie może być krytyczne. W tym artykule przyjęto założenie, że izolacja dzierżawy jest wymagana.

Architektura jest pokazana na poniższym diagramie.

![Współużytkowane HSP z jednym centrum vCenter](./media/vmware-azure-multi-tenant-overview/shared-hosting-scenario.png)  

**Hosting współdzielony z jednym serwerem vCenter**

Na diagramie każdy klient ma osobny serwer zarządzania. Ta konfiguracja ogranicza dostęp dzierżawy do maszyn wirtualnych specyficznych dla dzierżawy i umożliwia izolację dzierżawy. Replikacja maszyn wirtualnych VMware używa serwera konfiguracji do odnajdowania maszyn wirtualnych i instalowania agentów. Te same zasady mają zastosowanie do środowisk wielodostępnych, z dodatkiem ograniczania odnajdowania maszyn wirtualnych przy użyciu kontroli dostępu vCenter.

Wymóg izolacji danych oznacza, że wszystkie poufne informacje o infrastrukturze (takie jak poświadczenia dostępu) pozostają niejawne dla dzierżawców. Z tego powodu zaleca się, aby wszystkie składniki serwera zarządzania pozostawały pod wyłączną kontrolą partnera. Składniki serwera zarządzania to:

* Serwer konfiguracji
* Serwer przetwarzania
* Główny serwer docelowy

Oddzielny serwer procesów skalowany w poziomie jest również pod kontrolą partnera.

## <a name="configuration-server-accounts"></a>Konta serwerów konfiguracji

Każdy serwer konfiguracji w scenariuszu wielodostępowym używa dwóch kont:

- **Konto dostępu vCenter:** To konto służy do odnajdywać maszyny wirtualne dzierżawy. Ma przypisane uprawnienia dostępu vCenter. Aby uniknąć przecieków dostępu, zaleca się, aby partnerzy sami wprowadzić te poświadczenia w narzędziu konfiguracji.

- **Konto dostępu do maszyny wirtualnej:** To konto służy do instalowania agenta usługi mobilności na maszynach wirtualnych dzierżawy za pomocą automatycznego wypychania. Zazwyczaj jest to konto domeny, które dzierżawca może dostarczyć partnerowi lub konto, które partner może zarządzać bezpośrednio. Jeśli dzierżawca nie chce bezpośrednio udostępniać szczegóły partnerowi, może wprowadzić poświadczenia za pośrednictwem ograniczonego w czasie dostępu do serwera konfiguracji. Lub, z pomocą partnera, mogą ręcznie zainstalować agenta usługi mobilności.

## <a name="vcenter-account-requirements"></a>Wymagania dotyczące konta vCenter

Skonfiguruj serwer konfiguracji za pomocą konta, do które przypisano specjalną rolę.

- Przypisanie roli musi być stosowane do konta dostępu vCenter dla każdego obiektu vCenter, a nie propagowane do obiektów podrzędnych. Ta konfiguracja zapewnia izolację dzierżawy, ponieważ propagacja dostępu może spowodować przypadkowy dostęp do innych obiektów.

    ![Opcja Propaguj do obiektów podrzędnych](./media/vmware-azure-multi-tenant-overview/assign-permissions-without-propagation.png)

- Alternatywne podejście jest przypisanie konta użytkownika i roli w obiekcie centrum danych i propagacji ich do obiektów podrzędnych. Następnie nadaj kontu rolę **Brak dostępu** dla każdego obiektu (takiego jak maszyny wirtualne należące do innych dzierżaw), które powinny być niedostępne dla określonej dzierżawy. Ta konfiguracja jest uciążliwa. Udostępnia kontroli dostępu przypadkowego dostępu, ponieważ każdy nowy obiekt podrzędny jest również automatycznie przyznawany dostęp, który jest dziedziczony z nadrzędnego. W związku z tym zaleca się użycie pierwszego podejścia.

### <a name="create-a-vcenter-account"></a>Tworzenie konta vCenter

1. Utwórz nową rolę, klonując wstępnie zdefiniowaną rolę *tylko do odczytu,* a następnie nadaj jej wygodną nazwę (taką jak Azure_Site_Recovery, jak pokazano w tym przykładzie).
2. Przypisz do tej roli następujące uprawnienia:

   * **Magazyn danych**: Przydzielanie miejsca, Przeglądanie magazynu danych, Operacje na plikach niskiego poziomu, Usuwanie pliku, Aktualizowanie plików maszyn wirtualnych
   * **Sieć**: Przypisanie sieci
   * **Zasób**: Przypisywanie maszyny Wirtualnej do puli zasobów, migrowanie wyłączonej maszyny Wirtualnej, migrowanie z zasilaniem na maszynie Wirtualnej
   * **Zadania**: Tworzenie zadania, Aktualizowanie zadania
   * **VM - Konfiguracja:** Wszystkie
   * **VM - Interakcja** > pytanie odpowiedzi, Połączenie urządzenia, Konfigurowanie nośników CD, Konfigurowanie nośników dyskietek, Wyłączanie, Włączanie, Instalowanie narzędzi VMware
   * **Maszyna wirtualna — > zapasów** Utwórz z istniejących, Utwórz nowe, Zarejestruj się, Wyrejestruuj
   * **Maszyna wirtualna — inicjowanie obsługi administracyjnej** > Zezwalaj na pobieranie maszyn wirtualnych, Zezwalaj na przekazywanie plików maszyn wirtualnych
   * **Maszyna wirtualna — zarządzanie migawkami** > usuwanie migawek

       ![Okno dialogowe Edytowanie roli](./media/vmware-azure-multi-tenant-overview/edit-role-permissions.png)

3. Przypisywanie poziomów dostępu do konta vCenter (używanego na serwerze konfiguracji dzierżawy) dla różnych obiektów w następujący sposób:

>| Obiekt | Rola | Uwagi |
>| --- | --- | --- |
>| vCenter | Tylko do odczytu | Potrzebne tylko do umożliwienia dostępu vCenter do zarządzania różnymi obiektami. To uprawnienie można usunąć, jeśli konto nigdy nie zostanie dostarczone do dzierżawy lub używane do żadnych operacji zarządzania w centrum vCenter. |
>| Centrum danych | Azure_Site_Recovery |  |
>| Klaster hosta i hosta | Azure_Site_Recovery | Ponownie zapewnia, że dostęp jest na poziomie obiektu, dzięki czemu tylko dostępne hosty mają maszyny wirtualne dzierżawy przed przejściem w stan failover i po powiększaniu. |
>| Magazyn danych i klaster magazynu danych | Azure_Site_Recovery | Tak samo jak poprzedni. |
>| Network (Sieć) | Azure_Site_Recovery |  |
>| Serwer zarządzania | Azure_Site_Recovery | Zawiera dostęp do wszystkich komponentów (CS, PS i MT) poza maszyną CS. |
>| Maszyny wirtualne dzierżawy | Azure_Site_Recovery | Zapewnia, że wszystkie nowe maszyny wirtualne dzierżawy określonej dzierżawy również uzyskać ten dostęp lub nie będą one wykrywalne za pośrednictwem witryny Azure portal. |

Dostęp do konta vCenter został ukończony. Ten krok spełnia wymagania minimalne uprawnienia do ukończenia operacji powrotu po awarii. Można również użyć tych uprawnień dostępu z istniejącymi zasadami. Wystarczy zmodyfikować istniejące uprawnienia ustawione tak, aby uwzględnić uprawnienia roli z kroku 2, wyszczególnione wcześniej.

### <a name="failover-only"></a>Tylko przewijamy w sposób failover
Aby ograniczyć operacje odzyskiwania po awarii tylko do pracy awaryjnej (czyli bez możliwości powrotu po awarii), należy użyć poprzedniej procedury, z tymi wyjątkami:

- Zamiast przypisywać *rolę Azure_Site_Recovery* do konta dostępu vCenter, przypisz do tego konta tylko rolę Tylko do *odczytu.* Ten zestaw uprawnień umożliwia replikację maszyny Wirtualnej i pracy awaryjnej i nie zezwala na powrót po awarii.
- Wszystko inne w poprzednim procesie pozostaje tak, jak jest. Aby zapewnić izolację dzierżawy i ograniczyć odnajdowanie maszyn wirtualnych, każde uprawnienie jest nadal przypisywane tylko na poziomie obiektu i nie jest propagowane do obiektów podrzędnych.

### <a name="deploy-resources-to-the-tenant-subscription"></a>Wdrażanie zasobów w subskrypcji dzierżawy

1. W witrynie Azure portal utwórz grupę zasobów, a następnie wdrożyć magazyn usług odzyskiwania na zwykły proces.
2. Pobierz klucz rejestracji magazynu.
3. Zarejestruj CS dla dzierżawy przy użyciu klucza rejestracji magazynu.
4. Wprowadź poświadczenia dla dwóch kont dostępu, konta, aby uzyskać dostęp do serwera vCenter i konta, aby uzyskać dostęp do maszyny Wirtualnej.

    ![Konta serwerów konfiguracji programu Manager](./media/vmware-azure-multi-tenant-overview/config-server-account-display.png)

### <a name="register-servers-in-the-vault"></a>Rejestrowanie serwerów w przechowalni

1. W portalu Azure w magazynie utworzonym wcześniej zarejestruj serwer vCenter na serwerze konfiguracji przy użyciu utworzonego konta vCenter.
2. Zakończ proces "Przygotowanie infrastruktury" do odzyskiwania witryny w zwykłym procesie.
3. Maszyny wirtualne są teraz gotowe do replikacji. Sprawdź, czy tylko maszyny wirtualne dzierżawcy są wyświetlane w**maszynach wirtualnych** **Replicate** > Select .

## <a name="dedicated-hosting-solution"></a>Dedykowane rozwiązanie hostingowe

Jak pokazano na poniższym diagramie, różnica architektury w dedykowanym rozwiązaniu hostingu jest, że infrastruktura każdej dzierżawy jest skonfigurowana tylko dla tej dzierżawy.

![architektura-shared-hsp](./media/vmware-azure-multi-tenant-overview/dedicated-hosting-scenario.png)  
**Dedykowany scenariusz hostingu z wieloma centrami vCenters**

## <a name="managed-service-solution"></a>Zarządzane rozwiązanie serwisowe

Jak pokazano na poniższym diagramie, różnica architektury w rozwiązaniu usługi zarządzanej jest, że infrastruktura każdego dzierżawy jest również fizycznie oddzielona od infrastruktury innych dzierżawców. Ten scenariusz zwykle istnieje, gdy dzierżawca jest właścicielem infrastruktury i chce dostawcy rozwiązania do zarządzania odzyskiwaniem po awarii.

![architektura-shared-hsp](./media/vmware-azure-multi-tenant-overview/managed-service-scenario.png)  
**Scenariusz usługi zarządzanej z wieloma centrami vCenters**

## <a name="next-steps"></a>Następne kroki
- [Dowiedz się więcej](site-recovery-role-based-linked-access-control.md) o kontroli dostępu opartej na rolach w funkcji Odzysk witryny.
- Dowiedz się, jak [skonfigurować odzyskiwanie po awarii maszyn wirtualnych VMware na platformie Azure.](vmware-azure-tutorial.md)
- Dowiedz się więcej o [wielonajemach dzięki maszynom wirtualnym CSP dla VMWare.](vmware-azure-multi-tenant-csp-disaster-recovery.md)
