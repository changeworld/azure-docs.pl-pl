---
title: Temat tekst na mowę | Dokumentacja firmy Microsoft
description: Omówienie funkcji tekst na mowę.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 84baf03c83bb63883b80982056cdf6e1e25b3fb7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349705"
---
# <a name="about-the-text-to-speech-api"></a>Temat tekst na mowę interfejsu API

**Tekst na mowę** interfejsu API (TTS) usługi mowy konwertuje tekst wejściowy mowy pomiarowe naturalny (nazywane również *mowy syntezy*).

Aby wygenerować mowy, aplikacja wysyłanie żądania HTTP POST do usługi mowy. Istnieje, tekst jest syntezatora do personelu pomiarowe mowy i zwracane w postaci pliku audio. Różne głosy i języki są obsługiwane.

Scenariusze, w których mowy przyjmuje się syntezy obejmują:

* *Zwiększanie dostępności:* **tekst na mowę** technologia umożliwia Właściciele zawartości i wydawców odpowiedzieć na różne sposoby osób interakcyjnie ich zawartości. Osobom aktualizującego visual lub trudności odczytu docenia możliwość korzystania z zawartości głos. Głos output również ułatwia użytkownikom korzystanie z zawartości tekstowej, takich jak gazetach lub blogi, na urządzeniach przenośnych podczas podróży lub wykonywania.

* *Odpowiada w scenariuszach wielozadaniowości:* **tekst na mowę** umożliwia użytkownikom przyjęcia szybko i wygodnie ważne informacje podczas kierowania lub w przeciwnym razie poza wygodny sposób odczytywania środowiska. Nawigacja jest typowym zastosowaniem w tym obszarze. 

* *Udoskonalanie uczenia z wielu trybów:* różne osoby Dowiedz się najlepiej w różny sposób. Eksperci uczenia online wykazały, że zapewnianie tekstu i komunikacja głosowa ze sobą mogą ułatwić informacji do nauczenia i zachować.

* *Dostarcza intuicyjne robotów lub Asystenci:* komunikować się może być integralną częścią bot inteligentnego rozmów lub Asystenta wirtualnego. Coraz więcej firm opracowujesz robotów rozmów zapewnienie atrakcyjnej klienta środowiska usługi dla klientów. Głos dodaje innego wymiaru, zezwalając bot odpowiedzi do odebrania głos (na przykład przez telefon).

## <a name="voice-support"></a>Obsługa głosu

Microsoft **tekst na mowę** usługa zawiera więcej niż 75 głosy w więcej niż 45 języków i ustawień regionalnych. Aby zastosować te standard "czcionek głos", wystarczy do określenia nazwy głosu kilka innych parametrów po wywołaniu interfejsu API REST usługi. Aby uzyskać szczegółowe informacje głosy są obsługiwane, zobacz [obsługiwanych języków](supported-languages.md). 

Jeśli chcesz głosu unikatowy dla aplikacji, można utworzyć [czcionki niestandardowe głosu](how-to-customize-voice-font.md) z próbek mowy.

## <a name="next-steps"></a>Kolejne kroki

* [Pobierz wersję próbną subskrypcji mowy](https://azure.microsoft.com/try/cognitive-services/)
* [Zobacz rozpoznawanie mowy w języku C#](quickstart-csharp-windows.md)
