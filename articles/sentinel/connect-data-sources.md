---
title: Połącz źródła danych do platformy Azure przez wartownika w wersji zapoznawczej? | Dokumentacja firmy Microsoft
description: Dowiedz się, jak połączyć źródeł danych z platformy Azure przez wartownika.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a3b63cfa-b5fe-4aff-b105-b22b424c418a
ms.service: sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: ba0f584e8026fe3828ec79c4b6c0ff5a0bb89f5a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59492366"
---
# <a name="connect-data-sources"></a>Łączenie ze źródłami danych

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Aby dołączyć przez wartownika platformy Azure należy najpierw połączyć się ze źródłami danych. Azure wartownik dołączono wiele łączników dla rozwiązań firmy Microsoft, dostępne poza pole, a także w czasie rzeczywistym integracji, w tym rozwiązań do ochrony przed zagrożeniami firmy Microsoft i Microsoft 365 źródeł, takich jak usługi Office 365, Azure AD, Azure ATP i Microsoft Cloud App Security i nie tylko. Ponadto ma wbudowanych łączników szerszy ekosystemem zabezpieczenia dla rozwiązań firmy Microsoft. Można również użyć typowego formatu zdarzeń dziennika systemu lub interfejsu API REST, nawiązywanie połączeń ze źródłami danych z platformy Azure przez wartownika także.  

![Moduły zbierające dane](./media/collect-data/collect-data-page.png)

## <a name="data-connection-methods"></a>Metody połączenia danych

Następujące metody połączenia danych są obsługiwane przez usługi Azure przez wartownika:

- **Usługi firmy Microsoft**:<br> Usługi firmy Microsoft są połączone w sposób macierzysty, korzystanie z platformy Azure podstawa out-gotową integrację, następujące rozwiązania mogą być połączone za pomocą kilku kliknięć:
    - [Office 365](connect-office-365.md)
    - [Usługa Azure AD inspekcji, dzienniki i logowania](connect-azure-active-directory.md)
    - [Aktywności platformy Azure](connect-azure-activity.md)
    - [Azure AD Identity Protection](connect-azure-ad-Identity-protection.md)
    - [Azure Security Center](connect-azure-security-center.md)
    - [Azure Information Protection](connect-azure-information-protection.md)
    - [Usługa Azure Advanced Threat Protection](connect-azure-atp.md)
    - [Cloud App Security](connect-cloud-app-security.md)
    - [Zdarzenia zabezpieczeń Windows](connect-windows-security-events.md) 
    - [Zapora Windows](connect-windows-firewall.md)

- **Zewnętrzny rozwiązań za pośrednictwem interfejsu API**: Niektóre źródła danych są połączone za pomocą interfejsów API, które zostały udostępnione przez połączonego źródła danych. Zazwyczaj większość technologii zabezpieczeń zawierają zestaw interfejsów API za pomocą którego można pobrać dzienników zdarzeń. Interfejsy API nawiązać połączenie z platformy Azure przez wartownika i zbieranie danych z konkretnych typów i wysyłać je do usługi Azure Log Analytics. Urządzenia połączone za pośrednictwem interfejsu API to:
    - [Barracuda](connect-barracuda.md)
    - [Symantec](connect-symantec.md)
- **Zewnętrzny rozwiązań za pośrednictwem agenta**: Wartownik platformy Azure mogą być połączone do wszystkich innych źródeł danych, które mogą wykonywać, przesyłanie strumieniowe dzienników w czasie rzeczywistym, za pomocą przy użyciu protokołu Syslog, za pośrednictwem agenta. <br>Większość urządzeń Użyj protokołu Syslog, aby wysyłać komunikaty o zdarzeniach, które zawierają dziennik, sam, jak i dane dotyczące dziennika. Format dzienniki różni się jednak większość urządzeń obsługuje standard Common Event Format (CEF). <br>Agent platformy Azure przez wartownika, który jest oparty na agenta pakietu OMS, konwertuje CEF sformatowane dzienników do formatu, który może być pozyskiwane przez usługę Log Analytics. W zależności od typu urządzenia agent jest zainstalowany bezpośrednio na urządzeniu lub na dedykowanych serwerach Linux. Agent dla systemu Linux odbiera zdarzenia z demona usługi Syslog za pośrednictwem protokołu UDP, ale w przypadkach, gdzie maszynę z systemem Linux oczekuje się, aby zebrać dużą liczbę zdarzeń dziennika systemu, te są wysyłane za pośrednictwem protokołu TCP z demona usługi Syslog do agenta, a do usługi Log Analytics.
    - Zapór, proxy i punktów końcowych:
        - [F5](connect-f5.md)
        - [Check Point](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [Fortinet](connect-fortinet.md)
        - [Palo Alto](connect-paloalto.md)
        - [Inne urządzenia CEF](connect-common-event-format.md)
        - [Inne urządzenia usługi Syslog](connect-syslog.md)
    - Rozwiązania DLP
    - [Dostawców analizy zagrożeń](connect-threat-intelligence.md)
    - [DNS maszyn](connect-dns.md) -agent jest zainstalowany bezpośrednio na maszynie DNS
    - Serwery z systemem Linux
    - Inne chmury
    
## Opcje połączenia agenta<a name="agent-options"></a>

Nawiązać połączenia zewnętrzne urządzenie przez wartownika platformy Azure, należy wdrożyć agenta na dedykowanym komputerze (maszyny Wirtualnej lub lokalnie) na potrzeby obsługi komunikacji między urządzeniem i przez wartownika Azure. Agenta można wdrożyć automatycznie lub ręcznie. Automatyczne wdrażanie jest dostępna tylko w przypadku dedykowanej maszynie nową maszynę Wirtualną, tworzysz na platformie Azure. 


![CEF na platformie Azure](./media/connect-cef/cef-syslog-azure.png)

Alternatywnie można wdrożyć agenta ręcznie na istniejącej Maszynie wirtualnej platformy Azure, na maszynie Wirtualnej w innej chmurze lub na maszynie lokalnej.

![CEF w środowisku lokalnym](./media/connect-cef/cef-syslog-onprem.png)


## <a name="next-steps"></a>Kolejne kroki

- Aby rozpocząć korzystanie z platformy Azure przez wartownika, musisz mieć subskrypcji platformy Microsoft Azure. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).
- Dowiedz się, jak [dołączyć dane do platformy Azure przez wartownika](quickstart-onboard.md), i [Uzyskaj wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
