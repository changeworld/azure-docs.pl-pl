---
title: Obsługiwanych ustawień regionalnych i języków — Custom Speech Service
titlesuffix: Azure Cognitive Services
description: Omówienie obsługiwanych języków Custom Speech Service w usługach Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 34af6673689244364ab3a1fe3f2a6ab056ea8598
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47223959"
---
# <a name="supported-locales-in-custom-speech-service"></a>Obsługiwane ustawienia regionalne w usłudze Custom Speech Service
Custom Speech Service obecnie obsługuje dostosowywania modeli w następujących lokalizacjach:

| Typ modelu | Obsługa języków |
|----|-----|
| Modeli akustycznych | Angielski (US) (en US) |
| Modele językowe | Angielski (US) (en US), chiński (zh-CN) |

Dostosowanie modelu akustycznego jest tylko obsługiwana w US English, importowanie chińskich dane akustyczne jest obsługiwane na potrzeby testowania niestandardowych modeli języka chińskiego w trybie offline.

Przed podjęciem jakiegokolwiek działania należy wybrać odpowiednie ustawienia regionalne. Bieżące ustawienia regionalne są wskazywane w tytule tabeli na wszystkich stronach danych, modelu i wdrożenia. Aby zmienić ustawienia regionalne, kliknij przycisk "Zmień ustawienia regionalne" znajdujący się pod tytułem tabeli. Spowoduje to przejście do strony potwierdzenia ustawień regionalnych. Kliknij przycisk OK, aby powrócić do tabeli.

Należy stosować do następnych kroków
* Dowiedz się, [jak utworzyć niestandardowy model akustyczny](cognitive-services-custom-speech-create-acoustic-model.md) w celu zwiększenia dokładności rozpoznawania
* Dowiedz się, [sposobu tworzenia modelu języka niestandardowego](cognitive-services-custom-speech-create-language-model.md) można zwiększyć szybkość rozpoznawania
* Postępuj zgodnie z [wytycznych transkrypcji](cognitive-services-custom-speech-transcription-guidelines.md) do przygotowania danych
