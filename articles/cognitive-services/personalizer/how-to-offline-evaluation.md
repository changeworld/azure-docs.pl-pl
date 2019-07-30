---
title: Ocena w trybie offline — Personalizowanie
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak analizować pętlę szkoleniową za pomocą oceny w trybie offline
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: f14403422e2c783d75634bb929d8c2130bd505b6
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663871"
---
# <a name="how-to-analyze-your-learning-loop-with-an-offline-evaluation"></a>Jak analizować pętlę szkoleniową za pomocą oceny w trybie offline


Dowiedz się, jak przeprowadzić ocenę w trybie offline i zrozumieć wyniki.

Oceny w trybie offline umożliwiają mierzenie, jak skuteczny program Personalizuj jest porównywany z domyślnym zachowaniem aplikacji, Dowiedz się, jakie funkcje są najbardziej związane z personalizacją, i automatycznie Odkryj nowe ustawienia uczenia maszynowego.

Przeczytaj o [ocenach w trybie offline](concepts-offline-evaluation.md) , aby dowiedzieć się więcej.


## <a name="prerequisites"></a>Wymagania wstępne

1. Musisz mieć skonfigurowaną pętlę personalizacji
1. Pętla personalizacji musi mieć co najmniej 50 000 zdarzeń w swoich dziennikach, aby uzyskać istotne wyniki oceny.

Opcjonalnie możesz również wcześniej wyeksportować pliki _zasad uczenia_ , które można porównywać i testować w tej samej ocenie.

## <a name="steps-to-start-a-new-offline-evaluation"></a>Kroki umożliwiające rozpoczęcie nowej oceny w trybie offline

1. Znajdź zasób pętli personalizacji w Azure Portal.
1. Przejdź do sekcji "Ocena".
1. Kliknij nową ocenę
1. Wybierz datę początkową i końcową dla oceny w trybie offline. Są to daty w przeszłości, które określają zakres danych do użycia podczas obliczania. Te dane muszą być obecne w dziennikach, zgodnie z ustawieniami [przechowywania danych](how-to-settings.md) .
1. Opcjonalnie możesz przekazać własne zasady uczenia się. 
1. Określ, czy Personalizacja ma tworzyć zoptymalizowane zasady uczenia na podstawie zachowania użytkownika zaobserwowanego w tym okresie.
1. Rozpocznij Obliczanie

## <a name="results"></a>Wyniki

Obliczenia mogą zająć dużo czasu, w zależności od ilości danych do przetworzenia, liczby zasad uczenia do porównania oraz od tego, czy Optymalizacja zażądała.

Po zakończeniu można zobaczyć następujące wyniki:

1. Porównanie zasad uczenia, w tym:
    * **Zasady online**: Bieżące zasady uczenia używane w programie Personalizacja
    * **Linia bazowa**: Wartość domyślna aplikacji (zgodnie z pierwszą akcją wysłaną w wywołaniach rangi),
    * **Zasady losowe**: Zachowanie rangi urojonej, które zawsze zwraca losowo wybór akcji z dostarczonych.
    * **Zasady niestandardowe**: Dodatkowe zasady uczenia zostały przekazane podczas uruchamiania oceny.
    * **Zoptymalizowane zasady**: Jeśli Ocena została rozpoczęta z opcją odnalezienia zoptymalizowanych zasad, zostanie również porównana i będzie można ją pobrać lub wprowadzić zasady uczenia online, zastępując bieżące.

1. Skuteczność [funkcji](concepts-features.md) dla akcji i kontekstu.


## <a name="more-information"></a>Więcej informacji

* Dowiedz się, [jak działają oceny w trybie offline](concepts-offline-evaluation.md).