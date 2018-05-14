---
title: Omówienie wielodostępnej obsługę replikacji maszyny Wirtualnej VMware do platformy Azure (CSP) przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Zawiera omówienie pomocy technicznej usługi Azure Site Recovery dla dzierżawcy subskrypcje w środowisku wielodostępnym za pośrednictwem programu dostawcy usług Kryptograficznych.
services: site-recovery
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: manayar
ms.openlocfilehash: 0168849c01add3f714b139c7984e3def74f40a5b
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="overview-of-multi-tenant-support-for-vmware-replication-to-azure-with-csp"></a>Omówienie wielodostępnej obsługę replikacji maszyn wirtualnych VMware do platformy Azure z dostawcy usług Kryptograficznych

[Usługa Azure Site Recovery](site-recovery-overview.md) obsługuje wielodostępne środowiska subskrypcji dzierżawcy. Obsługuje ona również wielodostępność dla subskrypcji dzierżawcy, które są tworzone i zarządzane przez program Microsoft Cloud Solution Provider (CSP).

W tym artykule omówiono wdrażanie i zarządzanie nim wielodostępne VMware do platformy Azure replikacji.

## <a name="multi-tenant-environments"></a>Środowiska z wieloma dzierżawcami

Istnieją trzy główne modele wielodostępne:

* **Hosting usług dostawcy (HSP) udostępnionych**: partnera jest właścicielem infrastruktury fizycznej i korzysta z zasobów udostępnionych (vCenter, centrów danych magazynu fizycznego i tak dalej) do obsługi wielu dzierżawców maszyny wirtualne na tej samej infrastrukturze. Partner zapewniają odzyskiwania po awarii management jako usługa zarządzana lub dzierżawcy mogą być właścicielami odzyskiwania po awarii, jako rozwiązanie samoobsługi.

* **Dostawcy usług hostingu w wersji dedykowanej**: partnera jest właścicielem infrastruktury fizycznej, ale używa zasobów dedykowanych (wiele Vcenter, datastores fizycznych i tak dalej) do obsługi maszyn wirtualnych każdego dzierżawcy w osobnej infrastruktury. Partner zapewniają odzyskiwania po awarii management jako usługa zarządzana lub dzierżawcy mogą należeć go jako rozwiązaniem samoobsługi.

* **Zarządzane usługi dostawcy (MSP)**: klient jest właścicielem infrastruktury fizycznej, który jest hostem maszyny wirtualnej, a partnera umożliwia włączenie odzyskiwania po awarii i zarządzania.

## <a name="shared-hosting-services-provider-hsp"></a>Dostawca usług udostępnionych hosting (HSP)

Dwa scenariusze są podzbiorem scenariusz udostępnionych hosting i używają tych samych zasad. Różnice są opisane na końcu wskazówki udostępniane hosting.

Podstawowe wymagania w przypadku wielu dzierżawców jest dzierżawcy należy oddzielić. Jeden dzierżawy nie może być można obserwować, co hosted innego dzierżawcę. W środowisku zarządzana przez partnera to wymaganie nie jest równie ważne, ponieważ jest on w środowisku samoobsługi, którym może mieć kluczowe znaczenie. W tym artykule zakłada, że wymagane izolacji dzierżawców.

Na poniższym diagramie przedstawiono architekturę.

![Udostępniony HSP z jednego vCenter](./media/vmware-azure-multi-tenant-overview/shared-hosting-scenario.png)  

**Hosting udostępnionych z jednym serwerem vCenter**

Na diagramie każdy klient ma na serwerze zarządzania oddzielne. Ta konfiguracja ogranicza dzierżawcy dostęp do maszyn wirtualnych specyficznego dla dzierżawy, i umożliwia odizolowania dzierżawców. Replikacja maszyny Wirtualnej VMware używa serwera konfiguracji odnajdywanie maszyn wirtualnych i zainstalować agentów. Te same zasady stosowane w środowiskach wielodostępnych, z uwzględnieniem ograniczenie odnajdywania maszyny Wirtualnej za pomocą kontroli dostępu vCenter.

Wymaganie izolacji danych oznacza, że wszystkie informacje poufne infrastruktury (na przykład dostęp do poświadczeń) pozostaje niejawne dzierżawcom. Z tego powodu zaleca się pozostawienie wszystkie składniki serwera zarządzania, w obszarze wyłączną kontrolę partnera. Składniki serwera zarządzania są:

* Serwer konfiguracji
* Serwer przetwarzania
* Główny serwer docelowy

Oddzielnego skalowalnych w poziomie serwera przetwarzania jest również pod kontrolą partnera.

## <a name="configuration-server-accounts"></a>Konfiguracja kont serwera

Każdy serwer konfiguracji w scenariuszu wielodostępne używa dwóch kont:

