---
title: Rozwiązywanie problemów z trybu failover do błędów platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposoby rozwiązywania typowych problemów w przypadku przechodzenia w tryb failover na platformie Azure
services: site-recovery
documentationcenter: ''
author: ponatara
manager: abhemraj
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/06/2018
ms.author: ponatara
ms.openlocfilehash: ad8b69bfe6f3261f00cd33846efc86ce3b198954
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2018
ms.locfileid: "37919696"
---
# <a name="troubleshoot-errors-when-failing-over-a-virtual-machine-to-azure"></a>Rozwiązywanie problemów podczas przechodzenia w tryb failover maszyny wirtualnej na platformie Azure

Może pojawić się jeden z następujących błędów podczas wykonywania pracy w trybie failover maszyny wirtualnej na platformie Azure. Aby rozwiązać problemy, należy użyć opisane kroki poszczególnych warunków wystąpienia błędu.

## <a name="failover-failed-with-error-id-28031"></a>Tryb failover nie powiodło się z Identyfikator błędu 28031

Usługa Site Recovery nie był w stanie niepowodzenia tworzenia maszyny wirtualnej platformy Azure. Może się zdarzyć z jednego z następujących powodów:

* Nie ma wystarczającego limitu przydziału, które są dostępne do utworzenia maszyny wirtualnej: dostępny limit przydziału można sprawdzić, przechodząc do subskrypcji -> użycie i przydziały. Możesz otworzyć [nowe żądanie pomocy technicznej](http://aka.ms/getazuresupport) Aby zwiększyć limit przydziału.

* Próbujesz trybu failover maszyny wirtualne rozmiar różnych rodzin w tym samym zestawie dostępności. Upewnij się, wybierz ten sam rodzina rozmiarów dla wszystkich maszyn wirtualnych w tym samym zestawie dostępności. Zmiana rozmiaru, przechodząc do ustawień obliczenia i sieć maszyny wirtualnej, a następnie ponów próbę pracy awaryjnej.

* Brak zasad w ramach subskrypcji, która uniemożliwia tworzenie maszyny wirtualnej. Zmień zasady aby umożliwić tworzenie maszyny wirtualnej, a następnie ponów próbę pracy awaryjnej.

## <a name="failover-failed-with-error-id-28092"></a>Tryb failover nie powiodło się z Identyfikator błędu 28092

Usługa Site Recovery nie mogła utworzyć interfejsu sieciowego dla nieudane przez maszynę wirtualną. Upewnij się, że masz wystarczającego limitu przydziału, które są dostępne w celu utworzenia interfejsów sieciowych w ramach subskrypcji. Dostępny limit przydziału można sprawdzić, przechodząc do subskrypcji -> użycie i przydziały. Możesz otworzyć [nowe żądanie pomocy technicznej](http://aka.ms/getazuresupport) Aby zwiększyć limit przydziału. Jeśli masz wystarczającego limitu przydziału, a następnie może to być tymczasowy wysyłania, spróbuj ponownie wykonać operację. Jeśli problem będzie nadal występować po ponawianiu prób, pozostaw komentarz na końcu tego dokumentu.  

## <a name="failover-failed-with-error-id-70038"></a>Tryb failover nie powiodło się z Identyfikator błędu 70038

Usługa Site Recovery nie mógł utworzyć nie powiodło się za pośrednictwem klasycznej maszyny wirtualnej na platformie Azure. Może się zdarzyć, ponieważ:

* Jeden z zasobów, takich jak sieć wirtualną, która jest wymagana dla maszyny wirtualnej, która ma zostać utworzony nie istnieje. Tworzenie sieci wirtualnej, zgodnie z postanowieniami w obszarze ustawień obliczenia i sieć maszyny wirtualnej lub zmodyfikuj ustawienia sieci wirtualnej, która już istnieje, a następnie ponów próbę pracy awaryjnej.

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>Nie można nawiązać połączenia ani protokołu RDP/SSH nieudane przez maszynę wirtualną ze względu na szary przycisk Połącz na maszynie wirtualnej

Jeśli przycisk Połącz jest wyszarzony i nie masz połączenia Expressroute lub sieci VPN typu lokacja-lokacja połączenie, następnie na platformie Azure

1. Przejdź do **maszyny wirtualnej** > **sieć**, kliknij nazwę interfejsu sieciowego wymagane.  ![Interfejs sieciowy](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
2. Przejdź do **konfiguracje adresów Ip**, następnie kliknij pole nazwy wymaganych konfiguracji adresu IP. ![Elementy Ipconfiguration](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
3. Aby włączyć publiczny adres IP, kliknij **Włącz**. ![Włączyć adresu IP](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
4. Kliknij pozycję **Skonfiguruj wymagane ustawienia** > **Utwórz nową**. ![Utwórz nową](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
5. Wprowadź nazwę publicznego adresu, wybierz domyślne opcje dotyczące **jednostki SKU** i **przypisania**, następnie kliknij przycisk **OK**.
6. Teraz, aby zapisać zmiany, kliknij **Zapisz**.
7. Zamknięcie paneli i przejdź do **Przegląd** części maszyny wirtualnej do połączenia RDP.

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-even-though-connect-button-is-available-not-grayed-out-on-the-virtual-machine"></a>Nie można nawiązać połączenia ani protokołu RDP/SSH nie powiodło się za pośrednictwem wirtualnej maszyny mimo Połącz przycisk jest dostępny (nie szary) na maszynie wirtualnej

Sprawdź **diagnostykę rozruchu** na maszynie wirtualnej i sprawdź błędy wymienione w tym artykule.

1. Jeśli maszyna wirtualna nie została uruchomiona, spróbuj przejść w tryb failover do starszego punktu odzyskiwania.
2. Jeśli aplikacja wewnątrz maszyny wirtualnej nie jest się, spróbuj przejść w tryb failover do punktu odzyskiwania spójnego na poziomie aplikacji.
3. Jeśli maszyna wirtualna jest przyłączona do domeny, a następnie upewnij się, że kontroler domeny działa dokładnie. Można to zrobić, postępując poniżej podane kroki.
    a. Utwórz nową maszynę wirtualną w tej samej sieci

    b.  Upewnij się, że jest mógł dołączyć do tej samej domeny, na którym nieudane przez maszynę wirtualną oczekuje się, co pozwoli uzyskać.

    c. Jeśli kontroler domeny jest **nie** działa prawidłowo, spróbuj zalogowanie się na nieudane przez maszynę wirtualną przy użyciu konta administratora lokalnego
4. Jeśli używasz niestandardowego serwera DNS, upewnij się, że jest dostępny. Można to zrobić, postępując poniżej podane kroki.
    a. Utwórz nową maszynę wirtualną w tej samej sieci oraz b. Sprawdź, czy maszyna wirtualna jest możliwość nazwa rozpoznawanie przy użyciu niestandardowego serwera DNS

>[!Note]
>Włączenie dowolnego ustawienia innego niż Diagnostyka rozruchu wymaga agenta maszyny Wirtualnej platformy Azure do zainstalowania na maszynie wirtualnej przed włączeniem trybu failover

## <a name="next-steps"></a>Kolejne kroki

Jeśli potrzebujesz więcej pomocy, na następnie publikują zapytania [forum Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) lub pozostaw komentarz na końcu tego dokumentu. Mamy aktywnej społeczności użytkowników, które powinno być możliwe pomóc.
