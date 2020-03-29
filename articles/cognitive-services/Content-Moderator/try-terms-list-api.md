---
title: Moderowanie tekstu z niestandardowymi listami terminów - Moderator treści
titleSuffix: Azure Cognitive Services
description: Użyj interfejsu API zarządzania listami, aby utworzyć niestandardowe listy terminów do użycia z interfejsem API moderowania tekstu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 16cfb6c15a4d17ff3fb4f7f41f59f9f80af1e9e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75382127"
---
# <a name="moderate-with-custom-term-lists-in-the-api-console"></a>Moderowanie z niestandardowymi listami terminów w konsoli interfejsu API

Domyślna globalna lista terminów w usługach Azure Content Moderator wystarcza w przypadku większości potrzeb moderowania zawartości. Jednak może być konieczne sprawdzanie terminów, które są specyficzne dla organizacji. Na przykład warto oznaczyć tagiem nazwy konkurencji w celu dalszego przeglądu. 

Użyj [interfejsu API zarządzania listami,](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) aby utworzyć niestandardowe listy terminów do użycia z interfejsem API moderowania tekstu. Operacja **Tekst — ekran** skanuje tekst w poszukiwaniu wulgaryzmów, a także porównuje tekst z niestandardowymi i udostępnionymi listami zablokowanych.

> [!NOTE]
> Istnieje maksymalny limit wynoszący **5 list terminów**, a poszczególne listy **nie mogą przekraczać 10 000 terminów**.
>

Za pomocą interfejsu API zarządzania listami można wykonać następujące zadania:
- Tworzenie listy.
- Dodawanie terminów do listy.
- Sprawdzanie terminów względem terminów na liście.
- Usuwanie terminów z listy.
- Usuwanie listy.
- Edycja informacji na liście.
- Odświeżanie indeksu, aby zmiany na liście były uwzględnione w nowym skanowaniu.

## <a name="use-the-api-console"></a>Korzystanie z konsoli interfejsu API

Aby można było przetestować interfejs API w konsoli online, potrzebny jest klucz subskrypcji. Ten klucz znajduje się na karcie **Ustawienia** w polu **Ocp-Apim-Subscription-Key.** Aby uzyskać więcej informacji, zobacz [Omówienie](overview.md).

## <a name="refresh-search-index"></a>Odśwież indeks wyszukiwania

Po wkonaniu zmian na liście terminów należy odświeżyć jego indeks, aby zmiany zostały uwzględnione w przyszłych skanach. Ten krok jest podobny do tego, jak wyszukiwarka na pulpicie (jeśli jest włączona) lub wyszukiwarka internetowa stale odświeża swój indeks, aby uwzględnić nowe pliki lub strony.

1. W [odwołaniu interfejsu API zarządzania listą terminów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)w menu po lewej stronie wybierz pozycję **Listy terminów**, a następnie wybierz pozycję **Odśwież indeks wyszukiwania**. 

   Zostanie otwarta strona **Listy terminów — odśwież indeks wyszukiwania.**

2. W przypadku **konsoli testowania otwartego interfejsu API**wybierz region, który najbardziej opisuje Twoją lokalizację. 

   ![Listy terminów — odświeżanie wyboru regionu strony indeksu wyszukiwania](images/test-drive-region.png)

   Zostanie otwarta konsola INTERFEJSU API **Listy terminów — odświeżanie indeksu wyszukiwania.**

3. W polu **identyfikator listy** wprowadź identyfikator listy. Wprowadź klucz subskrypcji, a następnie wybierz pozycję **Wyślij**.

   ![Interfejs API list terminów — odświeżanie pola zawartości konsoli indeksu wyszukiwania](images/try-terms-list-refresh-1.png)

## <a name="create-a-term-list"></a>Tworzenie listy terminów
1. Przejdź do [odwołania interfejsu API zarządzania listą terminów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f). 

   Zostanie otwarta strona **Term Lists - Create.**

2. W przypadku **konsoli testowania otwartego interfejsu API**wybierz region, który najbardziej opisuje Twoją lokalizację. 

   ![Listy terminów — tworzenie zaznaczenia regionu strony](images/test-drive-region.png)

   Zostanie otwarta konsola **Term Lists — Tworzenie** interfejsu API.
 
3. W polu **Ocp-Apim-Subscription-Key** wprowadź klucz subskrypcji.

4. W **polu Treść Żądanie** wprowadź wartości **name** (na przykład MyList) i **Description**.

   ![Listy terminów — tworzenie nazwy i opisu treści żądania konsoli](images/try-terms-list-create-1.png)

5. Użyj symboli zastępczych pary klucz-wartość, aby przypisać do listy więcej metadanych opisowych.

       {
          "Name": "MyExclusionList",
          "Description": "MyListDescription",
          "Metadata": 
          {
             "Category": "Competitors",
             "Type": "Exclude"
          }
       }

   Dodaj metadane listy jako pary klucz-wartość, a nie rzeczywiste terminy.
 
