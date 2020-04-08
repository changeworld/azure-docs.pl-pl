---
title: Integracja z innymi aplikacjami - QnA Maker
description: QnA Maker integruje się z aplikacjami klienckimi, takimi jak czat boty, a także z innymi usługami przetwarzania języka naturalnego, takimi jak Language Understanding (LUIS).
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: c1edbfb6badfb73ce08a99709da0f8bfb61b7dc3
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804192"
---
# <a name="design-knowledge-base-for-client-applications"></a>Projektowanie bazy wiedzy dla aplikacji klienckich

QnA Maker integruje się z aplikacjami klienckimi, takimi jak czat boty, a także z innymi usługami przetwarzania języka naturalnego, takimi jak Language Understanding (LUIS).

## <a name="integration-with-a-conversational-client"></a>Integracja z klientem konwersacyjnym

QnA Maker integruje się z konwersacyjnymi aplikacjami klienckimi, takimi jak [Microsoft Bot Framework](https://dev.botframework.com/). Tekst wysłany do programu QnA Maker nie musi być czyszczony ani przekształcany. QnA Maker akceptuje języki naturalne i zwraca najlepszą odpowiedź.

## <a name="create-a-bot-without-writing-any-code"></a>Tworzenie bota bez pisania kodu

Po opublikowaniu bazy wiedzy utwórz bota na stronie **Publikowanie,** wybierając przycisk **Utwórz bota.** Skorzystaj z [samouczka bota,](../Quickstarts/create-publish-knowledge-base.md) aby dowiedzieć się, co się stanie po wybraniu przycisku.

## <a name="providing-multi-turn-conversations"></a>Prowadzenie rozmów w wielu turach

Klient bota zapewnia najlepszą wybraną odpowiedź z bazy wiedzy i może dostarczyć monitów uzupełniających, jeśli odpowiedź jest częścią wieloezwracać pary QnA. Dowiedz [się, jak](../how-to/multiturn-conversation.md) dodać zestawy pytań i odpowiedzi do konwersacji z wieloma kolejami do bazy wiedzy.

## <a name="natural-language-processing"></a>Przetwarzanie języka naturalnego

Podczas gdy QnA Maker przetwarza pytania, które używają przetwarzania języka naturalnego, może być również używany jako część większego systemu, który odpowiada na pytania z wielu baz wiedzy. Można połączyć QnA Maker z inną usługą Cognitive Service, Language Understanding (LUIS), aby zapewnić przetwarzanie języka naturalnego przed dotarciem do określonej bazy wiedzy. Dowiedz się więcej o tym, kiedy i jak korzystać ze współpracy [z usługią LUIS i QnA Maker.](../../luis/choose-natural-language-processing-service.md?toc=/azure/cognitive-services/qnamaker/toc.json)

## <a name="next-steps"></a>Następne kroki

Poznaj [koncepcje](development-lifecycle-knowledge-base.md) cyklu rozwoju dla programu QnA Maker.