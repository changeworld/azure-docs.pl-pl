---
title: Wielodostępne obsługę replikacji maszyny Wirtualnej VMware do platformy Azure (dostawcy usług Kryptograficznych programu) | Dokumentacja firmy Microsoft
description: Opisuje sposób wdrażania usługi Azure Site Recovery w środowisku wielodostępnym do organizowania replikacji, trybu failover i odzyskiwania maszyn wirtualnych VMware lokalnymi (VM) na platformie Azure za pośrednictwem dostawcy usług Kryptograficznych programu przy użyciu portalu Azure
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: 532dd399d2d5fcbab616744dd02f4a95078cbb1b
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2018
ms.locfileid: "29768466"
---
# <a name="multi-tenant-support-in-azure-site-recovery-for-replicating-vmware-virtual-machines-to-azure-through-csp"></a>Obsługa wielu dzierżawców w usłudze Azure Site Recovery replikację maszyn wirtualnych VMware do platformy Azure za pośrednictwem dostawcy usług Kryptograficznych

Usługa Azure Site Recovery obsługuje środowiskach wielodostępnych subskrypcji dzierżawcy. Obsługuje ona również wielodostępność dla subskrypcji dzierżawcy, które są tworzone i zarządzane przez program Microsoft Cloud Solution Provider (CSP). W tym artykule szczegółowo wskazówki dotyczące wdrażania i zarządzania nimi wielodostępne scenariusze VMware do platformy Azure. Aby uzyskać więcej informacji na temat tworzenia i zarządzanie subskrypcjami dzierżawy, zobacz [Zarządzanie wielu dzierżawców z dostawcy usług Kryptograficznych](site-recovery-manage-multi-tenancy-with-csp.md) .

W tych wskazówkach pobiera silnie z istniejąca dokumentacja dotycząca replikację maszyn wirtualnych VMware do platformy Azure. Aby uzyskać więcej informacji, zobacz [maszyn wirtualnych VMware replikować do platformy Azure z usługą Site Recovery](site-recovery-vmware-to-azure.md).

## <a name="multi-tenant-environments"></a>Środowiska z wieloma dzierżawcami
Istnieją trzy główne modele wielodostępne:

* **Hosting usług dostawcy (HSP) udostępnionych**: partnera jest właścicielem infrastruktury fizycznej i korzysta z zasobów udostępnionych (vCenter, centrów danych magazynu fizycznego i tak dalej) do obsługi wielu dzierżawców maszyny wirtualne na tej samej infrastrukturze. Partner zapewniają odzyskiwania po awarii management jako usługa zarządzana lub dzierżawcy mogą być właścicielami odzyskiwania po awarii, jako rozwiązanie samoobsługi.

* **Dostawcy usług hostingu w wersji dedykowanej**: partnera jest właścicielem infrastruktury fizycznej, ale używa zasobów dedykowanych (wiele Vcenter, datastores fizycznych i tak dalej) do obsługi maszyn wirtualnych każdego dzierżawcy w osobnej infrastruktury. Partner zapewniają odzyskiwania po awarii management jako usługa zarządzana lub dzierżawcy mogą należeć go jako rozwiązaniem samoobsługi.

* **Zarządzane usługi dostawcy (MSP)**: klient jest właścicielem infrastruktury fizycznej, który jest hostem maszyny wirtualnej, a partnera umożliwia włączenie odzyskiwania po awarii i zarządzania.

## <a name="shared-hosting-multi-tenant-guidance"></a>Hosting udostępnione wskazówki wieloma dzierżawcami
W tej sekcji omówiono scenariusz udostępnionych hosting szczegółowo. Dwa scenariusze są podzbiorem scenariusz udostępnionych hosting i używają tych samych zasad. Różnice są opisane na końcu wskazówki udostępniane hosting.

Podstawowe wymagania w przypadku wielu dzierżawców jest odizolowanie różnych dzierżaw. Jeden dzierżawy nie może być można obserwować, co hosted innego dzierżawcę. W środowisku zarządzana przez partnera to wymaganie nie jest równie ważne, ponieważ jest on w środowisku samoobsługi, którym może mieć kluczowe znaczenie. We wskazówkach tych założono, że izolacji dzierżawców, jest wymagana.

Architektura jest przedstawiony na poniższym diagramie:

![Udostępniony HSP z jednego vCenter](./media/site-recovery-multi-tenant-support-vmware-using-csp/shared-hosting-scenario.png)  
**Hosting udostępnionych scenariusza z jednym vCenter**

