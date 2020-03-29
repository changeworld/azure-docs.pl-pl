---
title: Moderowanie obrazów z listami niestandardowymi i konsolą interfejsu API — Moderator zawartości
titleSuffix: Azure Content Moderator
description: Interfejs API zarządzania listami w usłudze Azure Content Moderator służy do tworzenia niestandardowych list obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 27d9b12d9e1a0237050243c2b5f07edaa8d8857a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72757189"
---
# <a name="moderate-with-custom-image-lists-in-the-api-console"></a>Moderowanie z niestandardowymi listami obrazów w konsoli interfejsu API

Interfejs [API zarządzania listami](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672) w usłudze Azure Content Moderator służy do tworzenia niestandardowych list obrazów. Użyj niestandardowych list obrazów z interfejsem API moderowania obrazu. Operacja moderowania obrazu ocenia obraz. Jeśli utworzysz listy niestandardowe, operacja porównuje ją również z obrazami na listach niestandardowych. Za pomocą list niestandardowych można blokować lub zezwalać na obraz.

> [!NOTE]
> Istnieje maksymalny limit wynoszący **5 list obrazów**, a poszczególne listy **nie mogą przekraczać 10 000 obrazów**.
>

Za pomocą interfejsu API zarządzania listami można wykonać następujące zadania:

- Tworzenie listy.
- Dodawanie obrazów do listy.
- Obrazy ekranu względem obrazów na liście.
- Usuwanie obrazów z listy.
- Usuwanie listy.
- Edycja informacji na liście.
- Odświeżanie indeksu, aby zmiany na liście były uwzględnione w nowym skanowaniu.

## <a name="use-the-api-console"></a>Korzystanie z konsoli interfejsu API
Aby można było przetestować interfejs API w konsoli online, potrzebny jest klucz subskrypcji. Znajduje się on na karcie **Ustawienia** w polu **Ocp-Apim-Subscription-Key.** Aby uzyskać więcej informacji, zobacz [Omówienie](overview.md).

## <a name="refresh-search-index"></a>Odśwież indeks wyszukiwania

Po wkonaniu zmian na liście obrazów należy odświeżyć jej indeks, aby zmiany zostały uwzględnione w przyszłych skanach. Ten krok jest podobny do tego, jak wyszukiwarka na pulpicie (jeśli jest włączona) lub wyszukiwarka internetowa stale odświeża swój indeks, aby uwzględnić nowe pliki lub strony.

1. W [menu po](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)lewej stronie wybierz polecenie **Listy obrazów**, a następnie wybierz polecenie **Odśwież indeks wyszukiwania**.

   Zostanie otwarta strona **Lista obrazów — odśwież indeks wyszukiwania.**

2. W przypadku **konsoli testowania otwartego interfejsu API**wybierz region, który najbardziej opisuje Twoją lokalizację. 
 
    ![Listy obrazów — odświeżanie wyboru regionu strony indeksu wyszukiwania](images/test-drive-region.png)

    Zostanie otwarta konsola interfejsu API **Listy obrazów — odświeżanie indeksu wyszukiwania.**

3. W polu **identyfikator listy** wprowadź identyfikator listy. Wprowadź klucz subskrypcji, a następnie wybierz pozycję **Wyślij**.

   ![Listy obrazów — odświeżanie pola zawartości konsoli indeksu wyszukiwania](images/try-image-list-refresh-1.png)


## <a name="create-an-image-list"></a>Tworzenie listy obrazów

1. Przejdź do [odwołania interfejsu API zarządzania listą obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672).

   Zostanie otwarta strona **Listy obrazów — tworzenie.** 

3. W przypadku **konsoli testowania otwartego interfejsu API**wybierz region, który najbardziej opisuje Twoją lokalizację.

   ![Listy obrazów — tworzenie zaznaczenia regionu strony](images/test-drive-region.png)

   Zostanie otwarta konsola **Image Lists — Tworzenie** interfejsu API.
 
4. W polu **Ocp-Apim-Subscription-Key** wprowadź klucz subskrypcji.

5. W **polu Treść Żądanie** wprowadź wartości **name** (na przykład MyList) i **Description**.

   ![Listy obrazów — tworzenie nazwy i opisu treści żądania konsoli](images/try-terms-list-create-1.png)

6. Użyj symboli zastępczych pary klucz-wartość, aby przypisać do listy więcej metadanych opisowych.

       {
          "Name": "MyExclusionList",
          "Description": "MyListDescription",
          "Metadata": 
          {
            "Category": "Competitors",
            "Type": "Exclude"
          }
       }

   Dodaj metadane listy jako pary klucz-wartość, a nie rzeczywiste obrazy.
 
