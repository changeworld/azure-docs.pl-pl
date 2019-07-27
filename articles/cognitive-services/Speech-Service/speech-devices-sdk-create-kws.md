---
title: Tworzenie niestandardowej usługi Wake Word-mowę
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
ms.openlocfilehash: 2bc1a6cbbf1e0d790326849a41b0788e332daa31
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68553107"
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

Aby można było użyć niestandardowego programu do wznawiania pracy z urządzeniem, musisz utworzyć słowo wznawiania za pomocą usługi Microsoft Custom Wake Word Generation. Po udostępnieniu wyrazu wznawiania Usługa tworzy plik, który jest wdrażany w twoim zestawie deweloperskim, aby umożliwić programowi Word wznowienie na urządzeniu.

1. Przejdź do [portalu Custom Speech Service](https://aka.ms/sdsdk-speechportal) i **Zaloguj się** lub jeśli nie masz subskrypcji mowy, wybierz pozycję [**Utwórz subskrypcję**](https://go.microsoft.com/fwlink/?linkid=2086754)

    ![Portal Custom Speech Service](media/speech-devices-sdk/wake-word-4.png)

1. Na [niestandardowej stronie wybudzenia programu Word](https://aka.ms/sdsdk-wakewordportal) w wybranym przez siebie edytorze wznawiania kliknij przycisk **Dodaj program Wake Word**. Mamy pewne [wskazówki](#choose-an-effective-wake-word) ułatwiające wybór efektywnego słowa kluczowego. Obecnie obsługujemy tylko język en-US.

    ![Wprowadź swoje słowo aktywujące](media/speech-devices-sdk/wake-word-5.png)

1. Zostaną utworzone trzy alternatywne wymowy dla programu Wake Word. Możesz wybrać wszystkie wymowy. Następnie wybierz pozycję **Prześlij** , aby wygenerować program Wake Word. Jeśli chcesz zmienić słowo wznawiania, Usuń istniejące pierwsze, po umieszczeniu wskaźnika myszy w wierszu wymowy zostanie wyświetlona ikona usuwania.

    ![Przejrzyj słowo Wake](media/speech-devices-sdk/wake-word-6.png)

1. Generowanie modelu może potrwać do jednej minuty. Zostanie wyświetlony monit o pobranie pliku.

    ![Pobierz program Wake Word](media/speech-devices-sdk/wake-word-7.png)

1. Zapisz plik zip na komputer. Ten plik będzie potrzebny do wdrożenia niestandardowego programu wznawiania w programie Development Kit.

## <a name="next-steps"></a>Następne kroki

Przetestuj niestandardowe słowo wznawiania za pomocą [zestawu Speech Devices SDK szybkiego startu](https://aka.ms/sdsdk-quickstart).
