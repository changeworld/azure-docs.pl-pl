---
title: Co to jest głos niestandardowy? — Usługa mowy
titleSuffix: Azure Cognitive Services
description: Niestandardowy głos to zestaw narzędzi online, które umożliwiają tworzenie rozpoznawalnego, jednoczęściowego głosu dla marki. Wszystkie potrzebne do rozpoczęcia pracy to kilku plików audio i skojarzonych transkrypcji. Skorzystaj z poniższych linków, aby rozpocząć tworzenie niestandardowego środowiska zamiany mowy na tekst.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 636c397001a3130c60fc2bf498339ad058aca374
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959399"
---
# <a name="get-started-with-custom-voice"></a>Wprowadzenie do niestandardowego głosu

[Niestandardowy głos](https://aka.ms/customvoice) to zestaw narzędzi online, które umożliwiają tworzenie rozpoznawalnego, jednoczęściowego głosu dla marki. Wszystkie potrzebne do rozpoczęcia pracy to kilku plików audio i skojarzonych transkrypcji. Skorzystaj z poniższych linków, aby rozpocząć tworzenie niestandardowego środowiska zamiany tekstu na mowę.

## <a name="whats-in-custom-voice"></a>Co znajduje się w niestandardowym głosowaniu?

Przed rozpoczęciem pracy z niestandardowym głosem musisz mieć konto platformy Azure i subskrypcję usługi Speech Services. Po utworzeniu konta możesz przygotować swoje dane, wyszkolić i przetestować modele, oszacować jakość głosu i ostatecznie wdrożyć niestandardowy model głosowy.

Na poniższym diagramie przedstawiono kroki umożliwiające utworzenie niestandardowego modelu głosu przy użyciu [niestandardowego portalu głosu](https://aka.ms/customvoice). Skorzystaj z linków, aby dowiedzieć się więcej.

![Niestandardowy diagram architektury głosu](media/custom-voice/custom-voice-diagram.png)

1.  [Subskrybuj i Utwórz projekt](#set-up-your-azure-account) — Utwórz konto platformy Azure i Utwórz subskrypcję usługi Speech Services. Ta ujednolicona subskrypcja zapewnia dostęp do funkcji zamiany mowy na tekst, zamiany tekstu na mowę, tłumaczenia mowy i niestandardowego portalu głosu. Następnie za pomocą subskrypcji usługi Speech Services Utwórz swój pierwszy niestandardowy projekt głosowy.

2.  [Przekazuj](how-to-custom-voice-create-voice.md#upload-your-datasets) dane — przekazywanie danych (audio i tekstowych) przy użyciu niestandardowego portalu głosu lub niestandardowego interfejsu API mowy. Z poziomu portalu można zbadać i oszacować wyniki wymowy oraz współczynnik sygnałów do szumów. Aby uzyskać więcej informacji, zobacz [jak przygotować dane dla niestandardowego głosu](how-to-custom-voice-prepare-data.md).

3.  [Uczenie modelu](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) — używanie danych do tworzenia niestandardowego modelu głosu zamiany tekstu na mowę. Możesz nauczyć model w różnych językach. Po przeprowadzeniu szkolenia Przetestuj model i, jeśli wynik jest zadowalający, możesz wdrożyć model.

4.  [Wdrażanie modelu](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) — Tworzenie niestandardowego punktu końcowego dla modelu głosu zamiany tekstu na mowę i używanie go na potrzeby syntezy mowy w produktach, narzędziach i aplikacjach.

## <a name="set-up-your-azure-account"></a>Skonfiguruj swoje konto platformy Azure

Przed rozpoczęciem korzystania z portalu Custom Speech w celu utworzenia modelu niestandardowego należy skorzystać z subskrypcji usługi Speech Services. Postępuj zgodnie z tymi instrukcjami, aby utworzyć subskrypcję usługi Speech Services na platformie Azure. Jeśli nie masz konta platformy Azure, możesz utworzyć nowe konto.  

Po utworzeniu konta platformy Azure i subskrypcji usługi Speech Services należy zalogować się do portalu niestandardowego i połączyć się z subskrypcją.

1. Pobierz klucz subskrypcji usługi Speech Services z Azure Portal.
2. Zaloguj się do [niestandardowego portalu głosowego](https://aka.ms/custom-voice).
3. Wybierz swoją subskrypcję i Utwórz projekt mowy.
4. Jeśli chcesz przełączyć się na inną subskrypcję mowy, użyj ikony koło zębate znajdującej się w górnym obszarze nawigacji.

> [!NOTE]
> Niestandardowa usługa głosowa nie obsługuje 30-dniowego bezpłatnego klucza wersji próbnej. Aby można było korzystać z usługi, na platformie Azure musi być utworzony klucz F0 lub S0.

## <a name="how-to-create-a-project"></a>Jak utworzyć projekt

Zawartość, taka jak dane, modele, testy i punkty końcowe, są zorganizowane w **projekty** w portalu niestandardowym. Każdy projekt jest specyficzny dla kraju/języka i płci głosu, który chcesz utworzyć. Na przykład możesz utworzyć projekt dla żeńskiego głosu dla rozmowy telefonicznej botów, która używa języka angielskiego w Stany Zjednoczone (EN-US).

Aby utworzyć swój pierwszy projekt, wybierz kartę **Zamiana tekstu na mowę/niestandardową** , a następnie kliknij pozycję **Nowy projekt**. Postępuj zgodnie z instrukcjami wyświetlanymi przez kreatora, aby utworzyć projekt. Po utworzeniu projektu zobaczysz cztery karty: **dane**, **szkolenia**, **testowanie**i **wdrażanie**. Skorzystaj z linków w [sekcji Następne kroki](#next-steps) , aby dowiedzieć się, jak korzystać z każdej karty.

## <a name="next-steps"></a>Następne kroki

- [Przygotowywanie niestandardowych danych głosowych](how-to-custom-voice-prepare-data.md)
- [Tworzenie niestandardowego głosu](how-to-custom-voice-create-voice.md)
- [Przewodnik: zapisywanie przykładów głosu](record-custom-voice-samples.md)
