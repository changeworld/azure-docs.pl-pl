---
title: Konfigurowanie odzyskiwania po awarii lokalnych maszyn wirtualnych VMware funkcji Hyper V (bez użycia programu VMM) na platformie Azure przy użyciu usługi Azure Site Recovery | Microsoft Docs
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii lokalnych maszyn wirtualnych funkcji Hyper-V (bez użycia programu VMM) na platformie Azure przy użyciu usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: da643a4d7a1dc74385b3854c1952af5ba93bd241
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59358101"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Konfigurowanie odzyskiwania po awarii lokalnych maszyn wirtualnych funkcji Hyper-V na platformie Azure

c

> [!div class="checklist"]
> * Wybieranie źródła i celu replikacji.
> * Konfigurowanie środowiska źródłowego replikacji wraz z lokalnymi składnikami usługi Site Recovery oraz środowiska docelowego replikacji.
> * Tworzenie zasad replikacji.
> * Włączanie replikacji maszyny wirtualnej.

> [!NOTE]
> W samouczkach pokazano to najprostsza ścieżka wdrażania scenariusza. Jeśli to możliwe, używają opcji domyślnych i nie przedstawiają wszystkich możliwych ustawień i ścieżek. Szczegółowe informacje na ten temat można znaleźć w artykule w sekcji jak tabeli odzyskiwania lokacji zawartości.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Jest to trzeci samouczek z tej serii. Założono w nim, że zostały już wykonane zadania z poprzednich samouczków:

1. [Przygotowywanie platformy Azure](tutorial-prepare-azure.md)
2. [Przygotowywanie lokalnej funkcji Hyper-V](tutorial-prepare-on-premises-hyper-v.md)



## <a name="select-a-replication-goal"></a>Wybieranie celu replikacji

