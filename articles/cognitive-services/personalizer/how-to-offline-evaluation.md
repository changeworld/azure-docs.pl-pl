---
title: Ocena w trybie offline — Personalizer
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak analizować pętli uczenie na ocenę w trybie offline
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: b719e6e693471415350007a4f4fabed917b8e12d
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722314"
---
# <a name="how-to-analyze-your-learning-loop-with-an-offline-evaluation"></a>Jak analizować pętli uczenie na ocenę w trybie offline


Dowiedz się, jak wykonać ocenę w trybie offline i zrozumienie wyników.

W trybie offline ocen umożliwiają pomiar sposobu skutecznej Personalizer jest porównywany z aplikacji domyślne zachowanie, Dowiedz się, jakie funkcje są największy personalizacji i Poznaj nowe machine learning ustawienia automatycznie.

Przeczytaj o [ocen w trybie Offline](concepts-offline-evaluation.md) Aby dowiedzieć się więcej.


## <a name="prerequisites"></a>Wymagania wstępne

1. Konieczne jest posiadanie pętlę Personalizer skonfigurowane
1. Pętla Personalizer musi mieć co najmniej 50 000 zdarzeń w jej dzienników dla oceny znaczących wyników.

Opcjonalnie może także zostały wcześniej wyeksportowane _nauka zasad_ pliki można porównać i testowanie w tej samej wersji ewaluacyjnej.

## <a name="steps-to-start-a-new-offline-evaluation"></a>Czynności w celu uruchomienia nowej oceny w trybie Offline

1. Znajdź zasób pętli personalizacji w witrynie Azure portal.
1. Przejdź do sekcji "Ocena".
1. Kliknij pozycję Nowa ocena
1. Wybierz datę początkową i końcową, oceny w trybie offline. Są to daty z przeszłości, które określają zakres danych do użycia podczas obliczania. Te dane muszą znajdować się w dziennikach, jak to określono w [przechowywanie danych](how-to-settings.md) ustawienie.
1. Opcjonalnie można przekazać zasad uczenia. 
1. Określ, czy Personalizer należy utworzyć zasadę zoptymalizowane Learning na podstawie zachowania użytkowników, w tym okresie.
1. Uruchom ocenę

## <a name="results"></a>Wyniki

Oceny może potrwać dłuższy czas, w zależności od ilości danych do przetwarzania, liczba uczenia zasady, aby porównać, oraz tego, czy zażądano optymalizacji.

Po zakończeniu możesz zobaczyć następujące wyniki:

1. Porównywanie zasad nauki, w tym:
    * **Zasady usługi online**: Bieżące zasady Learning używane w Personalizer
    * **Baseline**: Aplikacja domyślnie (zgodnie z ustaleniami pierwszej akcji, które są wysyłane w wywołaniach rangi)
    * **Losowe zasad**: Urojone rangi zachowanie zawsze zwraca losowym akcje niż podana.
    * **Zasady niestandardowe**: Dodatkowe zasady Learning przekazane podczas uruchamiania oceny.
    * **Zasady zoptymalizowane pod kątem**: Jeśli ocena uruchomiono opcję, aby odnaleźć zoptymalizowanych zasad, również zostaną porównane, i będzie można ją pobrać lub ustawić, jego zasady szkolenia online, zastępując bieżący.

1. Skuteczność [funkcji](concepts-features.md) dla akcji i kontekstu.


## <a name="more-information"></a>Więcej informacji

* Dowiedz się, [jak ocen Praca w trybie offline](concepts-offline-evaluation.md).