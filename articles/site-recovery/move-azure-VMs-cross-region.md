---
title: Przenoszenie maszyn wirtualnych platformy Azure do innego regionu za pomocą usługi Azure Site Recovery
description: Używanie usługi Azure Site Recovery do przenoszenia maszyn wirtualnych IaaS platformy Azure z jednego regionu świadczenia usługi Azure do innego.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: dc37cb6fa05a2be56de7bf5536d7274190257d85
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78303923"
---
# <a name="move-azure-vms-to-another-azure-region"></a>Przenoszenie maszyn wirtualnych platformy Azure do innego regionu platformy Azure

Można przenieść infrastruktury platformy Azure jako usługi (IaaS) maszyn wirtualnych z jednego regionu do drugiego w celu zwiększenia niezawodności, dostępności, zarządzania lub nadzoru. W tym samouczku pokazano, jak przenieść maszyny wirtualne do innego regionu przy użyciu usługi Azure Site Recovery. Omawiane tematy:

> [!div class="checklist"]
> * Weryfikowanie wymagań wstępnych
> * Przygotowywanie źródłowych maszyn wirtualnych
> * Przygotowywanie regionu docelowego
> * Kopiowanie danych do regionu docelowego
> * Testowanie konfiguracji
> * Przenoszenie
> * Wyrzucanie zasobów z regionu źródłowego


> [!IMPORTANT]
> W tym artykule opisano sposób przenoszenia maszyn wirtualnych platformy Azure z jednego regionu do *drugiego.* Jeśli twoim celem jest zwiększenie dostępności infrastruktury poprzez przeniesienie maszyn wirtualnych do stref dostępności, zobacz [Przenoszenie maszyn wirtualnych platformy Azure do stref dostępności.](move-azure-vms-avset-azone.md)

## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że masz maszyny wirtualne platformy Azure w źródłowym regionie platformy Azure, *z*którego chcesz przenieść.
- Sprawdź, czy jest [obsługiwana wybrana kombinacja regionu docelowego źródła](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)i starannie wybierz region docelowy.
- Przeanalizuj informacje o [składnikach i architekturze scenariusza](azure-to-azure-architecture.md).
- Zapoznaj się z [ograniczeniami i wymaganiami dotyczącymi pomocy technicznej](azure-to-azure-support-matrix.md).
- Weryfikuj uprawnienia konta. Jeśli właśnie utworzono bezpłatne konto platformy Azure, *jesteś* administratorem subskrypcji. Jeśli nie jesteś administratorem, współpracuj z administratorem, aby uzyskać potrzebne uprawnienia:
  -  Aby włączyć replikację maszyny Wirtualnej i skopiować dane do obiektu docelowego przy użyciu usługi Site Recovery, musisz mieć uprawnienia do tworzenia maszyny Wirtualnej w zasobach platformy Azure. Wbudowana rola „Współautor maszyny wirtualnej” ma te uprawnienia. Za pomocą uprawnień można:
        - Tworzenie maszyny wirtualnej w wybranej grupie zasobów.
        - Tworzenie maszyny wirtualnej w wybranej sieci wirtualnej.
        - Zapis na wybranym koncie magazynu.

  - Potrzebne są również uprawnienia do zarządzania operacjami odzyskiwania witryny. Rola współautora odzyskiwania lokacji ma wszystkie uprawnienia wymagane do zarządzania operacjami odzyskiwania witryny w magazynie usług Azure Recovery Services.

## <a name="prepare-the-source-vms"></a>Przygotowywanie źródłowych maszyn wirtualnych

1. Sprawdź, czy maszyny wirtualne platformy Azure, które zamierzasz przenieść, mają najnowsze certyfikaty główne. Jeśli tak nie jest, nie można włączyć kopiowania danych do regionu docelowego z powodu ograniczeń zabezpieczeń.

    - W przypadku maszyn wirtualnych z systemem Windows zainstaluj najnowsze aktualizacje systemu Windows, aby wszystkie zaufane certyfikaty główne były dostępne na komputerze. W środowisku rozłączanym postępuj zgodnie ze standardowymi procesami usługi Windows Update i aktualizacji certyfikatów w organizacji.
    - W przypadku maszyn wirtualnych z systemem Linux postępuj zgodnie ze wskazówkami dystrybutora systemu Linux, aby uzyskać najnowsze zaufane certyfikaty główne i listę odwołania certyfikatów.
2. Upewnij się, że nie używasz serwera proxy uwierzytelniania do kontrolowania łączności sieciowej dla maszyn wirtualnych, które mają być przeniesione.
3. Jeśli maszyna wirtualna, którą chcesz przenieść, nie ma dostępu do Internetu i używa serwera proxy zapory do kontrolowania dostępu wychodzącego, sprawdź [wymagania](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms).
4. Dokumentuj układ sieci źródłowej i wszystkie zasoby, których aktualnie używasz, w tym (ale nie wyłącznie) moduły równoważenia obciążenia, sieciowe grupy zabezpieczeń i publiczne adresy IP do weryfikacji.