7. Wybierz pozycję **Wyślij**. Lista zostanie utworzona. Zwróć uwagę na wartość **identyfikatora** skojarzoną z nową listą. Ten identyfikator jest potrzebny do innych funkcji zarządzania listami obrazów.

   ![Listy obrazów — okno Tworzenie zawartości odpowiedzi konsoli zawiera identyfikator listy](images/try-terms-list-create-2.png)
 
8. Następnie dodaj obrazy do mylisty. W menu po lewej stronie wybierz pozycję **Obraz**, a następnie wybierz pozycję **Dodaj obraz**.

   Zostanie otwarta strona **Obraz — Dodaj obraz.** 

9. W przypadku **konsoli testowania otwartego interfejsu API**wybierz region, który najbardziej opisuje Twoją lokalizację.

   ![Obraz — dodawanie wyboru regionu strony obrazu](images/test-drive-region.png)

   Zostanie otwarta konsola interfejsu API **Image — Dodaj obraz.**
 
10. W polu **identyfikator listId** wprowadź wygenerowany identyfikator listy, a następnie wprowadź adres URL obrazu, który chcesz dodać. Wprowadź klucz subskrypcji, a następnie wybierz pozycję **Wyślij**.

11. Aby sprawdzić, czy obraz został dodany do listy, w menu po lewej stronie wybierz pozycję **Obraz**, a następnie wybierz pozycję **Pobierz wszystkie identyfikatory obrazów**.

    Zostanie otwarta konsola interfejsu API **Image - Get All Image Ids.**
  
12. W polu **identyfikator listId** wprowadź identyfikator listy, a następnie wprowadź klucz subskrypcji. Wybierz pozycję **Wyślij**.

    ![Obraz — pobierz wszystkie identyfikatory obrazów konsola Odpowiedź zawartość pole zawiera listę obrazów, które zostały wprowadzone](images/try-image-list-create-11.png)
 
10. Dodaj jeszcze kilka obrazów. Teraz, gdy utworzono niestandardową listę obrazów, spróbuj [ocenić obrazy](try-image-api.md) przy użyciu niestandardowej listy obrazów. 

## <a name="delete-images-and-lists"></a>Usuwanie obrazów i list

Usunięcie obrazu lub listy jest proste. Za pomocą interfejsu API można wykonać następujące zadania:

- Usuwanie obrazu. (Obraz **— usuwanie)**
- Usuń wszystkie obrazy z listy bez usuwania listy. (Obraz **- Usuń wszystkie obrazy)**
- Usuwanie listy i całej jej zawartości. (Listy**obrazów — usuwanie)**

W tym przykładzie usuwa pojedynczy obraz:

1. W [menu po](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)lewej stronie wybierz polecenie **Obraz**, a następnie wybierz polecenie **Usuń**. 

   Zostanie otwarta strona **Obraz — usuń.**

2. W przypadku **konsoli testowania otwartego interfejsu API**wybierz region, który najbardziej opisuje Twoją lokalizację. 

   ![Obraz — usuwanie zaznaczenia regionu strony](images/test-drive-region.png)
 
   Zostanie otwarta konsola **Image — Delete** API.
 
3. W polu **identyfikator list** Wprowadź identyfikator listy, z usunąć obraz.  Jest to numer zwrócony w **konsoli Image - Get All Image Ids** for MyList. Następnie wprowadź **imageid** obrazu do usunięcia. 

W naszym przykładzie identyfikator listy to **58953**, wartość **contentsource**. Identyfikator obrazu to **59021**, wartość **contentids**.

1. Wprowadź klucz subskrypcji, a następnie wybierz pozycję **Wyślij**.

1. Aby sprawdzić, czy obraz został usunięty, użyj konsoli **Obraz — Pobierz wszystkie identyfikatory obrazów.**
 
## <a name="change-list-information"></a>Zmienianie informacji o liście

Możesz edytować nazwę i opis listy oraz dodawać elementy metadanych.

1. W [menu po](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)lewej stronie wybierz polecenie **Listy obrazów**, a następnie wybierz polecenie **Aktualizuj szczegóły**. 

   Zostanie otwarta strona **Listy obrazów — szczegóły aktualizacji.**

2. W przypadku **konsoli testowania otwartego interfejsu API**wybierz region, który najbardziej opisuje Twoją lokalizację.  

    ![Listy obrazów — wybór regionu strony Aktualizuj szczegóły](images/test-drive-region.png)

    Zostanie otwarta konsola interfejsu API **Listy obrazów — aktualizacja szczegółów.**
 
3. W polu **identyfikator listId** wprowadź identyfikator listy, a następnie wprowadź klucz subskrypcji.

4. W **treści Żądanie** wykonuj zmiany, a następnie wybierz przycisk **Wyślij** na stronie.

   ![Listy obrazów — aktualizacja szczegółów konsoli Zażądaj edycji treści](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Następne kroki

Użyj interfejsu API REST w kodzie lub zacznij od [listy obrazów .NET Szybki start,](image-lists-quickstart-dotnet.md) aby zintegrować się z aplikacją.
