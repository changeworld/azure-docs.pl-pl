---
title: Przenoszenie maszyn wirtualnych IaaS platformy Azure do innego regionu świadczenia usługi Azure przy użyciu usługi Azure Site Recovery | Microsoft Docs
description: Używanie usługi Azure Site Recovery do przenoszenia maszyn wirtualnych IaaS platformy Azure z jednego regionu świadczenia usługi Azure do innego.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: bbede01844f20c0240b154fd319b818a43463131
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824561"
---
# <a name="move-azure-vms-to-another-region"></a>Przenoszenie maszyn wirtualnych platformy Azure do innego regionu

Istnieją różne scenariusze, w których warto przenieść istniejące maszyny wirtualne IaaS platformy Azure z jednego regionu do innego, na przykład w celu zwiększenia niezawodności i dostępności istniejących maszyn wirtualnych czy zapewnienia lepszych możliwości zarządzania lub z przyczyn związanych z ładem, zgodnie ze przedstawionymi szczegółowymi informacjami. 

Ten samouczek przedstawia sposób przenoszenia maszyn wirtualnych platformy Azure do innego regionu przy użyciu usługi Azure Site Recovery. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * [Weryfikowanie wymagań wstępnych](#verify-prerequisites)
> * [Przygotowywanie źródłowych maszyn wirtualnych](#prepare-the-source-vms)
> * [Przygotowywanie regionu docelowego](#prepare-the-target-region)
> * [Kopiowanie danych do regionu docelowego](#copy-data-to-the-target-region)
> * [Testowanie konfiguracji](#test-the-configuration)
> * [Przenoszenie](#perform-the-move-to-the-target-region-and-confirm)
> * [Odrzucanie zasobu w regionie źródłowym](#discard-the-resource-in-the-source-region)

> [!IMPORTANT]
> W tym dokumencie opisano sposób przenoszenia maszyn wirtualnych platformy Azure z jednego regionu do innego bez ich modyfikowania. Jeśli wymagane jest zwiększenie dostępności infrastruktury przez przeniesienie maszyn wirtualnych do stref dostępności, skorzystaj z samouczka dostępnego tutaj.

## <a name="verify-prerequisites"></a>Weryfikowanie wymagań wstępnych

- Upewnij się, że w źródłowym regionie świadczenia usługi Azure, z którego chcesz przenieść, znajdują się maszyny wirtualne platformy Azure.
- Upewnij się, że wybrana [kombinacja regionu źródłowego i regionu docelowego jest obsługiwana](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support) oraz podejmij świadomą decyzję dotyczącą regionu docelowego.
- Przeanalizuj informacje o [składnikach i architekturze scenariusza](azure-to-azure-architecture.md).
- Zapoznaj się z [ograniczeniami i wymaganiami dotyczącymi pomocy technicznej](azure-to-azure-support-matrix.md).
- Zweryfikuj uprawnienia konta: Jeśli bezpłatne konto platformy Azure zostało właśnie utworzone, jesteś administratorem subskrypcji. Jeśli nie jesteś administratorem subskrypcji, współpracuj z administratorem w celu przypisania potrzebnych uprawnień. Aby włączyć replikację dla maszyny wirtualnej i skopiować dane do miejsca docelowego przy użyciu usługi Azure Site Recovery, musisz mieć następujące uprawnienia:

    1. Uprawnienia do tworzenia maszyny wirtualnej w ramach zasobów platformy Azure. Wbudowana rola „Współautor maszyny wirtualnej” ma te uprawnienia. Obejmują one:
        - Uprawnienie do tworzenia maszyny wirtualnej w wybranej grupie zasobów
        - Uprawnienie do tworzenia maszyny wirtualnej w wybranej sieci wirtualnej
        - Uprawnienie do zapisu na wybranym koncie magazynu

    2. Musisz mieć również uprawnienie do zarządzania operacjami usługi Azure Site Recovery. Rola „Współautor usługi Site Recovery” ma wszystkie uprawnienia wymagane do zarządzania operacjami usługi Site Recovery w magazynie usługi Recovery Services.

## <a name="prepare-the-source-vms"></a>Przygotowywanie źródłowych maszyn wirtualnych

1. Sprawdź, czy na maszynach wirtualnych platformy Azure, które mają zostać przeniesione, są obecne wszystkie najnowsze certyfikaty główne. W przypadku braku najnowszych certyfikatów głównych nie można włączyć kopiowania danych do regionu docelowego ze względu na ograniczenia dotyczące zabezpieczeń.

    - Aby zainstalować wszystkie zaufane certyfikaty główne na maszynach wirtualnych z systemem Windows, zainstaluj wszystkie najnowsze aktualizacje systemu Windows. W środowisku bez połączenia postępuj zgodnie ze standardową procedurą usługi Windows Update i procedurą aktualizacji certyfikatów, które są używane w danej organizacji.
    - Aby zainstalować najnowsze wymagane certyfikaty główne i listę odwołania certyfikatów na maszynach wirtualnych z systemem Linux, postępuj zgodnie ze wskazówkami dystrybutora systemu Linux.
2. Upewnij się, że nie używasz uwierzytelniania serwera proxy do kontrolowania łączności sieciowej dla maszyn wirtualnych, które chcesz przenieść.
3. Jeśli maszyna wirtualna, którą próbujesz przenieść, nie ma dostępu do Internetu i używa serwera proxy zapory do kontrolowania dostępu wychodzącego, sprawdź wymagania przedstawione [tutaj](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).
4. Zidentyfikuj i zanotuj układ sieci źródłowej i wszystkie zasoby, które są obecnie używane, m.in. moduły równoważenia obciążenia, sieciowe grupy zabezpieczeń, publiczny adres IP itd., w celu weryfikacji.

## <a name="prepare-the-target-region"></a>Przygotowywanie regionu docelowego

1. Sprawdź, czy Twoja subskrypcja platformy Azure umożliwia tworzenie maszyn wirtualnych w regionie docelowym używanym do odzyskiwania po awarii. W razie potrzeby skontaktuj się z pomocą techniczną, aby włączyć wymagany limit przydziału.

2. Upewnij się, że zasoby subskrypcji są wystarczające do obsługi maszyn wirtualnych o rozmiarach odpowiadających źródłowym maszynom wirtualnym. Jeśli dane są kopiowane do miejsca docelowego przy użyciu usługi Site Recovery, usługa sama wybiera ten sam lub najbardziej zbliżony rozmiar dla docelowej maszyny wirtualnej.

3. Upewnij się, że utworzono zasób docelowy dla każdego składnika zidentyfikowanego w układzie sieci źródłowej. Jest to niezbędne do zapewnienia, że po przeniesieniu do regionu docelowego maszyny wirtualne będą mieć wszystkie funkcje, które miały w regionie źródłowym.

    > [!NOTE]
    > Usługa Azure Site Recovery automatycznie wykrywa i tworzy sieć wirtualną oraz konto magazynu po włączeniu replikacji dla źródłowej maszyny wirtualnej. Można również wstępnie utworzyć te zasoby i przypisać je do maszyny wirtualnej w ramach kroku włączania replikacji. Jednak pozostałe zasoby należy utworzyć ręcznie w regionie docelowym, jak wspomniano poniżej.

     Skorzystaj z poniższych dokumentów, aby utworzyć odpowiednie najczęściej używane zasoby sieciowe, na podstawie konfiguracji źródłowej maszyny wirtualnej.

    - [Sieciowe grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Moduły równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    - [Publiczny adres IP](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
    Informacje na temat innych składników sieciowych zawiera [dokumentacja](https://docs.microsoft.com/azure/#pivot=products&panel=network) sieci. 

4. Ręcznie [utwórz sieć nieprodukcyjną](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) w regionie docelowym, jeśli chcesz przetestować konfigurację przed wykonaniem końcowej migracji do regionu docelowego. Ogranicza to do minimum zaburzenia działania środowiska produkcyjnego i jest zalecane.
    
## <a name="copy-data-to-the-target-region"></a>Kopiowanie danych do regionu docelowego
Poniżej przedstawiono procedurę kopiowania danych do regionu docelowego przy użyciu usługi Azure Site Recovery.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Magazyn można utworzyć w dowolnym regionie, z wyjątkiem regionu źródłowego.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) > **Recovery Services**.
2. Kliknij kolejno pozycje **Utwórz zasób** > **Narzędzia do zarządzania** > **Backup i Site Recovery**.
3. W polu **Nazwa** podaj przyjazną nazwę **ContosoVMVault**. Jeśli masz więcej niż jedną subskrypcję, wybierz odpowiednią z nich.
4. Utwórz grupę zasobów **ContosoRG**.
5. Określ region platformy Azure. Aby sprawdzić obsługiwane regiony, zobacz sekcję dotyczącą dostępności geograficznej w temacie [Szczegóły cennika usługi Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. W obszarze magazynów usługi Recovery Services kliknij pozycję **Przegląd** > **ConsotoVMVault** > **+Replikuj**.
7. W obszarze **Źródło** wybierz pozycję **Azure**.
8. W obszarze **Lokalizacja źródłowa** wybierz źródłowy region świadczenia usługi Azure, w którym są uruchomione maszyny wirtualne.
9. Wybierz model wdrażania usługi Resource Manager. Następnie wybierz pozycje **Subskrypcja źródłowa** i **Źródłowa grupa zasobów**.
10. Kliknij pozycję **OK**, aby zapisać ustawienia.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Włącz replikację dla maszyn wirtualnych platformy Azure i rozpocznij kopiowanie danych.

Usługa Site Recovery pobiera listę maszyn wirtualnych skojarzonych z subskrypcją i grupą zasobów.

1. W następnym kroku: Wybierz maszynę wirtualną, którą chcesz przenieść. Następnie kliknij przycisk **OK**.
3. W obszarze **Ustawienia** kliknij pozycję **Odzyskiwanie po awarii**.
4. W obszarze **Konfigurowanie odzyskiwania po awarii** > **Region docelowy** wybierz region docelowy, w którym maszyna będzie replikowana.
5. Wybrać można domyślne zasoby docelowe lub skorzystać z wstępnie utworzonych zasobów.
6. Kliknij pozycję **Włącz replikację**. Spowoduje to uruchomienie zadania włączającego replikację dla maszyny wirtualnej.

    ![włączanie replikacji](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="test-the-configuration"></a>Testowanie konfiguracji


1. Przejdź do magazynu, w obszarze **Ustawienia** > **Zreplikowane elementy** kliknij maszynę wirtualną, którą chcesz przenieść do regionu docelowego, a następnie kliknij ikonę **+Test pracy w trybie failover**.
2. W obszarze **Test pracy w trybie failover** wybierz punkt odzyskiwania, którego chcesz użyć podczas pracy w trybie failover:

   - **Najnowszy przetworzony**: wprowadza maszynę wirtualną w tryb failover do najnowszego punktu odzyskiwania przetworzonego przez usługę Site Recovery. Wyświetlana jest sygnatura czasowa. Ta opcja zapewnia niską wartość celu czasu odzyskiwania (RTO, Recovery Time Objective), ponieważ nie trzeba poświęcać czasu na przetwarzanie danych.
   - **Najnowszy spójny na poziomie aplikacji**: ta opcja wprowadza wszystkie maszyny wirtualne w tryb failover do najnowszego spójnego na poziomie aplikacji punktu odzyskiwania. Wyświetlana jest sygnatura czasowa.
   - **Niestandardowy**: można wybrać dowolny punkt odzyskiwania.

3. Wybierz docelową sieć wirtualną platformy Azure, do której chcesz przenieść maszyny wirtualne platformy Azure, aby przetestować konfigurację. 

> [!IMPORTANT]
> Zalecane jest użycie oddzielnej sieci maszyn wirtualnych platformy Azure na wypadek niepowodzenia testu, a nie sieci produkcyjnej w regionie docelowym, do którego mają zostać ostatecznie przeniesione maszyny wirtualne.

4. Aby rozpocząć testowanie przenoszenia, kliknij przycisk **OK**. Aby śledzić postępy, kliknij maszynę wirtualną w celu otwarcia jej właściwości. Możesz też kliknąć zadanie **Test pracy w trybie failover** w obszarze nazwy magazynu > **Ustawienia** > **Zadania** > **Zadania usługi Site Recovery**.
5. Po zakończeniu trybu failover w obszarze Azure Portal > **Maszyny wirtualne** będzie widoczna replika maszyny wirtualnej platformy Azure. Upewnij się, że maszyna wirtualna jest uruchomiona, ma właściwy rozmiar i została połączona z odpowiednią siecią.
6. Aby usunąć maszynę wirtualną utworzoną podczas testowania przeniesienia, kliknij pozycję **Wyczyść test pracy w trybie failover** replikowanego elementu. W obszarze **Uwagi** zarejestruj i zapisz wszelkie obserwacje związane z testem.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Wykonaj przeniesienie do regionu docelowego i potwierdź.

1.  Przejdź do magazynu, w obszarze **Ustawienia** > **Replikowane elementy** kliknij maszynę wirtualną, a następnie kliknij pozycję **Tryb failover**.
2. W obszarze **Tryb failover** wybierz pozycję **Najnowsze**. 
3. Wybierz pozycję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover**. Usługa Site Recovery próbuje zamknąć źródłową maszynę wirtualną przed wyzwoleniem trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Na stronie **Zadania** można śledzić postęp trybu failover. 
4. Po ukończeniu zadania sprawdź, czy maszyna wirtualna jest wyświetlana w regionie docelowym platformy Azure zgodnie z oczekiwaniami.
5. W obszarze **Replikowane elementy** kliknij prawym przyciskiem myszy maszynę wirtualną, a następnie kliknij pozycję **Zatwierdź**. Spowoduje to zakończenie procesu przenoszenia do regionu docelowego. Zaczekaj na ukończenie zadania zatwierdzania.

## <a name="discard-the-resource-in-the-source-region"></a>Odrzucanie zasobu w regionie źródłowym 

1. Przejdź do maszyny wirtualnej.  Kliknij pozycję **Wyłącz replikację**.  Spowoduje to zatrzymanie procesu kopiowania danych maszyny wirtualnej.  

> [!IMPORTANT]
> Wykonanie powyższego kroku jest ważne w celu uniknięcia naliczania opłat za replikację przy użyciu usługi Site Recovery po przeniesieniu.

Jeśli nie zamierzasz już używać zasobów źródłowych, przejdź do następnego zestawu kroków.

1. Usuń wszystkie odpowiednie zasoby sieciowe w regionie źródłowym wyszczególnione w kroku 4 w sekcji [Przygotowywanie źródłowych maszyn wirtualnych](#prepare-the-source-vms) 
2. Usuń odpowiednie konto magazynu w regionie źródłowym.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przeniesiono maszynę wirtualną platformy Azure do innego regionu świadczenia usługi Azure. Teraz możesz skonfigurować odzyskiwanie po awarii dla przeniesionej maszyny wirtualnej.

> [!div class="nextstepaction"]
> [Konfigurowanie odzyskiwania po awarii po migracji](azure-to-azure-quickstart.md)

