---
title: Migrowanie serwerów systemu Windows Server 2008 w środowisku lokalnym na platformie Azure przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób migracji maszyn systemu Windows Server 2008 w środowisku lokalnym na platformie Azure przy użyciu usługi Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: bsiva
manager: abhemraj
editor: raynew
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 09/22/2018
ms.author: bsiva
ms.openlocfilehash: d15a5b62a148e971c0740f01744fce308e502340
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47056040"
---
# <a name="migrate-servers-running-windows-server-2008-to-azure"></a>Migrowanie serwerów z systemem Windows Server 2008 na platformie Azure

W tym samouczku pokazano, jak przeprowadzić migrację lokalnych serwerów z systemem Windows Server 2008 lub 2008 R2 na platformie Azure przy użyciu usługi Azure Site Recovery. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przygotowanie środowiska lokalnego do migracji
> * Konfigurowanie środowiska docelowego
> * Konfigurowanie zasad replikacji
> * Włączanie replikacji
> * Uruchamianie testowania migracji w celu sprawdzenia, czy wszystko działa zgodnie z oczekiwaniami
> * Tryb failover na platformie Azure i przeprowadzić migrację

Ograniczenia i znane problemy sekcji niektóre ograniczenia i obejścia znanych problemów z tej listy, mogą wystąpić podczas migracji systemu Windows Server 2008, działające na platformie Azure. 


## <a name="supported-operating-systems-and-environments"></a>Obsługiwane systemy operacyjne i środowiska


|System operacyjny  | Środowisko lokalne  |
|---------|---------|
|Windows Server 2008 z dodatkiem SP2 — wersje 32-bitowe i 64-bitowych (IA-32 i x86 64)</br>— Standard</br>-Enterprise</br>-Datacenter   |     Maszyny wirtualne VMware, maszyny wirtualne funkcji Hyper-V i serwerów fizycznych    |
|Windows Server 2008 R2 z dodatkiem SP1 — wersja 64-bitowa</br>— Standard</br>-Enterprise</br>-Datacenter     |     Maszyny wirtualne VMware, maszyny wirtualne funkcji Hyper-V i serwerów fizycznych|

> [!WARNING]
> - Migracja serwerów z systemem Server Core nie jest obsługiwana.
> - Upewnij się, że masz najnowszy dodatek service pack i aktualizacje Windows przed migracją.


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem warto zapoznać się z architekturą usługi Azure Site Recovery dla [migracji serwera VMware i fizycznych](vmware-azure-architecture.md) lub [migracji maszyny wirtualnej funkcji Hyper-V](hyper-v-azure-architecture.md) 

Aby przeprowadzić migrację maszyn wirtualnych funkcji Hyper-V z systemem Windows Server 2008 lub Windows Server 2008 R2, należy wykonać czynności opisane w [Migrowanie maszyn lokalnych na platformę Azure](migrate-tutorial-on-premises-azure.md) samouczka.

Pozostałej części tego samouczka dowiesz się, jak można migrować lokalne maszyny wirtualne VMware i serwerów fizycznych z systemem Windows Server 2008 lub 2008 R2.


## <a name="limitations-and-known-issues"></a>Ograniczenia i znane problemy

- Serwer konfiguracji, dodatkowych serwerów przetwarzania i usługę mobilności, używane do przeprowadzania migracji serwerów systemu Windows Server 2008 z dodatkiem SP2 powinna być uruchomiona wersja 9.19.0.0 lub nowszej oprogramowania usługi Azure Site Recovery.

- Funkcji spójności wielu maszyn wirtualnych i punktów odzyskiwania zapewniających spójność aplikacji nie są obsługiwane dla replikacji serwerów z systemem Windows Server 2008 z dodatkiem SP2. Serwery Windows Server 2008 SP2, powinny być migrowane do punktu odzyskiwania spójnego na poziomie awarii. Punkty odzyskiwania spójnego na poziomie awarii są generowane co 5 minut, domyślnie. Przy użyciu zasad replikacji z częstotliwością migawek spójnych na poziomie skonfigurowanej aplikacji spowoduje, że kondycja replikacji włączyć krytyczny ze względu na Brak punktów odzyskiwania zapewniających spójność aplikacji. W celu uniknięcia wyników fałszywie dodatnich, należy ustawić częstotliwość migawek spójnych z aplikacją w zasadach replikacji na wartość "Wyłączone".

- Serwery są migrowane powinny mieć .NET Framework 3.5 Service Pack 1 dla usługi mobilności do pracy.

- Jeśli serwer ma dyski dynamiczne, mogą pojawić się w niektórych konfiguracjach, które dyski są w trybie awaryjnym serwera są oznaczone w trybie offline lub jest przedstawiana jako dysków obcych. Może także zauważyć, że stan dublowany zestaw woluminy dublowane na dynamicznych dysków jest oznaczona "Nadmiarowości nie powiodło się". Można naprawić ten problem, z diskmgmt.msc, ręcznie zaimportować te dyski i ponownie uaktywnia je.

