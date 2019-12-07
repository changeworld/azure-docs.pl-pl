---
title: Rozwiązywanie problemów z analizatorem internetowym platformy Azure | Microsoft Docs
description: Informacje dotyczące rozwiązywania problemów z usługą Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: a7216e697680bcc049d2ceb4caec74adfc1760b0
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74897392"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Rozwiązywanie problemów z analizatorem internetowym platformy Azure

Ten artykuł zawiera kroki rozwiązywania problemów dotyczących typowych problemów z analizatorem internetowym.

## <a name="azure-portal"></a>Portal Azure
**"Nie zebrano wystarczającej ilości pomiarów dla tej karty wyników" w sekcji kart wyników**

Należy pamiętać, że:
- Dane pomiarowe będą zbierane tylko wtedy, gdy skrypt klienta profilu analizatora internetowego został osadzony w aplikacji, która odbiera rzeczywisty ruch użytkownika. Ruch syntetyczny (na przykład testy wydajności WebApp platformy Azure) nie zazwyczaj wykonuje osadzony kod JavaScript, więc żadne pomiary nie będą generowane przez ten typ ruchu.
- Szeregi czasowe są generowane raz na godzinę, więc musisz poczekać co najmniej przez czas na wyświetlenie nowych danych pomiarów.
- Karty wyników są generowane codziennie (na koniec każdego dnia, czasu UTC).
- Karty wyników są generowane tylko wtedy, gdy zebrano ponad 100 pomiarów dla wybranej kombinacji filtru (test, okres, kraj itp.).

## <a name="next-steps"></a>Następne kroki
Przeczytaj [często zadawane pytania dotyczące narzędzia Internet Analyzer](internet-analyzer-faq.md)
