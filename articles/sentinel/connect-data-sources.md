---
title: Połączyć źródła danych z wersją zapoznawczą platformy Azure Microsoft Docs
description: Dowiedz się, jak łączyć źródła danych z danymi wskaźnikowymi platformy Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: a3b63cfa-b5fe-4aff-b105-b22b424c418a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/04/2019
ms.author: rkarlin
ms.openlocfilehash: 4928657aa9052b50faf1f326cc09797c5aaf69bb
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780513"
---
# <a name="connect-data-sources"></a>Łączenie ze źródłami danych

> [!IMPORTANT]
> W publicznej wersji zapoznawczej jest obecnie dostępna usługa Azure.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Aby przejść do tablicy wskaźnikowej platformy Azure, musisz najpierw nawiązać połączenie ze źródłami danych. Wskaźnik platformy Azure obejmuje wiele łączników dla rozwiązań firmy Microsoft, dostępnych poza platformą i zapewniania integracji w czasie rzeczywistym, w tym rozwiązań firmy Microsoft do ochrony przed zagrożeniami, a także źródeł Microsoft 365, takich jak Office 365, Azure AD, Azure ATP i Microsoft Cloud App Security i nie tylko. Ponadto istnieją wbudowane łączniki do szerszego ekosystemu zabezpieczeń dla rozwiązań firm innych niż Microsoft. Możesz również użyć typowego formatu zdarzeń, dziennika systemowego lub REST-API, aby połączyć źródła danych z systemem Azure.  

1. Z menu wybierz pozycję **Łączniki danych**. Ta strona umożliwia wyświetlenie pełnej listy łączników udostępnianych przez wskaźnik platformy Azure oraz ich stan. Wybierz łącznik, który chcesz połączyć, a następnie wybierz pozycję **Otwórz stronę łącznika**. 

   ![Moduły zbierające dane](./media/collect-data/collect-data-page.png)

1. Na określonej stronie łącznika upewnij się, że spełniono wszystkie wymagania wstępne, i postępuj zgodnie z instrukcjami, aby połączyć dane z platformą Azure wskaźnikiem. Synchronizowanie dzienników z platformą Azure jest możliwe dopiero po pewnym czasie. Po nawiązaniu połączenia zobaczysz podsumowanie danych w grafie **otrzymane dane** oraz stan łączności typów danych.

   ![Połącz moduły zbierające](./media/collect-data/opened-connector-page.png)
  
1. Kliknij kartę **następne kroki** , aby uzyskać listę zawartości wbudowanej platformy Azure, która zapewnia dla określonego typu danych.

   ![Moduły zbierające dane](./media/collect-data/data-insights.png)
 

## <a name="data-connection-methods"></a>Metody połączenia danych

Następujące metody łączenia danych są obsługiwane przez wskaźnik na platformie Azure:

- **Usługi firmy Microsoft**:<br> Usługi firmy Microsoft są połączone natywnie, wykorzystując platformę Azure Foundation do zintegrowanej integracji, następujące rozwiązania mogą być połączone za pomocą kilku kliknięć:
    - [Office 365](connect-office-365.md)
    - [Dzienniki inspekcji usługi Azure AD i logowania](connect-azure-active-directory.md)
    - [Aktywność platformy Azure](connect-azure-activity.md)
    - [Azure AD Identity Protection](connect-azure-ad-Identity-protection.md)
    - [Azure Security Center](connect-azure-security-center.md)
    - [Azure Information Protection](connect-azure-information-protection.md)
    - [Zaawansowana ochrona przed zagrożeniami na platformie Azure](connect-azure-atp.md)
    - [Cloud App Security](connect-cloud-app-security.md)
    - [Zdarzenia zabezpieczeń systemu Windows](connect-windows-security-events.md) 
    - [Zapora systemu Windows](connect-windows-firewall.md)

