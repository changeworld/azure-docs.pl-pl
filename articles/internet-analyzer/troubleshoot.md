---
title: Rozwiązywanie problemów z analizatorem internetowym platformy Azure | Microsoft Docs
description: Informacje dotyczące rozwiązywania problemów z usługą Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: 74bf0422bbe6c2c1c84365c1e8f9329a01ff9fdd
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75909036"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Rozwiązywanie problemów z analizatorem internetowym platformy Azure

Ten artykuł zawiera kroki rozwiązywania problemów dotyczących typowych problemów z analizatorem internetowym.

## <a name="azure-portal"></a>Azure Portal
**"Nie zebrano wystarczającej ilości pomiarów dla tej karty wyników" w sekcji kart wyników**

Należy pamiętać, że:
- Skrypt klienta musi być osadzony w witrynie sieci Web **https** . Pomiary nie będą zbierane, jeśli skrypt zostanie uruchomiony w witrynie sieci Web (**http://** ) lub lokalnie (**File://** ).
- Dane pomiarowe będą zbierane tylko wtedy, gdy skrypt klienta profilu analizatora internetowego został osadzony w aplikacji, która odbiera rzeczywisty ruch użytkownika. Ruch syntetyczny (na przykład testy wydajności WebApp platformy Azure) nie zazwyczaj wykonuje osadzony kod JavaScript, więc żadne pomiary nie będą generowane przez ten typ ruchu.
- Szeregi czasowe są generowane raz na godzinę, więc musisz poczekać co najmniej przez czas na wyświetlenie nowych danych pomiarów.
- Karty wyników są generowane codziennie (na koniec każdego dnia, czasu UTC).
- Karty wyników są generowane tylko wtedy, gdy zebrano ponad 100 pomiarów dla wybranej kombinacji filtru (test, okres, kraj itp.).

## <a name="next-steps"></a>Następne kroki
Przeczytaj [często zadawane pytania dotyczące narzędzia Internet Analyzer](internet-analyzer-faq.md)
