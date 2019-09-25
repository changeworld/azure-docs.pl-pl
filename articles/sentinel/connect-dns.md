---
title: Łączenie danych DNS na platformie Azure — wskaźnikowa | Microsoft Docs
description: Dowiedz się, jak połączyć dane DNS na platformie Azure — wskaźnik.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: dd5442ff8c8d296dfa221a9ea7ed8d5833fd89c1
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240710"
---
# <a name="connect-your-domain-name-server---preview"></a>Łączenie serwera nazw domen — wersja zapoznawcza



Można połączyć wszystkie serwery nazw domen (DNS) działające w systemie Windows z platformą Azure. W tym celu należy zainstalować agenta na maszynie DNS. Korzystając z dzienników DNS, można uzyskać szczegółowe informacje dotyczące zabezpieczeń, wydajności i operacji w infrastrukturze DNS organizacji przez zbieranie, analizowanie i skorelowanie dzienników analitycznych i inspekcji oraz innych powiązanych danych z serwerów DNS.

Po włączeniu połączenia dziennika DNS można:
- Identyfikowanie klientów próbujących rozpoznać złośliwe nazwy domen
- Identyfikowanie starych rekordów zasobów
- Zidentyfikuj często badane nazwy domen i próg klientów DNS
- Wyświetlanie obciążenia żądaniami na serwerach DNS
- Wyświetl błędy rejestracji dynamicznej usługi DNS

## <a name="connected-sources"></a>Połączone źródła

W poniższej tabeli opisano połączone źródła, które są obsługiwane przez to rozwiązanie:

| **Połączone źródło** | **Pomoc techniczna** | **Opis** |
| --- | --- | --- |
| [Agenci dla systemu Windows](../azure-monitor/platform/agent-windows.md) | Yes | Rozwiązanie zbiera informacje DNS z agentów dla Windows. |
| [Agenci dla systemu Linux](../azure-monitor/learn/quick-collect-linux-computer.md) | Nie | Rozwiązanie zbiera informacje DNS z bezpośrednich agentów systemu Linux. |
| [Grupa zarządzania programu System Center Operations Manager](../azure-monitor/platform/om-agents.md) | Yes | Rozwiązanie zbiera informacje DNS z agentów w połączonej grupie zarządzania programu Operations Manager. Bezpośrednie połączenie z agenta Operations Manager do Azure Monitor nie jest wymagane. Dane są przekazywane z grupy zarządzania do obszaru roboczego usługi Log Analytics. |
| [Konto usługi Azure Storage](../azure-monitor/platform/collect-azure-metrics-logs.md) | Nie | Usługa Azure storage nie jest używana przez to rozwiązanie. |

### <a name="data-collection-details"></a>Szczegóły dotyczące zbierania danych

Rozwiązanie zbiera spis DNS i dane dotyczące zdarzeń DNS z serwerów DNS, którym jest zainstalowany agent usługi Log Analytics. Dane dotyczące zapasów, takie jak liczba serwerów DNS, strefy i rekordy zasobów są zbierane przez uruchomienie polecenia cmdlet programu DNS PowerShell. Dane są aktualizowane co dwa dni. Dane dotyczące zdarzeń są zbierane w czasie rzeczywistym z [analitycznych i dzienników inspekcji](https://technet.microsoft.com/library/dn800669.aspx#enhanc) podał rozszerzonych DNS rejestrowania i diagnostyki w systemie Windows Server 2012 R2.


## <a name="connect-your-dns-appliance"></a>Połącz urządzenie DNS

1. W portalu wskaźnikowym platformy Azure wybierz pozycję **Łączniki danych** i wybierz kafelek **DNS** .
1. Jeśli Twoje maszyny DNS znajdują się na platformie Azure:
    1. Kliknij przycisk **Zainstaluj agenta na maszynie wirtualnej platformy Azure systemu Windows**.
    1. Na liście **maszyny wirtualne** wybierz MASZYNę DNS, która ma zostać przesłana do usługi Azure wskaźnikowej. Upewnij się, że jest to maszyna wirtualna z systemem Windows.
    1. W oknie otwartym dla tej maszyny wirtualnej kliknij pozycję **Połącz**.  
    1. W oknie **łącznika usługi DNS** kliknij pozycję **Włącz** . 

2. Jeśli maszyna DNS nie jest maszyną wirtualną platformy Azure:
    1. Kliknij przycisk **Zainstaluj agenta na maszynach spoza platformy Azure**.
    1. W oknie **agenta bezpośredniego** wybierz pozycję **Pobierz agenta systemu Windows (64 bit)** lub **pobierz agenta systemu Windows (32 bit)** .
    1. Zainstaluj agenta na maszynie DNS. Skopiuj **Identyfikator obszaru roboczego**, **klucz podstawowy**i **klucz pomocniczy** , a następnie użyj ich po wyświetleniu monitu podczas instalacji.

3. Aby użyć odpowiedniego schematu w Log Analytics dla dzienników DNS, wyszukaj ciąg **DnsEvents**.

## <a name="validate"></a>Weryfikuj 

W Log Analytics Wyszukaj schemat **DnsEvents** i upewnij się, że istnieją zdarzenia.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób nawiązywania połączenia z lokalnymi urządzeniami DNS z systemem Azure wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
