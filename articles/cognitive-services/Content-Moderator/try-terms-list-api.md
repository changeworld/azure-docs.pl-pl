---
title: Średnie tekst z listy niestandardowych termin w zawartości moderatora Azure | Dokumentacja firmy Microsoft
description: Test-Drive listy niestandardowe termin w konsoli zawartości moderatora interfejsu API.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: 2542e4590781879408aafe8d072eceef157e02c9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347161"
---
# <a name="moderate-with-custom-term-lists-in-the-api-console"></a>Średnie z listami termin niestandardowych w konsoli interfejsu API

Domyślnej globalnej listy warunków w moderatora zawartości platformy Azure jest wystarczająca dla potrzeb łagodzenia najbardziej zawartości. Jednak może być konieczne ekranu terminów, które są specyficzne dla Twojej organizacji. Na przykład możesz chcieć nazwy tagu dla dalszego przeglądu. 

Użyj [interfejs API zarządzania listy](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) do tworzenia niestandardowych list warunków do użycia przy użyciu interfejsu API łagodzenia tekstu. **Tekst — ekranu** operacji skanuje tekstu niestosownych wyrażeń i porównują również tekstu względem blacklists udostępnionego i niestandardowych.

> [!NOTE]
> Istnieje limit maksymalny wynoszący **wymieniono 5 termin** z każdej listy **przekracza 10 000 warunki**.
>

Interfejs API zarządzania listy umożliwia wykonywanie następujących zadań:
- Utwórz listę.
- Dodawanie warunków do listy.
- Warunki ekranu względem warunki na liście.
- Usuwanie terminów z listy.
- Usuń z listy.
- Edytuj informacje o liście.
- Odśwież indeks, aby zmiany na liście znajdują się w nowe skanowanie.

## <a name="use-the-api-console"></a>Za pomocą konsoli interfejsu API

Zanim można test-drive interfejsu API w konsoli usługi online, należy klucz subskrypcji. Ten klucz znajduje się na **ustawienia** karcie **Ocp-Apim-subskrypcji — klucz** pole. Aby uzyskać więcej informacji, zobacz [omówienie](overview.md).

## <a name="refresh-search-index"></a>Odśwież indeksu wyszukiwania

Po wprowadzeniu zmian do listy termin należy odświeżyć jej indeks zmian do uwzględnienia w czasie przyszłych skanowań. Ten krok jest podobny do sposobu aparat wyszukiwania na pulpicie (jeśli jest włączona) lub aparatu wyszukiwania w sieci web stale odświeża jej indeks w celu uwzględnienia nowych plików lub strony.

1.  W [dokumentacja interfejsu API zarządzania listy termin](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f), w menu po lewej stronie wybierz **wymieniono termin**, a następnie wybierz **Odśwież indeksu wyszukiwania**. 

  **Termin - Odśwież wyszukiwania indeks listy** zostanie otwarta strona.

2. Aby uzyskać **konsoli testowania otwarty interfejs API**, wybierz region, który najlepiej opisuje Twojej lokalizacji. 

  ![Określenie listy — pole region indeksu wyszukiwania odświeżania strony](images/test-drive-region.png)

  **Termin - Odśwież wyszukiwania indeks listy** zostanie otwarta konsola interfejsu API.

3.  W **identyfikatora listy można wpisać** wprowadź identyfikator listy. Wprowadź klucz subskrypcji, a następnie wybierz **wysyłania**.

  ![Określenie listy interfejsu API - pola zawartości odpowiedzi konsoli odświeżania indeksu wyszukiwania](images/try-terms-list-refresh-1.png)

## <a name="create-a-term-list"></a>Utwórz listę termin
1.  Przejdź do [dokumentacja interfejsu API zarządzania listy termin](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f). 

  **Termin wymieniono — tworzenie** zostanie otwarta strona.

2.  Aby uzyskać **konsoli testowania otwarty interfejs API**, wybierz region, który najlepiej opisuje Twojej lokalizacji. 

  ![Termin list — Utwórz pole region strony](images/test-drive-region.png)

  **Termin wymieniono — tworzenie** zostanie otwarta konsola interfejsu API.
 
3.  W **Ocp-Apim-subskrypcji — klucz** wprowadź klucz subskrypcji.

4.  W **treść żądania** wprowadź wartości dla **nazwa** (na przykład MyList) i **opis**.

  ![Termin list — Tworzenie konsoli żądania treści nazwę i opis](images/try-terms-list-create-1.png)

5.  Użyj symboli zastępczych pary klucz wartość do przypisania do listy opisowej metadanych.

        {
           "Name": "MyExclusionList",
           "Description": "MyListDescription",
           "Metadata": 
           {
              "Category": "Competitors",
              "Type": "Exclude"
           }
        }

  Dodaj metadane listy jako pary klucz wartość, a nie rzeczywiste warunki.
 
6.  Wybierz pozycję **Wyślij**. Na liście jest tworzony. Uwaga **identyfikator** wartość, która jest skojarzona z nową listę. Ten identyfikator jest konieczne do innych funkcji zarządzania listy terminu.

  ![Termin list — tworzenie zawartości pola zawiera identyfikator listy konsoli odpowiedzi](images/try-terms-list-create-2.png)
 
