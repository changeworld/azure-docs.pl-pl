---
title: Zdań i tokenów w interfejsie API językową analiza | Dokumentacja firmy Microsoft
description: Więcej informacji na temat rozdzielenie zdanie i tokenizacji językowe analizy interfejsu API w usługach kognitywnych.
services: cognitive-services
author: DavidLiCIG
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 03/21/2016
ms.author: davl
ms.openlocfilehash: 78e539f365728ad540308e9cfb07af44bf6d8fe7
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2018
ms.locfileid: "37084046"
---
# <a name="sentence-separation-and-tokenization"></a>Rozdzielenie zdanie i Tokenizacji

## <a name="background-and-motivation"></a>Tło i motywacją

Podana treść, pierwszym krokiem językową analiza jest podziel go na zdań i tokenów.

### <a name="sentence-separation"></a>Rozdzielenie zdanie

Na pierwszy rzut wydaje się, że krytyczne tekstu do zdań jest łatwy: tylko znaleźć znaczników zakończenia zdania i przerwać zdań.
Znaczniki są jednak często skomplikowane i niejednoznaczne.

Należy wziąć pod uwagę następujące przykładowy tekst:

> Co powiedziałeś?!? Nie można otrzymywać informacje o dyrektora "nowej propozycji." Należy koniecznie Państwa-Pani Smith.

Ten tekst zawiera trzy zdań:

- Co powiedziałeś?!?
- Nie można otrzymywać informacje o dyrektora "nowej propozycji."
- Należy koniecznie Państwa-Pani Smith.

Należy zwrócić uwagę, jak punkty końcowe zdań są oznaczone w bardzo różne sposoby.
Pierwszy kończy się na kombinacji znaków zapytania i wykrzykniki (nazywane również interrobang).
Drugi kończy się kropką lub kropką, ale następujące znaku cudzysłowu należy pobierane do poprzedniego zdanie.
W trzecim zdaniu widać, jak ten sam znak kropki może służyć do oznaczania także skróty.
Wyszukiwanie tylko na znaki interpunkcyjne zawiera zestaw odpowiednimi kandydatami, ale dalszej pracy wymagane w celu zidentyfikowania granice zdanie wartość true.

### <a name="tokenization"></a>Tokenizacji

Następne zadanie jest Podziel te zdania na tokeny.
W większości przypadków tokeny w języku angielskim są rozdzielane znakami odstępu.
(Wyszukiwanie słów lub tokenów jest znacznie łatwiejsze w języku angielskim niż w języku chińskim, gdzie spacje przeważnie nie są używane między wyrazami.
Pierwsze zdanie mogą być zapisane jako "Whatdidyousay?")

Istnieje kilka przypadków trudne.
Po pierwsze znaki interpunkcyjne często (ale nie zawsze) powinien można podzielić od jego otaczającym kontekście.
Po drugie, jest język angielski *skrótów*, takie jak "nie" lub "jest", gdzie skompresowane i skrót na mniejsze części słowa. Celem tokenizator ma podzielić sekwencja znaków słów.

Teraz wróć do zdań przykład z góry.
Teraz możemy umieszczone kropką"center" (&middot;) między każdy token distinct.

- Co &middot; czy &middot; można &middot; powiedzieć &middot; ?!?
- I &middot; czy &middot; e &middot; usłyszeć &middot; o &middot; &middot; Dyrektor &middot; w &middot; " &middot; nowe &middot; propozycji &middot; . &middot; "
- On &middot; w &middot; ważne &middot; do &middot; p.&middot; i &middot; Pani. &middot; Smith &middot; .

Należy zwrócić uwagę, jak większość tokeny są słowa znajdował się w słowniku (np. *ważne*, *Dyrektor*).
Inne może składać się wyłącznie znaków interpunkcyjnych.
Ponadto istnieją bardziej nietypowe tokenów do reprezentowania skrótów, takich jak *e* dla *nie*, zaimki dzierżawcze, takich jak *w*itp. Ta tokenizacji umożliwia obsługę słowa *nie* i zwrot *nie* w sposób bardziej spójny dla wystąpienia.

## <a name="specification"></a>Specyfikacja

Należy podjąć spójne decyzje dotyczące co obejmuje zdania i token.
Firma Microsoft zależą od specyfikacji z [Penn Treebank](https://catalog.ldc.upenn.edu/ldc99t42) (niektóre dodatkowe szczegóły są dostępne pod adresem ftp://ftp.cis.upenn.edu/pub/treebank/public_html/tokenization.html).
