---
title: Wbudowane domeny w aplikacjach LUIS na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać wbudowane domen w aplikacji usługi inteligentnego opis języka (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 72ca0def8528adae5e46a02fa5bfccd14a3b6ab4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349801"
---
# <a name="use-prebuilt-domains-in-luis-apps"></a>Wbudowane domeny w aplikacjach LUIS  

Zawiera opis języka (LUIS) *wbudowane domen*, które są wbudowane zestawy [intencje](luis-how-to-add-intents.md) i [jednostek](luis-concept-entity-types.md) razem dla domen lub kategorii wspólnej pracy aplikacje klienckie. Wbudowane domeny ma wstępnie uczony i są gotowe do dodania do aplikacji LUIS. Po dodaniu ich do aplikacji — możesz to zrobić później je z zniesławiających z systemu, są odpowiednie dla Twoich użytkowników intencje i jednostek w domenie wbudowane są można swobodnie dostosowywać. Użyj całej domeny wbudowane jako punktu wyjścia do dostosowania lub przekazania tylko kilka opcji lub jednostek z wbudowane domeny. 

Przeglądaj **wbudowane domen** kartę, aby wyświetlić inne wbudowane domeny można używać w aplikacji. Kliknij Kafelek dla domeny dodać go do aplikacji lub na **więcej** w jego kafelkiem, aby dowiedzieć się o jego lokalizacji docelowych i jednostek.

> [!TIP]
> Pełną listę wbudowane domeny można znaleźć [odwołania wbudowane domen](./luis-reference-prebuilt-domains.md).

![Dodawanie domeny wbudowane](./media/luis-how-to-prebuilt-domain-entities/add-prebuilt-domain.png)


## <a name="add-a-prebuilt-domain"></a>Dodawanie domeny wbudowane
W **wbudowane domen** , znaleźć domeny RestaurantReservation i kliknij **Dodaj domenę**. Po dodaniu aplikacji LUIS wbudowane domeny otwórz **intencje** i wybierz polecenie Opcje RestaurantReservation.Reserve. Widać, że wiele zniesławiających przykład zostały już podany i oznaczenie jednostek.

![Próba RestaurantReservation.Reserve](./media/luis-how-to-prebuilt-domain-entities/prebuilt-domain-restaurant-reservation.png)


## <a name="designing-luis-apps-from-prebuilt-domains"></a>Projektowanie aplikacji LUIS z domen wbudowane
Korzystając z domeny wbudowane w aplikacji LUIS, można dostosować całej domeny wbudowane lub po prostu zacznij od kilku intencje i jednostek.

## <a name="customizing-an-entire-prebuilt-domain"></a>Dostosowywanie w całej domenie wbudowane
Wbudowane domen są zaprojektowane jako ogólne. Zawierają wiele lokalizacji docelowych i jednostek, które są dostępne do dostosowania do potrzeb aplikacji. Jeśli w przypadku uruchomienia Dostosowywanie całej domeny wbudowane, usunąć intencje i jednostkami, którymi nie trzeba używać aplikacji. Do zestawu, który już zawiera wbudowane domeny można również dodać niektóre opcje lub jednostek. Na przykład, jeśli używasz **zdarzenia** wbudowane domeny aplikacji sportowych zdarzeń, można dodać jednostek dla aplikacji sportowych zespołów. Po rozpoczęciu [dostarczanie zniesławiających](luis-how-to-add-example-utterances.md) do LUIS, zawierają postanowienia, które są specyficzne dla aplikacji. LUIS uzyskuje informacje o ich rozpoznawanie i dostosowanie intencje wbudowane domeny i jednostki do potrzeb aplikacji. 

> [!TIP]
> Intencje i jednostek w domenie wbudowane najlepiej ze sobą. Warto połączyć intencje i jednostki w tej samej domenie, jeśli to możliwe.
> * Najlepszym rozwiązaniem jest Użyj intencje powiązane z tej samej domeny. Na przykład, jeśli są dostosowywanie `MakeReservation` konwersji w **miejsca** domeny, następnie wybierz `Cancel` konwersji z **miejsca** domeny zamiast celem użycia jej Anuluj **Zdarzenia** lub **narzędzia** domen.

## <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Zmiana zachowania założeń wbudowane domeny
Może się okazać, że celem podobny do opcje, których chcesz mieć w Twojej aplikacji LUIS zawiera wbudowane domeny, ale ma zachowywać się inaczej. Na przykład **miejsca** zawiera wbudowane domeny `MakeReservation` zamiar dokonywania rezerwacji restauracji, ale mają aplikację, aby użyć tego celem do wprowadzania rezerwację hoteli. W takim przypadku można zmodyfikować zachowanie tego celem zapewnienie zniesławiających LUIS o Rezerwacja miejsc w hotelach i etykietowanie je przy użyciu `MakeReservation` konwersji, tak więc LUIS może być retrained rozpoznawanie `MakeReservation` konwersji w żądaniu zarezerwować hoteli .

> [!TIP]
> Zapoznaj się w domenie narzędzia wbudowane lokalizacji docelowych, które można dostosować do użycia w każdej domenie. Na przykład można dodać `Utilities.Repeat` do aplikacji i train program rozpoznaje, niezależnie od działań użytkownika może chcesz powtarzać w aplikacji.


## <a name="next-step"></a>Następny krok

Wbudowane domeny należy dostosować, dodając do niego więcej zniesławiających przykład.

> [!div class="nextstepaction"]
> [Dodaj zniesławiających przykład](./luis-how-to-add-example-utterances.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

Zobacz [odwołania wbudowane domen](./luis-reference-prebuilt-domains.md) szczegółowe wbudowane domen.