- Serwery są migrowane powinny mieć vmstorfl.sys sterownika. Tryb failover może zakończyć się niepowodzeniem, jeśli sterownik nie jest obecny na serwerze migrowane. 
  > [!TIP]
  >Sprawdź, czy sterownik znajduje się na "C:\Windows\system32\drivers\vmstorfl.sys". Jeśli sterownik nie zostanie znaleziony, możesz obejść ten problem, tworząc plik zastępczy w miejscu. 
  >
  > Otwórz wiersz polecenia (Uruchom > cmd) i uruchom następujące polecenie: "Kopiuj nul c:\Windows\system32\drivers\vmstorfl.sys"

- Nie można nawiązać połączenie RDP na serwerach Windows Server 2008 z dodatkiem SP2 z 32-bitowym systemie operacyjnym, natychmiast po zakończeniu przełączone w tryb failover lub testu przełączone w tryb failover Azure. Uruchom ponownie nieudane przez maszynę wirtualną w witrynie Azure portal i spróbuj ponownie nawiązać połączenie. Jeśli nadal nie można się połączyć, sprawdź, jeśli serwer jest skonfigurowany do zezwalania na połączenia pulpitu zdalnego i upewnij się, że nie istnieją żadne reguły zapory lub grupy zabezpieczeń sieci blokuje połączenia. 
  > [!TIP]
  > Testowanie trybu failover zaleca się przed przeprowadzeniem migracji serwerów. Upewnij się, że zostało wykonane co najmniej jeden udanego testu trybu awaryjnego na każdym serwerze, na które ma być migrowane. W ramach testowania trybu failover nawiązać połączenia testowego w trybie Failover maszyny i upewnij się, elementów roboczych, zgodnie z oczekiwaniami.
  >
  >Test operacji trybu failover jest niezakłócającego i pomaga testować migracje dzięki tworzeniu maszyn wirtualnych w sieci izolowanej wybranych przez użytkownika. W odróżnieniu od operacji trybu failover, podczas testu trybu failover operacji replikacji danych w dalszym ciągu progres. Można wykonać, wiele testy trybu failover, jak chcesz, zanim wszystko będzie gotowe do migracji. 
  >
  >


## <a name="getting-started"></a>Wprowadzenie

Wykonaj następujące zadania w celu przygotowania środowiska platformy Azure serwer fizyczny/VMware subskrypcji i dla lokalnego:

1. [Przygotowywanie platformy Azure](tutorial-prepare-azure.md)
2. Przygotuj lokalne [VMware](vmware-azure-tutorial-prepare-on-premises.md)


## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) > **Recovery Services**.
2. Kliknij kolejno pozycje **Utwórz zasób** > **Monitorowanie i zarządzanie** > **Backup and Site Recovery**.
3. W **nazwa**, określ przyjazną nazwę **migracji W2K8**. Jeśli masz więcej niż jedną subskrypcję, wybierz jedną z nich.
4. Utwórz grupę zasobów **w2k8migrate**.
5. Określ region platformy Azure. Aby sprawdzić obsługiwane regiony, zobacz sekcję dotyczącą dostępności geograficznej w temacie [Szczegóły cennika usługi Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Aby szybko uzyskać dostęp do magazynu z pulpitu nawigacyjnego, kliknij pozycję **Przypnij do pulpitu nawigacyjnego**, a następnie kliknij pozycję **Utwórz**.

   ![Nowy magazyn](media/migrate-tutorial-windows-server-2008/migrate-windows-server-2008-vault.png)

Nowy magazyn zostanie dodany do sekcji **Pulpit nawigacyjny** w obszarze **Wszystkie zasoby** oraz pojawi się na stronie głównej **Magazyny usługi Recovery Services**.


## <a name="prepare-your-on-premises-environment-for-migration"></a>Przygotowanie środowiska lokalnego do migracji

- Migrowanie maszyn wirtualnych systemu Windows Server 2008, działające w programie VMware, [instalacji na lokalnym serwerze konfiguracji w programie VMware](vmware-azure-tutorial.md#set-up-the-source-environment).
- Jeśli na serwerze konfiguracji nie można zainstalować jako maszynę wirtualną VMware [skonfigurować serwer konfiguracji na serwerze fizycznym w środowisku lokalnym lub maszynie wirtualnej](physical-azure-disaster-recovery.md#set-up-the-source-environment).

## <a name="set-up-the-target-environment"></a>Konfigurowanie środowiska docelowego

Wybierz i zweryfikuj zasoby docelowe.

1. Kliknij kolejno pozycje **Przygotowywanie infrastruktury** > **Docelowa** i wybierz subskrypcję platformy Azure do użycia.
2. Określ model wdrażania usługi Resource Manager.
3. Usługa Site Recovery sprawdza, czy masz co najmniej jedno zgodne konto magazynu Azure i co najmniej jedną sieć platformy Azure.


## <a name="set-up-a-replication-policy"></a>Konfigurowanie zasad replikacji

1. Aby utworzyć nowe zasady replikacji, kliknij przycisk **infrastruktura usługi Site Recovery** > **zasady replikacji** > **+ zasady replikacji**.
2. W **Tworzenie zasad replikacji**, określ nazwę zasad.
3. W **progu celu punktu odzyskiwania**, określ limit punktu odzyskiwania celu (RPO). Alert jest generowany, gdy cel punktu odzyskiwania replikacji przekracza ten limit.
4. W **czas przechowywania punktu odzyskiwania**, określić czas (w godzinach) okno przechowywania dla każdego punktu odzyskiwania. Replikowane maszyny wirtualne można odzyskać do dowolnego punktu w tym oknie. Do przechowywania do 24 godzin jest obsługiwany w przypadku maszyn replikowanych do magazynu premium storage, a następnie 72 godzin w przypadku magazynu w warstwie standardowa.
5. W **częstotliwość migawek spójności aplikacji**, określ **poza**. Kliknij przycisk **OK**, aby utworzyć zasady.

Zasady zostaną automatycznie skojarzone z serwerem konfiguracji.

> [!WARNING]
> Upewnij się, że podajesz **OFF** w spójny na poziomie aplikacji migawki odpowiednie ustawienie częstotliwości zasad replikacji. Podczas replikowania serwerów z systemem Windows Server 2008 obsługiwane są tylko punkty odzyskiwania spójne na poziomie awarii. Określenie, które jakakolwiek inna wartość, aby uzyskać częstotliwość migawek spójności aplikacji spowoduje fałszywym alertom przez włączenie replikacji kondycję serwera krytyczne z powodu braku punktów odzyskiwania zapewniających spójność aplikacji.

   ![Tworzenie zasad replikacji](media/migrate-tutorial-windows-server-2008/create-policy.png)

## <a name="enable-replication"></a>Włączanie replikacji

[Włącz replikację](physical-azure-disaster-recovery.md#enable-replication) dla systemu Windows Server 2008 z dodatkiem SP2 / serwera Windows Server 2008 R2 z dodatkiem SP1 do migracji.
   
   ![Dodawanie serwera fizycznego](media/migrate-tutorial-windows-server-2008/Add-physical-server.png)

   ![Włączanie replikacji](media/migrate-tutorial-windows-server-2008/Enable-replication.png)

## <a name="run-a-test-migration"></a>Uruchamianie migracji testowej

Można przeprowadzić testu trybu failover replikacji serwerów po ukończeniu replikacji początkowej i przechodzi w stan serwera **chronione**.

Uruchom [testowanie trybu failover](tutorial-dr-drill-azure.md), aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.

   ![Testowanie trybu failover](media/migrate-tutorial-windows-server-2008/testfailover.png)


## <a name="migrate-to-azure"></a>Migracja na platformę Azure

Uruchom tryb failover dla maszyn, które chcesz migrować.

1. W obszarze **Ustawienia** > **Zreplikowane elementy** kliknij maszynę wirtualną > **Tryb failover**.
2. W obszarze **Tryb failover** wybierz **Punkt odzyskiwania**, którego chcesz użyć do przełączenia do trybu failover. Wybierz najnowszy punkt odzyskiwania.
3. Wybierz pozycję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover**. Usługa Site Recovery spróbuje należy wyłączyć serwer przed wyzwoleniem trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Na stronie **Zadania** można śledzić postęp trybu failover.
4. Sprawdź, czy maszyna wirtualna Azure jest wyświetlana na platformie Azure zgodnie z oczekiwaniami.
5. W obszarze **Replikowane elementy** kliknij prawym przyciskiem myszy maszynę wirtualną > **Zakończ migrację**. Spowoduje to zakończenie procesu migracji, zatrzymanie replikacji maszyny wirtualnej oraz zatrzymanie naliczania opłat za usługę Site Recovery dla maszyny wirtualnej.

   ![Kończenie migracji](media/migrate-tutorial-windows-server-2008/complete-migration.png)


> [!WARNING]
> **Nie anuluj trybu failover po rozpoczęciu przełączania**: replikacja maszyny wirtualnej zostanie zatrzymana przed uruchomieniem trybu failover. Jeśli anulujesz tryb failover po rozpoczęciu przełączania, zostanie ono zatrzymane, ale maszyna wirtualna nie zostanie ponownie zreplikowana.
