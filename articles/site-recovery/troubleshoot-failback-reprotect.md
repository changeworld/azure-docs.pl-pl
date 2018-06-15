---
title: Rozwiązywanie błędów podczas powrotu po awarii do środowiska lokalnego z platformy Azure, a następnie Obejmij ochroną na platformie Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposoby rozwiązywania typowych problemów w niepowodzenie powrót do lokalnej z platformy Azure i podczas ponownej ochrony
services: site-recovery
documentationcenter: ''
author: rajani-janaki-ram
manager: gauravd
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/19/2017
ms.author: rajanaki
ms.openlocfilehash: d487c1fcf7fb6444c2b8489df839a6450715ae0a
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2018
ms.locfileid: "29677413"
---
# <a name="troubleshoot-errors-reported-during-the-process-of-failback"></a>Rozwiązywanie błędów podczas powrotu po awarii
Powrót po awarii zasadniczo obejmuje dwa podstawowe kroki. Jeden Włącz ponownie ochronę maszyn wirtualnych z platformy Azure do środowiska lokalnego, drugi ma dokonać powrót po awarii z platformy Azure do środowiska lokalnego.

## <a name="troubleshoot-errors-when-reprotecting-a-virtual-machine-back-to-on-premises-after-failover"></a>Rozwiązywanie problemów podczas ponownej ochrony maszyny wirtualnej do lokalnego, po trybu failover
Może pojawić się jeden z następujących błędów podczas wykonywania ponownej ochrony maszyny wirtualnej na platformie Azure. Aby rozwiązać problemy, użyj opisane kroki dla każdego warunku błędu.


### <a name="error-code-95226"></a>Kod błędu 95226

*Ponownej ochrony nie powiodła się, ponieważ maszyny wirtualnej platformy Azure nie mógł uzyskać dostęp do lokalnego serwera konfiguracji.*

Dzieje się tak podczas 
1. Maszyny wirtualnej platformy Azure nie może skontaktować się z lokalnego serwera konfiguracji i dlatego nie można być wykryte i zarejestrowany na serwerze konfiguracji. 
2. Usługa InMage Scout aplikacji na maszynie wirtualnej Azure, która musi działać do komunikowania się do lokalnego serwera konfiguracji może nie działać po pracy awaryjnej.

Aby rozwiązać ten problem
1. Należy się upewnić, że sieć maszyny wirtualnej platformy Azure jest skonfigurowany w taki sposób, że maszyna wirtualna może się komunikować z lokalnego serwera konfiguracji. W tym celu należy skonfigurować sieci VPN między lokacjami do lokalnego centrum danych lub skonfiguruj połączenie ExpressRoute z prywatnej komunikacji równorzędnej w sieci wirtualnej platformy Azure maszyny wirtualnej. 
2. Jeśli masz już skonfigurowane w taki sposób, że maszyny wirtualne Azure mogą komunikować się z lokalnego serwera konfiguracji sieci, następnie zaloguj się do maszyny wirtualnej i sprawdź "Usługa aplikacji InMage Scout". Jeśli zauważysz, że nie jest uruchomiona usługa aplikacji InMage Scout, uruchom usługę ręcznie i sprawdź, czy typ uruchamiania usługi jest ustawiony na automatyczny.

### <a name="error-code-78052"></a>Kod błędu 78052
Ponownej ochrony kończy się niepowodzeniem z komunikatem o błędzie: *maszyny wirtualnej nie można ukończyć włączania ochrony.*

Może to nastąpić z dwóch przyczyn
1. Maszyny wirtualnej, które są ponownej ochrony jest Windows Server 2016. Obecnie ten system operacyjny nie jest obsługiwana w przypadku powrotu po awarii, ale będzie wkrótce obsługiwany.
2. Istnieje już maszyny wirtualnej o tej samej nazwie na głównym serwerze docelowym kończy się niepowodzeniem do.

Aby rozwiązać ten problem można wybrać innego głównego serwera docelowego na innego hosta, dzięki czemu ponownej ochrony utworzy komputera na inny host, gdzie nie powodują konfliktu nazw. Można również vMotion główny cel do innego hosta, na którym nie nastąpi kolizję nazw. Jeśli istniejącej maszyny wirtualnej jest stray maszyny, można po prostu ją zmienić tak, aby na tym samym hoście ESXi można utworzyć nowej maszyny wirtualnej.

### <a name="error-code-78093"></a>Kod błędu 78093

*Maszyna wirtualna nie jest uruchomiona, w stanie zawieszone, lub jest niedostępny.*

Włącz ponownie ochronę nieudanej przez maszynę wirtualną do lokalnej, należy uruchamiania maszyny wirtualnej platformy Azure. Jest to, aby usługa mobilności rejestruje z serwerem konfiguracji lokalnych i rozpocząć replikację komunikując się z serwerem przetwarzania. Jeśli komputer jest niepoprawna sieci lub nie działa (zawiesić lub zamknięcie), serwer konfiguracji nie można osiągnąć usługi mobilności na maszynie wirtualnej, aby rozpocząć ponownej ochrony. Można ponownie uruchomić maszynę wirtualną tak, aby umożliwić komunikację wstecz lokalnymi. Uruchom ponownie zadanie ponownej ochrony po uruchomieniu maszyny wirtualnej platformy Azure

### <a name="error-code-8061"></a>Kod błędu 8061

*Magazyn danych jest niedostępny z hosta ESXi.*

Zapoznaj się [wzorca wstępne docelowej](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server) i [obsługuje datastores](site-recovery-how-to-reprotect.md#what-datastore-types-are-supported-on-the-on-premises-esxi-host-during-failback) powrotu po awarii


## <a name="troubleshoot-errors-when-performing-a-failback-of-an-azure-virtual-machine-back-to-on-premises"></a>Rozwiązywanie błędów podczas powrotu po awarii z maszyny wirtualnej platformy Azure do lokalnej
Może pojawić się jeden z następujących błędów podczas wykonywania powrotu po awarii maszyny wirtualnej platformy Azure do lokalnego. Aby rozwiązać problemy, użyj opisane kroki dla każdego warunku błędu.

### <a name="error-code-8038"></a>Kod błędu 8038

*Nie można wyświetlić na lokalnej maszynie wirtualnej z powodu błędu*

Dzieje się tak, gdy na lokalnej maszynie wirtualnej jest włączane na hoście, który nie ma wystarczającej ilości pamięci udostępnione.

Aby rozwiązać ten problem

1. Umożliwia obsługę większej ilości pamięci na hoście ESXi.
1. vMotion maszyny Wirtualnej do innego hosta ESXi, który ma wystarczającą ilość pamięci do rozruchu maszyny wirtualnej.