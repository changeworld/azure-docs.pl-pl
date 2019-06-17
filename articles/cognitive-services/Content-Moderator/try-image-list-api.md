---
title: Umiarkowany obrazów z list niestandardowych i konsola interfejsu API — Content Moderator
titlesuffix: Azure Content Moderator
description: Użyjesz interfejsu API zarządzania listy w usłudze Azure Content Moderator do tworzenia niestandardowych list obrazów.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 7efa5114a903ba88010ec44f2f1038331df62948
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62097984"
---
# <a name="moderate-with-custom-image-lists-in-the-api-console"></a>Moderowanie przy użyciu niestandardowych list obrazów w konsoli interfejsu API

Możesz użyć [interfejsu API zarządzania listy](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672) w usłudze Azure Content Moderator do tworzenia niestandardowych list obrazów. Za pomocą interfejsu API moderowania obrazów niestandardowych list obrazów. Operacja moderowania obrazów ocenia obrazu. Jeśli tworzysz niestandardowych list, operacja również porównuje go obrazy z list niestandardowych. Niestandardowe listy umożliwia blokowanie lub zezwalanie na obrazie.

> [!NOTE]
> Istnieje maksymalny limit wynoszący **5 list obrazów**, a poszczególne listy **nie mogą przekraczać 10 000 obrazów**.
>

Interfejs API zarządzania lista umożliwia wykonywanie następujących zadań:

- Tworzenie listy.
- Dodawanie obrazów do listy.
- Obrazy ekranu względem obrazów na liście.
- Usuwanie obrazów z listy.
- Usuwanie listy.
- Edycja informacji na liście.
- Odświeżanie indeksu, aby zmiany na liście były uwzględnione w nowym skanowaniu.

## <a name="use-the-api-console"></a>Użyj konsoli interfejsu API
Zanim można Testuj interfejs API w konsoli usługi online, należy się klucz subskrypcji. Ten element znajduje się na **ustawienia** na karcie **Ocp-Apim-Subscription-Key** pole. Aby uzyskać więcej informacji, zobacz [Omówienie](overview.md).

## <a name="refresh-search-index"></a>Odśwież indeksu wyszukiwania

Po wprowadzeniu zmian do listy obrazów należy odświeżyć jej indeks zmian, które mają zostać uwzględnione w czasie przyszłych skanowań. Ten krok jest podobny do sposobu wyszukiwarki na pulpicie systemu Windows (jeśli jest włączone) lub aparatu wyszukiwania w sieci web stale odświeża jej indeks w celu uwzględnienia nowych plików lub stron.

1. W [dokumentacja interfejsu API zarządzania listy obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672), w menu po lewej stronie wybierz **listy obrazów**, a następnie wybierz pozycję **Odśwież indeksu wyszukiwania**.

   **Listy obrazów — odświeżanie indeksu wyszukiwania** zostanie otwarta strona.

2. Aby uzyskać **konsoli testowania interfejsu Open API**, wybierz region, który najlepiej opisuje Twojej lokalizacji. 
 
    ![Listy obrazów — pole region strony indeksu wyszukiwania odświeżania](images/test-drive-region.png)

    **Listy obrazów — odświeżanie indeksu wyszukiwania** zostanie otwarta konsola interfejsu API.

3. W **listId** wprowadź identyfikator listy. Wprowadź klucz subskrypcji, a następnie wybierz pozycję **wysyłania**.

   ![Listy obrazów — pola zawartości indeksu wyszukiwania odświeżania konsoli odpowiedzi](images/try-image-list-refresh-1.png)


## <a name="create-an-image-list"></a>Tworzenie listy obrazów

1. Przejdź do [dokumentacja interfejsu API zarządzania listy obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672).

   **Listy obrazów — tworzenie** zostanie otwarta strona. 

