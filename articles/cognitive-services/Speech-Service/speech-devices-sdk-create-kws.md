---
title: Utwórz niestandardowe słowo aktywujące — usługi mowy
titleSuffix: Azure Cognitive Services
description: Urządzenie jest zawsze nasłuchiwanie słowo aktywujące (lub frazy). Po użytkownik odpowie słowo aktywujące, urządzenie wysyła wszystkie kolejne audio w chmurze, dopóki użytkownik zatrzymuje mówić. Dostosowywanie usługi słowo aktywujące jest efektywnym sposobem rozróżnienia urządzenia i zwiększanie znakowanie.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: f43cbedc633b26a3a7fcbfb5f6a75da514bf0c45
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604865"
---
# <a name="create-a-custom-wake-word-by-using-the-speech-service"></a>Utwórz niestandardowe słowo aktywujące przy użyciu usługi mowy

Urządzenie jest zawsze nasłuchiwanie słowo aktywujące (lub frazy). Na przykład "Hey Cortana" to słowo aktywujące Asystenta Cortany. Po użytkownik odpowie słowo aktywujące, urządzenie wysyła wszystkie kolejne audio w chmurze, dopóki użytkownik zatrzymuje mówić. Dostosowywanie usługi słowo aktywujące jest efektywnym sposobem rozróżnienia urządzenia i zwiększanie znakowanie.

W tym artykule dowiesz się, jak utworzyć niestandardowe słowo aktywujące dla Twojego urządzenia.

## <a name="choose-an-effective-wake-word"></a>Wybierz obowiązujące słowo aktywujące

W przypadku wybrania słowa wznawiania, należy wziąć pod uwagę następujące wytyczne:

* Twoje słowo aktywujące powinny być słowa angielskiego lub frazy. Powinno zająć maksymalnie dwóch sekund powiedzieć.

* Najlepiej słów sylab 4 do 7. Na przykład "Hey, komputer" jest dobrym aktywujące. Po prostu "Hej," jest niska.

* Wyrazy wznawiania należy stosować typowych reguł Wymowa w języku angielskim.

* Unikatowy lub nawet gotowych słowa występującego typowych reguł Wymowa angielskie może zmniejszyć liczbę fałszywych alarmów. Na przykład "computerama" może być dobrym aktywujące.

* Nie należy wybierać wspólnego programu word. Na przykład "jedzenie" i "Przejdź" to słowa, które ludzie mówią, często w zwykłych konwersacji. Mogą one false wyzwalaczy dla Twojego urządzenia.

* Należy unikać używania aktywujące, która może zawierać alternatywne wymowy. Użytkownicy musi znać "prawo" Wymowa można pobrać swoje urządzenie. Na przykład "509" mogą występować w "pięć 9, zero" "pięć AHA dziewięć," lub "pięć sto i dziewięć." "R.E.I." mogą występować w "r-e-i" lub "ray." Może być wymawiane "Live", "/līv/" lub "/liv/".

* Nie należy używać znaków specjalnych, symbole i cyfry. Na przykład "Go #" i "20 + koty" nie jest dobry wznawiania słów. Jednak "Przejdź do sharp" lub "dwadzieścia plus koty" może działać. Można nadal stosować symbole w znakowanie i używać marketingowych i dokumentacji wzmocnienie Wymowa odpowiednie.

> [!NOTE]
> Wyrazem chronionym znakiem towarowym jako swojej słowo aktywujące, należy się upewnić, że posiadasz znaku towarowego lub czy masz pozwolenie od właściciela tych znaków towarowych, można użyć słowa. Microsoft nie ponosi odpowiedzialność za kwestie prawne, które mogą wystąpić z użyciem regionu wybranego słowo aktywujące.

## <a name="create-your-wake-word"></a>Utwórz swoje słowo aktywujące

Zanim użyjesz niestandardowe słowo aktywujące urządzeń z systemem należy utworzyć słowo aktywujące przy użyciu usługi Microsoft niestandardowe Wake generowania programu Word. Po podaniu słowo wznawiania, tworzy usługi, plik, który można wdrożyć na usługi deweloperski, aby umożliwić aktywujące na urządzeniu z systemem.

1. Przejdź do [Custom Speech portalu](https://aka.ms/sdsdk-speechportal) i **Zaloguj** lub jeśli nie masz subskrypcji mowy, wybierz [ **Utwórz subskrypcję**](https://go.microsoft.com/fwlink/?linkid=2086754)

    ![Portal usługi Custom Speech Service](media/speech-devices-sdk/wake-word-4.png)

1. W [niestandardowe wznawiania w programie Word](https://aka.ms/sdsdk-wakewordportal) stronie wpisz wyraz wznawiania i kliknij przycisk **Dodaj słowo aktywujące**. Mamy coś [wytycznych](#choose-an-effective-wake-word) aby ułatwić sobie wybór skuteczne — słowo kluczowe. Aktualnie obsługiwany jest tylko język en US.

    ![Wprowadź swoje słowo aktywujące](media/speech-devices-sdk/wake-word-5.png)

1. Trzy wymowy alternatywne z Twojej słowo aktywujące zostanie utworzony. Możesz wybrać wszystkie wymowy, które chcesz. Następnie wybierz pozycję **przesyłania** do generowania słowo aktywujące. Jeśli chcesz zmienić słowo aktywujące Usuń istniejącą grupę najpierw, po umieszczeniu kursora w wierszu Wymowa pojawi się ikona usuwania.

    ![Przejrzyj swoje słowo aktywujące](media/speech-devices-sdk/wake-word-6.png)

1. Może zająć jedną minutę na model, który ma zostać wygenerowane. Zostanie wyświetlony monit pobierania pliku.

    ![Pobierz swoje słowo aktywujące](media/speech-devices-sdk/wake-word-7.png)

1. Zapisz plik zip na komputer. Konieczne będzie ten plik, aby wdrożyć swoje niestandardowe słowo aktywujące development Kit.

## <a name="next-steps"></a>Kolejne kroki

Testowanie swoje niestandardowe słowo aktywujące z [szybkiego startu zestawu SDK urządzenia mowy](https://aka.ms/sdsdk-quickstart).