7.  Dodawanie warunków do MyList. W menu po lewej stronie w obszarze **termin**, wybierz pozycję **dodać termin**. 

  **Termin - dodać termin** zostanie otwarta strona. 

8.  Aby uzyskać **konsoli testowania otwarty interfejs API**, wybierz region, który najlepiej opisuje Twojej lokalizacji. 

  ![Termin — Dodaj pole region strony termin](images/test-drive-region.png)

  **Termin - dodać termin** zostanie otwarta konsola interfejsu API.
 
9.  W **identyfikatora listy można wpisać** wprowadź identyfikator listy, który zostanie wygenerowany, a wybierz wartość **języka**. Wprowadź klucz subskrypcji, a następnie wybierz **wysyłania**.

  ![Termin — Dodawanie parametrów zapytania konsoli termin](images/try-terms-list-create-3.png)
 
10. Aby sprawdzić, czy termin został dodany do listy w menu po lewej stronie wybierz **termin**, a następnie wybierz **pobrać wszystkie warunki**. 

  **Termin — Pobierz wszystkie warunki** zostanie otwarta konsola interfejsu API.

11. W **identyfikatora listy można wpisać** wprowadź identyfikator listy, a następnie wprowadź klucz subskrypcji. Wybierz pozycję **Wyślij**.

12. W **zawartości odpowiedzi** Sprawdź wprowadzony warunki.

  ![Termin - Get wszystkie warunki konsoli odpowiedzi pola zawartości listy warunków, które zostały wprowadzone](images/try-terms-list-create-4.png)
 
13. Dodaj kilka warunków więcej. Teraz, po utworzeniu niestandardowej listy warunków, spróbuj [skanowanie tekst](try-text-api.md) przy użyciu listy termin niestandardowych. 

## <a name="delete-terms-and-lists"></a>Usuwanie terminów i listy

Usuwanie termin lub listy jest prosta. Interfejs API umożliwia wykonywanie następujących zadań:

- Usuwanie terminów. (**Termin — Usuń**)
- Usuń wszystkie warunki na liście bez usuwania na liście. (**Termin - Usuń wszystkie warunki**)
- Usuń listę i całą jego zawartość. (**Termin list - Usuń**)

W tym przykładzie Usuwa pojedynczy termin.

1.  W [dokumentacja interfejsu API zarządzania listy termin](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f), w menu po lewej stronie wybierz **termin**, a następnie wybierz **usunąć**. 

  **Termin — Usuń** otwiera.

2. Aby uzyskać **konsoli testowania otwarty interfejs API**, wybierz region, który najlepiej opisuje Twojej lokalizacji. 

  ![Termin — pole region strony Delete](images/test-drive-region.png)

  **Termin — Usuń** zostanie otwarta konsola interfejsu API.
  
3.  W **identyfikatora listy można wpisać** wprowadź identyfikator, który chcesz usunąć warunek z listy. Ten identyfikator jest numer (w naszym przykładzie **122**) który jest zwracany w **termin wymieniono — Pobierz szczegóły** konsoli dla MyList. Wprowadź termin, a następnie wybierz język.
 
  ![Termin - parametry zapytania konsoli Delete](images/try-terms-list-delete-1.png)

4.  Wprowadź klucz subskrypcji, a następnie wybierz **wysyłania**.

5.  Aby sprawdzić, czy termin została usunięta, użyj **termin wymieniono - pobrania wszystkich** konsoli.

  ![Termin list — pobranie zawartości usunięciu termin wyświetlane okno konsoli wszystkie odpowiedzi](images/try-terms-list-delete-2.png)
 
## <a name="change-list-information"></a>Zmień informacje dotyczące listy

Edytuj nazwę i opis listy i Dodaj elementy metadanych.

1.  W [dokumentacja interfejsu API zarządzania listy termin](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f), w menu po lewej stronie wybierz **wymieniono termin**, a następnie wybierz **szczegóły aktualizacji**. 

  **Termin listy — informacje na temat aktualizacji** zostanie otwarta strona.

2. Aby uzyskać **konsoli testowania otwarty interfejs API**, wybierz region, który najlepiej opisuje Twojej lokalizacji. 

  ![Określenie listy — pole region strony Szczegóły aktualizacji](images/test-drive-region.png)

  **Termin listy — informacje na temat aktualizacji** zostanie otwarta konsola interfejsu API.

3.  W **identyfikatora listy można wpisać** wprowadź identyfikator listy, a następnie wprowadź klucz subskrypcji.

4.  W **treść żądania** , edytować, a następnie wybierz **wysyłania**.

  ![Określenie listy — informacje na temat aktualizacji konsoli edycji treść żądania](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Kolejne kroki

W kodzie za pomocą interfejsu API REST lub rozpoczynać [termin wymieniono szybkiego startu .NET](term-lists-quickstart-dotnet.md) do integracji z aplikacji.
