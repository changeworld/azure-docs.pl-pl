---
title: Rozwiązywanie problemów z przejścia w tryb failover na platformie Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak rozwiązywać typowe problemy występujące podczas trybu failover na platformie Azure przy użyciu usługi Azure Site Recovery.
author: ponatara
manager: abhemraj
ms.service: site-recovery
ms.topic: article
ms.date: 09/11/2018
ms.author: ponatara
ms.openlocfilehash: 420d061b34734c7b5997f5cdd58fe7faaee9cb82
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51236760"
---
# <a name="troubleshoot-errors-when-failing-over-a-virtual-machine-to-azure"></a>Rozwiązywanie problemów podczas przechodzenia w tryb failover maszyny wirtualnej na platformie Azure

Może pojawić się jeden z następujących błędów podczas wykonywania pracy w trybie failover maszyny wirtualnej na platformie Azure. Aby rozwiązać problemy, należy użyć opisane kroki poszczególnych warunków wystąpienia błędu.

## <a name="failover-failed-with-error-id-28031"></a>Tryb failover nie powiodło się z Identyfikator błędu 28031

Usługa Site Recovery nie był w stanie niepowodzenia tworzenia maszyny wirtualnej platformy Azure. Może się zdarzyć z jednego z następujących powodów:

* Nie ma wystarczającego limitu przydziału, które są dostępne do utworzenia maszyny wirtualnej: dostępny limit przydziału można sprawdzić, przechodząc do subskrypcji -> użycie i przydziały. Możesz otworzyć [nowe żądanie pomocy technicznej](https://aka.ms/getazuresupport) Aby zwiększyć limit przydziału.

* Próbujesz trybu failover maszyny wirtualne rozmiar różnych rodzin w tym samym zestawie dostępności. Upewnij się, wybierz ten sam rodzina rozmiarów dla wszystkich maszyn wirtualnych w tym samym zestawie dostępności. Zmiana rozmiaru, przechodząc do ustawień obliczenia i sieć maszyny wirtualnej, a następnie ponów próbę pracy awaryjnej.

* Brak zasad w ramach subskrypcji, która uniemożliwia tworzenie maszyny wirtualnej. Zmień zasady aby umożliwić tworzenie maszyny wirtualnej, a następnie ponów próbę pracy awaryjnej.

## <a name="failover-failed-with-error-id-28092"></a>Tryb failover nie powiodło się z Identyfikator błędu 28092

Usługa Site Recovery nie mogła utworzyć interfejsu sieciowego dla nieudane przez maszynę wirtualną. Upewnij się, że masz wystarczającego limitu przydziału, które są dostępne w celu utworzenia interfejsów sieciowych w ramach subskrypcji. Dostępny limit przydziału można sprawdzić, przechodząc do subskrypcji -> użycie i przydziały. Możesz otworzyć [nowe żądanie pomocy technicznej](https://aka.ms/getazuresupport) Aby zwiększyć limit przydziału. Jeśli masz wystarczającego limitu przydziału, a następnie może to być tymczasowy wysyłania, spróbuj ponownie wykonać operację. Jeśli problem będzie nadal występować po ponawianiu prób, pozostaw komentarz na końcu tego dokumentu.  

## <a name="failover-failed-with-error-id-70038"></a>Tryb failover nie powiodło się z Identyfikator błędu 70038

Usługa Site Recovery nie mógł utworzyć nie powiodło się za pośrednictwem klasycznej maszyny wirtualnej na platformie Azure. Może się zdarzyć, ponieważ:

* Jeden z zasobów, takich jak sieć wirtualną, która jest wymagana dla maszyny wirtualnej, która ma zostać utworzony nie istnieje. Tworzenie sieci wirtualnej, zgodnie z postanowieniami w obszarze ustawień obliczenia i sieć maszyny wirtualnej lub zmodyfikuj ustawienia sieci wirtualnej, która już istnieje, a następnie ponów próbę pracy awaryjnej.

## <a name="unable-to-connectrdpssh---vm-connect-button-grayed-out"></a>Nie można nawiązać połączenia ani protokołu RDP/SSH — szary przycisk Połącz z maszyną Wirtualną

Jeśli **Connect** przycisku w trybie Failover maszyny Wirtualnej na platformie Azure jest wyszarzona i nie masz połączenia Expressroute lub sieci VPN typu lokacja-lokacja połączenie, następnie na platformie Azure

1. Przejdź do **maszyny wirtualnej** > **sieć**, kliknij nazwę interfejsu sieciowego wymagane.  ![Interfejs sieciowy](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
2. Przejdź do **konfiguracje adresów Ip**, następnie kliknij pole nazwy wymaganych konfiguracji adresu IP. ![Elementy Ipconfiguration](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
3. Aby włączyć publiczny adres IP, kliknij **Włącz**. ![Włączyć adresu IP](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
4. Kliknij pozycję **Skonfiguruj wymagane ustawienia** > **Utwórz nową**. ![Utwórz nową](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
5. Wprowadź nazwę publicznego adresu, wybierz domyślne opcje dotyczące **jednostki SKU** i **przypisania**, następnie kliknij przycisk **OK**.
6. Teraz, aby zapisać zmiany, kliknij **Zapisz**.
7. Zamknięcie paneli i przejdź do **Przegląd** części maszyny wirtualnej do połączenia RDP.

## <a name="unable-to-connectrdpssh---vm-connect-button-available"></a>Nie można nawiązać połączenia ani protokołu RDP/SSH - dostępne przycisku Połącz z maszyną Wirtualną

Jeśli **Connect** przycisku w trybie Failover maszyny Wirtualnej na platformie Azure jest dostępna (nie szary), a następnie sprawdź **diagnostykę rozruchu** na maszynie wirtualnej i sprawdź błędy wymienione w [w tym artykule](../virtual-machines/windows/boot-diagnostics.md).

1. Jeśli maszyna wirtualna nie została uruchomiona, spróbuj przejść w tryb failover do starszego punktu odzyskiwania.
2. Jeśli aplikacja wewnątrz maszyny wirtualnej nie jest się, spróbuj przejść w tryb failover do punktu odzyskiwania spójnego na poziomie aplikacji.
3. Jeśli maszyna wirtualna jest przyłączona do domeny, a następnie upewnij się, że kontroler domeny działa dokładnie. Można to zrobić, postępując poniżej podane kroki:

    a. Utwórz nową maszynę wirtualną w tej samej sieci.

    b.  Upewnij się, że jest mógł dołączyć do tej samej domeny, na którym nieudane przez maszynę wirtualną oczekuje się, co pozwoli uzyskać.

    c. Jeśli kontroler domeny jest **nie** działa prawidłowo, spróbuj zalogowanie się na nieudane przez maszynę wirtualną przy użyciu konta administratora lokalnego.
4. Jeśli używasz niestandardowego serwera DNS, upewnij się, że jest dostępny. Można to zrobić, postępując poniżej podane kroki:

    a. Utwórz nową maszynę wirtualną w tej samej sieci i

    b. Sprawdź, czy maszyna wirtualna jest możliwość nazwa rozpoznawanie przy użyciu niestandardowego serwera DNS

>[!Note]
>Włączenie dowolnego ustawienia innego niż Diagnostyka rozruchu wymaga agenta maszyny Wirtualnej platformy Azure do zainstalowania na maszynie wirtualnej przed włączeniem trybu failover

## <a name="unexpected-shutdown-message-event-id-6008"></a>Nieoczekiwane zamknięcie wiadomości (6008 identyfikator zdarzenia)

W przypadku uruchamiania systemu Windows maszyny Wirtualnej po przejściu w tryb failover, jeśli zostanie wyświetlony komunikat nieoczekiwanego zamknięcia w odzyskanej maszyny Wirtualnej, oznacza to, stan zamknięcia maszyny Wirtualnej nie została przechwycona w punkcie odzyskiwania do trybu failover. Dzieje się tak, podczas odzyskiwania do punktu, gdy maszyna wirtualna ma nie została w pełni zamknięta.

Zwykle nie jest przyczyną problemu i zazwyczaj można zignorować dla niezaplanowanych operacji Failover. W przypadku planowanego trybu failover upewnij się, że maszyna wirtualna jest zamknięta prawidłowo przed trybu failover i zapewnić wystarczającą ilość czasu do czasu replikacji danych w sieci lokalnej do wysłania do platformy Azure. Następnie użyj **najnowsze** opcja [ekranu trybu Failover](site-recovery-failover.md#run-a-failover) tak, aby wszystkie oczekujące dane na platformie Azure są przetwarzane na punkt odzyskiwania, który jest następnie używany na potrzeby trybu failover maszyny Wirtualnej.

## <a name="retaining-drive-letter-after-failover"></a>Przechowywanie literę dysku, po włączeniu trybu failover
Aby zachować litery dysku na maszynach wirtualnych, po włączeniu trybu failover, można ustawić **zasad sieci SAN** dla maszyny wirtualnej w środowisku lokalnym do **OnlineAll**. [Dowiedz się więcej](https://support.microsoft.com/help/3031135/how-to-preserve-the-drive-letter-for-protected-virtual-machines-that-are-failed-over-or-migrated-to-azure).

## <a name="next-steps"></a>Kolejne kroki
- Rozwiązywanie problemów z [połączenia RDP z maszyną Wirtualną Windows](../virtual-machines/windows/troubleshoot-rdp-connection.md)
- Rozwiązywanie problemów z [połączenie SSH z maszyną Wirtualną systemu Linux](../virtual-machines/linux/detailed-troubleshoot-ssh-connection.md)

Jeśli potrzebujesz więcej pomocy, na następnie publikują zapytania [forum Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) lub pozostaw komentarz na końcu tego dokumentu. Mamy aktywnej społeczności użytkowników, które powinno być możliwe pomóc.
