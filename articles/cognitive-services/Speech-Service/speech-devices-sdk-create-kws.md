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
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: bf9afb66163532b4095e0d30b1167010320abbf8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490932"
---
# <a name="create-a-custom-keyword-by-using-the-speech-service"></a>Tworzenie niestandardowego słowa kluczowego przy użyciu usługi mowy

Urządzenie zawsze nasłuchuje słowa kluczowego (lub frazy). Na przykład "Hey Cortana" to słowo kluczowe dla Asystenta Cortany. Gdy użytkownik wyświetla słowo kluczowe, urządzenie wysyła wszystkie kolejne audio do chmury, dopóki użytkownik nie przestanie mówić. Dostosowanie słowa kluczowego jest skutecznym sposobem na odróżnienie urządzenia i wzmocnienie oznakowania.

W tym artykule dowiesz się, jak utworzyć niestandardowe słowo kluczowe dla urządzenia.

## <a name="choose-an-effective-keyword"></a>Wybierz efektywne słowo kluczowe

Podczas wybierania słowa kluczowego należy wziąć pod uwagę następujące wytyczne:

* Słowo kluczowe powinno być słowem w języku angielskim lub frazą. Nie może upłynąć dłużej niż dwa sekundy.

* Słowa z 4 do 7 sylab działają najlepiej. Na przykład "Hej, Computer" jest dobrym słowem kluczowym. Tylko "Hej" jest słabo.

* Słowa kluczowe powinny być zgodne z typowymi regułami wymowy w języku angielskim

* Unikatowy lub nawet utworzony wyraz, który jest zgodny z typowymi regułami wymowy w języku angielskim, może obniżyć liczbę fałszywie dodatnich. Na przykład "computerama" może być dobrym słowem kluczowym.

* Nie wybieraj wspólnego wyrazu. Na przykład "Eat" i "go" są słowami, które ludzie często mówią w zwykłych konwersacjach. Mogą to być fałszywe wyzwalacze dla Twojego urządzenia.

* Unikaj używania słowa kluczowego, które może mieć alternatywne wymowy. Użytkownicy będą musieli znać wymowę "Right", aby uzyskać odpowiedzi na swoje urządzenia. Na przykład "509" może być wymawiane "5 0 9", "5 0 9" lub "509". "R.E.I." może być wymawiany "r-e-i" lub "ray". "Na żywo" może być wymawiane "/līv/" lub "/Liv/".

* Nie należy używać znaków specjalnych, symboli ani cyfr. Na przykład "go #" i "20 + koty" nie będzie dobrym słowami kluczowymi. Jednak może się zdarzyć, że "nie tylko" lub "dwadzieścia i koty". Można nadal używać symboli w znakowaniu i korzystać z marketingu i dokumentacji, aby wzmocnić odpowiednią wymowę.

> [!NOTE]
> Jeśli wybierzesz słowo znakowe jako słowo kluczowe, upewnij się, że jesteś właścicielem tego znaku towarowego lub że masz uprawnienia od właściciela znaku towarowego do korzystania z tego słowa. Firma Microsoft nie ponosi odpowiedzialności za wszelkie problemy prawne, które mogą wystąpić przy wyborze słowa kluczowego.

## <a name="create-your-keyword"></a>Utwórz słowo kluczowe

Aby można było użyć niestandardowego słowa kluczowego z urządzeniem, musisz utworzyć słowo kluczowe przy użyciu usługi generowania słów kluczowych firmy Microsoft. Po podaniu słowa kluczowego Usługa tworzy plik, który jest wdrażany w twoim zestawie deweloperskim, aby włączyć słowo kluczowe na urządzeniu.

1. Przejdź do programu [Speech Studio](https://aka.ms/sdsdk-speechportal) i **Zaloguj się** lub, jeśli nie masz jeszcze subskrypcji mowy, wybierz pozycję [**Utwórz subskrypcję**](https://go.microsoft.com/fwlink/?linkid=2086754).

1. Na stronie [niestandardowe słowo kluczowe](https://aka.ms/sdsdk-wakewordportal) wpisz słowo kluczowe, a następnie kliknij przycisk **Dodaj słowo kluczowe**. Mamy pewne [wskazówki](#choose-an-effective-keyword) ułatwiające wybór efektywnego słowa kluczowego. Pomoc techniczna jest obecnie ograniczona do języka en-US.

    ![Wprowadź słowo kluczowe](media/speech-devices-sdk/custom-kws-portal-enter-keyword.png)

1. Portal utworzy teraz wymowy kandydatów dla słowa kluczowego. Nasłuchiwanie poszczególnych kandydatów przez kliknięcie przycisku Odtwórz i usunięcie kontroli obok wszelkich wymowy, które są nieprawidłowe. Gdy zaznaczone są tylko dobre wymowy, wybierz pozycję **Prześlij** , aby rozpocząć generowanie słowa kluczowego. Jeśli chcesz zmienić słowo kluczowe, najpierw usuń istniejące, klikając przycisk Usuń, który pojawia się po prawej stronie wiersza, gdy przesuwa się nad nim.

    ![Przejrzyj słowo kluczowe](media/speech-devices-sdk/custom-kws-portal-review-keyword.png)

1. Generowanie modelu może potrwać do jednej minuty. Następnie zostanie wyświetlony monit o pobranie pliku.

    ![Pobierz słowo kluczowe](media/speech-devices-sdk/custom-kws-portal-download-keyword.png)

1. Zapisz plik zip na komputerze. Ten plik będzie potrzebny do wdrożenia niestandardowego słowa kluczowego w programie Development Kit.

## <a name="next-steps"></a>Następne kroki

Przetestuj niestandardowe słowo kluczowe przy użyciu [zestawu Speech Devices SDK szybkiego startu](https://aka.ms/sdsdk-quickstart).
