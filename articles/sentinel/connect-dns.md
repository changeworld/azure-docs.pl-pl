---
title: Połącz dane DNS w wersji zapoznawczej Azure przez wartownika | Dokumentacja firmy Microsoft
description: Dowiedz się, jak połączyć dane DNS w przez wartownika platformy Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: 4e6ed18a49a77f8061c975bdf3ecb085ebf71317
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190765"
---
# <a name="connect-your-domain-name-server"></a>Połączyć swój serwer nazwy domeny

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Możesz połączyć wszystkie domeny nazwy serwera (DNS) systemem Windows na platformie Azure przez wartownika. Odbywa się to przez zainstalowanie agenta na komputerze DNS. Loguje się przy użyciu systemu DNS, możesz uzyskać zabezpieczeń, wydajności i operacji związanych z szczegółowych informacji w infrastrukturze DNS organizacji przez zbieranie, analizowanie, i korelowanie analityczne i dzienniki inspekcji i inne powiązane dane z serwerów DNS.

Po włączeniu połączenia dziennika DNS, możesz wykonywać następujące czynności:
- Identyfikacja klientów próbujących rozpoznać nazwy domen złośliwe
- Identyfikowanie starych rekordów
- Zidentyfikuj domeny często poszukiwanych nazw i próg klienci DNS
- Widok obciążenie żądaniami na serwerach DNS
- Widok dynamiczny niepowodzenia rejestracji DNS

## <a name="connected-sources"></a>Połączone źródła

W poniższej tabeli opisano połączone źródła, które są obsługiwane przez to rozwiązanie:

| **Połączone źródło** | **Pomoc techniczna** | **Opis** |
| --- | --- | --- |
| [Agenci dla systemu Windows](../azure-monitor/platform/agent-windows.md) | Yes | Rozwiązanie zbiera informacje DNS z agentów dla Windows. |
| [Agenci dla systemu Linux](../azure-monitor/learn/quick-collect-linux-computer.md) | Nie | Rozwiązanie zbiera informacje DNS z bezpośrednich agentów systemu Linux. |
| [Grupa zarządzania programu System Center Operations Manager](../azure-monitor/platform/om-agents.md) | Yes | Rozwiązanie zbiera informacje DNS z agentów w połączonej grupie zarządzania programu Operations Manager. Bezpośrednie połączenie agenta programu Operations Manager do usługi Azure Monitor nie jest wymagane. Dane są przekazywane z grupy zarządzania do obszaru roboczego usługi Log Analytics. |
| [Konto usługi Azure Storage](../azure-monitor/platform/collect-azure-metrics-logs.md) | Nie | Usługa Azure storage nie jest używana przez to rozwiązanie. |

### <a name="data-collection-details"></a>Szczegóły dotyczące zbierania danych

Rozwiązanie zbiera spis DNS i dane dotyczące zdarzeń DNS z serwerów DNS, którym jest zainstalowany agent usługi Log Analytics. Dane dotyczące zapasów, takie jak liczba serwerów DNS, strefy i rekordy zasobów są zbierane przez uruchomienie polecenia cmdlet programu DNS PowerShell. Dane są aktualizowane co dwa dni. Dane dotyczące zdarzeń są zbierane w czasie rzeczywistym z [analitycznych i dzienników inspekcji](https://technet.microsoft.com/library/dn800669.aspx#enhanc) podał rozszerzonych DNS rejestrowania i diagnostyki w systemie Windows Server 2012 R2.


## <a name="connect-your-dns-appliance"></a>Połącz urządzenie DNS

1. W portalu Azure przez wartownika wybierz **łączników danych** i wybierz polecenie **DNS** kafelka.
1. W przypadku maszyn z systemem DNS na platformie Azure:
    1. Kliknij przycisk **Instalowanie agenta na maszynie wirtualnej Windows Azure**.
    1. W **maszyn wirtualnych** listy, wybierz maszynę DNS ma być przesyłana strumieniowo do usługi Azure przez wartownika. Upewnij się, że jest to maszyny Wirtualnej z systemem Windows.
    1. W otwartym oknie dla tej maszyny Wirtualnej, kliknij przycisk **Connect**.  
    1. Kliknij przycisk **Włącz** w **łącznika DNS** okna. 

2. Jeśli maszyna DNS nie jest w Maszynie wirtualnej platformy Azure:
    1. Kliknij przycisk **Instalowanie agenta na komputerach nienależących do platformy Azure**.
    1. W **agent bezpośredni** okna, wybierz opcję **Pobierz Windows agent (64-bitowy)** lub **Pobierz Windows agent (32-bitowa)** .
    1. Zainstaluj agenta na komputerze DNS. Kopiuj **identyfikator obszaru roboczego**, **klucza podstawowego**, i **klucz pomocniczy** i korzystania z nich po wyświetleniu monitu podczas instalacji.

3. Aby użyć odpowiednich schematu w usłudze Log Analytics dla dzienników DNS, możesz wyszukać **DnsEvents**.

## <a name="validate"></a>Walidacja 

W usłudze Log Analytics Wyszukaj schematu **DnsEvents** i upewnij się, że zdarzenia.

## <a name="next-steps"></a>Kolejne kroki
W tym dokumencie przedstawiono sposób łączenia urządzeń lokalnych DNS na platformie Azure przez wartownika. Aby dowiedzieć się więcej na temat platformy Azure przez wartownika, zobacz następujące artykuły:
- Dowiedz się, jak [Uzyskaj wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpoczynanie pracy [wykrywanie zagrożeń za pomocą platformy Azure przez wartownika](tutorial-detect-threats.md).
