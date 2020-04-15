---
title: Planowanie aplikacji — usługa LUIS
description: Określ odpowiednie intencje aplikacji i encje, a następnie utwórz plany aplikacji w usłudze Language Understanding Intelligent Services (LUIS).
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: dfed27a05973a2ea2e9a97eaa1c233b847b33d87
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382303"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>Planowanie schematu aplikacji usługi LUIS za pomocą domeny podmiotu i wyodrębniania danych

Schemat aplikacji usługi LUIS zawiera [intencje](luis-glossary.md#intent) i [encje](luis-glossary.md#entity) istotne dla [domeny](luis-glossary.md#domain)podmiotu . Intencje klasyfikują [wypowiedzi](luis-glossary.md#utterance)użytkownika, a jednostki wyodrębniają dane z wypowiedzi użytkownika.

## <a name="identify-your-domain"></a>Identyfikowanie domeny

Aplikacja usługi LUIS jest skoncentrowana wokół domeny tematu. Na przykład możesz mieć aplikację turystyczną, która obsługuje rezerwację biletów, lotów, hoteli i wypożyczonych samochodów. Inna aplikacja może dostarczać treści związane z ćwiczeniami, śledzeniem wysiłków fitness i wyznaczaniem celów. Identyfikowanie domeny pomaga znaleźć słowa lub frazy, które są istotne dla Twojej domeny.

> [!TIP]
> Usługa LUIS oferuje [wstępnie utworzone domeny](luis-how-to-use-prebuilt-domains.md) dla wielu typowych scenariuszy. Sprawdź, czy możesz użyć wstępnie utworzonej domeny jako punktu wyjścia dla aplikacji.

## <a name="identify-your-intents"></a>Zidentyfikuj swoje intencje

Pomyśl o [intencjach,](luis-concept-intent.md) które są ważne dla zadania aplikacji.

Weźmy przykład aplikacji turystycznej, z funkcjami rezerwacji lotu i sprawdzenia pogody w miejscu docelowym użytkownika. Można zdefiniować `BookFlight` `GetWeather` i intencje dla tych akcji.

W bardziej złożonej aplikacji z większą licznymi funkcjami masz więcej intencji i należy je dokładnie zdefiniować, aby intencje nie były zbyt specyficzne. Na przykład `BookFlight` `BookHotel` i może być konieczne oddzielne `BookInternationalFlight` `BookDomesticFlight` intencje, ale i może być zbyt podobne.

> [!NOTE]
> Jest najlepszym rozwiązaniem, aby użyć tylko tyle intencji, jak trzeba wykonać funkcje aplikacji. Jeśli zdefiniujesz zbyt wiele intencji, staje się trudniejsze dla usługi LUIS do poprawnego klasyfikowania wypowiedzi. Jeśli zdefiniujesz zbyt mało, mogą one być tak ogólne, że nakładają się na siebie.

Jeśli nie trzeba identyfikować ogólną intencję użytkownika, dodaj wszystkie `None` wypowiedzi przykładowych użytkowników do intencji. Jeśli aplikacja staje się wymagająca więcej intencji, możesz je utworzyć później.

## <a name="create-example-utterances-for-each-intent"></a>Tworzenie przykładowych wypowiedzi dla każdej intencji

Na początek należy unikać tworzenia zbyt wiele wypowiedzi dla każdego zamiaru. Po określeniu intencji, utwórz 15 do 30 wypowiedzi przykład na intencji. Każda wypowiedź powinna się różnić od wcześniej dostarczonych wypowiedzi. Dobra różnorodność w wypowiedziach obejmuje ogólną liczbę słów, wybór wyrazów, czas czasownika i [interpunkcję](luis-reference-application-settings.md#punctuation-normalization).

Aby uzyskać więcej informacji, zobacz [opis dobrych wypowiedzi dla aplikacji usługi LUIS](luis-concept-utterance.md).

## <a name="identify-your-entities"></a>Identyfikowanie jednostek

W wypowiedzi przykład identyfikować jednostki, które mają wyodrębnione. Aby zarezerwować lot, potrzebujesz informacji, takich jak miejsce docelowe, data, linia lotnicza, kategoria biletu i klasa podróży. Utwórz jednostki dla tych typów danych, a następnie oznacz [jednostki](luis-concept-entity-types.md) w wypowiedziach przykładowych. Jednostki są ważne dla realizacji intencji.

Podczas określania jednostek, które mają być używane w aplikacji, należy pamiętać, że istnieją różne typy jednostek do przechwytywania relacji między typami obiektów. [Jednostki w usłudze LUIS](luis-concept-entity-types.md) zawiera więcej szczegółów na temat różnych typów.

> [!TIP]
> Usługa LUIS oferuje wstępnie utworzone jednostki dla [typowych,](luis-prebuilt-entities.md) konwersacyjnych scenariuszy użytkownika. Należy rozważyć użycie wstępnie utworzonych jednostek jako punktu wyjścia dla tworzenia aplikacji.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Uczenie się życia rozwoju usługi LUIS](luis-concept-app-iteration.md)

