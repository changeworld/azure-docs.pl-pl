---
title: Przenoszenie maszyny wirtualnej do innego regionu (usługa Azure Site Recovery)
description: Dowiedz się, jak przeprowadzić migrację maszyny wirtualnej programu SQL Server z jednego regionu do drugiego na platformie Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 3b84119cdcc1bb7e8603de64e3d23c69dac70cc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022289"
---
# <a name="move-sql-server-vm-to-another-region-within-azure-with-azure-site-recovery-services"></a>Przenoszenie maszyny Wirtualnej programu SQL Server do innego regionu platformy Azure za pomocą usług Azure Site Recovery

W tym artykule dowiesz się, jak używać usługi Azure Site Recovery do migracji maszyny wirtualnej programu SQL Server (VM) z jednego regionu do drugiego na platformie Azure. 

Przeniesienie maszyny Wirtualnej programu SQL Server do innego regionu wymaga wykonywania następujących czynności:
1. [**Przygotowanie:**](#prepare-to-move)Upewnij się, że zarówno źródłowa maszyna wirtualna programu SQL Server, jak i region docelowy są odpowiednio przygotowane do przeniesienia. 
1. [**Konfigurowanie: Przenoszenie**](#configure-azure-site-recovery-vault)maszyny Wirtualnej programu SQL Server wymaga, aby była ona obiektem replikowanym w przechowalni usługi Azure Site Recovery. Należy dodać maszynę wirtualną programu SQL Server do magazynu usługi Azure Site Recovery. 
1. [**Testowanie:**](#test-move-process)Migracja maszyny Wirtualnej programu SQL Server wymaga awarii go z regionu źródłowego do zreplikowanego regionu docelowego. Aby upewnić się, że proces przenoszenia zakończy się pomyślnie, należy najpierw przetestować, czy maszyna wirtualna programu SQL Server może pomyślnie przejść w stan failover do regionu docelowego. Pomoże to ujawnić wszelkie problemy i uniknąć ich podczas wykonywania rzeczywistego ruchu. 
1. [**Przenoszenie:**](#move-the-sql-server-vm)Po zakończeniu pracy awaryjnej testu i wiesz, że można bezpiecznie przeprowadzić migrację maszyny Wirtualnej programu SQL Server, można wykonać przeniesienie maszyny Wirtualnej do regionu docelowego. 
1. [**Czyszczenie:**](#clean-up-source-resources)Aby uniknąć opłat rozliczeniowych, usuń maszynę wirtualną programu SQL Server z magazynu i wszelkie niepotrzebne zasoby, które pozostały w grupie zasobów. 

## <a name="verify-prerequisites"></a>Weryfikowanie wymagań wstępnych 

- Upewnij się, że przejście z regionu źródłowego do regionu docelowego [jest obsługiwane](../../../site-recovery/azure-to-azure-support-matrix.md#region-support).  
- Przejrzyj [architekturę scenariusza i składniki,](../../../site-recovery/azure-to-azure-architecture.md) a także [ograniczenia i wymagania dotyczące obsługi](../../../site-recovery/azure-to-azure-support-matrix.md). 
- Weryfikuj uprawnienia konta. Jeśli utworzono bezpłatne konto platformy Azure, jesteś administratorem subskrypcji. Jeśli nie jesteś administratorem subskrypcji, współpracuj z administratorem, aby przypisać potrzebne uprawnienia. Aby włączyć replikację maszyny Wirtualnej i skopiować dane przy użyciu usługi Azure Site Recovery, musisz mieć: 
    - Uprawnienia do tworzenia maszyny Wirtualnej. Wbudowana rola *współautora maszyny wirtualnej* ma następujące uprawnienia, które obejmują: 
        - Uprawnienia do tworzenia maszyny Wirtualnej w wybranej grupie zasobów. 
        - Uprawnienia do tworzenia maszyny Wirtualnej w wybranej sieci wirtualnej. 
        - Uprawnienia do zapisu na wybranym koncie magazynu. 
      - Uprawnienia do zarządzania operacjami usługi Azure Site Recovery. *Rola Współautor odzyskiwania lokacji* ma wszystkie uprawnienia wymagane do zarządzania operacjami odzyskiwania witryny w magazynie usług odzyskiwania.  

## <a name="prepare-to-move"></a>Przygotowanie do ruchu
Przygotuj zarówno źródłowej maszyny Wirtualnej programu SQL Server, jak i region docelowy dla przeniesienia. 

### <a name="prepare-the-source-sql-server-vm"></a>Przygotowywanie źródłowej maszyny Wirtualnej programu SQL Server

- Upewnij się, że wszystkie najnowsze certyfikaty główne znajdują się na maszynie wirtualnej programu SQL Server, którą chcesz przenieść. Jeśli najnowszych certyfikatów głównych nie ma, ograniczenia zabezpieczeń uniemożliwią kopiowanie danych do regionu docelowego. 
- W przypadku maszyn wirtualnych z systemem Windows zainstaluj wszystkie najnowsze aktualizacje systemu Windows na maszynie Wirtualnej, tak aby wszystkie zaufane certyfikaty główne były dostępne na komputerze. W środowisku rozłączanym postępuj zgodnie ze standardowym procesem udate systemu Windows i aktualizacji certyfikatów w organizacji. 
- W przypadku maszyn wirtualnych z systemem Linux postępuj zgodnie ze wskazówkami dystrybutora systemu Linux, aby uzyskać najnowsze zaufane certyfikaty główne i listę odwołania certyfikatów na maszynie wirtualnej. 
- Upewnij się, że nie używasz serwera proxy uwierzytelniania do kontrolowania łączności sieciowej dla maszyn wirtualnych, które chcesz przenieść. 
- Jeśli maszyna wirtualna, którą próbujesz przenieść, nie ma dostępu do Internetu lub używa serwera proxy zapory do kontrolowania dostępu wychodzącego, sprawdź wymagania. 
- Zidentyfikuj układ sieci źródłowej i wszystkie zasoby, których aktualnie używasz. Obejmuje to między innymi moduły równoważenia obciążenia, sieciowe grupy zabezpieczeń i publiczne wiadomości IP. 

### <a name="prepare-the-target-region"></a>Przygotowywanie regionu docelowego

- Sprawdź, czy subskrypcja platformy Azure umożliwia tworzenie maszyn wirtualnych w regionie docelowym, który jest używany do odzyskiwania po awarii. Skontaktuj się z pomocą techniczną, aby włączyć wymagany limit przydziału. 
- Upewnij się, że subskrypcja ma wystarczającą ilość zasobów do obsługi maszyn wirtualnych o rozmiarze zgodnym ze źródłowymi maszynami wirtualnymi. Jeśli używasz usługi Site Recovery do kopiowania danych do obiektu docelowego, usługa Site Recovery wybiera ten sam rozmiar lub najbliższy możliwy rozmiar dla docelowej maszyny Wirtualnej. 
- Upewnij się, że tworzysz zasób docelowy dla każdego składnika, który jest identyfikowany w układzie sieci źródłowej. Ten krok jest ważne, aby upewnić się, że maszyny wirtualne mają wszystkie funkcje i funkcje w regionie docelowym, który miał w regionie źródłowym. 
    - Usługa Azure Site Recovery automatycznie odnajduje i tworzy sieć wirtualną po włączeniu replikacji dla źródłowej maszyny Wirtualnej. Można również wstępnie utworzyć sieć i przypisać ją do maszyny Wirtualnej w przepływie użytkownika w celu włączenia replikacji. Należy ręcznie utworzyć inne zasoby w regionie docelowym.
- Aby utworzyć najczęściej używane zasoby sieciowe, które są istotne dla Ciebie na podstawie źródłowej konfiguracji maszyny Wirtualnej, zobacz następującą dokumentację: 
    - [Grupy zabezpieczeń sieci](../../../virtual-network/tutorial-filter-network-traffic.md) 
    - [Moduł równoważenia obciążenia](../../../load-balancer/tutorial-load-balancer-basic-internal-portal.md)
    - [Publiczny adres IP](../../../virtual-network/virtual-network-public-ip-address.md)
    - Informacje na o jakichkolwiek dodatkowych składnikach sieci można znaleźć w [dokumentacji sieci](../../../virtual-network/virtual-networks-overview.md).
- Ręcznie utwórz sieć nieprodukcową w regionie docelowym, jeśli chcesz przetestować konfigurację przed wykonaniem ostatecznego przeniesienia do regionu docelowego. Zalecamy ten krok, ponieważ zapewnia minimalne zakłócenia w sieci produkcyjnej. 

## <a name="configure-azure-site-recovery-vault"></a>Konfigurowanie magazynu usługi Azure Site Recovery

Poniższe kroki pokazują, jak używać usługi Azure Site Recovery do kopiowania danych do regionu docelowego. Utwórz magazyn usług odzyskiwania w dowolnym regionie innym niż region źródłowy. 

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com). 
1. Wybierz pozycję **Utwórz zasób** z lewego górnego rogu okienka nawigacji. 
1. Wybierz **narzędzia do zarządzania & IT,** a następnie wybierz pozycję **Kopia zapasowa i odzyskiwanie witryny**. 
1. Na karcie **Podstawy** w obszarze **Szczegóły projektu**utwórz nową grupę zasobów w regionie docelowym lub wybierz istniejącą grupę zasobów w regionie docelowym. 
1. W obszarze **Szczegóły wystąpienia**określ nazwę przechowalni, a następnie wybierz obszar **docelowy** z listy rozwijanej. 
1. Wybierz **opcję Przejrzyj + Utwórz,** aby utworzyć magazyn usług odzyskiwania. 
1. Wybierz **pozycję Wszystkie usługi** z lewego górnego rogu okienka `recovery services`nawigacji i w polu wyszukiwania . 
1. (Opcjonalnie) Wybierz gwiazdkę obok **przechowalni usług odzyskiwania,** aby dodać ją do paska szybkiego paska nawigacyjnego. 
1. Wybierz **przechowalnie usług odzyskiwania,** a następnie wybierz utworzony magazyn usług odzyskiwania. 
1. W okienku **Przegląd** wybierz pozycję **Replikuj**. 

   ![Konfigurowanie replikacji](media/virtual-machines-windows-sql-move-to-new-region/configure-replication.png)

1. Wybierz **źródło,** a następnie wybierz **pozycję Azure** jako źródło. Wybierz odpowiednie wartości dla innych pól rozwijanych, takich jak lokalizacja źródłowych maszyn wirtualnych. Tylko grupy zasobów znajdujące się w regionie **Lokalizacja źródło** będzie widoczne w polu Grupa **zasobów Źródło.** 
1. Wybierz **maszyny wirtualne,** a następnie wybierz maszyny wirtualne, które chcesz przeprowadzić migrację. Wybierz **przycisk OK,** aby zapisać zaznaczenie maszyny Wirtualnej. 
1. Wybierz **pozycję Ustawienia**, a następnie wybierz lokalizację **docelową** z listy rozwijanej. Powinna to być grupa zasobów przygotowana wcześniej. 
1. Po dostosowaniu replikacji wybierz pozycję **Utwórz zasoby docelowe,** aby utworzyć zasoby w nowej lokalizacji. 
1. Po zakończeniu tworzenia zasobów wybierz **pozycję Włącz replikację,** aby rozpocząć replikację maszyny Wirtualnej programu SQL Server ze źródła do regionu docelowego.
1. Stan replikacji można sprawdzić, przechodząc do magazynu odzyskiwania, wybierając **pozycję Elementy replikowane** i wyświetlając **stan** maszyny Wirtualnej programu SQL Server. Stan **Protected** wskazuje, że replikacja została zakończona. 

   ![Sprawdzanie stanu replikacji](media/virtual-machines-windows-sql-move-to-new-region/check-replication-status.png)

## <a name="test-move-process"></a>Proces przenoszenia testu
Poniższe kroki pokazują, jak za pomocą usługi Azure Site Recovery, aby przetestować proces przenoszenia. 

1. Przejdź do **magazynu usług odzyskiwania** w [witrynie Azure portal](https://portal.azure.com) i wybierz pozycję Elementy **replikowane**. 
1. Wybierz maszynę wirtualną programu SQL Server, którą chcesz przenieść, sprawdź, czy **kondycja replikacji** jest wyświetlana jako **w dobrej kondycji,** a następnie wybierz pozycję **Test failover**. 

   ![Testowanie pracy awaryjnej maszyny Wirtualnej](media/virtual-machines-windows-sql-move-to-new-region/test-failover-of-replicated-vm.png)

1. Na stronie **Testowanie trybu failover** wybierz punkt odzyskiwania **spójne z najnowszą aplikacją,** który ma być używany do pracy awaryjnej, ponieważ jest to jedyny typ migawki, który może zagwarantować spójność danych programu SQL Server. 
1. Wybierz sieć wirtualną w **obszarze sieć wirtualna platformy Azure,** a następnie wybierz **przycisk OK,** aby przetestować tryb failover. 
   
   >[!IMPORTANT]
   > Zaleca się użycie oddzielnej sieci maszyn wirtualnych platformy Azure dla testu trybu failover. Nie należy używać sieci produkcyjnej, która została skonfigurowana po włączeniu replikacji i że chcesz przenieść maszyny wirtualne do ostatecznie. 

1. Aby monitorować postęp, przejdź do magazynu, wybierz pozycję **Zadania odzyskiwania witryny** w obszarze **Monitorowanie**, a następnie wybierz zadanie **testowania trybu failover,** które jest w toku.

   ![Monitorowanie postępu testu trybu failover](media/virtual-machines-windows-sql-move-to-new-region/monitor-failover-test-job.png)

1. Po zakończeniu testu przejdź do **maszyn wirtualnych** w portalu i przejrzyj nowo utworzoną maszynę wirtualną. Upewnij się, że maszyna wirtualna programu SQL Server jest uruchomiona, jest odpowiednio dobrany i jest podłączony do odpowiedniej sieci. 
1. Usuń maszynę wirtualną, która została utworzona w ramach testu, ponieważ opcja **Praca awaryjna** zostanie wyszarzona, dopóki zasoby testowe trybu failover nie zostaną wyczyszczone. Przejdź z powrotem do przechowalni, wybierz pozycję **Elementy replikowane**, wybierz maszynę wirtualną programu SQL Server, a następnie wybierz pozycję **Oczyszczanie testu w pracy awaryjnej**. Rejestrowanie i zapisywanie wszystkich obserwacji skojarzonych z testem w sekcji **Notatki** i zaznacz pole wyboru obok **pozycji Testowanie jest zakończone. Usuń testowe maszyny wirtualne trybu failover**. Wybierz **przycisk OK,** aby wyczyścić zasoby po teście. 

   ![czyszczenie elementów po teście pracy awaryjnej](media/virtual-machines-windows-sql-move-to-new-region/cleanup-test-items.png)

## <a name="move-the-sql-server-vm"></a>Przenoszenie maszyny Wirtualnej programu SQL Server 
Poniższe kroki pokazują, jak przenieść maszynę wirtualną programu SQL Server z regionu źródłowego do regionu docelowego. 

1. Przejdź do skarbca **usług odzyskiwania,** wybierz pozycję **Elementy replikowane**, wybierz maszynę wirtualną, a następnie wybierz pozycję **Przewijanie awaryjne**. 

   ![Inicjowanie pracy awaryjnej](media/virtual-machines-windows-sql-move-to-new-region/initiate-failover.png)

1. Wybierz najnowszy punkt odzyskiwania **spójny z aplikacją** w obszarze **Punkt odzyskiwania**. 
1. Zaznacz pole wyboru obok pozycji **Zamknij komputer przed rozpoczęciem pracy awaryjnej**. Usługa Site Recovery podejmie próbę zamknięcia źródłowej maszyny Wirtualnej przed wyzwoleniem pracy awaryjnej. Przewijania awaryjnego będzie kontynuowana, nawet jeśli zamknięcie nie powiedzie się. 
1. Wybierz **przycisk OK,** aby rozpocząć pracę awaryjną.
1. Proces pracy awaryjnej można monitorować na tej samej stronie **zadań odzyskiwania witryny,** którą wyświetlino podczas monitorowania testu trybu failover w poprzedniej sekcji. 
1. Po zakończeniu zadania sprawdź, czy maszyna wirtualna programu SQL Server jest wyświetlana w regionie docelowym zgodnie z oczekiwaniami. 
1. Przejdź z powrotem do przechowalni, wybierz pozycję **Elementy replikowane**, wybierz maszynę wirtualną programu SQL Server i wybierz pozycję **Przyjmij,** aby zakończyć proces przenoszenia do regionu docelowego. Poczekaj, aż zadanie zatwierdzania zakończy się. 
1. Zarejestruj maszynę wirtualną programu SQL Server u dostawcy zasobów maszyny wirtualnej SQL, aby włączyć możliwość zarządzania **maszyną wirtualną SQL** w portalu azure i funkcje skojarzone z dostawcą zasobów. Aby uzyskać więcej informacji, zobacz [Rejestrowanie maszyny Wirtualnej programu SQL Server u dostawcy zasobów maszyny Wirtualnej SQL](virtual-machines-windows-sql-register-with-rp.md). 

  > [!WARNING]
  > Spójność danych programu SQL Server jest gwarantowana tylko za pomocą migawek spójnych z aplikacjami. Najnowszej **przetworzonej** migawki nie można użyć dla pracy awaryjnej programu SQL Server, ponieważ migawka odzyskiwania po awarii nie może zagwarantować spójności danych programu SQL Server. 

## <a name="clean-up-source-resources"></a>Oczyszczanie zasobów źródłowych
Aby uniknąć opłat rozliczeniowych, usuń maszynę wirtualną programu SQL Server z magazynu i usuń wszelkie niepotrzebne skojarzone zasoby. 

1. Przejdź z powrotem do magazynu **odzyskiwania witryny,** wybierz pozycję **Elementy replikowane**i wybierz maszynę wirtualną programu SQL Server. 
1. Wybierz **pozycję Wyłącz replikację**. Wybierz przyczynę wyłączenia ochrony, a następnie wybierz **przycisk OK,** aby wyłączyć replikację. 

   >[!IMPORTANT]
   > Jest ważne, aby wykonać ten krok, aby uniknąć opłat za replikację usługi Azure Site Recovery. 

1. Jeśli nie masz planów ponownego użycia zasobów w regionie źródłowym, usuń wszystkie odpowiednie zasoby sieciowe i odpowiadające im konta magazynu. 


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji zobacz następujące artykuły: 

* [Omówienie programu SQL Server na maszynie Wirtualnej systemu Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server na maszynie Wirtualnej systemu Windows — często zadawane pytania](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server na wskazówki dotyczące cen maszyn wirtualnych systemu Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server w informacjach o wersji maszyny Wirtualnej systemu Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


