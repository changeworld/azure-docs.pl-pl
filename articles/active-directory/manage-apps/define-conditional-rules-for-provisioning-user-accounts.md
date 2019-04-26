---
title: Aprowizowanie aplikacji z filtrami zakresu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać filtrów określania zakresu, aby zapobiec obiektów w aplikacji, które obsługują aprowizacji użytkowników zautomatyzowanych z aprowizowane, jeśli obiekt nie spełniają Twoje wymagania biznesowe.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ebc6c1e8a264c5570f3100885c4fca7d0d0d90d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60442861"
---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>Aprowizacja aplikacji opartych na atrybutach z filtrami zakresu
Celem tego artykułu jest wyjaśnienie, jak używać filtrów określania zakresu do zdefiniowania reguł opartych na atrybutach, określające, którzy użytkownicy są udostępnione do aplikacji.

## <a name="scoping-filter-use-cases"></a>Wyznaczanie zakresu filtru przypadków użycia

Filtr określania zakresu umożliwia usłudze Azure Active Directory (Azure AD) inicjowania obsługi usługi do dołączania lub wykluczania żadnych użytkowników, którzy mają atrybut, który odpowiada określonej wartości. Na przykład podczas aprowizacji użytkowników z usługi Azure AD aplikacji SaaS, używane przez zespół sprzedaży, należy można określić, że tylko użytkownicy z atrybutem "Dział" w "Sprzedaż" musi należeć do zakresu do inicjowania obsługi.

Określania zakresu filtry mogą być używane inaczej w zależności od typu łącznika inicjowania obsługi administracyjnej:

* **Aprowizacja wychodzące z usługi Azure AD z aplikacjami SaaS**. W przypadku usługi Azure AD w systemie źródłowym [przypisań użytkowników i grup](assign-user-or-group-access-portal.md) są najczęściej spotykaną metodą określania użytkowników, którzy znajdują się w zakresie do inicjowania obsługi. Te przypisania również są używane do włączania logowania jednokrotnego i udostępnianie jednej metody zarządzanie dostępem i Inicjowanie obsługi administracyjnej. Filtrami zakresu można opcjonalnie oprócz przypisania lub zamiast ich do filtrowania użytkowników na podstawie wartości atrybutu.

    >[!TIP]
    > Można wyłączyć, inicjowanie obsługi administracyjnej, zmieniając ustawienia w oparciu o przypisania dla aplikacji dla przedsiębiorstw [zakres](user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) menu w obszarze Ustawienia udostępniania **Synchronizuj wszystkich użytkowników i grup**. Przy użyciu tej opcji, a także opartych na atrybutach filtrów określania zakresu oferuje wyższą wydajność niż za pomocą przypisań na podstawie grupy.  

* **Dla ruchu przychodzącego, inicjowanie obsługi administracyjnej z HCM aplikacji do usługi Azure AD i usługi Active Directory**. Gdy [aplikacji HCM, takich jak Workday](../saas-apps/workday-tutorial.md) jest w systemie źródłowym filtrów określania zakresu są podstawową metodą określania użytkowników, którzy powinny zostać aprowizowane z aplikacją HCM, do usługi Active Directory lub Azure AD.

Domyślnie łączniki inicjowania obsługi usługi Azure AD nie ma żadnych opartych na atrybutach filtrów określania zakresu skonfigurowane. 

## <a name="scoping-filter-construction"></a>Wyznaczanie zakresu filtru konstrukcji

Filtr określania zakresu składa się z co najmniej jeden *klauzule*. Klauzule określają, które użytkownicy mogą przechodzić przez filtru określania zakresu poprzez ocenę atrybuty każdego użytkownika. Na przykład może mieć jedną klauzulę, który wymaga atrybutu "Stan" użytkownika równa się "New York", dlatego tylko użytkownicy z nowego Jorku są udostępnione do aplikacji. 

Pojedyncza klauzula definiuje pojedynczego warunku dla wartości jeden atrybut. Jeśli wiele klauzul są tworzone w jeden filtr określania zakresu, są one oceniane razem przy użyciu logiki "I". Oznacza to, że wszystkie klauzule musi zwrócić wartość "true" w kolejności użytkownik może być obsługiwana.

Na koniec można utworzyć wielu filtrów określania zakresu dla pojedynczej aplikacji. W przypadku wielu filtrów określania zakresu ocenia je z one ze sobą przy użyciu logiki "Lub". Oznacza to, że jeśli wszystkie klauzule we wszystkich skonfigurowanych filtrów określania zakresu na wartość "true", użytkownik zostanie zainicjowana.

Poszczególnych użytkowników lub grup przetworzonych przez usługę aprowizacji usługi Azure AD zawsze jest obliczany osobno dla każdego filtru określania zakresu.

Na przykład należy wziąć pod uwagę następujące filtru określania zakresu:

![Filtru określania zakresu](./media/define-conditional-rules-for-provisioning-user-accounts/scoping-filter.PNG) 

Zgodnie z tym filtru określania zakresu użytkowników musi spełniać następujące kryteria do zainicjowania obsługi administracyjnej:

