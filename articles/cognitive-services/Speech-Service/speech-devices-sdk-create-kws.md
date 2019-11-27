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
ms.openlocfilehash: 9a5b5de71ee290b39603968cf4309171689e22e4
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184831"
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

1. Na stronie [niestandardowe słowo kluczowe](https://aka.ms/sdsdk-wakewordportal) wpisz słowo kluczowe, a następnie kliknij przycisk **Dodaj słowo kluczowe**. Mamy pewne [wskazówki](#choose-an-effective-keyword) ułatwiające wybór efektywnego słowa kluczowego. Pomoc techniczna jest obecnie ograniczona do języka en-US.

    ![Wprowadź słowo kluczowe](media/speech-devices-sdk/custom-kws-portal-enter-keyword.png)

1. Portal utworzy teraz wymowy kandydatów dla słowa kluczowego. Nasłuchiwanie poszczególnych kandydatów przez kliknięcie przycisku Odtwórz i usunięcie kontroli obok wszelkich wymowy, które są nieprawidłowe. Gdy zaznaczone są tylko dobre wymowy, wybierz pozycję **Prześlij** , aby rozpocząć generowanie słowa kluczowego. Jeśli chcesz zmienić słowo kluczowe, najpierw usuń istniejące, klikając przycisk Usuń, który pojawia się po prawej stronie wiersza, gdy przesuwa się nad nim.

    ![Przejrzyj słowo kluczowe](media/speech-devices-sdk/custom-kws-portal-review-keyword.png)

1. Generowanie modelu może potrwać do jednej minuty. Następnie zostanie wyświetlony monit o pobranie pliku.

    ![Pobierz słowo kluczowe](media/speech-devices-sdk/custom-kws-portal-download-keyword.png)

1. Zapisz plik zip na komputer. Ten plik będzie potrzebny do wdrożenia niestandardowego słowa kluczowego na urządzeniu.

## <a name="next-steps"></a>Następne kroki

Przetestuj niestandardowe słowo kluczowe przy użyciu [zestawu Speech Devices SDK szybkiego startu](https://aka.ms/sdsdk-quickstart).
