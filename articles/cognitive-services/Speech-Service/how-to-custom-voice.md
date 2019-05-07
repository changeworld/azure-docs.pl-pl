---
title: Co to jest Custom Voice? -Usługi mowy
titlesuffix: Azure Cognitive Services
description: Custom Voice to zestaw narzędzi online, które pozwalają tworzyć mówiącą, jeden z rodzajem głosu dla Twojej marki. Wszystko, czego potrzebny na rozpoczęcie pracy jest kilka przydatnych plików audio i skojarzone transkrypcji. Skorzystaj z linków poniżej, aby rozpocząć tworzenie niestandardowego środowiska mowy na tekst.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: erhopf
ms.openlocfilehash: 8cf9dc6cbfc96448462aac3a64807f8beb6036ad
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65156913"
---
# <a name="get-started-with-custom-voice"></a>Rozpoczynanie pracy z usługą Custom Voice

Custom Voice to zestaw narzędzi online, które pozwalają tworzyć mówiącą, jeden z rodzajem głosu dla Twojej marki. Wszystko, czego potrzebny na rozpoczęcie pracy jest kilka przydatnych plików audio i skojarzone transkrypcji. Skorzystaj z linków poniżej, aby rozpocząć tworzenie niestandardowego środowiska zamiany tekstu na mowę.

## <a name="whats-in-custom-voice"></a>Jaka jest Custom Voice?

Przed rozpoczęciem z głosem niestandardowe, konieczne będzie konto platformy Azure i Subskrypcja usług przetwarzania mowy. Po utworzeniu konta możesz można przygotowywanie danych, uczenie i testowanie modeli, ocenić jakość dźwięku i ostatecznie wdrażanie modelu niestandardowego głosu.

Na poniższym diagramie prezentuje kroki, aby utworzyć model niestandardowych voice przy użyciu portalu usługi Custom Voice. Użyj linków, aby dowiedzieć się więcej.

![Diagram architektury usługi niestandardowych Voice](media/custom-voice/custom-voice-diagram.png)

1.  [Subskrypcja i Utwórz projekt](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice?branch=release-build-cogserv-speech-services#set-up-your-azure-account) — Tworzenie konta platformy Azure i utworzyć subskrypcję usług przetwarzania mowy. Ta subskrypcja ujednoliconego zapewnia dostęp do zamiany mowy na tekst, zamiany tekstu na mowę, tłumaczenie mowy i portalu usługi Custom Voice. Następnie przy użyciu posiadanej subskrypcji usług przetwarzania mowy, Utwórz swój pierwszy projekt Custom Voice.

2.  [Przekazywanie danych](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font?branch=release-build-cogserv-speech-services#upload-your-datasets) — przekazywanie danych (audio i tekst) przy użyciu niestandardowych Voice portal lub interfejsu API usługi Custom Voice. Z poziomu portalu można zbadać i ocenić wyniki Wymowa i współczynniki sygnał do szumu. Aby uzyskać więcej informacji, zobacz [sposób przygotowania danych dla niestandardowych Voice](how-to-custom-voice-prepare-data.md).

3.  [Uczenie modelu](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font?branch=release-build-cogserv-speech-services#build-your-voice-font) — korzystanie z Twoich danych w celu utworzenia modelu niestandardowych voice zamiany tekstu na mowę. Możesz uczyć modelu w różnych językach. Po szkoleniu Testowanie modelu, a jeśli jesteś zadowolony z wyników, należy wdrożyć model.

4.  [Model jest wdrażany](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font?branch=release-build-cogserv-speech-services#create-and-use-a-custom-endpoint) — Tworzenie niestandardowego punktu końcowego dla modelu głosowego zamiany tekstu na mowę i użyć jej do synteza mowy produktów, narzędzi i aplikacji.

## <a name="set-up-your-azure-account"></a>Konfigurowanie konta platformy Azure

Wymagana jest subskrypcja usług przetwarzania mowy, korzystać z portalu usługi Custom Speech, aby utworzyć niestandardowy model. Wykonaj te instrukcje, aby utworzyć subskrypcję usługi mowy na platformie Azure. Jeśli nie masz konta platformy Azure, możesz zarejestrować się na nową.  

Po utworzeniu konta platformy Azure i Subskrypcja usług przetwarzania mowy, musisz zalogować się do portalu usługi Custom Voice i Połącz z subskrypcją.

1. Pobierz klucz subskrypcji usług przetwarzania mowy z witryny Azure portal.
2. Zaloguj się do [portal Custom Voice](https://aka.ms/custom-voice).
3. Wybierz swoją subskrypcję i Utwórz projekt mowy.
4. Jeśli chcesz przełączyć się do innej subskrypcji mowy, użyj ikony koła zębatego, znajduje się w górnym menu nawigacyjnym.

> [!NOTE]
> Usługa Custom Voice nie obsługuje klucz 30-dniowej wersji próbnej bezpłatne. Konieczne jest posiadanie F0 lub klucza S0 utworzone na platformie Azure, zanim użyjesz usługi.

## <a name="how-to-create-a-project"></a>Jak utworzyć projekt

Zawartości, takie jak danych, modele, testy i punktów końcowych są podzielone na **projektów** w portalu usługi Custom Voice. Każdy projekt jest specyficzne dla kraju/języka i płeć głosu, który chcesz utworzyć. Na przykład mogą utworzyć projekt w przypadku głosu żeńskiego dla botów rozmowy z Centrum telefonicznej, korzystających z języka angielskiego na terenie Stanów Zjednoczonych (en US).

Aby utworzyć swój pierwszy projekt, wybierz **Text-to-Speech/Custom Voice** , a następnie kliknij **nowy projekt**. Postępuj zgodnie z instrukcjami kreatora do tworzenia projektu. Po utworzeniu projektu są wyświetlane cztery karty: **Dane**, **szkolenia**, **testowania**, i **wdrożenia**. Użyć linków dostępnych w [następne kroki](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice?branch=release-build-cogserv-speech-services#next-steps) dowiesz się, jak używać każdej karty.

## <a name="next-steps"></a>Kolejne kroki

- [Przygotowywanie danych Custom Voice](how-to-custom-voice.md)
- [Tworzenie niestandardowych Voice](how-to-custom-voice-create-voice.md)
- [Przewodnik: Zarejestruj swoje próbki głosu](record-custom-voice-samples.md)
