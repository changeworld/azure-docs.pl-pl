---
title: Tworzenie nowej aplikacji z użyciem usługi LUIS | Dokumentacja firmy Microsoft
description: Utwórz aplikacje i zarządzaj nimi na stronie sieci Web Language Understanding (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 04/17/2018
ms.author: v-geberr
ms.openlocfilehash: 998a85720f5707fbf6ed4c5cfa3ed0dab5d1cc0e
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37865049"
---
# <a name="create-an-app"></a>Tworzenie aplikacji
Możesz utworzyć nową aplikację na różne sposoby: 

* [Rozpocznij](#create-new-app) z pustą aplikację i utworzyć intencji, wypowiedzi i jednostek.
* [Rozpocznij](#create-new-app) z pustą aplikacją i Dodaj [ze wstępnie utworzonych domen](luis-how-to-use-prebuilt-domains.md).
* [Importowanie aplikacji usługi LUIS](#import-new-app) z pliku JSON, który zawiera już intencji, wypowiedzi i jednostek.

## <a name="what-is-an-app"></a>Co to jest aplikacja
Ta aplikacja zawiera [wersji](luis-how-to-manage-versions.md) modelu, a także dowolnego [współpracowników](luis-how-to-collaborate.md) dla aplikacji. Podczas tworzenia aplikacji wybierz kulturę ([języka](luis-supported-languages.md)) który **nie można później zmienić**. 

Domyślna wersja nowej aplikacji jest "0.1". 

Można tworzyć i zarządzanie aplikacjami na **Moje aplikacje** strony. Są zawsze dostępne na tej stronie, wybierając **Moje aplikacje** na górnym pasku nawigacyjnym z [LUIS](luis-reference-regions.md) witryny sieci Web. 

[![](media/luis-create-new-app/apps-list.png "Zrzut ekranu przedstawiający listę aplikacji")](media/luis-create-new-app/apps-list.png#lightbox)

## <a name="create-new-app"></a>Tworzenie nowej aplikacji

1. Na **Moje aplikacje** wybierz opcję **Utwórz nową aplikację**.
2. W oknie dialogowym Nazwa aplikacji "TravelAgent".

    ![Utwórz nowe okno dialogowe aplikacji](./media/luis-create-new-app/create-app.png)

3. Wybierz Twojej kulturze aplikacji (TravelAgent aplikacji, wybierz język angielski), a następnie wybierz pozycję **gotowe**. 

    >[!NOTE]
    >Nie można zmienić kulturę, gdy aplikacja zostanie utworzona. 

## <a name="import-new-app"></a>Importuj nowej aplikacji
Nazwa (50 char max), wersja (10 char max) i opis aplikacji można ustawić w pliku JSON. Przykładowe pliki JSON aplikacji są dostępne pod adresem [przykłady LUIS](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/Examples-BookFlight).

1. Na **Moje aplikacje** wybierz opcję **importowania Nowa aplikacja**.
2. W **importowania Nowa aplikacja** okno dialogowe, wybierz plik JSON definiujący aplikacji usługi LUIS.

    ![Importuj nowe okno dialogowe aplikacji](./media/luis-create-new-app/import-app.png)

## <a name="export-app"></a>Eksportowanie aplikacji
1. Na **Moje aplikacje** stronie, wybierz przycisk wielokropka (***...*** ) na końcu wiersza aplikacji.

    [![](media/luis-create-new-app/apps-list.png "Zrzut ekranu przedstawiający wyskakujące okno dialogowe akcji dla aplikacji")](media/luis-create-new-app/three-dots.png#lightbox)

2. Wybierz **eksportowania aplikacji** z menu. 

## <a name="rename-app"></a>Zmień nazwę aplikacji

1. Na **Moje aplikacje** stronie, wybierz przycisk wielokropka (***...*** ) na końcu wiersza aplikacji. 
2. Wybierz **Zmień nazwę** z menu.
3. Wprowadź nową nazwę aplikacji, a następnie wybierz pozycję **gotowe**.

## <a name="delete-app"></a>Usuwanie aplikacji

> [!CAUTION]
> Usuwasz aplikację dla wszystkich współpracowników i właściciela. [Eksportuj](#export-app) aplikacji przed jej usunięciem. 

1. Na **Moje aplikacje** stronie, wybierz przycisk wielokropka (***...*** ) na końcu wiersza aplikacji. 
2. Wybierz **Usuń** z menu.
3. Wybierz **Ok** w oknie potwierdzenia.

## <a name="export-endpoint-logs"></a>Eksportuj dzienniki punktu końcowego
Dzienniki zawierają zapytanie, czasem UTC i odpowiedź JSON usługi LUIS.

1. Na **Moje aplikacje** stronie, wybierz przycisk wielokropka (***...*** ) na końcu wiersza aplikacji. 
2. Wybierz **Eksportuj dzienniki punktu końcowego** z menu.

```
Query,UTC DateTime,Response
text i'm driving and will be 30 minutes late to the meeting,02/13/2018 15:18:43,"{""query"":""text I'm driving and will be 30 minutes late to the meeting"",""intents"":[{""intent"":""None"",""score"":0.111048922},{""intent"":""SendMessage"",""score"":0.987501}],""entities"":[{""entity"":""i ' m driving and will be 30 minutes late to the meeting"",""type"":""Message"",""startIndex"":5,""endIndex"":58,""score"":0.162995353}]}"
```

## <a name="next-steps"></a>Kolejne kroki

Pierwsze zadanie w aplikacji jest [Dodawanie intencji](luis-how-to-add-intents.md).