Jak pokazano na powyższym diagramie, każdy klient ma na serwerze zarządzania oddzielne. Ta konfiguracja ogranicza dzierżawcy dostęp do maszyn wirtualnych specyficznego dla dzierżawy i umożliwia odizolowania dzierżawców. Scenariusz replikacji maszyn wirtualnych VMware używa serwer konfiguracji do zarządzania kontami odnajdywanie maszyn wirtualnych i instalowania agentów. Te same zasady dotyczą środowiskach wielodostępnych, z uwzględnieniem ograniczenie odnajdywania maszyny Wirtualnej za pośrednictwem kontroli dostępu vCenter.

Wymaganie izolacji danych wymaga, aby wszystkie informacje poufne infrastruktury (na przykład dostęp do poświadczeń) pozostała niejawne dzierżawcom. Z tego powodu zaleca się pozostawienie wszystkie składniki serwera zarządzania, w obszarze wyłączną kontrolę partnera. Składniki serwera zarządzania są:
* Serwer konfiguracji (CS)
* Serwer przetwarzania (PS)
* Główny serwer docelowy (MT)

PS skalowalnego w poziomie jest również pod kontrolą partnera.

### <a name="every-cs-in-the-multi-tenant-scenario-uses-two-accounts"></a>Każdy CS w scenariuszu wielodostępne używa dwóch kont

- **konto dostępu do vCenter**: Odkryj maszyny wirtualne dzierżawców za pomocą tego konta. Ma uprawnienia dostępu vCenter przypisane do niej (zgodnie z opisem w następnej sekcji). Aby zapobiec przypadkowemu dostępu przecieki, zaleca się wprowadzenia przez partnerów tych samych poświadczeń narzędzia konfiguracji.

- **Konto dostępu do maszyny wirtualnej**: Użyj tego konta do zainstalowania agenta mobilności w ramach dzierżawy maszyn wirtualnych za pomocą wypychania automatycznego. Zazwyczaj jest konta domeny, które Dzierżawca może udostępniać partnera lub taki, który partner może zarządzać bezpośrednio. Jeśli dzierżawa nie chcą udostępniać szczegóły partnera bezpośrednio, można można zezwolić na wprowadź poświadczenia do ograniczonej czasowo dostępu do CS lub, przy pomocy partnera, zainstaluj agentów mobilności ręcznie.

### <a name="requirements-for-a-vcenter-access-account"></a>Wymagania dotyczące konta dostępu do vCenter

Jak wspomniano w poprzedniej sekcji, należy skonfigurować CS z konta, które ma specjalne uprawnienia przypisane do niej. Przypisania roli należy zastosować do konta dostępu vCenter dla każdego obiektu vCenter i nie są propagowane do obiektów podrzędnych. Ta konfiguracja zapewnia izolacji dzierżawców, ponieważ propagacja dostępu może spowodować przypadkowe dostęp do innych obiektów.

![Propaguj opcji obiektów podrzędnych](./media/site-recovery-multi-tenant-support-vmware-using-csp/assign-permissions-without-propagation.png)

Alternatywą jest przypisanie konta użytkownika i roli obiektu centrum danych i propagować je do obiektów podrzędnych. Następnie nadaj kontu *dostępu* roli do wszystkich obiektów (np. maszyny wirtualne z innymi dzierżawcami) powinna być niedostępne dla konkretnego dzierżawy. Ta konfiguracja jest skomplikowane i udostępnia ona kontroli dostępu przypadkowe, ponieważ każdy nowy obiekt podrzędny automatycznie udzielono dostępu, który jest dziedziczone z obiektu nadrzędnego. Dlatego zaleca się, że używasz pierwszego podejścia.

Procedura dostępu do konta vCenter jest następująca:

1. Utwórz nową rolę przez klonowanie wstępnie zdefiniowanych *tylko do odczytu* roli, a następnie nadaj nazwę wygodny (na przykład Azure_Site_Recovery, jak pokazano w tym przykładzie).

