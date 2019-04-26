---
title: Czym jest usługa Custom Decision Service?
titlesuffix: Azure Cognitive Services
description: Ten artykuł zawiera omówienie usługi Custom Decision Service.
services: cognitive-services
author: alekh
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: overview
ms.date: 05/08/2018
ms.author: slivkins
ms.openlocfilehash: f4a0f519fe83162e7e161c3fda2b78a94b1d4bd1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60507831"
---
# <a name="what-is-custom-decision-service"></a>Czym jest usługa Custom Decision Service?

W typowej aplikacji internetowej lub mobilnej strona główna zawiera łącza do wielu artykułów lub innego rodzaju treści. Podczas ładowania strony głównej aplikacja może wysłać do usługi Custom Decision Service żądanie z zapytaniem o ranking artykułów na tej stronie. Gdy użytkownik wybierze artykuł kliknięciem, do usługi Custom Decision Service można wysłać drugie żądanie rejestrujące wynik tej decyzji użytkownika.

Korzystanie z usługi Custom Decision Service jest łatwe, ponieważ wymaga tylko kanału RSS treści aplikacji i dodania do aplikacji kilku wierszy kodu JavaScript.

Custom Decision Service przekształca treści w cechy służące do uczenia maszynowego. System korzysta z tych cech, aby zinterpretować tekst, obrazy, filmy i ogólny przekaz treści. Ta usługa używa kilku innych usług [Microsoft Cognitive Services](https://www.microsoft.com/cognitive-services), w tym [Entity Linking](../entitylinking/home.md), [Analiza tekstu](../text-analytics/overview.md), [Rozpoznawanie emocji](../emotion/home.md) i [Przetwarzanie obrazów](../computer-vision/home.md).

Do typowych zastosowań usługi Custom Decision Service należą:

* personalizowanie artykułów w witrynie z wiadomościami;
* personalizowanie filmów w portalu multimedialnym;
* optymalizacja reklam lub stron internetowych, do których kierują reklamy;
* tworzenie rankingu polecanych produktów w sklepie internetowym.

Usługa Custom Decision Service jest obecnie dostępna w *bezpłatnej wersji zapoznawczej*. Może ona spersonalizować listę artykułów w witrynie lub aplikacji. Wydobywanie cech działa najlepiej w przypadku treści w języku angielskim. Dostępna jest [ograniczona obsługa](../text-analytics/overview.md) innych języków: hiszpańskiego, francuskiego, niemieckiego, portugalskiego i japońskiego. Niniejsza dokumentacja będzie aktualizowana po udostępnieniu nowych funkcji.

Usługi Custom Decision Service można używać w aplikacjach spoza dziedziny personalizacji treści. W takich aplikacjach być może warto użyć niestandardowej wersji zapoznawczej. [Skontaktuj się z nami](https://azure.microsoft.com/overview/sales-number/), aby dowiedzieć się więcej.

## <a name="api-usage-modes"></a>Tryby używania interfejsu API

Usługi Custom Decision Service można używać na stronach internetowych i w aplikacjach mobilnych. Interfejsy API można wywoływać z przeglądarki lub z aplikacji. Sposób używania interfejsu API jest podobny w obu przypadkach, ale pewne szczegóły są różne.

## <a name="glossary-of-terms"></a>Słownik

W niniejszej dokumentacji często występują następujące terminy:

* **Zbiór przetwarzany**: zbiór elementów zawartości, których ranking ma dostarczyć usługa Custom Decision Service. Te zbiór można wskazać jako punkt końcowy *RSS* lub *Atom*.
* **Ranking**: każde żądanie wysyłane do usługi Custom Decision Service określa co najmniej jeden zbiór przetwarzany. System pobiera wszystkie pozycje zawartości z tych zbiorów i jako odpowiedź zwraca je w kolejności zgodnej z rankingiem.
* **Funkcja wywołania zwrotnego**: ta funkcja, tworzona przez programistę aplikacji, renderuje zawartość w interfejsie użytkownika aplikacji. Zawartość jest ułożona w kolejności odpowiadającej rankingowi ustalonemu przez usługę Custom Decision Service.
* **Nagroda**: miara reakcji użytkownika na wyświetloną zawartość. Usługa Custom Decision Service mierzy reakcję użytkownika na podstawie kliknięć. Kliknięcia są zgłaszane do systemu za pomocą kodu umieszczonego w aplikacji przez programistę.

## <a name="next-steps"></a>Kolejne kroki

* [Zarejestruj swoją aplikację](custom-decision-service-get-started-register.md) w usłudze Custom Decision Service
* Zoptymalizuj [stronę internetową](custom-decision-service-get-started-browser.md) lub [aplikację na smartfona](custom-decision-service-get-started-app.md).
* Zapoznaj się z [dokumentacją interfejsu API](custom-decision-service-api-reference.md), aby dowiedzieć się więcej o funkcjach usługi.
