---
title: Replikowanie wielowarstwowa wdrożenia Dynamics AX za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób replikowania i chronić Dynamics AX przy użyciu usługi Azure Site Recovery
services: site-recovery
documentationcenter: ''
author: asgang
manager: rochakm
editor: ''
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: asgang
ms.openlocfilehash: b390f6c62a6ddf8c800f79b42a36dac2c4f4c908
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2018
ms.locfileid: "29876960"
---
# <a name="replicate-a-multitier-dynamics-ax-application-by-using-azure-site-recovery"></a>Replikowanie wielowarstwowej aplikacji Dynamics AX za pomocą usługi Azure Site Recovery

## <a name="overview"></a>Przegląd


 Dynamics AX jest jednym z najbardziej popularnych rozwiązań ERP umożliwia przedsiębiorstwom standaryzacji procesów w lokalizacjach, zarządzanie zasobami i uprościć zgodności. Ponieważ aplikacja jest szczególnie ważne dla organizacji, na wypadek awarii, aplikacja powinna być uruchomiona w minimalny czas.

Już dziś Dynamics AX nie zapewnia żadnych po awarii poza pole możliwości odzyskiwania. Dynamics AX składa się z wielu składników serwera, takie jak serwera aplikacji systemu Windows Azure Active Directory, baza danych SQL Azure, programu SharePoint Server i usług Reporting Services. Do zarządzania awarią odzyskiwania każdego z tych składników ręcznie jest nie tylko kosztowna, ale także błąd podatnych na błędy.

W tym artykule opisano, jak utworzyć rozwiązanie odzyskiwania po awarii dla aplikacji Dynamics AX przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md). Obejmuje ona również testu planowane lub nieplanowane przejście w tryb failover przy użyciu planu odzyskiwania jednym kliknięciem, obsługiwane konfiguracje i wymagania wstępne.



## <a name="prerequisites"></a>Wymagania wstępne

Wdrażanie odzyskiwania po awarii dla aplikacji Dynamics AX przy użyciu usługi Site Recovery wymagane są następujące wymagania wstępne:

• Skonfiguruj lokalnego wdrożenia programu Dynamics AX.

• Utwórz magazyn usługi Site Recovery w subskrypcji platformy Azure.

• Jeśli funkcja Azure to witryny odzyskiwania, uruchom narzędzie oceny gotowości maszyny wirtualnej platformy Azure na maszynach wirtualnych. Muszą być zgodne z usługami Azure, maszyny wirtualne i usługi Site Recovery.

## <a name="site-recovery-support"></a>Obsługa odzyskiwania lokacji

Na potrzeby tworzenia w tym artykule, będziemy używać maszyn wirtualnych VMware z Dynamics AX 2012 R3 w systemie Windows Server 2012 R2 Enterprise. Ponieważ replikacja z lokacji odzyskiwania jest niezależny od aplikacji, oczekujemy zalecenia zawarte w tym miejscu, aby pomieścić w następujących scenariuszach.

### <a name="source-and-target"></a>Źródłowa i docelowa

**Scenariusz** | **Do lokacji dodatkowej** | **Platforma Azure**
--- | --- | ---
**Funkcja Hyper-V** | Yes | Yes
**VMware** | Yes | Yes
**Serwer fizyczny** | Yes | Yes

## <a name="enable-disaster-recovery-of-the-dynamics-ax-application-by-using-site-recovery"></a>Włącz odzyskiwanie po awarii aplikacji Dynamics AX przy użyciu usługi Site Recovery
### <a name="protect-your-dynamics-ax-application"></a>Ochrona aplikacji Dynamics AX
Aby włączyć replikację kompletna aplikacja i odzyskiwania, musi być chroniony każdego składnika programu Dynamics AX.

### <a name="1-set-up-active-directory-and-dns-replication"></a>1. Konfigurowanie replikacji usługi Active Directory i DNS

Usługi Active Directory jest wymagany w lokacji odzyskiwania po awarii dla aplikacji Dynamics AX do funkcji. Zaleca się następujące dwie opcje, w zależności od złożoności klienta w środowisku lokalnym.

**Opcja 1**

Klient ma małej liczby aplikacji i jednego kontrolera domeny dla całej lokacji lokalnymi i planuje razem ulegają awarii w całej lokacji. Zalecane jest użycie usługi Site Recovery replikacji do replikowania maszyny kontrolera domeny do lokacji dodatkowej (dotyczy tylko w scenariuszach zarówno do lokacji i lokacji do platformy Azure).

