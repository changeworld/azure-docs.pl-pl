---
title: Temat zamiany tekstu na mowę — usługi mowy
titleSuffix: Azure Cognitive Services
description: Interfejs API zamiany tekstu na mowę oferuje ponad 75 głosów w ponad 45 języków i ustawień regionalnych. Aby użyć czcionki głosowe standardowe, wystarczy do określenia nazwy głosowych za pomocą kilku innych parametrów, podczas wywoływania usługi mowy.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 0836ae4a9041db27cfed35dd0f1fc0df6e541aff
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55859338"
---
# <a name="about-the-text-to-speech-api"></a>O interfejsie API zamiany tekstu na mowę

**Zamiany tekstu na mowę** (TTS) interfejsu API konwertuje tekst wejściowy do naturalnym brzmiącą mowę (nazywane również *synteza mowy*).

Na potrzeby generowania mowy, Twoja aplikacja wysyła żądania HTTP POST do interfejsu API zamiany tekstu na mowę. Istnieje, tekst jest przekształcony na mowę brzmiącą człowieka i zwracane w postaci pliku audio. Różnych głosów i języki są obsługiwane.

Scenariusze, w których mowy przyjmowana jest syntezy obejmują:

* *Poprawianie ułatwień dostępu:* **zamiany tekstu na mowę** technologia umożliwia właścicielom zawartości i wydawcy, aby odpowiedzieć na osoby różne sposoby interakcji z zawartością. Osób z wadami wzroku lub trudności z odczytu docenia możliwość korzystania z zawartości głos. Głos również danych wyjściowych ułatwia osób w celu korzystania z zawartości tekstowej, takich jak gazety lub blogi, na urządzeniach przenośnych podczas podróży lub wykonywania.

* *Reagowanie w scenariuszach wielozadaniowość:* **zamiany tekstu na mowę** umożliwia użytkownikom ochrony przed rozproszonymi ważne informacje szybko i wygodnie podczas prowadzenia lub w przeciwnym razie poza wygodny sposób odczytu środowiska. Nawigacja jest typowych aplikacji, w tym obszarze.

* *Udoskonalanie uczenie przy użyciu wielu trybów:* Różne osoby Dowiedz się najlepiej w różny sposób. Ekspertów szkolenia online wykazały, jednocześnie zapewniając połączeń głosowych i tekst mogą ułatwić informacji do nauczenia i zachować.

* *Dostarczanie intuicyjne Boty czy asystentów:* Komunikować się może być integralną częścią czatbot inteligentnych lub wirtualnych Asystenta ustawień. Coraz więcej firm tworzenia botów rozmowy w celu udostępnienia klientowi angażujące środowiska usług dla swoich klientów. Głos dodaje innego wymiaru, umożliwiając botów odpowiedzi móc odbierać głos (na przykład telefon).

## <a name="voice-support"></a>Obsługa głosu

Microsoft **zamiany tekstu na mowę** usługa oferuje ponad 75 głosów w ponad 45 języków i ustawień regionalnych. Aby zastosować te standard "czcionki głosowe", należy tylko określić nazwę głosu kilka innych parametrów, po wywołaniu interfejsu API REST usługi. Aby uzyskać więcej informacji na temat obsługiwanych języków, ustawień regionalnych i głosów, zobacz [obsługiwane języki](language-support.md#text-to-speech).

> [!IMPORTANT]
> Koszty się różnić w przypadku standardowych, niestandardowych i neuronowych głosów. Aby uzyskać więcej informacji, zobacz [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="neural-voices"></a>Głosy neuronowych

Neuronowych zamiany tekstu na mowę można wprowadzić bardziej naturalne interakcje z czatbotów i asystentów wirtualnego i angażujące konwertować teksty cyfrowych, takich jak książki elektroniczne audiobooks i poprawić funkcjonalność z systemów w samochodzie nawigacji. Przypominającej ludzką prosody naturalnych i wyczyść ustaleniu słów neuronowych TTS ma dużo mniejsze zmęczenie nasłuchiwania podczas interakcji z systemami sztucznej Inteligencji. Aby uzyskać więcej informacji na temat neuronowych głosów zobacz [obsługiwane języki](language-support.md#text-to-speech).

### <a name="custom-voices"></a>Głosów niestandardowych

Dostosowywania głosu zamiany tekstu na mowę umożliwia tworzenie mówiącą, jeden z rodzajem głosu dla Twojej marki: *czcionka głosowa.* Aby utworzyć czcionki głosowe, tworzenie nagrania studio i przekaż skojarzone skrypty jako dane szkoleniowe. Usługa tworzy następnie model unikatowy głosu dostosowana do Twojego nagrania. Można użyć jego czcionka głosowa syntetyzować mowy. Aby uzyskać więcej informacji, zobacz [czcionki głosowe niestandardowe](how-to-customize-voice-font.md).

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

* [Uzyskaj subskrypcję usługi mowy bezpłatna](https://azure.microsoft.com/try/cognitive-services/)
* [Szybki start: Konwertuj tekst na mowę, języka Python](quickstart-python-text-to-speech.md)
* [Szybki start: Konwertuj tekst na mowę, .NET Core](quickstart-dotnet-text-to-speech.md)
* [Dokumentacja interfejsu API REST](rest-apis.md)
