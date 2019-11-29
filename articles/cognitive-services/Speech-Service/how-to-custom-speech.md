---
title: Wprowadzenie do usługi Custom Speech-Speech
titleSuffix: Azure Cognitive Services
description: Custom Speech to zestaw narzędzi online, dzięki którym można oszacować i poprawić dokładność zamiany mowy na tekst dla aplikacji, narzędzi i produktów. Wszystkie te rzeczy są potrzebne do rozpoczęcia pracy z kilkuem plików audio. Skorzystaj z poniższych linków, aby rozpocząć tworzenie niestandardowego środowiska zamiany mowy na tekst.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 1d8e473afa2c8e7c0abf58e2dbc9caea030f2f5f
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2019
ms.locfileid: "74554983"
---
# <a name="what-is-custom-speech"></a>Co to jest Custom Speech?

[Custom Speech](https://aka.ms/customspeech) to zestaw narzędzi online, dzięki którym można oszacować i poprawić dokładność zamiany mowy na mowę firmy Microsoft dla aplikacji, narzędzi i produktów. Wszystkie te rzeczy są potrzebne do rozpoczęcia pracy z kilkuem plików audio. Skorzystaj z poniższych linków, aby rozpocząć tworzenie niestandardowego środowiska zamiany mowy na tekst.

## <a name="whats-in-custom-speech"></a>Co znajduje się w Custom Speech?

Przed rozpoczęciem wykonywania jakichkolwiek czynności mających Custom Speech musisz mieć konto platformy Azure i subskrypcję usługi Speech Services. Po otrzymaniu konta możesz przygotować swoje dane, wyszkolić i przetestować modele, sprawdzić jakość rozpoznawania, oszacować dokładność i ostatecznie wdrożyć i korzystać z niestandardowego modelu zamiany mowy na tekst.

Ten diagram przedstawia elementy wchodzące w skład [portalu Custom Speech](https://aka.ms/customspeech). Skorzystaj z poniższych linków, aby dowiedzieć się więcej o każdym z kroków.

![Wyróżnia różne składniki wchodzące w skład portalu Custom Speech.](./media/custom-speech/custom-speech-overview.png)

1. [Subskrybuj i Utwórz projekt](#set-up-your-azure-account) — Utwórz konto platformy Azure i Zasubskrybuj usługi mowy. Ta ujednolicona subskrypcja zapewnia dostęp do funkcji zamiany mowy na tekst, zamiany tekstu na mowę, tłumaczenia mowy i [portalu Custom Speech](https://speech.microsoft.com/customspeech). Następnie za pomocą subskrypcji usługi Speech Services Utwórz pierwszy projekt Custom Speech.

2. [Przekazywanie danych testowych](how-to-custom-speech-test-data.md) — przekazywanie danych testowych (plików audio) w celu oszacowania oferty zamiany mowy na tekst firmy Microsoft dla aplikacji, narzędzi i produktów.

3. [Inspekcja jakości rozpoznawania](how-to-custom-speech-inspect-data.md) — użyj [portalu Custom Speech](https://speech.microsoft.com/customspeech) , aby odtworzyć załadowane audio i sprawdzić jakość rozpoznawania mowy danych testowych. Aby uzyskać miary ilościowe, zobacz [Sprawdzanie danych](how-to-custom-speech-inspect-data.md).

4. [Oceń dokładność](how-to-custom-speech-evaluate-data.md) — Oceń dokładność modelu zamiany mowy na tekst. [Portal Custom Speech](https://speech.microsoft.com/customspeech) będzie zawierać *Współczynnik błędów wyrazów*, który może służyć do określenia, czy wymagane jest dodatkowe szkolenie. Jeśli dokładność jest zadowalająca, można używać interfejsów API usługi mowy bezpośrednio. Jeśli chcesz poprawić dokładność przez średnią wartość wynoszącą 5%-20%, Użyj karty **szkolenia** w portalu, aby przekazać dodatkowe dane szkoleniowe, takie jak transkrypcje z etykietami ludzkimi i powiązane teksty.

5. [Uczenie modelu](how-to-custom-speech-train-model.md) — Poprawa dokładności modelu zamiany mowy na tekst dzięki udostępnieniu zapisów pisanych (10 – 1000 godzin) i powiązanego tekstu (< 200 MB) wraz z danymi testu dźwiękowego. Te dane ułatwiają uczenie modelu zamiany mowy na tekst. Po przekształceniu, ponownym przetestowaniu i, jeśli wynik jest zadowalający, można wdrożyć model.

6. [Wdróż model](how-to-custom-speech-deploy-model.md) — Utwórz niestandardowy punkt końcowy dla modelu zamiany mowy na tekst i użyj go w aplikacjach, narzędziach lub produktach.

## <a name="set-up-your-azure-account"></a>Skonfiguruj swoje konto platformy Azure

Przed rozpoczęciem korzystania z [portalu Custom Speech](https://speech.microsoft.com/customspeech) w celu utworzenia modelu niestandardowego należy skorzystać z subskrypcji usługi Speech Services. Postępuj zgodnie z tymi instrukcjami, aby utworzyć standardową subskrypcję usługi Speech Services: [Utwórz subskrypcję mowy](get-started.md#try-the-speech-service-using-a-new-azure-account).

> [!NOTE]
> Pamiętaj, aby utworzyć standardowe subskrypcje (S0) i subskrypcje bezpłatnej wersji próbnej (F0) nie są obsługiwane.

Po utworzeniu konta platformy Azure i subskrypcji usługi Speech Services należy zalogować się do [portalu Custom Speech](https://speech.microsoft.com/customspeech) i połączyć swoją subskrypcję.

1. Pobierz klucz subskrypcji usługi Speech Services z Azure Portal.
2. Zaloguj się do [portalu Custom Speech](https://aka.ms/custom-speech).
3. Wybierz subskrypcję, na której chcesz korzystać, i Utwórz projekt mowy.
4. Jeśli chcesz zmodyfikować swoją subskrypcję, użyj ikony **koło zębate** znajdującej się w górnym obszarze nawigacji.

## <a name="how-to-create-a-project"></a>Jak utworzyć projekt

Zawartość, taka jak dane, modele, testy i punkty końcowe, są zorganizowane w **projekty** w [portalu Custom Speech](https://speech.microsoft.com/customspeech). Każdy projekt jest specyficzny dla domeny i kraju/języka. Na przykład możesz utworzyć projekt dla centrów wywołań, które używają języka angielskiego w Stany Zjednoczone.

Aby utworzyć swój pierwszy projekt, wybierz **mowę zamiany mowy na tekst/niestandardową**, a następnie kliknij pozycję **Nowy projekt**. Postępuj zgodnie z instrukcjami wyświetlanymi przez kreatora, aby utworzyć projekt. Po utworzeniu projektu powinny zostać wyświetlone cztery karty: **dane**, **testowanie**, **szkolenia**i **wdrożenia**. Skorzystaj z linków w [sekcji Następne kroki](#next-steps) , aby dowiedzieć się, jak korzystać z każdej karty.

## <a name="next-steps"></a>Następne kroki

* [Przygotowywanie i testowanie danych](how-to-custom-speech-test-data.md)
* [Inspekcja danych](how-to-custom-speech-inspect-data.md)
* [Oceń dane](how-to-custom-speech-evaluate-data.md)
* [Uczenie modelu](how-to-custom-speech-train-model.md)
* [Wdrażanie modelu](how-to-custom-speech-deploy-model.md)
