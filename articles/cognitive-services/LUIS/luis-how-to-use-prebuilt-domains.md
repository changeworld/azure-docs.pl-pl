---
title: Umożliwia tworzenie aplikacji dzięki szybszemu w usługi LUIS aplikacji ze wstępnie utworzonych domen
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) oferuje ze wstępnie utworzonych domen, które są wstępnie utworzone zestawy intencje i podmioty, które współpracują ze sobą w przypadku domen lub wspólnych kategorii aplikacji klienckich. Ze wstępnie utworzonych domen wstępnie przeszkolonych i są gotowe do dodania z aplikacją usługi LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/06/2018
ms.author: diberry
ms.openlocfilehash: 1aa7e1bf9c1a584803a60a5061b4ae4cc8664ff4
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47037375"
---
# <a name="use-prebuilt-domains-in-luis-apps"></a>Użyj wstępnie utworzonych domen w aplikacjach usługi LUIS  

Language Understanding (LUIS) oferuje *ze wstępnie utworzonych domen*, które są wstępnie utworzone zestawy [intencji](luis-how-to-add-intents.md) i [jednostek](luis-concept-entity-types.md) współpracujące ze sobą dla domen lub typowe kategorie aplikacje klienckie. Ze wstępnie utworzonych domen wstępnie przeszkolonych i są gotowe do dodania z aplikacją usługi LUIS. Intencje i podmioty, w domenie wbudowanych są w pełni konfigurowalne po ich dodaniu do aplikacji — możesz uczyć je za pomocą wypowiedzi w systemie, dzięki czemu są odpowiednie dla Twoich użytkowników. Użyj całej domeny ze wstępnie utworzonych jako punktu wyjścia do dostosowywania lub po prostu "pożyczać" kilka intencji lub jednostkami z wbudowanych domeny. 

Przeglądaj **ze wstępnie utworzonych domen** kartę, aby zobaczyć inne ze wstępnie utworzonych domen można użyć w aplikacji. Kliknij Kafelek dla domeny dodać go do swojej aplikacji, lub kliknij pozycję **więcej** w jej Kafelek, aby dowiedzieć się o jego intencje i podmioty.

> [!TIP]
> Znajduje się pełna lista ze wstępnie utworzonych domen w [odwołania ze wstępnie utworzonych domen](./luis-reference-prebuilt-domains.md).

![Dodawanie wstępnie utworzonej domeny](./media/luis-how-to-prebuilt-domain-entities/add-prebuilt-domain.png)


## <a name="add-a-prebuilt-domain"></a>Dodawanie wstępnie utworzonej domeny
W **ze wstępnie utworzonych domen** karcie Znajdź domeny RestaurantReservation i kliknij przycisk **Dodaj domenę**. Po wbudowanych domena została dodana do aplikacji usługi LUIS, otwórz **intencji** i kliknij pozycję Opcje RestaurantReservation.Reserve. Widać, że wiele wypowiedzi przykład już zostały podane i oznaczone przy użyciu jednostek.

![Celem RestaurantReservation.Reserve](./media/luis-how-to-prebuilt-domain-entities/prebuilt-domain-restaurant-reservation.png)


## <a name="designing-luis-apps-from-prebuilt-domains"></a>Projektowanie aplikacji usługi LUIS z wstępnie utworzonych domen
Podczas korzystania ze wstępnie utworzonych domen w aplikacją usługi LUIS, można dostosować całej domeny wbudowanych lub po prostu zacznij z kilku jego intencje i podmioty.

## <a name="customizing-an-entire-prebuilt-domain"></a>Dostosowywanie wstępnie całej domeny
Ze wstępnie utworzonych domen są projektowane jako ogólne. Zawierają one wiele intencje i podmioty, które można wybierać spośród dostosowywania aplikacji do swoich potrzeb. W przypadku uruchomienia Dostosowywanie wstępnie całej domeny, Usuń intencje i podmioty, nie trzeba używać twojej aplikacji. Do zestawu, który zapewnia wbudowane domeny, można dodać niektórych opcjami lub jednostki. Na przykład, jeśli używasz **zdarzenia** wbudowanych domeny dla aplikacji sportowych zdarzeń, można dodać jednostki dla zespołów, dyscyplin sportowych. Po uruchomieniu [dostarczanie wypowiedzi](luis-how-to-add-example-utterances.md) do usługi LUIS, zawierają postanowienia, które są specyficzne dla aplikacji. Usługa LUIS uczy się rozpoznawać je i dostosowanie intencje i podmioty do potrzeb swojej aplikacji, w domenie wbudowanych. 

> [!TIP]
> Intencje i podmioty, w domenie wbudowanych najlepiej działają razem. Zaleca się połączyć intencje i podmioty w tej samej domenie, jeśli jest to możliwe.
> * Najlepszym rozwiązaniem jest używać intencji pokrewne w tej samej domenie. Na przykład, jeśli dostosowujesz `MakeReservation` intencji w **miejsc** domeny, następnie wybierz pozycję `Cancel` intencji z **miejsc** domeny zamiast intencji anulowania w **Zdarzenia** lub **narzędzia** domen.

## <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Zmiana zachowania intencji ze wstępnie utworzonych domen
Może się okazać, że wbudowanych domena zawiera cel, który jest podobny do intencji, które chcesz mieć w swojej aplikacji usługi LUIS, ale chcesz, aby zachowywać się inaczej. Na przykład **miejsc** udostępnia wstępnie utworzonych domen `MakeReservation` intencji składania rezerwacji restauracji, ale ma aplikację do używania z tym przeznaczeniem aby rezerwację hoteli. W takiej sytuacji możesz zmodyfikować zachowanie tym przeznaczeniem dostarczając wypowiedzi użytkownikowi usługi LUIS więcej o tworzeniu rezerwacji hotelowych i etykietowania je przy użyciu `MakeReservation` intencji, tak więc LUIS może być retrained rozpoznawanie `MakeReservation` intencji w żądaniu, aby zarezerwować hotelu .

> [!TIP]
> Zapoznaj się z domeny programów narzędziowych dla wbudowanych intencji, które można dostosować do użytku w każdej domenie. Na przykład można dodać `Utilities.Repeat` do swojej aplikacji i szkolenie go rozpoznać, niezależnie od działań użytkownika może być ma zostać powtórzony w aplikacji.


## <a name="next-step"></a>Następny krok

Dostosuj wstępnie utworzonych domen, dodając więcej wypowiedzi przykład do niego.

> [!div class="nextstepaction"]
> [Dodawanie wypowiedzi przykład](./luis-how-to-add-example-utterances.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

Zobacz [odwołania ze wstępnie utworzonych domen](./luis-reference-prebuilt-domains.md) więcej szczegółów dotyczących ze wstępnie utworzonych domen.
