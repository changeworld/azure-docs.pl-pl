---
title: Jak używać Conversation Learner z innymi technologiami kompilowania bot — Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Dowiedz się, jak używać Conversation Learner z innymi technologiami kompilowania bot.
services: cognitive-services
author: mattm
manager: larsliden
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 07/13/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: c964c62c34f952a547d077e93e7bb4d0eb7b192d
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703678"
---
# <a name="how-to-use-conversation-learner-with-other-bot-building-technologies"></a>Jak używać Conversation Learner z innymi technologiami tworzenia bot

W tym samouczku opisano, jak używać Conversation Learner z innymi technologiami tworzenia bot oraz jak pamięć (lub stan) mogą być współużytkowane przez te technologie. 

## <a name="video"></a>Połączenia wideo

[![Samouczek hybrydowej botów w wersji zapoznawczej](https://aka.ms/cl_Tutorial_v3_Hybrid_Applications_Preview)](https://aka.ms/cl_Tutorial_v3_Hybrid_Applications)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga użycia emulatora Bot do tworzenia okien dialogowych dziennika, a nie interfejsu użytkownika sieci Web okna dialogowego. Więcej informacji na temat konfigurowania emulatora platformy bot Framework można znaleźć [tutaj](https://docs.microsoft.com/azure/bot-service/bot-service-debug-emulator?view=azure-bot-service-4.0). 

Ten samouczek wymaga, aby bot samouczka hybrydowego był uruchomiony:

    npm run tutorial-hybrid

## <a name="details"></a>Szczegóły

Gdy Conversation Learner jest w formancie, wszystkie Stany względem sesji Conversation Learner muszą być przechowywane w Menedżerze pamięci Conversation Learner. Jest to konieczne, ponieważ uczenie maszynowe używa stanu, aby określić sposób tworzenia konwersacji. Stan zewnętrzny można przesłać do Conversation Learner w OnSessionStartCallback, który jest wywoływany, gdy rozpoczyna się sesja. Stan wewnętrzny może być zwracany przez OnSessionEndCallback po zakończeniu sesji.

Można prawie traktować Conversation Learner jako wywołanie funkcji, które pobiera stan początkowy i zwraca wartości.

W tym przykładzie utworzysz bot hybrydowe przy użyciu dwóch różnych systemów:
1. Model Conversation Learner <br/>
    Używa modelu uczenia rozmowy, aby określić następną akcję bot w oparciu o bieżącą sesję. Ta część bot przyjmuje jeden z Stanów `isOpen` początkowych (który wskazuje, czy magazyn jest otwarty czy zamknięty) i zwraca inną część stanu `purchaseItem` (nazwę elementu zakupionego przez użytkownika).

2. Dopasowanie tekstu <br />
    Po prostu wyszukuje przychodzący tekst dla określonych ciągów i reaguje na nie. Ta część bota zarządza mechanizmem innych magazynów botów i jest odpowiedzialna za rozpoczęcie sesji CL. W tym celu zarządza trzema `usingConversationLearner`zmiennymi: `purchaseItem`, `storeIsOpen`i.

Zacznijmy od zastosowania modelu użytego w tym pokazie.

### <a name="open-the-demo"></a>Otwórz demonstrację

W interfejsie użytkownika sieci Web kliknij pozycję "Importowanie samouczków" i wybierz model o nazwie "samouczek-16-HybridBot".

## <a name="entities"></a>Jednostki

Otwórz stronę jednostki i zwróć uwagę na dwie jednostki `isOpen` : i`purchaseItem`

Aby zrozumieć, w jaki sposób są używane te jednostki, Otwórz `C:\<installedpath>\src\demos\tutorialHybrid.ts` plik: Aby sprawdzić wywołania zwrotne.

Zwróć uwagę, że kod `OnSessionStartCallback` w programie kopiuje `storeIsOpen` wartość z BotBuilder do magazynu konwersacji `isOpen` jako wartość jednostki, aby była dostępna do Conversation Learner. Zobacz następujący kod:

![](../media/tutorial17_sessionstart.PNG)

Podobnie, kod w `OnSessionEndCallback` (Jeśli sesja została zakończona ze względu na działanie, które nie jest tylko limitem czasu) kopiuje wartość Entity `purchaseItem` do magazynu `purchaseItem`BotBuilder. Zobacz następujący kod:

![](../media/tutorial17_sessionend.PNG)

Teraz przyjrzyjmy się akcjom.

## <a name="actions"></a>Akcje

Zwróć uwagę na to, że model ma cztery akcje.

Zamierzone reguły dla akcji są następujące:

- `isOpen` Jeśli jednostka jest ustawiona, bot będzie pytał "co chcesz kupić?" i przechowywać je `puchaseItem` w gnieździe.
- Jeśli `isOpen` nie jest ustawiona, bot będzie powiedzieć "jestem zamykany".
- Pozostałe dwie akcje są typu `END_SESSION`.
- Akcja END_SESSION wskazuje na ConversationLearner, że konwersacja została ukończona.

### <a name="overall-bot-logic"></a>Ogólna logika bot

Najpierw zobaczysz, że jeśli `usingConversationLearner` Flaga stanu bot została ustawiona, przejdziemy do Conversation Learner. Jeśli nie, przejdziemy do innej kontrolki.  W tym przykładzie przedstawiono proste dopasowanie tekstu, ale może to być jakakolwiek inna technologia bot, w tym LUIS, QnA Maker, a nawet inne wystąpienie Conversation Learner.

Potrzebujemy, aby użytkownik mógł otworzyć i zamknąć sklep, więc porównujemy ciąg z "otwartym magazynem" i "Zamknij magazyn" i ustawić flagę "storeIsOpen".

Następnie potrzebujemy metody wyzwalania kontroli nad naszym modelem Conversation Learner. W przypadku dopasowania do ciągu "Shop" wykonaj następujące czynności:
- `usingConversationLearner` Ustaw flagę w pamięci bot.
- Wywołaj metodę "StartSession" w naszym modelu Conversation Learner.  Spowoduje to wyzwolenie elementu "onSessionStartCallback", który `isOpen` spowoduje zainicjowanie wartości jednostki

Zobacz poniżej:

![](../media/tutorial17_useConversationLearner.PNG)

Wprowadzamy również dopasowanie tekstu do "historii", co spowoduje wyświetlenie tego ostatniego elementu zakupu.
Na koniec, jeśli zostanie wpisana jakakolwiek inna wartość, zostaną wyświetlone dostępne polecenia użytkownika

## <a name="train-dialog"></a>Okno dialogowe uczenia

Na potrzeby tego samouczka model jest już wstępnie przeszkolony.  Będziemy testować pełną bot, aby zobaczyć efekt wywołania zwrotnego sesji startowej i końcowej w praktyce.

## <a name="testing-the-bot"></a>Testowanie bot

W przeciwieństwie do modelu Lean Manufacturing botów, nie będzie możliwe przetestowanie go w interfejsie użytkownika Conversation Learner, ponieważ może on tylko zobaczyć, co jest obsługiwane przez model Conversation Learner.

### <a name="install-the-bot-framework-emulator"></a>Instalowanie emulatora struktury bot Framework

- Przejdź do obszaru [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator) (Ustawienia — Integracje i usługi).
- Pobierz i zainstaluj emulator.

### <a name="configure-the-emulator"></a>Konfigurowanie emulatora

- Otwórz emulator i upewnij się, że adres URL jest przeznaczony dla tego samego portu, na którym uruchomiono bot. Możliwe`http://localhost:3978/api/messages`

### <a name="test"></a>Testowanie 

#### <a name="scenario-1-store-is-closed"></a>Scenariusz 1: Magazyn jest zamknięty
1. Wprowadź "Shop". Jest to obsługiwane przez dopasowanie tekstu i nadaje formantowi Conversation Learner model.
2. Wprowadź "Hello".  Ponieważ `isOpen` wartość nie jest ustawiona, bot będzie powiedzieć "jestem zamykany" i zakończyć sesję.

#### <a name="scenario-2-store-is-open"></a>Scenariusz 2: Magazyn jest otwarty
1. Wprowadź "Otwórz sklep".  Spowoduje to ustawienie `isOpen` wartości true.
1. Wprowadź "Shop".
1. Wprowadź "Hello".  Ponieważ `isOpen` wartość jest równa true, bot będzie powiedzieć "co chcesz kupić?"
1. Wprowadź "krzesło". "krzesło" zostanie zapisane w pamięci CL jako jednostka `purchaseItem`. Wywołanie zwrotne sesji końcowej jest wywoływane, która kopiuje tę wartość do magazynu konwersacji.
1. Wprowadź "History".  Bot będzie powiedzieć "krzesło zakupione" jako ostatnie `purchaseItem`.

## <a name="conclusion"></a>Wniosek

Korzystając z informacji podanych powyżej, powinno być możliwe łączenie Conversation Learner z dowolną inną technologią tworzenia bot.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Rozgałęzianie i cofanie](./17-branch-undo.md)
