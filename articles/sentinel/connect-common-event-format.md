---
title: Łączenie danych cef z usługą Azure Sentinel Preview| Dokumenty firmy Microsoft
description: Dowiedz się, jak połączyć dane cef z usługą Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2019
ms.author: yelevin
ms.openlocfilehash: 8314614616c6b8969832d52fc684d47ba1bf0fe3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588352"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>Podłączanie rozwiązania zewnętrznego przy użyciu wspólnego formatu zdarzeń


Po podłączeniu rozwiązania zewnętrznego, które wysyła komunikaty CEF, istnieją trzy kroki do nawiązania połączenia z usługą Azure Sentinel:

KROK 1: [Podłącz instrument CEF poprzez wdrożenie agenta](connect-cef-agent.md) KROK 2: [Wykonaj kroki specyficzne dla rozwiązania](connect-cef-solution-config.md) KROK 3: Sprawdź [łączność](connect-cef-verify.md)

W tym artykule opisano, jak działa połączenie, zawiera wymagania wstępne i zawiera kroki wdrażania agenta w rozwiązaniach zabezpieczeń, które wysyłają komunikaty common event format (CEF) na górze Syslog. 

> [!NOTE] 
> Dane są przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchomiona usługa Azure Sentinel.

Aby nawiązać to połączenie, należy wdrożyć agenta na dedykowanym komputerze z systemem Linux (maszyna wirtualna lub lokalnie) do obsługi komunikacji między urządzeniem a usługą Azure Sentinel. Na poniższym diagramie opisano konfigurację w przypadku maszyny Wirtualnej systemu Linux na platformie Azure.

 ![CEF na platformie Azure](./media/connect-cef/cef-syslog-azure.png)

Alternatywnie ta konfiguracja będzie istnieć, jeśli używasz maszyny Wirtualnej w innej chmurze lub komputera lokalnego. 

 ![CEF w lokalu](./media/connect-cef/cef-syslog-onprem.png)


## <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa

Upewnij się, że zabezpieczenia komputera są skonfigurowane zgodnie z zasadami zabezpieczeń organizacji. Można na przykład skonfigurować sieć tak, aby była zgodna z zasadami zabezpieczeń sieci firmowej, i zmienić porty i protokoły w demonie, aby były zgodne z wymaganiami. Aby poprawić konfigurację zabezpieczeń komputera, można użyć następujących instrukcji: Bezpieczna maszyna wirtualna na  [platformie Azure](../virtual-machines/linux/security-policy.md), Najważniejsze [wskazówki dotyczące zabezpieczeń sieci.](../security/fundamentals/network-best-practices.md)

Aby użyć komunikacji TLS między rozwiązaniem zabezpieczającym a komputerem Syslog, należy skonfigurować demon Syslog (rsyslog lub syslog-ng) do komunikowania się w TLS: [Szyfrowanie ruchu Syslog z TLS -rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [szyfrowanie komunikatów dziennika za pomocą TLS –syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).

 
## <a name="prerequisites"></a>Wymagania wstępne
Upewnij się, że na komputerze z systemem Linux używanym jako serwer proxy jest uruchomiony jeden z następujących systemów operacyjnych:

- 64-bitowy
  - CentOS 6 i 7
  - Amazon Linux 2017.09
  - Oracle Linux 6 i 7
  - Serwer Red Hat Enterprise Linux Server 6 i 7
  - Debian GNU/Linux 8 i 9
  - Ubuntu Linux 14.04 LTS, 16.04 LTS i 18.04 LTS
  - SUSE Linux Enterprise Server 12
- 32-bitowa
   - CentOS 6
   - Oracle Linux 6
   - Red Hat Enterprise Linux Server 6
   - Debian GNU/Linux 8 i 9
   - Ubuntu Linux 14.04 LTS i 16.04 LTS
 
 - Wersje demonów
   - Syslog-ng: 2,1 - 3,22,1
   - Rsyslog: v8
  
 - Obsługiwane kontrolery RFC syslogu
   - Syslog RFC 3164
   - Syslog RFC 5424
 
Upewnij się, że urządzenie spełnia również następujące wymagania: 
- Uprawnienia
    - Musisz mieć podwyższone uprawnienia (sudo) na komputerze. 
- Wymagania dotyczące oprogramowania
    - Upewnij się, że python działa na komputerze



## <a name="next-steps"></a>Następne kroki
W tym dokumencie dowiesz się, jak połączyć urządzenia CEF z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats.md).

