---
title: Konfigurowanie odzyskiwania po awarii dla wielowarstwowej wdrożenia systemu Dynamics AX przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania odzyskiwania po awarii dla systemu Dynamics AX przy użyciu usługi Azure Site Recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: b97bf56c23dfa96acf7cb5af5ac28b4270de117d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61281485"
---
# <a name="set-up-disaster-recovery-for-a-multitier-dynamics-ax-application"></a>Konfigurowanie odzyskiwania po awarii dla wielowarstwowej aplikacji Dynamics AX   




 Dynamics AX jest jednym z najbardziej popularnych rozwiązań ERP umożliwia przedsiębiorstwom standaryzowania procesów w lokalizacjach, zarządzanie zasobami i upraszcza proces zapewniania zgodności. Ponieważ aplikacja ma kluczowe znaczenie dla organizacji, na wypadek awarii, aplikacja powinna być działa w minimalny czas.

Już dziś Dynamics AX nie zapewnia awariami out-of--box możliwości odzyskiwania. Dynamics AX składa się z wielu składników serwera, np. serwera aplikacji Windows, usługi Azure Active Directory, Azure SQL Database, serwera SharePoint i usług Reporting Services. Do zarządzania awaryjnego odzyskiwania dla każdego z tych składników ręcznie jest nie tylko kosztowna, ale również podatne.

W tym artykule wyjaśniono, jak utworzyć rozwiązanie odzyskiwania po awarii dla aplikacji systemu Dynamics AX przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md). Obejmuje ona również testu planowane lub nieplanowane przejścia w tryb failover przy użyciu planu odzyskiwania jednego kliknięcia, obsługiwane konfiguracje i wymagania wstępne.



## <a name="prerequisites"></a>Wymagania wstępne

Implementowanie odzyskiwania po awarii dla aplikacji systemu Dynamics AX przy użyciu usługi Site Recovery wymaga następujących wymagań wstępnych:

• Skonfiguruj lokalne wdrożenie systemu Dynamics AX.

• Utwórz magazyn usługi Site Recovery w subskrypcji platformy Azure.

• Jeśli platforma Azure to lokacji odzyskiwania, uruchom narzędzie oceny gotowości maszyn wirtualnych platformy Azure na maszynach wirtualnych. Muszą być zgodne z usługami Azure Virtual Machines i Site Recovery.

## <a name="site-recovery-support"></a>Obsługa usługi Site Recovery

Na potrzeby tworzenia ten artykuł, użyliśmy maszyn wirtualnych VMware z systemu Dynamics AX 2012 R3 na Windows Server 2012 R2 Enterprise. Ponieważ replikacja usługi site recovery jest niezależny od aplikacji, oczekujemy, że zalecenia podane tutaj, aby pomieścić w następujących scenariuszach.

### <a name="source-and-target"></a>Źródłowe i docelowe

**Scenariusz** | **Lokacja dodatkowa** | **Platforma Azure**
--- | --- | ---
**Funkcja Hyper-V** | Yes | Yes
**VMware** | Yes | Yes
**Serwer fizyczny** | Yes | Yes

## <a name="enable-disaster-recovery-of-the-dynamics-ax-application-by-using-site-recovery"></a>Włącz odzyskiwanie po awarii aplikacji Dynamics AX przy użyciu usługi Site Recovery
### <a name="protect-your-dynamics-ax-application"></a>Ochrona aplikacji Dynamics AX
Aby umożliwić aplikacji pełną replikację i odzyskiwanie, muszą być chronione każdego składnika systemu Dynamics AX.

### <a name="1-set-up-active-directory-and-dns-replication"></a>1. Konfigurowanie replikacji usługi Active Directory i DNS

Usługi Active Directory jest wymagany w lokacji odzyskiwania po awarii dla aplikacji systemu Dynamics AX do funkcji. Zaleca się następujące dwie opcje oparte na złożoność przez klienta w środowisku lokalnym.

**Opcja 1**

Klient ma małej liczby aplikacji i jednego kontrolera domeny dla całej lokacji lokalnej i planuje razem przechodzą w całej lokacji. Zalecamy użycie replikacji usługi Site Recovery można replikować maszyny kontrolera domeny do lokacji dodatkowej (dotyczy tylko dla scenariuszy lokacja lokacja i lokacja na platformie Azure).

**Opcja 2**

