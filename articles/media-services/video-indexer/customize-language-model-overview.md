---
title: Dostosowywanie modelu języka w indeksatorze wideo — Azure
titleSuffix: Azure Media Services
description: W tym artykule przedstawiono omówienie modelu języka w indeksatorze wideo i jak go dostosować.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: b096b9352be65033f2fb782b118e815dc16b43b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838318"
---
# <a name="customize-a-language-model-with-video-indexer"></a>Dostosowywanie modelu języka za pomocą indeksatora wideo

Indeksator wideo obsługuje automatyczne rozpoznawanie mowy dzięki integracji z [usługą Microsoft Custom Speech Service](https://azure.microsoft.com/services/cognitive-services/custom-speech-service/). Model języka można dostosować, przesyłając tekst adaptacyjny, a mianowicie tekst z domeny, do którego słownictwa chcesz dostosować się do aparatu. Po przeszkoleniu modelu nowe słowa pojawiające się w tekście adaptacyjnym zostaną rozpoznane, przy założeniu domyślnej wymowy, a model języka nauczy się nowych prawdopodobnych sekwencji słów. Modele języka niestandardowego są obsługiwane w językach angielskim, hiszpańskim, francuskim, niemieckim, włoskim, chińskim (uproszczonym), japońskim, rosyjskim, brazylijskim portugalskim, hindi i koreańskim. 

Weźmy słowo, które jest bardzo specyficzne, takie jak "Kubernetes" (w kontekście usługi Azure Kubernetes), jako przykład. Ponieważ słowo jest nowe w Video Indexer, jest rozpoznawany jako "społeczności". Musisz wyszkolić model, aby rozpoznał go jako "Kubernetes". W innych przypadkach istnieją słowa, ale model języka nie oczekuje, że pojawią się one w określonym kontekście. Na przykład "usługa kontenera" nie jest sekwencją 2-wyrazową, którą model języka niewyspecjalizowanych rozpoznałby jako określony zestaw słów.

Można przekazywać wyrazy bez kontekstu na liście w pliku tekstowym. Uznaje się to za częściowe dostosowanie. Alternatywnie można przesłać pliki tekstowe dokumentacji lub zdań związanych z treścią w celu lepszej adaptacji.

Za pomocą interfejsów API indeksatora wideo lub witryny sieci Web można tworzyć i edytować niestandardowe modele języka, zgodnie z opisem w tematach w sekcji [Następne kroki](#next-steps) tego tematu.

## <a name="best-practices-for-custom-language-models"></a>Najważniejsze wskazówki dotyczące niestandardowych modeli języka

Video Indexer uczy się na podstawie prawdopodobieństwa kombinacji słów, więc aby dowiedzieć się najlepiej:

* Podaj wystarczająco dużo prawdziwych przykładów zdań, jak będą one wypowiadane.
* Umieść tylko jedno zdanie w wierszu, a nie więcej. W przeciwnym razie system nauczy się prawdopodobieństwa między zdaniami.
* To jest w porządku, aby umieścić jedno słowo jako zdanie, aby zwiększyć słowo przeciwko innym, ale system uczy się najlepiej z pełnych zdań.
* Wprowadzając nowe słowa lub akronimy, jeśli to możliwe, podaj jak najwięcej przykładów użycia w pełnym zdaniu, aby dać jak najwięcej kontekstu systemowi.
* Spróbuj umieścić kilka opcji adaptacji i zobaczyć, jak działają dla Ciebie.
* Unikaj wielokrotnego powtarzania tego samego zdania. Może to spowodować stronniczość wobec reszty danych wejściowych.
* Unikaj umieszczania nietypowych symboli (~, # @ % &), ponieważ zostaną one odrzucone. Zdania, w których się pojawiają, również zostaną odrzucone.
* Unikaj wprowadzania zbyt dużych nakładów, takich jak setki tysięcy zdań, ponieważ spowoduje to osłabienie efektu zwiększenia.

## <a name="next-steps"></a>Następne kroki

[Dostosowywanie modelu języka przy użyciu interfejsów API](customize-language-model-with-api.md)

[Dostosowywanie modelu języka za pomocą witryny sieci Web](customize-language-model-with-website.md)
