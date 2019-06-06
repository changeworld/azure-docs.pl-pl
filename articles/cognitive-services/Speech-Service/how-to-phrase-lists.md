---
title: Wyświetla frazy — usługi mowy
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak Podaj usług przetwarzania mowy z listy frazy za pomocą `PhraseListGrammar` obiekt, aby poprawić wyniki rozpoznawania mowy na tekst.
services: cognitive-services
author: rhurey
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 5/02/2019
ms.author: rhurey
ms.openlocfilehash: a3be5d28ebe394771a2d8b492f1f6a9c8a82fb9e
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515304"
---
# <a name="phrase-lists-for-speech-to-text"></a>Fraza list mowy na tekst

Dostarczając usług przetwarzania mowy z listą frazy, może zwiększyć dokładność rozpoznawania mowy. Fraza list są używane do identyfikowania znanych frazy w danych audio, takie jak nazwisko osoby lub konkretnej lokalizacji.

Na przykład w przypadku polecenia "Przenieś" i możliwe miejsca docelowego "Dalej", który może być wymawiane, można dodać wpis "Przesuń do tyłu". Dodawanie frazy zwiększy prawdopodobieństwo, że, kiedy audio jest rozpoznawana rozpoznawania "Przesuń do tyłu" zamiast "Przenoszenia kierunku".

Pojedynczy słów i fraz pełną można dodać do listy fraz. Podczas rozpoznawania wpis na liście frazy jest używany, jeśli dokładne dopasowanie jest uwzględniona w audio. Opieranie się na poprzedni przykład, jeśli lista fraz zawiera "Przesuń do tyłu", a także frazy przechwycone jest "Przenieś powoli kierunku", a następnie wynik rozpoznawania będzie "Przesuń do tyłu powoli".

## <a name="how-to-use-phrase-lists"></a>Jak używać Wyświetla frazy

Poniższe przykłady pokazują, jak tworzyć zawartość przy użyciu listy fraz `PhraseListGrammar` obiektu.

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
> Maksymalna liczba listy frazy, które będzie używane przez usługę rozpoznawania mowy, aby dopasować mowy to 1024 fraz.

Można także wyczyścić fraz skojarzony `PhraseListGrammar` przez wywołującego metody clear().

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
> Zmienia się na `PhraseListGrammar` obiektu take wpływ na rozpoznawanie dalej lub ponowne nawiązanie połączenia z usługami mowy.

## <a name="next-steps"></a>Kolejne kroki

* [Dokumentacja referencyjna zestawu SDK rozpoznawania mowy](speech-sdk.md)
