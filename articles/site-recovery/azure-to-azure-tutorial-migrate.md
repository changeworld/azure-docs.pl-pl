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
ms.openlocfilehash: 4882206692c334d6ab6af28feb5d2cba5277eea1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78303940"
---
# <a name="move-azure-vms-to-another-region"></a>Przenoszenie maszyn wirtualnych platformy Azure do innego regionu

Istnieją różne scenariusze, w których chcesz przenieść istniejące maszyny wirtualne usługi Azure IaaS (maszyny wirtualne) z jednego regionu do drugiego. Na przykład chcesz zwiększyć niezawodność i dostępność istniejących maszyn wirtualnych, aby poprawić łatwość zarządzania lub przenieść ze względów nadzoru. Aby uzyskać więcej informacji, zobacz [omówienie przenoszenia maszyny Wirtualnej platformy Azure](azure-to-azure-move-overview.md). 

Za pomocą usługi [Azure Site Recovery](site-recovery-overview.md) można zarządzać i aranżować odzyskiwanie po awarii na komputerach lokalnych i maszynach wirtualnych platformy Azure w celu zapewnienia ciągłości działania i odzyskiwania po awarii (BCDR). Można również użyć usługi Site Recovery do zarządzania przenoszeniem maszyn wirtualnych platformy Azure do regionu pomocniczego.

W tym samouczku zostaną wykonane następujące czynności:

