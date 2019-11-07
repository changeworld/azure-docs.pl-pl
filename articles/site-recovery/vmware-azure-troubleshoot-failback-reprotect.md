---
title: Rozwiązywanie problemów z powrót po awarii w Azure Site Recovery ramach odzyskiwania po awarii maszyny wirtualnej programu VMware
description: W tym artykule opisano sposoby rozwiązywania problemów z odzyskiwaniem po awarii i ochroną podczas odzyskiwania po awarii maszyny wirtualnej VMware na platformę Azure przy użyciu Azure Site Recovery.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: b597ecb67ab30c8617029fe741af1014444a9b70
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693154"
---
# <a name="troubleshoot-failback-to-on-premises-from-azure"></a>Rozwiązywanie problemów z powrotem w środowisku lokalnym z platformy Azure

W tym artykule opisano sposób rozwiązywania problemów, które mogą wystąpić w przypadku powrotu po awarii maszyn wirtualnych platformy Azure do lokalnej infrastruktury VMware po przejściu do trybu failover na platformie Azure przy użyciu [Azure Site Recovery](site-recovery-overview.md).

Powrót po awarii dotyczy głównie dwóch głównych kroków. W pierwszym kroku po przejściu do trybu failover należy ponownie włączyć ochronę maszyn wirtualnych platformy Azure w środowisku lokalnym, aby umożliwić ich replikację. Drugim krokiem jest uruchomienie trybu failover z platformy Azure w celu powrotu po awarii do lokacji lokalnej.

## <a name="troubleshoot-reprotection-errors"></a>Rozwiązywanie problemów z błędami ochrony

Ta sekcja zawiera szczegółowe informacje o typowych błędach ochrony i sposobach ich korygowania.

### <a name="error-code-95226"></a>Kod błędu 95226

**Ponowne włączanie ochrony nie powiodło się, ponieważ maszyna wirtualna platformy Azure nie mogła nawiązać połączenia z lokalnym serwerem konfiguracji.**

Ten błąd występuje, gdy:

* Maszyna wirtualna platformy Azure nie może nawiązać połączenia z lokalnym serwerem konfiguracji. Nie można odnaleźć maszyny wirtualnej i zarejestrować jej na serwerze konfiguracji.
* Usługa aplikacji InMage Scout nie jest uruchomiona na maszynie wirtualnej platformy Azure po przejściu w tryb failover. Usługa jest wymagana do komunikacji z lokalnym serwerem konfiguracji.

Aby rozwiązać ten problem:

* Sprawdź, czy sieć maszyny wirtualnej platformy Azure umożliwia maszynie wirtualnej platformy Azure komunikowanie się z lokalnym serwerem konfiguracji. Można skonfigurować sieć VPN typu lokacja-lokacja w lokalnym centrum danych lub skonfigurować połączenie usługi Azure ExpressRoute z prywatną usługą komunikacji równorzędnej w sieci wirtualnej maszyny wirtualnej platformy Azure.
* Jeśli maszyna wirtualna może komunikować się z lokalnym serwerem konfiguracji, zaloguj się do maszyny wirtualnej. Następnie sprawdź usługę aplikacji InMage Scout. Jeśli zobaczysz, że nie jest uruchomiony, uruchom usługę ręcznie. Sprawdź, czy typ uruchomienia usługi jest ustawiony na wartość **automatycznie**.

### <a name="error-code-78052"></a>Kod błędu 78052

**Nie można ukończyć ochrony maszyny wirtualnej.**

Ten problem może wystąpić, jeśli istnieje już maszyna wirtualna o takiej samej nazwie na głównym serwerze docelowym, do której nastąpi powrót po awarii.

Aby rozwiązać ten problem:

* Wybierz inny główny serwer docelowy na innym hoście, tak aby program reprotection utworzył maszynę na innym hoście, gdzie nazwy nie kolidują.
* Można również użyć vMotion, aby przenieść główny cel do innego hosta, na którym nie nastąpiła kolizja nazw. Jeśli istniejąca maszyna wirtualna jest niedziałaną maszyną, zmień jej nazwę, tak aby nowa maszyna wirtualna mogła zostać utworzona na tym samym hoście ESXi.


### <a name="error-code-78093"></a>Kod błędu 78093

**Maszyna wirtualna nie jest uruchomiona, jest w stanie zawieszonym lub jest niedostępna.**

Aby rozwiązać ten problem:

Aby ponownie chronić maszynę wirtualną w trybie failover, maszyna wirtualna platformy Azure musi być uruchomiona, aby usługa mobilności rejestrował się na lokalnym serwerze konfiguracji i mogła rozpocząć replikację, komunikując się z serwerem przetwarzania. Jeśli komputer znajduje się w nieprawidłowej sieci lub nie jest uruchomiony (nie odpowiada ani nie zostanie zamknięty), serwer konfiguracji nie może nawiązać połączenia z usługą mobilności na maszynie wirtualnej, aby rozpocząć ponowną ochronę.

* Uruchom ponownie maszynę wirtualną, aby można było rozpocząć komunikację z powrotem w środowisku lokalnym.
* Uruchom ponownie zadanie ponownego włączania ochrony po uruchomieniu maszyny wirtualnej platformy Azure.

### <a name="error-code-8061"></a>Kod błędu 8061

**Magazyn danych nie jest dostępny z hosta ESXi.**

Sprawdź [wymagania wstępne głównego elementu docelowego i obsługiwane magazyny danych](vmware-azure-reprotect.md#deploy-a-separate-master-target-server) na potrzeby powrotu po awarii.


## <a name="troubleshoot-failback-errors"></a>Rozwiązywanie błędów powrotu po awarii

W tej sekcji opisano typowe błędy, które mogą wystąpić podczas powrotu po awarii.

### <a name="error-code-8038"></a>Kod błędu 8038

**Nie można wyświetlić lokalnej maszyny wirtualnej z powodu błędu.**

Ten problem występuje, gdy lokalna maszyna wirtualna zostanie przeprowadzona na hoście, na którym nie zainicjowano wystarczającej ilości pamięci. 

Aby rozwiązać ten problem:

* Zainicjuj obsługę więcej pamięci na hoście ESXi.
* Ponadto możesz użyć vMotion, aby przenieść maszynę wirtualną do innego hosta ESXi, który ma wystarczającą ilość pamięci, aby uruchomić maszynę wirtualną.