## <a name="prepare-the-target-region"></a>Przygotowywanie regionu docelowego

1. W ramach subskrypcji platformy Azure sprawdź, czy można tworzyć maszyny wirtualne w regionie docelowym, który jest używany do odzyskiwania po awarii. Skontaktuj się z pomocą techniczną, aby w razie potrzeby włączyć wymagany przydział.

2. Upewnij się, że subskrypcja ma wystarczającą ilość zasobów do obsługi źródłowych maszyn wirtualnych. Jeśli używasz usługi Site Recovery do kopiowania danych do obiektu docelowego, wybiera ten sam rozmiar lub najbliższy dostępny rozmiar dla docelowych maszyn wirtualnych.

3. Upewnij się, że tworzysz zasób docelowy dla każdego składnika zidentyfikowanego w układzie sieci źródłowej. Gwarantuje to, że maszyny wirtualne będą miały wszystkie funkcje i funkcje w regionie docelowym, który mieli w regionie źródłowym.

   Usługa Azure Site Recovery automatycznie odnajduje i tworzy konto sieci wirtualnej i magazynu po włączeniu replikacji dla źródłowej maszyny Wirtualnej. Można również wstępnie utworzyć te zasoby i przypisać je do maszyny Wirtualnej w ramach kroku włącz replikację. Należy jednak ręcznie utworzyć inne zasoby w regionie docelowym. Aby utworzyć najczęściej używane zasoby sieciowe na podstawie źródłowej konfiguracji maszyny Wirtualnej, należy zapoznać się z następującymi dokumentami:

   - [Grupy zabezpieczeń sieci](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
   - [Moduły równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer)
   - [Publiczny adres IP](../virtual-network/virtual-network-public-ip-address.md)
    
   Aby uzyskać inne składniki sieci, zobacz [dokumentację sieci platformy Azure](https://docs.microsoft.com/azure/?pivot=products&panel=network). 

4. Aby przetestować konfigurację przed wykonaniem przeniesienia, ręcznie [utwórz sieć nieprodukcową](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) w regionie docelowym. Testowanie konfiguracji powoduje minimalne zakłócenia w środowisku produkcyjnym i zalecamy.
    
## <a name="copy-data-to-the-target-region"></a>Kopiowanie danych do regionu docelowego
Poniższe kroki używają usługi Azure Site Recovery do kopiowania danych do regionu docelowego.

### <a name="create-the-vault-in-any-region-except-the-source"></a>Tworzenie przechowalni w dowolnym regionie z wyjątkiem źródła

1. Zaloguj się do**usługi odzyskiwania** [portalu](https://portal.azure.com) > Azure .
2. Wybierz **pozycję Utwórz kopię zapasową** > narzędzi do > **zarządzania zasobami****i odzyskiwanie witryny**.
3. W obszarze **Nazwa**należy określić przyjazną nazwę **ContosoVMVault**. Jeśli masz więcej niż jedną subskrypcję, wybierz jedną z nich.
4. Utwórz grupę zasobów **ContosoRG**.
5. Określ region platformy Azure. Aby sprawdzić obsługiwane regiony, zobacz [Szczegóły cen usługi Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. W przypadku magazynów usług odzyskiwania wybierz **pozycję Przegląd** > **ConsotoVMVault** > **+Replikacja**.
7. W przypadku **opcji Źródło**wybierz pozycję **Azure**.
8. W przypadku **lokalizacji źródłowej**wybierz źródłowy region platformy Azure, w którym są aktualnie uruchomione maszyny wirtualne.
9. Wybierz model wdrażania usługi Azure Resource Manager. Następnie wybierz **grupę zasobów** **Subskrypcja źródło** i Źródło .
10. Wybierz **przycisk OK,** aby zapisać ustawienia.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Włącz replikację dla maszyn wirtualnych platformy Azure i rozpocznij kopiowanie danych

Usługa Site Recovery pobiera listę maszyn wirtualnych skojarzonych z grupą subskrypcji i zasobów.

1. Wybierz maszynę wirtualną, którą chcesz przenieść, a następnie wybierz przycisk **OK**.
2. W obszarze **Ustawienia**wybierz pozycję **Odzyskiwanie po awarii**.
3. W przypadku **konfigurowania** > **regionu docelowego**odzyskiwania po awarii wybierz region docelowy, do którego replikujesz.
4. Wybierz opcję użycia domyślnych zasobów docelowych lub tych, które zostały wstępnie utworzone.
5. Wybierz **włącz replikację,** aby rozpocząć zadanie.

   ![Włączanie replikacji](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="test-the-configuration"></a>Testowanie konfiguracji


1. Przejdź do skarbca. W **ustawieniach** > **replikowane elementy**wybierz maszynę wirtualną, którą chcesz przenieść do regionu docelowego. Następnie wybierz opcję **Test failover**.
2. W **testowym przewijaniu awaryjnego**wybierz punkt odzyskiwania, który ma być używany do pracy awaryjnej:

   - **Najnowszy przetworzony**: wprowadza maszynę wirtualną w tryb failover do najnowszego punktu odzyskiwania przetworzonego przez usługę Site Recovery. Wyświetlana jest sygnatura czasowa. Nie spędza się czasu na przetwarzaniu danych, więc ta opcja zapewnia cel czasu odzyskiwania (RTO).
   - **Najnowsze spójne z aplikacjami:** Kończy się niepowodzeniem wszystkich maszyn wirtualnych do najnowszego punktu odzyskiwania spójnego z aplikacjami. Wyświetlana jest sygnatura czasowa.
   - **Niestandardowy**: można wybrać dowolny punkt odzyskiwania.

3. Wybierz docelową sieć wirtualną platformy Azure, do której chcesz przenieść maszyny wirtualne platformy Azure, aby przetestować konfigurację.

   > [!IMPORTANT]
   > Zaleca się użycie oddzielnej sieci maszyn wirtualnych platformy Azure dla testowania trybu failover, a nie sieci produkcyjnej w regionie docelowym.

4. Aby rozpocząć testowanie ruchu, wybierz **przycisk OK**. Aby śledzić postęp, wybierz maszynę wirtualną, aby otworzyć jej **właściwości.** Możesz też wybrać zadanie **testowania trybu failover** w magazynie. Następnie wybierz pozycję **Ustawienia** > zadania odzyskiwania**witryny Zadania zadań** > **.**
5. Po zakończeniu trybu failover w obszarze Azure Portal > **Maszyny wirtualne** będzie widoczna replika maszyny wirtualnej platformy Azure. Upewnij się, że maszyna wirtualna jest uruchomiona, ma właściwy rozmiar i została połączona z odpowiednią siecią.
6. Aby usunąć maszynę wirtualną utworzoną do testowania, wybierz opcję **Oczyszczanie testu trybu failover** na zreplikowanym elemencie. Z **notatek**zapisuj i zapisuj wszelkie obserwacje związane z testem.

## <a name="perform-the-move-and-confirm"></a>Wykonaj ruch i potwierdź

1. Przejdź do przechowalni w obszarze**Elementy replikowane** **ustawienia** > , wybierz maszynę wirtualną, a następnie wybierz pozycję **Tryb failover**.
1. W przypadku **trybu failover**wybierz pozycję **Najnowsze**. 
2. Wybierz pozycję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover**. Usługa Site Recovery próbuje zamknąć źródłowej maszyny Wirtualnej przed wyzwoleniem pracy awaryjnej. Ale tryb failover nadal, nawet jeśli zamknięcie nie powiedzie się. Postęp pracy awaryjnej można śledzić na stronie **Zadania.**
3. Po zakończeniu zadania sprawdź, czy maszyna wirtualna jest wyświetlana w docelowym regionie platformy Azure zgodnie z oczekiwaniami.
4. W **obszarze Elementy replikowane**kliknij prawym przyciskiem myszy maszynę wirtualną i wybierz polecenie **Zatwierdź**. To kończy ruch. Poczekaj, aż zadanie zatwierdzania zakończy się.

## <a name="discard-the-resources-from-the-source-region"></a>Wyrzucanie zasobów z regionu źródłowego

- Przejdź do maszyny Wirtualnej i wybierz pozycję **Wyłącz replikację**. Spowoduje to zatrzymanie procesu kopiowania danych maszyny wirtualnej.

  > [!IMPORTANT]
  > Wykonaj ten krok, aby uniknąć naliczania opłat za replikację usługi Site Recovery po przeprowadzce.

Jeśli nie planujesz ponownego użycia któregokolwiek z zasobów źródłowych, wykonaj następujące kroki:

1. Usuń wszystkie odpowiednie zasoby sieciowe w regionie źródłowym wymienionym w kroku 4 [przygotowywania źródłowych maszyn wirtualnych](#prepare-the-source-vms).
2. Usuń odpowiednie konto magazynu w regionie źródłowym.

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak przenieść maszyny wirtualne platformy Azure do innego regionu platformy Azure. Teraz można skonfigurować odzyskiwanie po awarii dla tych maszyn wirtualnych.

> [!div class="nextstepaction"]
> [Konfigurowanie odzyskiwania po awarii po migracji](azure-to-azure-quickstart.md)

