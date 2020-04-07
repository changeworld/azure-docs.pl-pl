---
title: Informacje
description: Wprowadzenie do zdalnego renderowania platformy Azure
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: overview
ms.openlocfilehash: a06c63152cb56be6d94cccc472d2e1d65651d6ce
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679954"
---
# <a name="about-azure-remote-rendering"></a>Informacje o renderowaniu zdalnym platformy Azure

> [!IMPORTANT]
> **Renderowanie zdalne platformy Azure** jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

*Azure Remote Rendering* (ARR) to usługa, która umożliwia renderowanie wysokiej jakości, interaktywnej zawartości 3D w chmurze i przesyłanie strumieniowe w czasie rzeczywistym do urządzeń, takich jak HoloLens 2.

![Przykładowy model](../media/arr-engine.png)

Urządzenia swobodne mają ograniczoną moc obliczeniową do renderowania złożonych modeli. W przypadku wielu aplikacji byłoby jednak niedopuszczalne, aby w jakikolwiek sposób zmniejszyć wierność wizualną.

*Zdalne renderowanie* rozwiązuje ten problem, przenosząc obciążenie renderowania do wysokiej klasy procesorów GPU w chmurze. Aparat grafiki hostowanej w chmurze renderuje obraz, koduje go jako strumień wideo i przesyła strumieniowo go do urządzenia docelowego.

## <a name="hybrid-rendering"></a>Renderowanie hybrydowe

W większości aplikacji nie jest wystarczające, aby tylko renderować złożony model. Potrzebujesz również niestandardowego interfejsu użytkownika, aby zapewnić funkcjonalność dla użytkownika. Renderowanie zdalne platformy Azure nie zmusza do korzystania z dedykowanej struktury interfejsu użytkownika, zamiast tego obsługuje *renderowanie hybrydowe.* Oznacza to, że można renderować elementy na urządzeniu, przy użyciu preferowanej metody, takich jak [MRTK](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/GettingStartedWithTheMRTK.html).

Na końcu ramki zdalne renderowanie platformy Azure automatycznie łączy lokalnie renderowane treści z obrazem zdalnym. Jest nawet w stanie to zrobić z prawidłowym okluzji.

## <a name="multi-gpu-rendering"></a>Renderowanie wielu procesorów graficznych

Niektóre modele są zbyt skomplikowane, aby renderować z interakcją, nawet dla wysokiej klasy procesora graficznego. Szczególnie w wizualizacji przemysłowej jest to częsty problem. Aby jeszcze bardziej przesuwać limity, zdalne renderowanie platformy Azure może dystrybuować obciążenie do wielu procesorów GPU. Wyniki są scalane w jeden obraz, dzięki czemu proces jest całkowicie przezroczysty dla użytkownika.

## <a name="high-level-architecture"></a>Architektura wysokiego poziomu

Ten diagram ilustruje architekturę zdalnego renderowania:

![Architektura](./media/arr-high-level-architecture.png)

Pełny cykl generowania obrazu obejmuje następujące kroki:

1. Po stronie klienta: Konfiguracja ramki
    1. Kod: Dane wejściowe użytkownika są przetwarzane, wykres sceny jest aktualizowany
    1. Kod ARR: Aktualizacje wykresu sceny i przewidywana pozycja głowy wysyłane do serwera
1. Po stronie serwera: Zdalne renderowanie
    1. Aparat renderowania dystrybuuje renderowanie między dostępnymi procesorami graficznymi
    1. Dane wyjściowe z wielu procesorów graficznych są komponowane w jednym obrazie
    1. Obraz jest kodowany jako strumień wideo, wysyłany z powrotem do klienta
1. Po stronie klienta: Finalizacja
    1. Twój kod: Opcjonalna zawartość lokalna (UI, znaczniki, ...) jest renderowana
    1. Kod ARR: w przypadku "obecnego" lokalnie renderowana zawartość jest automatycznie scalana ze strumieniem wideo

Opóźnienie sieci jest głównym problemem. Czas przerówki między wysłaniem żądania a otrzymaniem wyniku jest zazwyczaj zbyt długi dla interaktywnych klatek na sekundę. W związku z tym w każdej chwili w locie może znajdować się więcej niż jedna klatka.

## <a name="next-steps"></a>Następne kroki

* [Wymagania systemowe](system-requirements.md)
* [Szybki start: renderowanie modelu za pomocą unity](../quickstarts/render-model.md)