2. Przypisz następujące uprawnienia do tej roli:

    * **Magazyn danych**: przydzielić miejsca, Magazyn danych przeglądania, operacje na plikach niskiego poziomu, usuń plik, pliki aktualizacji maszyny wirtualnej

    * **Sieci**: przypisywanie sieci

    * **Zasób**: przypisanie maszyny Wirtualnej do puli zasobów, migracji wyłączyć maszynę Wirtualną, migracji wyłączyć na maszynie Wirtualnej

    * **Zadania**: Tworzenie zadania, zadania aktualizacji

    * **Maszyna wirtualna**:
        * Konfiguracja > wszystkie
        * Interakcja > odpowiedzi na pytanie, połączenie z urządzeniem, skonfiguruj dysków CD, konfigurowanie dyskietka, wyłącz zasilanie, zainstaluj narzędzia VMware
        * Spis > Utwórz na podstawie istniejących, Utwórz nowy, zarejestruj, wyrejestrować
        * Inicjowanie obsługi administracyjnej > Zezwalaj na pobieranie maszyny wirtualnej, przekazywanie plików maszyny wirtualnej Zezwalaj
        * Zarządzanie migawkami > Usuń migawki

    ![Okno dialogowe Edytuj rolę](./media/site-recovery-multi-tenant-support-vmware-using-csp/edit-role-permissions.png)

3. Przypisz poziomy dostępu do konta vCenter (używane w dzierżawie CS) dla różnych obiektów, w następujący sposób:

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

Aby ograniczyć operacje odzyskiwania po awarii do stanu trybu failover (oznacza to, bez możliwości powrotu po awarii), wykonaj poprzednią procedurę, z wyjątkiem: zamiast przypisywać *Azure_Site_Recovery* rolę vCenter konta dostępu należy przypisywać tylko *tylko do odczytu* roli do tego konta. Ten zestaw uprawnień pozwala na replikację maszyny Wirtualnej i trybu failover, a nie zezwala na powrót po awarii. Wszystkie inne poprzedniego procesu pozostaje jest. Do zapewnienia izolacji dzierżawy i ograniczyć odnajdywanie maszyny Wirtualnej, co uprawnienie jest ciągle przypisany tylko na poziomie obiektu i nie są propagowane do obiektów podrzędnych.

## <a name="other-multi-tenant-environments"></a>Innych środowiskach wielodostępnych

Poprzedniej sekcji opisano sposób konfigurowania środowiska z wieloma dzierżawami udostępnionego rozwiązania hostingu. Inne dwa główne rozwiązania są przeznaczone wyłącznie i zarządzane usługi hostingu. W poniższych sekcjach opisano architekturę tych rozwiązań.

### <a name="dedicated-hosting-solution"></a>Dedykowane rozwiązania do hostingu

Jak pokazano na poniższym diagramie, architektury różnica w dedykowane rozwiązania hostingu jest, że infrastruktura każdego dzierżawcy jest skonfigurowany dla tej dzierżawy tylko. Ponieważ dzierżaw są izolowane za pomocą osobnych Vcenter, dostawcy hostingu musi nadal zawiera procedura dostawcy usług Kryptograficznych dla udostępniona Usługa hostingu, ale nie trzeba martwić izolacji dzierżawców. Instalator dostawcy usług Kryptograficznych pozostaje niezmieniona.

![architecture-shared-hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/dedicated-hosting-scenario.png)  
**Dedykowany hostingu scenariusza z wieloma Vcenter**

### <a name="managed-service-solution"></a>Usługi zarządzane przez rozwiązanie

Jak pokazano na poniższym diagramie, architektury różnica w rozwiązaniu zarządzanych usług jest infrastruktury każdego dzierżawcy jest również fizycznie oddzielona od innych dzierżaw infrastruktury. W tym scenariuszu zwykle występuje, gdy dzierżawa jest właścicielem infrastruktury i chce dostawcę rozwiązania do zarządzania odzyskiwania po awarii. Ponownie ponieważ dzierżaw są fizycznie izolowane za pośrednictwem różnych infrastruktur, konieczność partnera postępuj zgodnie z instrukcjami dostawcy usług Kryptograficznych przewidzianych udostępniona Usługa hostingu, ale nie trzeba martwić izolacji dzierżawców. Inicjowanie obsługi administracyjnej dostawcy usług Kryptograficznych pozostaje niezmieniona.

![architecture-shared-hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/managed-service-scenario.png)  
**Zarządzane usługi scenariusza z wieloma Vcenter**

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej](site-recovery-role-based-linked-access-control.md) o kontroli dostępu opartej na rolach do zarządzania wdrożenia usługi Azure Site Recovery.

[Zarządzanie wielu dzierżawców z dostawcy usług Kryptograficznych](site-recovery-manage-multi-tenancy-with-csp.md)
