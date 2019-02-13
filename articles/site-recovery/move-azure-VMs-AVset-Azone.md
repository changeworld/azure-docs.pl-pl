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
ms.openlocfilehash: 6a731750da4edfb4a71c00156c5ff527dee30941
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824551"
---
# <a name="move-azure-vms-into-availability-zones"></a>Przenoszenie maszyn wirtualnych platformy Azure do stref dostępności
Strefy dostępności platformy Azure chronią aplikacje i dane przed awariami centrum danych. Każda strefa dostępności obejmuje co najmniej jedno centrum danych wyposażone w niezależne zasilanie i chłodzenie oraz niezależną sieć. W celu zapewnienia odporności istnieją co najmniej trzy osobne strefy we wszystkich włączonych regionach. Fizyczna separacja stref dostępności w ramach regionu chroni aplikacje i dane przed awariami centrum danych. Dzięki strefom dostępności platforma Azure oferuje najlepszą w branży umowę dotycząca poziomu usług (SLA) gwarantującą czas działania na poziomie 99,99%. Strefa dostępności jest obsługiwana w wybranych regionach wymienionych [tutaj](https://docs.microsoft.com/azure/availability-zones/az-overview#regions-that-support-availability-zones). 

W scenariuszu, w którym maszyny wirtualne zostały wdrożone jako pojedyncze wystąpienia w określonym regionie, można przenieść je do strefy dostępności przy użyciu usługi Azure Site Recovery, aby zwiększyć ich dostępność. Można to dodatkowo podzielić na następujące kategorie:

- Przenoszenie pojedynczych wystąpień maszyn wirtualnych do stref dostępności w regionie docelowym
- Przenoszenie maszyn wirtualnych w zestawie dostępności do stref dostępności w regionie docelowym

> [!IMPORTANT]
> Obecnie usługa Azure Site Recovery obsługuje przenoszenie maszyn wirtualnych z jednego regionu do innego i nie obsługuje przenoszenia w ramach regionu. 

## <a name="verify-prerequisites"></a>Weryfikowanie wymagań wstępnych

- Sprawdź, czy region docelowy [obsługuje strefę dostępności](https://docs.microsoft.com/azure/availability-zones/az-overview#regions-that-support-availability-zones)— upewnij się, że wybrana kombinacja [regionu źródłowego i regionu docelowego jest obsługiwana](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support) i podejmij świadomą decyzję dotyczącą regionu docelowego.
- Przeanalizuj informacje o [składnikach i architekturze scenariusza](azure-to-azure-architecture.md).
- Zapoznaj się z [ograniczeniami i wymaganiami dotyczącymi pomocy technicznej](azure-to-azure-support-matrix.md).
- Zweryfikuj uprawnienia konta: Jeśli bezpłatne konto platformy Azure zostało właśnie utworzone, jesteś administratorem subskrypcji. Jeśli nie jesteś administratorem subskrypcji, współpracuj z administratorem w celu przypisania potrzebnych uprawnień. Aby włączyć replikację dla maszyny wirtualnej i skopiować dane do miejsca docelowego przy użyciu usługi Azure Site Recovery, musisz mieć następujące uprawnienia:

    1. Uprawnienia do tworzenia maszyny wirtualnej w ramach zasobów platformy Azure. Wbudowana rola „Współautor maszyny wirtualnej” ma te uprawnienia. Obejmują one:
        - Uprawnienie do tworzenia maszyny wirtualnej w wybranej grupie zasobów
        - Uprawnienie do tworzenia maszyny wirtualnej w wybranej sieci wirtualnej
        - Uprawnienie do zapisu na wybranym koncie magazynu

    2. Musisz mieć również uprawnienie do zarządzania operacjami usługi Azure Site Recovery. Rola „Współautor usługi Site Recovery” ma wszystkie uprawnienia wymagane do zarządzania operacjami usługi Site Recovery w magazynie usługi Recovery Services.

## <a name="prepare-the-source-vms"></a>Przygotowywanie źródłowych maszyn wirtualnych

1. Maszyny wirtualne powinny korzystać z dysków zarządzanych, aby mogły być przeniesione do strefy dostępności przy użyciu usługi Site Recovery. Istniejące maszyny wirtualne z systemem Windows używające dysków niezarządzanych można przekonwertować tak, aby korzystały z dysków zarządzanych, wykonując kroki wymienione [tutaj](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks). W tym celu upewnij się, że zestaw dostępności jest skonfigurowany jako zarządzany. 
2. Sprawdź, czy na maszynach wirtualnych platformy Azure, które mają zostać przeniesione, są obecne wszystkie najnowsze certyfikaty główne. W przypadku braku najnowszych certyfikatów głównych nie można włączyć kopiowania danych do regionu docelowego ze względu na ograniczenia dotyczące zabezpieczeń.

3. Aby zainstalować wszystkie zaufane certyfikaty główne na maszynach wirtualnych z systemem Windows, zainstaluj wszystkie najnowsze aktualizacje systemu Windows. W środowisku bez połączenia postępuj zgodnie ze standardową procedurą usługi Windows Update i procedurą aktualizacji certyfikatów, które są używane w danej organizacji.

4. Aby zainstalować najnowsze wymagane certyfikaty główne i listę odwołania certyfikatów na maszynach wirtualnych z systemem Linux, postępuj zgodnie ze wskazówkami dystrybutora systemu Linux.
5. Upewnij się, że nie używasz uwierzytelniania serwera proxy do kontrolowania łączności sieciowej dla maszyn wirtualnych, które chcesz przenieść.

6. Jeśli maszyna wirtualna, którą próbujesz przenieść, nie ma dostępu do Internetu i używa serwera proxy zapory do kontrolowania dostępu wychodzącego, sprawdź wymagania przedstawione [tutaj](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).

7. Zidentyfikuj układ sieci źródłowej i wszystkie zasoby, które są obecnie używane, w tym moduły równoważenia obciążenia, sieciowe grupy zabezpieczeń, publiczny adres IP itd., w celu weryfikacji.

## <a name="prepare-the-target-region"></a>Przygotowywanie regionu docelowego

1. Sprawdź, czy Twoja subskrypcja platformy Azure umożliwia tworzenie maszyn wirtualnych w regionie docelowym używanym do odzyskiwania po awarii. W razie potrzeby skontaktuj się z pomocą techniczną, aby włączyć wymagany limit przydziału.

2. Upewnij się, że zasoby subskrypcji są wystarczające do obsługi maszyn wirtualnych o rozmiarach odpowiadających źródłowym maszynom wirtualnym. Jeśli dane są kopiowane do miejsca docelowego przy użyciu usługi Site Recovery, usługa sama wybiera ten sam lub najbardziej zbliżony rozmiar dla docelowej maszyny wirtualnej.

3. Upewnij się, że utworzono zasób docelowy dla każdego składnika zidentyfikowanego w układzie sieci źródłowej. Jest to niezbędne do zapewnienia, że po przeniesieniu do regionu docelowego maszyny wirtualne będą mieć wszystkie funkcje, które miały w regionie źródłowym.

    > [!NOTE]
    > Usługa Azure Site Recovery automatycznie wykrywa i tworzy sieć wirtualną oraz konto magazynu po włączeniu replikacji dla źródłowej maszyny wirtualnej. Można również wstępnie utworzyć te zasoby i przypisać je do maszyny wirtualnej w ramach kroku włączania replikacji. Jednak pozostałe zasoby należy utworzyć ręcznie w regionie docelowym, jak wspomniano poniżej.

     Skorzystaj z poniższych dokumentów, aby utworzyć odpowiednie najczęściej używane zasoby sieciowe, na podstawie konfiguracji źródłowej maszyny wirtualnej.

    - [Sieciowe grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Moduły równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials
        
     - [Publiczny adres IP](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
   Informacje na temat innych składników sieciowych zawiera [dokumentacja](https://docs.microsoft.com/azure/#pivot=products&panel=network) sieci. 

> [!IMPORTANT]
> Upewnij się, że w miejscu docelowym jest używany strefowo nadmiarowy moduł równoważenia obciążenia. Więcej informacji znajduje się [tutaj](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones).

4. Ręcznie [utwórz sieć nieprodukcyjną](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) w regionie docelowym, jeśli chcesz przetestować konfigurację przed wykonaniem końcowej migracji do regionu docelowego. Ogranicza to do minimum zaburzenia działania środowiska produkcyjnego i jest zalecane.


> [!NOTE]
> Poniższe kroki dotyczą pojedynczej maszyny wirtualnej. Można je rozszerzyć do wielu maszyn wirtualnych, przechodząc do magazynu usługi Recovery Services, klikając opcję + Replikuj i wybierając odpowiednie maszyny wirtualne jednocześnie.

## <a name="enable-replication"></a>Włączanie replikacji
Poniżej przedstawiono procedurę włączania replikacji danych w regionie docelowym przy użyciu usługi Azure Site Recovery przed ostatecznym przeniesieniem danych do stref dostępności.

1. W witrynie Azure Portal kliknij pozycję **Maszyny wirtualne** i wybierz maszynę wirtualną, którą chcesz przenieść do stref dostępności.
2. W obszarze **Operacje** kliknij pozycję **Odzyskiwanie po awarii**.
3. W obszarze **Konfigurowanie odzyskiwania po awarii** > **Region docelowy** wybierz region docelowy, w którym maszyna będzie replikowana. Upewnij się, że ten region [obsługuje](https://docs.microsoft.com/azure/availability-zones/az-overview#regions-that-support-availability-zones) strefy dostępności.
![enable-rep-1.PNG](media/azure-vms-to-zones/enable-rep-1.PNG)

1. Wybierz opcję **Dalej: Ustawienia zaawansowane**
2. Wybierz odpowiednie wartości docelowej subskrypcji, docelowej grupy zasobów maszyny wirtualnej i sieci wirtualnej.
3. W sekcji **Dostępność** wybierz strefę dostępności, do której chcesz przenieść maszynę wirtualną. 
> [!NOTE]
> Jeśli opcja zestawu dostępności lub strefy dostępności jest niewidoczna, upewnij się, że spełniono [wymagania wstępne](#prepare-the-source-vms) i ukończono [przygotowywanie](#prepare-the-source-vms) źródłowych maszyn wirtualnych.

   ![enable-rep-2.PNG](media/azure-vms-to-zones/enable-rep-2.PNG)

7. Kliknij opcję Włącz replikację. Spowoduje to uruchomienie zadania włączającego replikację dla maszyny wirtualnej.

## <a name="verify-settings"></a>Weryfikowanie ustawień

Po zakończeniu zadania replikacji można sprawdzić stan replikacji, zmodyfikować ustawienia replikacji i przetestować wdrożenie.

1. W menu maszyny wirtualnej kliknij pozycję **Odzyskiwanie po awarii**.
2. Można sprawdzić kondycję replikacji, utworzone punkty odzyskiwania oraz regiony źródłowy i docelowy na mapie.

   ![Stan replikacji](media/azure-to-azure-quickstart/replication-status.png)

## <a name="test-the-configuration"></a>Testowanie konfiguracji

1. W menu maszyny wirtualnej kliknij pozycję **Odzyskiwanie po awarii**.
2. Kliknij ikonę **Test pracy w trybie failover**.
3. W obszarze **Test pracy w trybie failover** wybierz punkt odzyskiwania, którego chcesz użyć podczas pracy w trybie failover:

   - **Najnowszy przetworzony**: wprowadza maszynę wirtualną w tryb failover do najnowszego punktu odzyskiwania przetworzonego przez usługę Site Recovery. Wyświetlana jest sygnatura czasowa. Ta opcja zapewnia niską wartość celu czasu odzyskiwania (RTO, Recovery Time Objective), ponieważ nie trzeba poświęcać czasu na przetwarzanie danych.
   - **Najnowszy spójny na poziomie aplikacji**: ta opcja wprowadza wszystkie maszyny wirtualne w tryb failover do najnowszego spójnego na poziomie aplikacji punktu odzyskiwania. Wyświetlana jest sygnatura czasowa.
   - **Niestandardowy**: można wybrać dowolny punkt odzyskiwania.

3. Wybierz testową docelową sieć wirtualną platformy Azure, do której chcesz przenieść maszyny wirtualne platformy Azure, aby przetestować konfigurację. 

> [!IMPORTANT]
> Zalecane jest użycie oddzielnej sieci maszyn wirtualnych platformy Azure na wypadek niepowodzenia testu, a nie sieci produkcyjnej w regionie docelowym, do którego mają zostać ostatecznie przeniesione maszyny wirtualne.

4. Aby rozpocząć testowanie przenoszenia, kliknij przycisk **OK**. Aby śledzić postępy, kliknij maszynę wirtualną w celu otwarcia jej właściwości. Możesz też kliknąć zadanie **Test pracy w trybie failover** w obszarze nazwy magazynu > **Ustawienia** > **Zadania** > **Zadania usługi Site Recovery**.
5. Po zakończeniu trybu failover w obszarze Azure Portal > **Maszyny wirtualne** będzie widoczna replika maszyny wirtualnej platformy Azure. Upewnij się, że maszyna wirtualna jest uruchomiona, ma właściwy rozmiar i została połączona z odpowiednią siecią.
6. Aby usunąć maszynę wirtualną utworzoną podczas testowania przeniesienia, kliknij pozycję **Wyczyść test pracy w trybie failover** replikowanego elementu. W obszarze **Uwagi** zarejestruj i zapisz wszelkie obserwacje związane z testem.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Wykonaj przeniesienie do regionu docelowego i potwierdź.

1.  W menu maszyny wirtualnej kliknij pozycję **Odzyskiwanie po awarii**.
2. Kliknij ikonę **Tryb failover**.
3. W obszarze **Tryb failover** wybierz pozycję **Najnowsze**. 
4. Wybierz pozycję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover**. Usługa Site Recovery próbuje zamknąć źródłową maszynę wirtualną przed wyzwoleniem trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Na stronie **Zadania** można śledzić postęp trybu failover. 
5. Po ukończeniu zadania sprawdź, czy maszyna wirtualna jest wyświetlana w regionie docelowym platformy Azure zgodnie z oczekiwaniami.
6. W obszarze **Replikowane elementy** kliknij prawym przyciskiem myszy maszynę wirtualną, a następnie kliknij pozycję **Zatwierdź**. Spowoduje to zakończenie procesu przenoszenia do regionu docelowego. Zaczekaj na ukończenie zadania zatwierdzania.

## <a name="discard-the-resource-in-the-source-region"></a>Odrzucanie zasobu w regionie źródłowym 

1. Przejdź do maszyny wirtualnej.  Kliknij pozycję **Wyłącz replikację**.  Spowoduje to zatrzymanie procesu kopiowania danych maszyny wirtualnej.  

> [!IMPORTANT]
> Wykonanie powyższego kroku jest ważne w celu uniknięcia naliczania opłat za replikację przy użyciu usługi Site Recovery po przeniesieniu. Ustawienia replikacji źródła są automatycznie czyszczone. Pamiętaj, że rozszerzenie usługi Site Recovery, które jest instalowane w ramach replikacji, nie jest usuwane i należy je usunąć ręcznie. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku zwiększono dostępność maszyny wirtualnej platformy Azure przez przeniesienie jej do zestawu dostępności lub strefy dostępności. Teraz możesz skonfigurować odzyskiwanie po awarii dla przeniesionej maszyny wirtualnej.

> [!div class="nextstepaction"]
> [Konfigurowanie odzyskiwania po awarii po migracji](azure-to-azure-quickstart.md)


