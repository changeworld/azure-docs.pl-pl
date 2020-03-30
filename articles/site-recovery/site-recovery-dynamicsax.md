---
title: Odzyskiwanie po awarii systemu Dynamics AX za pomocą usługi Azure Site Recovery
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii dla systemu Dynamics AX za pomocą usługi Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: 0b32f00374aa8ce6c41415e28f319e3e7d5abddb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941590"
---
# <a name="set-up-disaster-recovery-for-a-multitier-dynamics-ax-application"></a>Konfigurowanie odzyskiwania po awarii dla wielowarstwowej aplikacji Dynamics AX   




 Dynamics AX to jedno z najpopularniejszych rozwiązań ERP stosowane przez przedsiębiorstwa do standaryzacji procesów w różnych lokalizacjach, zarządzania zasobami i upraszczania zgodności. Ponieważ aplikacja jest krytyczna dla organizacji, w przypadku awarii, aplikacja powinna być uruchomiona i działa w minimalnym czasie.

Obecnie dynamics AX nie zapewnia żadnych gotowych funkcji odzyskiwania po awarii. System Dynamics AX składa się z wielu składników serwera, takich jak Windows Application Object Server, Azure Active Directory, Azure SQL Database, SharePoint Server i Reporting Services. Aby zarządzać odzyskiwanie po awarii każdego z tych składników ręcznie jest nie tylko drogie, ale także podatne na błędy.

W tym artykule wyjaśniono, jak utworzyć rozwiązanie do odzyskiwania po awarii dla aplikacji Dynamics AX przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md). Obejmuje ona również planowane/nieplanowane testy awaryjne przy użyciu planu odzyskiwania jednym kliknięciem, obsługiwanych konfiguracji i wymagań wstępnych.



## <a name="prerequisites"></a>Wymagania wstępne

Implementowanie odzyskiwania po awarii dla aplikacji Dynamics AX przy użyciu usługi Site Recovery wymaga następujących wymagań wstępnych:

• Skonfiguruj lokalne wdrożenie Dynamics AX.

• Tworzenie magazynu odzyskiwania witryny w ramach subskrypcji platformy Azure.

• Jeśli platforma Azure jest twoją witryną odzyskiwania, uruchom narzędzie oceny gotowości maszyny wirtualnej platformy Azure na maszynach wirtualnych. Muszą być zgodne z usługami Azure Virtual Machines i usługi odzyskiwania witryn.

## <a name="site-recovery-support"></a>Obsługa usługi Site Recovery

W celu utworzenia tego artykułu użyliśmy maszyn wirtualnych VMware z systemem Dynamics AX 2012 R3 w systemie Windows Server 2012 R2 Enterprise. Ponieważ replikacja usługi Site Recovery jest niezależna od aplikacji, oczekujemy, że zalecenia podane w tym miejscu będą zawierać dla następujących scenariuszy.

### <a name="source-and-target"></a>Źródło i cel

**Scenariusz** | **Do lokacji dodatkowej** | **Na platformę Azure**
--- | --- | ---
**Funkcja Hyper-V** | Tak | Tak
**Vmware** | Tak | Tak
**Serwer fizyczny** | Tak | Tak

## <a name="enable-disaster-recovery-of-the-dynamics-ax-application-by-using-site-recovery"></a>Włączanie odzyskiwania po awarii aplikacji Dynamics AX przy użyciu funkcji Odzysk lokacji
### <a name="protect-your-dynamics-ax-application"></a>Chroń swoją aplikację Dynamics AX
Aby włączyć pełną replikację i odzyskiwanie aplikacji, każdy składnik dynamics AX musi być chroniony.

### <a name="1-set-up-active-directory-and-dns-replication"></a>1. Konfigurowanie usługi Active Directory i replikacji DNS

Usługa Active Directory jest wymagana w witrynie odzyskiwania po awarii, aby aplikacja Dynamics AX działała. Firma Microsoft zaleca następujące dwie opcje w oparciu o złożoność środowiska lokalnego klienta.

**Wariant 1**

Klient ma niewielką liczbę aplikacji i jeden kontroler domeny dla całej lokacji lokalnej i planuje połączenie awaryjne całej lokacji razem. Zaleca się, aby użyć replikacji usługi Site Recovery do replikacji komputera kontrolera domeny do lokacji dodatkowej (dotyczy zarówno scenariuszy lokacja lokacja-lokacja, jak i od lokacji do platformy Azure).

**Wariant 2**

