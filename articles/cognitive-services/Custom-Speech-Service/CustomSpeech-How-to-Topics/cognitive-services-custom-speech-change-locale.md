---
title: Obsługiwane ustawienia regionalne i strony języków w usłudze Custom Speech Service na platformie Azure | Dokumentacja firmy Microsoft
description: Omówienie obsługiwanych języków Custom Speech Service w usługach Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 1f186681c7e46d2e47ed7eee55c8f61290c48fcb
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987530"
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
