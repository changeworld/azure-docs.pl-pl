---
title: Dodawanie chit-chat do bazy wiedzy QnA Maker
titleSuffix: Azure Cognitive Services
description: Dodanie osobistego chit-chat do bota sprawia, że bardziej konwersacyjne i wciągające podczas tworzenia KB. QnA Maker pozwala łatwo dodać wstępnie wypełniony zestaw najlepszych chit-chat, do bazy wiedzy.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: a9a14056e6be62fc1c1b5e542c1a3acceb738eac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220711"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>Dodawanie Chit-chat do bazy wiedzy

Dodawanie chit-chat do bota sprawia, że bardziej konwersacyjne i wciągające. Funkcja chit-chat w kreatorze QnA pozwala łatwo dodać wstępnie wypełniony zestaw najlepszych chit-chat, do bazy wiedzy (KB). Może to być punkt wyjścia dla osobowości twojego bota, a to pozwoli Ci zaoszczędzić czas i koszty pisania ich od podstaw.  

Ten zestaw danych ma około 100 scenariuszy chit-chat w głosie wielu personas, takich jak Professional, Friendly i Witty. Wybierz persona, która najbardziej przypomina głos twojego bota. Biorąc pod uwagę zapytanie użytkownika, QnA Maker próbuje dopasować go do najbliższego znanego chit-chat QnA.  

Poniżej znajdują się przykłady różnych osobowości. Możesz zobaczyć wszystkie [zbiory danych](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets) osobowości wraz ze szczegółami osobowości.

Dla zapytania użytkownika `When is your birthday?`, każda osobowość ma stylową odpowiedź:

<!-- added quotes so acrolinx doesn't score these sentences -->
|Osobowości|Przykład|
|--|--|
|Professional Edition|Wiek tak naprawdę nie odnosi się do mnie.|
|Przyjazny|Naprawdę nie mam wieku.|
|Dowcipny|Jestem wolny od wieku.|
|Opiekuńczy|Nie mam wieku.|
|Entuzjastycznie|Jestem botem, więc nie mam wieku.|
||


## <a name="language-support"></a>Obsługa języków

Zestawy danych chit-chat są obsługiwane w następujących językach:

|Język|
|--|
|Chiński|
|Polski|
|Francuski|
|Niemcy|
|Włoski|
|Japoński|
|Koreański|
|Portugalski|
|Hiszpański|


## <a name="add-chit-chat-during-kb-creation"></a>Dodawanie chit-chat podczas tworzenia bazy wiedzy
Podczas tworzenia bazy wiedzy, po dodaniu źródłowych adresów URL i plików, istnieje możliwość dodania chit-chat. Wybierz osobowość, którą chcesz jako bazę chit-chat. Jeśli nie chcesz dodawać chit-chat, lub jeśli masz już wsparcie chit-chat w źródłach danych, wybierz **brak**. 

## <a name="add-chit-chat-to-an-existing-kb"></a>Dodawanie chit-chat do istniejącej bazy wiedzy
Wybierz swoją bazę wiedzy i przejdź do strony **Ustawienia.** Istnieje link do wszystkich zestawów danych chit-chat w odpowiednim formacie **.tsv.** Pobierz odpowiednią osobowość, a następnie prześlij ją jako źródło pliku. Upewnij się, że nie edytujesz formatu ani metadanych podczas pobierania i przekazywania pliku. 
  
![Dodawanie chit-chat do istniejącej bazy wiedzy](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

## <a name="edit-your-chit-chat-questions-and-answers"></a>Edytuj swoje pytania i odpowiedzi na chit-chat
Podczas edytowania bazy wiedzy zostanie wyświetlene nowe źródło czatu chit-chat, w zależności od wybranej osobowości. Teraz możesz dodawać zmienione pytania lub edytować odpowiedzi, tak jak w przypadku każdego innego źródła. 

![Edycja chit-chat QnAs](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

Aby wyświetlić metadane, wybierz pozycję **Opcje widoku** na pasku narzędzi, a następnie wybierz pozycję **Pokaż metadane**.

## <a name="add-additional-chit-chat-questions-and-answers"></a>Dodawanie dodatkowych pytań i odpowiedzi na chit-chat
Możesz dodać nowy chit-chat QnA, który nie znajduje się w wstępnie zdefiniowanym zestawie. Upewnij się, że nie duplikujesz pary QnA, która jest już ujęta w zestawie chit-chat. Po dodaniu nowego chit-chat QnA, zostanie on dodany do źródła **redakcyjnego.** Aby upewnić się, że ranker rozumie, że jest to chit-chat, dodaj parę klucz/wartość metadanych "Editorial: chitchat", jak widać na poniższej ilustracji:
   
![! [Dodaj chit-chat QnAs] (.. /media/qnamaker-how-to-chit-chat/add-new-chit-chat.png)](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png#lightbox)

## <a name="delete-chit-chat-from-an-existing-kb"></a>Usuwanie chit-chat z istniejącej bazy wiedzy
Wybierz swoją bazę wiedzy i przejdź do strony **Ustawienia.** Twoje konkretne źródło chit-chat jest wyświetlane jako plik z wybraną nazwą osobowości. Można go usunąć jako plik źródłowy.

![Usuń chit-chat z KB](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Importowanie bazy wiedzy](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>Zobacz też 

[Omówienie usługi QnA Maker](../Overview/overview.md)
