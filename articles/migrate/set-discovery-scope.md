---
title: Ustawianie zakresu odnajdowania maszyn wirtualnych VMware za pomocą usługi Azure Migrate
description: W tym artykule opisano sposób ustawiania zakresu odnajdywania dla oceny i migracji maszyn wirtualnych VMware za pomocą usługi Azure Migrate.
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 29b3077ead168cef2790468d6ac62d1c59c24c11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337638"
---
# <a name="set-discovery-scope-for-vmware-vms"></a>Ustawianie zakresu odnajdowania maszyn wirtualnych VMware

W tym artykule opisano sposób ograniczania zakresu odnajdowania maszyn wirtualnych VMware, które są odnalezione przez [urządzenie migracji platformy Azure.](migrate-appliance-architecture.md)

Urządzenie migracji platformy Azure odnajduje lokalne maszyny wirtualne VMware podczas: 

- Narzędzie [Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) służy do oceny maszyn wirtualnych VMware do migracji na platformę Azure.
- Użyj narzędzia [Migracji platformy Azure:Migracja serwera](migrate-services-overview.md#azure-migrate-server-migration-tool) do [bezagentowej migracji](server-migrate-overview.md) maszyn wirtualnych VMware na platformę Azure.

## <a name="set-discovery-scope"></a>Ustawianie zakresu odnajdowania


Po skonfigurowaniu urządzenia migracji platformy Azure dla oceny lub migracji maszyn wirtualnych VMware urządzenie rozpoczyna odnajdowanie maszyn wirtualnych i wysyłanie metadanych maszyn wirtualnych na platformę Azure. Przed podłączeniem urządzenia do serwera vCenter Server w celu odnajdywania można ustawić zakres odnajdywania, aby ograniczyć odnajdowanie do centrów danych vCenter Server, klastrów, folderu klastrów, hostów, folderu hostów lub poszczególnych maszyn wirtualnych.

Aby ustawić zakres, należy przypisać uprawnienia do konta, którego używa usługa Azure Migrate, aby uzyskać dostęp do serwera vCenter Server.

## <a name="create-a-vcenter-user-account"></a>Tworzenie konta użytkownika vCenter

Jeśli nie skonfigurowano jeszcze konta użytkownika vCenter używanego przez urządzenie usługi Azure Migrate do odnajdywanie, ocenę i migracja maszyn wirtualnych VMware, wykonaj to najpierw.

1.    Zaloguj się do vSphere Web Client jako administrator serwera vCenter.
2.    Wybierz pozycję**Użytkownicy i grupy jednosojowe** **administracji** > i kliknij kartę **Użytkownicy.**
3.    Wybierz ikonę **Nowy użytkownik.**
4.    Wypełnij nowe informacje o użytkowniku > **OK**.

Uprawnienia do konta zależą od tego, co wdrażasz.

**Funkcja** | **Uprawnienia do konta**
--- | ---
[Ocenić](tutorial-assess-vmware.md)| Konto wymaga dostępu tylko do odczytu.
[Odnajdywanie aplikacji/ról/funkcji](how-to-discover-applications.md) | Konto wymaga dostępu tylko do odczytu, z włączonymi uprawnieniami dla maszyn wirtualnych > operacji gościa.
[Analizowanie zależności (bez agenta)](how-to-create-group-machine-dependencies-agentless.md) | Konto wymaga dostępu tylko do odczytu, z włączonymi uprawnieniami dla maszyn wirtualnych > operacji gościa.
[Migracja (bez agenta)](tutorial-migrate-vmware.md) | Potrzebna jest rola, która ma przypisane odpowiednie uprawnienia.<br/><br/> Aby utworzyć rolę, zaloguj się do vSphere Web Client jako administrator serwera vCenter. Wybierz wystąpienie serwera vCenter server > **rolę Utwórz**. Określ nazwę roli, na przykład <em>Azure_Migrate</em>i przypisz [wymagane uprawnienia](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) do roli.


## <a name="assign-permissions-on-vcenter-objects"></a>Przypisywanie uprawnień do obiektów vCenter

Uprawnienia do obiektów magazynowych można przypisać przy użyciu jednej z dwóch metod:

- Przypisz rolę z wymaganymi uprawnieniami do używanego konta dla wszystkich obiektów nadrzędnych, które hostują maszyny wirtualne, które chcesz odnajdować/przeprowadzić migrację.
- Alternatywnie można przypisać rolę i konto użytkownika na poziomie centrum danych i propagować je do obiektów podrzędnych. Następnie nadaj kontu rolę **Brak dostępu** dla każdego obiektu, którego nie chcesz wykrywać/migrować. Nie zaleca się tego podejścia, ponieważ jest to uciążliwe i może udostępnić kontroli dostępu, ponieważ każdy nowy obiekt podrzędny jest automatycznie przyznawany dostęp dziedziczone z nadrzędnego.

Aby przypisać rolę do konta używanego dla wszystkich odpowiednich obiektów, wykonaj następujące czynności:

- **Do oceny:** W przypadku oceny maszyn wirtualnych zastosuj rolę tylko do **odczytu** do konta użytkownika vCenter dla wszystkich obiektów nadrzędnych obsługujących maszyny wirtualne, które chcesz odkryć. Obiekty nadrzędne obejmowały: hosta, folder hostów, klaster i folder klastrów w hierarchii, aż do centrum danych. Propagowanie tych uprawnień do obiektów podrzędnych w hierarchii.

    ![Przypisywanie uprawnień](./media/tutorial-assess-vmware/assign-perms.png)

- **W przypadku migracji bez agenta:** W przypadku migracji bez agenta zastosuj rolę zdefiniowaną przez użytkownika z [wymaganymi uprawnieniami](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) do konta użytkownika dla wszystkich obiektów nadrzędnych obsługujących maszyny wirtualne, które chcesz odkryć. Rolę można nazwać <em>Azure_Migrate</em>.

### <a name="scope-support"></a>Obsługa zakresu

Obecnie narzędzie oceny serwera nie może odnajdywać maszyn wirtualnych, jeśli konto vCenter ma dostęp udzielony na poziomie folderu vCenter VM. Obsługiwane są foldery hostów i klastrów.

Jeśli chcesz zakres odnajdywania przez foldery maszyn wirtualnych, wykonaj następną procedurę, aby upewnić się, że konto vCenter ma dostęp tylko do odczytu przypisany na poziomie maszyny Wirtualnej.

1. Przypisz uprawnienia tylko do odczytu dla wszystkich maszyn wirtualnych w folderach maszyn wirtualnych, które mają być objęte zakresem odnajdowania.
2. Udziel dostępu tylko do odczytu do wszystkich obiektów nadrzędnych, które hostują maszyny wirtualne.
    - Wszystkie obiekty nadrzędne (host, folder hostów, klaster, folder klastrów) w hierarchii aż do centrum danych są uwzględniane.
    - Nie trzeba propagować uprawnień do wszystkich obiektów podrzędnych.
3. Użyj poświadczeń do odnajdowania, wybierając centrum danych jako **zakres kolekcji**. Konfiguracja kontroli dostępu oparta na rolach zapewnia, że odpowiednie konto użytkownika vCenter ma dostęp tylko do maszyn wirtualnych specyficznych dla dzierżawy.


## <a name="next-steps"></a>Następne kroki

[Skonfiguruj urządzenie](how-to-set-up-appliance-vmware.md) i [rozpocznij ciągłe odnajdowanie](how-to-set-up-appliance-vmware.md#start-continuous-discovery-by-providing-vcenter-server-and-vm-credential).
