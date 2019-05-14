---
title: Przenoszenie maszyn wirtualnych IaaS platformy Azure do innego regionu platformy Azure przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Używanie usługi Azure Site Recovery do przenoszenia maszyn wirtualnych IaaS platformy Azure z jednego regionu świadczenia usługi Azure do innego.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 636993879422edaf08051be61b9d4770effdcae0
ms.sourcegitcommit: 9e8dfa1169a55c3c8af93a6c5f4e0dace4de48b2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/13/2019
ms.locfileid: "65556070"
---
# <a name="move-azure-vms-to-another-region"></a>Przenoszenie maszyn wirtualnych platformy Azure do innego regionu

Istnieją różne scenariusze, w których chcesz przenieść istniejących maszyn wirtualnych IaaS platformy Azure (VM) z jednego regionu do innego. Na przykład chcesz zwiększyć niezawodność i dostępność istniejących maszyn wirtualnych, aby zwiększyć możliwości zarządzania lub, aby przenieść ze względów nadzoru. Aby uzyskać więcej informacji, zobacz [maszyny Wirtualnej platformy Azure, Przenieś Przegląd](azure-to-azure-move-overview.md). 

Możesz użyć [usługi Azure Site Recovery](site-recovery-overview.md) usługę zarządzania i Organizuj odzyskiwanie po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure dla firm ciągłości działania i odzyskiwania po awarii (BCDR). Usługa Site Recovery umożliwia także zarządzanie przenoszenia maszyn wirtualnych platformy Azure do regionu pomocniczego.

W tym samouczku zostaną wykonane następujące czynności:

