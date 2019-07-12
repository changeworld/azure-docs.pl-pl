---
title: Co to jest interfejs API rozpoznawania osoby mówiącej?
titleSuffix: Azure Cognitive Services
description: Użyj zaawansowanych algorytmów służących do weryfikacji osoby mówiącej i identyfikacji osoby mówiącej przy użyciu interfejsu API rozpoznawania osoby mówiącej w usłudze Cognitive Services.
services: cognitive-services
author: dwlin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speaker-recognition
ms.topic: overview
ms.date: 10/01/2018
ms.author: nitinme
ms.openlocfilehash: 15fc320a5b76a50def634d937a02fa639dce3739
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2019
ms.locfileid: "67845866"
---
# <a name="speaker-recognition-api"></a>Interfejs API rozpoznawania osoby mówiącej

Interfejsy API rozpoznawania osoby mówiącej usługi Azure Cognitive Services — Zapraszamy! Interfejsy API rozpoznawania osoby mówiącej to chmurowe interfejsy API oferujące najbardziej zaawansowane algorytmy do weryfikacji oraz identyfikacji osoby mówiącej. Rozpoznawanie osoby mówiącej można podzielić na dwie kategorie: weryfikację osoby mówiącej oraz identyfikację osoby mówiącej.


## <a name="speaker-verification"></a>Weryfikacja osoby mówiącej

Głos ma unikatowe cechy, które mogą służyć do identyfikowania osoby, podobnie jak ma to miejsce w przypadku odcisku palca.  Korzystanie z głosu do kontroli dostępu i scenariuszy uwierzytelniania okazało się nowym innowacyjnym narzędziem —zasadniczo oferuje ono wyższy stopień bezpieczeństwa, co ułatwia klientom uwierzytelnianie.

Interfejsy API weryfikacji osoby mówiącej mogą automatycznie weryfikować i uwierzytelniać użytkowników przy użyciu ich głosu lub mowy.

### <a name="enrollment"></a>Rejestracja

Rejestracja do weryfikacji osoby mówiącej jest zależna od tekstu, co oznacza, że osoby mówiące muszą wybrać określone hasło, które będzie używane w fazach rejestracji i weryfikacji.

Podczas rejestracji jest nagrywany głos osoby mówiącej w trakcie wypowiadania określonej frazy, następnie zostaje wyodrębniona pewna liczba cech i wybrana fraza zostaje rozpoznana. Wyodrębnione cechy i wybrana fraza wspólnie tworzą unikatowy podpis głosowy.

### <a name="verification"></a>Weryfikacja

Podczas weryfikacji głos i fraza wejściowa są porównywane z podpisem głosowym i frazą z rejestracji — aby zweryfikować, czy pochodzą od tej samej osoby i czy zawierają prawidłową frazę.

Aby uzyskać więcej informacji na temat weryfikacji osoby mówiącej, zapoznaj się z artykułem dotyczącym interfejsu API  [weryfikacji osoby mówiącej](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652).

## <a name="speaker-identification"></a>Identyfikacja osoby mówiącej

Interfejsy API identyfikacji osoby mówiącej mogą automatycznie identyfikować osobę mówiącą w pliku audio po podaniu grupy potencjalnych osób mówiących. Dane wejściowe w postaci głosu są porównywane z podaną grupą osób mówiących. Po znalezieniu dopasowania jest zwracana tożsamość osoby mówiącej.

Wszystkie osoby mówiące powinny najpierw przejść proces rejestracji, aby ich głos został zarejestrowany w systemie i została utworzona ich próbka głosu.


### <a name="enrollment"></a>Rejestracja

Rejestracja w celu identyfikacji osoby mówiącej jest niezależna od tekstu, co oznacza, że nie ma żadnych ograniczeń w zakresie słów wypowiadanych przez osobę mówiącą na nagraniu dźwiękowym. Po nagraniu głosu osoby mówiącej następuje wyodrębnienie pewnej liczby cech i utworzenie unikatowego podpisu głosowego.


### <a name="recognition"></a>Rozpoznawanie

Podczas rozpoznawania należy dostarczyć nagranie dźwiękowe nieznanej osoby mówiącej oraz potencjalną grupę osób mówiących. Głos wejściowy jest porównywany ze wszystkimi osobami mówiącymi w celu ustalenia, do kogo należy głos, a po znalezieniu dopasowania zostaje zwrócona tożsamość osoby mówiącej.

Aby uzyskać więcej informacji na temat identyfikacji osoby mówiącej, zapoznaj się z artykułem dotyczącym interfejsu API  [identyfikacji osoby mówiącej](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e).
