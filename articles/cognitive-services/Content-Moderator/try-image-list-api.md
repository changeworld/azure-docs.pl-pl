---
title: Umiarkowany obrazów przy użyciu niestandardowych list Konsola interfejsu API — Content Moderator
titlesuffix: Azure Content Moderator
description: Testuj niestandardowych list obrazów w konsoli usługi Content Moderator interfejsu API.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: 5e573262879b6a16e3cd530757900b86228ae299
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219961"
---
# <a name="moderate-with-custom-image-lists-in-the-api-console"></a>Moderowanie przy użyciu niestandardowych list obrazów w konsoli interfejsu API

Możesz użyć [interfejsu API zarządzania listy](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672) w usłudze Azure Content Moderator do tworzenia niestandardowych list obrazów. Za pomocą interfejsu API moderowania obrazów niestandardowych list obrazów. Operacja moderowania obrazów ocenia obrazu. Jeśli tworzysz niestandardowych list, operacja również porównuje go obrazy z list niestandardowych. Niestandardowe listy umożliwia blokowanie lub zezwalanie na obrazie.

> [!NOTE]
> Istnieje maksymalny limit wynoszący **5 image list** z każdej listy **nie może przekraczać 10 000 obrazów**.
>

Interfejs API zarządzania lista umożliwia wykonywanie następujących zadań:

- Utwórz listę.
- Dodawanie obrazów do listy.
- Obrazy ekranu względem obrazów na liście.
- Usuwanie obrazów z listy.
- Usuwanie listy.
- Edytuj informacje na liście.
- Odśwież indeks, aby zmiany na liście znajdują się w nowe skanowanie.

## <a name="use-the-api-console"></a>Użyj konsoli interfejsu API
Zanim można Testuj interfejs API w konsoli usługi online, należy się klucz subskrypcji. Ten element znajduje się na **ustawienia** na karcie **Ocp-Apim-Subscription-Key** pole. Aby uzyskać więcej informacji, zobacz [Przegląd](overview.md).

## <a name="refresh-search-index"></a>Odśwież indeksu wyszukiwania

Po wprowadzeniu zmian do listy obrazów należy odświeżyć jej indeks zmian, które mają zostać uwzględnione w czasie przyszłych skanowań. Ten krok jest podobny do sposobu wyszukiwarki na pulpicie systemu Windows (jeśli jest włączone) lub aparatu wyszukiwania w sieci web stale odświeża jej indeks w celu uwzględnienia nowych plików lub stron.

1.  W [dokumentacja interfejsu API zarządzania listy obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672), w menu po lewej stronie wybierz **listy obrazów**, a następnie wybierz pozycję **Odśwież indeksu wyszukiwania**.

  **Listy obrazów — odświeżanie indeksu wyszukiwania** zostanie otwarta strona.

2. Aby uzyskać **konsoli testowania interfejsu Open API**, wybierz region, który najlepiej opisuje Twojej lokalizacji. 
 
    ![Listy obrazów — pole region strony indeksu wyszukiwania odświeżania](images/test-drive-region.png)

    **Listy obrazów — odświeżanie indeksu wyszukiwania** zostanie otwarta konsola interfejsu API.

3.  W **listId** wprowadź identyfikator listy. Wprowadź klucz subskrypcji, a następnie wybierz pozycję **wysyłania**.

  ![Listy obrazów — pola zawartości indeksu wyszukiwania odświeżania konsoli odpowiedzi](images/try-image-list-refresh-1.png)


## <a name="create-an-image-list"></a>Tworzenie listy obrazów

1.  Przejdź do [dokumentacja interfejsu API zarządzania listy obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672).

  **Listy obrazów — tworzenie** zostanie otwarta strona. 

3.  Aby uzyskać **konsoli testowania interfejsu Open API**, wybierz region, który najlepiej opisuje Twojej lokalizacji.

    ![Obraz listy — Tworzenie strony pole region](images/test-drive-region.png)

    **Listy obrazów — tworzenie** zostanie otwarta konsola interfejsu API.
 
4.  W **Ocp-Apim-Subscription-Key** wprowadź swój klucz subskrypcji.

5.  W **treść żądania** wprowadź wartości dla **nazwa** (na przykład MyList) i **opis**.

  ![Obraz listy — tworzenie nazwa treści żądania w konsoli i opis](images/try-terms-list-create-1.png)

6.  Użyj symboli zastępczych pary klucz wartość, aby przypisać bardziej opisowe metadanych do listy.

        {
           "Name": "MyExclusionList",
           "Description": "MyListDescription",
           "Metadata": 
           {
             "Category": "Competitors",
             "Type": "Exclude"
           }
        }

  Dodaj metadane listy jako pary klucz wartość, a nie rzeczywiste obrazów.
 
7.  Wybierz pozycję **Wyślij**. Na liście jest tworzony. Uwaga **identyfikator** wartość, która jest skojarzona z nową listę. Ten identyfikator będzie potrzebny do innych funkcji zarządzania listy obrazów.

  ![Obraz listy — Tworzenie konsoli odpowiedzi na zawartości pola zawiera identyfikator listy](images/try-terms-list-create-2.png)
 
