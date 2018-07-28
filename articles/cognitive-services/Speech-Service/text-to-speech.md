---
title: Zamiana tekstu na mowę — informacje
description: Omówienie funkcji zamiany tekstu na mowę.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: d111a9f852b849df15dbd056a7210fac82cee190
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39324259"
---
# <a name="about-the-text-to-speech-api"></a>Zamiana tekstu na mowę interfejsu API — informacje

**Zamiany tekstu na mowę** (TTS) interfejsu API Speech service konwertuje tekst wejściowy do naturalnym brzmiącą mowę (nazywane również *synteza mowy*).

Na potrzeby generowania mowy, aplikacja wysyła żądania HTTP POST do usługi rozpoznawania mowy. Istnieje, tekst jest przekształcony na mowę brzmiącą człowieka i zwracane w postaci pliku audio. Różnych głosów i języki są obsługiwane.

Scenariusze, w których mowy przyjmowana jest syntezy obejmują:

* *Poprawianie ułatwień dostępu:* **zamiany tekstu na mowę** technologia umożliwia właścicielom zawartości i wydawcy, aby odpowiedzieć na osoby różne sposoby interakcji z zawartością. Osób z wadami wzroku lub trudności z odczytu docenia możliwość korzystania z zawartości głos. Głos również danych wyjściowych ułatwia osób w celu korzystania z zawartości tekstowej, takich jak gazety lub blogi, na urządzeniach przenośnych podczas podróży lub wykonywania.

* *Reagowanie w scenariuszach wielozadaniowość:* **zamiany tekstu na mowę** umożliwia użytkownikom ochrony przed rozproszonymi ważne informacje szybko i wygodnie podczas prowadzenia lub w przeciwnym razie poza wygodny sposób odczytu środowiska. Nawigacja jest typowych aplikacji, w tym obszarze. 

* *Udoskonalanie uczenie przy użyciu wielu trybów:* różnych osób się najlepiej na różne sposoby. Ekspertów szkolenia online wykazały, jednocześnie zapewniając połączeń głosowych i tekst mogą ułatwić informacji do nauczenia i zachować.

* *Dostarczanie intuicyjne Boty czy asystentów:* komunikować się może być integralną częścią czatbot inteligentnych lub wirtualnych Asystenta ustawień. Coraz więcej firm tworzenia botów rozmowy w celu udostępnienia klientowi angażujące środowiska usług dla swoich klientów. Głos dodaje innego wymiaru, umożliwiając botów odpowiedzi móc odbierać głos (na przykład telefon).

## <a name="voice-support"></a>Obsługa głosu

Microsoft **zamiany tekstu na mowę** usługa oferuje ponad 75 głosów w ponad 45 języków i ustawień regionalnych. Aby zastosować te standard "czcionki głosowe", należy tylko określić nazwę głosu kilka innych parametrów, po wywołaniu interfejsu API REST usługi. Aby uzyskać szczegółowe informacje głosy są obsługiwane, zobacz [obsługiwane języki](https://docs.microsoft.com/azure/cognitive-services/speech-service/supported-languages#text-to-speech). 

Chcąc głosu unikatowy dla aplikacji, można utworzyć [czcionki głosowe niestandardowe](how-to-customize-voice-font.md) z próbek mowy.

## <a name="next-steps"></a>Kolejne kroki

* [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
* [Zobacz, jak syntetyzowania mowy za pośrednictwem interfejsu API REST](how-to-text-to-speech.md)
