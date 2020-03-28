---
title: Przenoszenie maszyn wirtualnych do regionu platformy Azure ze strefami dostępności przy użyciu usługi Azure Site Recovery
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 5728ce7125695b191de4f91d5bd9003384f428a7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78298312"
---
# <a name="move-azure-vms-into-availability-zones"></a>Przenoszenie maszyn wirtualnych platformy Azure do stref dostępności
Strefy dostępności na platformie Azure pomagają chronić aplikacje i dane przed awariami centrów danych. Każda strefa dostępności składa się z co najmniej jednego centrum danych wyposażonego w niezależne zasilanie, chłodzenie i sieć. W celu zapewnienia odporności istnieją co najmniej trzy osobne strefy we wszystkich włączonych regionach. Fizyczne oddzielenie stref dostępności w regionie pomaga chronić aplikacje i dane przed awariami centrów danych. Dzięki strefom dostępności platforma Azure oferuje umowę dotyczącą poziomu usług (SLA) w wysokości 99,99% dla czasu pracy maszyn wirtualnych (VM). Strefy dostępności są obsługiwane w wybranych regionach, jak wspomniano w [Co to są strefy dostępności na platformie Azure?](https://docs.microsoft.com/azure/availability-zones/az-overview#services-support-by-region).

W scenariuszu, w którym maszyny wirtualne są wdrażane jako *pojedyncze wystąpienie* w określonym regionie i chcesz poprawić swoją dostępność, przenosząc te maszyny wirtualne do strefy dostępności, można to zrobić za pomocą usługi Azure Site Recovery. Tę akcję można dodatkowo podzielić na:

- Przenoszenie maszyn wirtualnych z jednym wystąpieniem do stref dostępności w regionie docelowym
- Przenoszenie maszyn wirtualnych w zestawie dostępności do stref dostępności w regionie docelowym

> [!IMPORTANT]
> Obecnie usługa Azure Site Recovery obsługuje przenoszenie maszyn wirtualnych z jednego regionu do drugiego, ale nie obsługuje przenoszenia w obrębie regionu.

## <a name="check-prerequisites"></a>Sprawdzanie wymagań wstępnych

- Sprawdź, czy region docelowy obsługuje [strefy dostępności](https://docs.microsoft.com/azure/availability-zones/az-overview#services-support-by-region). Sprawdź, czy jest [obsługiwana kombinacja regionu źródłowego/regionu docelowego.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support) Podejmij świadomą decyzję w sprawie regionu docelowego.
- Przeanalizuj informacje o [składnikach i architekturze scenariusza](azure-to-azure-architecture.md).
- Zapoznaj się z [ograniczeniami i wymaganiami dotyczącymi pomocy technicznej](azure-to-azure-support-matrix.md).
- Sprawdź uprawnienia konta. Jeśli właśnie utworzono bezpłatne konto platformy Azure, jesteś administratorem subskrypcji. Jeśli nie jesteś administratorem subskrypcji, pracuj z administratorem, aby przypisać potrzebne uprawnienia. Aby włączyć replikację maszyny Wirtualnej i ostatecznie skopiować dane do obiektu docelowego przy użyciu usługi Azure Site Recovery, musisz mieć:

    1. Uprawnienie do tworzenia maszyny Wirtualnej w zasobach platformy Azure. Wbudowana rola *współautora maszyny wirtualnej* ma następujące uprawnienia, które obejmują:
        - Uprawnienie do tworzenia maszyny wirtualnej w wybranej grupie zasobów
        - Uprawnienie do tworzenia maszyny wirtualnej w wybranej sieci wirtualnej
        - Uprawnienie do zapisu na wybranym koncie magazynu

    2. Uprawnienie do zarządzania zadaniami usługi Azure Site Recovery. Rola *Współautor odzyskiwania lokacji* ma wszystkie uprawnienia wymagane do zarządzania akcjami odzyskiwania witryny w magazynie usług odzyskiwania.

## <a name="prepare-the-source-vms"></a>Przygotowywanie źródłowych maszyn wirtualnych

1. Maszyny wirtualne powinny używać dysków zarządzanych, jeśli chcesz przenieść je do strefy dostępności przy użyciu funkcji Odzyskiwanie witryny. Można przekonwertować istniejące maszyny wirtualne z systemem Windows, które używają dysków niezarządzanych do używania dysków zarządzanych. Wykonaj kroki opisane w [punkcie Konwertuj maszynę wirtualną systemu Windows z dysków niezarządzanych na dyski zarządzane](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks). Upewnij się, że zestaw dostępności jest skonfigurowany jako *zarządzany*.
2. Sprawdź, czy na maszynach wirtualnych platformy Azure, które mają zostać przeniesione, są obecne wszystkie najnowsze certyfikaty główne. Jeśli najnowszych certyfikatów głównych nie ma, nie można włączyć kopiowania danych do regionu docelowego z powodu ograniczeń zabezpieczeń.

3. Aby zainstalować wszystkie zaufane certyfikaty główne na maszynach wirtualnych z systemem Windows, zainstaluj wszystkie najnowsze aktualizacje systemu Windows. W środowisku rozłączanym postępuj zgodnie ze standardowymi procesami aktualizacji systemu Windows i aktualizacji certyfikatów dla organizacji.

4. W przypadku maszyn wirtualnych z systemem Linux postępuj zgodnie ze wskazówkami dystrybutora systemu Linux, aby uzyskać najnowsze zaufane certyfikaty główne i listę odwołania certyfikatów na maszynie wirtualnej.
5. Upewnij się, że nie używasz serwera proxy uwierzytelniania do kontrolowania łączności sieciowej dla maszyn wirtualnych, które chcesz przenieść.

6. Jeśli maszyna wirtualna, którą próbujesz przenieść, nie ma dostępu do Internetu i używa serwera proxy zapory do kontrolowania dostępu wychodzącego, sprawdź wymagania w [temacie Konfigurowanie wychodzącej łączności sieciowej](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms).

7. Zidentyfikuj układ sieci źródłowej i zasoby, które są obecnie używane do weryfikacji, w tym moduły równoważenia obciążenia, sieciowe sieciowe i publiczny adres IP.

## <a name="prepare-the-target-region"></a>Przygotowywanie regionu docelowego

1. Sprawdź, czy subskrypcja platformy Azure umożliwia tworzenie maszyn wirtualnych w regionie docelowym używanym do odzyskiwania po awarii. W razie potrzeby skontaktuj się z pomocą techniczną, aby włączyć wymagany przydział.

2. Upewnij się, że zasoby subskrypcji są wystarczające do obsługi maszyn wirtualnych o rozmiarach odpowiadających źródłowym maszynom wirtualnym. Jeśli używasz site recovery do kopiowania danych do obiektu docelowego, wybiera ten sam rozmiar lub najbliższy możliwy rozmiar dla docelowej maszyny Wirtualnej.

3. Utwórz zasób docelowy dla każdego składnika zidentyfikowanego w układzie sieci źródłowej. Ta akcja gwarantuje, że po przecięciu do regionu docelowego maszyny wirtualne mają wszystkie funkcje i funkcje, które miały w źródle.

    > [!NOTE]
    > Usługa Azure Site Recovery automatycznie odnajduje i tworzy konto sieci wirtualnej i magazynu po włączeniu replikacji dla źródłowej maszyny Wirtualnej. Można również wstępnie utworzyć te zasoby i przypisać do maszyny Wirtualnej w ramach kroku włącz replikację. Ale dla innych zasobów, jak wspomniano później, należy ręcznie utworzyć je w regionie docelowym.

     W poniższych dokumentach opisano sposób tworzenia najczęściej używanych zasobów sieciowych, które są istotne dla ciebie, na podstawie źródłowej konfiguracji maszyny Wirtualnej.

    - [Grupy zabezpieczeń sieci](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Moduły równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer)
    - [Publiczny adres IP](../virtual-network/virtual-network-public-ip-address.md)
    
   W przypadku innych składników sieci można znaleźć w [dokumentacji](https://docs.microsoft.com/azure/?pivot=products&panel=network)sieci .

    > [!IMPORTANT]
    > Upewnij się, że używasz nadmiarowego modułu równoważenia obciążenia strefowego w docelowych. Więcej informacji można przeczytać w : [Standard Load Balancer and Availability Zones](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones).

4. Ręcznie [utwórz sieć nieprodukcową](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) w regionie docelowym, jeśli chcesz przetestować konfigurację przed wycięciem do regionu docelowego. Zaleca się takie podejście, ponieważ powoduje minimalne zakłócenia w środowisku produkcyjnym.

## <a name="enable-replication"></a>Włączanie replikacji
Poniższe kroki poprowadzą Cię podczas korzystania z usługi Azure Site Recovery, aby włączyć replikację danych do regionu docelowego, zanim ostatecznie przeniesiesz je do stref dostępności.

> [!NOTE]
> Te kroki są dla pojedynczej maszyny Wirtualnej. Można rozszerzyć to samo na wiele maszyn wirtualnych. Przejdź do magazynu usług odzyskiwania, wybierz **+ Replikuj**i wybierz odpowiednie maszyny wirtualne razem.

1. W witrynie Azure portal wybierz pozycję **Maszyny wirtualne**i wybierz maszynę wirtualną, którą chcesz przenieść do stref dostępności.
2. W obszarze **Operacja** wybierz pozycję **Odzyskiwanie po awarii**.
3. W **obszarze Konfigurowanie** > **regionu docelowego**odzyskiwania po awarii wybierz region docelowy, do którego będzie można replikować. Upewnij się, że ten region [obsługuje](https://docs.microsoft.com/azure/availability-zones/az-overview#services-support-by-region) strefy dostępności.

    ![Wybór regionu docelowego](media/azure-vms-to-zones/enable-rep-1.PNG)

4. Wybierz **dalej: Ustawienia zaawansowane**.
5. Wybierz odpowiednie wartości dla subskrypcji docelowej, docelowej grupy zasobów maszyny Wirtualnej i sieci wirtualnej.
6. W sekcji **Dostępność** wybierz strefę dostępności, do której chcesz przenieść maszynę wirtualną. 
   > [!NOTE]
   > Jeśli nie widzisz opcji zestawu dostępności lub strefy dostępności, upewnij się, że [wymagania wstępne](#prepare-the-source-vms) są spełnione i [przygotowanie](#prepare-the-source-vms) źródłowych maszyn wirtualnych jest zakończone.
  
    ![Wybór strefy dostępności](media/azure-vms-to-zones/enable-rep-2.PNG)

7. Wybierz pozycję **Włącz replikację**. Ta akcja uruchamia zadanie, aby włączyć replikację maszyny Wirtualnej.

## <a name="check-settings"></a>Sprawdź ustawienia

Po zakończeniu zadania replikacji można sprawdzić stan replikacji, zmodyfikować ustawienia replikacji i przetestować wdrożenie.

1. W menu maszyny wirtualnej wybierz pozycję **Odzyskiwanie po awarii**.
2. Można sprawdzić kondycję replikacji, punkty odzyskiwania, które zostały utworzone i źródło oraz regiony docelowe na mapie.

   ![Stan replikacji](media/azure-to-azure-quickstart/replication-status.png)

## <a name="test-the-configuration"></a>Testowanie konfiguracji

1. W menu maszyny wirtualnej wybierz polecenie **Odzyskiwanie po awarii**.
2. Wybierz ikonę **Testowanie trybu failover.**
3. W **testowym przewijaniu awaryjnego**wybierz punkt odzyskiwania, który ma być używany do pracy awaryjnej:

   - **Najnowszy przetworzony**: wprowadza maszynę wirtualną w tryb failover do najnowszego punktu odzyskiwania przetworzonego przez usługę Site Recovery. Wyświetlana jest sygnatura czasowa. Ta opcja zapewnia niską wartość celu czasu odzyskiwania (RTO, Recovery Time Objective), ponieważ nie trzeba poświęcać czasu na przetwarzanie danych.
   - **Najnowszy spójny na poziomie aplikacji**: ta opcja wprowadza wszystkie maszyny wirtualne w tryb failover do najnowszego spójnego na poziomie aplikacji punktu odzyskiwania. Wyświetlana jest sygnatura czasowa.
   - **Niestandardowy**: można wybrać dowolny punkt odzyskiwania.

3. Wybierz testową docelową sieć wirtualną platformy Azure, do której chcesz przenieść maszyny wirtualne platformy Azure, aby przetestować konfigurację. 

    > [!IMPORTANT]
    > Zaleca się użycie oddzielnej sieci maszyn wirtualnych platformy Azure dla niepowodzenia testu, a nie sieci produkcyjnej w regionie docelowym, do którego chcesz przenieść maszyny wirtualne.

4. Aby rozpocząć testowanie ruchu, wybierz **przycisk OK**. Aby śledzić postęp, wybierz maszynę wirtualną, aby otworzyć jej właściwości. Można też wybrać zadanie **testowania trybu failover** w nazwie magazynu > zadania odzyskiwania**Jobs** > **witryny** **Zadania ustawień** > .
5. Po zakończeniu trybu failover w obszarze Azure Portal > **Maszyny wirtualne** będzie widoczna replika maszyny wirtualnej platformy Azure. Upewnij się, że maszyna wirtualna jest uruchomiona, ma właściwy rozmiar i została połączona z odpowiednią siecią.
6. Jeśli chcesz usunąć maszynę wirtualną utworzoną w ramach testowania przeniesienia, wybierz opcję **Przemorżanie testowe oczyszczania** w elementów replikowanych. W obszarze **Uwagi** zarejestruj i zapisz wszelkie obserwacje związane z testem.

## <a name="move-to-the-target-region-and-confirm"></a>Przejdź do regionu docelowego i potwierdź

1.  W menu maszyny wirtualnej wybierz polecenie **Odzyskiwanie po awarii**.
2. Wybierz ikonę **Trybu failover.**
3. W obszarze **Tryb failover** wybierz pozycję **Najnowsze**. 
4. Wybierz pozycję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover**. Usługa Site Recovery próbuje zamknąć źródłową maszynę wirtualną przed wyzwoleniem trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Postęp pracy awaryjnej można śledzić na stronie **Zadania.** 
5. Po zakończeniu zadania sprawdź, czy maszyna wirtualna jest wyświetlana w docelowym regionie platformy Azure zgodnie z oczekiwaniami.
6. W obszarze **Replikowane elementy** kliknij prawym przyciskiem myszy maszynę wirtualną, a następnie kliknij pozycję **Zatwierdź**. Spowoduje to zakończenie procesu przenoszenia do regionu docelowego. Poczekaj, aż zadanie zatwierdzania zostanie zakończone.

## <a name="discard-the-resource-in-the-source-region"></a>Odrzucanie zasobu w regionie źródłowym

Przejdź do maszyny Wirtualnej. Wybierz **pozycję Wyłącz replikację**. Ta akcja zatrzymuje proces kopiowania danych dla maszyny Wirtualnej.  

> [!IMPORTANT]
> Wykonaj poprzedni krok, aby uniknąć naliczania opłat za replikację usługi Site Recovery po przeprowadzce. Ustawienia replikacji źródła są automatycznie czyszczone. Należy zauważyć, że rozszerzenie odzyskiwania witryny zainstalowane jako część replikacji nie jest usuwane i musi zostać usunięte ręcznie.

## <a name="next-steps"></a>Następne kroki

W tym samouczku zwiększono dostępność maszyny Wirtualnej platformy Azure, przechodząc do zestawu dostępności lub strefy dostępności. Teraz można ustawić odzyskiwanie po awarii dla przeniesionej maszyny Wirtualnej.

> [!div class="nextstepaction"]
> [Konfigurowanie odzyskiwania po awarii po migracji](azure-to-azure-quickstart.md)


