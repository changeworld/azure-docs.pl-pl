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
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: b5ace2e741f900dd4ab7ba6518d0956284af35f6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61461616"
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

Zanim użyjesz niestandardowe słowo aktywujące urządzeń z systemem słowo aktywujące należy utworzyć przy użyciu usługi Microsoft niestandardowe Wake generowania programu Word. Po podaniu słowo wznawiania, tworzy usługi, plik, który można wdrożyć na usługi deweloperski, aby umożliwić aktywujące na urządzeniu z systemem.

1. Przejdź do [Portal usługi Custom Speech Service](https://cris.ai/).

    ![Portal usługi Custom Speech Service](media/speech-devices-sdk/wake-word-4.png)

1. Zaloguj się przy użyciu adresu e-mail, który otrzymał zaproszenie dla usługi Azure Active Directory.

1. **Niestandardowe wznawiania w programie Word** strona nie jest dostępny publicznie, więc nie ma żadnych bezpośredni link umożliwiający przejście istnieje. Funkcja Custom Speech wymaga subskrypcji platformy Azure, ale nie funkcji Wake niestandardowe w programie Word. Jeśli masz **nie nie znaleziono subskrypcji.** Strona błędu, po prostu zastąpić **"subskrypcji? komunikat o błędzie = nie % 20Subscriptions % 20found"** za pomocą "**customkws**" adres URL i wprowadź trafień. Adres URL powinien być jednym z następujących: https://westus.cris.ai/customkws, https://eastasia.cris.ai/customkws lub https://northeurope.cris.ai/customkws, w zależności od których jest Twoim regionie.

1. Wpisz słowo aktywujące wybranych przez użytkownika, a następnie wybierz **przesłać słowo**.

    ![Wprowadź swoje słowo aktywujące](media/speech-devices-sdk/wake-word-5.png)

1. Może upłynąć kilka minut, zanim pliki do wygenerowania. Powinien zostać wyświetlony rotowania okrąg w oknie przeglądarki. Po chwili informacji pasek jest wyświetlany, pytaniem, aby pobrać plik zip.

1. Zapisz plik zip na komputer. Należy ten plik, aby wdrażać niestandardowe słowo aktywujące development Kit. Aby wdrożyć niestandardowe słowo aktywujące, postępuj zgodnie z instrukcjami [wprowadzenie do zestawu Speech Devices SDK](speech-devices-sdk-qsg.md).

1. Wybierz **się wylogować.**

## <a name="next-steps"></a>Kolejne kroki

Aby rozpocząć pracę, Pobierz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) i Zamów zestawu Speech Devices SDK.

> [!div class="nextstepaction"]
> [Zamów zestawu Speech Devices SDK](get-speech-devices-sdk.md)
