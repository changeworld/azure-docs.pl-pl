---
title: Dostosowywanie modelu marek w Video Indexer — platformy Azure
titlesuffix: Azure Media Services
description: Ten artykuł zawiera omówienie modelu marek w Video Indexer i sposobami ich dostosowywania.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 863dbd9a6044ee33ae39ac9693a7d4f74382b9c7
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65799672"
---
# <a name="customize-a-brands-model-in-video-indexer"></a>Dostosowywanie modelu marek w indeksatora wideo

Usługa Video Indexer obsługuje wykrywanie marki mowy i tekstu podczas indeksowania i indeksowanie zawartości audio i wideo. Funkcja wykrywania marki identyfikuje wzmianek o produktach, usługach i firm zaproponowana przez Bing marek w bazie danych. Na przykład Microsoft jest wymieniony w zawartości wideo lub audio lub zostanie ona wyświetlona w tekście visual w filmach wideo, Video Indexer wykrywa ją jako marki w zawartości. Marek są rozróżniane od innych postanowień przy użyciu kontekstu.

Wykrywanie na marki przydaje się w wielu różnych scenariuszy biznesowych, takich jak zawartość archiwum i odnajdywania, kontekstowe reklamy, analiza mediów społecznościowych, detaliczna konkurować analizy i wiele innych. Funkcję wykrywania marki indeksator wideo umożliwia wzmianki marki indeksu w mowy i tekstu, za pomocą usługi Bing przez marek w bazie danych, a także z dostosowywaniem, tworząc niestandardowy model marki dla każdego konta Video Indexer. Funkcja niestandardowej modelu marek umożliwia wybranie, czy Video Indexer wykryje marek bazy danych marek Bing i wykluczyć pewne marek miałyby wykryto (zasadniczo tworzy czarnej listy marek) i zawierać znaki firmowe, które powinny być częścią usługi model, który może nie być w bazie danych marek Bing (zasadniczo tworzy białą listę marek). Niestandardowy model marek, którą tworzysz tylko będą dostępne w ramach konta, w którym został utworzony model.

## <a name="out-of-the-box-detection-example"></a>Poza przykład wykrywania pola

W [Microsoft Build 2017 dnia 2](https://www.videoindexer.ai/media/ed6ede78ad/) prezentacji, markę "Microsoft Windows" pojawia się wiele razy. Czasami w transkrypcji, czasami jako tekstu i nigdy nie verbatim. Usługa Video Indexer wykrywa dużej dokładności, termin jest w rzeczywistości marki, na podstawie kontekstu, obejmującego ponad 90k marek gotowych i stale aktualizacji. Godzinie 02:25 Video Indexer wykrywa marki z wypowiedzi, a następnie ponownie na 02:40 z tekstu, który jest częścią logo systemu windows.

![Omówienie marki](./media/content-model-customization/brands-overview.png)

Mówić o korzystaniu z okien w kontekście budowy nie wykrywa słowo "Windows" jako marki i te same pola, firmy Apple, Fox, itp., oparte na zaawansowanych algorytmów uczenia maszynowego, które odróżnić od kontekstu. Wykrywanie na marki działa w przypadku naszej obsługiwanych języków. Kliknij tutaj, aby [pełna Microsoft Build 2017 dnia 2 prezentacja wideo i indeks](https://www.videoindexer.ai/media/ed6ede78ad/).

Aby wyświetlić własne marki, zapoznaj się z następnych kroków.

## <a name="next-steps"></a>Kolejne kroki

[Dostosuj model marek przy użyciu interfejsów API](customize-brands-model-with-api.md)

[Dostosuj model marek witryny sieci Web](customize-brands-model-with-website.md)
