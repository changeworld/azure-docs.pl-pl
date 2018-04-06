---
title: Wprowadzenie do platformy Azure połączenia obserwator Rozwiązywanie problemów z | Dokumentacja firmy Microsoft
description: Ta strona zawiera przegląd możliwości rozwiązywania problemów połączenia obserwator sieci
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: jdial
ms.openlocfilehash: 0268c7e54aa82df12243f98fd72de836fbc82070
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="introduction-to-connection-troubleshoot-in-azure-network-watcher"></a>Wprowadzenie do połączenia Rozwiązywanie problemów w obserwatora sieciowego Azure

Rozwiązywanie problemów z połączenie funkcji obserwatora sieciowego umożliwia bezpośrednie połączenie TCP z maszyny wirtualnej z maszyną wirtualną (VM), pełną nazwę domeny (FQDN), identyfikator URI, lub adres IPv4. Scenariusze sieci są skomplikowane, są implementowane przy użyciu grup zabezpieczeń sieci, zapór, trasy zdefiniowane przez użytkownika i zasobów udostępnianych przez platformę Azure. Złożonych konfiguracji należy trudne Rozwiązywanie problemów z łącznością. Obserwatora sieciowego pozwala skrócić czas, aby znaleźć i wykrywania problemów z łącznością. Wyniki zwrócone zapewniają wgląd w informacje czy problemem z łącznością przyczyną jest to platforma lub problem z konfiguracją użytkownika. Można sprawdzić łączności z [PowerShell](network-watcher-connectivity-powershell.md), [interfejsu wiersza polecenia Azure](network-watcher-connectivity-cli.md), i [interfejsu API REST](network-watcher-connectivity-rest.md).

> [!IMPORTANT]
> Rozwiązywanie problemów z połączenia wymaga, aby rozwiązać z maszyny Wirtualnej miało `AzureNetworkWatcherExtension` zainstalowane rozszerzenia maszyny Wirtualnej. Instalowanie rozszerzenia na maszynie Wirtualnej systemu Windows można znaleźć [rozszerzenie maszyny wirtualnej Azure sieci obserwatorów agenta dla systemu Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) i dla maszyny Wirtualnej systemu Linux, odwiedź [rozszerzenie maszyny wirtualnej Azure sieci obserwatorów agenta dla systemu Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Rozszerzenie nie jest wymagany dla docelowego punktu końcowego.

## <a name="response"></a>Odpowiedź

W poniższej tabeli przedstawiono właściwości zwrócony podczas rozwiązywania połączenia zakończył działanie.

|Właściwość  |Opis  |
|---------|---------|
|ConnectionStatus     | Stan sprawdzenia łączności z serwerem. Wyniki są **osiągalne** i **informujący**.        |
|AvgLatencyInMs     | Średni czas oczekiwania, podczas sprawdzania łączności (w milisekundach). (Tylko widoczne, gdy stan wyboru jest dostępny)        |
|MinLatencyInMs     | Minimalny czas oczekiwania, podczas sprawdzania łączności (w milisekundach). (Tylko widoczne, gdy stan wyboru jest dostępny)        |
|MaxLatencyInMs     | Maksymalny czas oczekiwania, podczas sprawdzania łączności (w milisekundach). (Tylko widoczne, gdy stan wyboru jest dostępny)        |
|ProbesSent     | Liczba sond wysyłane podczas sprawdzania. Wartość maksymalna to 100.        |
|ProbesFailed     | Liczbę sond, których nie powiodła się podczas sprawdzania. Wartość maksymalna to 100.        |
|Liczba przeskoków     | Przeskok przez ścieżkę przeskoku ze źródła do miejsca docelowego.        |
|Przeskoków []. Typ     | Typ zasobu. Możliwe wartości to **źródła**, **VirtualAppliance**, **VnetLocal**, i **Internet**.        |
|Przeskoków []. Identyfikator | Unikatowy identyfikator przeskoku.|
|Hops[].Address | Adres IP przeskoku.|
|Hops[].ResourceId | ResourceID przeskoku Jeśli przeskoków jest zasobem platformy Azure. Jeśli jest zasobu internetowego, ResourceID jest **Internet**. |
|Hops[].NextHopIds | Unikatowy identyfikator następnego przeskoku podjęte.|
|Przeskoków []. Problemy | Zbiór problemów, które wystąpiły podczas sprawdzania przy tym przeskoku. Jeśli nie było żadnych problemów, wartość jest pusta.|
|Hops[].Issues[].Origin | W bieżącej przeskoku, którym wystąpił problem. Możliwe wartości:<br/> **Liczba przychodzących** — problem jest łącze poprzedniej przeskoku do bieżącego przeskoku<br/>**Wychodzące** — problem jest łącze bieżącego przeskoku do następnego przeskoku<br/>**Lokalne** — problem jest bieżącym przeskoku.|
|Przeskoków []. Problemy z []. Ważność | Ważność wykrytego problemu. Możliwe wartości to **błąd** i **ostrzeżenie**. |
|Przeskoków []. Problemy z []. Typ |Typ Napotkano problem. Możliwe wartości: <br/>**CPU**<br/>**Pamięci**<br/>**GuestFirewall**<br/>**DnsResolution**<br/>**NetworkSecurityRule**<br/>**UserDefinedRoute** |
|Przeskoków []. Problemy z []. Kontekst |Szczegóły dotyczące Napotkano problem.|
|Przeskoków []. Problemy z []. Kontekst .key] |Klucz para klucz-wartość zwracana.|
|Hops[].Issues[].Context[].value |Zwracana wartość para klucz-wartość.|

Oto przykład problemu znaleziono przeskoku.

```json
"Issues": [
    {
        "Origin": "Outbound",
        "Severity": "Error",
        "Type": "NetworkSecurityRule",
        "Context": [
            {
                "key": "RuleName",
                "value": "UserRule_Port80"
            }
        ]
    }
]
```
## <a name="fault-types"></a>Typy błędów

Połączenie Rozwiązywanie problemów z typów błędów zwraca o połączeniu. Poniższa tabela zawiera listę bieżące typy błędów zwracane.

|Typ  |Opis  |
|---------|---------|
|Procesor CPU     | Wysokie użycie procesora CPU.       |
|Memory (Pamięć)     | Wysokie użycie pamięci.       |
|GuestFirewall     | Ruch jest zablokowany z powodu konfiguracji zapory maszyny wirtualnej.        |
|DNSResolution     | Rozpoznawanie nazw DNS nie powiodło się dla adresu docelowego.        |
|NetworkSecurityRule    | Ruch jest blokowany przez reguły NSG (zasada jest zwracany)        |
|UserDefinedRoute|Ruch zostanie usunięte ze względu na zdefiniowane przez użytkownika lub systemu trasy. |

### <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak rozwiązywać problemy z połączeniami przy użyciu [portalu Azure](network-watcher-connectivity-portal.md), [PowerShell](network-watcher-connectivity-powershell.md), [interfejsu wiersza polecenia Azure](network-watcher-connectivity-cli.md), lub [interfejsu API REST](network-watcher-connectivity-rest.md).