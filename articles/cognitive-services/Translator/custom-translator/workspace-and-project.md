---
title: Co to jest obszar roboczy i projekt? - Tłumacz niestandardowy
titleSuffix: Azure Cognitive Services
description: W tym artykule zostaną wyjaśnione różnice między obszarem roboczym a projektem, a także kategorie projektów i etykiety dla usługi Translator niestandardowy.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: d2f7903fa85c645357e46a753d1cb043e0893254
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219435"
---
# <a name="what-is-a-custom-translator-workspace"></a>Co to jest obszar roboczy tłumacza niestandardowego?

Obszar roboczy to obszar roboczy do tworzenia i tworzenia niestandardowego systemu tłumaczenia. Obszar roboczy może zawierać wiele projektów, modeli i dokumentów. Cała praca wyz./ Translator jest wewnętrzna w określonym obszarze roboczym.

Obszar roboczy jest prywatny dla Ciebie i osób, które zapraszasz do swojego obszaru roboczego. Osoby nieproszone nie mają dostępu do zawartości obszaru roboczego. Możesz zaprosić dowolną liczbę osób do swojego obszaru roboczego i zmodyfikować lub usunąć ich dostęp w dowolnym momencie. Można również utworzyć nowy obszar roboczy. Domyślnie obszar roboczy nie będzie zawierał żadnych projektów ani dokumentów znajdujących się w innych obszarach roboczych.

## <a name="what-is-a-custom-translator-project"></a>Co to jest projekt translatora niestandardowego?

Projekt jest otoką dla modelu, dokumentów i testów. Każdy projekt automatycznie zawiera wszystkie dokumenty, które są przekazywane do tego obszaru roboczego, które mają poprawną parę języków. Na przykład jeśli masz zarówno projekt z języka angielskiego do hiszpańskiego, jak i projekt hiszpański-angielski, te same dokumenty zostaną uwzględnione w obu projektach. Każdy projekt ma CategoryID skojarzone z nim, który jest używany podczas wykonywania zapytań [interfejsu API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) dla tłumaczeń. CategoryID jest parametrem używanym do uzyskania tłumaczeń z dostosowanego systemu zbudowanego za pomocą niestandardowego translatora.

## <a name="project-categories"></a>Kategorie projektów

Kategoria identyfikuje domenę — obszar terminologii i stylu, którego chcesz użyć — dla projektu. Wybierz kategorię najbardziej odpowiednią dla twoich dokumentów. W niektórych przypadkach wybór kategorii bezpośrednio wpływa na zachowanie translatora niestandardowego.

Mamy dwa zestawy modeli bazowych. Są ogólne i technologiczne. Jeśli wybrano kategorię **Technologia,** zostaną użyte modele bazowe technologii. W przypadku innego wyboru kategorii używane są ogólne modele linii bazowej. Model bazowy technologii dobrze radzi sobie w dziedzinie technologii, ale pokazuje niższą jakość, jeśli zdania używane do tłumaczenia nie należą do domeny technologii. Sugerujemy klientom, aby wybrali kategorię Technologia tylko wtedy, gdy zdania należą ściśle do domeny technologii.

W tym samym obszarze roboczym można tworzyć projekty dla tej samej pary języków w różnych kategoriach. Usługa Custom Translator zapobiega tworzeniu zduplikowanego projektu z tą samą parą języków i kategorią. Zastosowanie etykiety do projektu pozwala uniknąć tego ograniczenia. Nie używaj etykiet, chyba że budujesz systemy tłumaczeń dla wielu klientów, ponieważ dodanie unikatowej etykiety do projektu zostanie odzwierciedlone w projekcie CategoryID.

## <a name="project-labels"></a>Etykiety projektu

Usługa Custom Translator umożliwia przypisanie etykiety projektu do projektu. Etykieta projektu rozróżnia wiele projektów z tą samą parą językową i kategorią. Najlepszym rozwiązaniem jest unikanie używania etykiet projektu, chyba że jest to konieczne.

Etykieta projektu jest używana jako część identyfikatora kategorii. Jeśli etykieta projektu pozostaje nieustawiona lub jest ustawiona identycznie między projektami, projekty z tą samą kategorią i *różnymi* parami językowymi będą współużytkować ten sam identyfikator kategorii. Takie podejście jest korzystne, ponieważ umożliwia użytkownikowi lub klientowi przełączanie się między językami podczas korzystania z interfejsu API translatora tekstu bez martwienia się o identyfikator kategorii, który jest unikatowy dla każdego projektu.

Na przykład, gdybym chciał włączyć tłumaczenia w domenie technologii z angielskiego na francuski i francuski na\> angielski, stworzyłbym\> dwa projekty: jeden dla języka angielskiego - francuskiego i jeden dla francuskiego - angielskiego. Chciałbym określić tę samą kategorię (Technologia) dla obu i pozostawić etykietę projektu puste. Identyfikator kategorii dla obu projektów będzie zgodny, więc mogę zapytać api dla tłumaczeń w języku angielskim i francuskim bez konieczności modyfikowania identyfikatora kategorii.

Jeśli jesteś dostawcą usług językowych i chcesz obsługiwać wielu klientów z różnymi modelami, które zachowują tę samą kategorię i parę językową, to użycie etykiety projektu do rozróżnienia między klientami byłoby mądrą decyzją.

## <a name="next-steps"></a>Następne kroki

- Przeczytaj o [szkolenia i modelu](training-and-model.md) wiedzieć, jak skutecznie zbudować model tłumaczenia.
