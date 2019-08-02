---
title: Czym jest Uczeń konwersacji? -Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Dowiedz się więcej o Conversation Learner i sposobach jego działania.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 725a1d3628fb320a58e073fe2d825af23b02c0bd
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707014"
---
# <a name="what-is-conversation-learner"></a>Czym jest Uczeń konwersacji?

Conversation Learner umożliwia tworzenie i uczenie interfejsów konwersacji, które uczyją się na przykład interakcje. 

W przeciwieństwie do tradycyjnych metod, Conversation Learner traktuje kompleksowy kontekst dialogu, aby poprawić odpowiedzi i zapewnić bardziej atrakcyjne środowisko użytkownika. Obejmując szeroką gamę przypadków użycia zorientowanych na zadania, Conversation Learner stosuje Uczenie maszynowe w tle, dzięki czemu botów i inteligentny agenci mogą mniej korzystnie frustrować użytkowników, ponosić dodatkowe koszty obsługi klienta i Spur bardziej intuicyjne interakcje.

Deweloperzy zaczynają od wprowadzenia okien dialogowych prototypowe, które chcą naśladować. Model uczy się, jak wprowadzono więcej okien dialogowych. Gdy model działa prawidłowo, bot można wdrożyć dla użytkowników końcowych. Conversation Learner rejestruje konwersacje z użytkownikami, a deweloper może je przeglądać. Jeśli występują błędy, deweloper może dokonać korekty na miejscu, a model jest ponownie przemieszczony i dostępny do natychmiastowego wykorzystania.

Takie podejście zmniejsza Ręczne kodowanie logiki kontroli dialogu i umożliwia właścicielom firmy lub ekspertom domeny Współtworzenie się w interfejsie konwersacji bez wcześniejszej wiedzy na temat uczenia maszynowego. Niezależnie od tego, czy jest wdrażany jako część bot, Smart Device czy inteligentnego agenta, Conversation Learner może szybko zaiterować nowe umiejętności, zachowania lub kompetencje oraz szybko poprawić ich jakość. 

Conversation Learner pozwala deweloperom zwiększyć szybkość na rynku i zapewnić pomyślne okna dialogowe w wielu kanałach konwersacji za pośrednictwem programu Microsoft bot Framework lub autonomicznie przy użyciu własnej infrastruktury.

Podsumowanie i najważniejsze informacje:

- Conversation Learner to w pierwszej kolejności tworzenia botów zorientowanych na zadania.

- Polega ona na kompleksowym przewróceniu sieci neuronowych (LSTM) i uczy się bezpośrednio od wieloskładnikowych przykładów konwersacji. 

- Umożliwia projektantom, deweloperom, użytkownikom biznesowym i pracownikom wywołującym zarządzanie botów. 

- Zapewnia możliwość wyrażania reguł firmy i powszechnego sensu w kodzie.

- Podczas sesji nauczania model sieci neuronowych jest używany do oceny następnego zestawu oczekiwanych akcji w konwersacji. Deweloper bot może następnie wybrać poprawną akcję i przeszkolić sieć w celu zapewnienia odpowiedniej odpowiedzi.
 
- Po zakończeniu szkolenia deweloperzy mogą korzystać z okien dialogowych dziennika z interakcji z użytkownikami, aby wprowadzać poprawki do bot odpowiedzi i ponownie szkolić model. 

- Może wywoływać interfejsy API specyficzne dla domeny i innych firm, aby wykonać zadania.

