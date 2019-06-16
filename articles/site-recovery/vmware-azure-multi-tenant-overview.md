---
title: Omówienie obsługi wielodostępu na potrzeby odzyskiwania po awarii maszyn wirtualnych programu VMware do platformy Azure (CSP) przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Omówienie pomocy technicznej usługi Azure Site Recovery do odzyskiwania po awarii programu VMWare do platformy Azure w programie środowisku wielodostępnym (CSP).
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: d227b8d038dd686bde9b031ca2c58adc7dd6d76b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60718125"
---
# <a name="overview-of-multi-tenant-support-for-vmware-disaster-recovery-to-azure-with-csp"></a>Omówienie obsługi wielodostępu do odzyskiwania po awarii programu VMware na platformę Azure za pomocą programu CSP

[Usługa Azure Site Recovery](site-recovery-overview.md) obsługuje środowiska wielu dzierżaw w przypadku subskrypcji z dzierżawy. Obsługuje ona również wielodostępu dla subskrypcji dzierżawcy, które są tworzone i zarządzane za pośrednictwem programu Microsoft Cloud Solution Provider (CSP).

W tym artykule omówiono wdrażanie i zarządzanie wieloma dzierżawami z programu VMware na replikacja usługi Azure.

## <a name="multi-tenant-environments"></a>Środowiska z wieloma dzierżawcami

Istnieją trzy główne modele wielodostępne:

* **Dostawcy usług hostingu (HSP) udostępnionych**: Partner ten jest właścicielem infrastruktury fizycznej i korzysta z zasobów udostępnionych (vCenter, centrów danych, magazynu fizycznego i tak dalej) do hostowania wielu maszyn wirtualnych dzierżawy w tej samej infrastrukturze. Partner może zapewnić zarządzanie odzyskiwania po awarii jako usługa zarządzana, lub dzierżawy mogą być właścicielami odzyskiwania po awarii jako rozwiązanie samoobsługi.

* **Dostawca usług hostingu w wersji dedykowanej**: Partner jest właścicielem infrastruktury fizycznej, ale używa dedykowanych zasobów (wiele vCenters, fizycznych magazynów danych i tak dalej) do obsługi każdego dzierżawcy maszyny wirtualne, w osobnej infrastruktury. Partner może zapewnić zarządzanie odzyskiwania po awarii jako usługa zarządzana lub dzierżawcy mogą jej właścicielem jako rozwiązanie samoobsługi.

* **Zarządzane usługi dostawcy (MSP)** : Klient jest właścicielem infrastruktury fizycznej, który jest hostem maszyny wirtualne, a partner zapewnia możliwości odzyskiwania po awarii i zarządzanie.

## <a name="shared-hosting-services-provider-hsp"></a>Dostawcy usług udostępnionych hostingu (HSP)

Inne scenariusze są podzbiorem scenariusza hostingu udostępnionych i używają tych samych zasad. Różnice są opisane na końcu wskazówki udostępniane hostingu.

Podstawowy wymóg w scenariuszu z wieloma dzierżawami to, że dzierżawy musi być izolowane. Jednej dzierżawy nie należy obserwować, co ma hostowanej w innej dzierżawie. W środowisku zarządzana przez partnera to wymaganie nie jest tak ważne, ponieważ jest on Samoobsługowe środowiska, gdzie może być krytyczny. W tym artykule założono, że izolacji dzierżawcy jest wymagane.

Na poniższym diagramie przedstawiono architektury.

![Udostępnione HSP z jednego vCenter](./media/vmware-azure-multi-tenant-overview/shared-hosting-scenario.png)  

**Hosting udostępnione z jednego serwera vCenter**

Na diagramie każdy klient ma na serwerze zarządzania oddzielne. Ta konfiguracja ogranicza dostęp dzierżawy do maszyn wirtualnych specyficznym dla dzierżawy i odizolowania dzierżawców umożliwia. Replikacja maszyny Wirtualnej VMware korzysta z serwera konfiguracji do odnalezienia maszyn wirtualnych i instalowanie agentów. Te same zasady mają zastosowanie w środowiskach wielodostępnych, dodając ograniczenie odnajdowania maszyn wirtualnych za pomocą kontroli dostępu vCenter.

Wymaganie izolację danych oznacza, że wszystkie informacje poufne infrastruktury (na przykład dostęp do poświadczeń) pozostaje niejawne dzierżawcom. Z tego powodu zaleca się pozostawienie wszystkie składniki serwera zarządzania w ramach wyłączną kontrolę partnera. Składniki serwera zarządzania są następujące:

* Serwer konfiguracji
* Serwer przetwarzania
* Główny serwer docelowy

Oddzielnego skalowanych w poziomie serwera przetwarzania jest również pod kontrolą partnera.

## <a name="configuration-server-accounts"></a>Konta serwera konfiguracji

Każdy serwer konfiguracji w scenariuszu z wieloma dzierżawami używa dwóch kont:

