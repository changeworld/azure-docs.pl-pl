---
title: Listy fraz — usługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak dostarczać usługi mowy z listą fraz `PhraseListGrammar` przy użyciu obiektu, aby poprawić wyniki rozpoznawania zamiany mowy na tekst.
services: cognitive-services
author: rhurey
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: rhurey
ms.openlocfilehash: 0e552d502184d1b537263c2c1f6b2a8562cdf791
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562772"
---
# <a name="phrase-lists-for-speech-to-text"></a>Listy fraz dla zamiany mowy na tekst

Dostarczając usługi mowy z listą fraz, można poprawić dokładność rozpoznawania mowy. Listy fraz są używane do identyfikowania znanych fraz w danych audio, takich jak nazwa osoby lub określonej lokalizacji.

Jeśli na przykład masz polecenie "Przenieś do" i możliwe miejsce docelowe "do wewnątrz", które może być wymawiane, możesz dodać wpis "przejdź do samego". Dodanie frazy spowoduje zwiększenie prawdopodobieństwa, że gdy dźwięk zostanie rozpoznany, że "Przenieś do wewnątrz" zostanie rozpoznany zamiast "Przenieś w stronę".

Pojedyncze słowa lub kompletne wyrażenia można dodać do listy fraz. Podczas rozpoznawania, gdy w dźwięku znajduje się dokładne dopasowanie, jest używana pozycja na liście fraz. Kompilowanie w poprzednim przykładzie, jeśli lista fraz zawiera ciąg "Przenieś do niego" i przechwycona fraza ma wartość "Przenieś w kierunku spowolnienia", a następnie wynik rozpoznawania zostanie "przesunięty do wolniej".

>[!Note]
> Obecnie listy fraz obsługują tylko język angielski dla zamiany mowy na tekst.

## <a name="how-to-use-phrase-lists"></a>Jak używać list fraz

Poniższe przykłady ilustrują sposób tworzenia listy fraz przy użyciu `PhraseListGrammar` obiektu.

```C++
auto phraselist = PhraseListGrammar::FromRecognizer(recognizer);
phraselist->AddPhrase("Move to Ward");
phraselist->AddPhrase("Move to Bill");
phraselist->AddPhrase("Move to Ted");
```

```cs
PhraseListGrammar phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Move to Ward");
phraseList.AddPhrase("Move to Bill");
phraseList.AddPhrase("Move to Ted");
```

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Move to Ward")
phrase_list_grammar.addPhrase("Move to Bill")
phrase_list_grammar.addPhrase("Move to Ted")
```

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

```Java
PhraseListGrammar phraseListGrammar = PhraseListGrammar.fromRecognizer(recognizer);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

>[!Note]
> Maksymalna liczba list fraz, które będą używane przez usługę mowy do dopasowania mowy, to 1024 fraz.

Możesz również wyczyścić frazy skojarzone z `PhraseListGrammar` przez wywołanie metody Clear ().

```C++
phraselist->Clear();
```

```cs
phraseList.Clear();
```

```Python
phrase_list_grammar.clear()
```

```JavaScript
phraseListGrammar.clear();
```

```Java
phraseListGrammar.clear();
```

> [!NOTE]
> Zmiany w `PhraseListGrammar` obiekcie mają wpływ na następne rozpoznanie lub po ponownym nawiązaniu połączenia z usługą mowy.

## <a name="next-steps"></a>Następne kroki

* [Dokumentacja referencyjna zestawu Speech SDK](speech-sdk.md)