Klient ma dużą liczbę aplikacji i jest uruchomiona w lesie usługi Active Directory i planów do trybu failover kilka aplikacji w danym momencie. Firma Microsoft zaleca skonfigurowanie dodatkowego kontrolera domeny w lokacji odzyskiwania po awarii (lokacji dodatkowej lub na platformie Azure).

 Aby uzyskać więcej informacji, zobacz [udostępnić kontrolera domeny w lokacji odzyskiwania po awarii](site-recovery-active-directory.md). W pozostałej części tego dokumentu przyjęto założenie, że kontroler domeny jest dostępny w lokacji odzyskiwania po awarii.

### <a name="2-set-up-sql-server-replication"></a>2. Konfigurowanie replikacji programu SQL Server
Techniczne wskazówki dotyczące to zalecana opcja ochrony z warstwy programu SQL, zobacz [Replikowanie aplikacji za pomocą programu SQL Server i usługi Azure Site Recovery](site-recovery-sql.md).

### <a name="3-enable-protection-for-the-dynamics-ax-client-and-application-object-server-vms"></a>3. Włącz ochronę dla klienta systemu Dynamics AX i maszyn wirtualnych serwera obiektu aplikacji
Skonfigurowanie odpowiednich Site Recovery na tego, czy maszyny wirtualne są wdrażane na podstawie [funkcji Hyper-V](site-recovery-hyper-v-site-to-azure.md) lub [VMware](site-recovery-vmware-to-azure.md).

> [!TIP]
> Firma Microsoft zaleca, aby skonfigurować częstotliwość spójnych awaryjnie do 15 minut.
>

Następującej migawki pokazuje stan ochrony maszyn wirtualnych Dynamics składnika w scenariuszu VMware ochrony witryny na platformie Azure.

