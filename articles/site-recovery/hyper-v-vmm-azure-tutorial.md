---
title: Konfigurowanie funkcji Hyper-V (z programem VMM) odzyskiwanie awaryjne przy użyciu Azure Site Recovery
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii lokalnych maszyn wirtualnych funkcji Hyper-V w chmurach programu System Center VMM do platformy Azure przy użyciu Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 0c570702e4c3899ef2847883e6fc8649e603a787
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79281680"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Konfigurowanie odzyskiwania po awarii lokalnych maszyn wirtualnych funkcji Hyper-V w chmurach programu VMM na platformie Azure

W tym artykule opisano sposób włączania replikacji lokalnych maszyn wirtualnych funkcji Hyper-V zarządzanych przez program System Center Virtual Machine Manager (VMM) na potrzeby odzyskiwania po awarii na platformie Azure przy użyciu usługi [Azure Site Recovery](site-recovery-overview.md) . Jeśli nie korzystasz z programu VMM, [postępuj zgodnie z tym samouczkiem](hyper-v-azure-tutorial.md) .

Jest to trzeci samouczek z serii, który pokazuje, jak skonfigurować odzyskiwanie po awarii na platformie Azure dla lokalnych maszyn wirtualnych VMware. W poprzednim samouczku [przygotowano lokalne środowisko funkcji Hyper-V](hyper-v-prepare-on-premises-tutorial.md) na potrzeby odzyskiwania po awarii na platformie Azure.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wybieranie źródła i celu replikacji.
> * Skonfiguruj środowisko replikacji źródłowej, w tym lokalne składniki Site Recovery i środowisko docelowej replikacji.
> * Skonfiguruj mapowanie sieci na potrzeby mapowania między sieciami maszyn wirtualnych programu VMM i sieciami wirtualnymi platformy Azure.
> * Tworzenie zasad replikacji.
> * Włączanie replikacji maszyny wirtualnej.