**Opcja 2**

Klient ma dużą liczbę aplikacji i jest uruchomiona w lesie usługi Active Directory i planów awaryjnie kilka aplikacji w czasie. Zaleca się Konfigurowanie dodatkowego kontrolera domeny w lokacji odzyskiwania po awarii (lokacji dodatkowej lub na platformie Azure).

 Aby uzyskać więcej informacji, zobacz [udostępnić kontrolera domeny w lokacji odzyskiwania po awarii](site-recovery-active-directory.md). W pozostałej części tego dokumentu przyjęto założenie, że kontroler domeny jest dostępny w lokacji odzyskiwania po awarii.

### <a name="2-set-up-sql-server-replication"></a>2. Konfigurowanie replikacji programu SQL Server
Aby techniczne to zalecana opcja ochrony warstwy SQL, zobacz [Replikowanie aplikacji za pomocą programu SQL Server i usługi Azure Site Recovery](site-recovery-sql.md).

### <a name="3-enable-protection-for-the-dynamics-ax-client-and-application-object-server-vms"></a>3. Włącz ochronę dla klienta programu Dynamics AX i maszyn wirtualnych serwera obiektu aplikacji
Wykonaj odpowiednie konfiguracji usługi Site Recovery na czy maszyny wirtualne są wdrażane na podstawie [funkcji Hyper-V](site-recovery-hyper-v-site-to-azure.md) lub [VMware](site-recovery-vmware-to-azure.md).

> [!TIP]
> Firma Microsoft zaleca, aby skonfigurować częstotliwość spójna w razie awarii do 15 minut.
>

Następującej migawki pokazuje stan ochrony składnika Dynamics maszyn wirtualnych w scenariuszu ochrony lokacji do platformy Azure VMware.