![Chronione elementy](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4. Konfigurowanie sieci
**Konfigurowanie zasobów obliczeniowych maszyn wirtualnych i ustawień sieciowych**

Klient Dynamics AX i maszyn wirtualnych serwera obiektu aplikacji skonfigurować ustawienia sieciowe w usłudze Site Recovery, tak aby sieci maszyn wirtualnych Pobierz dołączone do sieć odzyskiwania po awarii w prawo, po włączeniu trybu failover. Upewnij się, że sieć odzyskiwania po awarii dla tych warstwach routingowi warstwę SQL.

Możesz wybrać maszynę Wirtualną w zreplikowanych elementów, aby skonfigurować ustawienia sieciowe, jak pokazano na następującej migawki:

* W przypadku serwerów serwera aplikacji wybierz zestaw poprawnej dostępności.

* Jeśli używasz statycznych adresów IP, określ adres IP, który chcesz, aby maszyna wirtualna może zająć **docelowy adres IP** pola tekstowego.

    ![Ustawienia sieci](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png)


### <a name="5-create-a-recovery-plan"></a>5. Tworzenie planu odzyskiwania

W usłudze Site Recovery w celu zautomatyzowania procesu trybu failover, można utworzyć planu odzyskiwania. Dodawanie warstwy aplikacji i warstwy sieci web w ramach planu odzyskiwania. Uporządkuj je w różnych grupach, tak aby frontonu zamknięty przed warstwy aplikacji.

1. Wybierz magazyn usługi Site Recovery w ramach subskrypcji, a następnie wybierz pozycję **plany odzyskiwania** kafelka.

2. Wybierz **+ planu odzyskiwania**i określ nazwę.

3. Wybierz **źródła** i **docelowej**. Element docelowy może być platformy Azure lub lokacji dodatkowej. Jeśli wybierzesz platformy Azure, należy określić w modelu wdrażania.

    ![Tworzenie planu odzyskiwania](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4. Wybierz pozycję serwera aplikacji i klienta maszyn wirtualnych do planu odzyskiwania, a następnie wybierz ✓.

    ![Wybierz elementy](./media/site-recovery-dynamics-ax/selectvms.png)

    Przykład planu odzyskiwania:

    ![Szczegóły planu odzyskiwania](./media/site-recovery-dynamics-ax/recoveryplan.png)

Plan odzyskiwania dla aplikacji systemu Dynamics AX można dostosować, dodając następujące kroki. Poprzednią migawkę przedstawia plan odzyskiwania ukończone po dodaniu wszystkich kroków.


* **Kroki pracy awaryjnej programu SQL Server**: Aby uzyskać informacji na temat określonych kroków odzyskiwania do programu SQL server, zobacz [replikacji aplikacji za pomocą programu SQL Server i usługi Azure Site Recovery](site-recovery-sql.md).

* **Grupy trybu failover 1**: Pracy awaryjnej maszyn wirtualnych serwera obiektu aplikacji.
Upewnij się, że wybrany punkt odzyskiwania jest możliwie najdokładniej bazą PIT, ale nie przed jej.

* **skrypt**: Dodaj usługę równoważenia obciążenia (tylko E-A).
Dodaj skrypt (za pośrednictwem usługi Azure Automation) po grupie maszyn wirtualnych serwera obiektu aplikacji funkcjonuje dodać moduł równoważenia obciążenia do niej. Można użyć skryptu do wykonania tego zadania. Aby uzyskać więcej informacji, zobacz [sposób dodawania równoważenia obciążenia na potrzeby odzyskiwania po awarii aplikacji wielowarstwowej](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/).

* **Grupy trybu failover 2**: Tryb failover klienta systemu Dynamics AX maszyn wirtualnych. Tryb failover maszyn wirtualnych warstwy internetowej jako część planu odzyskiwania.


### <a name="perform-a-test-failover"></a>Wykonaj test trybu failover

Aby uzyskać więcej informacji specyficznych dla usługi Active Directory podczas testowania trybu failover zobacz Przewodnik po "Rozwiązania odzyskiwania po awarii usługi Active Directory".

Aby uzyskać więcej informacji specyficznych dla programu SQL server podczas testowania trybu failover, zobacz [Replikowanie aplikacji za pomocą programu SQL Server i usługi Azure Site Recovery](site-recovery-sql.md).

1. Przejdź do witryny Azure portal i wybierz magazyn usługi Site Recovery.

2. Wybierz plan odzyskiwania, utworzony dla systemu Dynamics AX.

3. Wybierz pozycję **Test pracy w trybie failover**.

4. Wybierz sieć wirtualną, aby rozpocząć proces testu trybu failover.

5. Po skonfigurowaniu dodatkowej środowiska można wykonywać swoje operacji sprawdzania poprawności.

6. Po zakończeniu walidacji wybierz **ukończenia operacji sprawdzania poprawności** i testowe środowisko trybu failover jest czyszczona.

Aby uzyskać więcej informacji na temat wykonywania testu trybu failover, zobacz [testowanie trybu failover na platformie Azure w usłudze Site Recovery](site-recovery-test-failover-to-azure.md).

### <a name="perform-a-failover"></a>Przejścia w tryb failover

1. Przejdź do witryny Azure portal i wybierz magazyn usługi Site Recovery.

2. Wybierz plan odzyskiwania, utworzony dla systemu Dynamics AX.

3. Wybierz **trybu Failover**i wybierz **trybu Failover**.

4. Wybieranie sieci docelowej, a następnie wybierz pozycję **✓** można uruchomić procesu pracy awaryjnej.

Aby uzyskać więcej informacji na temat przełączeniem w tryb failover, zobacz [trybu Failover w usłudze Site Recovery](site-recovery-failover.md).

### <a name="perform-a-failback"></a>Wykonywanie powrotu po awarii

Kwestie związane z określonym programem SQL Server podczas powrotu po awarii, zobacz [Replikowanie aplikacji za pomocą programu SQL Server i usługi Azure Site Recovery](site-recovery-sql.md).

1. Przejdź do witryny Azure portal i wybierz magazyn usługi Site Recovery.

2. Wybierz plan odzyskiwania, utworzony dla systemu Dynamics AX.

3. Wybierz **trybu Failover**i wybierz **trybu Failover**.

4. Wybierz **zmianę kierunku**.

5. Wybierz odpowiednie opcje: synchronizacja danych i tworzenie maszyny Wirtualnej.

6. Wybierz **✓** można uruchomić procesu powrotu po awarii.


Aby uzyskać więcej informacji na ten powrotu po awarii, zobacz [maszyny wirtualne VMware podczas powrotu po awarii z platformy Azure do lokalnych](site-recovery-failback-azure-to-vmware.md).

## <a name="summary"></a>Podsumowanie
Przy użyciu usługi Site Recovery, można utworzyć plan odzyskiwania po awarii na automatyczne zakończenie aplikacji Dynamics AX. W przypadku przerwania działania można zainicjować trybu failover w ciągu kilku sekund z dowolnego miejsca i rozpocząć pracę aplikacji w kilka minut.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej o ochronie obciążeń przedsiębiorstwa z usługą Site Recovery, zobacz [jakie obciążenia mogę chronić?](site-recovery-workload.md).