> [!NOTE]
> Samouczki pokazują najprostszą ścieżkę wdrożenia dla scenariusza. Jeśli to możliwe, używają opcji domyślnych i nie przedstawiają wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z artykułami w sekcji **przewodniki** z instrukcjami w [dokumentacji Site Recovery](https://docs.microsoft.com/azure/site-recovery).



## <a name="before-you-begin"></a>Przed rozpoczęciem

Jest to trzeci samouczek z tej serii. Przyjęto założenie, że zadania zostały już wykonane w poprzednich samouczkach:

1. [Przygotowywanie platformy Azure](tutorial-prepare-azure.md)
2. [Przygotowywanie lokalnej funkcji Hyper-V](tutorial-prepare-on-premises-hyper-v.md)

## <a name="select-a-replication-goal"></a>Wybieranie celu replikacji

1. W Azure Portal przejdź do obszaru **magazyny Recovery Services** i wybierz magazyn. **ContosoVMVault** magazynu został przygotowany w poprzednim samouczku.
2. W **wprowadzenie**wybierz pozycję **Site Recovery**, a następnie wybierz pozycję **Przygotuj infrastrukturę**.
3. W obszarze **cel ochrony** > gdzie znajdują się **maszyny?** wybierz pozycję **lokalnie**.
4. W obszarze **gdzie chcesz replikować maszyny?** wybierz pozycję **na platformie Azure**.
5. W obszarze **czy maszyny są zwirtualizowane?** wybierz pozycję **tak, używając funkcji Hyper-V**.
6. Czy w programie **System Center VMM jest używany do zarządzania hostami funkcji Hyper-V?** wybierz pozycję **tak**.
7.  Kliknij przycisk **OK**.

    ![Cel replikacji](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Potwierdzanie planowania wdrożenia

1. W przypadku **planowania wdrożenia**, jeśli planujesz duże wdrożenie, Pobierz planista wdrażania dla funkcji Hyper-V z linku na stronie. [Dowiedz się więcej](hyper-v-deployment-planner-overview.md) o planowaniu wdrożenia funkcji Hyper-V.
2. W tym samouczku nie są potrzebne Planista wdrażania. Czy w programie **wykonano Planowanie wdrożenia?** zaznacz opcję **chcę zrobić to później**, a następnie wybierz **przycisk OK**.

## <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

Podczas konfigurowania środowiska źródłowego należy zainstalować dostawcę Azure Site Recovery na serwerze programu VMM i zarejestrować serwer w magazynie. Należy zainstalować agenta Recovery Services platformy Azure na każdym hoście funkcji Hyper-V.

1. W obszarze **Przygotowanie infrastruktury**wybierz pozycję **Źródło**.
2. W obszarze **Przygotowywanie źródła**wybierz pozycję **+ VMM** , aby dodać serwer programu VMM. W obszarze **Dodaj serwer** sprawdź, czy pozycja **Serwer System Center VMM** pojawia się jako **Typ serwera**.
3. Pobierz Instalatora dla dostawcy Site Recovery Microsoft Azure.
4. Pobierz klucz rejestracji magazynu. Ten klucz będzie potrzebny podczas uruchamiania Instalatora dostawcy. Klucz jest ważny przez pięć dni po jego wygenerowaniu.
5. Pobierz Instalatora dla agenta Microsoft Azure Recovery Services.

    ![Pobierz dostawcę, klucz rejestracji i agenta](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>Instalowanie dostawcy na serwerze VMM

1. W Kreatorze instalacji dostawcy usługi Azure Site Recovery w obszarze **Microsoft Update** wyraź zgodę na używanie usługi Microsoft Update do sprawdzania aktualizacji dostawcy.
2. W obszarze **Instalacja**zaakceptuj domyślną lokalizację instalacji dostawcy i wybierz pozycję **Zainstaluj**.
3. Po zakończeniu instalacji w Kreatorze Microsoft Azure rejestracji Site Recovery > **ustawienia magazynu**wybierz pozycję **Przeglądaj**, a następnie w polu **plik klucza**wybierz pobrany plik klucza magazynu.
4. Określ subskrypcję Azure Site Recovery i nazwę magazynu (**ContosoVMVault**). Określ przyjazną nazwę serwera programu VMM, aby zidentyfikować ją w magazynie.
5. W obszarze **Ustawienia serwera proxy** wybierz pozycję **Połącz bezpośrednio z usługą Azure Site Recovery bez serwera proxy**.
6. Zaakceptuj domyślną lokalizację certyfikatu używanego do szyfrowania danych. Zaszyfrowane dane zostaną odszyfrowane po przełączeniu w tryb failover.
7. W obszarze **Synchronizuj metadane chmury**wybierz pozycję **Synchronizuj metadane chmury do portalu Site Recovery**. Ta akcja musi wystąpić tylko raz na każdym serwerze. Następnie wybierz pozycję **zarejestruj**.
8. Po zarejestrowaniu serwera w magazynie wybierz pozycję **Zakończ**.

Po zakończeniu rejestracji metadane z serwera są pobierane przez Azure Site Recovery, a serwer programu VMM jest wyświetlany w **infrastrukturze Site Recovery**.

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>Zainstaluj agenta Recovery Services na hostach funkcji Hyper-V

Zainstaluj agenta na każdym hoście funkcji Hyper-V zawierającym maszyny wirtualne, które chcesz replikować.

1. W Kreatorze instalacji agenta Microsoft Azure Recovery Services > **Sprawdzanie wymagań wstępnych**wybierz pozycję **dalej**. Wszystkie brakujące wymagania wstępne zostaną zainstalowane automatycznie.
2. W obszarze **ustawienia instalacji**Zaakceptuj lokalizację instalacji i lokalizację pamięci podręcznej. Dysk pamięci podręcznej musi mieć co najmniej 5 GB miejsca w magazynie. Zalecamy używanie dysku z 600 GB lub więcej wolnego miejsca. Następnie wybierz pozycję **Zainstaluj**.
3. Po zakończeniu instalacji kliknij przycisk **Zamknij** **, aby**zakończyć pracę kreatora.

    ![Zainstaluj agenta](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>Konfigurowanie środowiska docelowego

1. Wybierz pozycje **Przygotowanie infrastruktury** > **Cel**.
2. Wybierz subskrypcję i grupę zasobów (**ContosoRG**), w której zostaną utworzone maszyny wirtualne platformy Azure po przejściu do trybu failover.
3. Wybierz model wdrażania **Menedżer zasobów** .

Usługa Site Recovery sprawdza, czy masz co najmniej jedno zgodne konto magazynu Azure i co najmniej jedną sieć platformy Azure.

## <a name="configure-network-mapping"></a>Konfiguracja mapowania sieci

1. W obszarze **Site Recovery infrastruktura** > **mapowania sieci** > **mapowanie sieci**wybierz ikonę **+ mapowanie sieci** .
2. W obszarze **Dodawanie mapowania sieci**wybierz źródłowy serwer programu VMM. Wybierz **platformę Azure** jako element docelowy.
3. Sprawdź subskrypcję i model wdrożenia po przejściu do trybu failover.
4. W obszarze **Sieć źródłowa**wybierz źródłową lokalną maszynę wirtualną.
5. W obszarze **Sieć docelowa**wybierz sieć platformy Azure, w której będą znajdować się repliki maszyn wirtualnych platformy Azure po ich utworzeniu po przejściu do trybu failover. Następnie wybierz przycisk **OK**.

    ![Mapowanie sieci](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Konfigurowanie zasad replikacji

1. Wybierz pozycję **Przygotuj infrastrukturę** > **Ustawienia replikacji** >  **+ Utwórz i skojarz**.
2. W obszarze **Utwórz i skojarz zasady** określ nazwę zasad. Korzystamy z usługi **ContosoReplicationPolicy**.
3. Pozostaw ustawienia domyślne i wybierz **przycisk OK**.
    - **Częstotliwość kopiowania** wskazuje, że po replikacji początkowej dane różnicowe będą replikowane co pięć minut.
    - **Przechowywanie punktów odzyskiwania** wskazuje, że każdy punkt odzyskiwania będzie przechowywany przez dwie godziny.
    - Ustawienie **Częstotliwość migawek na poziomie aplikacji** wskazuje, że punkty odzyskiwana zawierające migawki na poziomie aplikacji będą tworzone co godzinę.
    - **Czas rozpoczęcia replikacji początkowej** wskazuje, że replikacja początkowa rozpocznie się natychmiast.
    - **Szyfrowanie danych przechowywanych na platformie Azure** jest ustawione na wartość domyślne (**wyłączone**) i wskazuje, że dane w spoczynku na platformie Azure nie są szyfrowane.
4. Po utworzeniu zasad wybierz **przycisk OK**. Nowo utworzone zasady są automatycznie kojarzone z chmurą programu VMM.

## <a name="enable-replication"></a>Włączanie replikacji

1. W obszarze **replikacja aplikacji**wybierz pozycję **Źródło**.
2. W obszarze **Źródło**Wybierz chmurę programu VMM. Następnie wybierz przycisk **OK**.
3. W obszarze **cel**Sprawdź, czy element docelowy (Azure), subskrypcję magazynu i wybierz model **Menedżer zasobów** .
4. Wybierz konto magazynu **contosovmsacct1910171607** i Sieć **ContosoASRnet** Azure.
5. W obszarze **maszyny wirtualne** > **Wybierz**opcję, a następnie wybierz maszynę wirtualną, którą chcesz replikować. Następnie wybierz przycisk **OK**.

   Możesz śledzić postępy akcji **Włącz ochronę** w obszarze **Zadania** > **Zadania usługi Site Recovery**. Po zakończeniu zadania **finalizowania ochrony** Replikacja początkowa zostanie zakończona, a maszyna wirtualna jest gotowa do przejścia w tryb failover.

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Uruchamianie próbnego odzyskiwania po awarii](tutorial-dr-drill-azure.md)