> [!div class="checklist"]
> 
> * Sprawdź wymagania wstępne dotyczące przenoszenia
> * Przygotowanie źródłowe maszyny wirtualne i region docelowy
> * Skopiuj dane i włączanie replikacji
> * Testowanie konfiguracji i przenoszenie
> * Aby usunąć zasoby w regionie źródłowym
> 
> [!NOTE]
> W tym samouczku dowiesz się, jak przenieść maszyny wirtualne platformy Azure z jednego regionu do innego, ponieważ jest. Jeśli trzeba zwiększyć dostępność, przenosząc maszyn wirtualnych w zestawie dostępności w strefie przypięte maszyn wirtualnych w różnych regionach, zobacz [przenoszenie maszyn wirtualnych platformy Azure w strefach dostępności samouczek](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że maszyny wirtualne platformy Azure w regionie platformy Azure, z którego chcesz przenieść.
- Upewnij się, że wybór [jest obsługiwany region źródła - kombinacja region docelowy](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)i podjąć świadomą decyzję o regionie docelowym.
- Przeanalizuj informacje o [składnikach i architekturze scenariusza](azure-to-azure-architecture.md).
- Zapoznaj się z [ograniczeniami i wymaganiami dotyczącymi pomocy technicznej](azure-to-azure-support-matrix.md).
- Sprawdzanie uprawnień konta. Twoje bezpłatne konto platformy Azure zostało utworzone, jesteś administratorem subskrypcji. Jeśli nie jesteś administratorem subskrypcji, Współpracuj z administratorem w celu przypisania uprawnień, które należy. Aby włączyć replikację dla maszyny Wirtualnej i zasadniczo kopiowanie danych przy użyciu usługi Azure Site Recovery, musisz mieć:

    * Uprawnienia do tworzenia maszyny wirtualnej w ramach zasobów platformy Azure. Wbudowana rola Współautor maszyny wirtualnej ma te uprawnienia, które obejmują:
        - Uprawnienie do tworzenia maszyny wirtualnej w wybranej grupie zasobów
        - Uprawnienie do tworzenia maszyny wirtualnej w wybranej sieci wirtualnej
        - Uprawnienie do zapisu na wybranym koncie magazynu

    * Uprawnienia do zarządzania operacjami usługi Azure Site Recovery. Rola Współautor usługi Site Recovery ma wszystkie uprawnienia, które są wymagane do zarządzania operacjami usługi Site Recovery w magazynie usługi Recovery Services.

## <a name="prepare-the-source-vms"></a>Przygotowywanie źródłowych maszyn wirtualnych

1. Upewnij się, że wszystkie najnowsze certyfikaty główne na maszynach wirtualnych platformy Azure, który chcesz przenieść. Jeśli na maszynie Wirtualnej nie są najnowsze certyfikaty główne, ograniczenia zabezpieczeń uniemożliwi kopiowanie danych w regionie docelowym.

    - Aby zainstalować wszystkie zaufane certyfikaty główne na maszynach wirtualnych z systemem Windows, zainstaluj wszystkie najnowsze aktualizacje systemu Windows. W środowisku bez połączenia postępuj zgodnie ze standardową procedurą usługi Windows Update i procedurą aktualizacji certyfikatów, które są używane w danej organizacji.
    - W przypadku maszyn wirtualnych systemu Linux postępuj zgodnie ze wskazówkami dystrybutora systemu Linux można pobrać najnowsze wymagane certyfikaty główne i listę odwołania certyfikatów na maszynie Wirtualnej.
1. Upewnij się, że nie używasz uwierzytelniającego serwera proxy do sterowania łącznością sieciową dla maszyn wirtualnych, które chcesz przenieść.
1. Jeśli maszynę Wirtualną, która próbujesz przenieść nie ma dostępu do Internetu lub korzysta ona z serwera proxy zapory do kontrolowania dostępu wychodzącego [Sprawdź wymagania](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).
1. Określ układ sieci źródłowego i wszystkie zasoby, które obecnie używasz. To m.in. nie ogranicza się do usługi równoważenia obciążenia, sieciowe grupy zabezpieczeń (NSG) i publicznych adresów IP.

## <a name="prepare-the-target-region"></a>Przygotowywanie regionu docelowego

1. Sprawdź, czy subskrypcji platformy Azure umożliwia tworzenie maszyn wirtualnych w regionie docelowym, który służy do odzyskiwania po awarii. Skontaktuj się z pomocą techniczną, aby włączyć wymagany limit przydziału.

1. Upewnij się, że Twoja subskrypcja ma za mało zasobów do obsługi maszyn wirtualnych o rozmiarach odpowiadających źródłowym maszynom wirtualnym. Jeśli używasz Site Recovery umożliwia kopiowanie danych do docelowej, Usługa Site Recovery wybiera ten sam lub najbardziej zbliżony rozmiar dla docelowej maszyny Wirtualnej.

1. Upewnij się, utwórz zasób docelowy dla każdego składnika, który jest identyfikowany w układzie sieci źródłowej. Ten krok jest ważne upewnić się, że maszyny wirtualne mają wszystkie funkcje i w regionie docelowym, które były dostępne w regionie źródłowym.

    > [!NOTE]
    > Usługa Azure Site Recovery automatycznie wykrywa i tworzy sieć wirtualną po włączeniu replikacji dla źródłowej maszyny Wirtualnej. Można wstępnie utworzyć sieć i przypisać je do maszyn wirtualnych w usłudze flow użytkownika dla. Włączanie replikacji. Jak wspomniano wcześniej później, należy ręcznie utworzyć wszelkie inne zasoby w regionie docelowym.

     Do utworzenia w pełni powszechnie używane zasobów sieciowych, które są odpowiednie na podstawie konfiguracji źródłowej maszyny Wirtualnej, znajduje się poniższej dokumentacji:

   - [Sieciowe grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
   - [Moduły równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
   - [Publiczny adres IP](../virtual-network/virtual-network-public-ip-address.md)
    
     Dla innych składników sieciowych, zobacz [sieć dokumentacji](https://docs.microsoft.com/azure/#pivot=products&panel=network).

1. Ręcznie [Utwórz sieć nieprodukcyjnych](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) w regionie docelowym, jeśli chcesz przetestować konfigurację, przed wykonaniem końcowego przenoszenia w regionie docelowym. Firma Microsoft zaleca tego kroku, ponieważ zapewnia minimalne zakłócenia w sieci produkcyjnej.

## <a name="copy-data-to-the-target-region"></a>Kopiowanie danych do regionu docelowego
Poniższe kroki pokazują, jak używać usługi Azure Site Recovery umożliwia kopiowanie danych do regionu docelowego.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Utwórz magazyn w dowolnym regionie, z wyjątkiem regionu źródłowego

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) > **Recovery Services**.
1. Wybierz **Utwórz zasób** > **narzędzia do zarządzania** > **Backup i Site Recovery**.
1. W polu **Nazwa** podaj przyjazną nazwę **ContosoVMVault**. Jeśli masz więcej niż jedną subskrypcję, wybierz jedną z nich.
1. Utwórz grupę zasobów **ContosoRG**.
1. Określ region platformy Azure. Aby sprawdzić obsługiwane regiony, zobacz sekcję dotyczącą dostępności geograficznej w [szczegóły cennika usługi Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
1. W **Magazyny usługi Recovery Services**, wybierz opcję **Przegląd** > **ContosoVMVault** > **+ Replikuj**.
1. W obszarze **Źródło** wybierz pozycję **Azure**.
1. W obszarze **Lokalizacja źródłowa** wybierz źródłowy region świadczenia usługi Azure, w którym są uruchomione maszyny wirtualne.
1. Wybierz model wdrażania usługi Resource Manager. Następnie wybierz pozycje **Subskrypcja źródłowa** i **Źródłowa grupa zasobów**.
1. Wybierz **OK** Aby zapisać ustawienia.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Włącz replikację dla maszyn wirtualnych platformy Azure i Rozpocznij kopiowanie danych

Usługa Site Recovery pobiera listę maszyn wirtualnych, które są skojarzone z subskrypcji i grupie zasobów.

1. W następnym kroku, wybierz maszynę Wirtualną, którą chcesz przenieść, a następnie wybierz **OK**.
1. W **ustawienia**, wybierz opcję **odzyskiwania po awarii**.
1. W **Konfigurowanie odzyskiwania po awarii** > **region docelowy**, wybierz region docelowy, do którego będą replikowane.
1. Dla celów tego samouczka zaakceptuj inne ustawienia domyślne.
1. Wybierz **włączyć replikację**. W tym kroku uruchamia zadanie, aby włączyć replikację dla maszyny Wirtualnej.

    ![Włączanie replikacji](media/tutorial-migrate-azure-to-azure/settings.png)

## <a name="test-the-configuration"></a>Testowanie konfiguracji

1. Przejdź do magazynu. W **ustawienia** > **zreplikowane elementy**, wybierz maszynę Wirtualną, którą chcesz przenieść do regionu docelowego, a następnie wybierz **+ testowy tryb Failover**.
1. W **testowy tryb Failover**, wybierz punkt odzyskiwania na potrzeby pracy w trybie failover:

   - **Najnowszy przetworzony**: wprowadza maszynę wirtualną w tryb failover do najnowszego punktu odzyskiwania przetworzonego przez usługę Site Recovery. Wyświetlana jest sygnatura czasowa. Po wybraniu tej opcji nie jest czas przetwarzania danych, dzięki czemu zapewnia niski cel czasu odzyskiwania (RTO).
   - **Najnowszy spójny na poziomie aplikacji**: ta opcja wprowadza wszystkie maszyny wirtualne w tryb failover do najnowszego spójnego na poziomie aplikacji punktu odzyskiwania. Wyświetlana jest sygnatura czasowa.
   - **Niestandardowy**: można wybrać dowolny punkt odzyskiwania.

1. Wybierz docelową sieć wirtualną platformy Azure, do której chcesz przenieść maszyny wirtualne platformy Azure, aby przetestować konfigurację.
    > [!IMPORTANT]
    > Zalecamy użycie oddzielnej sieci maszyn wirtualnych platformy Azure do testowania trybu failover. Nie należy używać produkcyjnego środowiska sieciowego, który został skonfigurowany po włączeniu replikacji, i chcesz przenosić maszyny wirtualne do po pewnym czasie.
1. Aby rozpocząć testowanie przenoszenia, kliknij przycisk **OK**. Aby śledzić postępy, kliknij maszynę wirtualną w celu otwarcia jej właściwości. Możesz też kliknąć zadanie **Test pracy w trybie failover** w obszarze nazwy magazynu > **Ustawienia** > **Zadania** > **Zadania usługi Site Recovery**.
1. Po zakończeniu trybu failover w obszarze Azure Portal > **Maszyny wirtualne** będzie widoczna replika maszyny wirtualnej platformy Azure. Upewnij się, że maszyna wirtualna jest uruchomiona, ma odpowiedni rozmiar i jest połączona z odpowiednią siecią.
1. Jeśli chcesz usunąć tha maszyna wirtualna została utworzona w ramach testowania przeniesienie, kliknij pozycję **wyczyść test pracy awaryjnej** replikowanego elementu. W **uwagi**Zarejestruj i zapisz wszelkie obserwacje, które są skojarzone z testem.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Przenoszenie do regionu docelowego i upewnij się

1. Przejdź do magazynu. W **ustawienia** > **zreplikowane elementy**, wybierz maszynę Wirtualną, a następnie wybierz **trybu Failover**.
1. W obszarze **Tryb failover** wybierz pozycję **Najnowsze**.
1. Wybierz pozycję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover**. Usługa Site Recovery próbuje zamknąć źródłową maszynę wirtualną przed wyzwoleniem trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Na stronie **Zadania** można śledzić postęp trybu failover.
1. Po zakończeniu zadania Sprawdź, czy maszyna wirtualna jest wyświetlana w element docelowy region świadczenia usługi Azure, zgodnie z oczekiwaniami.
1. W **zreplikowane elementy**, po prawej stronie i wybierz maszynę Wirtualną > **zatwierdzić**. Ten krok kończy proces przenoszenia region docelowy. Poczekaj na zakończenie zadania zatwierdzenia.

## <a name="delete-the-resource-in-the-source-region"></a>Usuwanie zasobu w regionie źródłowym

Przejdź do maszyny Wirtualnej. Wybierz **Wyłącz replikację**. Ten krok powoduje zatrzymanie procesu z kopiowania danych dla maszyny Wirtualnej.

> [!IMPORTANT]
> Należy wykonać ten krok, aby uniknąć opłata jest naliczana za replikacji usługi Azure Site Recovery.

Jeśli nie planuje ponowne użycie zasobów źródłowego, wykonaj następujące kroki:

1. Aby usunąć wszystkie zasoby odpowiednich sieci w regionie źródłowym, znajdującego się w ramach kroku 4 [przygotowanie źródłowe maszyny wirtualne](#prepare-the-source-vms).
1. Usuń odpowiednie konto magazynu w regionie źródłowym.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku został przeniesiony do innego regionu platformy Azure Maszynie wirtualnej platformy Azure. Teraz można skonfigurować odzyskiwanie po awarii dla maszyny Wirtualnej, który został przeniesiony.

> [!div class="nextstepaction"]
> [Konfigurowanie odzyskiwania po awarii po migracji](azure-to-azure-quickstart.md)

