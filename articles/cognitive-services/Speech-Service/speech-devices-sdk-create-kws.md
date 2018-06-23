---
title: Tworzenie niestandardowego wyraz | Dokumentacja firmy Microsoft
description: Tworzenie word niestandardowego dla zestawu SDK urządzenia mowy.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 04/28/2018
ms.author: v-jerkin
ms.openlocfilehash: 2575ed24bb931ca4da05dd6663b976406af590e6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349729"
---
# <a name="create-a-custom-wake-word-using-speech-service"></a>Tworzenie przy użyciu usługi mowy wyraz niestandardowego

Urządzenie jest zawsze nasłuchiwanie słowo wznawiania (lub wyrażenie). Na przykład "Witaj Cortana" jest słowem wznawiania Asystenta Cortana. Gdy użytkownik odpowie word wznawiania, urządzenie rozpoczyna wysyłanie wszystkie kolejne audio w chmurze, dopóki użytkownik przestanie mówiąc. Dostosowywanie programu word wznawiania jest efektywny sposób odróżnienia urządzenia i zwiększenia poziomu znakowanie.

W tym artykule dowiesz się tworzenie niestandardowego programu word dla urządzenia.

## <a name="choosing-an-effective-wake-word"></a>Wybieranie wznawiania wprowadzenia programu word

W przypadku wybrania słowa wznawiania, należy wziąć pod uwagę poniższe wskazówki.

* Program word wznawiania należy angielskiej wersji językowej wyraz lub frazę. Powinno zająć dłuższy niż dwie sekundy powiedzieć.

* Najlepiej słów 4-7 sylab. Na przykład "Witaj, komputer" jest słowem dobrej wznawiania podczas tylko "Witaj" jest niska.

* Słowa wznawiania należy wykonać wspólne zasady wymowy angielskiej wersji językowej.

* Unikatowy lub nawet gotowych wyrazu następującego wspólne zasady wymowy angielskiej wersji językowej można redukować liczbę fałszywych alarmów. Na przykład "computerama" może być wyrazu dobrych wznawiania.

* Nie należy wybierać opcji Typowe programu word. Na przykład "jeść" i "Przejdź" to słowa, których ludzie często mówią zwykłej konwersacji. Może być false Wyzwalacze dla danego urządzenia.

* Unikaj używania wyraz wznawiania mające alternatywnych wymowy. Użytkownicy musieliby znać wymowy "prawa", aby przywrócić urządzenia odpowiada. Na przykład "509" może występować w przypadku jako "pięć zero 9", "pięć Niestety dziewięć", lub "pięciu sto i 9." "R.E.I." może występować w przypadku jako "R E I" lub "Ray." "Słowo live" może występować w przypadku jako [līv] lub [liv].

* Nie należy używać znaków specjalnych, symbole i cyfry. Na przykład "Przejdź #" i "20 + kotów" nie jest dobrym wznawiania słów. Jednak "Przejdź do sharp" lub "dwadzieścia plus kotów" może działać. Można nadal stosować symbole w znakowaniu i korzystać marketing i dokumentacji wzmocnienie prawidłowego wymowy.

> [!NOTE]
> Wyrazem chronionym znakiem towarowym jako program word wznawiania, należy się upewnić, że użytkownik właścicielem znaku towarowego, w przeciwnym razie uprawnień od właściciela znak towarowy z niego korzystać. Firma Microsoft nie ma ponosić odpowiedzialności za problemach prawnych, które mogą wynikać z wybranym Word wznawiania.

## <a name="creating-your-wake-word"></a>Tworzenie programu word wznawiania

Zanim użyjesz word niestandardowego z urządzeniem, należy utworzyć ją przy użyciu usługi Microsoft niestandardowych Wake generowania programu Word. Po podaniu word wznawiania, usługi i tworzy plik można następnie wdrożyć zestawie deweloperów, aby włączyć program word wznawiania pracy na urządzeniu.

1. Przejdź do [portalu niestandardowe mowy](https://cris.ai/).

2. Utwórz nowe konto z adresem e-mail, na którym otrzymano zaproszenie usługi Azure Active Directory. 

    ![Utwórz nowe konto](media/speech-devices-sdk/wake-word-1.png)
 
3.  Po zalogowaniu, wypełnij formularz, a następnie kliknij przycisk **Start podróży.**

    ![pomyślnie zalogowany](media/speech-devices-sdk/wake-word-3.png)
 
4. **Word Wake niestandardowe** strona nie jest dostępna publicznie, więc nie ma żadnego linku pobierającej. Kliknij przycisk lub wklejania w to łącze: https://cris.ai/customkws.

    ![ukryte strony](media/speech-devices-sdk/wake-word-4.png)
 
6. W programie word wznawiania wybranego przez Ciebie typu, następnie **przesyłania** go.

    ![Wprowadź program word wznawiania](media/speech-devices-sdk/wake-word-5.png)
 
7. Może upłynąć kilka minut, aż pliki do wygenerowania. Powinny pojawić się obracające się koło na karcie przeglądarki. Po chwili, jest wyświetlany pasek informacji pytaniem, aby pobrać `.zip` pliku.

    ![odbieranie pliku zip](media/speech-devices-sdk/wake-word-6.png)

8. Zapisz `.zip` plik na swoim komputerze. Należy ten plik do wdrażania niestandardowego word development kit, postępując zgodnie z instrukcjami w [Rozpoczynanie pracy z zestawem SDK urządzenia mowy](speech-devices-sdk-qsg.md).

9. Możesz teraz **Wyloguj.**

## <a name="next-steps"></a>Kolejne kroki

Aby rozpocząć pracę, należy pobrać [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) i załóż SDK urządzenia mowy.

> [!div class="nextstepaction"]
> [Załóż mowy SDK urządzenia](get-speech-devices-sdk.md)

