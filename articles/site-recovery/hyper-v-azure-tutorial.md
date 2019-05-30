---
title: Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych funkcji Hyper-V w środowisku lokalnym (bez programu VMM) do platformy Azure z usługą Site Recovery | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii lokalnych maszyn wirtualnych funkcji Hyper-V (bez programu VMM) na platformie Azure przy użyciu usługi Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 9d45e0f0c759685f9d35285ee7718585d5961333
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399407"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Konfigurowanie odzyskiwania po awarii lokalnych maszyn wirtualnych funkcji Hyper-V na platformie Azure

[Usługi Azure Site Recovery](site-recovery-overview.md) przyczynia się do strategii odzyskiwania po awarii przez organizowanie replikacji, trybu failover i powrotu po awarii maszyn lokalnych i maszyn wirtualnych (VM) i zarządzanie nimi.

Jest to trzeci samouczek z tej serii. Prezentuje sposób konfigurowania odzyskiwania po awarii lokalnych maszyn wirtualnych z funkcją Hyper-V do platformy Azure. Ten samouczek dotyczy maszyn wirtualnych funkcji Hyper-V, które nie są zarządzane przez Microsoft System Center Virtual Machine Manager (VMM).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wybieranie źródła i celu replikacji.
> * Konfigurowanie środowiska źródłowego replikacji, w tym lokalnych składników usługi Site Recovery oraz środowiska docelowego replikacji.
> * Tworzenie zasad replikacji.
> * Włączanie replikacji maszyny wirtualnej.

