---
title: Utwórz niestandardowe słowo aktywujące
description: Dowiedz się, jak utworzyć niestandardowe słowo aktywujące dla zestawu Speech Devices SDK.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 04/28/2018
ms.author: v-jerkin
ms.openlocfilehash: e1a74828213b595e6955a750904de7a3c2a5a865
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44719090"
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

Zanim użyjesz niestandardowe słowo aktywujące urządzeń z systemem słowo aktywujące należy utworzyć przy użyciu usługi Microsoft niestandardowe Wake generowania programu Word. Po podaniu słowo wznawiania, tworzy usługi, plik, który można wdrożyć na Twoje deweloperski umożliwiające swoje słowo aktywujące na urządzeniu z systemem.

1. Przejdź do [portal usługi Custom Speech](https://cris.ai/).

2. Utwórz nowe konto, za pomocą adresu e-mail, na którym odebrano zaproszenie dla usługi Azure Active Directory. 

    ![Utwórz nowe konto](media/speech-devices-sdk/wake-word-1.png)
 
3.  Po zalogowaniu, wypełnij formularz, a następnie wybierz **Start Moja podróż**.

    ![Zalogowano pomyślnie](media/speech-devices-sdk/wake-word-3.png)
 
4. **Niestandardowe wznawiania w programie Word** strona nie jest dostępny publicznie, więc nie ma żadnego połączenia wykorzystującej istnieje. Wybierz lub Wklej to łącze zamiast: https://cris.ai/customkws.

    ![Strona niestandardowe wznawiania w programie Word jest ukryta](media/speech-devices-sdk/wake-word-4.png)
 
6. Wpisz słowo aktywujące wybranych przez użytkownika, a następnie wybierz **przesłać słowo**.

    ![Wprowadź swoje słowo aktywujące](media/speech-devices-sdk/wake-word-5.png)
 
7. Może upłynąć kilka minut, zanim pliki do wygenerowania. Powinien zostać wyświetlony rotowania okrąg w oknie przeglądarki. Po chwili informacji pasek jest wyświetlany, pytaniem, aby pobrać plik zip.

    ![Odbieranie pliku zip](media/speech-devices-sdk/wake-word-6.png)

8. Zapisz plik zip na komputer. Należy ten plik, aby wdrażać niestandardowe słowo aktywujące development Kit. Aby wdrożyć niestandardowe słowo aktywujące, postępuj zgodnie z instrukcjami [wprowadzenie do zestawu Speech Devices SDK](speech-devices-sdk-qsg.md).

9. Wybierz **się wylogować.**

## <a name="next-steps"></a>Kolejne kroki

Aby rozpocząć pracę, Pobierz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) i Zamów zestawu Speech Devices SDK.

> [!div class="nextstepaction"]
> [Zamów zestawu Speech Devices SDK](get-speech-devices-sdk.md)

