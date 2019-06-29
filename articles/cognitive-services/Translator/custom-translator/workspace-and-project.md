---
title: Co to jest obszar roboczy i projektu? — Niestandardowy w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Obszar roboczy jest obszar roboczy, do redagowania i tworzenia systemu tłumaczenia niestandardowych. Obszar roboczy może zawierać wiele projektów, modeli i dokumentów. Projekt jest otoką elementu modelu, dokumentów i testów. Każdy projekt automatycznie zawiera wszystkich dokumentów, które zostały załadowane do obszaru roboczego, które mają pary odpowiedni język.
services: cognitive-services
author: swmachan
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: c35f3911b591a0d9adccdc48dbdc17f8afb5ea00
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447341"
---
# <a name="what-is-a-custom-translator-workspace"></a>Co to jest obszar roboczy niestandardowe w usłudze Translator?

Obszar roboczy jest obszar roboczy, do redagowania i tworzenia systemu tłumaczenia niestandardowych. Obszar roboczy może zawierać wiele projektów, modeli i dokumentów. Cała praca wykonywana w niestandardowych w usłudze Translator znajduje się wewnątrz określonego obszaru roboczego.

Obszar roboczy jest prywatny dla Ciebie i osoby, którym można zaprosić do obszaru roboczego. Uninvited osoby nie mają dostępu do zawartości obszaru roboczego. Możesz zaprosić jak najwięcej osób, ponieważ do obszaru roboczego, takich jak i zmodyfikowaniu lub usunięciu ich dostęp w dowolnym momencie. Można również utworzyć nowy obszar roboczy. Domyślnie obszar roboczy nie będzie zawierać żadnych projektów lub dokumentów, które znajdują się w swoich obszarów roboczych.

## <a name="what-is-a-custom-translator-project"></a>Co to jest projektem niestandardowe w usłudze Translator?

Projekt jest otoką elementu modelu, dokumentów i testów. Każdy projekt automatycznie zawiera wszystkich dokumentów, które zostały załadowane do obszaru roboczego, które mają pary odpowiedni język. Na przykład jeśli masz zarówno angielski, hiszpański projektu i hiszpański do projektu w języku angielskim, ten sam dokumentów będą uwzględniane w obu projektach. Każdy projekt ma CategoryID skojarzonych z nim jest używany podczas wykonywania zapytań dotyczących [interfejsu API w wersji 3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) poszukiwaniu tłumaczeń. CategoryID to parametr służący do uzyskiwanie tłumaczenia dostosowany system utworzonych za pomocą niestandardowych w usłudze Translator.

## <a name="project-categories"></a>Kategorie projektu

Kategoria identyfikuje domeny — obszar terminologii i stylu, którego chcesz użyć — dla Twojego projektu. Wybierz kategorię, która jest najbardziej odpowiednie do dokumentów. W niektórych przypadkach wybraną kategorię bezpośrednio wpływa na zachowanie niestandardowe w usłudze Translator.

Firma Microsoft ma dwa zestawy modeli podstawowych. Są one ogólne i technologii. Jeśli kategoria **technologii** jest zaznaczone, modeli podstawowych technologii będą używane. Do innych wybór kategorii modele ogólne odniesienia są używane. Modelu odniesienia technologii, jak również w domeny z technologii, ale pokazuje niższa jakość, jeśli zdania używane na potrzeby tłumaczenia nie należy do domeny z technologii. Zalecamy, aby klienci mogą wybrać kategorię technologii, tylko wtedy, gdy zdania ściśle mieszczą się w domenie technologii.

W tym samym obszarze roboczym można tworzyć projekty dla tej samej pary języka w różnych kategoriach. Niestandardowe w usłudze Translator uniemożliwia tworzenie duplikatu projektu za pomocą tej samej pary języka i kategorii. Zastosowanie etykiety do projektu umożliwia uniknięcie tego ograniczenia. Nie należy używać etykiety, chyba że w przypadku tworzenia systemów translation dla wielu klientów, jak dodawanie, że unikatową etykietę do projektu, które zostaną odzwierciedlone w swoich projektach CategoryID.

## <a name="project-labels"></a>Etykiety projektu

Niestandardowe w usłudze Translator umożliwia przypisanie etykiety projektu do projektu. Etykieta projektu rozróżnia między wieloma projektami za pomocą tej samej pary języka i kategorii. Najlepszym rozwiązaniem należy unikać projektu etykiety o ile to konieczne.

Etykieta projektu jest używana jako część CategoryID. Jeśli etykieta projektu pozostanie usunięta lub jest ustawiony tak samo w projektach, następnie projekty przy użyciu tej samej kategorii oraz *różnych* kierunki współużytkują ten sam CategoryID. To podejście jest korzystne, ponieważ zezwala ona na zostanie lub klient przełączać się między językami, korzystając z interfejsu API tekstu usługi Translator bez martwienia się o CategoryID, który jest unikatowy dla każdego projektu.

Na przykład, jeśli chciałem umożliwiające tłumaczenia w technologii domenie z angielskiego na francuski, a w języku francuskim do języka angielskiego, czy mogę utworzyć dwa projekty: jeden dla angielskiego -\> francuski i jeden dla francuski —\> języka angielskiego. I będzie określenie tej samej kategorii (technologia) dla obu i puste etykiety projektu. Umożliwi dopasowanie CategoryID dla obu projektów, więc I może wysłać zapytania do interfejsu API dla angielskiego i francuskiego tłumaczenia bez konieczności modyfikowania Moje CategoryID.

Jeśli usługodawcy języka, a chcesz obsługiwać wielu klientów z różnych modeli, które zachowują parę języka i tej samej kategorii, następnie za pomocą etykiety projektu do rozróżnienia między klientami będzie poddanie decyzji.

## <a name="next-steps"></a>Kolejne kroki

- Przeczytaj o [szkolenia i model](training-and-model.md) Aby dowiedzieć się, jak i wydajnego tworzenia modelu tłumaczenia.
