---
title: Jak używać uczeń konwersacji z innymi bot tworzenie technologii — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak używać uczeń konwersacji z botem, inne technologie do tworzenia.
services: cognitive-services
author: mattm
manager: larsliden
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 07/13/2018
ms.author: v-jaswel
ms.openlocfilehash: a03596ff8383a085314508d4a25d0ba89bcc3094
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39174572"
---
# <a name="how-to-use-conversation-learner-with-other-bot-building-technologies"></a>Jak używać uczeń konwersacji z botem, inne technologie do tworzenia

W tym samouczku opisano, jak używać uczeń konwersacji z botem, inne technologie do tworzenia i jak pamięci (lub stan) mogą być współużytkowane między te technologie 

## <a name="video"></a>Połączenia wideo

[![Samouczek 15 (wersja zapoznawcza)](http://aka.ms/cl-tutorial-15-preview)](http://aka.ms/blis-tutorial-15)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga przy użyciu emulatora usługi bot do utworzenia okien dialogowych dziennika, nie dziennika okna dialogowego internetowego interfejsu użytkownika.  

Ten samouczek wymaga, że bot samouczek hybrydowego jest uruchomiona:

    npm run tutorial-hybrid

## <a name="details"></a>Szczegóły

Uczeń konwersacji jest w formancie, stan wszystkich względem sesji uczeń konwersacji muszą być przechowywane w Menedżerze pamięci uczeń konwersacji. Jest to konieczne, ponieważ uczenie maszynowe wykorzystuje stan, aby określić, jak można dostarczać konwersacji. Stan zewnętrzne mogą być przekazywane do uczeń konwersacji w OnSessionStartCallback, która jest wywoływana po rozpoczęciu sesji. Wewnętrzne może zwrócić na zewnątrz OnSessionEndCallback gdy kończy się sesja.

Uczeń konwersacji można traktować prawie jako wywołanie funkcji, która przyjmuje pewne stanu początkowego i zwraca wartości.

W tym przykładzie utworzysz robota hybrydowych przy użyciu dwóch różnych systemów:
1. Model uczeń konwersacji <br />
Używa modelu uczeń konwersacji do określenia akcji następnym bot, w oparciu o bieżącą sesję.
Ta część bot przyjmuje jeden fragment początkowy stan `isOpen` (wskazuje, czy magazyn jest otwarte lub zamknięte) i zwraca inny element stanu `purchaseItem` (nazwa elementu użytkownika zakupów).

2. Dopasowywanie tekstu <br />
Po prostu analizuje przychodzące tekstu dla określonych parametrów i odpowiada.
Ta część bot zarządza Botów innych mechanizmów magazynu i odpowiada za uruchamianie sesji CL. W szczególności zarządza trzech zmiennych: `usingConversationLearner`, `storeIsOpen`, i `purchaseItem`.

Zacznijmy od przeglądając model używany w tej wersji demonstracyjnej.

### <a name="open-the-demo"></a>Otwórz wersję demonstracyjną

Na liście modelu interfejsu użytkownika sieci web kliknij samouczek-15-hybrydowych.

## <a name="entities"></a>Jednostki

Otwórz stronę jednostki i zwróć uwagę, dwie jednostki: `isOpen` i `purchaseItem`

Aby dowiedzieć się, jak te jednostki są używane, otwórz plik: `C:\<installedpath\>\src\demos\tutorialHybrid.ts` spojrzeć na wywołania zwrotne.

Należy zauważyć, że kod w `OnSessionStartCallback` kopiuje wartość `storeIsOpen` z magazynu konwersacji BotBuilder jako wartość `isOpen` jednostki, dzięki czemu są one dostępne dla uczeń konwersacji. Zobacz poniższy kod:

![](../media/tutorial17_sessionstart.PNG)

Podobnie, kod w `OnSessionEndCallback` (Jeśli sesja została zakończona z powodu nauczony działanie i nie tylko limitu czasu) kopiuje wartość jednostki `purchaseItem` poza usługą BotBuilder storage `purchaseItem`. Zobacz poniższy kod:

![](../media/tutorial17_sessionend.PNG)

Teraz Przyjrzyjmy się akcje.

## <a name="actions"></a>Akcje

Należy zauważyć, że model ma cztery akcje.

Zamierzone zasady działania są następujące:

- Jeśli `isOpen` zestaw jednostek, Bot zostanie wyświetlone pytanie "Co chcesz kupić?" i przechowywać w `puchaseItem` miejsca
- Jeśli `isOpen` nie jest ustawiony, Bot będzie wyświetlany tekst "Niestety mamy teraz zamknięte"
- Inne akcje są typu `END_SESSION`.
- Akcja END_SESSION wskazuje ConversationLearner, że Konwersacja została zakończona.

### <a name="overall-bot-logic"></a>Ogólny logiki Bot

Najpierw zobaczysz że jeśli stan Bot `usingConversationLearner` została ustawiona flaga, możemy przekazać sterowanie do uczeń konwersacji. Jeśli nie, możemy przekazać sterowanie do innego elementu.  W tym przykładzie są wyświetlane, dopasowania zwykłego tekstu, ale może to być inne technologie Bot, LUIS, w tym usługi QnA maker i jeszcze inne wystąpienie uczeń konwersacji.

Potrzebujemy sposobu dla użytkownika otworzyć i zamknij magazynu, dzięki czemu możemy zrobić ciąg porównania "open store" i "close store" i ustaw flagę "storeIsOpen".

Następnie należy sposób wyzwalacz obsługi kontroli nad tak, aby nasz Model uczeń konwersacji. Gdy firma Microsoft pasuje do ciągu "module" możemy wykonać następujące czynności:
- Ustaw `usingConversationLearner` flagi w pamięci Botów.
- Na nasz Model uczeń konwersacji, należy wywołać metodę "StartSession".  Spowoduje to wyzwolenie "onSessionStartCallback", który będzie inicjował `isOpen` wartości jednostki

Poniżej znajduje się:

![](../media/tutorial17_useConversationLearner.PNG)

Możemy również wykonać dopasowania tekstu do "Historia", co spowoduje wyświetlenie tego ostatniego elementu zakupu.
Ponadto jeśli czymkolwiek wpisze wyświetlamy polecenia dostępne użytkownika

## <a name="train-dialog"></a>Okno dialogowe szkolenie

Na potrzeby tego samouczka model jest już wstępnie przeszkolonych.  Testujemy pełną bot, aby zobaczyć efekt rozpoczęcia i zakończenia sesji wywołań zwrotnych, w praktyce.

## <a name="testing-the-bot"></a>Testowanie robota

W przeciwieństwie do pojedynczego botów modelu Odchudzony konwersacji nie będzie mógł testować jej w Interfejsie użytkownika uczeń konwersacji, jak można tylko wyświetlić, co jest obsługiwane przez Model uczeń konwersacji.

### <a name="install-the-bot-framework-emulator"></a>Zainstaluj Bot framework emulator

- Przejdź do obszaru [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator) (Ustawienia — Integracje i usługi).
- Pobierz i zainstaluj emulator.

### <a name="configure-the-emulator"></a>Konfigurowanie emulator

- Otwórz emulatora i upewnij się, że adres URL jest przeznaczony dla tego samego portu, bota na którym uruchomiona jest. Prawdopodobnie: `http://localhost:3978/api/messages`

### <a name="test"></a>Testowanie 

#### <a name="scenario-1-store-is-closed"></a>Scenariusz 1: Store jest zamknięty.
1. Wprowadź "module". Odbywa się przez dopasowanie tekstu i umożliwia uzyskanie kontroli do modelu uczeń konwersacji.
2. Wpisz "hello".  Ponieważ `isOpen` wartość nie jest ustawiona, bot będzie wyświetlany tekst "Niestety firma Microsoft jest zamknięty" i kończenia sesji.

#### <a name="scenario-2-store-is-open"></a>Scenariusz 2: Store jest otwarty
3. Wprowadź "Otwórz Sklep".  To ustawienie ustawi `isOpen` na wartość true.
4. Wprowadź "module".
5. Wpisz "hello".  Ponieważ `isOpen` wartość jest ustawiona na wartość true, bot będzie wyświetlany tekst "Co chcesz kupić?"
6. Wprowadź "przewodniczenia". "krzesło" zostaną zapisane do CL pamięci jako jednostka `purchaseItem`. Wywołanie zwrotne zakończenia sesji jest wywoływana, która kopiuje ta wartość w magazynie konwersacji.
7. Wprowadź "Historia".  Bot będzie wyświetlany tekst "Zakupiono krzesło", ponieważ było to przeczytanie `purchaseItem`.

## <a name="conclusion"></a>Podsumowanie

Za pomocą mają poznanych wyżej można połączyć uczeń konwersacji za pomocą innych technologii Bot
