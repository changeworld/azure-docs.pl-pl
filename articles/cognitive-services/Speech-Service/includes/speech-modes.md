---
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2020
ms.author: dapine
ms.openlocfilehash: bb177963b824e6d5d80d5f60e8b9f995b6ecd121
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80537011"
---
## <a name="speech-modes"></a>Tryby mowy

**Interaktywne**
- Przeznaczone do scenariuszy dowodzenia i kontroli.
- Ma wartość limit czasu segmentacji X.
- Na końcu jednego rozpoznanego wypowiedź usługa zatrzymuje przetwarzanie dźwięku z tego identyfikatora żądania i kończy obrót. Połączenie nie jest zamknięte.
- Maksymalny limit uznania wynosi 20s.
- Typowe wywołanie Carbon `RecognizeOnceAsync`wywołać jest .

**Konwersacja**
- Przeznaczone do dłuższych rozpoznawania działa.
- Ma wartość przecenienia czasu (Y != X)
- Będzie przetwarzać wiele kompletnych wypowiedzi bez zakończenia turn.
- Zakończy skręt za dużo ciszy.
- Carbon będzie nadal z nowym identyfikatorem żądania i odtwarzanie dźwięku w razie potrzeby.
- Usługa zostanie siłą rozłączona po 10 minutach rozpoznawania mowy.
- Carbon ponownie połączy i odtworzy niepotwierdzony dźwięk.
- Wywoływane w `StartContinuousRecognition`Carbon z .

**Dyktowania**
- Umożliwia użytkownikom określenie znaków interpunkcyjnych, mówiąc ją.
- Wywoływane w Carbon `EnableDictation` przez `SpeechConfig` określenie na obiekcie, niezależnie od wywołania interfejsu API, który rozpoczyna rozpoznawanie.
- Klaster<sup>1-owy</sup> zwraca `speech.fragment` wiadomości dla wyników pośrednich, wiadomości zwrotne `speech.hypothesis` 3<sup>strony.</sup>