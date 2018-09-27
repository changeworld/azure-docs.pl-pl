---
title: Moderowanie tekstu przy użyciu niestandardowych termin list - Content Moderator
titlesuffix: Azure Cognitive Services
description: Testuj listy terminów niestandardowych w konsoli usługi Content Moderator interfejsu API.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: 99df9fda2cc56f169a61ec215a976de28fc13d27
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47220282"
---
# <a name="moderate-with-custom-term-lists-in-the-api-console"></a>Moderowanie przy użyciu list niestandardowych termin w konsoli interfejsu API

Domyślnej globalnej listy terminy w usłudze Azure Content Moderator jest wystarczająca na potrzeby większości zawartości moderowania. Jednak może być konieczne ekranu warunków, które są specyficzne dla Twojej organizacji. Na przykład możesz chcieć nazwy tagu do dalszego przeglądu. 

Użyj [interfejsu API zarządzania listy](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) do tworzenia niestandardowych list terminów do wykorzystania przy użyciu interfejsu API moderowania tekstu. **Tekst — ekranu** operacji skanuje tekstu pod kątem wulgarności, a także porównanie tekst udostępnionych i niestandardowych list elementów zabronionych.

> [!NOTE]
> Istnieje maksymalny limit wynoszący **Wyświetla 5 termin** z każdej listy **nie może przekraczać 10 000 warunki**.
>

Interfejs API zarządzania lista umożliwia wykonywanie następujących zadań:
- Utwórz listę.
- Dodawanie warunków do listy.
- Warunki ekranu względem warunki na liście.
- Usuwanie warunków z listy.
- Usuwanie listy.
- Edytuj informacje na liście.
- Odśwież indeks, aby zmiany na liście znajdują się w nowe skanowanie.

## <a name="use-the-api-console"></a>Użyj konsoli interfejsu API

Zanim można Testuj interfejs API w konsoli usługi online, należy się klucz subskrypcji. Ten klucz znajduje się na **ustawienia** na karcie **Ocp-Apim-Subscription-Key** pole. Aby uzyskać więcej informacji, zobacz [Przegląd](overview.md).

## <a name="refresh-search-index"></a>Odśwież indeksu wyszukiwania

Po wprowadzeniu zmian do listy terminów należy odświeżyć jej indeks zmian, które mają zostać uwzględnione w czasie przyszłych skanowań. Ten krok jest podobny do sposobu wyszukiwarki na pulpicie systemu Windows (jeśli jest włączone) lub aparatu wyszukiwania w sieci web stale odświeża jej indeks w celu uwzględnienia nowych plików lub stron.

1.  W [dokumentacja interfejsu API zarządzania listy terminów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f), w menu po lewej stronie wybierz **Wyświetla termin**, a następnie wybierz pozycję **Odśwież indeksu wyszukiwania**. 

  **Termin — odświeżanie wyszukiwania indeks listy** zostanie otwarta strona.

2. Aby uzyskać **konsoli testowania interfejsu Open API**, wybierz region, który najlepiej opisuje Twojej lokalizacji. 

  ![Listy terminów — pole region strony indeksu wyszukiwania odświeżania](images/test-drive-region.png)

  **Termin — odświeżanie wyszukiwania indeks listy** zostanie otwarta konsola interfejsu API.

3.  W **listId** wprowadź identyfikator listy. Wprowadź klucz subskrypcji, a następnie wybierz pozycję **wysyłania**.

  ![Listy terminów interfejs API — pola zawartości indeksu wyszukiwania odświeżania konsoli odpowiedzi](images/try-terms-list-refresh-1.png)

## <a name="create-a-term-list"></a>Tworzenie listy terminów
1.  Przejdź do [dokumentacja interfejsu API zarządzania listy termin](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f). 

  **Termin Wyświetla — tworzenie** zostanie otwarta strona.

2.  Aby uzyskać **konsoli testowania interfejsu Open API**, wybierz region, który najlepiej opisuje Twojej lokalizacji. 

  ![Termin list — Tworzenie strony pole region](images/test-drive-region.png)

  **Termin Wyświetla — tworzenie** zostanie otwarta konsola interfejsu API.
 
3.  W **Ocp-Apim-Subscription-Key** wprowadź swój klucz subskrypcji.

4.  W **treść żądania** wprowadź wartości dla **nazwa** (na przykład MyList) i **opis**.

  ![Termin list — tworzenie nazwa treści żądania w konsoli i opis](images/try-terms-list-create-1.png)

5.  Użyj symboli zastępczych pary klucz wartość, aby przypisać bardziej opisowe metadanych do listy.

        {
           "Name": "MyExclusionList",
           "Description": "MyListDescription",
           "Metadata": 
           {
              "Category": "Competitors",
              "Type": "Exclude"
           }
        }

  Dodaj metadane listy jako pary klucz wartość, a nie rzeczywistych warunków.
 
6.  Wybierz pozycję **Wyślij**. Na liście jest tworzony. Uwaga **identyfikator** wartość, która jest skojarzona z nową listę. Ten identyfikator będzie potrzebny do innych funkcji zarządzania listy terminów.

  ![Termin list — Tworzenie konsoli odpowiedzi na zawartości pola zawiera identyfikator listy](images/try-terms-list-create-2.png)
 