1. W obszarze **Magazyny usługi Recovery Services** wybierz magazyn. Możemy przygotować magazyn **ContosoVMVault** w poprzednim samouczku.
2. W obszarze **Wprowadzenie** kliknij pozycję **Site Recovery**. Następnie kliknij pozycję **Przygotowywanie infrastruktury**
3. W **cel ochrony** > **których znajdują się maszyny?** wybierz **On-premises**.
4. W **gdzie chcesz zreplikować maszyny?**, wybierz opcję **na platformę Azure**.
5. W **są maszynach zwirtualizowane?** wybierz **tak, przy użyciu funkcji Hyper-V**.
6. W oknie **Czy używasz usługi System Center VMM do zarządzania hostami funkcji Hyper-V?** wybierz opcję **Nie**. Następnie kliknij przycisk **OK**.

    ![Cel replikacji](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Potwierdzanie planowania wdrożenia

1. W **planowania wdrożenia**, jeśli planowane jest dużych wdrożeniach, Pobierz planista wdrażania funkcji Hyper-v przy użyciu linku na stronie. [Dowiedz się więcej](hyper-v-deployment-planner-overview.md) o planowaniu wdrożenia funkcji Hyper-V.
2. Na potrzeby tego samouczka nie potrzebujemy planista wdrażania. W **czy ukończono Planowanie wdrożenia?** wybierz **zrobię to później**. Następnie kliknij przycisk **OK**.

    ![Planowanie wdrożenia](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

Aby skonfigurować środowisko źródłowe, możesz utworzyć lokację funkcji Hyper-V i dodać hosty funkcji Hyper-V zawierające maszyny wirtualne mają być replikowane do lokacji. Następnie pobierz i zainstaluj dostawcę usługi Azure Site Recovery oraz agenta usługi Azure Recovery Services na każdym hoście i zarejestruj lokację funkcji Hyper-V w magazynie. 

1. W obszarze **Przygotuj infrastrukturę**, kliknij przycisk **źródła**.
2. W **przygotowywanie źródła**, kliknij przycisk **+ lokacja funkcji Hyper-V**.
3. W **lokacji funkcji Hyper-V Utwórz**, a następnie określ nazwę lokacji. Firma Microsoft korzysta z **ContosoHyperVSite**.

    ![Lokacja funkcji Hyper-V](./media/hyper-v-azure-tutorial/hyperv-site.png)

3. Po utworzeniu witryny w **przygotowywanie źródła** > **krok 1: Wybierz witrynę funkcji Hyper-V**, wybierz lokację, został utworzony.
4. Kliknij przycisk **+ Serwer funkcji Hyper-V**.

    ![Serwer funkcji Hyper-V](./media/hyper-v-azure-tutorial/hyperv-server.png)

4. Pobierz Instalator dostawcy Microsoft Azure Site Recovery.
6. Pobierz klucz rejestracji magazynu. Będzie on potrzebny do zainstalowania dostawcy. Klucz jest ważny przez pięć dni po jego wygenerowaniu.

    ![Pobieranie dostawcy](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Instalowanie dostawcy

Zainstaluj pobrany plik Instalatora (AzureSiteRecoveryProvider.exe) na każdym hoście funkcji Hyper-V, który chcesz dodać do lokacji funkcji Hyper-V. Instalator zainstaluje dostawcę usługi Azure Site Recovery i agenta usług Recovery Services na każdym hoście usługi Hyper-V.

1. Uruchom plik Instalatora.
2. W Kreatorze instalacji dostawcy usługi Azure Site Recovery w obszarze **Microsoft Update** wyraź zgodę na używanie usługi Microsoft Update do sprawdzania aktualizacji dostawcy.
3. W obszarze **Instalacja** zaakceptuj domyślną lokalizację instalacji dostawcy i agenta, a następnie kliknij przycisk **Zainstaluj**.
4. Po zakończeniu instalacji w Kreatorze rejestracji usługi Microsoft Azure Site Recovery w obszarze **Ustawienia magazynu** kliknij przycisk **Przeglądaj**, a następnie w oknie **Plik klucza** wybierz pobrany plik klucza magazynu. 
5. Określ subskrypcję usługi Azure Site Recovery, nazwę magazynu (**ContosoVMVault**) oraz lokację funkcji Hyper-V (**ContosoHyperVSite**), do której należy serwer funkcji Hyper-V.
6. W obszarze **Ustawienia serwera proxy** wybierz pozycję **Połącz bezpośrednio z usługą Azure Site Recovery bez serwera proxy**.
7. W obszarze**Rejestracja** po zarejestrowaniu serwera w magazynie kliknij przycisk **Zakończ**.

Metadane z serwera funkcji Hyper-V zostaną pobrane przez usługę Azure Site Recovery, a serwer zostanie wyświetlony w obszarze **Infrastruktura usługi Site Recovery** > **Hosty funkcji Hyper-V**. Ten proces może potrwać do 30 minut.        

#### <a name="install-on-hyper-v-core-server"></a>Zainstaluj na funkcji Hyper-V w trybie server core

Jeśli korzystasz z instalacją server core funkcji Hyper-V, Pobierz plik instalacyjny i wykonaj następujące czynności:

1. Wyodrębnij pliki z AzureSiteRecoveryProvider.exe do katalogu lokalnego, uruchamiając

    `AzureSiteRecoveryProvider.exe /x:. /q`
 
2.  Uruchom ".\setupdr.exe/i. Wyniki są rejestrowane % Programdata%\ASRLogs\DRASetupWizard.log.

3.  Należy zarejestrować serwer przy użyciu następującego polecenia:

    ```
    cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
    ```
 

## <a name="set-up-the-target-environment"></a>Konfigurowanie środowiska docelowego

Wybierz i zweryfikuj zasoby docelowe. 

1. Kliknij pozycję **Przygotowanie infrastruktury** > **Cel**.
2. Wybierz subskrypcję i grupę zasobów **ContosoRG**, w której zostaną utworzone maszyny wirtualne platformy Azure po włączeniu trybu failover.
3. Wybierz model wdrażania usługi **Resource Manager**.

Usługa Site Recovery sprawdza, czy masz co najmniej jedno zgodne konto magazynu Azure i co najmniej jedną sieć platformy Azure.


## <a name="set-up-a-replication-policy"></a>Konfigurowanie zasad replikacji

1. Kliknij kolejno pozycje **Przygotowanie infrastruktury** > **Ustawienia replikacji** > **+ Utwórz i skojarz**.
2. W obszarze **Utwórz i skojarz zasady** określ nazwę zasad. Firma Microsoft korzysta z **ContosoReplicationPolicy**.
3. W tym samouczku pozostawimy wartości domyślnych.
    - **Częstotliwość kopiowania** wskazuje, jak często dane różnicowe (po replikacji początkowej) będą replikowane, co pięć minut domyślnie.
    - **Czas przechowywania punktu odzyskiwania** wskazuje, że punkty odzyskiwania będą przechowywane przez dwie godziny.
    - Ustawienie **Częstotliwość migawek na poziomie aplikacji** wskazuje, że punkty odzyskiwana zawierające migawki na poziomie aplikacji będą tworzone co godzinę.
    - Ustawienie **Czas rozpoczęcia replikacji początkowej** wskazuje, że replikacja początkowa rozpocznie się natychmiast.
4. Po utworzeniu zasad kliknij przycisk **OK**. Podczas tworzenia nowych zasad automatycznie skojarzone z określoną lokacją funkcji Hyper-V (w naszym samouczku to **ContosoHyperVSite**)

    ![Zasady replikacji](./media/hyper-v-azure-tutorial/replication-policy.png)


## <a name="enable-replication"></a>Włączanie replikacji


1. W obszarze **Replikowanie aplikacji** kliknij przycisk **Źródło**. 
2. W obszarze **Źródło** wybierz lokację **ContosoHyperVSite**. Następnie kliknij przycisk **OK**.
3. W obszarze **Cel** sprawdź, czy platforma Azure została wybrana jako środowisko docelowe oraz czy wskazano właściwą subskrypcję magazynu i model wdrożenia **Resource Manager**.
4. Jeśli używasz samouczek ustawienia, wybierz **contosovmsacct1910171607** konta magazynu utworzonego w poprzednim samouczku do replikowania danych, a **ContosoASRnet** sieci, w którym będą maszyn wirtualnych platformy Azure Po włączeniu trybu failover można zlokalizować.
5. W obszarze **Maszyny wirtualne** > **Wybierz** wybierz maszynę wirtualną, którą chcesz replikować. Następnie kliknij przycisk **OK**.

   Możesz śledzić postępy akcji **Włącz ochronę** w obszarze **Zadania** > **Zadania usługi Site Recovery**. Po zakończeniu zadania **Finalizuj ochronę** replikacja początkowa zostanie zakończona, a maszyna wirtualna będzie gotowa do pracy w trybie failover.

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Uruchamianie próbnego odzyskiwania po awarii](tutorial-dr-drill-azure.md)
