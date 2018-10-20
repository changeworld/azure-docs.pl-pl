---
title: Zamiana tekstu na mowę — usługi mowy — informacje
titleSuffix: Azure Cognitive Services
description: Interfejs API zamiany tekstu na mowę oferuje ponad 75 głosów w ponad 45 języków i ustawień regionalnych. Aby użyć czcionki głosowe standardowe, wystarczy do określenia nazwy głosowych za pomocą kilku innych parametrów, podczas wywoływania usługi mowy.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: erhopf
ms.openlocfilehash: 7f01fe5c71cdd6f4c70527fcf2553374aae9a5d8
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2018
ms.locfileid: "49469932"
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

Microsoft **zamiany tekstu na mowę** usługa oferuje ponad 75 głosów w ponad 45 języków i ustawień regionalnych. Aby zastosować te standard "czcionki głosowe", należy tylko określić nazwę głosu kilka innych parametrów, po wywołaniu interfejsu API REST usługi. Aby uzyskać szczegółowe informacje głosy są obsługiwane, zobacz [obsługiwane języki](language-support.md#text-to-speech).

Chcąc głosu unikatowy dla aplikacji, można utworzyć [czcionki głosowe niestandardowe](how-to-customize-voice-font.md) z próbek mowy.

## <a name="api-capabilities"></a>Funkcje interfejsu API

Wiele możliwości **zamiany tekstu na mowę** interfejsu API, zwłaszcza w części dotyczącej dostosowywania, są dostępne za pośrednictwem interfejsu REST. Poniższa tabela zawiera podsumowanie możliwości każdej metody uzyskiwania dostępu do interfejsu API. Aby uzyskać pełną listę możliwości i szczegóły interfejsu API, zobacz [Swagger odwołania](https://westus.cris.ai/swagger/ui/index).

| Przypadek użycia | REST | Zestawy SDK |
|-----|-----|-----|----|
| Przekaż zestawów danych dostosowywania głosu | Yes | Nie |
| Tworzenie i zarządzanie modelami czcionki głosowe | Yes | Nie |
| Tworzenie i zarządzanie wdrożeniami czcionki głosowe | Yes | Nie |
| Utwórz & zarządzania testami czcionki głosowe| Yes | Nie |
| Zarządzanie subskrypcjami | Yes | Nie |

> [!NOTE]
> Interfejs API implementuje, że limity żądań interfejsu API 25 na 5 sekund ograniczania przepływności. Nagłówki komunikatów będzie informować o limicie.

## <a name="next-steps"></a>Kolejne kroki

* [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
* [Zobacz, jak syntetyzowania mowy za pośrednictwem interfejsu API REST](how-to-text-to-speech.md)
