---
title: Utwórz nową aplikację z LUIS | Dokumentacja firmy Microsoft
description: Utwórz i Zarządzaj aplikacjami na stronie sieci Web opis języka (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 04/17/2018
ms.author: v-geberr
ms.openlocfilehash: 75edd39346995cdef72bb1e1fcb9eaff53d29702
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349788"
---
# <a name="create-an-app"></a>Tworzenie aplikacji
Możesz utworzyć nową aplikację na różne sposoby: 

* [Uruchom](#create-new-app) z pustą aplikację i Utwórz lokalizacji docelowych, zniesławiających i jednostek.
* [Uruchom](#create-new-app) z pustą aplikację i Dodaj [wbudowane domeny](luis-how-to-use-prebuilt-domains.md).
* [Importuj aplikację LUIS](#import-new-app) z pliku JSON, który zawiera już opcji, zniesławiających i jednostek.

## <a name="what-is-an-app"></a>Co to jest aplikacja
Ta aplikacja zawiera [wersji](luis-how-to-manage-versions.md) modelu, jak również żadnego [współpracownicy](luis-how-to-collaborate.md) dla aplikacji. Podczas tworzenia aplikacji wybierz kultury ([języka](luis-supported-languages.md)) który **nie można zmienić później**. 

Domyślna wersja nowej aplikacji jest "0,1." 

Można tworzyć i zarządzać aplikacjami na **Moje aplikacje** strony. Można zawsze dostęp do tej strony, wybierając **Moje aplikacje** na górnym pasku nawigacyjnym z [LUIS](luis-reference-regions.md) witryny sieci Web. 

[![](media/luis-create-new-app/apps-list.png "Zrzut ekranu przedstawiający listę aplikacji")](media/luis-create-new-app/apps-list.png#lightbox)

## <a name="create-new-app"></a>Utwórz nową aplikację

1. Na **Moje aplikacje** wybierz pozycję **Utwórz nową aplikację**.
2. W oknie dialogowym nazwy aplikacji "TravelAgent".

    ![Tworzenie nowej aplikacji okna dialogowego](./media/luis-create-new-app/create-app.png)

3. Wybierz kultury Twojej aplikacji (dla aplikacji TravelAgent, wybierz język angielski), a następnie wybierz **gotowe**. 

    >[!NOTE]
    >Kultura nie można zmienić po utworzeniu aplikacji. 

## <a name="import-new-app"></a>Importuj nowej aplikacji
Można ustawić nazwę (50 char max), wersji (10 char max) i opis aplikacji w pliku JSON. Przykłady pliki JSON aplikacji są dostępne pod adresem [przykłady LUIS](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/Examples-BookFlight).

1. Na **Moje aplikacje** wybierz pozycję **importu nowej aplikacji**.
2. W **importu nowej aplikacji** okno dialogowe, wybierz plik JSON definiujący LUIS aplikacji.

    ![Importuj nowe okno aplikacji](./media/luis-create-new-app/import-app.png)

## <a name="export-app"></a>Eksportowanie aplikacji
1. Na **Moje aplikacje** wybierz wielokropek (...) na końcu wiersza aplikacji.

    [![](media/luis-create-new-app/apps-list.png "Zrzut ekranu przedstawiający okno podręczne akcji dla aplikacji")](media/luis-create-new-app/three-dots.png#lightbox)

2. Wybierz **aplikacji eksportu** z menu. 

## <a name="rename-app"></a>Zmień nazwę aplikacji

1. Na **Moje aplikacje** wybierz wielokropek (...) na końcu wiersza aplikacji. 
2. Wybierz **zmienić** z menu.
3. Wprowadź nową nazwę aplikacji i wybierz **gotowe**.

## <a name="delete-app"></a>Usuwanie aplikacji

> [!CAUTION]
> W przypadku usuwania aplikacji dla wszystkich współpracowników i właściciela. [Eksportuj](#export-app) aplikacji przed jego usunięciem. 

1. Na **Moje aplikacje** wybierz wielokropek (...) na końcu wiersza aplikacji. 
2. Wybierz **usunąć** z menu.
3. Wybierz **Ok** w oknie potwierdzenia.

## <a name="export-endpoint-logs"></a>Eksportuj dzienniki punktu końcowego
Dzienniki zawiera zapytania, czasu UTC, a odpowiedź LUIS JSON.

1. Na **Moje aplikacje** wybierz wielokropek (...) na końcu wiersza aplikacji. 
2. Wybierz **Eksportowały dzienniki punktu końcowego** z menu.

```
Query,UTC DateTime,Response
text i'm driving and will be 30 minutes late to the meeting,02/13/2018 15:18:43,"{""query"":""text I'm driving and will be 30 minutes late to the meeting"",""intents"":[{""intent"":""None"",""score"":0.111048922},{""intent"":""SendMessage"",""score"":0.987501}],""entities"":[{""entity"":""i ' m driving and will be 30 minutes late to the meeting"",""type"":""Message"",""startIndex"":5,""endIndex"":58,""score"":0.162995353}]}"
```

## <a name="next-steps"></a>Kolejne kroki

Pierwsze zadanie w aplikacji jest [dodać intencje](luis-how-to-add-intents.md).