![Elementy chronione](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4. Konfigurowanie sieci
**Konfigurowanie obliczeń maszyny Wirtualnej i ustawień sieciowych**

Dynamics AX klienta i maszyn wirtualnych serwera obiektu aplikacji skonfigurować ustawienia sieciowe w usłudze Site Recovery, tak aby sieci maszyn wirtualnych uzyskać dołączony do sieci odzyskiwania po awarii prawo po pracy awaryjnej. Sprawdź, czy sieć odzyskiwania po awarii dla tych warstwach jest routingu do warstwy SQL.

Możesz wybrać maszynę Wirtualną w zreplikowanych elementów, aby skonfigurować ustawienia sieciowe opisane w następującej migawki:

* W przypadku serwerów serwera aplikacji wybierz zestaw dostępności jest prawidłowa.

* Jeśli używasz statycznego adresu IP, określ adres IP interesujące maszyny Wirtualnej w **docelowy adres IP** pola tekstowego.

    ![Ustawienia sieci ](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png).


### <a name="5-create-a-recovery-plan"></a>5. Tworzenie planu odzyskiwania

W usłudze Site Recovery można zautomatyzować proces trybu failover można utworzyć planu odzyskiwania. Dodawanie warstwy aplikacji i warstwy sieci web w planie odzyskiwania. Kolejność je w różnych grupach, dzięki czemu frontonu zamknięty przed warstwy aplikacji.

1. Wybierz magazyn usługi Site Recovery w ramach subskrypcji, a następnie wybierz **plany odzyskiwania** kafelka.

2. Wybierz **+ planu odzyskiwania**i określ nazwę.

3. Wybierz **źródła** i **docelowej**. Element docelowy może być Azure lub lokacji dodatkowej. Jeśli wybierzesz Azure, należy określić modelu wdrażania.

    ![Tworzenie planu odzyskiwania](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4. Wybierz serwera aplikacji i klienta maszyn wirtualnych do planu odzyskiwania, a następnie wybierz ✓.

    ![Wybierz elementy](./media/site-recovery-dynamics-ax/selectvms.png)

    Przykład planu odzyskiwania:

    ![Szczegóły planu odzyskiwania](./media/site-recovery-dynamics-ax/recoveryplan.png)

Plan odzyskiwania aplikacji Dynamics AX można dostosować, dodając następujące kroki. Poprzednią migawkę pokazuje planu odzyskiwania ukończone po dodaniu wszystkich kroków.


* **Kroki pracy awaryjnej programu SQL Server**: informacje o krokach odzyskiwania określonych do programu SQL server, zobacz [replikacji aplikacji za pomocą programu SQL Server i usługi Azure Site Recovery](site-recovery-sql.md).

* **Praca awaryjna grupy 1**: tryb failover maszyn wirtualnych serwera dla obiekt aplikacji.
Upewnij się, że wybrany punkt odzyskiwania jest jak najbliższe w bazie danych PIT, ale nie wcześniejsze go.

* **Skrypt**: Dodaj usługi równoważenia obciążenia (tylko A E).
Dodawanie skryptów (za pośrednictwem usługi Automatyzacja Azure) po grupie maszyny Wirtualnej serwera obiektu aplikacji pojawia się, aby dodać usługi równoważenia obciążenia. Skrypt służy do wykonania tego zadania. Aby uzyskać więcej informacji, zobacz [sposób dodawania usługi równoważenia obciążenia dla odzyskiwania po awarii aplikacji wielowarstwowej](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/).

* **Praca awaryjna grupy 2**: klient Dynamics AX maszyn wirtualnych w tryb failover. Tryb failover warstwa sieci web maszyn wirtualnych w ramach planu odzyskiwania.


### <a name="perform-a-test-failover"></a>Wykonaj test trybu failover

Aby uzyskać więcej informacji specyficznych dla usługi Active Directory podczas testowania trybu failover zobacz Przewodnik uzupełniający po "Rozwiązanie odzyskiwania po awarii usługi Active Directory".

Aby uzyskać więcej informacji specyficznych dla programu SQL server podczas testowania trybu failover, zobacz [Replikowanie aplikacji za pomocą programu SQL Server i usługi Azure Site Recovery](site-recovery-sql.md).

1. Przejdź do portalu Azure, a następnie wybierz magazyn usługi Site Recovery.

2. Wybierz plan odzyskiwania utworzone dla Dynamics AX.

3. Wybierz **testowanie trybu Failover**.

4. Wybierz sieć wirtualną, aby rozpocząć proces test trybu failover.

5. Po skonfigurowaniu dodatkowej środowiska można wykonywać z operacji sprawdzania poprawności.

6. Po zakończeniu operacji sprawdzania poprawności, wybierz **ukończenie operacji sprawdzania poprawności** i testowe środowisko trybu failover jest wyczyszczone.

Aby uzyskać więcej informacji dotyczących wykonywania test trybu failover, zobacz [testowanie trybu failover na platformie Azure w usłudze Site Recovery](site-recovery-test-failover-to-azure.md).

### <a name="perform-a-failover"></a>Przejściu w tryb failover

1. Przejdź do portalu Azure, a następnie wybierz magazyn usługi Site Recovery.

2. Wybierz plan odzyskiwania utworzone dla Dynamics AX.

3. Wybierz **pracy awaryjnej**i wybierz **pracy awaryjnej**.

4. Wybierz sieć docelowa, a następnie wybierz **✓** rozpoczęcie pracy awaryjnej.

Aby uzyskać więcej informacji na ten tryb failover, zobacz [pracy awaryjnej w usłudze Site Recovery](site-recovery-failover.md).

### <a name="perform-a-failback"></a>Wykonaj powrót po awarii

Aby uzyskać informacje o określonym do programu SQL Server podczas powrotu po awarii, zobacz [Replikowanie aplikacji za pomocą programu SQL Server i usługi Azure Site Recovery](site-recovery-sql.md).

1. Przejdź do portalu Azure, a następnie wybierz magazyn usługi Site Recovery.

2. Wybierz plan odzyskiwania utworzone dla Dynamics AX.

3. Wybierz **pracy awaryjnej**i wybierz **pracy awaryjnej**.

4. Wybierz **zmianę kierunku**.

5. Wybierz odpowiednie opcje: synchronizacji danych i tworzenie maszyny Wirtualnej.

6. Wybierz **✓** do rozpoczęcia procesu powrotu po awarii.


Aby uzyskać więcej informacji na ten powrotu po awarii, zobacz [maszyn wirtualnych VMware powrotu po awarii z platformy Azure do środowiska lokalnego](site-recovery-failback-azure-to-vmware.md).

## <a name="summary"></a>Podsumowanie
Przy użyciu usługi Site Recovery, można utworzyć plan odzyskiwania ukończenia automatycznego po awarii dla aplikacji programu Dynamics AX. W przypadku przerw w działaniu można zainicjować trybu failover w ciągu kilku sekund z dowolnego miejsca i pobrać i uruchomić aplikację w minutach.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej o ochronie obciążeń przedsiębiorstwa z usługą Site Recovery, zobacz [jakie obciążenia mogę chronić?](site-recovery-workload.md).