8.  Następnie dodaj obrazów do MyList. W menu po lewej stronie wybierz **obraz**, a następnie wybierz pozycję **Dodaj obraz**.

  **Obrazu — Dodaj obraz** zostanie otwarta strona. 

9. Aby uzyskać **konsoli testowania interfejsu Open API**, wybierz region, który najlepiej opisuje Twojej lokalizacji.

  ![Obraz — Dodawanie wybór regionu strony obrazu](images/test-drive-region.png)

  **Obrazu — Dodaj obraz** zostanie otwarta konsola interfejsu API.
 
10. W **listId** wprowadź identyfikator listy, który został wygenerowany, a następnie wprowadź adres URL obrazu, który chcesz dodać. Wprowadź klucz subskrypcji, a następnie wybierz pozycję **wysyłania**.

11. Aby sprawdzić, czy obraz został dodany do listy, w menu po lewej stronie wybierz pozycję **obraz**, a następnie wybierz pozycję **Pobierz wszystkie identyfikatory obraz**.

  **Obraz — Pobierz wszystkie identyfikatory obraz** zostanie otwarta konsola interfejsu API.
  
12. W **listId** wprowadź identyfikator listy, a następnie wprowadź klucz subskrypcji. Wybierz pozycję **Wyślij**.

  ![Obraz — wszystkie identyfikatory obraz Get konsoli pola zawartości zawiera listę obrazów, które zostały wprowadzone odpowiedzi](images/try-image-list-create-11.png)
 
10. Dodaj kilka więcej obrazów. Teraz, po utworzeniu niestandardowej listy obrazów, spróbuj [oceny obrazów](try-image-api.md) przy użyciu listy obrazu niestandardowego. 

## <a name="delete-images-and-lists"></a>Usuwanie obrazów i listy

Usuwanie obrazu lub listy jest bardzo proste. Interfejs API umożliwia wykonywanie następujących zadań:

- Usuwanie obrazu. (**Obrazu — Usuń**)
- Usuń wszystkie obrazy znajdujące się na liście, bez usuwania listy. (**Obrazu — Usuń wszystkie obrazy**)
- Usuń listę i całą jego zawartość. (**List obrazów — Usuń**)

W tym przykładzie Usuwa pojedynczy obraz:

1. W [dokumentacja interfejsu API zarządzania listy obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672), w menu po lewej stronie wybierz **obraz**, a następnie wybierz pozycję **Usuń**. 

  **Obrazu — Usuń** zostanie otwarta strona.

2. Aby uzyskać **konsoli testowania interfejsu Open API**, wybierz region, który najlepiej opisuje Twojej lokalizacji. 

  ![Obraz — pole region strony Delete](images/test-drive-region.png)
 
  **Obrazu — Usuń** zostanie otwarta konsola interfejsu API.
 
3.  W **listId** wprowadź identyfikator listy do usunięcia z obrazu.  Jest to liczba zwracanych w **obraz — Pobierz wszystkie identyfikatory obraz** konsoli MyList. Następnie wprowadź **ImageId** obrazu do usunięcia. 

W naszym przykładzie jest identyfikator listy **58953**, wartość **ContentSource**. Obraz, który jest Identyfikatorem **59021**, wartość **ContentIds**.

4.  Wprowadź klucz subskrypcji, a następnie wybierz pozycję **wysyłania**.

5.  Aby sprawdzić, że obraz został usunięty, użyj **obraz — Pobierz wszystkie identyfikatory obraz** konsoli.
 
## <a name="change-list-information"></a>Zmień informacje dotyczące listy

Możesz edytować nazwę i opis listy i dodawania elementów metadanych.

1.  W [dokumentacja interfejsu API zarządzania listy obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672), w menu po lewej stronie wybierz **listy obrazów**, a następnie wybierz pozycję **szczegóły aktualizacji**. 

  **Listy obrazów — informacje na temat aktualizacji** zostanie otwarta strona.

2. Aby uzyskać **konsoli testowania interfejsu Open API**, wybierz region, który najlepiej opisuje Twojej lokalizacji.  

    ![Listy obrazów — pole region strony Szczegóły aktualizacji](images/test-drive-region.png)

    **Listy obrazów — informacje na temat aktualizacji** zostanie otwarta konsola interfejsu API.
 
3.  W **listId** wprowadź identyfikator listy, a następnie wprowadź klucz subskrypcji.

4.  W **treść żądania** , edytować, a następnie wybierz **wysyłania** przycisk na stronie.

  ![Listy obrazów — informacje na temat aktualizacji konsoli edycji treść żądania](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Kolejne kroki

W kodzie za pomocą interfejsu API REST lub rozpoczynać się [obraz zawiera przewodnik Szybki Start .NET](image-lists-quickstart-dotnet.md) do integracji z aplikacją.