- **konto dostępu do vCenter**: To konto jest używane do odnajdywania maszyn wirtualnych dzierżawy. Posiada vCenter uprawnienia dostępu do niej przypisany. Aby zapobiec wyciekom dostępu, zaleca się, że partnerzy wprowadzić poświadczenia, te same w narzędziu konfiguracji.

- **Konto dostępu do maszyny wirtualnej**: To konto jest używane do zainstalowania agenta usługi mobilności na dzierżawy maszyn wirtualnych za pomocą wypychania automatycznego. Zazwyczaj jest konta domeny, które dzierżawcy mogą dostarczać z partnerem lub konta, które partner może bezpośrednio zarządzać. Jeśli dzierżawa nie chce udostępnić szczegóły partnera bezpośrednio, można wprowadzić poświadczenia za pośrednictwem dostępu ograniczona czasowo, z serwerem konfiguracji. Lub z pomocy partnera, można ją zainstalować agenta usługi mobilności ręcznie.

## <a name="vcenter-account-requirements"></a>wymagania dotyczące kont vCenter

Skonfiguruj serwer konfiguracji za pomocą konta które ma specjalną rolę do niej przypisany.

- Przypisanie roli należy zastosować do konta dostępu do vCenter dla każdego obiektu vCenter i nie są propagowane do obiektów podrzędnych. Ta konfiguracja gwarantuje izolacji dzierżawców, ponieważ propagacja dostępu może spowodować przypadkowe dostępu do innych obiektów.

    ![Propagacja opcji obiektów podrzędnych](./media/vmware-azure-multi-tenant-overview/assign-permissions-without-propagation.png)

- Alternatywnym podejściem jest przypisania roli na obiekt centrum danych i konta użytkownika i propagowania ich do obiektów podrzędnych. Następnie nadać kontu **bez dostępu** rolę dla każdego obiektu (na przykład w przypadku maszyn wirtualnych, które należą do innych dzierżaw), powinna być niedostępne do określonej dzierżawy. Ta konfiguracja jest kłopotliwe. Udostępnia ona kontrolę dostępu przypadkowe, ponieważ każdy nowy obiekt podrzędny również automatycznie uzyskuje dostęp, który jest dziedziczony z obiektu nadrzędnego. Dlatego zaleca się, że używasz pierwszego podejścia.

### <a name="create-a-vcenter-account"></a>Utwórz konto vCenter

1. Utwórz nową rolę przez klonowanie wstępnie zdefiniowanego *tylko do odczytu* roli, a następnie nadaj mu nazwę wygodne (na przykład Azure_Site_Recovery, jak pokazano w tym przykładzie).
2. Przypisz następujące uprawnienia do tej roli:

   * **Magazyn danych**: Przydzielanie miejsca, przegląd magazynu danych, operacji na plikach niskiego poziomu, usuń plik, pliki maszyny wirtualnej aktualizacji
   * **Sieć**: Przypisywanie sieci
   * **Zasób**: Przypisz maszyn wirtualnych do puli zasobów, migracja wyłączenia maszyny Wirtualnej, migracja, oparte na maszynie Wirtualnej
   * **Zadania podrzędne**: Utwórz zadania, aktualizowanie zadania
   * **Maszyny Wirtualnej — Konfiguracja**: Wszyscy
   * **Maszyny Wirtualnej — interakcji** > odpowiedzi na pytanie, połączenie z urządzeniem, konfigurowanie ciągłego Dostarczania multimediów, Konfiguruj dyskietka, wyłączanie zasilania na instalowanie narzędzi VMware
   * **Maszyny Wirtualnej — spis** > Tworzenie na podstawie istniejącego, Utwórz nową, rejestrowanie, wyrejestrowywanie
   * **Maszyny Wirtualnej — Inicjowanie obsługi administracyjnej** > Zezwalaj na pobieranie maszyny wirtualnej, przekazywanie plików maszyny wirtualnej Zezwalaj
   * **Maszyny Wirtualnej — Zarządzanie migawek** > usuwanie migawek

       ![Okno dialogowe Edytuj rolę](./media/vmware-azure-multi-tenant-overview/edit-role-permissions.png)

3. Przypisz poziomy dostępu do konta vCenter (używane na serwerze konfiguracji dzierżawy) dla różnych obiektów w następujący sposób:

>| Object | Rola | Uwagi |
>| --- | --- | --- |
>| vCenter | Tylko do odczytu | Potrzebne tylko po to zezwolić na dostęp vCenter do zarządzania różnymi obiektami. Można usuwać tego uprawnienie, jeśli konto nigdy nie będzie można przekazać do dzierżawy lub używany dla wszystkich operacji zarządzania w programie vcenter. |
>| Centrum danych | Azure_Site_Recovery |  |
>| Host i klaster hostów | Azure_Site_Recovery | Ponownie zapewnia dostęp na poziomie obiektu tak, aby tylko dostępne hosty mają dzierżawy maszyn wirtualnych przed włączeniem trybu failover i po powrocie po awarii. |
>| Klaster magazynu danych i magazynu danych | Azure_Site_Recovery | Taka sama jak poprzedzającego. |
>| Sieć | Azure_Site_Recovery |  |
>| Serwer zarządzania | Azure_Site_Recovery | Obejmuje dostęp do wszystkich składników (CS PS i MT) poza maszyną CS. |
>| Maszynami wirtualnymi dzierżawcy | Azure_Site_Recovery | Zapewnia, że wszelkie nowej dzierżawy maszyn wirtualnych dzierżawy określonego również uzyskać dostęp lub nie będą odnajdowane witryny Azure portal. |

