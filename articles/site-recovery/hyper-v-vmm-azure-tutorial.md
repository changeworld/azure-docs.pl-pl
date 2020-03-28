---
title: Konfigurowanie odzyskiwania po awarii funkcji Hyper-V (z programem VMM) przy użyciu usługi Azure Site Recovery
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii lokalnych maszyn wirtualnych funkcji Hyper-V w chmurach programu Windows Programu Windows programu Windows na platformie Azure przy użyciu usługi Site Recovery.
ms.topic: tutorial
ms.date: 03/19/2020
ms.custom: MVC
ms.openlocfilehash: a391d8eb3cf7bc43b52883cbf2e76170338c44c6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067573"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Konfigurowanie odzyskiwania po awarii lokalnych maszyn wirtualnych funkcji Hyper-V w chmurach VMM na platformie Azure

W tym samouczku opisano sposób włączania replikacji lokalnych maszyn wirtualnych funkcji Hyper-V zarządzanych przez menedżera maszyn wirtualnych (VMM) w centrum systemu na platformie Azure przy użyciu [usługi Azure Site Recovery.](site-recovery-overview.md) Jeśli nie używasz programu VMM, [wykonaj ten samouczek](hyper-v-azure-tutorial.md).

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wybieranie źródła i celu replikacji.
> * Skonfiguruj źródłowe środowisko replikacji, w tym lokalne składniki odzyskiwania lokacji i docelowe środowisko replikacji.
> * Konfigurowanie mapowania sieci między sieciami maszyn wirtualnych programu VMM a sieciami wirtualnymi platformy Azure.
> * Tworzenie zasad replikacji.
> * Włączanie replikacji maszyny wirtualnej.

> [!NOTE]
> Samouczki pokazują najprostszą ścieżkę wdrażania dla scenariusza. Jeśli to możliwe używają opcji domyślnych i nie przedstawiają wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z artykułami w sekcji **Instrukcje obsługi** [dokumentacji odzyskiwania witryny.](/azure/site-recovery/)

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku założono, że ukończono już następujące samouczki:

1. [Przygotowywanie platformy Azure](tutorial-prepare-azure.md)
1. [Przygotowywanie lokalnych serwerów funkcji Hyper-V](hyper-v-prepare-on-premises-tutorial.md)

## <a name="select-a-replication-goal"></a>Wybieranie celu replikacji

