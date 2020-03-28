---
title: Konfigurowanie odzyskiwania po awarii funkcji Hyper-V przy użyciu usługi Azure Site Recovery
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii lokalnych maszyn wirtualnych funkcji Hyper V (bez programu VMM) na platformie Azure przy użyciu usługi Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 741d4718b5e6140f4ddd2bb22e1a2ec830763176
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239847"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Konfigurowanie odzyskiwania po awarii lokalnych maszyn wirtualnych funkcji Hyper-V na platformie Azure

Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do strategii odzyskiwania po awarii, zarządzając replikacją, trybem failover i powrotem awaryjnym maszyn lokalnych i maszyn wirtualnych platformy Azure (VMs) i aranżacji ich.

Jest to trzeci samouczek z tej serii. Pokazuje, jak skonfigurować odzyskiwanie po awarii lokalnych maszyn wirtualnych z programem Hyper V na platformie Azure. Ten samouczek dotyczy maszyn wirtualnych funkcji Hyper-V, które nie są zarządzane przez menedżera maszyn wirtualnych programu Microsoft System Center (VMM).

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wybieranie źródła i celu replikacji.
> * Skonfiguruj źródłowe środowisko replikacji, w tym lokalne składniki odzyskiwania lokacji i docelowe środowisko replikacji.
> * Tworzenie zasad replikacji.
> * Włączanie replikacji maszyny wirtualnej.

