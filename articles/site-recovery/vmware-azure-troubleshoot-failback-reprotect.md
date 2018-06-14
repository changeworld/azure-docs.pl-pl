---
title: Rozwiązywanie błędów podczas powrotu po awarii maszyn wirtualnych Azure do programu VMware lokalnych z usługą Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposoby rozwiązywania typowych problemów powrotu po awarii i ponowne włączenie ochrony podczas powrotu po awarii do programu VMware z platformy Azure przy użyciu usługi Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/09/2018
ms.author: rajanaki
ms.openlocfilehash: 480c3524ad4fb8a8c6ea02f09b8d27f254da9b08
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2018
ms.locfileid: "29941039"
---
# <a name="troubleshoot-failback-from-azure-to-vmware"></a>Rozwiązywanie problemów z powrotu po awarii z platformy Azure do programu VMware

W tym artykule opisano sposób rozwiązywania problemów, które mogą wystąpić podczas nie zostanie ponownie Azure maszyn wirtualnych o lokalnej infrastrukturze firmy VMware po w tryb failover na platformie Azure przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md).

Powrót po awarii zasadniczo obejmuje dwa podstawowe kroki. Dla pierwszego stopnia po przejściu w tryb failover, należy włączyć ją ponownie maszynach wirtualnych platformy Azure do środowiska lokalnego, aby rozpoczęciu replikowanie. Drugim krokiem jest do uruchamiania trybu failover z platformy Azure na powrót po awarii do lokacji lokalnej.

## <a name="troubleshoot-reprotection-errors"></a>Rozwiązywanie problemów z błędami zastosowania

Ta sekcja zawiera szczegóły typowe zastosowania błędów i rozwiązania ich.

### <a name="error-code-95226"></a>Kod błędu 95226

**Ponownej ochrony nie powiodła się, ponieważ maszyny wirtualnej platformy Azure nie mógł uzyskać dostęp do lokalnego serwera konfiguracji.**

Ten błąd występuje, gdy:

* Maszyna wirtualna platformy Azure można nawiązać połączenia z lokalnego serwera konfiguracji. Maszyny Wirtualnej nie można odnaleźć i zarejestrowany na serwerze konfiguracji.
* Usługa aplikacji InMage Scout nie jest uruchomiony na maszynie Wirtualnej Azure po pracy awaryjnej. Usługa jest wymagana do komunikacji z lokalnego serwera konfiguracji.

Aby rozwiązać ten problem:

* Sprawdź, czy sieć wirtualna Azure umożliwia maszyny Wirtualnej platformy Azure do komunikowania się z lokalnego serwera konfiguracji. Można skonfigurować sieci VPN lokacja lokacja w lokalnym centrum danych lub skonfigurować Azure ExpressRoute połączenia z prywatną komunikację równorzędną w sieci wirtualnej maszyny wirtualnej Azure.
* Jeśli maszyna wirtualna może komunikować się z lokalnego serwera konfiguracji, zaloguj się do maszyny Wirtualnej. Następnie sprawdź, czy usługa aplikacji InMage Scout. Jeśli zobaczysz, że nie jest uruchomiona, uruchom usługę ręcznie. Sprawdź, czy typ uruchomienia usługi ma ustawioną wartość **automatyczne**.

### <a name="error-code-78052"></a>Kod błędu 78052

**Nie można ukończyć włączania ochrony dla maszyny wirtualnej.**

Ten problem może się zdarzyć, jeśli istnieje już maszyna wirtualna o tej samej nazwie na głównym serwerze docelowym z którym powrotu po awarii ponownie.

Aby rozwiązać ten problem:

* Wybierz innego głównego serwera docelowego na inny host, dzięki czemu przełączonej tworzy komputera na inny host, gdzie nie powodują kolizję nazw.
* Można również Użyj vMotion można przenieść główny cel do innego hosta, gdzie kolizję nazw nie nastąpi. Jeśli istniejącej maszyny Wirtualnej jest stray maszyny, należy ją zmienić, aby na tym samym hoście ESXi można utworzyć nowej maszyny Wirtualnej.


### <a name="error-code-78093"></a>Kod błędu 78093

**Maszyna wirtualna nie jest uruchomiona, w stanie zawieszone, lub jest niedostępny.**

Aby rozwiązać ten problem:

Włączyć ją ponownie przełączona w tryb failover maszyny Wirtualnej, musi być uruchomiona maszyny Wirtualnej Azure tak, aby uruchomić usługi mobilności rejestry z konfiguracji serwera lokalnego i umożliwia replikowanie komunikując się z serwerem przetwarzania. Jeśli komputer jest na nieprawidłowych sieci lub nie jest uruchomiona (zawiesił stanu lub zamykania w dół), serwer konfiguracji można nawiązać połączenia z usługą mobilności na maszynie Wirtualnej, aby rozpocząć przełączonej.

* Ponowne uruchomienie maszyny Wirtualnej tak, aby umożliwić komunikację wstecz lokalnymi.
* Po uruchomieniu maszyny wirtualnej platformy Azure, należy ponownie uruchomić zadanie ponownej ochrony.

### <a name="error-code-8061"></a>Kod błędu 8061

**Magazyn danych jest niedostępny z hosta ESXi.**

Sprawdź [wzorca docelowego wymagania wstępne i obsługiwane magazyny danych](vmware-azure-reprotect.md#deploy-a-separate-master-target-server) powrotu po awarii.


## <a name="troubleshoot-failback-errors"></a>Rozwiązywanie problemów w przypadku powrotu po awarii

W tej sekcji opisano typowe błędy, które mogą wystąpić podczas powrotu po awarii.

### <a name="error-code-8038"></a>Kod błędu 8038

**Nie można wyświetlić na lokalnej maszynie wirtualnej z powodu błędu.**

Ten problem spowodowany lokalnej maszyny Wirtualnej jest włączane na hoście, który nie ma wystarczającej ilości pamięci udostępnione. 

Aby rozwiązać ten problem:

* Udostępnij więcej pamięci na hoście ESXi.
* Ponadto umożliwia vMotion Przenieś maszynę Wirtualną do innego hosta ESXi, który ma wystarczającą ilość pamięci do rozruchu maszyny Wirtualnej.