- **konto dostępu do vCenter**: to konto służy do odnajdywania maszyny wirtualne dzierżawców. Ma przypisane do niej uprawnienia dostępu vCenter. Aby zapobiec przeciekom dostępu, zaleca się wprowadzenia przez partnerów tych samych poświadczeń narzędzia konfiguracji.

- **Konto dostępu do maszyny wirtualnej**: to konto jest używane do zainstalowania agenta usługi mobilności na dzierżawione maszyny wirtualne, z automatycznego wypychania. Zazwyczaj jest konta domeny, które dzierżawcy może zapewniać do partnera lub konta, które partnera może zarządzać bezpośrednio. Jeśli dzierżawa nie chcą udostępniać szczegóły partnera bezpośrednio, oni wprowadzać poświadczeń za pomocą ograniczony dostęp do konfiguracji serwera. Lub przy pomocy partnera, mogą zainstalować agenta usługi mobilności ręcznie.

## <a name="vcenter-account-requirements"></a>wymagania dotyczące konta vCenter

Konfigurowanie serwera konfiguracji z konta, które ma specjalne uprawnienia przypisane do niej.

- Przypisania roli należy zastosować do konta dostępu vCenter dla każdego obiektu vCenter i nie są propagowane do obiektów podrzędnych. Ta konfiguracja zapewnia izolacji dzierżawców, ponieważ propagacja dostępu może spowodować przypadkowe dostęp do innych obiektów.

    ![Propaguj opcji obiektów podrzędnych](./media/vmware-azure-multi-tenant-overview/assign-permissions-without-propagation.png)

- Innym podejściem jest przypisywanie konta użytkownika i roli obiektu centrum danych i obejmie ich obiektów podrzędnych. Następnie nadaj kontu **dostępu** roli do wszystkich obiektów (np. w przypadku maszyn wirtualnych, które należą do innych dzierżawców) powinny być niedostępne dla konkretnego dzierżawy. Ta konfiguracja jest skomplikowane. Prezentuje ona kontroli dostępu przypadkowe, ponieważ każdy nowy obiekt podrzędny automatycznie udzielono dostępu, który jest dziedziczone z obiektu nadrzędnego. Dlatego zaleca się, że używasz pierwszego podejścia.

### <a name="create-a-vcenter-account"></a>Utwórz konto vCenter

1. Utwórz nową rolę w klonowania wstępnie zdefiniowane *tylko do odczytu* roli, a następnie nadaj nazwę wygodny (na przykład Azure_Site_Recovery, jak pokazano w tym przykładzie).
2. Przypisz następujące uprawnienia do tej roli:

    * **Magazyn danych**: przydzielić miejsca, Magazyn danych przeglądania, operacje na plikach niskiego poziomu, usuń plik, pliki aktualizacji maszyny wirtualnej
    * **Sieci**: przypisywanie sieci
    * **Zasób**: przypisanie maszyny Wirtualnej do puli zasobów, migracji wyłączyć maszynę Wirtualną, migracji wyłączyć na maszynie Wirtualnej
    * **Zadania**: Tworzenie zadania, zadania aktualizacji
    * **Maszyna wirtualna — Konfiguracja**: wszystkie
    - **Maszyna wirtualna — interakcji** > odpowiedzi na pytanie, połączenie z urządzeniem, skonfiguruj dysków CD, konfigurowanie dyskietka, wyłącz zasilanie, zainstaluj narzędzia VMware
    - **Maszyna wirtualna — spis** > Utwórz na podstawie istniejących, Utwórz nowy, zarejestruj, wyrejestrować
    - **Maszyna wirtualna — inicjowania obsługi administracyjnej** > Zezwalaj na pobieranie maszyny wirtualnej, przekazywanie plików maszyny wirtualnej Zezwalaj
    - **Maszyna wirtualna — zarządzanie** > Usuń migawki

        ![Okno dialogowe Edytuj rolę](./media/vmware-azure-multi-tenant-overview/edit-role-permissions.png)

3. Przypisz poziomy dostępu do konta vCenter (używane na serwerze konfiguracji dzierżawy) dla różnych obiektów, w następujący sposób:

>| Obiekt | Rola | Uwagi |
>| --- | --- | --- |
>| vCenter | Tylko do odczytu | Konieczne jest tylko dostęp vCenter do zarządzania różnych obiektów. Można usunąć to uprawnienie, jeśli konto nigdy nie będą dostarczane przez dzierżawcę lub używane dla wszystkich operacji zarządzania na serwerze vCenter. |
>| Centrum danych | Azure_Site_Recovery |  |
>| Hosta i hostów klastra. | Azure_Site_Recovery | Ponownie zapewnia, że dostęp jest na poziomie obiektu, dzięki czemu tylko dostępne hosty muszą dzierżawione maszyny wirtualne przed trybu failover i po powrotu po awarii. |
>| Magazyn danych i magazynu danych klastra | Azure_Site_Recovery | Taka sama jak poprzedzających. |
>| Sieć | Azure_Site_Recovery |  |
>| Serwer zarządzania | Azure_Site_Recovery | Obejmuje dostęp do wszystkich składników (CS PS i MT) poza maszyny CS. |
>| Maszyny wirtualne dzierżawcy | Azure_Site_Recovery | Zapewnia, że dowolnej dzierżawy nowych maszyn wirtualnych dzierżawcy określonego także uzyskać dostęp, lub nie będzie wykrywalny za pośrednictwem portalu Azure. |

