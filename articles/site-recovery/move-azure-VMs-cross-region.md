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
ms.openlocfilehash: 0f73e68fd0c01d4323e8675d3fa12f7ca1051cdb
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119881"
---
# <a name="move-azure-vms-to-another-region"></a>Przenoszenie maszyn wirtualnych platformy Azure do innego regionu

Możesz chcieć przenieść infrastruktury platformy Azure jako maszyny wirtualne usługi (IaaS) z jednego regionu do innego celu poprawy niezawodności, dostępności, zarządzania i nadzoru. Ten samouczek pokazuje, jak przenieść maszyny wirtualne do innego regionu za pomocą usługi Azure Site Recovery. Omawiane tematy:

> [!div class="checklist"]
> * Weryfikowanie wymagań wstępnych
> * Przygotowywanie źródłowych maszyn wirtualnych
> * Przygotowywanie regionu docelowego
> * Kopiowanie danych do regionu docelowego
> * Testowanie konfiguracji
> * Przenoszenie
> * Odrzuć zasobów w regionie źródłowym


> [!IMPORTANT]
> W tym artykule opisano sposób przenoszenia maszyn wirtualnych platformy Azure z jednego regionu do innego *się*. Jeśli dowiesz się, jak zwiększyć dostępność infrastruktury przez przeniesienie maszyn wirtualnych do strefy dostępności, zobacz [przenoszenie maszyn wirtualnych platformy Azure do strefy dostępności](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że maszyny wirtualne platformy Azure w źródle region platformy Azure, który chcesz przenieść *z*.
- Upewnij się, że wybór [kombinacja region docelowy region źródła jest obsługiwana](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)i starannie wybrać region docelowy.
- Przeanalizuj informacje o [składnikach i architekturze scenariusza](azure-to-azure-architecture.md).
- Zapoznaj się z [ograniczeniami i wymaganiami dotyczącymi pomocy technicznej](azure-to-azure-support-matrix.md).
- Sprawdzanie uprawnień konta. Jeśli właśnie utworzone bezpłatnego konta platformy Azure, *możesz* jesteś administratorem subskrypcji. Jeśli nie jesteś administratorem, pracować z administratorem, aby uzyskać uprawnienia, które są potrzebne:
  -  Aby włączyć replikację dla maszyny Wirtualnej i kopiowania danych do obiektu docelowego przy użyciu usługi Site Recovery, musi mieć uprawnienia do tworzenia maszyny Wirtualnej w Twoich zasobów platformy Azure. Wbudowana rola „Współautor maszyny wirtualnej” ma te uprawnienia. Uprawnienia możesz wykonywać następujące czynności:
        - Tworzenie maszyny wirtualnej w wybranej grupie zasobów.
        - Tworzenie maszyny wirtualnej w wybranej sieci wirtualnej.
        - Zapis na wybranym koncie magazynu.

  - Należy również uprawnienia do zarządzania operacjami usługi Site Recovery. Rola Współautor usługi Site Recovery ma wszystkie uprawnienia, które są wymagane do zarządzania operacjami usługi Site Recovery w magazynie usługi Azure Recovery Services.

## <a name="prepare-the-source-vms"></a>Przygotowywanie źródłowych maszyn wirtualnych

1. Sprawdź najnowsze certyfikaty główne maszyny wirtualne platformy Azure, które ma zostać przeniesiona. Jeśli nie, nie można włączyć kopiowanie danych do regionu docelowego ze względu na ograniczenia zabezpieczeń.

    - W przypadku maszyn wirtualnych Windows zainstaluj najnowsze aktualizacje Windows, tak aby wszystkie zaufane certyfikaty główne na komputerze. W środowisku bez połączenia postępuj zgodnie z standardowych procesów aktualizacji Windows i aktualizacji certyfikatu dla Twojej organizacji.
    - W przypadku maszyn wirtualnych systemu Linux postępuj zgodnie z wskazówki od dystrybutora systemu Linux można pobrać najnowsze wymagane certyfikaty główne i listę odwołania certyfikatów.
2. Upewnij się, że nie używasz uwierzytelniającego serwera proxy do sterowania łącznością sieciową dla maszyn wirtualnych, które ma zostać przeniesiona.
3. Jeśli Maszynę wirtualną, którą chcesz przenieść nie ma dostępu do Internetu i używa serwera proxy zapory w celu kontrolowania dostępu wychodzącego, sprawdź [wymagania](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).
4. Układ sieci źródłowego i wszystkie zasoby, które aktualnie używasz, tym (między innymi) dokumentów modułów równoważenia obciążenia, sieciowe grupy zabezpieczeń i publicznych adresów IP w celu weryfikacji.

## <a name="prepare-the-target-region"></a>Przygotowywanie regionu docelowego

1. W ramach subskrypcji platformy Azure Sprawdź, możesz utworzyć maszyny wirtualne w regionie docelowym, który służy do odzyskiwania po awarii. Skontaktuj się z pomocą techniczną, aby włączyć Wymagany limit przydziału, jeśli to konieczne.

2. Upewnij się, że Twoja subskrypcja ma za mało zasobów do obsługi, źródłowe maszyny wirtualne. Jeśli używasz Site Recovery można skopiować danych do obiektu docelowego, wybiera ten sam rozmiar lub najbliższy dostępny rozmiar dla docelowej maszyny wirtualne.

3. Upewnij się, utwórz zasób docelowy dla każdego składnika, który został zidentyfikowany w układzie sieci źródłowej. Dzięki temu maszyny wirtualne będą wszystkie funkcje i w regionie docelowym, który w regionie źródłowym.

   Usługa Azure Site Recovery automatycznie wykrywa i tworzy konta magazynu i sieci wirtualnego po włączeniu replikacji dla źródłowej maszyny Wirtualnej. Można wstępnie utworzyć te zasoby i przypisać je do maszyny Wirtualnej w ramach kroku Włączanie replikacji. Jednak należy ręcznie utworzyć wszelkie inne zasoby w regionie docelowym. Można znaleźć w następujących dokumentach na tworzenie zasobów sieciowych najczęściej używanych na podstawie konfiguracji sieci źródłowej maszyny Wirtualnej:

   - [Sieciowe grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
   - [Moduły równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
   - [Publiczny adres IP](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
   Dla innych składników sieciowych, zobacz [dokumentacji sieci platformy Azure](https://docs.microsoft.com/azure/#pivot=products&panel=network). 

4. Aby przetestować konfigurację, przed wykonaniem przeniesienia, ręcznie [Utwórz sieć nieprodukcyjnych](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) w regionie docelowym. Testowanie instalacji tworzy minimalne zakłócenia w środowisku produkcyjnym, a firma Microsoft zaleca.
    
## <a name="copy-data-to-the-target-region"></a>Kopiowanie danych do regionu docelowego
Użyto usługi Azure Site Recovery umożliwia kopiowanie danych do regionu docelowego.

### <a name="create-the-vault-in-any-region-except-the-source"></a>Utwórz magazyn w dowolnym regionie, z wyjątkiem źródła

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) > **Recovery Services**.
2. Wybierz **Utwórz zasób** > **narzędzia do zarządzania** > **Backup i Site Recovery**.
3. Aby uzyskać **nazwa**, określ przyjazną nazwę **ContosoVMVault**. Jeśli masz więcej niż jedną subskrypcję, wybierz jedną z nich.
4. Utwórz grupę zasobów **ContosoRG**.
5. Określ region platformy Azure. Aby sprawdzić obsługiwane regiony, zobacz [szczegóły cennika usługi Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Magazyny usługi Recovery Services można wybrać **Przegląd** > **ConsotoVMVault** > **+ Replikuj**.
7. Aby uzyskać **źródła**, wybierz opcję **Azure**.
8. Aby uzyskać **lokalizacja źródłowa**, wybierz źródłowy region platformy Azure, w którym są uruchomione maszyny wirtualne.
9. Wybierz model wdrażania usługi Azure Resource Manager. Następnie wybierz **źródła subskrypcji** i **źródłowa grupa zasobów**.
10. Wybierz **OK** Aby zapisać ustawienia.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Włącz replikację dla maszyn wirtualnych platformy Azure i Rozpocznij kopiowanie danych

Usługa Site Recovery pobiera listę maszyn wirtualnych, które są skojarzone z subskrypcji i grupie zasobów.

1. Wybierz maszynę Wirtualną, którą chcesz przenieść, a następnie wybierz **OK**.
2. Aby uzyskać **ustawienia**, wybierz opcję **odzyskiwania po awarii**.
3. Aby uzyskać **Konfigurowanie odzyskiwania po awarii** > **region docelowy**, wybierz region docelowy, który replikujesz do.
4. Wybrać zasoby docelowe domyślne lub wstępnie utworzone.
5. Wybierz **włączyć replikację** Aby uruchomić zadanie.

   ![Włączanie replikacji](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="test-the-configuration"></a>Testowanie konfiguracji


1. Przejdź do magazynu. W **ustawienia** > **zreplikowane elementy**, wybierz maszynę wirtualną, którą chcesz przenieść do regionu docelowego. Następnie wybierz **testowy tryb Failover**.
2. W **testowy tryb Failover**, wybierz punkt odzyskiwania na potrzeby pracy w trybie failover:

   - **Najnowszy przetworzony**: wprowadza maszynę wirtualną w tryb failover do najnowszego punktu odzyskiwania przetworzonego przez usługę Site Recovery. Wyświetlana jest sygnatura czasowa. Nie czasu na przetwarzanie danych, więc ta opcja zapewnia niskimi celami czasu odzyskiwania (RTO).
   - **Najnowszy spójny na poziomie aplikacji**: Kończy się niepowodzeniem, za pośrednictwem wszystkich maszyn wirtualnych do najnowszego punktu odzyskiwania spójnego na poziomie aplikacji. Wyświetlana jest sygnatura czasowa.
   - **Niestandardowy**: można wybrać dowolny punkt odzyskiwania.

3. Wybierz docelową sieć wirtualną platformy Azure, do której chcesz przenieść maszyny wirtualne platformy Azure, aby przetestować konfigurację.

   > [!IMPORTANT]
   > Zalecamy użycie oddzielnej sieci maszyn wirtualnych platformy Azure do testowania trybu failover, nie sieci produkcyjnych w regionie docelowym.

4. Aby uruchomić testy przeniesienie, zaznacz **OK**. Aby śledzić postępy, wybierz maszynę Wirtualną i otwierając jej **właściwości.** Lub wybierz **testowy tryb Failover** zadania w magazynie. Następnie wybierz **ustawienia** > **zadania** > **zadania usługi Site Recovery**.
5. Po zakończeniu trybu failover w obszarze Azure Portal > **Maszyny wirtualne** będzie widoczna replika maszyny wirtualnej platformy Azure. Upewnij się, że maszyna wirtualna jest uruchomiona, ma właściwy rozmiar i została połączona z odpowiednią siecią.
6. Aby usunąć maszynę Wirtualną, która utworzone na potrzeby testowania, wybierz **wyczyść test pracy awaryjnej** replikowanego elementu. Z **uwagi**Zarejestruj i zapisz wszelkie obserwacje związane z testem.

## <a name="perform-the-move-and-confirm"></a>Przenoszenie i upewnij się

1. Przejdź do magazynu w **ustawienia** > **zreplikowane elementy**, wybierz maszynę wirtualną, a następnie wybierz **trybu Failover**.
1. Aby uzyskać **trybu Failover**, wybierz opcję **najnowsze**. 
2. Wybierz pozycję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover**. Usługa Site Recovery próbuje zamknąć źródłową maszynę Wirtualną przed wyzwoleniem trybu failover. Ale trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Na stronie **Zadania** można śledzić postęp trybu failover.
3. Po zakończeniu zadania Sprawdź, czy maszyna wirtualna jest wyświetlana w element docelowy region świadczenia usługi Azure, zgodnie z oczekiwaniami.
4. W **zreplikowane elementy**, kliknij prawym przyciskiem myszy maszynę Wirtualną i wybierz **zatwierdzić**. Spowoduje to zakończenie przejścia. Poczekaj na zakończenie zadania zatwierdzenia.

## <a name="discard-the-resources-from-the-source-region"></a>Odrzuć zasobów w regionie źródłowym

- Przejdź do maszyny Wirtualnej, a następnie wybierz pozycję **Wyłącz replikację**. Spowoduje to zatrzymanie procesu kopiowania danych maszyny wirtualnej.

  > [!IMPORTANT]
  > Wykonaj ten krok, aby uniknąć opłata jest naliczana za replikacji usługi Site Recovery po przeniesieniu.

Jeśli nie planujesz ponownego użycia zasobów źródłowego, wykonaj następujące czynności:

1. Aby usunąć wszystkie zasoby odpowiednich sieci w regionie źródłowym wymienionego w kroku 4 [przygotowanie źródłowe maszyny wirtualne](#prepare-the-source-vms).
2. Usuń odpowiednie konto magazynu w regionie źródłowym.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób przenoszenia maszyn wirtualnych platformy Azure do innego regionu platformy Azure. Teraz możesz skonfigurować odzyskiwanie po awarii dla tych maszyn wirtualnych.

> [!div class="nextstepaction"]
> [Konfigurowanie odzyskiwania po awarii po migracji](azure-to-azure-quickstart.md)

