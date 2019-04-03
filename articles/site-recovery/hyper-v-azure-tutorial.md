---
title: Konfigurowanie odzyskiwania po awarii lokalnych maszyn wirtualnych VMware funkcji Hyper V (bez użycia programu VMM) na platformie Azure przy użyciu usługi Azure Site Recovery | Microsoft Docs
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii lokalnych maszyn wirtualnych funkcji Hyper-V (bez użycia programu VMM) na platformie Azure przy użyciu usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 2201a8017f82517f287cc0b73346a90eaa2408a4
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58877724"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Konfigurowanie odzyskiwania po awarii lokalnych maszyn wirtualnych funkcji Hyper-V na platformie Azure

Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do realizacji strategii odzyskiwania po awarii przez zarządzanie replikacją, przełączaniem do trybu failover i powrotem po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure oraz koordynowanie tych procesów.

Ten samouczek przedstawia sposób konfigurowania odzyskiwania po awarii lokalnych maszyn wirtualnych funkcji Hyper V na platformie Azure. Ten samouczek jest istotny dla maszyn wirtualnych funkcji Hyper V, które nie są zarządzane przy użyciu programu System Center Virtual Machine Manager (VMM). Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wybieranie źródła i celu replikacji.
> * Konfigurowanie środowiska źródłowego replikacji wraz z lokalnymi składnikami usługi Site Recovery oraz środowiska docelowego replikacji.
> * Tworzenie zasad replikacji.
> * Włączanie replikacji maszyny wirtualnej.

Jest to trzeci samouczek z tej serii. Założono w nim, że zostały już wykonane zadania z poprzednich samouczków:

1. [Przygotowywanie platformy Azure](tutorial-prepare-azure.md)
2. [Przygotowywanie lokalnej funkcji Hyper-V](tutorial-prepare-on-premises-hyper-v.md)

Przed rozpoczęciem warto [zapoznać się z architekturą](concepts-hyper-v-to-azure-architecture.md) tego scenariusza odzyskiwania po awarii.

## <a name="select-a-replication-goal"></a>Wybieranie celu replikacji


