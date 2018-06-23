---
title: Średnie obrazów za pomocą list niestandardowych w zawartości moderatora Azure | Dokumentacja firmy Microsoft
description: Test-Drive listy obraz niestandardowy w konsoli zawartości moderatora interfejsu API.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: 2d714f017be16d978ffbb877a2b7e78e1caf9169
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347168"
---
# <a name="moderate-with-custom-image-lists-in-the-api-console"></a>Średnie z listami obraz niestandardowy w konsoli interfejsu API

Możesz użyć [interfejs API zarządzania listy](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672) w zawartości moderatora Azure do utworzenia listy obrazów. Listy obrazów za pomocą interfejsu API łagodzenia obrazu. Operacja łagodzenia obrazu oblicza obrazu. W przypadku utworzenia niestandardowych list, operacja również porównuje go do obrazów w niestandardowych list. Niestandardowych list, które umożliwia blokowanie lub zezwalanie na obrazie.

> [!NOTE]
> Istnieje limit maksymalny wynoszący **5 obrazu list** z każdej listy **przekracza 10 000 obrazów**.
>

Interfejs API zarządzania listy umożliwia wykonywanie następujących zadań:

- Utwórz listę.
- Dodawanie obrazów do listy.
- Obrazy ekranu względem obrazów na liście.
- Usuwanie obrazów z listy.
- Usuń z listy.
- Edytuj informacje o liście.
- Odśwież indeks, aby zmiany na liście znajdują się w nowe skanowanie.

## <a name="use-the-api-console"></a>Za pomocą konsoli interfejsu API
Zanim można test-drive interfejsu API w konsoli usługi online, należy klucz subskrypcji. Ten element znajduje się na **ustawienia** karcie **Ocp-Apim-subskrypcji — klucz** pole. Aby uzyskać więcej informacji, zobacz [omówienie](overview.md).

## <a name="refresh-search-index"></a>Odśwież indeksu wyszukiwania

Po wprowadzeniu zmian do listy obrazów, należy odświeżyć jej indeks zmian do uwzględnienia w czasie przyszłych skanowań. Ten krok jest podobny do sposobu aparat wyszukiwania na pulpicie (jeśli jest włączona) lub aparatu wyszukiwania w sieci web stale odświeża jej indeks w celu uwzględnienia nowych plików lub strony.

1.  W [dokumentacja interfejsu API zarządzania listy obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672), w menu po lewej stronie wybierz **listy obrazów**, a następnie wybierz **Odśwież indeksu wyszukiwania**.

  **Listy obrazów - Odśwież indeksu wyszukiwania** zostanie otwarta strona.

2. Aby uzyskać **konsoli testowania otwarty interfejs API**, wybierz region, który najlepiej opisuje Twojej lokalizacji. 
 
    ![Listy obrazów — pole region indeksu wyszukiwania odświeżania strony](images/test-drive-region.png)

    **Listy obrazów - Odśwież indeksu wyszukiwania** zostanie otwarta konsola interfejsu API.

3.  W **identyfikatora listy można wpisać** wprowadź identyfikator listy. Wprowadź klucz subskrypcji, a następnie wybierz **wysyłania**.

  ![Listy obrazów - pola zawartości odpowiedzi konsoli odświeżania indeksu wyszukiwania](images/try-image-list-refresh-1.png)


## <a name="create-an-image-list"></a>Tworzenie listy obrazów

1.  Przejdź do [dokumentacja interfejsu API zarządzania listy obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672).

  **Listy obrazów — tworzenie** zostanie otwarta strona. 

3.  Aby uzyskać **konsoli testowania otwarty interfejs API**, wybierz region, który najlepiej opisuje Twojej lokalizacji.

    ![Listy obrazów — utwórz pole region strony](images/test-drive-region.png)

    **Listy obrazów — tworzenie** zostanie otwarta konsola interfejsu API.
 
4.  W **Ocp-Apim-subskrypcji — klucz** wprowadź klucz subskrypcji.

5.  W **treść żądania** wprowadź wartości dla **nazwa** (na przykład MyList) i **opis**.

  ![Listy obrazów — Tworzenie konsoli żądania treści nazwę i opis](images/try-terms-list-create-1.png)

6.  Użyj symboli zastępczych pary klucz wartość do przypisania do listy opisowej metadanych.

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
 
7.  Wybierz pozycję **Wyślij**. Na liście jest tworzony. Uwaga **identyfikator** wartość, która jest skojarzona z nową listę. Ten identyfikator jest konieczne do innych funkcji zarządzania listy obrazów.

  ![Listy obrazów — tworzenie zawartości pola zawiera identyfikator listy konsoli odpowiedzi](images/try-terms-list-create-2.png)
 
