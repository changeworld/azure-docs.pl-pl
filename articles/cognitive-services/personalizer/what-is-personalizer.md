---
title: Co to jest usługa Personalizacja?
titleSuffix: Azure Cognitive Services
description: Personalizer to oparta na chmurze Usługa interfejsu API, która pozwala wybrać najlepsze wyniki, aby wyświetlić użytkowników, uczysz się na podstawie ich zachowania w czasie rzeczywistym.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 286a19207236392367b924bea7e26e90fd0db8d5
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68253463"
---
# <a name="what-is-personalizer"></a>Co to jest usługa Personalizacja?

Usługa Personalizacja platformy Azure to chmurowa usługa interfejsu API, która pozwala wybrać najlepsze środowiska dla użytkowników na podstawie ich zachowania w czasie rzeczywistym.

* Zawierają informacje dotyczące użytkowników i zawartości i otrzymywać najważniejsze akcję, aby wyświetlić użytkowników. 
* Nie ma potrzeby, aby wyczyścić i oznaczanie danych przed użyciem Personalizer.
* Przesyłanie opinii do Personalizer, gdy jest to wygodne dla Ciebie. 
* Wyświetlanie analiz w czasie rzeczywistym. 
* Umożliwia Personalizer większych nakładu pracy do analizy danych w ramach sprawdzania poprawności istniejących eksperymentów.

## <a name="how-does-personalizer-work"></a>Jak działa Personalizer?

Personalizer używa modeli uczenia maszynowego, aby dowiedzieć się, jakie działanie ma być ranking najwyższą pozycję w kontekście. Aplikacja kliencka zawiera listę możliwych działań z informacjami o nich. i informacje o kontekście, który może zawierać informacje dotyczące użytkowników, urządzeń itp. Personalizer Określa akcję do wykonania. Gdy Twoja aplikacja kliencka używa wybranej akcji, zapewnia informacje zwrotne do Personalizer w formie wynik osób trzecich. Po otrzymaniu opinii Personalizer automatycznie aktualizuje własny model używany dla przyszłych rangę.

## <a name="how-do-i-use-the-personalizer"></a>Jak używać Personalizer?

![Wybierz, które wideo, aby wyświetlić użytkownikowi za pomocą Personalizer](media/what-is-personalizer/personalizer-example-highlevel.png)

1. Wybierz aplikację, aby spersonalizować środowisko.
1. Utwórz i skonfiguruj wystąpienie usługi personalizacji w witrynie Azure portal. Każde wystąpienie jest Personalizer pętli.
1. Wywołaj Personalizer za pomocą informacji przy użyciu zestawu SDK (_funkcji_) dotyczące użytkowników i zawartości (_akcje_). Nie należy podać czyszczenia, etykietę danych przed użyciem Personalizer. 
1. W aplikacji klienckiej przedstawiać użytkownikowi wybranej przez Personalizer akcji.
1. Przesyłanie opinii do Personalizer wskazujący, jeśli użytkownik wybrał akcji Personalizer firmy przy użyciu zestawu SDK. Jest to _nagradzaj wynik_, zazwyczaj zakresu od -1 do 1.
1. Wyświetl analytics w witrynie Azure portal, aby ocenić, jak działa system oraz jak pomaga danych personalizacji.

## <a name="where-can-i-use-personalizer"></a>Gdzie można używać Personalizer?

Na przykład aplikacja kliencka można dodać Personalizer do:

* Personalizowanie, jakie artykułu jest wyróżniona na witryny sieci Web grup dyskusyjnych.    
* Optymalizuj umieszczania ad w witrynie internetowej.
* Wyświetlić spersonalizowane "zalecane element" w witrynie zakupów.
* Zaproponuj elementy interfejsu użytkownika, takie jak filtrów do zastosowania do określonego zdjęcia.
* Wybierz odpowiedź czatbot wyjaśnienia intencji użytkownika lub zasugerować akcji.
* Ustaw priorytet sugestie dotyczące co użytkownik powinien zrobić następny krok w procesie biznesowym.

## <a name="personalization-for-developers"></a>Personalizacja dla deweloperów

Usługa personalizer ma dwa interfejsy API:

* Wyślij informacje (_funkcji_) dotyczące użytkowników i zawartości (_akcje_) spersonalizować. Personalizer odpowiada za pomocą górnej akcji.
* Przesyłanie opinii do Personalizer o jak dobrze klasyfikację Prócz tego pracowałam jako numer zazwyczaj zakresu od 0 do 1 (poprzedniej sekcji powiedział -1 do 1). 

![Podstawowe sekwencję zdarzeń na potrzeby personalizacji](media/what-is-personalizer/personalization-intro.png)

## <a name="next-steps"></a>Następne kroki

* [Szybki start: Tworzenie pętli opinii wC#](csharp-quickstart-commandline-feedback-loop.md)
* [Użyj interaktywna demonstracja](https://personalizationdemo.azurewebsites.net/)
