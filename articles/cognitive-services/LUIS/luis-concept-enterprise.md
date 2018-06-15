---
title: Pojęcia przedsiębiorstwa aplikacji LUIS - Azure | Dokumentacja firmy Microsoft
description: Zrozumieć zagadnienia dotyczące projektowania dla dużych aplikacji LUIS.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/05/2018
ms.author: v-geberr
ms.openlocfilehash: dde7012dee0eb5ea3ac2e1257cb8d2fca5843d4b
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35356491"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>Strategie Enterprise LUIS aplikacji
Przejrzyj te strategii projektowania aplikacji przedsiębiorstwa.

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>Jeśli oczekujesz żądań LUIS po przekroczeniu przydziału
Jeśli szybkość LUIS aplikacji żądania przekracza dozwoloną [szybkość przydziału](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/), rozkładu obciążenia na więcej LUIS aplikacje za pomocą [tej samej definicji aplikacji](#use-multiple-apps-with-same-app-definition) lub Utwórz i [przypisać wiele kluczy](#assign-multiple-luis-keys-to-same-app) do aplikacja. 

### <a name="use-multiple-apps-with-same-app-definition"></a>Korzystanie z wielu aplikacji o tej samej definicji aplikacji
Eksportuj oryginalnej aplikacji LUIS, a następnie importowania aplikacji do osobnych aplikacji. Każda aplikacja ma własny identyfikator aplikacji. Podczas publikowania, zamiast używać tego samego klucza we wszystkich aplikacjach, Utwórz oddzielne klucz dla każdej aplikacji. Równoważenie obciążenia na wszystkie aplikacje tak, aby nie jednej aplikacji jest przeciążony. Dodaj [usługi Application Insights](luis-tutorial-bot-csharp-appinsights.md) do monitorowania użycia. 

Aby uzyskać tego samego zamiar top od wszystkich aplikacji, upewnij się, że jest dostatecznie szerokie, że LUIS nie jest mylić, podając różne wyniki aplikacji dla niewielkich zmian w zniesławiających Prognozowanie konwersji między celem pierwszego i drugiego. 

Wyznaczanie tylko jednej aplikacji jako wzorzec. Wszelkie zniesławiających, które są zalecane do przeglądu powinien dodana do aplikacji głównej następnie przeniesiona z powrotem do innych aplikacji. To jest pełna eksportu aplikacji, lub ładowania etykietą zniesławiających z wzorca do podrzędnych. Ładowanie może odbywać się przy użyciu dowolnego [LUIS] [ LUIS] witryny sieci Web lub tworzenia interfejsu API dla [pojedynczy utterance](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) lub [partii](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09). 

Zaplanuj okresowo [przeglądu zniesławiających punktu końcowego](label-suggested-utterances.md) active szkoleniowe, co dwa tygodnie, np. następnie ponownie ucz i ponownie opublikować. 

### <a name="assign-multiple-luis-keys-to-same-app"></a>Przypisywanie klawiszy wielu LUIS do tej samej aplikacji
Jeśli aplikacja LUIS otrzyma jeden punkt końcowy trafień niż pozwala przydziału jednego klucza, Utwórz i przypisz więcej klucze do aplikacji LUIS. Tworzenie Menedżera ruchu lub zarządzanie zapytań punktu końcowego za pośrednictwem kluczy subskrypcji usługi równoważenia obciążenia. 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>Gdy wbudowanymi aplikacji zwraca niewłaściwy zamiar
Jeśli aplikacja jest przeznaczona do prognozowania szerokiej gamy zniesławiających użytkownika, rozważ zaimplementowanie [modelu wysyłania](#dispatch-tool-and-model). Podzielenie wbudowanymi aplikacji umożliwia LUIS wykrywania fokus między intencje pomyślnie zamiast pobierania pomylić między intencje w aplikacji nadrzędnej i podrzędnej aplikacji. 

Zaplanuj okresowo [przeglądu zniesławiających punktu końcowego](label-suggested-utterances.md) active szkoleniowe, co dwa tygodnie, np. następnie ponownie ucz i ponownie opublikować. 

## <a name="when-you-need-to-have-more-than-500-intents"></a>Jeśli musisz mieć więcej niż 500 intencje
Na przykład załóżmy, że opracowywanym Asystentem pakietu office, który ma ponad 500 lokalizacji docelowych. Jeśli 200 intencje odnoszą się do planowania spotkań, 200 nastąpi przypomnienia, 200 są uzyskiwanie informacji na temat współpracowników i są 200 do wysyłania wiadomości e-mail, intencje grupy tak, aby każda grupa jest w jednej aplikacji, następnie utwórz aplikację najwyższego poziomu zawierającą każdego celem. Użyj [wysyłania narzędzia i architektura](#dispatch-tool-and-model) kompilowania aplikacji najwyższego poziomu. Następnie zmienić Twoje bot do użycia jako Pokaż w kaskadowych wywołania [samouczek wysyłania][dispatcher-application-tutorial]. 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>Aby połączyć aplikacje maker kilka LUIS i — strona główna
Jeśli masz kilka LUIS i — strona główna aplikacji maker, które należy odpowiedzieć na robotów, użyj [narzędzia wysyłki](#dispatch-tool-and-model) kompilowania aplikacji najwyższego poziomu. Następnie zmienić Twoje bot do użycia jako Pokaż w kaskadowych wywołania [samouczek wysyłania][dispatcher-application-tutorial]. 

## <a name="dispatch-tool-and-model"></a>Narzędzia wysyłki i modelu
Użyj [wysyłania] [ dispatch-tool] narzędzie wiersza polecenia, w [BotBuilder narzędzia](https://github.com/Microsoft/botbuilder-tools) połączyć wiele LUIS i/lub Maker — strona główna aplikacji do nadrzędnego LUIS aplikacji. Takie podejście umożliwia ma domeny nadrzędnej, w tym wszystkie tematy i różnych domen podmiotu w osobnych aplikacji. 

![Obrazu koncepcyjnego architektury wysyłania](./media/luis-concept-enterprise/dispatch-architecture.png)

Domena nadrzędna jest odnotowany w LUIS jako **wysyłania V** aplikacji. 

![Zrzut ekranu LUIS lista aplikacji z aplikacją LUIS utworzony przez narzędzie wysyłania](./media/luis-concept-enterprise/dispatch.png)

Chatbot odbiera utterance, a następnie wysyła do nadrzędnego LUIS aplikacji na potrzeby prognozowania. Górny zamiar przewidywane z aplikacji nadrzędnej określa podrzędnych, których aplikacji LUIS nazywa się obok. Chatbot wysyła utterance aplikacji podrzędnych do bardziej szczegółowych przewidywania.

Zrozumieć, w jaki sposób ma zostać tej hierarchii wywołań z v4 konstruktora Bot [samouczek w przypadku aplikacji dyspozytora][dispatcher-application-tutorial].  

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [test partii](luis-how-to-batch-test.md)

[LUIS]:luis-reference-regions.md
[dispatcher-application-tutorial]:https://aka.ms/bot-dispatch
[dispatch-tool]:https://github.com/Microsoft/botbuilder-tools/tree/master/Dispatch