> [!NOTE]
> W samouczkach pokazano to najprostsza ścieżka wdrażania scenariusza. Jeśli to możliwe, używają opcji domyślnych i nie przedstawiają wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z artykułami w **przewodniki z instrukcjami** części [dokumentacja usługi Site Recovery](https://docs.microsoft.com/azure/site-recovery).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Jest to trzeci samouczek z tej serii. Przyjęto założenie, że zostały już wykonane zadania z poprzednich samouczków:

1. [Przygotowywanie platformy Azure](tutorial-prepare-azure.md)
2. [Przygotowywanie lokalnej funkcji Hyper-V](tutorial-prepare-on-premises-hyper-v.md)

## <a name="select-a-replication-goal"></a>Wybieranie celu replikacji

1. W witrynie Azure portal przejdź do **Magazyny usługi Recovery Services** i wybierz magazyn. Możemy przygotować magazyn **ContosoVMVault** w poprzednim samouczku.
2. W **wprowadzenie**, wybierz opcję **Site Recovery**, a następnie wybierz pozycję **Przygotuj infrastrukturę**.
3. W **cel ochrony** > **których znajdują się maszyny?** , wybierz opcję **On-premises**.
4. W **gdzie chcesz zreplikować maszyny?** , wybierz opcję **na platformę Azure**.
5. W **są maszynach zwirtualizowane?** , wybierz opcję **tak, przy użyciu funkcji Hyper-V**.
6. W **używasz programu System Center VMM do zarządzania hostów funkcji Hyper-V?** , wybierz opcję **nie**.
7. Kliknij przycisk **OK**.

    ![Cel replikacji](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Potwierdzanie planowania wdrożenia

1. W **planowania wdrożenia**, jeśli planowane jest dużych wdrożeniach, Pobierz planista wdrażania funkcji Hyper-v przy użyciu linku na stronie. [Dowiedz się więcej](hyper-v-deployment-planner-overview.md) o planowaniu wdrożenia funkcji Hyper-V.
2. W tym samouczku nie potrzebujemy planista wdrażania. W **czy ukończono Planowanie wdrożenia?** , wybierz opcję **zrobię to później**, a następnie wybierz pozycję **OK**.

    ![Planowanie wdrożenia](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

Aby skonfigurować środowisko źródłowe, możesz utworzyć lokację funkcji Hyper-V i dodać do tej witryny i hosty funkcji Hyper-V zawierające maszyny wirtualne, które mają być replikowane. Następnie Pobierz i zainstaluj dostawcę usługi Azure Site Recovery oraz agenta usług odzyskiwania Azure na każdym hoście i zarejestrować lokację funkcji Hyper-V w magazynie.

1. W obszarze **Przygotuj infrastrukturę**, wybierz opcję **źródła**.
2. W **przygotowywanie źródła**, wybierz opcję **+ lokacja funkcji Hyper-V**.
3. W **lokacji funkcji Hyper-V Utwórz**, określ nazwę lokacji. Firma Microsoft korzysta z **ContosoHyperVSite**.

    ![Lokacja funkcji Hyper-V](./media/hyper-v-azure-tutorial/hyperv-site.png)

4. Po utworzeniu witryny w **przygotowywanie źródła** > **krok 1: Wybierz witrynę funkcji Hyper-V**, wybierz lokację, został utworzony.
5. Wybierz **+ serwer funkcji Hyper-V**.

    ![Serwer funkcji Hyper-V](./media/hyper-v-azure-tutorial/hyperv-server.png)

6. Pobierz Instalator dostawcy Microsoft Azure Site Recovery.
7. Pobierz klucz rejestracji magazynu. Będzie on potrzebny do zainstalowania dostawcy. Klucz jest ważny przez pięć dni po jego wygenerowaniu.

    ![Pobieranie dostawcy i klucz rejestracji](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Instalowanie dostawcy

Zainstaluj pobrany plik Instalatora (AzureSiteRecoveryProvider.exe) na każdym hoście funkcji Hyper-V, który chcesz dodać do lokacji funkcji Hyper-V. Instalator instaluje się dostawcę usługi Azure Site Recovery i agenta usług Recovery Services na każdym hoście funkcji Hyper-V.

1. Uruchom plik Instalatora.
2. W Kreatorze instalacji dostawcy usługi Azure Site Recovery w obszarze **Microsoft Update** wyraź zgodę na używanie usługi Microsoft Update do sprawdzania aktualizacji dostawcy.
3. W **instalacji**Zaakceptuj domyślną lokalizację instalacji dostawcy i agenta, a wybierz **zainstalować**.
4. Po zakończeniu instalacji w Kreatorze rejestracji usługi Microsoft Azure lokacji odzyskiwania > **ustawienia magazynu**, wybierz opcję **Przeglądaj**, a następnie w **plik klucza**, wybierz opcję pliku klucza magazynu, który pobrane.
5. Określ subskrypcję usługi Azure Site Recovery, nazwę magazynu (**ContosoVMVault**) oraz lokację funkcji Hyper-V (**ContosoHyperVSite**), do której należy serwer funkcji Hyper-V.
6. W obszarze **Ustawienia serwera proxy** wybierz pozycję **Połącz bezpośrednio z usługą Azure Site Recovery bez serwera proxy**.
7. W **rejestracji**po serwer jest zarejestrowany w magazynie, wybierz opcję **Zakończ**.

Metadane z serwera funkcji Hyper-V zostaną pobrane przez usługę Azure Site Recovery, a serwer zostanie wyświetlony w obszarze **Infrastruktura usługi Site Recovery** > **Hosty funkcji Hyper-V**. Ten proces może potrwać do 30 minut.

#### <a name="install-the-provider-on-a-hyper-v-core-server"></a>Zainstaluj dostawcę na funkcji Hyper-V server core

Jeśli korzystasz z instalacją server core funkcji Hyper-V, Pobierz plik instalacyjny i wykonaj następujące czynności:

1. Wyodrębnij pliki z AzureSiteRecoveryProvider.exe do katalogu lokalnego, uruchamiając następujące polecenie:

    `AzureSiteRecoveryProvider.exe /x:. /q`
 
2. Uruchom polecenie `.\setupdr.exe /i`. Wyniki są rejestrowane % Programdata%\ASRLogs\DRASetupWizard.log.

3. Zarejestruj serwer, uruchamiając następujące polecenie:

    ```
    cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
    ```

## <a name="set-up-the-target-environment"></a>Konfigurowanie środowiska docelowego

Wybierz i Zweryfikuj zasoby docelowe:

1. Wybierz pozycje **Przygotowanie infrastruktury** > **Cel**.
2. Wybierz subskrypcję i grupę zasobów **ContosoRG** , w której maszyny wirtualne platformy Azure zostanie utworzony po włączeniu trybu failover.
3. Wybierz model wdrażania usługi **Resource Manager**.

Usługa Site Recovery sprawdza, czy masz co najmniej jedno zgodne konto magazynu Azure i co najmniej jedną sieć platformy Azure.

## <a name="set-up-a-replication-policy"></a>Konfigurowanie zasad replikacji

1. Wybierz **przygotowanie infrastruktury** > **ustawień replikacji** >  **+ Utwórz i skojarz**.
2. W obszarze **Utwórz i skojarz zasady** określ nazwę zasad. Firma Microsoft korzysta z **ContosoReplicationPolicy**.
3. W tym samouczku pozostawimy wartości domyślnych:
    - **Częstotliwość kopiowania** wskazuje, jak często dane różnicowe (po replikacji początkowej) będą replikowane. Domyślna częstotliwość to co pięć minut.
    - **Czas przechowywania punktu odzyskiwania** wskazuje, że punkty odzyskiwania będą przechowywane przez dwie godziny.
    - Ustawienie **Częstotliwość migawek na poziomie aplikacji** wskazuje, że punkty odzyskiwana zawierające migawki na poziomie aplikacji będą tworzone co godzinę.
    - **Czas rozpoczęcia replikacji początkowej** wskazuje, że Replikacja początkowa rozpocznie się natychmiast.
4. Po utworzeniu zasad wybierz **OK**. Podczas tworzenia nowych zasad automatycznie skojarzone z określoną lokacją funkcji Hyper-V. W naszym samouczku to **ContosoHyperVSite**.

    ![Zasady replikacji](./media/hyper-v-azure-tutorial/replication-policy.png)

## <a name="enable-replication"></a>Włączanie replikacji

1. W **Replikowanie aplikacji**, wybierz opcję **źródła**.
2. W obszarze **Źródło** wybierz lokację **ContosoHyperVSite**. Następnie wybierz przycisk **OK**.
3. W **docelowej**, sprawdź element docelowy (Azure), subskrypcję magazynu i **usługi Resource Manager** modelu wdrażania.
4. Jeśli używasz samouczek ustawienia, wybierz **contosovmsacct1910171607** konta magazynu utworzonego w poprzednim samouczku do replikowania danych. Również wybrać **ContosoASRnet** sieci, w której maszyny wirtualne platformy Azure zostaną umieszczone po włączeniu trybu failover.
5. W **maszyn wirtualnych** > **wybierz**, wybierz maszynę Wirtualną, którą chcesz replikować. Następnie wybierz przycisk **OK**.

   Możesz śledzić postępy akcji **Włącz ochronę** w obszarze **Zadania** > **Zadania usługi Site Recovery**. Po **Finalizuj ochronę** zakończenia zadania, Replikacja początkowa zostanie zakończona i maszyna wirtualna jest gotowa do pracy awaryjnej.

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Uruchamianie próbnego odzyskiwania po awarii](tutorial-dr-drill-azure.md)