> [!div class="checklist"]
> 
> * Weryfikowanie wymagań wstępnych dla przeniesienia
> * Przygotowywanie źródłowych maszyn wirtualnych i regionu docelowego
> * Kopiowanie danych i włączanie replikacji
> * Przetestuj konfigurację i wykonaj ruch
> * Usuwanie zasobów w regionie źródłowym
> 
> [!NOTE]
> W tym samouczku pokazano, jak przenieść maszyny wirtualne platformy Azure z jednego regionu do drugiego, jak jest. Jeśli chcesz poprawić dostępność, przenosząc maszyny wirtualne w dostępności ustawionej na maszyny wirtualne przypięte do strefy w innym regionie, zobacz [samouczek Przenoszenie maszyn wirtualnych platformy Azure do stref dostępności](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że maszyny wirtualne platformy Azure znajdują się w regionie platformy Azure, z którego chcesz przenieść.
- Sprawdź, czy kombinacja [regionu źródłowego — regionu docelowego jest obsługiwana,](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)i podejmij świadomą decyzję o regionie docelowym.
- Przeanalizuj informacje o [składnikach i architekturze scenariusza](azure-to-azure-architecture.md).
- Zapoznaj się z [ograniczeniami i wymaganiami dotyczącymi pomocy technicznej](azure-to-azure-support-matrix.md).
- Weryfikuj uprawnienia konta. Jeśli utworzono bezpłatne konto platformy Azure, jesteś administratorem subskrypcji. Jeśli nie jesteś administratorem subskrypcji, współpracuj z administratorem, aby przypisać potrzebne uprawnienia. Aby włączyć replikację maszyny Wirtualnej i zasadniczo skopiować dane przy użyciu usługi Azure Site Recovery, musisz mieć:

    - Uprawnienia do tworzenia maszyny wirtualnej w ramach zasobów platformy Azure. Wbudowana rola współautora maszyny wirtualnej ma następujące uprawnienia, które obejmują:
    - Uprawnienie do tworzenia maszyny wirtualnej w wybranej grupie zasobów
    - Uprawnienie do tworzenia maszyny wirtualnej w wybranej sieci wirtualnej
    - Uprawnienie do zapisu na wybranym koncie magazynu
    
    - Uprawnienia do zarządzania operacjami usługi Azure Site Recovery. Rola Współautor odzyskiwania lokacji ma wszystkie uprawnienia wymagane do zarządzania operacjami odzyskiwania witryny w magazynie usług odzyskiwania.

- Upewnij się, że wszystkie najnowsze certyfikaty główne znajdują się na maszynach wirtualnych platformy Azure, które chcesz przenieść. Jeśli najnowszych certyfikatów głównych nie ma na maszynie Wirtualnej, ograniczenia zabezpieczeń uniemożliwią kopiowanie danych do regionu docelowego.

- Aby zainstalować wszystkie zaufane certyfikaty główne na maszynach wirtualnych z systemem Windows, zainstaluj wszystkie najnowsze aktualizacje systemu Windows. W środowisku bez połączenia postępuj zgodnie ze standardową procedurą usługi Windows Update i procedurą aktualizacji certyfikatów, które są używane w danej organizacji.
    
- W przypadku maszyn wirtualnych z systemem Linux postępuj zgodnie ze wskazówkami dystrybutora systemu Linux, aby uzyskać najnowsze zaufane certyfikaty główne i listę odwołania certyfikatów na maszynie wirtualnej.
- Upewnij się, że nie używasz serwera proxy uwierzytelniania do kontrolowania łączności sieciowej dla maszyn wirtualnych, które chcesz przenieść.

- Jeśli maszyna wirtualna, którą próbujesz przenieść, nie ma dostępu do Internetu lub używa serwera proxy zapory do kontrolowania dostępu [wychodzącego, sprawdź wymagania](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms).

- Zidentyfikuj układ sieci źródłowej i wszystkie zasoby, których aktualnie używasz. Obejmuje to między innymi moduły równoważenia obciążenia, sieciowe grupy zabezpieczeń i publiczne wiadomości IP.

- Sprawdź, czy subskrypcja platformy Azure umożliwia tworzenie maszyn wirtualnych w regionie docelowym, który jest używany do odzyskiwania po awarii. Skontaktuj się z pomocą techniczną, aby włączyć wymagany limit przydziału.

- Upewnij się, że subskrypcja ma wystarczającą ilość zasobów do obsługi maszyn wirtualnych o rozmiarach zgodnych ze źródłowymi maszynami wirtualnymi. Jeśli używasz usługi Site Recovery do kopiowania danych do obiektu docelowego, usługa Site Recovery wybiera ten sam rozmiar lub najbliższy możliwy rozmiar dla docelowej maszyny Wirtualnej.

- Upewnij się, że tworzysz zasób docelowy dla każdego składnika, który jest identyfikowany w układzie sieci źródłowej. Ten krok jest ważne, aby upewnić się, że maszyny wirtualne mają wszystkie funkcje i funkcje w regionie docelowym, który miał w regionie źródłowym.

     > [!NOTE] 
     > Usługa Azure Site Recovery automatycznie odnajduje i tworzy sieć wirtualną po włączeniu replikacji dla źródłowej maszyny Wirtualnej. Można również wstępnie utworzyć sieć i przypisać ją do maszyny Wirtualnej w przepływie użytkownika w celu włączenia replikacji. Jak wspomniano później, należy ręcznie utworzyć inne zasoby w regionie docelowym.

    Aby utworzyć najczęściej używane zasoby sieciowe, które są istotne dla Ciebie na podstawie źródłowej konfiguracji maszyny Wirtualnej, zobacz następującą dokumentację:
    - [Grupy zabezpieczeń sieci](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Moduły równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer)
    -  [Publiczny adres IP](../virtual-network/virtual-network-public-ip-address.md)
    - Informacje na o innych składnikach sieci można znaleźć w [dokumentacji sieci](https://docs.microsoft.com/azure/?pivot=products&panel=network).



## <a name="prepare"></a>Przygotowanie
W poniższych krokach pokazano, jak przygotować maszynę wirtualną do przeniesienia przy użyciu usługi Azure Site Recovery jako rozwiązania. 

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Tworzenie przechowalni w dowolnym regionie, z wyjątkiem regionu źródłowego

1. Zaloguj się do**usługi odzyskiwania** [portalu](https://portal.azure.com) > Azure .
1. Wybierz **pozycję Utwórz kopię zapasową** > narzędzi do > **zarządzania zasobami****i odzyskiwanie witryny**.
1. W polu **Nazwa** podaj przyjazną nazwę **ContosoVMVault**. Jeśli masz więcej niż jedną subskrypcję, wybierz jedną z nich.
1. Utwórz grupę zasobów **ContosoRG**.
1. Określ region platformy Azure. Aby sprawdzić obsługiwane regiony, zobacz dostępność geograficzna w [szczegółach cen usługi Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
1. W **przechowalniach usług odzyskiwania**wybierz pozycję **Przegląd** > **ContosoVMVault** > **+Replikacja**.
1. W obszarze **Źródło** wybierz pozycję **Azure**.
1. W obszarze **Lokalizacja źródłowa** wybierz źródłowy region świadczenia usługi Azure, w którym są uruchomione maszyny wirtualne.
1. Wybierz model wdrażania usługi Resource Manager. Następnie wybierz pozycje **Subskrypcja źródłowa** i **Źródłowa grupa zasobów**.
1. Wybierz **przycisk OK,** aby zapisać ustawienia.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Włącz replikację dla maszyn wirtualnych platformy Azure i rozpocznij kopiowanie danych

Usługa Site Recovery pobiera listę maszyn wirtualnych skojarzonych z grupą subskrypcji i zasobów.

1. W następnym kroku wybierz maszynę wirtualną, którą chcesz przenieść, a następnie wybierz przycisk **OK**.
1. W **obszarze Ustawienia**wybierz pozycję Odzyskiwanie po **awarii**.
1. W **obszarze Konfigurowanie** > **regionu docelowego**odzyskiwania po awarii wybierz region docelowy, do którego będzie można replikować.
1. Dla celów tego samouczka zaakceptuj inne ustawienia domyślne.
1. Wybierz **włącz replikację**. Ten krok uruchamia zadanie, aby włączyć replikację maszyny Wirtualnej.

    ![Włączanie replikacji](media/tutorial-migrate-azure-to-azure/settings.png)

## <a name="move"></a>Move

W poniższych krokach pokazano, jak wykonać przejście do regionu docelowego.

1. Przejdź do skarbca. W **obszarze Ustawienia** > **replikowane elementy**wybierz maszynę wirtualną, a następnie wybierz pozycję **Przewiduj awaryjnie**.
2. W obszarze **Tryb failover** wybierz pozycję **Najnowsze**.
3. Wybierz pozycję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover**. Usługa Site Recovery próbuje zamknąć źródłową maszynę wirtualną przed wyzwoleniem trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Postęp pracy awaryjnej można śledzić na stronie **Zadania.**
4. Po zakończeniu zadania sprawdź, czy maszyna wirtualna jest wyświetlana w docelowym regionie platformy Azure zgodnie z oczekiwaniami.


## <a name="discard"></a>Odrzuć 

W przypadku, gdy zaznaczono przeniesioną maszynę wirtualną i trzeba zmienić do punktu pracy awaryjnej lub chcesz wrócić do poprzedniego punktu, w **elementy replikowane**, wybierz prawym przyciskiem > **Zmień punkt odzyskiwania**. Ten krok umożliwia określenie innego punktu odzyskiwania i pracy awaryjnej do tego. 


## <a name="commit"></a>Zatwierdzenie 

Po sprawdzeniu przeniesionej maszyny Wirtualnej i gotowości do zatwierdzenia zmiany w **elementach replikowanych**wybierz maszynę wirtualną > **commit**. Ten krok kończy proces przenoszenia do regionu docelowego. Poczekaj, aż zadanie zatwierdzania zakończy się.

## <a name="clean-up"></a>Czyszczenie

Poniższe kroki poprowadzą Cię przez sposób czyszczenia regionu źródłowego, a także powiązanych zasobów, które zostały użyte do przeniesienia.

Dla wszystkich zasobów, które zostały użyte do przeniesienia:

- Przejdź do maszyny Wirtualnej. Wybierz **pozycję Wyłącz replikację**. Ten krok uniemożliwia procesowi kopiowanie danych dla maszyny Wirtualnej.

   > [!IMPORTANT]
   > Należy wykonać ten krok, aby uniknąć naliczania opłat za replikację usługi Azure Site Recovery.

Jeśli nie masz żadnych planów ponownego użycia zasobów źródłowych, wykonaj następujące dodatkowe kroki:

1. Usuń wszystkie odpowiednie zasoby sieciowe w regionie źródłowym, które zostały zidentyfikowane w [wymaganiach wstępnych](#prerequisites).
1. Usuń odpowiednie konto magazynu w regionie źródłowym.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przeniesiono maszynę wirtualną platformy Azure do innego regionu platformy Azure. Teraz można skonfigurować odzyskiwanie po awarii dla maszyny Wirtualnej, która została przeniesiona.

> [!div class="nextstepaction"]
> [Konfigurowanie odzyskiwania po awarii po migracji](azure-to-azure-quickstart.md)

