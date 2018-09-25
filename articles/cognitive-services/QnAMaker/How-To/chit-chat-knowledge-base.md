---
title: Dodawanie chit czatu do wiedzy usługi QnA Maker
titleSuffix: Azure Cognitive Services
description: Dodawanie osobistych rozmowy chit do bota sprawia, że konwersacji i bardziej angażujące kiedy tworzysz bazę wiedzy. Usługa QnA Maker umożliwia łatwe dodawanie wstępnie wypełnionych zbiór najważniejsze rozmowy chit, do wiedzy. To jest punkt wyjścia dla czatu internetowego chit Twój bot i zaoszczędzić czas i pieniądze, zapisywania ich od podstaw.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/24/2018
ms.author: tulasim
ms.openlocfilehash: 6f542d095211242fcf6dcb13d4426534e32e57ab
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47041731"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>Dodaj Chit czatu do bazy wiedzy

Dodawanie chit czatu do bota sprawia, że konwersacji i bardziej interesujące. Funkcja chit rozmowy w usługi QnA maker umożliwia łatwe dodawanie wstępnie wypełnionych zbiór najważniejsze rozmowy chit, do bazy wiedzy (KB). Może to być punkt wyjścia do osobowości Twój bot, a jego pozwoli zaoszczędzić czas i pieniądze, zapisywania ich od podstaw.  

Ten zestaw danych zawiera około 100 scenariusze dotyczące czacie chit przy użyciu głosu trzech osób - Comic Professional, przyjazne. Wybierz osoby, który najbardziej przypomina Twój bot głosu. Podana kwerenda użytkownika, usługa QnA Maker próbuje dopasować ją z najbliższego QnA czatu internetowego chit znane. 

Kilka przykładów różnych osobowości: <!-- added quotes so acrolinx doesn't score these sentences -->
|Kwerenda użytkownika|Professional|Zaprzyjaźnionej|Comic|
|--|--|--|--|
|`You are awesome`|`I aim to serve.`|`That's so nice of you!`|`Flattery. I like it.`|
|`Are you hungry?`|`I don't need to eat.`|`I only do food for thought.`|`Eating would require a lot of things I don't have. Like a digestive system. And silverware.`|
|`Sing a song`|`I'm afraid I'm not musically inclined.`|`La la la, tra la la. I'm awesome at this.`|`You can't handle my dulcet tones.`|
|`Who made you?`|`There wouldn't be time to list everyone.`|`So many people!`|`Nerds.`|


> [!NOTE]
> Obsługa chit rozmowy jest obecnie dostępna w języku angielskim tylko. 

## <a name="add-chit-chat-during-kb-creation"></a>Dodawanie rozmowy chit podczas tworzenia bazy wiedzy
Podczas tworzenia bazy wiedzy knowledge base, po dodaniu usługi źródłowy adres URL i plików jest opcja dodawania chit rozmowy. Wybierz użytkownika, który jako podstawa chit rozmowy. Jeśli nie chcesz dodać chit rozmowy lub jeśli masz już czatu internetowego chit obsługi w źródłach danych wybierz **Brak**. 
   
![Dodaj rozmowy chit podczas tworzenia](../media/qnamaker-how-to-chit-chat/create-kb-chit-chat.png)

## <a name="add-chit-chat-to-an-existing-kb"></a>Dodaj Chit czatu do istniejącej bazy wiedzy
Wybierz wiedzy, a następnie przejdź do **ustawienia** strony. Znajduje się link do wszystkich zestawów rozmowy chit danych w odpowiedniej **tsv** formatu. Osobowości, które chcesz pobrać, a następnie przekaż go jako źródło pliku. Upewnij się, że nie Edytuj format lub metadanych przy pobieraniu i przekazać plik. 
  
![Dodaj chit czatu do istniejącej bazy wiedzy](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

## <a name="edit-your-chit-chat-questions-and-answers"></a>Edytuj swojej rozmowy chit pytań i odpowiedzi
Podczas edytowania wiedzy, zobaczysz nowe źródło dla chit czatu internetowego, oparte na użytkownika, które wybrano. Teraz można dodać, zmienić pytania lub edytowania odpowiedzi, podobnie jak za pomocą dowolnego innego źródła. 

![Edytuj znacznie chit rozmowy](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

## <a name="add-additional-chit-chat-questions-and-answers"></a>Dodaj dodatkowe rozmowy chit pytań i odpowiedzi
Możesz dodać nowe rozmowy chit pytań i odpowiedzi, który nie jest wstępnie zdefiniowane zbioru. Upewnij się, że nie są duplikowania parą pytań i odpowiedzi, która jest już omówione w zestawie chit rozmowy. Po dodaniu żadnych nowych pytań i odpowiedzi czatu chit, pobiera ono dodane do Twojego **redakcyjnych** źródła. Aby upewnić się, oceniania rozumie się to chit rozmowy, Dodaj parę klucza i wartości metadanych "redakcyjnych: Rozmowa chit", jak pokazano na poniższej ilustracji:
   
![Dodaj znacznie chit rozmowy](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png)

## <a name="delete-chit-chat-from-an-existing-kb"></a>Usuń chit rozmowy z istniejącej bazy wiedzy
Wybierz wiedzy, a następnie przejdź do **ustawienia** strony. Źródła określonego rozmowy chit znajduje się w pliku o nazwie wybranego użytkownika. Możesz usunąć to, co plik źródłowy.

![Usuń chit rozmowy z bazy wiedzy](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Importowanie bazy wiedzy](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>Zobacz także 

[Omówienie usługi QnA Maker](../Overview/overview.md)