6. Wybierz pozycję **Wyślij**. Lista zostanie utworzona. Zwróć uwagę na wartość **identyfikatora** skojarzoną z nową listą. Ten identyfikator jest potrzebny do innych funkcji zarządzania listą terminów.

   ![Listy terminów — okno Tworzenie zawartości odpowiedzi konsoli zawiera identyfikator listy](images/try-terms-list-create-2.png)
 
7. Dodaj terminy do mylisty. W menu po lewej stronie w obszarze **Termin**wybierz pozycję **Dodaj termin**. 

   Zostanie otwarta strona **Termin — dodaj termin.** 

8. W przypadku **konsoli testowania otwartego interfejsu API**wybierz region, który najbardziej opisuje Twoją lokalizację. 

   ![Termin — dodawanie zaznaczenia regionu strony Termin](images/test-drive-region.png)

   Zostanie otwarta konsola **Termin — Dodaj termin** interfejsu API.
 
9. W polu **identyfikator listId** wprowadź wygenerowany identyfikator listy i wybierz wartość **języka**. Wprowadź klucz subskrypcji, a następnie wybierz pozycję **Wyślij**.

   ![Termin — dodawanie parametrów kwerendy konsoli Termin](images/try-terms-list-create-3.png)
 
10. Aby sprawdzić, czy termin został dodany do listy, w menu po lewej stronie wybierz **pozycję Termin**, a następnie wybierz pozycję Pobierz **wszystkie warunki**. 

    Zostanie otwarta konsola interfejsu API **Term - Get All Terms.**

11. W polu **identyfikator listId** wprowadź identyfikator listy, a następnie wprowadź klucz subskrypcji. Wybierz pozycję **Wyślij**.

12. W polu **Zawartość odpowiedzi** sprawdź wprowadzone warunki.

    ![Termin — pole Zawartość konsoli Get All Terms zawiera listę wprowadzonych warunków](images/try-terms-list-create-4.png)
 
13. Dodaj jeszcze kilka terminów. Teraz, gdy utworzono niestandardową listę terminów, spróbuj [zeskanować tekst](try-text-api.md) przy użyciu niestandardowej listy terminów. 

## <a name="delete-terms-and-lists"></a>Usuwanie terminów i list

Usuwanie terminu lub listy jest bardzo proste. Za pomocą interfejsu API można wykonać następujące zadania:

- Usuwanie terminu. (**Termin - Usuń**)
- Usuwanie wszystkich terminów z listy, bez usuwania listy. (Termin **- Usuń wszystkie warunki)**
- Usuwanie listy i całej jej zawartości. (Listy**terminów — usuń)**

W tym przykładzie usuwa pojedynczy termin.

1. W [odwołaniu interfejsu API zarządzania listą terminów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)w menu po lewej stronie wybierz pozycję **Termin**, a następnie wybierz pozycję **Usuń**. 

   Zostanie otwarty **termin — usuń.**

2. W przypadku **konsoli testowania otwartego interfejsu API**wybierz region, który najbardziej opisuje Twoją lokalizację. 

   ![Termin — usuwanie zaznaczenia regionu strony](images/test-drive-region.png)

   Zostanie otwarta konsola **Termin — Usuń** interfejs API.
  
3. W polu **identyfikator list Wprowadź** identyfikator listy, z której chcesz usunąć termin. Ten identyfikator jest liczbą (w naszym przykładzie **122),** która jest zwracana w **konsoli Termin Listy — Pobierz szczegóły** dla mylist. Wprowadź termin i wybierz język.
 
   ![Termin — usuwanie parametrów kwerendy konsoli](images/try-terms-list-delete-1.png)

4. Wprowadź klucz subskrypcji, a następnie wybierz pozycję **Wyślij**.

5. Aby sprawdzić, czy termin został usunięty, użyj konsoli **Termin Listy — Pobierz wszystkie.**

   ![Listy terminów — pole Zawartość odpowiedzi konsoli Pobierz całą konsolę pokazuje, że termin jest usuwany](images/try-terms-list-delete-2.png)
 
## <a name="change-list-information"></a>Zmienianie informacji o liście

Możesz edytować nazwę i opis listy oraz dodawać elementy metadanych.

1. W [odwołaniu interfejsu API zarządzania listą terminów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)w menu po lewej stronie wybierz polecenie **Listy terminów**, a następnie wybierz pozycję **Aktualizuj szczegóły**. 

   Zostanie otwarta strona **Term Lists - Update Details.The Term Lists — Update Details** page.

2. W przypadku **konsoli testowania otwartego interfejsu API**wybierz region, który najbardziej opisuje Twoją lokalizację. 

   ![Listy terminów — wybór regionu strony Szczegóły aktualizacji](images/test-drive-region.png)

   Zostanie otwarta konsola interfejsu API **Term Lists — Update Details.**

3. W polu **identyfikator listId** wprowadź identyfikator listy, a następnie wprowadź klucz subskrypcji.

4. W **treści Żądanie** wykonuj zmiany, a następnie wybierz pozycję **Wyślij**.

   ![Listy terminów — aktualizacja szczegółów konsoli Żądania zmian w treści](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Następne kroki

Użyj interfejsu API REST w kodzie lub zacznij od [listy Termin .NET Szybki start,](term-lists-quickstart-dotnet.md) aby zintegrować się z aplikacją.