3. Aby uzyskać **konsoli testowania interfejsu Open API**, wybierz region, który najlepiej opisuje Twojej lokalizacji.

   ![Obraz listy — Tworzenie strony pole region](images/test-drive-region.png)

   **Listy obrazów — tworzenie** zostanie otwarta konsola interfejsu API.
 
4. W **Ocp-Apim-Subscription-Key** wprowadź swój klucz subskrypcji.

5. W **treść żądania** wprowadź wartości dla **nazwa** (na przykład MyList) i **opis**.

   ![Obraz listy — tworzenie nazwa treści żądania w konsoli i opis](images/try-terms-list-create-1.png)

6. Użyj symboli zastępczych pary klucz wartość, aby przypisać bardziej opisowe metadanych do listy.

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
 
7. Wybierz pozycję **Wyślij**. Na liście jest tworzony. Uwaga **identyfikator** wartość, która jest skojarzona z nową listę. Ten identyfikator będzie potrzebny do innych funkcji zarządzania listy obrazów.

   ![Obraz listy — Tworzenie konsoli odpowiedzi na zawartości pola zawiera identyfikator listy](images/try-terms-list-create-2.png)
 
8. Następnie dodaj obrazów do MyList. W menu po lewej stronie wybierz **obraz**, a następnie wybierz pozycję **Dodaj obraz**.

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
- Usuwanie listy i całej jej zawartości. (**List obrazów — Usuń**)

W tym przykładzie Usuwa pojedynczy obraz:

1. W [dokumentacja interfejsu API zarządzania listy obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672), w menu po lewej stronie wybierz **obraz**, a następnie wybierz pozycję **Usuń**. 

   **Obrazu — Usuń** zostanie otwarta strona.

2. Aby uzyskać **konsoli testowania interfejsu Open API**, wybierz region, który najlepiej opisuje Twojej lokalizacji. 

   ![Obraz — pole region strony Delete](images/test-drive-region.png)
 
   **Obrazu — Usuń** zostanie otwarta konsola interfejsu API.
 
3. W **listId** wprowadź identyfikator listy do usunięcia z obrazu.  Jest to liczba zwracanych w **obraz — Pobierz wszystkie identyfikatory obraz** konsoli MyList. Następnie wprowadź **ImageId** obrazu do usunięcia. 

W naszym przykładzie jest identyfikator listy **58953**, wartość **ContentSource**. Obraz, który jest Identyfikatorem **59021**, wartość **ContentIds**.

1. Wprowadź klucz subskrypcji, a następnie wybierz pozycję **wysyłania**.

1. Aby sprawdzić, że obraz został usunięty, użyj **obraz — Pobierz wszystkie identyfikatory obraz** konsoli.
 
## <a name="change-list-information"></a>Zmień informacje dotyczące listy

Możesz edytować nazwę i opis listy i dodawania elementów metadanych.

1. W [dokumentacja interfejsu API zarządzania listy obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672), w menu po lewej stronie wybierz **listy obrazów**, a następnie wybierz pozycję **szczegóły aktualizacji**. 

   **Listy obrazów — informacje na temat aktualizacji** zostanie otwarta strona.

2. Aby uzyskać **konsoli testowania interfejsu Open API**, wybierz region, który najlepiej opisuje Twojej lokalizacji.  

    ![Listy obrazów — pole region strony Szczegóły aktualizacji](images/test-drive-region.png)

    **Listy obrazów — informacje na temat aktualizacji** zostanie otwarta konsola interfejsu API.
 
3. W **listId** wprowadź identyfikator listy, a następnie wprowadź klucz subskrypcji.

4. W **treść żądania** , edytować, a następnie wybierz **wysyłania** przycisk na stronie.

   ![Listy obrazów — informacje na temat aktualizacji konsoli edycji treść żądania](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Kolejne kroki

W kodzie za pomocą interfejsu API REST lub rozpoczynać się [obraz zawiera przewodnik Szybki Start .NET](image-lists-quickstart-dotnet.md) do integracji z aplikacją.
