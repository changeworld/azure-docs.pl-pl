---
title: Rozpoczynanie pracy z usługą mowy niestandardowe — usługi mowy
titlesuffix: Azure Cognitive Services
description: Custom Speech to zestaw narzędzi online, które pozwalają ocenić i zwiększyć dokładność mowy na tekst firmy Microsoft dla aplikacji, narzędzi i produktów. Wszystko, co może potrwać, aby rozpocząć kilka plików audio testowych. Skorzystaj z linków poniżej, aby rozpocząć tworzenie niestandardowego środowiska mowy na tekst.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: f9b9fc0a2939f601cbddafb6ac400130e794da2b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67060895"
---
# <a name="what-is-custom-speech"></a>Co to jest niestandardowa mowy?

[Custom Speech](https://aka.ms/custom-speech) to zestaw narzędzi online, które pozwalają ocenić i zwiększyć dokładność mowy na tekst firmy Microsoft dla aplikacji, narzędzi i produktów. Wszystko, co może potrwać, aby rozpocząć kilka plików audio testowych. Skorzystaj z linków poniżej, aby rozpocząć tworzenie niestandardowego środowiska mowy na tekst.

## <a name="whats-in-custom-speech"></a>Jaka jest Custom Speech?

Przed wykonaniem z Custom Speech, konieczne będzie konto platformy Azure i Subskrypcja usług przetwarzania mowy. Po skonfigurowaniu konta można przygotowywanie danych, uczenie i testowanie modeli, sprawdzić jakość rozpoznawania, oceny dokładności i ostatecznie wdrożenia i użyć niestandardowego modelu mowy na tekst.

Ten diagram zawiera wyróżnione fragmenty, które tworzą portalu usługi Custom Speech. Aby dowiedzieć się więcej na temat każdego kroku, skorzystaj z poniższych łączy.

![Prezentuje różne składniki, które tworzą portalu usługi Custom Speech.](./media/custom-speech/custom-speech-overview.png)

1. [Subskrypcja i Utwórz projekt](#set-up-your-azure-account) — Tworzenie konta platformy Azure i Subskrypcja usług przetwarzania mowy. Ta subskrypcja ujednoliconego zapewnia dostęp do zamiany mowy na tekst, zamiany tekstu na mowę, tłumaczenie mowy i portalu usługi Custom Speech. Następnie przy użyciu posiadanej subskrypcji usług przetwarzania mowy, Utwórz swój pierwszy projekt Custom Speech.

2. [Przekazywanie danych testowych](how-to-custom-speech-test-data.md) — przekazywanie danych testowych (pliki audio) do oceny firmy Microsoft zamiany mowy na tekst przeznaczone dla aplikacji, narzędzi i produktów.

3. [Sprawdź rozpoznawanie jakości](how-to-custom-speech-inspect-data.md) — za pomocą portalu Custom Speech odtwarzać dźwięk przekazane i sprawdzić jakość rozpoznawania mowy, danych testowych. Do pomiarów ilościowych, zobacz [sprawdzanie danych](how-to-custom-speech-inspect-data.md).

4. [Oceny dokładności](how-to-custom-speech-evaluate-data.md) -oceny dokładności modelu mowy na tekst. Custom Speech portal będzie wyświetlał *współczynnik błędów Word*, który może służyć do określenia, czy wymagane dodatkowe szkolenie. Jeżeli jesteś zadowolony z dokładności, może bezpośrednio korzystać z interfejsów API usługi mowy. Jeśli chcesz poprawić dokładność przez względną średnią z 5-20%, należy użyć **szkolenia** karta w portalu, aby przekazać dane dodatkowe szkolenie, takie jak oznaczone przez człowieka zapisów i dowolny tekst związany z.

5. [Uczenie modelu](how-to-custom-speech-train-model.md) — zwiększyć dokładność modelu mowy na tekst, zapewniając napisane zapisów (10-1000 godzin) i związane z tekstu (< 200 MB) wraz z audio dane testowe. Te dane ułatwiają do nauczenia modelu mowy na tekst. Po szkoleniu ponów test, a jeśli jesteś zadowolony z wyników, można wdrożyć modelu.

6. [Wdrażanie modelu](how-to-custom-speech-deploy-model.md) — Tworzenie niestandardowego punktu końcowego dla modelu mowy na tekst i używać go w swojej aplikacji, narzędzia lub produktów.

## <a name="set-up-your-azure-account"></a>Konfigurowanie konta platformy Azure

Wymagana jest subskrypcja usług przetwarzania mowy, korzystać z portalu usługi Custom Speech, aby utworzyć niestandardowy model. Wykonaj te instrukcje, aby utworzyć w ramach subskrypcji standardowej usług przetwarzania mowy: [Utwórz subskrypcję mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-azure-account).

> [!NOTE]
> Należy koniecznie Utwórz subskrypcje standardowe (S0), subskrypcje bezpłatnej wersji próbnej (F0) nie są obsługiwane.

Po utworzeniu konta platformy Azure i Subskrypcja usług przetwarzania mowy, musisz zalogować się do portalu Custom Speech i Połącz z subskrypcją.

1. Pobierz klucz subskrypcji usług przetwarzania mowy z witryny Azure portal.
2. Zaloguj się do [portal Custom Speech](https://aka.ms/custom-speech).
3. Wybierz subskrypcję, którą trzeba pracować nad, a następnie utwórz projekt mowy.
4. Jeśli chcesz zmodyfikować subskrypcję, użyj **koło zębate** ikonę znajduje się w górnym menu nawigacyjnym.

## <a name="how-to-create-a-project"></a>Jak utworzyć projekt

Zawartości, takie jak danych, modele, testy i punktów końcowych są podzielone na **projektów** w portalu usługi Custom Speech. Każdy projekt jest specyficzne dla domeny i kraju/języka. Może na przykład, Utwórz projekt dla centrów wywołania, korzystających z języka angielskiego na terenie Stanów Zjednoczonych.

Aby utworzyć swój pierwszy projekt, wybierz **mowy do tekst/Custom speech**, następnie kliknij przycisk **nowy projekt**. Postępuj zgodnie z instrukcjami kreatora do tworzenia projektu. Po utworzeniu projektu powinny zostać wyświetlone cztery karty: **Dane**, **testowania**, **szkolenia**, i **wdrożenia**. Użyć linków dostępnych w [następne kroki](#next-steps) dowiesz się, jak używać każdej karty.

## <a name="next-steps"></a>Kolejne kroki

* [Przygotuj i przetestować danych](how-to-custom-speech-test-data.md)
* [Sprawdzanie danych](how-to-custom-speech-inspect-data.md)
* [Oceń swoje dane](how-to-custom-speech-evaluate-data.md)
* [Uczenie modelu](how-to-custom-speech-train-model.md)
* [Wdrażanie modelu](how-to-custom-speech-deploy-model.md)
