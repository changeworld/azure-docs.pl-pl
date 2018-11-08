---
title: Pokaz uruchamianie aplikacji modelu, wirtualnej mogą stać się rzeczywistością uczeń konwersacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak utworzyć model uczeń konwersacji pokaz.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 31efcb2b2f05bd7a49701be708a89db8f2ef26f7
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51229701"
---
# <a name="demo-virtual-reality-app-launcher"></a>Pokaz: Uruchamianie aplikacji rzeczywistości wirtualnej

Ten pokaz ilustruje rzeczywistość wirtualna na uruchamiania modelu, który obsługuje poleceń, takich jak "start Skype i umieścić w na ścianie". Użytkownik musi podać nazwę aplikacji i lokalizacji w celu uruchomienia aplikacji. Uruchamianie modelu jest obsługiwany przez wywołanie interfejsu API. Po rozpoznaniu nazwy aplikacji przez użytkownika entityDetectionCallback sprawdza, czy żądana aplikacji jest zgodna z co najmniej jedną aplikację na liście zainstalowanych aplikacji. Obsługuje przypadek, gdzie Żądana aplikacja nie zostanie zainstalowana, a nazwa aplikacji jest niejednoznaczny (odpowiada więcej niż jeden zainstalowanej aplikacji).

## <a name="video"></a>Połączenia wideo

[![Pokaz rzeczywistości Wirtualnej aplikacji (wersja zapoznawcza)](https://aka.ms/cl-demo-vrapp-preview)](https://aka.ms/blis-demo-vrapp)

## <a name="requirements"></a>Wymagania

Ten samouczek wymaga, że VRAppLauncher bot działa:

    npm run demo-vrapp
    
### <a name="open-the-demo"></a>Otwórz wersję demonstracyjną

Na liście modelu interfejsu użytkownika sieci web należy kliknąć VRAppLauncher. 

## <a name="entities"></a>Jednostki

Utworzyliśmy cztery jednostki:

- Nazwa aplikacji: na przykład programu Skype
- PlacementLocation: na przykład tablicy
- UnknownAppName: programowe jednostki, która ustawia systemu, gdy nazwa jednostki nie rozpoznaje użytkownik odpowie, na przykład, ponieważ nie został zainstalowany.
- DisAmbigAppNames: mówi tablicę dwóch lub więcej nazw zainstalowanej aplikacji, które odpowiadają użytkownika. 

![](../media/tutorial_vrapplauncher_entities.PNG)

### <a name="actions"></a>Akcje

Utworzyliśmy zestaw akcji, który zawiera interfejs API o nazwie LaunchApp, który rozpocznie się wywołanie funkcji, aby uruchomić aplikację.

![](../media/tutorial_vrapplauncher_actions.PNG)

### <a name="training-dialogs"></a>Szkolenie w oknach dialogowych
Zdefiniowaliśmy wiele okien dialogowych szkolenia.

![](../media/tutorial_vrapplauncher_dialogs.PNG)

Na przykład Wypróbujmy sesji nauczania.

1. Kliknij przycisk okien dialogowych szkolenie, polecenie nowe okno pociągu.
1. Wprowadź "Cześć".
2. Kliknij wynik akcji.
3. Kliknij opcję "aplikacji, która chcesz zacząć?"
4. Wprowadź "outlook".
    - Usługa LUIS rozpoznaje je jako jednostkę.
5. Kliknij wynik akcji.
3. Kliknij, aby wybrać "gdzie chcesz go umieścić?"
4. Wprowadź "na ścianie".
    - Usługa LUIS rozpoznaje je jako PlacementLocation.
2. Wprowadź wynik akcji.
6. Wybierz pozycję "LaunchApp"
7. System: "uruchamiania programu outlook na ścianie".
    - Wyzwalane, to wywołanie interfejsu API. Kod dla tego wywołania jest w C:\<\installedpath > \src\demos\demoVRAppLauncher.ts. Jednak nie faktycznie zawiera kod, który można uruchomić programu Outlook dla tej wersji demonstracyjnej.
    - To powoduje wyczyszczenie AppName i PlacementLocation jednostek. Zwraca Powyższy ciąg jako odpowiedzi.
4. Kliknij przycisk Gotowe, nauczania.

![](../media/tutorial_vrapplauncher_callbackcode.PNG)

Zacznijmy od innej sesji szkoleniowych obsługę jednostek nieznany i niejednoznaczne.

1. Kliknij okno dialogowe Nowy pociągu.
1. Wprowadź "Uruchom program OneNote". 
    - Model rozpoznaje programu OneNote jako nazwę aplikacji. `EntityDetectionCallback` Funkcję zdefiniowaną w kodzie jest rozpoznawany jako nazwy wprowadzonej przez użytkownika na nazwę aplikacji, dopasowując go do listy aplikacji, zdefiniowana w kodzie. Zwraca zestaw wszystkich zgodnych aplikacji. 
    - Jeśli z listy dopasowań wynosi zero, oznacza to, że aplikacja nie jest zainstalowana. Następnie są umieszczane w unknownAppName.
    - Jeśli znajdzie więcej niż jedną aplikację, kopiuje je do `DisambigAppNames` i czyści jednostki AppName.
2. Kliknij wynik akcji.
3. Kliknij, aby wybrać "Niestety, nie wiem, aplikacja $UknownAppName".
4. Wprowadź "start Amazon". Spróbujemy innej ścieżki.
5. Kliknij wynik akcji.
    - Amazon wideo i odtwarzaniu muzyki Amazon są teraz w `DisambigAppNames` został wyczyszczony pamięci i OneNote.
3. Kliknij przycisk do wybierz opcję "Istnieje kilka aplikacji, które brzmią Krewny, który..."
    - Wynik nie jest zbyt duża, ponieważ tylko zdefiniowaliśmy kilka szkolenia okien dialogowych do tej pory. Definiowanie więcej szkoleń okien dialogowych czyniłyby, model bardziej decydujący.
2. Wprowadź wynik akcji.
4. Kliknij przycisk Gotowe, nauczania.

Teraz wiesz jak to zrobić rozpoznawania jednostek. Pokaz również zilustrowane wywołania zwrotne interfejsu API, a także wykazało, że szablon zbieranie informacji o sprawdzania pod kątem obecności i niejednoznaczności i w związku z tym podjęcia odpowiednich akcji.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wdrażanie bot uczeń konwersacji](../deploy-to-bf.md)
