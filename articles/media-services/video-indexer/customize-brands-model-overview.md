---
title: Dostosowywanie modelu marek w indeksatorze wideo — Azure
titleSuffix: Azure Media Services
description: W tym artykule przedstawiono omówienie modelu marek w indeksatorze wideo i sposobu jego dostosowania.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: ca3d825fb2f4184448cc279d9408f47ad4ad004a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838370"
---
# <a name="customize-a-brands-model-in-video-indexer"></a>Dostosowywanie modelu marki w indeksatorze wideo

Indeksator wideo obsługuje wykrywanie marki z mowy i tekstu wizualnego podczas indeksowania i ponownego indeksowania zawartości wideo i audio. Funkcja wykrywania marki identyfikuje wzmianki o produktach, usługach i firmach sugerowane przez bazę danych marek Bing. Na przykład jeśli firma Microsoft jest wymieniona w zawartości wideo lub audio lub jeśli jest wyświetlana w tekście wizualnym w filmie wideo, indeksator wideo wykrywa go jako markę w zawartości. Marki są odznaczone od innych terminów przy użyciu kontekstu.

Wykrywanie marki jest przydatne w wielu różnych scenariuszach biznesowych, takich jak archiwizacja i odkrywanie treści, reklama kontekstowa, analiza mediów społecznościowych, analiza konkurencji detalicznej i wiele innych. Wykrywanie marki indeksatora wideo umożliwia indeksowanie wzmianek o marce w mowie i tekście wizualnym, przy użyciu bazy danych marek Bing, a także z dostosowaniem przez tworzenie niestandardowego modelu marek dla każdego konta indeksatora wideo. Niestandardowa funkcja modelu Marki pozwala wybrać, czy Indeksator wideo wykryje marki z bazy danych marek Bing, wykluczy niektóre marki z wykrycia (zasadniczo tworząc czarną listę marek) i uwzględni marki, które powinny być częścią Twojej bazy danych modelu, który może nie znajdować się w bazie danych marek Bing (zasadniczo tworząc białą listę marek). Utworzony model marki niestandardowy będzie dostępny tylko na koncie, na którym utworzono model.

## <a name="out-of-the-box-detection-example"></a>Przykład wykrywania po wyjęciu z pudełka

W prezentacji [Microsoft Build 2017 Dzień 2,](https://www.videoindexer.ai/media/ed6ede78ad/) marka "Microsoft Windows" pojawia się wiele razy. Czasami w transkrypcji, czasami jako tekst wizualny, a nigdy tak dosłownie. Video Indexer wykrywa z dużą precyzją, że termin jest rzeczywiście marki w oparciu o kontekst, obejmujące ponad 90k marek po wyjęciu z pudełka, i stale aktualizowane. O 02:25 Indeksator wideo wykrywa markę z mowy, a następnie ponownie o 02:40 z tekstu wizualnego, który jest częścią logo systemu Windows.

![Przegląd marek](./media/content-model-customization/brands-overview.png)

Mówienie o oknach w kontekście budowy nie wykryje słowa "Windows" jako marki, i to samo dla Box, Apple, Fox, itp., w oparciu o zaawansowane algorytmy uczenia maszynowego, które wiedzą, jak odróżnić od kontekstu. Wykrywanie marki działa dla wszystkich obsługiwanych przez nas języków. Kliknij [tutaj, aby uzyskać pełny Microsoft Build 2017 Dzień 2 keynote wideo i indeks](https://www.videoindexer.ai/media/ed6ede78ad/).

Aby zabrać ze sobą własne marki, zapoznaj się z kolejnymi krokami.

## <a name="next-steps"></a>Następne kroki

[Dostosowywanie modelu marki przy użyciu interfejsów API](customize-brands-model-with-api.md)

[Dostosowywanie modelu marek za pomocą strony internetowej](customize-brands-model-with-website.md)