Klient ma dużą liczbę aplikacji i uruchamia las usługi Active Directory i planuje awaryjnie kilka aplikacji naraz. Zaleca się skonfigurowanie dodatkowego kontrolera domeny w lokacji odzyskiwania po awarii (lokacji dodatkowej lub na platformie Azure).

 Aby uzyskać więcej informacji, zobacz [Udostępnianie kontrolera domeny w witrynie odzyskiwania po awarii](site-recovery-active-directory.md). W pozostałej części tego dokumentu zakładamy, że kontroler domeny jest dostępny w lokacji odzyskiwania po awarii.

### <a name="2-set-up-sql-server-replication"></a>2. Konfigurowanie replikacji programu SQL Server
Aby uzyskać wskazówki techniczne dotyczące zalecanej opcji ochrony warstwy SQL, zobacz [Replikowanie aplikacji za pomocą programu SQL Server i usługi Azure Site Recovery](site-recovery-sql.md).

### <a name="3-enable-protection-for-the-dynamics-ax-client-and-application-object-server-vms"></a>3. Włącz ochronę maszyn wirtualnych klienta i serwera obiektów aplikacji Dynamics AX
Wykonaj odpowiednią konfigurację odzyskiwania lokacji w zależności od tego, czy maszyny wirtualne są wdrażane w funkcji [Hyper-V](site-recovery-hyper-v-site-to-azure.md) czy [VMware](site-recovery-vmware-to-azure.md).

> [!TIP]
> Zaleca się skonfigurowanie częstotliwości spójnej z awariami do 15 minut.
>

Poniższa migawka przedstawia stan ochrony maszyn wirtualnych składników Dynamics w scenariuszu ochrony lokacji platformy VMware na platformie Azure.

