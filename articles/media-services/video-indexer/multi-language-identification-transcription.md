---
title: Automatyczne identyfikowanie i transkrybowanie zawartości wielojęzycznej za pomocą indeksatora wideo
titleSuffix: Azure Media Services
description: W tym temacie pokazano, jak automatycznie identyfikować i transkrybować zawartość wielojęzyczną za pomocą indeksatora wideo.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/01/2019
ms.author: juliako
ms.openlocfilehash: f0dede42891069bb5d01ddc33f3797c20c5493d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72968740"
---
# <a name="automatically-identify-and-transcribe-multi-language-content-preview"></a>Automatyczne identyfikowanie i transkrybowanie zawartości wielojęzycznej (wersja zapoznawcza)

Indeksator wideo obsługuje automatyczną identyfikację języka i transkrypcję w wielu językach. Proces ten polega na automatycznym identyfikowaniu języka mówionego w różnych segmentach z audio, wysyłaniu każdego segmentu pliku multimedialnego do transkrypcji i łączeniu transkrypcji z powrotem do jednej ujednoliconej transkrypcji. 

## <a name="choosing-multilingual-identification-on-indexing-with-portal"></a>Wybór wielojęzycznej identyfikacji podczas indeksowania za pomocą portalu

Podczas przesyłania i indeksowania filmu możesz wybrać **wykrywanie** w wielu językach. Możesz też wybrać **wykrywanie w wielu językach** podczas ponownego indeksowania filmu. W poniższych krokach opisano sposób ponownego indeksu:

1. Przejdź do witryny internetowej [Video Indexer](https://vi.microsoft.com/) i zaloguj się.
1. Przejdź do strony **Biblioteka** i umieść wskaźnik myszy na nazwie filmu, który chcesz ponownie wyeksliować. 
1. W prawym dolnym rogu kliknij przycisk **Ponownie indeksuj wideo.** 
1. W oknie dialogowym **Ponowne indeksowanie wideo** wybierz opcję **Wykrywanie w wielu językach** z listy rozwijanej **Język źródłowy wideo.**

    * Gdy film jest indeksowany jako wielojęzyczny, strona wglądu będzie zawierać tę opcję i pojawi się dodatkowy typ wglądu, umożliwiający użytkownikowi wyświetlanie, który segment jest przepisyowany w którym języku "Język mówiony".
    * Tłumaczenie na wszystkie języki jest w pełni dostępne w 2015 roku.
    * Wszystkie inne spostrzeżenia pojawią się w wykrytym języku głównym — to jest język, który pojawił się najbardziej w dźwięku.
    * Napisy kodowane na odtwarzaczu są również dostępne w wielu językach.

![Środowisko portalu](./media/multi-language-identification-transcription/portal-experience.png)

## <a name="choosing-multilingual-identification-on-indexing-with-api"></a>Wybór wielojęzycznej identyfikacji podczas indeksowania za pomocą interfejsu API

Podczas indeksowania lub [ponownego indeksowania](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) wideo za `multi-language detection` pomocą interfejsu `sourceLanguage` API wybierz opcję w parametrze.

### <a name="model-output"></a>Dane wyjściowe modelu

Model pobierze wszystkie języki wykryte w filmie na jednej liście

```json
"sourceLanguage": null,
"sourceLanguages": [
    "es-ES",
    "en-US"
],
```

Ponadto każde wystąpienie w sekcji transkrypcji będzie zawierać język, w którym został

```json
{
  "id": 136,
  "text": "I remember well when my youth Minister took me to hear Doctor King I was a teenager.",
  "confidence": 0.9343,
  "speakerId": 1,
  "language": "en-US",
  "instances": [
    {
       "adjustedStart": "0:21:10.42",
       "adjustedEnd": "0:21:17.48",
       "start": "0:21:10.42",
       "end": "0:21:17.48"
    }
  ]
},
```

## <a name="guidelines-and-limitations"></a>Wytyczne i ograniczenia

* Zestaw obsługiwanych języków: angielski, francuski, niemiecki, hiszpański.
* Obsługa zawartości wielojęzycznej w maksymalnie trzech obsługiwanych językach.
* Jeśli dźwięk zawiera języki inne niż obsługiwana lista powyżej, wynik jest nieoczekiwany.
* Minimalna długość segmentu do wykrycia dla każdego języka – 15 sekund.
* Przesunięcie wykrywania języka wynosi średnio 3 sekundy.
* Oczekuje się, że mowa będzie ciągła. Częste zmiany między językami mogą mieć wpływ na wydajność modeli.
* Mowa osób niebędących użytkownikami natywnych może mieć wpływ na wydajność modelu (na przykład, gdy głośniki używają języka ojczystego i przełączają się na inny język).
* Model jest przeznaczony do rozpoznawania spontanicznej mowy konwersacyjnej z rozsądną akustyką dźwięku (nie poleceń głosowych, śpiewu itp.).
* Tworzenie i edytowanie projektu nie jest obecnie dostępne w przypadku filmów w wielu językach.
* Niestandardowe modele językowe nie są dostępne podczas korzystania z wykrywania wielu języków.
* Dodawanie słów kluczowych nie jest obsługiwane.
* Podczas eksportowania plików z podpisami kodowanych nie pojawi się wskażanie języka.
* Interfejs API transkrypcji aktualizacji nie obsługuje plików wielu języków.

## <a name="next-steps"></a>Następne kroki

[Omówienie usługi Video Indexer](video-indexer-overview.md)
