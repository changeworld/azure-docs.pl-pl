---
title: Tworzenie niestandardowe słowo aktywujące
description: Tworzenie niestandardowe słowo aktywujące dla zestawu Speech Devices SDK.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 04/28/2018
ms.author: v-jerkin
ms.openlocfilehash: 615a901c70fff92141442699ea6e4b8fce1c9ace
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282577"
---
# <a name="create-a-custom-wake-word-using-speech-service"></a>Utwórz niestandardowe słowo aktywujące przy użyciu usługi mowy

Urządzenie jest zawsze nasłuchiwanie słowo aktywujące (lub frazy). Na przykład "Hey Cortana" to słowo aktywujące Asystenta Cortany. Gdy użytkownik odpowie słowo aktywujące, urządzenie zostanie uruchomione, wysyła wszystkie kolejne audio do chmury, dopóki użytkownik zatrzymuje wypowiedzi. Dostosowywanie usługi słowo aktywujące jest efektywnym sposobem rozróżnienia urządzenia i zwiększanie znakowanie.

W tym artykule dowiesz się, jak utworzyć niestandardowe słowo aktywujące dla Twojego urządzenia.

## <a name="choosing-an-effective-wake-word"></a>Wybieranie skuteczne słowo aktywujące

Wybierając słowo aktywujące, weź pod uwagę poniższe wskazówki.

* Twoje słowo aktywujące powinny być słowa angielskiego lub frazy. Powinno zająć maksymalnie dwóch sekund powiedzieć.

* Najlepiej słów sylab 4 – 7. Na przykład "Hey, komputer" jest słowem wznawiania dobre, gdy tylko "Hej," jest niska.

* Wyrazy wznawiania należy stosować typowych reguł Wymowa w języku angielskim.

* Typowe regułom angielskiej Wymowa słowem unikatowy, lub nawet wykonane można redukować liczbę fałszywych alarmów. Na przykład "computerama" może być dobrym aktywujące.

* Nie należy wybierać wspólnego programu word. Na przykład "jedzenie" i "Przejdź" to słowa, które ludzie mówią, często w zwykłych konwersacji. Może być fałszywe wyzwalaczy dla Twojego urządzenia.

* Należy unikać używania aktywujące, mających wymowy alternatywne. Użytkownicy musi znać "prawo" Wymowa można pobrać swoje urządzenie. Na przykład "509" może występować jako "pięć 9 zero", "pięć AHA dziewięć", lub "pięć sto i dziewięć." "R.E.I." może być wymawiane jako "R E I" lub "Ray." "Live" może być wymawiane jako [līv] lub [liv].

* Nie należy używać znaków specjalnych, symbole i cyfry. Na przykład "Go #" i "20 + koty" nie jest dobry wznawiania słów. Jednak "Przejdź do sharp" lub "dwadzieścia plus koty" może działać. Można nadal używać symboli w znakowanie i korzystać marketingowych i dokumentacji wzmocnienie Wymowa odpowiednie.

> [!NOTE]
> Wyrazem chronionym znakiem towarowym jako swojej słowo aktywujące, należy się upewnić, że możesz właścicielem znaku towarowego; w przeciwnym razie uzyskał pozwolenie od właściciela znaków towarowych z niego korzystać. Microsoft nie ponosi odpowiedzialność za wszelkie kwestie prawne, które mogą wystąpić z użyciem regionu wybranego słowo aktywujące.

## <a name="creating-your-wake-word"></a>Tworzenie usługi słowo aktywujące

Zanim użyjesz niestandardowe słowo aktywujące urządzeń z systemem, należy utworzyć przy użyciu usługi Microsoft niestandardowe Wake generowania programu Word. Po podaniu słowo wznawiania, tworzy usługi, plik, który można następnie wdrożyć swój zestaw deweloperski umożliwiające swoje słowo aktywujące na urządzeniu z systemem.

1. Przejdź do [portal usługi Custom Speech Service](https://cris.ai/).

2. Utwórz nowe konto, za pomocą adresu e-mail, na którym odebrano zaproszenie dla usługi Azure Active Directory. 

    ![Utwórz nowe konto](media/speech-devices-sdk/wake-word-1.png)
 
3.  Po zalogowaniu, wypełnij formularz, a następnie kliknij przycisk **Rozpocznij podróż.**

    ![zalogowali się pomyślnie](media/speech-devices-sdk/wake-word-3.png)
 
4. **Niestandardowe wznawiania w programie Word** strona nie jest dostępny publicznie, więc nie ma żadnego połączenia wykorzystującej istnieje. Kliknij przycisk lub zamiast tego Wklej to łącze: https://cris.ai/customkws.

    ![ukrytą stronę](media/speech-devices-sdk/wake-word-4.png)
 
6. Wpisz wyraz wznawiania, co pozwala następnie **przesyłania** go.

    ![Wprowadź swoje słowo aktywujące](media/speech-devices-sdk/wake-word-5.png)
 
7. Może upłynąć kilka minut, zanim pliki do wygenerowania. Okrąg rotowania powinien zostać wyświetlony na karcie w przeglądarce. Po chwili jest wyświetlany pasek informacji pytaniem, aby pobrać `.zip` pliku.

    ![odbieranie pliku zip](media/speech-devices-sdk/wake-word-6.png)

8. Zapisz `.zip` plik na swoim komputerze. Potrzebujesz tego pliku, aby wdrażać niestandardowe słowo aktywujące development kit, postępując zgodnie z instrukcjami w [wprowadzenie do zestawu Speech Devices SDK](speech-devices-sdk-qsg.md).

9. Możesz teraz **się wylogować.**

## <a name="next-steps"></a>Kolejne kroki

Aby rozpocząć pracę, Pobierz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) i Zamów zestawu Speech Devices SDK.

> [!div class="nextstepaction"]
> [Zamów zestawu Speech Devices SDK](get-speech-devices-sdk.md)

