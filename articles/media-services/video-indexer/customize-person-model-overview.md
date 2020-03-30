---
title: Dostosowywanie modelu osoby w indeksatorze wideo — Azure
titleSuffix: Azure Media Services
description: W tym artykule przedstawiono omówienie modelu osoby w indeksatorze wideo i jak go dostosować.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 3fabba98cb137975da749411ca9accb5a951742d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838293"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Dostosowywanie modelu osoby w indeksatorze wideo

Indeksator wideo obsługuje rozpoznawanie gwiazd w Twoich filmach. Funkcja rozpoznawania gwiazd obejmuje około miliona twarzy na podstawie powszechnie żądanego źródła danych, takiego jak IMDB, Wikipedia i najlepsi influencerzy LinkedIn. Twarze, które nie są rozpoznawane przez indeksator wideo są nadal wykrywane, ale pozostają bez nazwy. Klienci mogą tworzyć niestandardowe modele osób i włączyć indeksator wideo do rozpoznawania twarzy, które nie są domyślnie rozpoznawane. Klienci mogą tworzyć te modele osób, łącząc imię i nazwisko osoby z plikami obrazów twarzy danej osoby.  

Jeśli Twoje konto obsługuje różne przypadki użycia, możesz skorzystać z możliwości utworzenia wielu modeli osób na konto. Jeśli na przykład zawartość konta ma być posortowana na różne kanały, można utworzyć oddzielny model osoby dla każdego kanału. 

> [!NOTE]
> Każdy model osoby obsługuje do 1 miliona osób, a każde konto ma limit 50 modeli person. 

Po utworzeniu modelu można go użyć, podając identyfikator modelu określonego modelu osoby podczas przesyłania/indeksowania lub ponownego indeksowania wideo. Trenuj nową twarz dla filmu, aktualizuje określony model niestandardowy, z który był skojarzony. 

Jeśli nie potrzebujesz obsługi wielu modeli person, nie przypisuj identyfikatora modelu osoby do filmu podczas przesyłania/indeksowania lub ponownego indeksowania. W takim przypadku indeksator wideo użyje domyślnego modelu osoby na twoim koncie. 

Za pomocą witryny video indexer można edytować twarze wykryte w filmie i zarządzać wieloma niestandardowymi modelami osób na koncie, zgodnie z opisem w [temacie Dostosowywanie osoby przy użyciu tematu witryny sieci Web.](customize-person-model-with-website.md) Można również użyć interfejsu API, zgodnie z opisem w [polu Dostosuj model osoby przy użyciu interfejsów API](customize-person-model-with-api.md).
