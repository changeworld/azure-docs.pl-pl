---
title: Wprowadzenie do rozwiązywania problemów z połączeniem z usługą Azure Network Watcher | Dokumenty firmy Microsoft
description: Ta strona zawiera omówienie możliwości rozwiązywania problemów z połączeniem z obserwatorem sieci
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: damendo
ms.openlocfilehash: cae3072a3468b232e95d7c1949948b71059695ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283279"
---
# <a name="introduction-to-connection-troubleshoot-in-azure-network-watcher"></a>Wprowadzenie do rozwiązywania problemów z połączeniem w usłudze Azure Network Watcher

Funkcja rozwiązywania problemów z połączeniem w funkcji Kontrola sieci umożliwia sprawdzenie bezpośredniego połączenia TCP z maszyny wirtualnej do maszyny wirtualnej (VM), w pełni kwalifikowanej nazwy domeny (FQDN), identyfikatora URI lub adresu IPv4. Scenariusze sieciowe są złożone, są implementowane przy użyciu sieciowych grup zabezpieczeń, zapór, tras zdefiniowanych przez użytkownika i zasobów dostarczanych przez platformę Azure. Złożone konfiguracje sprawiają, że rozwiązywanie problemów z łącznością jest trudne. Funkcja Kontrola sieci pomaga skrócić czas znajdowania i wykrywania problemów z łącznością. Zwrócone wyniki mogą zapewnić wgląd w to, czy problem z łącznością jest spowodowany problemem z platformą lub konfiguracją użytkownika. Łączność można sprawdzić za pomocą [programów PowerShell,](network-watcher-connectivity-powershell.md) [interfejsu wiersza polecenia platformy Azure](network-watcher-connectivity-cli.md)i interfejsu API [REST](network-watcher-connectivity-rest.md).

> [!IMPORTANT]
> Rozwiązywanie problemów z połączeniem wymaga, `AzureNetworkWatcherExtension` aby maszyna wirtualna, z której można rozwiązać problem, ma zainstalowane rozszerzenie maszyny Wirtualnej. Aby zainstalować rozszerzenie na maszynie Wirtualnej systemu Windows odwiedź [rozszerzenie maszyny wirtualnej usługi Azure Network Watcher Agent dla systemu Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) i maszyny wirtualnej z systemem Linux, odwiedź rozszerzenie maszyny [wirtualnej usługi Azure Network Watcher Agent dla systemu Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Rozszerzenie nie jest wymagane w docelowym punkcie końcowym.

## <a name="response"></a>Odpowiedź

W poniższej tabeli przedstawiono właściwości zwrócone po zakończeniu rozwiązywania problemów z połączeniem.

|Właściwość  |Opis  |
|---------|---------|
|Stan połączenia     | Sprawdzanie stanu łączności. Możliwe wyniki są **osiągalne** i **nieosiągalne**.        |
|AvgLatencyInMs     | Średnie opóźnienie podczas sprawdzania łączności w milisekundach. (Tylko wtedy, gdy stan czeku jest osiągalny)        |
|MinLatencyInMs     | Minimalne opóźnienie podczas sprawdzania łączności w milisekundach. (Tylko wtedy, gdy stan czeku jest osiągalny)        |
|MaxLatencyInMs     | Maksymalne opóźnienie podczas sprawdzania łączności w milisekundach. (Tylko wtedy, gdy stan czeku jest osiągalny)        |
|ProbesSent (SondaSent)     | Liczba sond wysłanych podczas kontroli. Wartość maksymalna wynosi 100.        |
|SondyNiewłasne     | Liczba sond, które nie powiodły się podczas sprawdzania. Wartość maksymalna wynosi 100.        |
|Przeskoki     | Przeskakuj według ścieżki przeskoku ze źródła do miejsca docelowego.        |
|Chmiel[]. Typu     | Typ zasobu. Możliwe wartości **to Źródło**, **VirtualAppliance**, **VnetLocal**i **Internet**.        |
|Chmiel[]. Identyfikator | Unikatowy identyfikator przeskoku.|
|Chmiel[]. Adres | Adres IP przeskoku.|
|Chmiel[]. Resourceid | Identyfikator zasobów przeskoku, jeśli przeskok jest zasobem platformy Azure. Jeśli jest to zasób internetowy, Identyfikator zasobu to **Internet**. |
|Chmiel[]. NextHopIds (Łajda) | Unikatowy identyfikator następnego przeskoku zrobione.|
|Chmiel[]. Problemy | Zbiór problemów, które zostały napotkane podczas kontroli w tym skoku. Jeśli nie było żadnych problemów, wartość jest pusta.|
|Chmiel[]. Problemy[]. Pochodzenia | Przy bieżącym przeskoku, gdzie wystąpił problem. Możliwe wartości:<br/> **Przychodzące** — problem znajduje się w łączu z poprzedniego przeskoku do bieżącego przeskoku<br/>**Ruch wychodzący** — problem znajduje się w łączu z bieżącego przeskoku do następnego przeskoku<br/>**Lokalny** — problem jest na bieżącym przeskoku.|
|Chmiel[]. Problemy[]. Ważności | Ważność wykrytego problemu. Możliwe wartości to **Błąd** i **Ostrzeżenie**. |
|Chmiel[]. Problemy[]. Typu |Rodzaj znalezionego problemu. Możliwe wartości: <br/>**Procesora**<br/>**Pamięci**<br/>**GuestFirewall (Ściana gościnna)**<br/>**Rozwiązanie DnsResolution**<br/>**Reguła zabezpieczeń sieciowych**<br/>**Ustawienia ustawienia użytkownika** |
|Chmiel[]. Problemy[]. Kontekście |Szczegóły dotyczące znalezionego problemu.|
|Chmiel[]. Problemy[]. Kontekst[]klucz |Klucz pary wartości klucza zwrócony.|
|Chmiel[]. Problemy[]. Kontekst[].wartość |Zwrócona wartość pary wartości klucza.|

Poniżej przedstawiono przykład problemu znalezionego na przeskoku.

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
## <a name="fault-types"></a>Typy usterek

Rozwiązywanie problemów z połączeniem zwraca typy błędów dotyczących połączenia. Poniższa tabela zawiera listę bieżących typów błędów zwracanych.

|Typ  |Opis  |
|---------|---------|
|Procesor CPU     | Wysokie wykorzystanie procesora.       |
|Memory (Pamięć)     | Wysokie wykorzystanie pamięci.       |
|GuestFirewall (Ściana gościnna)     | Ruch jest zablokowany z powodu konfiguracji zapory maszyny wirtualnej.        |
|Rozwiązanie DNS     | Rozpoznawanie dns nie powiodło się dla adresu docelowego.        |
|Reguła zabezpieczeń sieciowych    | Ruch jest blokowany przez regułę nsg (zwracana jest reguła)        |
|Ustawienia ustawienia użytkownika|Ruch jest usuwany z powodu zdefiniowanej przez użytkownika trasy lub trasy systemowej. |

### <a name="next-steps"></a>Następne kroki

Dowiedz się, jak rozwiązywać problemy z połączeniami za pomocą [portalu Azure,](network-watcher-connectivity-portal.md) [programu PowerShell,](network-watcher-connectivity-powershell.md) [interfejsu wiersza polecenia platformy Azure](network-watcher-connectivity-cli.md)lub interfejsu API [REST](network-watcher-connectivity-rest.md).