* Muszą być w Nowym Jorku.
* Muszą one pracować w dziale zespołu inżynieryjnego.
* Identyfikator pracownika ich firmy musi wynosić od 1 000 000 do 2 000 000.
* Stanowiska zadania nie może być zerowa lub pusta.

## <a name="create-scoping-filters"></a>Tworzenie filtrów określania zakresu
Filtrów określania zakresu są skonfigurowane jako część mapowań atrybutów dla każdego użytkownika usługi Azure AD, inicjowanie obsługi administracyjnej łącznika. W poniższej procedurze przyjęto, że masz już skonfigurowane automatycznej aprowizacji dla [jednego z obsługiwanych aplikacji](../saas-apps/tutorial-list.md) i dodawania do niego filtru określania zakresu.

### <a name="create-a-scoping-filter"></a>Tworzenie filtru określania zakresu
1. W [witryny Azure portal](https://portal.azure.com), przejdź do **usługi Azure Active Directory** > **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje** sekcji.

2. Wybierz aplikację, dla którego skonfigurowano automatyczną aprowizację: na przykład "ServiceNow".

3. Wybierz **aprowizacji** kartę.

4. W **mapowania** wybierz mapowanie, które chcesz skonfigurować filtr określania zakresu dla: na przykład "synchronizacji usługi Azure Active Directory użytkowników do usługi ServiceNow".

5. Wybierz **zakres obiektów źródłowych** menu.

6. Wybierz **Dodawanie filtru określania zakresu**.

7. Określ klauzulę, wybierając źródło **nazwa atrybutu**, **Operator**i **wartość atrybutu** do dopasowywania. Obsługiwane są następujące operatory:

   a. **RÓWNA SIĘ**. Klauzula zwraca "true", jeśli atrybut ocenianą pasuje do wartości ciąg wejściowy dokładnie (z uwzględnieniem wielkości liter).

   b. **NIE RÓWNA SIĘ**. Klauzula zwraca wartość "true", jeśli atrybut ocenianą nie jest zgodny wejściową wartość ciągu (z uwzględnieniem wielkości liter).

   c. **MA WARTOŚĆ TRUE**. Klauzula zwraca wartość "true", jeśli atrybut ocenianą wartość logiczną PRAWDA.

   d. **MA WARTOŚĆ FALSE**. Klauzula zwraca wartość "true", jeśli atrybut ocenianą wartość logiczną FAŁSZ.

   e. **MA WARTOŚĆ NULL**. Klauzula zwraca wartość "true", jeśli atrybut ocenianą jest pusta.

   f. **NIE MA WARTOŚCI NULL**. Klauzula zwraca wartość "true", jeśli atrybut ocenianą nie jest pusty.

   g. **DOPASOWANIE WYRAŻENIA REGULARNEGO**. Klauzula zwraca wartość "true", jeśli atrybut ocenianą pasuje do wzorca wyrażenia regularnego. Na przykład: ([1-9][0-9]) dopasowuje dowolną liczbę z zakresu od 10 do 99.

   h. **WYRAŻENIE REGULARNE PASUJE**. Klauzula zwraca wartość "true", jeśli ocenianą atrybut nie jest zgodny z wzorcem wyrażenia regularnego.

8. Wybierz **Dodaj nową klauzulę określania zakresu**.

9. Opcjonalnie powtórz kroki od 7-8, aby dodać więcej klauzul określania zakresu.

10. W **tytuł filtru określania zakresu**, Dodaj nazwę dla zakresu filtru.

11. Kliknij przycisk **OK**.

12. Wybierz **OK** ponownie na **filtrów określania zakresu** ekranu. Opcjonalnie powtórz kroki od 6 do 11 można dodać innego filtru określania zakresu.

13. Wybierz **Zapisz** na **mapowanie atrybutu** ekranu. 

>[!IMPORTANT] 
> Zapisywanie nowego zakresu wyzwalaczy filtr do nowej pełnej synchronizacji dla aplikacji, gdzie wszyscy użytkownicy w systemie źródłowym są ponownie oceniane względem nowego filtru określania zakresu. Jeśli użytkownik w aplikacji był wcześniej w zakresie udostępniania, ale wypada poza zakresem, jego konto jest wyłączone lub anulowanie aprowizacji w aplikacji.


## <a name="related-articles"></a>Pokrewne artykuły:
* [Automatyzowanie użytkownika aprowizacji i cofania aprowizacji z aplikacjami SaaS](user-provisioning.md)
* [Dostosowywanie mapowań atrybutów dla aprowizacji użytkowników](customize-application-attributes.md)
* [Pisanie wyrażeń do mapowania atrybutów](functions-for-customizing-application-data.md)
* [Powiadomienia z aprowizacją kont](user-provisioning.md)
* [Umożliwia włączenie automatycznej aprowizacji użytkowników i grup z usługi Azure Active Directory do aplikacji Standard SCIM](use-scim-to-provision-users-and-groups.md)
* [Lista samouczków dotyczących integrowania aplikacji SaaS](../saas-apps/tutorial-list.md)