> [!NOTE]
> Samouczki pokazują najprostszą ścieżkę wdrażania dla scenariusza. Jeśli to możliwe używają opcji domyślnych i nie przedstawiają wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z artykułami w sekcji **Instrukcje obsługi** [dokumentacji odzyskiwania witryny.](https://docs.microsoft.com/azure/site-recovery)



## <a name="before-you-begin"></a>Przed rozpoczęciem

Jest to trzeci samouczek z tej serii. Zakłada się, że zadania zostały już wykonane w poprzednich samouczkach:

1. [Przygotowywanie platformy Azure](tutorial-prepare-azure.md)
2. [Przygotowywanie lokalnej funkcji Hyper-V](tutorial-prepare-on-premises-hyper-v.md)

## <a name="select-a-replication-goal"></a>Wybieranie celu replikacji

1. W witrynie Azure portal przejdź do **magazynów usług odzyskiwania** i wybierz magazyn. Przygotowaliśmy vault **ContosoVMVault** w poprzednim samouczku.
2. W **obszarze Wprowadzenie**wybierz pozycję **Odzysk witryny**, a następnie wybierz pozycję **Przygotuj infrastrukturę**.
3. W **celu** > Ochrony**Gdzie znajdują się twoje maszyny?**, wybierz **opcję Lokalnie**.
4. W **obszarze Gdzie chcesz replikować swoje maszyny?** **To Azure**
5. W **czy twoje maszyny są zwirtualizowane?**, wybierz **Tak, z Hyper-V**.
6. Czy **do zarządzania hostami funkcji Hyper-V używasz**programu System Center V? **No**
7. Kliknij przycisk **OK**.

    ![Cel replikacji](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Potwierdzanie planowania wdrożenia

1. W **przypadku planowania wdrażania,** jeśli planujesz duże wdrożenie, pobierz planista wdrażania dla funkcji Hyper-V z łącza na stronie. [Dowiedz się więcej](hyper-v-deployment-planner-overview.md) o planowaniu wdrażania funkcji Hyper-V.
2. W tym samouczku nie potrzebujemy planisty wdrażania. W **obszarze Czy ukończono planowanie wdrażania?**, wybierz **pozycję Zrobię to później**, a następnie wybierz przycisk **OK**.

    ![Planowanie wdrożenia](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

Aby skonfigurować środowisko źródłowe, należy utworzyć witrynę funkcji Hyper-V i dodać do tej lokacji hosty funkcji Hyper-V zawierające maszyny wirtualne, które mają zostać zreplikowane. Następnie należy pobrać i zainstalować dostawcę odzyskiwania witryny azure i agenta usług Azure Recovery Services na każdym hoście i zarejestrować witrynę funkcji Hyper-V w magazynie.

1. W obszarze **Przygotowanie infrastruktury**wybierz pozycję **Źródło**.
2. W **obszarze Przygotuj źródło**wybierz + **Witrynę funkcji Hyper-V**.
3. W **obszarze Tworzenie witryny funkcji Hyper-V**określ nazwę witryny. Używamy **ContosoHyperVSite**.

    ![Lokacja funkcji Hyper-V](./media/hyper-v-azure-tutorial/hyperv-site.png)

4. Po utworzeniu witryny w **obszarze Przygotuj źródło** > **Krok 1: Wybierz witrynę funkcji Hyper-V**wybierz utworzoną witrynę.
5. Wybierz **+ Serwer Hyper-V**.

    ![Serwer funkcji Hyper-V](./media/hyper-v-azure-tutorial/hyperv-server.png)

6. Pobierz instalator dla dostawcy odzyskiwania witryny platformy Microsoft Azure.
7. Pobierz klucz rejestracji magazynu. Ten klucz jest potrzebny do zainstalowania dostawcy. Klucz jest ważny przez pięć dni po jego wygenerowaniu.

    ![Dostawca pobierania i klucz rejestracyjny](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Instalowanie dostawcy

Zainstaluj pobrany plik instalacyjny (AzureSiteRecoveryProvider.exe) na każdym hoście funkcji Hyper-V, który chcesz dodać do witryny funkcji Hyper-V. Instalator instaluje agenta dostawcy odzyskiwania witryny platformy Azure i usług odzyskiwania na każdym hoście funkcji Hyper-V.

1. Uruchom plik instalacyjny.
2. W Kreatorze instalacji dostawcy usługi Azure Site Recovery w obszarze **Microsoft Update** wyraź zgodę na używanie usługi Microsoft Update do sprawdzania aktualizacji dostawcy.
3. W **obszarze Instalacja**zaakceptuj domyślną lokalizację instalacji dostawcy i agenta, a następnie wybierz pozycję **Zainstaluj**.
4. Po instalacji w Kreatorze rejestracji odzyskiwania witryny platformy Microsoft Azure > **ustawieniach przechowalni**wybierz pozycję **Przeglądaj**, a następnie w **polu Plik klucza**wybierz pobrany plik klucza przechowalni.
5. Określ subskrypcję usługi Azure Site Recovery, nazwę magazynu (**ContosoVMVault**) oraz lokację funkcji Hyper-V (**ContosoHyperVSite**), do której należy serwer funkcji Hyper-V.
6. W obszarze **Ustawienia serwera proxy** wybierz pozycję **Połącz bezpośrednio z usługą Azure Site Recovery bez serwera proxy**.
7. W **obszarze Rejestracja**, po zarejestrowaniu serwera w przechowalni, wybierz pozycję **Zakończ**.

Metadane z serwera funkcji Hyper-V są pobierane przez usługę Azure Site Recovery, a serwer jest wyświetlany w > **hostach Hyper-V** **infrastruktury odzyskiwania lokacji**. Ten proces może potrwać do 30 minut.

#### <a name="install-the-provider-on-a-hyper-v-core-server"></a>Instalowanie dostawcy na serwerze podstawowym funkcji Hyper-V

Jeśli korzystasz z serwera rdzenia funkcji Hyper-V, pobierz plik instalacyjny i wykonaj następujące czynności:

1. Wyodrębnij pliki z usługi AzureSiteRecoveryProvider.exe do katalogu lokalnego, uruchamiając to polecenie:

    `AzureSiteRecoveryProvider.exe /x:. /q`
 
2. Uruchom polecenie `.\setupdr.exe /i`. Wyniki są rejestrowane w pliku %Programdata%\ASRLogs\DRASetupWizard.log.

3. Zarejestruj serwer, uruchamiając to polecenie:

    ```
    cd  "C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
    ```

## <a name="set-up-the-target-environment"></a>Konfigurowanie środowiska docelowego

Wybierz i sprawdź zasoby docelowe:

1. Wybierz **pozycję Przygotuj infrastrukturę** > **docelową**.
2. Wybierz subskrypcję i grupę zasobów **ContosoRG,** w której będą tworzone maszyny wirtualne platformy Azure po przemienniu w stan failover.
3. Wybierz model wdrażania usługi **Resource Manager**.

Usługa Site Recovery sprawdza, czy masz co najmniej jedno zgodne konto magazynu Azure i co najmniej jedną sieć platformy Azure.

## <a name="set-up-a-replication-policy"></a>Konfigurowanie zasad replikacji

1. Wybierz **pozycję Przygotuj** > **ustawienia** > replikacji infrastruktury **+Utwórz i skojarzyć**.
2. W **obszarze Tworzenie i kojarzenie zasad**określ nazwę zasad. Używamy **ContosoReplicationPolicy**.
3. W tym samouczku pozostawimy ustawienia domyślne:
    - **Częstotliwość kopiowania** wskazuje, jak często dane delta (po replikacji początkowej) będą replikowane. Domyślna częstotliwość jest co pięć minut.
    - **Zatrzymanie punktu odzyskiwania** wskazuje, że punkty odzyskiwania zostaną zachowane przez dwie godziny.
    - Ustawienie **Częstotliwość migawek na poziomie aplikacji** wskazuje, że punkty odzyskiwana zawierające migawki na poziomie aplikacji będą tworzone co godzinę.
    - **Początkowa godzina rozpoczęcia replikacji** wskazuje, że replikacja początkowa rozpocznie się natychmiast.
4. Po utworzeniu zasady wybierz przycisk **OK**. Podczas tworzenia nowej zasady jest ona automatycznie skojarzona z określoną witryną funkcji Hyper-V. W naszym poradniku, to **ContosoHyperVSite**.

    ![Zasady replikacji](./media/hyper-v-azure-tutorial/replication-policy.png)

## <a name="enable-replication"></a>Włączanie replikacji

1. W **obszarze Replikuj aplikację**wybierz pozycję **Źródło**.
2. W obszarze **Źródło** wybierz lokację **ContosoHyperVSite**. Następnie wybierz przycisk **OK**.
3. W **obszarze Docelowy**sprawdź obiekt docelowy (Azure), subskrypcję magazynu i model wdrażania **Usługi Resource Manager.**
4. Jeśli używasz ustawień samouczka, wybierz konto magazynu **contosovmsacct1910171607** utworzone w poprzednim samouczku dla replikowanych danych. Wybierz również sieć **ContosoASRnet,** w której maszyny wirtualne platformy Azure będą znajdować się po przemiń awaryjnych.
5. W **obszarze Maszyny wirtualne** > **Wybierz**wybierz maszynę wirtualną, którą chcesz replikować. Następnie wybierz przycisk **OK**.

   Możesz śledzić postępy akcji **Włącz ochronę** w obszarze **Zadania** > **Zadania usługi Site Recovery**. Po zakończeniu zadania **Finalize Protection** replikacja początkowa została zakończona, a maszyna wirtualna jest gotowa do pracy awaryjnej.

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Uruchamianie próbnego odzyskiwania po awarii](tutorial-dr-drill-azure.md)
