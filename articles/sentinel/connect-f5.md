---
title: Łączenie danych F5 z usługą Azure Sentinel| Dokumenty firmy Microsoft
description: Dowiedz się, jak połączyć dane F5 z usługą Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 6f33cecca1c67f91d0f2be64ab156f45ee500521
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588267"
---
# <a name="connect-f5-to-azure-sentinel"></a>Łączenie F5 z usługą Azure Sentinel

W tym artykule wyjaśniono, jak połączyć urządzenie F5 z usługą Azure Sentinel. Łącznik danych F5 umożliwia łatwe łączenie dzienników F5 z usługą Azure Sentinel, wyświetlanie pulpitów nawigacyjnych, tworzenie niestandardowych alertów i usprawnianie badań. Korzystanie z F5 na platformie Azure Sentinel zapewni Ci więcej informacji na temat korzystania z Internetu w organizacji i zwiększy jej możliwości obsługi zabezpieczeń. 

## <a name="configure-your-f5-to-send-cef-messages"></a>Konfigurowanie urządzenia F5 do wysyłania wiadomości cef

1. Przejdź do [strony F5 Konfigurowanie rejestrowania zdarzeń zabezpieczeń aplikacji](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html)i postępuj zgodnie z instrukcjami, aby skonfigurować zdalne rejestrowanie, korzystając z następujących wskazówek:
   - Ustaw **typ magazynu zdalnego** na **CEF**.
   - Ustaw **protokół** na **TCP**.
   - Ustaw **adres IP** na adres IP serwera Syslog.
   - Ustaw **numer portu** na **514**lub port, który ma być używany przez agenta.
   - Maksymalny **rozmiar ciągu zapytania** można ustawić na rozmiar ustawiony w agencie.

1. Aby użyć odpowiedniego schematu w usłudze Log Analytics `CommonSecurityLog`dla zdarzeń cef, wyszukaj .

1. Przejdź do [kroku 3: Sprawdź poprawność łączności](connect-cef-verify.md).


## <a name="next-steps"></a>Następne kroki
W tym dokumencie dowiesz się, jak połączyć F5 z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats.md).
- Monitorowanie danych [za pomocą skoroszytów.](tutorial-monitor-your-data.md)

