---
title: Rozwiązywanie problemów z wprowadzeniem do połączenia obserwatorów sieci platformy Azure | Dokumentacja firmy Microsoft
description: Ta strona zawiera omówienie funkcji rozwiązywania problemów z połączenia usługi Network Watcher
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: kumud
ms.openlocfilehash: 9510905f67ee943b4b1dfa5a14c2753efac39da7
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64705822"
---
# <a name="introduction-to-connection-troubleshoot-in-azure-network-watcher"></a>Wprowadzenie do połączeń Rozwiązywanie problemów w usłudze Azure Network Watcher

Rozwiązywanie problemów z połączeniami funkcji usługi Network Watcher oferuje możliwość sprawdzenia bezpośredniego połączenia TCP z maszyny wirtualnej do maszyny wirtualnej (VM), w pełni kwalifikowaną nazwę domeny (FQDN), identyfikator URI, lub adres IPv4. Złożone scenariusze sieci, są one wykonywane przy użyciu sieciowych grup zabezpieczeń, zapór, trasy zdefiniowane przez użytkownika i zasobów udostępnianych przez platformę Azure. Złożonych konfiguracji należy trudne Rozwiązywanie problemów z łącznością. Usługa Network Watcher zmniejsza ilość czasu do znalezienia i wykrycia problemów z łącznością. Zwrócone wyniki mogą zapewnić szczegółowe informacje dotyczące tego, czy jest problem z łącznością z powodu platformy lub problem z konfiguracją użytkownika. Łączność może zostać sprawdzone za pomocą [PowerShell](network-watcher-connectivity-powershell.md), [wiersza polecenia platformy Azure](network-watcher-connectivity-cli.md), i [interfejsu API REST](network-watcher-connectivity-rest.md).

> [!IMPORTANT]
> Rozwiązywanie problemów z połączeniami wymaga, że maszyna wirtualna, rozwiązywanie problemów z z ma `AzureNetworkWatcherExtension` zainstalowane rozszerzenie maszyny Wirtualnej. Instalowanie rozszerzenia na maszynie Wirtualnej Windows można znaleźć [Agent usługi Azure Network Watcher rozszerzenie maszyny wirtualnej dla Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) i maszyny Wirtualnej systemu Linux można znaleźć pod adresem [Agent usługi Azure Network Watcher rozszerzenie maszyny wirtualnej dla systemu Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Rozszerzenie nie jest wymagany dla docelowego punktu końcowego.

## <a name="response"></a>Odpowiedź

W poniższej tabeli przedstawiono właściwości zwrócony podczas rozwiązywania problemów z połączeniem zakończył działanie.

|Właściwość  |Opis  |
|---------|---------|
|connectionStatus     | Stan sprawdzania łączności. Możliwe wyniki są **osiągalne** i **informujący**.        |
|AvgLatencyInMs     | Średnie opóźnienie podczas sprawdzania łączności (w milisekundach). (Tylko widoczne, gdy stan testu jest dostępny)        |
|MinLatencyInMs     | Minimalne opóźnienie podczas sprawdzania łączności (w milisekundach). (Tylko widoczne, gdy stan testu jest dostępny)        |
|MaxLatencyInMs     | Maksymalny czas oczekiwania podczas sprawdzania łączności (w milisekundach). (Tylko widoczne, gdy stan testu jest dostępny)        |
|ProbesSent     | Liczba sondy wysłane podczas sprawdzania. Wartość maksymalna to 100.        |
|ProbesFailed     | Liczba sond, które nie powiodły się podczas sprawdzania. Wartość maksymalna to 100.        |
|Przeskoki     | Przeskok w ścieżce przeskoku ze źródła do miejsca docelowego.        |
|Hops[].Type     | Typ zasobu. Możliwe wartości to **źródła**, **VirtualAppliance**, **VnetLocal**, i **Internet**.        |
|Hops[].Id | Unikatowy identyfikator przeskoku.|
|Hops[].Address | Adres IP przeskoku.|
|Hops[].ResourceId | ResourceID przeskoków, jeśli przeskok jest zasobem platformy Azure. Jeśli zasobu internetowego, ResourceID jest **Internet**. |
|Hops[].NextHopIds | Unikatowy identyfikator następny przeskok podjęte.|
|Przeskoki []. Problemy | Zbiór problemów, które wystąpiły podczas sprawdzania u danego przeskoku. Jeśli nie wystąpiły żadne problemy, wartość jest pusta.|
|Hops[].Issues[].Origin | U bieżącej hop, gdzie wystąpił problem. Możliwe wartości:<br/> **Liczba przychodzących** — problem się na łączu poprzedniego przeskoku do bieżącego przeskoku<br/>**Wychodzące** — problem jest Link z bieżącego przeskoków, aby następny przeskok<br/>**Lokalne** — problem znajduje się w bieżącym przeskoku.|
|Hops[].Issues[].Severity | Ważność wykrytego problemu. Możliwe wartości to **błąd** i **ostrzeżenie**. |
|Hops[].Issues[].Type |Typ znalezione problemy. Możliwe wartości: <br/>**CPU**<br/>**Pamięć**<br/>**GuestFirewall**<br/>**DnsResolution**<br/>**NetworkSecurityRule**<br/>**UserDefinedRoute** |
|Przeskoki []. Problemy z []. Kontekst |Szczegóły dotyczące znalezione problemy.|
|Przeskoki []. Problemy z []. Kontekst [] Key |Klucz parę klucz-wartość zwracana.|
|Przeskoki []. Problemy z []. Kontekst [] .value |Zwracana wartość parę klucz-wartość.|

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

Rozwiązywanie problemów z połączeniami zwraca typy błędów dotyczących połączenia. Poniższa tabela zawiera listę bieżące typy błędów zwracane.

|Type  |Opis  |
|---------|---------|
|Procesor CPU     | Wysokie wykorzystanie procesora CPU.       |
|Memory (Pamięć)     | Wysokie wykorzystanie pamięci.       |
|GuestFirewall     | Ruch jest zablokowany z powodu konfiguracji zapory maszyny wirtualnej.        |
|DNSResolution     | Rozpoznawanie nazw DNS nie powiodło się dla adresu docelowego.        |
|NetworkSecurityRule    | Ruch jest blokowany przez reguły sieciowej grupy zabezpieczeń (zwrócone reguły)        |
|UserDefinedRoute|Ruch jest porzucany ze względu na zdefiniowanych przez użytkownika lub trasa systemowa. |

### <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak rozwiązywać problemy z połączeniami przy użyciu [witryny Azure portal](network-watcher-connectivity-portal.md), [PowerShell](network-watcher-connectivity-powershell.md), [wiersza polecenia platformy Azure](network-watcher-connectivity-cli.md), lub [interfejsu API REST](network-watcher-connectivity-rest.md).