Dostęp do konta vCenter jest teraz ukończona. W tym kroku spełnia wymagania minimalne uprawnienia, aby ukończyć operacji powrotu po awarii. Umożliwia także te uprawnienia dostępu z istniejących zasad. Po prostu zmodyfikuj swoje istniejące uprawnienia ustawione na wartość Dołącz uprawnienia roli z kroku 2, szczegóły wcześniej.

### <a name="failover-only"></a>Tylko trybu failover
Aby ograniczyć operacje odzyskiwania po awarii do trybu failover tylko (oznacza to, bez możliwości powrotu po awarii), użyj poprzedniej procedury, z uwzględnieniem poniższych wyjątków:

- Zamiast przypisywania *Azure_Site_Recovery* roli vCenter konta dostępu należy przypisywać tylko *tylko do odczytu* roli do tego konta. Ten zestaw uprawnień umożliwia replikację maszyny Wirtualnej i tryb failover, a nie zezwala na powrót po awarii.
- Wszystkie inne poprzedniego procesu pozostaje się. Zapewnienie izolacji dzierżawcy oraz ograniczyć odnajdowania maszyn wirtualnych, co uprawnienie jest ciągle przypisany tylko na poziomie obiektu i nie są propagowane do obiektów podrzędnych.

### <a name="deploy-resources-to-the-tenant-subscription"></a>Wdrażanie zasobów do subskrypcji dzierżawcy

1. W witrynie Azure portal Utwórz grupę zasobów, a następnie wdrożyć magazyn usług odzyskiwania na zwykły proces.
2. Pobierz klucz rejestracji magazynu.
3. Zarejestruj CS dzierżawy przy użyciu klucza rejestracji magazynu.
4. Wprowadź poświadczenia dla kont dostępu dwa konta dostępu do serwera vCenter i konta, które ma dostęp do maszyny Wirtualnej.

    ![Konta server configuration Manager](./media/vmware-azure-multi-tenant-overview/config-server-account-display.png)

### <a name="register-servers-in-the-vault"></a>Zarejestruj serwer w magazynie

1. W witrynie Azure portal w magazynie, który został utworzony wcześniej należy zarejestrować serwer vCenter do serwera konfiguracji za pomocą utworzone konto vCenter.
2. Zakończ proces "Przygotowanie infrastruktury" usługi Site Recovery na zwykły proces.
3. Maszyny wirtualne są teraz gotowe do replikacji. Sprawdź, czy tylko dla maszyn wirtualnych dzierżawy są wyświetlane na liście **replikować** > **wybierz maszyny wirtualne**.

## <a name="dedicated-hosting-solution"></a>Dedykowane rozwiązania hostingu

Jak pokazano na poniższym diagramie, architektury różnica w dedykowane rozwiązania hostingu jest, że infrastruktura każdej dzierżawy zostało skonfigurowane dla tej dzierżawy tylko.

![architecture-shared-hsp](./media/vmware-azure-multi-tenant-overview/dedicated-hosting-scenario.png)  
**Hostingu scenariusza z wieloma vCenters w wersji dedykowanej**

## <a name="managed-service-solution"></a>Rozwiązania usługi zarządzanej

Jak pokazano na poniższym diagramie, architektury różnica w rozwiązaniu usługi zarządzanej jest infrastruktury każdego dzierżawcy jest również fizycznie oddzielona od innych dzierżaw infrastruktury. W tym scenariuszu istnieje zazwyczaj w przypadku, gdy dzierżawy jest właścicielem infrastruktury i chce dostawcy rozwiązania, aby zarządzać odzyskiwaniem po awarii.

![architecture-shared-hsp](./media/vmware-azure-multi-tenant-overview/managed-service-scenario.png)  
**Zarządzane usługi scenariusza z wieloma vCenters**

## <a name="next-steps"></a>Kolejne kroki
- [Dowiedz się więcej](site-recovery-role-based-linked-access-control.md) temat kontroli dostępu opartej na rolach w usłudze Site Recovery.
- Dowiedz się, jak [Konfigurowanie odzyskiwania po awarii maszyn wirtualnych programu VMware na platformę Azure](vmware-azure-tutorial.md).
- Dowiedz się więcej o [wielodostępnością za pomocą programu CSP dla maszyn wirtualnych VMWare](vmware-azure-multi-tenant-csp-disaster-recovery.md).