Dostęp do konta vCenter jest teraz ukończona. Ten krok spełnia wymagania minimalne uprawnienia, aby ukończyć operacji powrotu po awarii. Można również używać tych uprawnień dostępu z istniejących zasad. Po prostu zmodyfikować istniejące uprawnienia zawiera uprawnienia roli w kroku 2, szczegółowe wcześniej.

### <a name="failover-only"></a>Tylko trybu failover
Aby ograniczyć operacje odzyskiwania po awarii do trybu failover tylko (czyli bez możliwości powrotu po awarii), powyższej procedury, można użyć z następującymi wyjątkami:

- Zamiast przypisywać *Azure_Site_Recovery* rolę vCenter konta dostępu należy przypisywać tylko *tylko do odczytu* roli do tego konta. Ten zestaw uprawnień pozwala na replikację maszyny Wirtualnej i trybu failover, a nie zezwala na powrót po awarii.
- Wszystkie inne poprzedniego procesu pozostaje jest. Do zapewnienia izolacji dzierżawy i ograniczyć odnajdywanie maszyny Wirtualnej, co uprawnienie jest ciągle przypisany tylko na poziomie obiektu i nie są propagowane do obiektów podrzędnych.

### <a name="deploy-resources-to-the-tenant-subscription"></a>Wdrażanie zasobów w subskrypcji dzierżawy

1. W portalu Azure Utwórz grupę zasobów, a następnie wdrożyć na zwykły proces magazynu usług odzyskiwania.
2. Pobierz klucz rejestracji magazynu.
3. Zarejestruj CS dla dzierżawcy przy użyciu klucza rejestracji magazynu.
4. Wprowadź poświadczenia dla kont dostępu dwa konta dostępu do serwera vCenter i konto, które ma dostęp do maszyny Wirtualnej.

    ![Kont serwera configuration Manager](./media/vmware-azure-multi-tenant-overview/config-server-account-display.png)

### <a name="register-servers-in-the-vault"></a>Zarejestruj serwer w magazynie

1. W portalu Azure w magazynie, który został utworzony wcześniej należy zarejestrować serwer vCenter do konfiguracji serwera, za pomocą utworzonego konta vCenter.
2. Zakończ proces "Przygotowanie infrastruktury" Site Recovery na zwykły proces.
3. Maszyny wirtualne są teraz gotowe do replikacji. Sprawdź, czy tylko maszyny wirtualne dzierżawcy są wyświetlane w **replikować** > **wybierz maszyny wirtualne**.

## <a name="dedicated-hosting-solution"></a>Dedykowane rozwiązania do hostingu

Jak pokazano na poniższym diagramie, architektury różnica w dedykowane rozwiązania hostingu jest, że infrastruktura każdego dzierżawcy jest skonfigurowany dla tej dzierżawy tylko.

![architecture-shared-hsp](./media/vmware-azure-multi-tenant-overview/dedicated-hosting-scenario.png)  
**Dedykowany hostingu scenariusza z wieloma Vcenter**

## <a name="managed-service-solution"></a>Usługi zarządzane przez rozwiązanie

Jak pokazano na poniższym diagramie, architektury różnica w rozwiązaniu zarządzanych usług jest infrastruktury każdego dzierżawcy jest również fizycznie oddzielona od innych dzierżaw infrastruktury. W tym scenariuszu zwykle występuje, gdy dzierżawa jest właścicielem infrastruktury i chce dostawcę rozwiązania do zarządzania odzyskiwania po awarii.

![architecture-shared-hsp](./media/vmware-azure-multi-tenant-overview/managed-service-scenario.png)  
**Zarządzane usługi scenariusza z wieloma Vcenter**

## <a name="next-steps"></a>Kolejne kroki
- [Dowiedz się więcej](site-recovery-role-based-linked-access-control.md) o kontroli dostępu opartej na rolach w usłudze Site Recovery.
- Dowiedz się, jak [Konfigurowanie odzyskiwania po awarii maszyn wirtualnych VMware do platformy Azure](vmware-azure-tutorial.md).
- Dowiedz się więcej o [wielodostępność z dostawcy usług Kryptograficznych dla maszyn wirtualnych VMWare](vmware-azure-multi-tenant-csp-disaster-recovery.md).
