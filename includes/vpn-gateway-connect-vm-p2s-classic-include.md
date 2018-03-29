---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e49cfe786272b34675ca377808e2961e61a757e4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
Z maszyną wirtualną, która jest wdrażana w sieci wirtualnej, można się połączyć, tworząc połączenie pulpitu zdalnego z tą maszyną. Najlepszym sposobem na zweryfikowanie, czy można połączyć się z maszyną wirtualną, jest połączenie się z nią za pomocą jej prywatnego adresu IP, a nie nazwy komputera. W ten sposób można przetestować możliwość połączenia się, a nie poprawność skonfigurowania rozpoznawania nazw. 

1. Zlokalizuj prywatny adres IP dla swojej maszyny wirtualnej. Prywatny adres IP maszyny wirtualnej można znaleźć, zaglądając do właściwości maszyny wirtualnej w witrynie Azure Portal lub używając programu PowerShell.
2. Sprawdź, czy masz połączenie z siecią wirtualną przez połączenie sieci VPN punkt-lokacja. 
3. Otwórz program Podłączanie pulpitu zdalnego, wpisując „RDP” lub „Podłączanie pulpitu zdalnego” w polu wyszukiwania na pasku zadań, a następnie wybierając pozycję Podłączanie pulpitu zdalnego. Program Podłączanie pulpitu zdalnego możesz także otworzyć za pomocą polecenia „mstsc” w programie PowerShell. 
3. W programie Podłączanie pulpitu zdalnego wprowadź prywatny adres IP maszyny wirtualnej. Możesz kliknąć pozycję „Pokaż opcje”, aby dostosować dodatkowe ustawienia. Następnie nawiąż połączenie.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>Jak rozwiązywać problemy z połączeniem RDP z maszyną wirtualną

Jeśli masz problemy z łączeniem się z maszyną wirtualną za pośrednictwem połączenia sieci VPN, istnieje kilka rzeczy, które możesz sprawdzić. Aby uzyskać więcej informacji na temat rozwiązywania problemów, zobacz [Rozwiązywanie problemów z połączeniami pulpitu zdalnego z maszyną wirtualną](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md).

- Sprawdź, czy połączenie sieci VPN zostało pomyślnie nawiązane.
- Sprawdź, czy łączysz się z prywatnym adresem IP maszyny wirtualnej.
- Użyj narzędzia „ipconfig”, aby sprawdzić adres IPv4 przypisany do karty Ethernet na komputerze, z którego jest nawiązywane połączenie. Jeśli adres IP znajduje się w zakresie adresów sieci wirtualnej, z którą jest nawiązywane połączenie, lub w zakresie adresów puli VPNClientAddressPool, jest to określane jako nakładająca się przestrzeń adresowa. Kiedy przestrzeń adresowa nakłada się w ten sposób, ruch sieciowy nie dociera do platformy Azure, tylko pozostaje w sieci lokalnej.
- Jeśli możesz połączyć się z maszyną wirtualną za pomocą prywatnego adresu IP, ale nie za pomocą nazwy komputera, sprawdź, czy usługa DNS została prawidłowo skonfigurowana. Aby uzyskać więcej informacji na temat tego, jak działa rozpoznawanie nazw dla maszyn wirtualnych, zobacz [Name Resolution for VMs](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) (Rozpoznawanie nazw dla maszyn wirtualnych).
- Sprawdź, czy pakiet konfiguracji klienta sieci VPN został wygenerowany po określeniu adresów IP serwera DNS dla sieci wirtualnej. Jeśli adresy IP serwera DNS zostały zaktualizowane, wygeneruj i zainstaluj nowy pakiet konfiguracji klienta sieci VPN.