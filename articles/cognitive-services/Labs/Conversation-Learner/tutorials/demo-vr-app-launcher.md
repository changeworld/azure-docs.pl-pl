---
title: Pokaz uruchamiania aplikacji stan aplikacji, wirtualne uczeń konwersacji - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak utworzyć demonstrację aplikacji uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 3e41125bf7da9ee64d666d22cb275af01af54012
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348665"
---
# <a name="demo-virtual-reality-app-launcher"></a>Pokaz: Uruchamianie aplikacji rzeczywistości wirtualnej

Ten przewodnik przedstawia rzeczywistości wirtualnej uruchamiania aplikacji, obsługi poleceń, takich jak "start Skype i umieszcza niej". Użytkownik musi powiedzieć aplikacji nazwy i lokalizacji, aby uruchomić aplikację. Uruchamianie aplikacji jest obsługiwany przez wywołanie interfejsu API. Gdy nazwa aplikacji jest rozpoznawana przez użytkownika, entityDetectionCallback sprawdza, czy żądany aplikacji zgodny co najmniej jednej aplikacji na liście zainstalowanych aplikacji. Obsługuje przypadek, gdzie żądanej aplikacji nie jest zainstalowany, a nazwa aplikacji jest niejednoznaczny (odpowiada więcej niż jeden zainstalowaną aplikację).

## <a name="requirements"></a>Wymagania

Ten samouczek wymaga działa VRAppLauncher bot

    npm run demo-vrapp

### <a name="open-the-demo"></a>Otwórz pokaz

Na liście aplikacji w interfejsie użytkownika sieci web kliknij VRAppLauncher. 

## <a name="entities"></a>Jednostki

Utworzono jednostek cztery:

- AppName: na przykład Skype
- PlacementLocation: na przykład tablicy
- UnknownAppName: programowe jednostki, która ustawia systemu, gdy nie rozpoznaje nazwy jednostki użytkownik odpowie, np. ponieważ nie został zainstalowany.
- DisAmbigAppNames: powiedzieć tablicy dwóch lub więcej nazw zainstalowaną aplikację zgodne użytkownika. 

![](../media/tutorial_vrapplauncher_entities.PNG)

### <a name="actions"></a>Akcje

Utworzono zestaw akcji, który zawiera wywołuje LaunchApp, w którym zostanie uruchomiona wywołanie funkcji, aby uruchomić aplikację interfejsu API.

![](../media/tutorial_vrapplauncher_actions.PNG)

### <a name="training-dialogs"></a>Szkolenie w oknach dialogowych
Firma Microsoft zdefiniowanych wiele okien dialogowych szkolenia.

![](../media/tutorial_vrapplauncher_dialogs.PNG)

Na przykład spróbujmy sesji nauczania.

1. Kliknij przycisk Train okien dialogowych, następnie nowe okno pociągu.
1. Wprowadź "hi".
2. Kliknij przycisk wynik akcji.
3. Kliknij opcję "aplikacji, które chcesz uruchomić?"
4. Wprowadź "outlook".
    - Należy pamiętać, że LUIS rozpoznaje ją jako jednostki.
5. Kliknij przycisk wynik akcji.
3. Kliknij, aby wybrać "gdzie chcesz ją umieścić?"
4. Wprowadź "na tablicy".
    - Należy pamiętać, że LUIS rozpoznaje ją jako PlacementLocation.
2. Wprowadź wynik akcji.
6. Wybierz opcję "LaunchApp"
7. System: "Uruchamianie programu outlook na tablicy".
    - Należy pamiętać, że jest to wyzwalane wywołanie interfejsu API. Kod dla tego wywołania jest w C:\<\installedpath > \src\demos\demoVRAppLauncher.ts. Nie faktycznie ma logiki można jednak uruchomić program outlook dla tego pokazu.
    - Czyści AppName i PlacementLocation jednostek. Zwraca Powyższy ciąg jako odpowiedzi.
4. Kliknij przycisk Done nauczania.

![](../media/tutorial_vrapplauncher_callbackcode.PNG)

Zacznijmy innej sesji szkolenia obsługi nieznanych i niejednoznaczne jednostek.

1. Kliknij przycisk Nowe okno dialogowe pociągu.
1. Wprowadź "OneNote start". 
    - Go rozpozna, że nazwa aplikacji, ponieważ EntityDetectionCallback zdefiniowane w ma kod użytkownika nazwa została wprowadzona i jest rozpoznawany jako jego nazwę aplikacji przez dopasowanie do listy aplikacji zdefiniowane w kodzie. Zwraca zestaw wszystkich zgodnych aplikacji. 
    - Jeśli z listy dopasowań wynosi zero, oznacza to, że aplikacja nie jest zainstalowana. Następnie są umieszczane w unknownAppName.
    - W przypadku odnalezienia więcej niż jedną aplikację, utworzy skopiuj je do DisambigAppNames i wyczyść AppName jednostki.
2. Kliknij przycisk wynik akcji.
3. Kliknij, aby wybrać "Niestety, nie wiem aplikacji $UknownAppName".
4. Wprowadź "amazon start". Zostanie uruchomiony innej ścieżki.
5. Kliknij przycisk wynik akcji.
    - Uwaga Amazon wideo i muzyka Amazon są teraz DisambigAppNames pamięci. I OneNote został wyczyszczony.
3. Kliknij przycisk do wybierz opcję "Brak kilka aplikacji, tak jak dźwiękowych..."
    - Należy zauważyć, że wynik nie jest zbyt duża, ponieważ istnieje tylko kilka szkolenia okien dialogowych do tego punktu. Prawdopodobnie będzie konieczne niektóre więcej tutaj modelu więcej decydujący.
2. Wprowadź wynik akcji.
4. Kliknij przycisk Done nauczania.

Jak przeprowadzić rozpoznawanie jednostek ma teraz widoczna. Pokaz również ilustrowane wywołania zwrotne interfejsu API i wykazało szablon do zbierania informacji o sprawdzanie obecności i niejednoznaczności i podejmowania działań prawej, oparta na.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wdrażanie bot uczeń konwersacji](../deploy-to-bf.md)
