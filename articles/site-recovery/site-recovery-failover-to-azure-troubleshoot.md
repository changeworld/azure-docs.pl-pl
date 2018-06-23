---
title: Rozwiązywanie problemów z trybu failover na awarie Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposoby rozwiązywania typowych problemów w przypadku awarii na platformie Azure
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
ms.date: 03/09/2018
ms.author: ponatara
ms.openlocfilehash: 838eac510fc17d56f808f541f4e205a279f63c56
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36318895"
---
# <a name="troubleshoot-errors-when-failing-over-a-virtual-machine-to-azure"></a>Rozwiązywanie problemów z błędami przechodzenie w tryb failover maszyny wirtualnej na platformie Azure

Może pojawić się jeden z następujących błędów podczas wykonywania pracy w trybie failover maszyny wirtualnej na platformie Azure. Aby rozwiązać problemy, użyj opisane kroki dla każdego warunku błędu.

## <a name="failover-failed-with-error-id-28031"></a>Tryb failover nie powiodło się z Identyfikator błędu 28031

Usługa Site Recovery nie mógł utworzyć nieudanej przez maszynę wirtualną na platformie Azure. Może się zdarzyć z jednego z następujących powodów:

* Nie ma wystarczającego przydziału do utworzenia maszyny wirtualnej: Sprawdź dostępny limit przydziału, przechodząc do subskrypcji -> użycia + przydziałów. Możesz otworzyć [nowe żądanie pomocy technicznej](http://aka.ms/getazuresupport) Aby zwiększyć przydział.

* Próbujesz trybu failover maszyny wirtualne rodzin inny rozmiar w tym samym zestawie dostępności. Upewnij się, wybrać tej samej rodziny rozmiar dla wszystkich maszyn wirtualnych w tym samym zestawie dostępności. Zmień rozmiar, przechodząc do ustawień obliczania i sieci maszyny wirtualnej, a następnie ponów próbę pracy awaryjnej.

* Brak zasad na subskrypcję, która uniemożliwia tworzenie maszyny wirtualnej. Zmień zasady Zezwalaj na tworzenie maszyny wirtualnej, a następnie spróbuj ponownie trybu failover.

## <a name="failover-failed-with-error-id-28092"></a>Tryb failover nie powiodło się z Identyfikator błędu 28092

Usługa Site Recovery nie mógł utworzyć interfejsu sieciowego dla nieudane przez maszynę wirtualną. Upewnij się, że masz wystarczającego przydziału do utworzenia interfejsów sieciowych w subskrypcji. Sprawdź dostępny limit przydziału, przechodząc do subskrypcji -> użycia + przydziałów. Możesz otworzyć [nowe żądanie pomocy technicznej](http://aka.ms/getazuresupport) Aby zwiększyć przydział. Jeśli masz wystarczającego limitu przydziału, może to być tymczasowy wystawiania, spróbuj ponownie wykonać operację. Jeśli problem będzie nadal występował mimo ponownych prób, następnie zostaw komentarz na końcu tego dokumentu.  

## <a name="failover-failed-with-error-id-70038"></a>Tryb failover nie powiodło się z Identyfikator błędu 70038

Usługa Site Recovery nie mógł utworzyć nieudanej za pośrednictwem klasycznego maszyny wirtualnej platformy Azure. Może się zdarzyć, ponieważ:

* Jeden z zasobów, takich jak sieć wirtualna, która jest wymagana do utworzenia maszyny wirtualnej nie istnieje. Tworzenie sieci wirtualnej, zgodnie z ustawień obliczania i sieci maszyny wirtualnej lub zmodyfikować to ustawienie, aby sieć wirtualna, która już istnieje, a następnie ponów próbę pracy awaryjnej.

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>Nie można połączyć/protokołu RDP/SSH do nieudane za pośrednictwem maszyny wirtualnej ze względu na wygaszone, przycisk Połącz na maszynie wirtualnej

Jeśli jest szary przycisk Połącz i nie połączono z platformy Azure za pośrednictwem Express Route lub sieci VPN typu lokacja-lokacja, następnie połączenie,

1. Przejdź do **maszyny wirtualnej** > **sieci**, kliknij nazwę interfejsu sieciowego wymagane.  ![Interfejs sieciowy](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
2. Przejdź do **konfiguracje adresów Ip**, następnie kliknij polecenie w polu nazwy konfiguracji adresu IP. ![Elementy Ipconfiguration](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
3. Aby włączyć publicznego adresu IP, kliknij na **włączyć**. ![Włączyć adresu IP](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
4. Polecenie **Skonfiguruj wymagane ustawienia** > **Utwórz nowy**. ![Utwórz nową](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
5. Wprowadź nazwę publicznego adresu, wybierz polecenie Opcje domyślne **SKU** i **przypisania**, następnie kliknij przycisk **OK**.
6. Teraz, aby zapisać zmiany, kliknij przycisk **zapisać**.
7. Zamknięcie paneli i przejdź do **omówienie** sekcji maszyny wirtualnej w celu połączenia RDP.

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-even-though-connect-button-is-available-not-grayed-out-on-the-virtual-machine"></a>Nie można połączyć/protokołu RDP/SSH do nieudane za pośrednictwem wirtualnej maszynie mimo że Connect przycisk jest dostępny (nie wyszarzona) na maszynie wirtualnej

Sprawdź **diagnostyki rozruchu** na maszynie wirtualnej i sprawdź, czy błędy wymienione w tym artykule.

1. Jeśli maszyna wirtualna nie została uruchomiona, spróbuj przechodzenie w tryb failover do starszych punktu odzyskiwania.
2. Jeśli aplikacja na maszynie wirtualnej nie jest zapasowej, spróbuj awarii do punktów odzyskiwania zapewniających spójność aplikacji.
3. Jeśli maszyna wirtualna jest przyłączony do domeny, upewnij się, że ten kontroler domeny działa prawidłowo. Można to zrobić, postępując poniżej podane kroki.
    a. Tworzenie nowej maszyny wirtualnej w tej samej sieci

    b.  Upewnij się, że jest w stanie dołączyć do tej samej domeny, na którym nie powiodło się na maszynie wirtualnej oczekuje znaleziona.

    c. Jeśli kontroler domeny jest **nie** działa prawidłowo, spróbuj podczas logowania się na nieudane przez maszynę wirtualną przy użyciu konta administratora lokalnego
4. Jeśli używasz niestandardowego serwera DNS, a następnie upewnij się, że jest dostępny. Można to zrobić, postępując poniżej podane kroki.
    a. Utwórz nową maszynę wirtualną w tej samej sieci i b. Sprawdź, czy maszyna wirtualna jest możliwość nazwa rozpoznawanie przy użyciu niestandardowego serwera DNS

>[!Note]
>Włączenie ustawienia dowolnej innej niż diagnostyki rozruchu wymagają Agent maszyny Wirtualnej Azure musi zostać zainstalowany na maszynie wirtualnej przed przejście w tryb failover

## <a name="next-steps"></a>Kolejne kroki

Jeśli potrzebujesz więcej pomocy, na następnie przesłanie kwerendy [forum usługi Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) lub zostaw komentarz na końcu tego dokumentu. Mamy aktywnej społeczności, który powinien być w stanie ułatwi.
