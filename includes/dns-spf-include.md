---
author: WenJason
ms.service: dns
ms.topic: include
origin.date: 11/25/2018
ms.date: 12/24/2018
ms.author: v-jay
ms.openlocfilehash: 9cc650cea17acb8d89933c819c4ca60e2c459d1c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60563368"
---
Rekordy framework (SPF) zasad nadawcy są używane do określenia serwerów poczty e-mail, które może wysyłać wiadomości e-mail w imieniu nazwy domeny. Prawidłowej konfiguracji rekordy SPF ważne jest, aby zapobiec odbiorców oznaczanie wiadomości e-mail jako wiadomości-śmieci.

Specyfikacje RFC DNS pierwotnie została wprowadzona nowy typ rekordu SPF, aby obsługiwać ten scenariusz. Aby zapewnić obsługę starszych serwerów nazw, również dozwolone użycie typu rekordu TXT, aby określić rekordy SPF. Tę niejednoznaczność doprowadziło do wprowadzenia w błąd, który został rozwiązany przez [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1). Stwierdza, że rekordy SPF, muszą zostać utworzone przy użyciu typu rekordu TXT. Stanowi również, czy typ rekordu SPF jest przestarzały.

**Rekordy SPF są obsługiwane przez usługę Azure DNS i musi zostać utworzony przy użyciu typu rekordu TXT.** Przestarzałe typu rekordu SPF nie jest obsługiwane. Po użytkownik [zaimportować plik strefy DNS](../articles/dns/dns-import-export.md), wszystkie rekordy SPF, korzystających z typu rekordu SPF są konwertowane na typ rekordu TXT.
