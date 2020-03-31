---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: 9cc650cea17acb8d89933c819c4ca60e2c459d1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183862"
---
Rekordy struktury zasad nadawcy (SPF) służą do określania, które serwery poczty e-mail mogą wysyłać wiadomości e-mail w imieniu nazwy domeny. Prawidłowa konfiguracja rekordów SPF jest ważne, aby uniemożliwić adresatom oznaczanie wiadomości e-mail jako wiadomości-śmieci.

Kontrolery RFC DNS pierwotnie wprowadzono nowy typ rekordu SPF do obsługi tego scenariusza. Aby obsługiwać starsze serwery nazw, zezwalały one również na użycie typu rekordu TXT do określania rekordów SPF. Ta niejednoznaczność doprowadziła do zamieszania, które zostało rozwiązane przez [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1). Stwierdza, że rekordy SPF muszą być tworzone przy użyciu typu rekordu TXT. Stwierdza również, że typ rekordu SPF jest przestarzały.

**Rekordy SPF są obsługiwane przez usługę Azure DNS i muszą być tworzone przy użyciu typu rekordu TXT.** Przestarzały typ rekordu SPF nie jest obsługiwany. Podczas [importowania pliku strefy DNS](../articles/dns/dns-import-export.md)wszystkie rekordy SPF, które używają typu rekordu SPF, są konwertowane na typ rekordu TXT.
