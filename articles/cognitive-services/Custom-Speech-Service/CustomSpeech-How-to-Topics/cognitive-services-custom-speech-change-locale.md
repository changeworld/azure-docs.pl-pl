---
title: Obsługiwane ustawienia regionalne i języki w niestandardowej usługi rozpoznawania mowy na platformie Azure | Dokumentacja firmy Microsoft
description: Przegląd obsługiwanych języków usługi mowy niestandardowe w usługach kognitywnych.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: c378fd951f9cd04884f44fbec5accb5d9a886bfe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346888"
---
# <a name="supported-locales-in-custom-speech-service"></a>Obsługiwanych ustawień regionalnych w niestandardowej usługi rozpoznawania mowy
Usługa mowy niestandardowe obecnie obsługuje dostosowywania modeli w następujących lokalizacjach:

| Typ modelu | Obsługa języków |
|----|-----|
| Modele akustycznego | Angielski (US) (en US) |
| Modele języka | Angielski (US) (en US), chiński (zh-CN) |

Dostosowywanie modelu akustycznego jest obsługiwana tylko w US English, ale importowanie danych akustycznych chińskiej wersji językowej jest obsługiwane na potrzeby testowania w trybie offline niestandardowych modeli języka chińskiego.

Przed podjęciem działania należy wybrać odpowiednie ustawienia regionalne. Bieżące ustawienia regionalne jest wskazane tytułu tabeli dla wszystkich danych, modelu i strony. Aby zmienić ustawienia regionalne, kliknij przycisk "Zmień ustawienia regionalne" znajdujący się pod tytułem tabeli. Spowoduje to przejście do strony potwierdzenia ustawień regionalnych. Kliknij przycisk "OK", aby powrócić do tabeli.

Należy stosować kolejne kroki
* Dowiedz się [sposób tworzenia niestandardowych modelu akustycznego](cognitive-services-custom-speech-create-acoustic-model.md) aby poprawić dokładność rozpoznawania
* Dowiedz się [sposobu tworzenia modelu języków niestandardowych](cognitive-services-custom-speech-create-language-model.md) zwiększające szybkość rozpoznawania
* Postępuj zgodnie z [wytyczne przekształcania](cognitive-services-custom-speech-transcription-guidelines.md) przygotowania danych
