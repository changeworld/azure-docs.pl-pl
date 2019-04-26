---
title: Dostosuj model języka, w Video Indexer — platformy Azure
titlesuffix: Azure Media Services
description: Ten artykuł zawiera omówienie co to jest model języka w Video Indexer i sposobami ich dostosowywania.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: c4ccc189c0f8ed3de868c9965d7068ad9670cbcb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60535414"
---
# <a name="customize-a-language-model-with-video-indexer"></a>Dostosuj model języka, za pomocą indeksatora wideo

Usługa Video Indexer obsługuje automatyczne rozpoznawanie mowy dzięki integracji z usługą Microsoft [Custom Speech Service](https://azure.microsoft.com/services/cognitive-services/custom-speech-service/). Możesz dostosować model języka przez przekazanie tekstu dostosowania, a mianowicie tekst z domeny słownictwo, którego chcesz aparatu, aby dostosować je do. Po użytkownik nauczenia modelu, nowych słów, które pojawiają się w tekście adaptacji zostanie rozpoznany, zakładając, że Wymowa domyślne i modelu językowego dowiesz się, nowe prawdopodobne sekwencji wyrazów. Niestandardowe modele językowe są obsługiwane w przypadku angielski, hiszpański, francuski, niemiecki, włoski, chiński (uproszczony), japoński, rosyjski, portugalski (Brazylia), Hindi i koreańskim. 

Weźmy programu word, który jest bardzo specyficzny, takich jak "Kubernetes" (w kontekście usługi Azure Kubernetes service), na przykład. Ponieważ słowo pojawił się do usługi Video Indexer, uznaje się za "społeczności". Należy do nauczenia modelu, rozpoznawał ją jako "Kubernetes". W innych przypadkach istnieje wyrazy, ale model języka nie jest oczekiwane pojawiać się w pewnym kontekście. Na przykład "container service" nie jest to sekwencja programu word 2 model języka niewyspecjalizowaną rozpoznawany jako określony zestaw wyrazów.

Masz możliwość przekazywania wyrazów bez kontekstu na liście w pliku tekstowym. To jest traktowany jako częściowe dostosowania. Alternatywnie możesz przekazać pliki tekst dokumentacji lub zdania związane z zawartość na potrzeby lepszego dostosowania.

Służy do interfejsów API indeksatora wideo lub witryny sieci Web można tworzyć i edytować niestandardowych modeli językowych, zgodnie z opisem w tematach w [następne kroki](#next-steps) części tego tematu.

## <a name="best-practices-for-custom-language-models"></a>Najlepsze rozwiązania dotyczące niestandardowych modeli językowych

Usługa Video Indexer uczy się zależnie od prawdopodobieństwa kombinacje słowa, więc, aby dowiedzieć się, najlepiej:

* Przekazuje wystarczającej liczby rzeczywistych przykładów zdań, które jest wymawiany.
* Nie więcej umieścić tylko jeden zdanie w każdym wierszu. W przeciwnym razie system zawiera informacje prawdopodobieństwa między zdaniami.
* Można umieścić o jeden wyraz jako zdanie do poprawienia word względem innych użytkowników, ale system uczy się najlepiej z pełną zdań.
* Wprowadzając nowe słowa lub akronimów, jeśli jest to możliwe, podać dowolną liczbę przykłady zastosowania w zdaniu pełną oferowanie tyle kontekstu w możliwie systemu.
* Spróbuj umieścić kilka opcji dostosowania i zobacz, jak działają dla Ciebie.
* Należy unikać powtarzania z dokładnie jednym zdaniu wiele razy. To może spowodować utworzenie odchylenie dla pozostałych danych wejściowych.
* Unikaj, łącznie z symbolami nietypowe (~, # @ % &) jako zostanie wstrzymane. Zdania, w których są wyświetlane również zostanie wstrzymane.
* Należy unikać umieszczania duży dane wejściowe, takie jak setki tysięcy zdania, ponieważ będzie więc rozcieńczyć efekt zwiększania wyniku.

## <a name="next-steps"></a>Kolejne kroki

[Dostosuj model języka, za pomocą interfejsów API](customize-language-model-with-api.md)

[Dostosuj model języka, witryny sieci Web](customize-language-model-with-website.md)