- **Rozwiązania zewnętrzne za pośrednictwem interfejsu API**: Niektóre źródła danych są połączone przy użyciu interfejsów API, które są udostępniane przez połączone źródło danych. Zazwyczaj większość technologii zabezpieczeń zapewnia zestaw interfejsów API, za pomocą których można pobrać dzienniki zdarzeń. Interfejsy API nawiązują połączenie z platformą Azure, a następnie zbierają określone typy danych i wysyłają je do Log Analytics platformy Azure. Urządzenia połączone za pośrednictwem interfejsu API obejmują:
    - [Barracuda](connect-barracuda.md)
    - [Symantec](connect-symantec.md)
- **Rozwiązania zewnętrzne przez agenta**: Punkt kontrolny platformy Azure może być połączony ze wszystkimi innymi źródłami danych, które mogą wykonywać przesyłanie strumieniowe w czasie rzeczywistym przy użyciu protokołu dziennika systemowego za pośrednictwem agenta. <br>Większość urządzeń używa protokołu dziennika systemowego do wysyłania komunikatów zdarzeń, które obejmują sam dziennik i dane dotyczące dziennika. Format dzienników jest różny, ale większość urządzeń obsługuje standard CEF (Common Event format). <br>Agent wskaźnikowy platformy Azure, który jest oparty na Microsoft Monitoring Agent, konwertuje CEF sformatowane dzienniki do formatu, który może zostać pozyskany przez Log Analytics. W zależności od typu urządzenia Agent jest instalowany bezpośrednio na urządzeniu lub na dedykowanym serwerze z systemem Linux. Agent dla systemu Linux odbiera zdarzenia z demona dziennika systemu za pośrednictwem protokołu UDP, ale jeśli oczekuje się, że maszyna z systemem Linux będzie zbierać duże ilości zdarzeń dziennika systemowego, są one wysyłane za pośrednictwem protokołu TCP z demona dziennika systemowego do agenta i z tego miejsca do Log Analytics.
    - Zapory, proxy i punkty końcowe:
        - [F5](connect-f5.md)
        - [Punkt kontrolny](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [Fortinet](connect-fortinet.md)
        - [Palo Alto](connect-paloalto.md)
        - [Inne urządzenia CEF](connect-common-event-format.md)
        - [Inne urządzenia dziennika systemu](connect-syslog.md)
    - Rozwiązania DLP
    - [Dostawcy analizy zagrożeń](connect-threat-intelligence.md)
    - [Maszyny DNS](connect-dns.md) — Agent zainstalowany bezpośrednio na komputerze DNS
    - Serwery z systemem Linux
    - Inne chmury
    
## Opcje połączenia z agentem<a name="agent-options"></a>

Aby połączyć urządzenie zewnętrzne z centrum danych platformy Azure, należy wdrożyć agenta na dedykowanym komputerze (maszynie wirtualnej lub lokalnie), aby umożliwić obsługę komunikacji między urządzeniem i wskaźnikiem kontrolnym platformy Azure. Agenta można wdrożyć automatycznie lub ręcznie. Automatyczne wdrażanie jest dostępne tylko wtedy, gdy dedykowany komputer jest nową maszyną wirtualną, którą tworzysz na platformie Azure. 


![CEF na platformie Azure](./media/connect-cef/cef-syslog-azure.png)

Alternatywnie można wdrożyć agenta ręcznie na istniejącej maszynie wirtualnej platformy Azure, na maszynie wirtualnej w innej chmurze lub na maszynie lokalnej.

![CEF lokalnie](./media/connect-cef/cef-syslog-onprem.png)


## <a name="next-steps"></a>Kolejne kroki

- Aby rozpocząć pracę z systemem Azure — wskaźnikiem, potrzebna jest subskrypcja do Microsoft Azure. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).
- Dowiedz się, jak dołączać [dane do usługi Azure wskaźnikowej](quickstart-onboard.md)i [uzyskiwać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
