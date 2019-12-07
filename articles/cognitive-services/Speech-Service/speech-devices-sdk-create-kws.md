---
title: Tworzenie niestandardowego słowa kluczowego — usługa mowy
titleSuffix: Azure Cognitive Services
description: Urządzenie zawsze nasłuchuje słowa kluczowego (lub frazy). Gdy użytkownik wyświetla słowo kluczowe, urządzenie wysyła wszystkie kolejne audio do chmury, dopóki użytkownik nie przestanie mówić. Dostosowanie słowa kluczowego jest skutecznym sposobem na odróżnienie urządzenia i wzmocnienie oznakowania.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: erhopf
ms.openlocfilehash: 42bcc336bfeb325a08c3d65438d66690c0b35100
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896402"
---
# <a name="create-a-custom-keyword-by-using-the-speech-service"></a>Tworzenie niestandardowego słowa kluczowego przy użyciu usługi mowy

Urządzenie zawsze nasłuchuje słowa kluczowego (lub frazy). Na przykład "Hey Cortana" to słowo kluczowe dla Asystenta Cortany. Gdy użytkownik wyświetla słowo kluczowe, urządzenie wysyła wszystkie kolejne audio do chmury, dopóki użytkownik nie przestanie mówić. Dostosowanie słowa kluczowego jest skutecznym sposobem na odróżnienie urządzenia i wzmocnienie oznakowania.

W tym artykule dowiesz się, jak utworzyć niestandardowe słowo kluczowe dla urządzenia.

## <a name="choose-an-effective-keyword"></a>Wybierz efektywne słowo kluczowe

Podczas wybierania słowa kluczowego należy wziąć pod uwagę następujące wytyczne:

* Słowo kluczowe powinno być słowem w języku angielskim lub frazą. Powinno zająć maksymalnie dwóch sekund powiedzieć.

* Najlepiej słów sylab 4 do 7. Na przykład "Hej, Computer" jest dobrym słowem kluczowym. Po prostu "Hej," jest niska.

* Słowa kluczowe powinny być zgodne z typowymi regułami wymowy w języku angielskim

* Unikatowy lub nawet gotowych słowa występującego typowych reguł Wymowa angielskie może zmniejszyć liczbę fałszywych alarmów. Na przykład "computerama" może być dobrym słowem kluczowym.

* Nie należy wybierać wspólnego programu word. Na przykład "jedzenie" i "Przejdź" to słowa, które ludzie mówią, często w zwykłych konwersacji. Mogą one false wyzwalaczy dla Twojego urządzenia.

* Unikaj używania słowa kluczowego, które może mieć alternatywne wymowy. Użytkownicy musi znać "prawo" Wymowa można pobrać swoje urządzenie. Na przykład "509" mogą występować w "pięć 9, zero" "pięć AHA dziewięć," lub "pięć sto i dziewięć." "R.E.I." mogą występować w "r-e-i" lub "ray." Może być wymawiane "Live", "/līv/" lub "/liv/".

* Nie należy używać znaków specjalnych, symbole i cyfry. Na przykład "go #" i "20 + koty" nie będzie dobrym słowami kluczowymi. Jednak "Przejdź do sharp" lub "dwadzieścia plus koty" może działać. Można nadal stosować symbole w znakowanie i używać marketingowych i dokumentacji wzmocnienie Wymowa odpowiednie.

> [!NOTE]
> Jeśli wybierzesz słowo znakowe jako słowo kluczowe, upewnij się, że jesteś właścicielem tego znaku towarowego lub że masz uprawnienia od właściciela znaku towarowego do korzystania z tego słowa. Firma Microsoft nie ponosi odpowiedzialności za wszelkie problemy prawne, które mogą wystąpić przy wyborze słowa kluczowego.

## <a name="create-your-keyword"></a>Utwórz słowo kluczowe

Aby można było użyć niestandardowego słowa kluczowego, należy utworzyć słowo kluczowe przy użyciu strony [niestandardowe słowo kluczowe](https://aka.ms/sdsdk-wakewordportal) w programie [Speech Studio](https://aka.ms/sdsdk-speechportal). Po podaniu słowa kluczowego tworzy plik, który jest wdrażany na urządzeniu.

1. Przejdź do programu [Speech Studio](https://aka.ms/sdsdk-speechportal) i **Zaloguj się** lub, jeśli nie masz jeszcze subskrypcji mowy, wybierz pozycję [**Utwórz subskrypcję**](https://go.microsoft.com/fwlink/?linkid=2086754).

1. Na stronie [niestandardowe słowo kluczowe](https://aka.ms/sdsdk-wakewordportal) Utwórz **Nowy projekt**. 

1. Wprowadź **nazwę**i opcjonalny **Opis**, a następnie wybierz język. Potrzebny będzie jeden projekt dla każdego języka i pomoc techniczna jest obecnie ograniczona do języka en-US.

    ![Opisz swój projekt słowa kluczowego](media/custom-keyword/custom-kws-portal-new-project.png)

1. Wybierz swój projekt z listy. 

    ![Wybierz projekt słowa kluczowego](media/custom-keyword/custom-kws-portal-project-list.png)

1. Aby rozpocząć nowy model słów kluczowych, kliknij pozycję **Testuj model**.

1. Wprowadź **nazwę** modelu słowa kluczowego i opcjonalny **Opis** , a następnie wpisz **słowo kluczowe** , a następnie kliknij przycisk **dalej**. Mamy pewne [wskazówki](#choose-an-effective-keyword) ułatwiające wybór efektywnego słowa kluczowego.

    ![Wprowadź słowo kluczowe](media/custom-keyword/custom-kws-portal-new-model.png) 

1. Portal utworzy teraz wymowy kandydatów dla słowa kluczowego. Nasłuchiwanie poszczególnych kandydatów przez kliknięcie przycisku Odtwórz i usunięcie kontroli obok wszelkich wymowy, które są nieprawidłowe. Gdy sprawdzane są tylko dobre wymowy, kliknij pozycję **uczenie** , aby rozpocząć generowanie słowa kluczowego. 

    ![Przejrzyj słowo kluczowe](media/custom-keyword/custom-kws-portal-choose-prons.png) 

1. Wygenerowanie modelu może potrwać do 10 minut. Lista słów kluczowych zmieni się z **przetwarzania** na **powodzenie** , gdy model zostanie ukończony. Następnie można pobrać plik.

    ![Przejrzyj słowo kluczowe](media/custom-keyword/custom-kws-portal-download-model.png) 

1. Zapisz plik zip na komputer. Ten plik będzie potrzebny do wdrożenia niestandardowego słowa kluczowego na urządzeniu.

## <a name="next-steps"></a>Następne kroki

Przetestuj niestandardowe słowo kluczowe przy użyciu [zestawu Speech Devices SDK szybkiego startu](https://aka.ms/sdsdk-quickstart).
