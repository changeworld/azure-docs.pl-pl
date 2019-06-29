---
title: Często zadawane pytania dotyczące bezpośredniego mowy wiersza
titleSuffix: Azure Cognitive Services
description: Uzyskaj odpowiedzi na najpopularniejsze pytania dotyczące głosu pierwszy asystentów wirtualnego przy użyciu kanału Direct mowy wiersza.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: travisw
ms.openlocfilehash: 2c669f00ae65667f85976aca218ce51d630159ee
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67465499"
---
# <a name="voice-first-virtual-assistants-preview-frequently-asked-questions"></a>Wirtualnych asystentów głosowych pierwszy (wersja zapoznawcza): Często zadawane pytania

Jeśli nie możesz znaleźć odpowiedzi na pytania w tym dokumencie, zapoznaj się z [inne opcje pomocy technicznej](support.md).

## <a name="general"></a>Ogólne

**Pyt.: Co to jest bezpośrednie mowy wiersza?**

**Odp.:** `DialogServiceConnector` z zestawu SDK rozpoznawania mowy umożliwia dwukierunkowy, komunikacji asynchronicznej do Boty, które są podłączone do kanału Direct mowy wiersza na platformy Bot Framework. Ten kanał zapewnia skoordynowanego dostępu do zamiany mowy na tekst i zamiany tekstu na mowę z usług przetwarzania mowy platformy Azure, dzięki czemu Boty, które staną się całkowicie głosu w, połączeń głosowych się konwersacji środowiska. Dzięki obsłudze wyrazów wznawiania i wznawiania pracy weryfikacji programu Word to rozwiązanie umożliwia tworzenie dużym stopniu dostosowywane asystentów wirtualnych pierwszej głosu dla Twojej marki lub produktu.

**Pyt.: Jak rozpocząć pracę?**

**Odp.:** Najlepszy sposób rozpoczynać się tworzenie Asystenta ustawień wirtualnego pierwszy głos jest rozpoczęcie od [tworzenia podstawowy bot platformy Bot Framework](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0). Następnie połącz botowi [kanału Direct mowy wiersza](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

## <a name="debugging"></a>Debugowanie

**Pyt.: Otrzymują błąd 401 — dostęp, podczas nawiązywania połączenia i nic nie działa. Wiem, że mój klucz subskrypcji mowy jest prawidłowy. Co się dzieje?**

**Odp.:** W wersji zapoznawczej bezpośrednie mowy wiersz ma pewne ograniczenia na subskrypcje, które można użyć. Upewnij się, w przypadku używania **mowy** zasobów (Microsoft.CognitiveServicesSpeechServices, "Mowa") i *nie* **usług Cognitive Services** (zasobów Microsoft.CognitiveServicesAllInOne, "Wszystkie usługi Cognitive Services"). Tylko [regionach usług przetwarzania mowy przez](regions.md#voice-first-virtual-assistants) są obecnie obsługiwane przez bezpośrednie mowy wiersza.

![Popraw subskrypcji dla linii bezpośredniej mowy](media/voice-first-virtual-assistants/faq-supported-subscription.png "przykład zgodne subskrypcji mowy")

**Pyt.: Mogę wrócić rozpoznawanie tekstu z bezpośredniego wypowiedzi wiersza, ale widzę błąd "1011" i nic z moich botów. Dlaczego?**

**Odp.:** Ten błąd wskazuje problem z komunikacją między bot i bezpośredniego mowy wiersza. Upewnij się, że masz [połączone kanału Direct mowy wiersza](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech), [dodano obsługę protokołu przesyłania strumieniowego](https://aka.ms/botframework/addstreamingprotocolsupport) do botów (z powiązanych obsługi gniazda sieci Web), a następnie sprawdź, czy Twój bot odpowiada na żądania przychodzące żądania z kanału.

**Pyt.: Ten kod nie działa i/lub otrzymuję błąd różnych, korzystając z DialogServiceConnector. Co zrobić?**

**Odp.:** Rejestrowanie opartych na plikach zapewnia znacznie więcej szczegółów i mogą pomóc przyspieszyć żądania pomocy technicznej. Aby włączyć tę funkcję, zobacz [jak używać rejestrowania w pliku](how-to-use-logging.md).

## <a name="next-steps"></a>Kolejne kroki

* [Rozwiązywanie problemów](troubleshooting.md)
* [Informacje o wersji](releasenotes.md)