![Chronione przedmioty](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4. Konfigurowanie sieci
**Konfigurowanie ustawień obliczeniowych i sieciowych maszyn wirtualnych**

W przypadku maszyn wirtualnych klienta dynamics AX i serwera obiektów aplikacji skonfiguruj ustawienia sieciowe w programie Site Recovery, tak aby sieci maszyn wirtualnych zostały połączone z właściwą siecią odzyskiwania po awarii po przełączeniu awaryjnym. Upewnij się, że sieć odzyskiwania po awarii dla tych warstw jest rutowalna do warstwy SQL.

Można wybrać maszynę wirtualną w replikowanych elementach, aby skonfigurować ustawienia sieciowe, jak pokazano w następującej migawce:

* W przypadku serwerów application object server wybierz odpowiedni zestaw dostępności.

* Jeśli używasz statycznego adresu IP, określ adres IP, który ma zostać przyjmowany przez maszynę wirtualną w polu **tekstowym Docelowy adres IP.**

    ![Ustawienia sieciowe](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png)


### <a name="5-create-a-recovery-plan"></a>5. Tworzenie planu naprawczego

Można utworzyć plan odzyskiwania w układzie Site Recovery, aby zautomatyzować proces pracy awaryjnej. Dodaj warstwę aplikacji i warstwę sieci web w planie odzyskiwania. Zamów je w różnych grupach, tak aby front-end został zamknięty przed warstwą aplikacji.

1. Wybierz magazyn odzyskiwania witryny w ramach subskrypcji i wybierz kafelek **Plany odzyskiwania.**

2. Wybierz **+ Plan odzyskiwania**i określ nazwę.

3. Wybierz **źródło** i **cel**. Obiekt docelowy może być platformą Azure lub lokacją dodatkową. Jeśli wybierzesz platformę Azure, należy określić model wdrażania.

    ![Tworzenie planu odzyskiwania](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4. Wybierz serwer obiektów aplikacji i maszyny wirtualne klienta dla planu odzyskiwania i wybierz ✓.

    ![Zaznaczanie elementów](./media/site-recovery-dynamics-ax/selectvms.png)

    Przykład planu naprawczego:

    ![Szczegóły planu odzyskiwania](./media/site-recovery-dynamics-ax/recoveryplan.png)

Plan odzyskiwania dla aplikacji Dynamics AX można dostosować, dodając następujące kroki. Poprzednia migawka pokazuje pełny plan odzyskiwania po dodaniu wszystkich kroków.


* **Kroki pracy awaryjnej programu SQL Server:** Aby uzyskać informacje o krokach odzyskiwania specyficznych dla serwera SQL, zobacz [Aplikacje replikacji z programem SQL Server i usługą Azure Site Recovery](site-recovery-sql.md).

* **Grupa trybu failover 1:** Praca awaryjna maszyn wirtualnych serwera obiektów aplikacji.
Upewnij się, że wybrany punkt odzyskiwania jest jak najbliżej bazy danych PIT, ale nie przed nim.

* **Skrypt**: Dodaj moduł równoważenia obciążenia (tylko E-A).
Dodaj skrypt (za pośrednictwem usługi Azure Automation) po pojawi się grupa maszyn wirtualnych serwera obiektów aplikacji, aby dodać do niego moduł równoważenia obciążenia. Do wykonania tego zadania można użyć skryptu. Aby uzyskać więcej informacji, zobacz [Jak dodać moduł równoważenia obciążenia dla wielowarstwowego odzyskiwania po awarii aplikacji](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/).

* **Grupa trybu failover 2:** Praca awaryjna maszyn wirtualnych klienta Systemu Dynamics AX. Praca awaryjna maszyn wirtualnych warstwy sieci web w ramach planu odzyskiwania.


### <a name="perform-a-test-failover"></a>Wykonywanie testu trybu failover

Aby uzyskać więcej informacji dotyczących usługi Active Directory podczas pracy awaryjnej testu, zobacz przewodnik towarzyszący "Rozwiązanie do odzyskiwania po awarii usługi Active Directory".

Aby uzyskać więcej informacji dotyczących serwera SQL podczas pracy awaryjnej testu, zobacz [Replikowanie aplikacji za pomocą programu SQL Server i usługi Azure Site Recovery](site-recovery-sql.md).

1. Przejdź do witryny Azure portal i wybierz magazyn odzyskiwania witryny.

2. Wybierz plan odzyskiwania utworzony dla systemu Dynamics AX.

3. Wybierz pozycję **Test pracy w trybie failover**.

4. Wybierz sieć wirtualną, aby rozpocząć testowy proces pracy awaryjnej.

5. Po zakończeniu środowiska pomocniczego można wykonać sprawdzanie poprawności.

6. Po zakończeniu sprawdzania poprawności wybierz **sprawdzanie poprawności zakończone** i środowisko pracy awaryjnej testu jest czyszczone.

Aby uzyskać więcej informacji na temat wykonywania pracy awaryjnej testu, zobacz [Testowanie pracy awaryjnej na platformie Azure w usłudze Site Recovery.](site-recovery-test-failover-to-azure.md)

### <a name="perform-a-failover"></a>Wykonywanie pracy awaryjnej

1. Przejdź do witryny Azure portal i wybierz magazyn odzyskiwania witryny.

2. Wybierz plan odzyskiwania utworzony dla systemu Dynamics AX.

3. Wybierz opcję **Przewijanie awaryjne**i wybierz pozycję **Przewijanie awaryjne**.

4. Wybierz sieć docelową i wybierz **✓,** aby rozpocząć proces pracy awaryjnej.

Aby uzyskać więcej informacji na temat wykonywania pracy awaryjnej, zobacz [Działanie awaryjne w odzyskiwaniu witryny](site-recovery-failover.md).

### <a name="perform-a-failback"></a>Wykonywanie powrotu po awarii

Aby zapoznać się z zagadnieniami dotyczącymi programu SQL Server podczas powrotu po awarii, zobacz [Replikowanie aplikacji za pomocą programu SQL Server i usługi Azure Site Recovery](site-recovery-sql.md).

1. Przejdź do witryny Azure portal i wybierz magazyn odzyskiwania witryny.

2. Wybierz plan odzyskiwania utworzony dla systemu Dynamics AX.

3. Wybierz opcję **Przewijanie awaryjne**i wybierz pozycję **Przewijanie awaryjne**.

4. Wybierz **pozycję Zmień kierunek**.

5. Wybierz odpowiednie opcje: synchronizacja danych i tworzenie maszyn wirtualnych.

6. Wybierz **✓,** aby rozpocząć proces powrotu po awarii.


Aby uzyskać więcej informacji na temat wykonywania powrotu po awarii, zobacz [Failback VMware maszyn wirtualnych z platformy Azure do lokalnego.](site-recovery-failback-azure-to-vmware.md)

## <a name="summary"></a>Podsumowanie
Korzystając z funkcji Odzysk witryny, można utworzyć kompletny zautomatyzowany plan odzyskiwania po awarii dla aplikacji Dynamics AX. W przypadku zakłócenia można zainicjować pracy awaryjnej w ciągu kilku sekund z dowolnego miejsca i uzyskać aplikację i działa w ciągu kilku minut.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat ochrony obciążeń przedsiębiorstw za pomocą usługi Site Recovery, zobacz [Jakie obciążenia można chronić?](site-recovery-workload.md).