1. W obszarze **Wszystkie usługi** > **Magazyny usługi Recovery Services** wybierz magazyn przygotowany w poprzednim samouczku: **ContosoVMVault**.
2. W obszarze **Wprowadzenie** kliknij pozycję **Site Recovery**. Następnie kliknij pozycję **Przygotowywanie infrastruktury**
3. W obszarze **Cel ochrony** > **Gdzie znajdują się maszyny** wybierz pozycję **Lokalne**.
4. W obszarze **Gdzie chcesz zreplikować maszyny** wybierz pozycję **Na platformę Azure**.
5. W oknie **Czy używasz usługi System Center VMM do zarządzania hostami funkcji Hyper-V?** wybierz opcję **Nie**. Następnie kliknij przycisk **OK**.

    ![Cel replikacji](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Potwierdzanie planowania wdrożenia

Jeśli planujesz duże wdrożenie, pamiętaj o [planowaniu wdrożenia dla replikacji funkcji Hyper-V](hyper-v-deployment-planner-overview.md). Na potrzeby tego samouczka w oknie **Czy ukończono planowanie wdrożenia?**, wybierz na liście rozwijanej pozycję **Zrobię to później**.

![Planowanie wdrożenia](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

Aby skonfigurować środowisko źródłowe, utwórz lokację funkcji Hyper-V i dodaj do niej hosty funkcji Hyper-V. Następnie pobierz i zainstaluj dostawcę usługi Azure Site Recovery oraz agenta usługi Azure Recovery Services na każdym hoście i zarejestruj lokację funkcji Hyper-V w magazynie. 

1. W obszarze **Przygotowanie infrastruktury**, kliknij przycisk **Źródło**.
2. Kliknij przycisk **+ Lokacja funkcji Hyper-V** i wprowadź nazwę lokacji utworzonej w poprzednim samouczku: **ContosoHyperVSite**.

    ![Lokacja funkcji Hyper-V](./media/hyper-v-azure-tutorial/hyperv-site.png)

3. Po utworzeniu lokacji kliknij pozycję **+Hyper-V Server**.

    ![Serwer funkcji Hyper-V](./media/hyper-v-azure-tutorial/hyperv-server.png)

4. Pobierz plik instalatora dostawcy.
6. Pobierz klucz rejestracji magazynu. Będzie on potrzebny podczas instalacji dostawcy. Klucz jest ważny przez pięć dni po jego wygenerowaniu.

    ![Pobieranie dostawcy](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Instalowanie dostawcy

Uruchom plik instalatora dostawcy (AzureSiteRecoveryProvider.exe) na każdym hoście funkcji Hyper-V dodanym do lokacji **ContosoHyperVSite**. Instalator zainstaluje dostawcę usługi Azure Site Recovery i agenta usług Recovery Services na każdym hoście usługi Hyper-V.

1. W Kreatorze instalacji dostawcy usługi Azure Site Recovery w obszarze **Microsoft Update** wyraź zgodę na używanie usługi Microsoft Update do sprawdzania aktualizacji dostawcy.
2. W obszarze **Instalacja** zaakceptuj domyślną lokalizację instalacji dostawcy i agenta, a następnie kliknij przycisk **Zainstaluj**.
3. Po zakończeniu instalacji w Kreatorze rejestracji usługi Microsoft Azure Site Recovery w obszarze **Ustawienia magazynu** kliknij przycisk **Przeglądaj**, a następnie w oknie **Plik klucza** wybierz pobrany plik klucza magazynu. 
4. Określ subskrypcję usługi Azure Site Recovery, nazwę magazynu (**ContosoVMVault**) oraz lokację funkcji Hyper-V (**ContosoHyperVSite**), do której należy serwer funkcji Hyper-V.
5. W obszarze **Ustawienia serwera proxy** wybierz pozycję **Połącz bezpośrednio z usługą Azure Site Recovery bez serwera proxy**.
6. W obszarze**Rejestracja** po zarejestrowaniu serwera w magazynie kliknij przycisk **Zakończ**.

Metadane z serwera funkcji Hyper-V zostaną pobrane przez usługę Azure Site Recovery, a serwer zostanie wyświetlony w obszarze **Infrastruktura usługi Site Recovery** > **Hosty funkcji Hyper-V**. Ten proces może potrwać do 30 minut.        

W przypadku, gdy używasz serwera rdzenia funkcji Hyper-V, wykonaj poniższe czynności po pobraniu poświadczeń dostawcy i magazynu, jak wspomniano [tutaj](#set-up-the-source-environment)

1. Wyodrębnij pliki z pliku AzureSiteRecoveryProvider.exe, uruchamiając

    `AzureSiteRecoveryProvider.exe /x:. /q`
 
    Spowoduje to wyodrębnienie plików do katalogu lokalnego.
 
2.  Uruchom polecenie `.\setupdr.exe /i`

    Wyniki zostaną zarejestrowane w pliku %Programdata%\ASRLogs\DRASetupWizard.log

3.  Zarejestruj się na serwerze przy użyciu polecenia:

`cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"`
 

## <a name="set-up-the-target-environment"></a>Konfigurowanie środowiska docelowego

Wybierz i zweryfikuj zasoby docelowe. 

1. Kliknij pozycję **Przygotowanie infrastruktury** > **Cel**.
2. Wybierz subskrypcję i grupę zasobów **ContosoRG**, w której zostaną utworzone maszyny wirtualne platformy Azure po włączeniu trybu failover.
3. Wybierz model wdrażania usługi **Resource Manager**.

Usługa Site Recovery sprawdza, czy masz co najmniej jedno zgodne konto magazynu Azure i co najmniej jedną sieć platformy Azure.


## <a name="set-up-a-replication-policy"></a>Konfigurowanie zasad replikacji

> [!NOTE]
> W przypadku zasad replikacji funkcji Hyper-V do platformy Azure opcja częstotliwości kopiowania co 15 minut jest wycofywana na rzecz ustawień częstotliwości wynoszących 5 minut i 30 sekund. Zasady replikacji korzystające z częstotliwości kopiowania 15 minut zostaną automatycznie zaktualizowane tak, aby używać ustawienia częstotliwości kopiowania wynoszącego 5 minut. Opcje częstotliwości kopiowania co 5 minut i 30 sekund zapewniają lepszą wydajność replikacji i udoskonalone cele punktu odzyskiwania w porównaniu z częstotliwością kopiowania wynoszącą 15 minut przy minimalnym wpływie na wolumin użycia przepustowości i transferu danych.

1. Kliknij kolejno pozycje **Przygotowanie infrastruktury** > **Ustawienia replikacji** > **+ Utwórz i skojarz**.
2. W obszarze **Tworzenie i kojarzenie zasad** podaj nazwę zasad: **ContosoReplicationPolicy**.
3. Pozostaw ustawienia domyślne i kliknij przycisk **OK**.
    - Ustawienie **Częstotliwość kopiowania** wskazuje, że dane różnicowe (po replikacji początkowej) będą replikowane co pięć minut.
    - Ustawienie **Przechowywanie punktów odzyskiwania** wskazuje, że okna przechowywania dla każdego punktu odzyskiwania będą wynosiły dwie godziny.
    - Ustawienie **Częstotliwość migawek na poziomie aplikacji** wskazuje, że punkty odzyskiwana zawierające migawki na poziomie aplikacji będą tworzone co godzinę.
    - Ustawienie **Czas rozpoczęcia replikacji początkowej** wskazuje, że replikacja początkowa rozpocznie się natychmiast.
4. Po utworzeniu zasad kliknij przycisk **OK**. Po utworzeniu nowe zasady zostaną automatycznie skojarzone z określoną lokacją funkcji Hyper-V (**ContosoHyperVSite**)

    ![Zasady replikacji](./media/hyper-v-azure-tutorial/replication-policy.png)


## <a name="enable-replication"></a>Włączanie replikacji


1. W obszarze **Replikowanie aplikacji** kliknij przycisk **Źródło**. 
2. W obszarze **Źródło** wybierz lokację **ContosoHyperVSite**. Następnie kliknij przycisk **OK**.
3. W obszarze **Cel** sprawdź, czy platforma Azure została wybrana jako środowisko docelowe oraz czy wskazano właściwą subskrypcję magazynu i model wdrożenia **Resource Manager**.
4. Wybierz konto magazynu **contosovmsacct1910171607** utworzone w poprzednim samouczku do replikowania danych, a następnie wybierz sieć **ContosoASRnet**, w której zostaną umieszczone maszyny wirtualne platformy Azure po przejściu do trybu failover.
5. W obszarze **Maszyny wirtualne** > **Wybierz** wybierz maszynę wirtualną, którą chcesz replikować. Następnie kliknij przycisk **OK**.

   Możesz śledzić postępy akcji **Włącz ochronę** w obszarze **Zadania** > **Zadania usługi Site Recovery**. Po zakończeniu zadania **Finalizuj ochronę** replikacja początkowa zostanie zakończona, a maszyna wirtualna będzie gotowa do pracy w trybie failover.

## <a name="next-steps"></a>Kolejne kroki
[Uruchamianie próbnego odzyskiwania](tutorial-dr-drill-azure.md)
