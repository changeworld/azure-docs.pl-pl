---
title: Łączenie danych DNS w usłudze Azure Sentinel| Dokumenty firmy Microsoft
description: Dowiedz się, jak połączyć dane DNS w usłudze Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 6d43b82ecd13ac5e082d270ee44ce61fef763d2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588318"
---
# <a name="connect-your-domain-name-server"></a>Łączenie serwera nazw domen

> [!IMPORTANT]
> Łącznik danych DNS w usłudze Azure Sentinel jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Z usługą Azure Sentinel można połączyć dowolny serwer nazw domen (DNS) działający w systemie Windows. Odbywa się to przez zainstalowanie agenta na komputerze DNS. Korzystając z dzienników DNS, można uzyskać wgląd w infrastrukturę DNS organizacji związaną z zabezpieczeniami, wydajnością i operacjami, zbierając, analizując i korelując dzienniki analityczne i inspekcji oraz inne powiązane dane z serwerów DNS.

Po włączeniu połączenia dziennika DNS można:
- Identyfikowanie klientów, którzy próbują rozpoznać złośliwe nazwy domen
- Identyfikowanie starych rekordów zasobów
- Identyfikowanie często wyszukiwanych nazw domen i rozmownych klientów DNS
- Wyświetlanie obciążenia żądania na serwerach DNS
- Wyświetlanie dynamicznych błędów rejestracji DNS

## <a name="connected-sources"></a>Połączone źródła

W poniższej tabeli opisano połączone źródła, które są obsługiwane przez to rozwiązanie:

| **Połączone źródło** | **Pomoc techniczna** | **Opis** |
| --- | --- | --- |
| [Agenci dla systemu Windows](../azure-monitor/platform/agent-windows.md) | Tak | Rozwiązanie zbiera informacje DNS od agentów systemu Windows. |
| [Agenci dla systemu Linux](../azure-monitor/learn/quick-collect-linux-computer.md) | Nie | Rozwiązanie nie zbiera informacji DNS od bezpośrednich agentów systemu Linux. |
| [Grupa zarządzania programu System Center Operations Manager](../azure-monitor/platform/om-agents.md) | Tak | Rozwiązanie zbiera informacje DNS od agentów w połączonej grupie zarządzania programu Operations Manager. Bezpośrednie połączenie z agentem programu Operations Manager z usługą Azure Monitor nie jest wymagane. Dane są przekazywane z grupy zarządzania do obszaru roboczego usługi Log Analytics. |
| [Konto magazynu platformy Azure](../azure-monitor/platform/collect-azure-metrics-logs.md) | Nie | Usługa Azure Storage nie jest używana przez rozwiązanie. |

### <a name="data-collection-details"></a>Szczegóły gromadzenia danych

Rozwiązanie zbiera dane związane z spisem dns i zdarzeniami DNS z serwerów DNS, na których jest zainstalowany agent usługi Log Analytics. Dane związane z zapasami, takie jak liczba serwerów DNS, stref i rekordów zasobów, są zbierane przez uruchomienie poleceń cmdlet programu DNS PowerShell. Dane są aktualizowane co dwa dni. Dane związane ze zdarzeniami są zbierane w czasie zbliżonym do rzeczywistego z [dzienników analitycznych i inspekcji dostarczanych](https://technet.microsoft.com/library/dn800669.aspx#enhanc) przez ulepszone rejestrowanie i diagnostykę DNS w systemie Windows Server 2012 R2.


## <a name="connect-your-dns-appliance"></a>Podłączanie urządzenia DNS

1. W portalu Azure Sentinel wybierz **łączniki danych** i wybierz kafelek **DNS (Preview).**
1. Jeśli twoje maszyny DNS znajdują się na platformie Azure:
    1. Kliknij **pozycję Zainstaluj agenta na maszynie wirtualnej systemu Azure Windows**.
    1. Na liście **Maszyny wirtualne** wybierz maszynę DNS, którą chcesz przesyłać strumieniowo do usługi Azure Sentinel. Upewnij się, że jest to maszyna wirtualna systemu Windows.
    1. W oknie, które otwiera się dla tej maszyny Wirtualnej, kliknij przycisk **Połącz**.  
    1. Kliknij **pozycję Włącz** w oknie **łącznika DNS.** 

2. Jeśli komputer DNS nie jest maszyną wirtualną platformy Azure:
    1. Kliknij **pozycję Zainstaluj agenta na komputerach innych niż Azure**.
    1. W oknie **Agent bezpośredni** wybierz opcję **Pobierz agenta systemu Windows (64 bit)** lub **Pobierz agenta systemu Windows (32 bity).**
    1. Zainstaluj agenta na komputerze DNS. Skopiuj **identyfikator obszaru roboczego,** **klucz podstawowy**i **klucz pomocniczy** i użyj ich po wyświetleniu monitu podczas instalacji.

3. Aby użyć odpowiedniego schematu w usłudze Log Analytics dla dzienników DNS, wyszukaj **dnsevents**.

## <a name="validate"></a>Walidacja 

W usłudze Log Analytics wyszukaj schemat **DnsEvents** i upewnij się, że występują zdarzenia.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli kwerendy odnośnika nie są wyświetlane w usłudze Azure Sentinel, wykonaj następujące kroki, aby kwerendy były wyświetlane poprawnie:
1. Włącz [dzienniki usługi DNS Analytics na serwerach](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11)).
2. Upewnij się, że dnsevents są wyświetlane na liście kolekcji usługi Log Analytics.
3. Włącz [usługę Azure DNS Analytics](../azure-monitor/insights/dns-analytics.md).
4. W usłudze Azure DNS Analytics w obszarze **Konfiguracja**zmień dowolne ustawienia, zapisz je, a następnie zmień je z powrotem, jeśli zajdzie taka potrzeba, a następnie zapisz je ponownie.
5. Sprawdź analizy DNS platformy Azure, aby upewnić się, że kwerendy są teraz wyświetlane.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie dowiesz się, jak połączyć urządzenia lokalne DNS z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats-built-in.md).
