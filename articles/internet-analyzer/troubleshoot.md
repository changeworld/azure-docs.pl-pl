---
title: Rozwiązywanie problemów z analizatorem internetowym platformy Azure | Dokumenty firmy Microsoft
description: Odwołanie do rozwiązywania problemów dla analizatora internetowego platformy Azure.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: a265278652c16b4682707470d183a02a55b9a0ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77069221"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Rozwiązywanie problemów z analizatorem internetowym platformy Azure

Ten artykuł zawiera kroki rozwiązywania problemów z typowymi problemami z analizatorem internetowym.

## <a name="things-to-keep-in-mind"></a>Rzeczy, o których należy pamiętać
- Skrypt klienta musi być osadzony w witrynie **HTTPS.** Pomiary nie będą zbierane, jeśli skrypt działa w postaci zwykłego tekstu **(http://)** lub lokalnej **(file://).**
- Dane pomiarowe będą zbierane tylko wtedy, gdy skrypt klienta profilu analizatora internetowego został osadzony w aplikacji, która odbiera rzeczywisty ruch użytkownika. Ruch syntetyczny (na przykład testy wydajności usługi Azure WebApp) zazwyczaj nie wykonuje osadzonego kodu Javascript, więc żadne pomiary nie zostaną wygenerowane przez ten typ ruchu.

## <a name="azure-portal"></a>Portal Azure
**"Karta wyników nie została wygenerowana dla wybranej kombinacji filtrów" w sekcji Karty wyników**
- Karty wyników są generowane codziennie (na koniec każdego dnia, czas UTC).
- Karty wyników są generowane tylko wtedy, gdy zebrano więcej niż 100 pomiarów dla wybranej kombinacji filtrów (test, okres czasu, kraj itp.).

**"Całkowita liczba pomiarów" wynosi zero dla jednego lub obu punktów końcowych w teście**
- Szeregi czasowe i liczby pomiarów są obliczane raz na godzinę, więc musisz poczekać co najmniej tyle czasu, aby nowe dane pomiarowe się pojawiły.
- Analizator internetowy liczy tylko pomyślne pomiary (tj. odpowiedzi HTTP 200) do analizy. Jeśli jeden lub oba punkty końcowe w teście są nieosiągalne lub zwraca kod HTTP innych niż 200, będą wyświetlane z zerowym całkowitym pomiarem.

## <a name="next-steps"></a>Następne kroki
Przeczytaj często zadawane [pytania dotyczące analizatora internetowego](internet-analyzer-faq.md)