8.  Następnie dodaj obrazy do MyList. W menu po lewej stronie wybierz **obrazu**, a następnie wybierz **Dodaj obraz**.

  **Obrazu — Dodaj obraz** zostanie otwarta strona. 

9. Aby uzyskać **konsoli testowania otwarty interfejs API**, wybierz region, który najlepiej opisuje Twojej lokalizacji.

  ![Obraz — Dodaj pole region strony obrazu](images/test-drive-region.png)

  **Obrazu — Dodaj obraz** zostanie otwarta konsola interfejsu API.
 
10. W **identyfikatora listy można wpisać** wprowadź identyfikator listy, który zostanie wygenerowany, a następnie wprowadź adres URL obrazu, który chcesz dodać. Wprowadź klucz subskrypcji, a następnie wybierz **wysyłania**.

11. Aby sprawdzić, czy obraz został dodany do listy w menu po lewej stronie wybierz **obrazu**, a następnie wybierz **uzyskać wszystkie identyfikatory obrazu**.

  **Obraz — Pobierz wszystkie identyfikatory obrazu** zostanie otwarta konsola interfejsu API.
  
12. W **identyfikatora listy można wpisać** wprowadź identyfikator listy, a następnie wprowadź klucz subskrypcji. Wybierz pozycję **Wyślij**.

  ![Obraz — wszystkie identyfikatory obrazu Get konsoli odpowiedzi pola zawartości listy obrazów, które zostały wprowadzone](images/try-image-list-create-11.png)
 
10. Dodaj kilka więcej obrazów. Teraz, po utworzeniu niestandardowej listy obrazów, spróbuj [oceny obrazów](try-image-api.md) przy użyciu listy niestandardowego obrazu. 

## <a name="delete-images-and-lists"></a>Usuwanie obrazów i listy

Usuwanie obrazu lub listy jest prosta. Interfejs API umożliwia wykonywanie następujących zadań:

- Usuwanie obrazu. (**Obrazu — Usuń**)
- Usuń wszystkie obrazy na liście bez usuwania na liście. (**Obrazu — Usuń wszystkie obrazy**)
- Usuń listę i całą jego zawartość. (**Listy obrazów - Usuń**)

W tym przykładzie powoduje usunięcie jednego obrazu:

1. W [dokumentacja interfejsu API zarządzania listy obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672), w menu po lewej stronie wybierz **obrazu**, a następnie wybierz **usunąć**. 

  **Obrazu — Usuń** zostanie otwarta strona.

2. Aby uzyskać **konsoli testowania otwarty interfejs API**, wybierz region, który najlepiej opisuje Twojej lokalizacji. 

  ![Obraz — pole region strony Delete](images/test-drive-region.png)
 
  **Obrazu — Usuń** zostanie otwarta konsola interfejsu API.
 
3.  W **identyfikatora listy można wpisać** wprowadź identyfikator listy można usunąć obrazu z.  Jest to liczba zwracanych w **obraz — Pobierz wszystkie identyfikatory obrazu** konsoli dla MyList. Następnie wprowadź **ImageId** obrazu do usunięcia. 

W naszym przykładzie jest identyfikator listy **58953**, wartość **ContentSource**. Obraz jest identyfikator **59021**, wartość **ContentIds**.

4.  Wprowadź klucz subskrypcji, a następnie wybierz **wysyłania**.

5.  Aby sprawdzić, czy obraz została usunięta, użyj **obraz — Pobierz wszystkie identyfikatory obrazu** konsoli.
 
## <a name="change-list-information"></a>Zmień informacje dotyczące listy

Edytuj nazwę i opis listy i Dodaj elementy metadanych.

1.  W [dokumentacja interfejsu API zarządzania listy obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672), w menu po lewej stronie wybierz **listy obrazów**, a następnie wybierz **szczegóły aktualizacji**. 

  **Listy obrazów — informacje na temat aktualizacji** zostanie otwarta strona.

2. Aby uzyskać **konsoli testowania otwarty interfejs API**, wybierz region, który najlepiej opisuje Twojej lokalizacji.  

    ![Listy obrazów — pole region strony Szczegóły aktualizacji](images/test-drive-region.png)

    **Listy obrazów — informacje na temat aktualizacji** zostanie otwarta konsola interfejsu API.
 
3.  W **identyfikatora listy można wpisać** wprowadź identyfikator listy, a następnie wprowadź klucz subskrypcji.

4.  W **treść żądania** , edytować, a następnie wybierz **wysyłania** przycisk na stronie.

  ![Listy obrazów — informacje na temat aktualizacji konsoli edycji treść żądania](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Kolejne kroki

W kodzie za pomocą interfejsu API REST lub rozpoczynać [szybkiego startu .NET listy obrazów](image-lists-quickstart-dotnet.md) do integracji z aplikacji.
