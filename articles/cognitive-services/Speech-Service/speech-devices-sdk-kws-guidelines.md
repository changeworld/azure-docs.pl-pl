---
title: Wskazówki dotyczące nazewnictwa słów kluczowych — usługa mowy
titleSuffix: Azure Cognitive Services
description: Tworzenie efektywnego słowa kluczowego jest niezbędne do zapewnienia spójnej i dokładnej reakcji urządzenia.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: dapine
ms.openlocfilehash: 3c3943f7269fa2d0ed25acf2215549635b5f16ac
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456389"
---
# <a name="guidelines-for-creating-an-effective-keyword"></a>Wskazówki dotyczące tworzenia efektywnego słowa kluczowego

Tworzenie efektywnego słowa kluczowego jest niezbędne do zapewnienia spójnej i dokładnej reakcji urządzenia. Dostosowanie słowa kluczowego jest skutecznym sposobem na odróżnienie urządzenia i wzmocnienie oznakowania. W tym artykule omówiono niektóre zasady dotyczące tworzenia obowiązującego słowa kluczowego.

## <a name="choose-an-effective-keyword"></a>Wybierz efektywne słowo kluczowe

Podczas wybierania słowa kluczowego należy wziąć pod uwagę następujące wytyczne:

* Słowo kluczowe powinno być słowem lub frazą w języku angielskim.

* Powinno zająć maksymalnie dwóch sekund powiedzieć.

* Najlepiej słów sylab 4 do 7. Na przykład "Hej, Computer" jest dobrym słowem kluczowym. Po prostu "Hej," jest niska.

* Słowa kluczowe powinny być zgodne z typowymi regułami wymowy w języku angielskim

* Unikatowy lub nawet gotowych słowa występującego typowych reguł Wymowa angielskie może zmniejszyć liczbę fałszywych alarmów. Na przykład "computerama" może być dobrym słowem kluczowym.

* Nie należy wybierać wspólnego programu word. Na przykład "jedzenie" i "Przejdź" to słowa, które ludzie mówią, często w zwykłych konwersacji. Mogą one false wyzwalaczy dla Twojego urządzenia.

* Unikaj używania słowa kluczowego, które może mieć alternatywne wymowy. Użytkownicy musi znać "prawo" Wymowa można pobrać swoje urządzenie. Na przykład "509" mogą występować w "pięć 9, zero" "pięć AHA dziewięć," lub "pięć sto i dziewięć." "R.E.I." mogą występować w "r-e-i" lub "ray." Może być wymawiane "Live", "/līv/" lub "/liv/".

* Nie należy używać znaków specjalnych, symbole i cyfry. Na przykład "go #" i "20 + koty" mogą być problematycznymi słowami kluczowymi. Jednak "Przejdź do sharp" lub "dwadzieścia plus koty" może działać. Można nadal stosować symbole w znakowanie i używać marketingowych i dokumentacji wzmocnienie Wymowa odpowiednie.

> [!NOTE]
> Jeśli wybierzesz słowo znakowe jako słowo kluczowe, upewnij się, że jesteś właścicielem tego znaku towarowego lub że masz uprawnienia od właściciela znaku towarowego do korzystania z tego słowa. Firma Microsoft nie ponosi odpowiedzialności za wszelkie problemy prawne, które mogą wystąpić przy wyborze słowa kluczowego.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [utworzyć niestandardowe słowo kluczowe przy użyciu programu Speech Studio](speech-devices-sdk-create-kws.md).