1. W witrynie Azure portal przejdź do **magazynów usług odzyskiwania** i wybierz magazyn **ContosoVMVault,** który został utworzony w samouczku [Przygotowywanie platformy Azure.](tutorial-prepare-azure.md#create-a-recovery-services-vault)
1. W **obszarze Wprowadzenie**wybierz pozycję Przygotowanie infrastruktury odzyskiwania >  **witryny**i skonfiguruj następujące ustawienia:**Prepare Infrastructure**
    1. **Cel** > ochrony**Gdzie znajdują się twoje maszyny?**, wybierz **on-premises**.
    1. **Gdzie chcesz replikować swoje maszyny?**, wybierz **opcję Na platformę Azure**.
    1. **Czy twoje maszyny są zwirtualizowane?**, wybierz **tak, z hyper-V**.
    1. **Czy do zarządzania hostami funkcji Hyper-V używasz programu System Center V?** **Yes**
1. Kliknij przycisk **OK**.

   ![Cel replikacji](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Potwierdzanie planowania wdrożenia

1. W **przypadku planowania wdrażania,** jeśli planujesz duże wdrożenie, pobierz planista wdrażania dla funkcji Hyper-V z łącza na stronie. [Dowiedz się więcej](hyper-v-deployment-planner-overview.md) o planowaniu wdrażania funkcji Hyper-V.
1. W tym samouczku nie potrzebujemy planisty wdrażania. W **obszarze Czy ukończono planowanie wdrażania?**, wybierz **pozycję Zrobię to później**, a następnie wybierz przycisk **OK**.

## <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

Podczas konfigurowania środowiska źródłowego należy zainstalować dostawcę odzyskiwania witryny Azure na serwerze programu VMM i zarejestrować serwer w przechowalni. Agent usługi Azure Recovery Services jest instalowy na każdym hoście funkcji Hyper-V.

1. **Przygotuj infrastrukturę**. Wybierz **źródło**.
1. **Przygotuj źródło**. Wybierz **+ Program VMM,** aby dodać serwer programu VMM. W obszarze **Dodaj serwer** sprawdź, czy pozycja **Serwer System Center VMM** pojawia się jako **Typ serwera**.
1. Pobierz instalator dla dostawcy odzyskiwania witryny platformy Microsoft Azure.
1. Pobierz klucz rejestracji magazynu. Ten klucz jest potrzebny po uruchomieniu konfiguracji dostawcy. Klucz jest ważny przez pięć dni po jego wygenerowaniu.
1. Pobierz instalator dla agenta usług odzyskiwania platformy Microsoft Azure.

   ![Dostawca pobierania, klucz rejestracji i agent](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>Instalowanie dostawcy na serwerze VMM

1. W kreatorze konfigurowania dostawcy odzyskiwania witryny platformy Azure **Microsoft Update**. Zgłoś się do korzystania z witryny Microsoft Update w celu sprawdzenia dostępności aktualizacji dostawcy.
1. **Instalacja**. Zaakceptuj domyślną lokalizację instalacji dostawcy i wybierz pozycję **Zainstaluj**.
1. Po instalacji w Kreatorze rejestracji odzyskiwania witryny platformy Microsoft Azure wybierz pozycję **Ustawienia przechowalni**, **Przeglądaj**i w **pliku klucza**wybierz pobrany plik klucza przechowalni.
1. Określ subskrypcję usługi Azure Site Recovery i nazwę magazynu **(ContosoVMVault).** Określ przyjazną nazwę serwera programu VMM, aby zidentyfikować go w przechowalni.
1. **Ustawienia serwera proxy**. Wybierz **opcję Połącz bezpośrednio z usługą Azure Site Recovery bez serwera proxy**.
1. Zaakceptuj domyślną lokalizację certyfikatu używanego do szyfrowania danych. Zaszyfrowane dane zostaną odszyfrowane po przełączeniu awaryjnym.
1. **Synchronizowanie metadanych w chmurze**. Wybierz **pozycję Synchronizuj metawybory w chmurze z portalem odzyskiwania witryn**. Ta akcja musi się zdarzyć tylko raz na każdym serwerze. Następnie wybierz pozycję **Zarejestruj**.
1. Po zarejestrowaniu serwera w przechowalni wybierz opcję **Zakończ**.

Po zakończeniu rejestracji metadane z serwera są pobierane przez usługę Azure Site Recovery, a serwer programu VMM jest wyświetlany w **infrastrukturze odzyskiwania lokacji**.

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>Instalowanie agenta usług odzyskiwania na hostach funkcji Hyper-V

Zainstaluj agenta na każdym hoście funkcji Hyper-V zawierającym maszyny wirtualne, które chcesz replikować.

W Kreatorze instalacji agenta usług odzyskiwania platformy Microsoft Azure skonfiguruj następujące ustawienia:

1. **Wymagania wstępne Sprawdź**. Wybierz **pozycję Dalej**. Wszelkie brakujące wymagania wstępne zostaną zainstalowane automatycznie.
1. **Ustawienia instalacji**. Zaakceptuj lokalizację instalacji i lokalizację pamięci podręcznej. Dysk pamięci podręcznej wymaga co najmniej 5 GB miejsca. Zalecamy dysk z 600 GB lub więcej wolnego miejsca. Następnie wybierz pozycję **Zainstaluj**.
1. **Instalacja**. Po zakończeniu instalacji wybierz przycisk **Zamknij,** aby zakończyć kreatora.

   ![Instalowanie agenta](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>Konfigurowanie środowiska docelowego

1. Wybierz **pozycję Przygotuj infrastrukturę** > **docelową**.
1. Wybierz subskrypcję i grupę zasobów **(ContosoRG),** w której maszyny wirtualne platformy Azure zostaną utworzone po przemienniu w stan failover.
1. Wybierz model wdrażania **Menedżera zasobów.**

Usługa Site Recovery sprawdza, czy istnieje co najmniej jedno zgodne konta i sieci magazynu platformy Azure.

## <a name="configure-network-mapping"></a>Konfiguracja mapowania sieci

1. **Mapowanie** > **sieci**infrastruktury odzyskiwania**lokacji** > . Wybierz ikonę **+Mapowanie sieci.**
1. **Dodaj mapowanie sieci**. Wybierz serwer **programu VMM source system center.** W przypadku **opcji Docelowa**wybierz pozycję Azure.
1. Sprawdź subskrypcję i model wdrożenia po przejściu do trybu failover.
1. **Sieć źródłowsza**. Wybierz źródło lokalną sieć maszyn wirtualnych.
1. **Sieć docelowa**. Wybierz sieć platformy Azure, w której będą znajdować się maszyny wirtualne platformy Azure, gdy zostaną utworzone po przełączeniu awaryjnym. Następnie wybierz przycisk **OK**.

   ![Mapowanie sieci](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Konfigurowanie zasad replikacji

1. Wybierz **pozycję Przygotuj** > **ustawienia** > replikacji infrastruktury **+Utwórz i skojarzyć**.
1. W **obszarze Tworzenie i kojarzenie zasad**określ nazwę zasad. Używamy **ContosoReplicationPolicy**.
1. Zaakceptuj ustawienia domyślne i wybierz **przycisk OK:**
   - **Częstotliwość kopiowania** wskazuje, że po replikacji początkowej dane delta będą replikowane co pięć minut.
   - **Zatrzymanie punktu odzyskiwania** wskazuje, że każdy punkt odzyskiwania zostanie zachowany przez dwie godziny.
   - Ustawienie **Częstotliwość migawek na poziomie aplikacji** wskazuje, że punkty odzyskiwana zawierające migawki na poziomie aplikacji będą tworzone co godzinę.
   - **Początkowa godzina rozpoczęcia replikacji** wskazuje, że replikacja początkowa rozpocznie się natychmiast.
   - **Szyfrowanie danych przechowywanych na platformie Azure** jest ustawiona na domyślną **(Wyłącz)** i wskazuje, że dane w spoczynku na platformie Azure nie są szyfrowane.
1. Po utworzeniu zasady wybierz przycisk **OK**. Podczas tworzenia nowej zasady jest automatycznie skojarzony z chmurą programu VMM.

## <a name="enable-replication"></a>Włączanie replikacji

1. **Replikacja aplikacji**. Wybierz **źródło**.
1. **Źródło**. Wybierz chmurę programu VMM. Następnie wybierz przycisk **OK**.
1. **Cel**. Sprawdź obiekt docelowy (Azure), subskrypcję przechowalni i wybierz model **Menedżera zasobów.**
1. Wybierz konto magazynu **contosovmsacct1910171607** i sieć Platformy Azure **ContosoASRnet.**
1. **Maszyny wirtualne** > **Wybierz**. Wybierz maszynę wirtualną, którą chcesz replikować. Następnie wybierz przycisk **OK**.

   Możesz śledzić postępy akcji **Włącz ochronę** w obszarze **Zadania** > **Zadania usługi Site Recovery**. Po zakończeniu zadania **Finalize Protection** replikacja początkowa została zakończona, a maszyna wirtualna jest gotowa do pracy awaryjnej.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Uruchamianie próbnego odzyskiwania po awarii](tutorial-dr-drill-azure.md)