7.  Dodawanie warunków do MyList. W menu po lewej stronie w obszarze **termin**, wybierz opcję **Dodaj termin**. 

  **Termin — Dodaj termin** zostanie otwarta strona. 

8.  Aby uzyskać **konsoli testowania interfejsu Open API**, wybierz region, który najlepiej opisuje Twojej lokalizacji. 

  ![Termin — Dodaj pole region strony termin](images/test-drive-region.png)

  **Termin — Dodaj termin** zostanie otwarta konsola interfejsu API.
 
9.  W **listId** pole, wprowadź identyfikator listy, który został wygenerowany, a następnie wybierz wartość dla **języka**. Wprowadź klucz subskrypcji, a następnie wybierz pozycję **wysyłania**.

  ![Termin — Dodaj parametry zapytania konsoli termin](images/try-terms-list-create-3.png)
 
10. Aby sprawdzić, czy termin został dodany do listy, w menu po lewej stronie wybierz **termin**, a następnie wybierz pozycję **Pobierz wszystkie terminy**. 

  **Termin — Pobierz wszystkie terminy** zostanie otwarta konsola interfejsu API.

11. W **listId** wprowadź identyfikator listy, a następnie wprowadź klucz subskrypcji. Wybierz pozycję **Wyślij**.

12. W **zawartość odpowiedzi** upewnij się, warunkami, wprowadzony.

  ![Termin - Get-wszystkie terminy konsoli odpowiedzi pola zawartości listy warunków, które zostały wprowadzone](images/try-terms-list-create-4.png)
 
13. Dodaj kilka więcej postanowień. Teraz, po utworzeniu niestandardowej listy warunków, spróbuj [skanowanie jakiś tekst](try-text-api.md) przy użyciu listy terminów niestandardowych. 

## <a name="delete-terms-and-lists"></a>Usuń warunki i list

Usuwanie termin lub listy jest bardzo proste. Interfejs API umożliwia wykonywanie następujących zadań:

- Usuń termin. (**Termin — Usuń**)
- Usuń wszystkie warunki na liście, bez usuwania listy. (**Termin — Usuń wszystkie warunki**)
- Usuń listę i całą jego zawartość. (**Listy terminów — Usuń**)

W tym przykładzie Usuwa pojedynczy termin.

1.  W [dokumentacja interfejsu API zarządzania listy termin](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f), w menu po lewej stronie wybierz **termin**, a następnie wybierz pozycję **Usuń**. 

  **Termin — Usuń** zostanie otwarty.

2. Aby uzyskać **konsoli testowania interfejsu Open API**, wybierz region, który najlepiej opisuje Twojej lokalizacji. 

  ![Termin — pole region strony Delete](images/test-drive-region.png)

  **Termin — Usuń** zostanie otwarta konsola interfejsu API.
  
3.  W **listId** wprowadź identyfikator listy, który chcesz usunąć termin z. Ten identyfikator jest liczbą (w naszym przykładzie **122**), jest zwracany w **zawiera termin — Uzyskaj szczegółowe informacje** konsoli MyList. Wprowadź termin, a następnie wybierz język.
 
  ![Termin — parametry zapytania konsoli Delete](images/try-terms-list-delete-1.png)

4.  Wprowadź klucz subskrypcji, a następnie wybierz pozycję **wysyłania**.

5.  Aby sprawdzić, czy termin został usunięty, użyj **termin Wyświetla — Pobierz wszystkie** konsoli.

  ![Termin list — pobieranie zawartości, że pole pokazuje, że termin zostanie usunięta konsoli wszystkie odpowiedzi](images/try-terms-list-delete-2.png)
 
## <a name="change-list-information"></a>Zmień informacje dotyczące listy

Możesz edytować nazwę i opis listy i dodawania elementów metadanych.

1.  W [dokumentacja interfejsu API zarządzania listy termin](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f), w menu po lewej stronie wybierz **Wyświetla termin**, a następnie wybierz pozycję **szczegóły aktualizacji**. 

  **Termin listy — informacje na temat aktualizacji** zostanie otwarta strona.

2. Aby uzyskać **konsoli testowania interfejsu Open API**, wybierz region, który najlepiej opisuje Twojej lokalizacji. 

  ![Listy terminów — pole region strony Szczegóły aktualizacji](images/test-drive-region.png)

  **Termin listy — informacje na temat aktualizacji** zostanie otwarta konsola interfejsu API.

3.  W **listId** wprowadź identyfikator listy, a następnie wprowadź klucz subskrypcji.

4.  W **treść żądania** , edytować, a następnie wybierz **wysyłania**.

  ![Listy terminów — szczegóły aktualizacji konsoli edycji treść żądania](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Kolejne kroki

W kodzie za pomocą interfejsu API REST lub rozpoczynać się [termin zawiera przewodnik Szybki Start .NET](term-lists-quickstart-dotnet.md) do integracji z aplikacją.
