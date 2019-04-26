---
title: Przenoszenie maszyn wirtualnych IaaS platformy Azure do innego regionu świadczenia usługi Azure jako maszyn wirtualnych przypiętych do strefy przy użyciu usługi Azure Site Recovery | Microsoft Docs
description: Przenoszenie maszyn wirtualnych IaaS platformy Azure do innego regionu świadczenia usługi Azure jako maszyn wirtualnych przypiętych do strefy za pomocą usługi Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: b6107211f49978bbacd1a827a9adc37ccef60a5b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60196210"
---
# <a name="move-azure-vms-into-availability-zones"></a>Przenoszenie maszyn wirtualnych platformy Azure do stref dostępności
Strefy dostępności na platformie Azure chronić aplikacje i dane przed awariami centrum danych. Każda strefa dostępności obejmuje co najmniej jedno centrum danych wyposażone w niezależne zasilanie i chłodzenie oraz niezależną sieć. W celu zapewnienia odporności istnieją co najmniej trzy osobne strefy we wszystkich włączonych regionach. Fizyczne rozdzielenie stref dostępności w obrębie regionu pomaga chronić aplikacje i dane przed awariami centrum danych. Dzięki strefom dostępności platforma Azure oferuje umowy poziomu usług (SLA) 99,99% dla przestojów maszyn wirtualnych (VM). Strefy dostępności są obsługiwane w wybranych regionach, jak wspomniano w [co to są strefy dostępności na platformie Azure?](https://docs.microsoft.com/azure/availability-zones/az-overview#regions-that-support-availability-zones).

W scenariuszu, w której maszyny wirtualne są wdrażane jako *pojedyncze wystąpienie* w określonym regionie, a chcesz poprawić poziomu dostępności przez przeniesienie tych maszyn wirtualnych w strefie dostępności, możesz to zrobić za pomocą usługi Azure Site Recovery. Dodatkowo można podzielić na tę akcję:

- Przenoszenie maszyn wirtualnych z jednego wystąpienia w strefach dostępności w regionie docelowym
- Przenoszenie maszyn wirtualnych w zestawie w strefach dostępności w regionie docelowym dostępności

> [!IMPORTANT]
> Obecnie usługa Azure Site Recovery obsługuje przenoszenie maszyn wirtualnych z jednego regionu do innego, ale nie obsługuje przenoszenia w obrębie regionu.

## <a name="check-prerequisites"></a>Sprawdź wymagania wstępne

- Sprawdź, czy region docelowy ma [obsługę strefy dostępności](https://docs.microsoft.com/azure/availability-zones/az-overview#regions-that-support-availability-zones). Sprawdź, czy wybór [kombinacja region docelowy region źródła jest obsługiwana](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support). Podjąć świadomą decyzję w regionie docelowym.
- Przeanalizuj informacje o [składnikach i architekturze scenariusza](azure-to-azure-architecture.md).
- Zapoznaj się z [ograniczeniami i wymaganiami dotyczącymi pomocy technicznej](azure-to-azure-support-matrix.md).
- Sprawdź uprawnienia dla konta. Jeśli właśnie utworzone bezpłatnego konta platformy Azure, jesteś administratorem subskrypcji. Jeśli nie jesteś administratorem subskrypcji, należy skontaktować się z administratorem, aby przypisać uprawnienia, których potrzebujesz. Aby włączyć replikację dla maszyny Wirtualnej i ostatecznie kopiowanie danych do obiektu docelowego przy użyciu usługi Azure Site Recovery, musisz mieć:

    1. Uprawnienia do tworzenia maszyny Wirtualnej w ramach zasobów platformy Azure. *Współautor maszyny wirtualnej* wbudowana rola ma te uprawnienia, które obejmują:
        - Uprawnienie do tworzenia maszyny wirtualnej w wybranej grupie zasobów
        - Uprawnienie do tworzenia maszyny wirtualnej w wybranej sieci wirtualnej
        - Uprawnienie do zapisu na wybranym koncie magazynu

    2. Uprawnienie do zarządzania zadaniami usługi Azure Site Recovery. *Współautor usługi Site Recovery* rola ma wszystkie uprawnienia wymagane do zarządzania akcjami Site Recovery w magazynie usługi Recovery Services.

## <a name="prepare-the-source-vms"></a>Przygotowywanie źródłowych maszyn wirtualnych

1. Maszyny wirtualne powinny używać dysków zarządzanych, jeśli chcesz przenieść je w strefie dostępności przy użyciu usługi Site Recovery. Można przekonwertować istniejących maszyn wirtualnych Windows, który korzystać z dysków niezarządzanych do używania dysków zarządzanych. Wykonaj kroki opisane w temacie [konwertowania maszyny wirtualnej Windows z dysków niezarządzanych do usługi managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks). Upewnij się, że zestaw dostępności jest skonfigurowany jako *zarządzane*.
2. Sprawdź, czy na maszynach wirtualnych platformy Azure, które mają zostać przeniesione, są obecne wszystkie najnowsze certyfikaty główne. Jeśli brakuje najnowszych certyfikatów głównych kopiowanie danych do regionu docelowego nie można włączyć ze względu na ograniczenia zabezpieczeń.

3. Aby zainstalować wszystkie zaufane certyfikaty główne na maszynach wirtualnych z systemem Windows, zainstaluj wszystkie najnowsze aktualizacje systemu Windows. W środowisku bez połączenia postępuj zgodnie z standardowe Windows update i certyfikat aktualizacji procesów dla Twojej organizacji.

4. W przypadku maszyn wirtualnych systemu Linux postępuj zgodnie ze wskazówkami dystrybutora systemu Linux można pobrać najnowsze wymagane certyfikaty główne i listę odwołania certyfikatów na maszynie Wirtualnej.
5. Upewnij się, że korzystasz z uwierzytelniającego serwera proxy do kontrolowania łączności sieciowej dla maszyn wirtualnych, które chcesz przenieść.

6. Jeśli próbujesz przenieść maszynę Wirtualną nie ma dostępu do Internetu i używa serwera proxy zapory do kontrolowania dostępu wychodzącego, sprawdź wymagania przedstawione na [ konfigurowania połączenia sieciowego ruchu wychodzącego](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).

7. Określ układ sieci źródłowego i zasoby, które obecnie używana w celu weryfikacji, takich jak moduły równoważenia obciążenia, sieciowe grupy zabezpieczeń i publicznego adresu IP.

## <a name="prepare-the-target-region"></a>Przygotowywanie regionu docelowego

1. Sprawdź, czy Twoja subskrypcja platformy Azure umożliwia tworzenie maszyn wirtualnych w region docelowy używany na potrzeby odzyskiwania po awarii. Jeśli to konieczne, skontaktuj się z pomocą obsługuje włączyć Wymagany limit przydziału.

2. Upewnij się, że zasoby subskrypcji są wystarczające do obsługi maszyn wirtualnych o rozmiarach odpowiadających źródłowym maszynom wirtualnym. Jeśli usługa Site Recovery umożliwia kopiowanie danych do obiektu docelowego, wybiera ten sam lub najbardziej zbliżony rozmiar dla docelowej maszyny Wirtualnej.

3. Utwórz zasób docelowy dla każdego składnika identyfikowane w układzie sieci źródłowej. Ta akcja gwarantuje, że po jednorazową w regionie docelowym maszyn wirtualnych funkcji i funkcji, które masz w źródle.

    > [!NOTE]
    > Usługa Azure Site Recovery automatycznie wykrywa i tworzy konta magazynu i sieci wirtualnego po włączeniu replikacji dla źródłowej maszyny Wirtualnej. Można wstępnie utworzyć te zasoby i przypisać do maszyny Wirtualnej w ramach kroku Włączanie replikacji. Jednak do innych zasobów, jak wspomniano wcześniej później, należy ręcznie utworzyć je w regionie docelowym.

     Następujące dokumenty stwierdzić, jak utworzyć zasoby sieciowe najczęściej używane są istotne dla Ciebie oparte na konfiguracji źródłowej maszyny Wirtualnej.

    - [Sieciowe grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Moduły równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    - [Publiczny adres IP](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
   Dla innych składników sieciowych, można znaleźć sieci [dokumentacji](https://docs.microsoft.com/azure/#pivot=products&panel=network).

    > [!IMPORTANT]
    > Upewnij się, użyj modułu równoważenia obciążenia strefowo nadmiarowy w elemencie docelowym. Możesz dowiedzieć się więcej o [Balancer w warstwie standardowa i strefy dostępności](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones).

4. Ręcznie [Utwórz sieć nieprodukcyjnych](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) w regionie docelowym, jeśli chcesz przetestować konfigurację, zanim jednorazową w regionie docelowym. Zalecamy takie podejście, ponieważ powoduje on minimalny zakłócenia w środowisku produkcyjnym.

## <a name="enable-replication"></a>Włączanie replikacji
Następujące kroki przeprowadzą Cię, korzystając z usługi Azure Site Recovery można włączyć replikacji danych w regionie docelowym, zanim po pewnym czasie przenieść je w strefach dostępności.

> [!NOTE]
> Te kroki są dla pojedynczej maszyny Wirtualnej. Możesz rozszerzyć takie same na wielu maszynach wirtualnych. Przejdź do magazynu usługi Recovery Services, wybierz opcję **+ Replikuj**i wybrać odpowiednich maszyn wirtualnych ze sobą.

1. W witrynie Azure portal wybierz **maszyn wirtualnych**i wybierz maszynę Wirtualną, którą chcesz przenieść w strefach dostępności.
2. W **operacji**, wybierz opcję **odzyskiwania po awarii**.
3. W **Konfigurowanie odzyskiwania po awarii** > **region docelowy**, wybierz region docelowy, do którego będą replikowane. Upewnij się, ten region [obsługuje](https://docs.microsoft.com/azure/availability-zones/az-overview#regions-that-support-availability-zones) stref dostępności.

    ![Wybór region docelowy](media/azure-vms-to-zones/enable-rep-1.PNG)

4. Wybierz opcję **Dalej: Zaawansowane ustawienia**.
5. Wybierz odpowiednie wartości dla subskrypcji docelowej, docelowa grupa zasobów maszyny Wirtualnej i sieci wirtualnej.
6. W **dostępności** sekcji, wybierz strefę dostępności, do którego chcesz przenieść maszynę Wirtualną. 
   > [!NOTE]
   > Jeśli nie widzisz opcji dla zestawu dostępności lub stref dostępności, upewnij się, że [wymagania wstępne](#prepare-the-source-vms) są spełnione i [przygotowania](#prepare-the-source-vms) źródła maszyn wirtualnych zostało zakończone.
  
    ![Opcje dotyczące wybierania strefie dostępności](media/azure-vms-to-zones/enable-rep-2.PNG)

7. Wybierz pozycję **Włącz replikację**. Ta akcja uruchamia zadanie, aby włączyć replikację dla maszyny Wirtualnej.

## <a name="check-settings"></a>Sprawdź ustawienia

Po zakończeniu zadania replikacji można sprawdzić stan replikacji, zmodyfikować ustawienia replikacji i przetestować wdrożenie.

1. W menu maszyny Wirtualnej wybierz **odzyskiwania po awarii**.
2. Można sprawdzić kondycję replikacji, punkty odzyskiwania, które zostały utworzone i źródło i regiony docelowe na mapie.

   ![Stan replikacji](media/azure-to-azure-quickstart/replication-status.png)

## <a name="test-the-configuration"></a>Testowanie konfiguracji

1. W menu maszyny wirtualnej wybierz **odzyskiwania po awarii**.
2. Wybierz **testowy tryb Failover** ikony.
3. W **testowy tryb Failover**, wybierz punkt odzyskiwania na potrzeby pracy w trybie failover:

   - **Najnowszy przetworzony**: wprowadza maszynę wirtualną w tryb failover do najnowszego punktu odzyskiwania przetworzonego przez usługę Site Recovery. Wyświetlana jest sygnatura czasowa. Ta opcja zapewnia niską wartość celu czasu odzyskiwania (RTO, Recovery Time Objective), ponieważ nie trzeba poświęcać czasu na przetwarzanie danych.
   - **Najnowszy spójny na poziomie aplikacji**: ta opcja wprowadza wszystkie maszyny wirtualne w tryb failover do najnowszego spójnego na poziomie aplikacji punktu odzyskiwania. Wyświetlana jest sygnatura czasowa.
   - **Niestandardowy**: można wybrać dowolny punkt odzyskiwania.

3. Wybierz testową docelową sieć wirtualną platformy Azure, do której chcesz przenieść maszyny wirtualne platformy Azure, aby przetestować konfigurację. 

    > [!IMPORTANT]
    > Firma Microsoft zaleca używanie oddzielnej sieci maszyn wirtualnych platformy Azure dla niepowodzenia testu, a nie sieci produkcyjnych w regionie docelowym, do którego chcesz przenieść maszyny wirtualne.

4. Aby uruchomić testy przeniesienie, zaznacz **OK**. Aby śledzić postępy, wybierz maszynę Wirtualną i otwierając jej właściwości. Lub możesz wybrać **testowy tryb Failover** zadania w nazwa magazynu > **ustawienia** > **zadania** > **zadania usługi Site Recovery**.
5. Po zakończeniu trybu failover w obszarze Azure Portal > **Maszyny wirtualne** będzie widoczna replika maszyny wirtualnej platformy Azure. Upewnij się, że maszyna wirtualna jest uruchomiona, ma właściwy rozmiar i została połączona z odpowiednią siecią.
6. Jeśli chcesz usunąć maszyny Wirtualnej utworzona w ramach testowania przeniesienie, wybierz opcję **wyczyść test pracy awaryjnej** replikowanego elementu. W obszarze **Uwagi** zarejestruj i zapisz wszelkie obserwacje związane z testem.

## <a name="move-to-the-target-region-and-confirm"></a>Przenieś do regionu docelowego i upewnij się

1.  W menu maszyny wirtualnej wybierz **odzyskiwania po awarii**.
2. Wybierz **trybu Failover** ikony.
3. W obszarze **Tryb failover** wybierz pozycję **Najnowsze**. 
4. Wybierz pozycję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover**. Usługa Site Recovery próbuje zamknąć źródłową maszynę wirtualną przed wyzwoleniem trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Na stronie **Zadania** można śledzić postęp trybu failover. 
5. Po zakończeniu zadania Sprawdź, czy maszyna wirtualna jest wyświetlana w element docelowy region świadczenia usługi Azure, zgodnie z oczekiwaniami.
6. W obszarze **Replikowane elementy** kliknij prawym przyciskiem myszy maszynę wirtualną, a następnie kliknij pozycję **Zatwierdź**. Spowoduje to zakończenie procesu przenoszenia do regionu docelowego. Poczekaj na zakończenie zadania zatwierdzenia.

## <a name="discard-the-resource-in-the-source-region"></a>Odrzucanie zasobu w regionie źródłowym

Przejdź do maszyny Wirtualnej. Wybierz **Wyłącz replikację**. Ta akcja zatrzymuje proces kopiowania danych dla maszyny Wirtualnej.  

> [!IMPORTANT]
> Wykonaj poprzedni krok, aby uniknąć opłat za replikacji usługi Site Recovery po przeniesieniu. Ustawienia replikacji źródła są automatycznie czyszczone. Należy pamiętać, że rozszerzenie usługi Site Recovery zainstalowanego w ramach replikacji nie są usuwane i musi zostać usunięte ręcznie.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku większą dostępność maszyny Wirtualnej platformy Azure, przenosząc w zestawie dostępności lub stref dostępności. Teraz możesz ustawić odzyskiwania po awarii dla przeniesionego maszyny Wirtualnej.

> [!div class="nextstepaction"]
> [Konfigurowanie odzyskiwania po awarii po migracji](azure-to-azure-quickstart.md)


