---
title: Aprowizuj aplikacje za pomocą filtrów zakresu | Dokumenty firmy Microsoft
description: Dowiedz się, jak używać filtrów zakresu, aby zapobiec inicjowaniu obsługi administracyjnej obiektów w aplikacjach, które obsługują automatyczne inicjowanie obsługi administracyjnej użytkowników, jeśli obiekt nie spełnia wymagań biznesowych.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: mimart
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c667409f2abb9f1cf89ae3b34f08e0f9eec067e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79138539"
---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>Inicjowanie obsługi administracyjnej aplikacji oparte na atrybutach z filtrami zakresu
Celem tego artykułu jest wyjaśnienie, jak używać filtrów zakresu do definiowania reguł opartych na atrybutach, które określają, którzy użytkownicy są aprowizowani aplikacji.

## <a name="scoping-filter-use-cases"></a>Przypadki użycia filtru zakresu

Filtr zakresu umożliwia usłudze inicjowania obsługi administracyjnej usługi Azure Active Directory (Azure AD) uwzględnienie lub wykluczenie wszystkich użytkowników, którzy mają atrybut zgodny z określoną wartością. Na przykład podczas inicjowania obsługi administracyjnej użytkowników z usługi Azure AD do aplikacji SaaS używane przez zespół sprzedaży, można określić, że tylko użytkownicy z atrybutem "Dział" "Sprzedaż" powinny być w zakresie inicjowania obsługi administracyjnej.

Filtry zakresu mogą być używane w różny sposób w zależności od typu łącznika inicjowania obsługi administracyjnej:

* **Wychodzące inicjowanie obsługi administracyjnej z usługi Azure AD do aplikacji SaaS**. Gdy usługa Azure AD jest systemem źródłowym, [przypisania użytkowników i grup](../manage-apps/assign-user-or-group-access-portal.md) są najbardziej typową metodą określania, którzy użytkownicy są w zakresie inicjowania obsługi administracyjnej. Te przypisania są również używane do włączania logowania jednokrotnego i zapewniają jedną metodę zarządzania dostępem i inicjowania obsługi administracyjnej. Filtry zakresu mogą być używane opcjonalnie, oprócz przypisań lub zamiast nich, do filtrowania użytkowników na podstawie wartości atrybutów.

    >[!TIP]
    > Inicjowanie obsługi administracyjnej na podstawie przydziałów dla aplikacji przedsiębiorstwa można wyłączyć, zmieniając ustawienia w menu [Zakres](../app-provisioning/user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) w obszarze ustawień inicjowania obsługi administracyjnej, aby **zsynchronizować wszystkich użytkowników i grupy**. Użycie tej opcji oraz filtrów zakresu opartych na atrybutach zapewnia większą wydajność niż przy użyciu przypisań opartych na grupach.  

* **Inbound inicjowanie obsługi administracyjnej z aplikacji HCM do usługi Azure AD i usługi Active Directory**. Gdy [aplikacja HCM, taka jak Workday,](../saas-apps/workday-tutorial.md) jest systemem źródłowym, filtry zakresu są podstawową metodą określania, którzy użytkownicy powinni być aprowizowani z aplikacji HCM do usługi Active Directory lub usługi Azure AD.

Domyślnie łączniki inicjowania obsługi administracyjnej usługi Azure AD nie mają skonfigurowanych filtrów zakresu opartych na atrybutach. 

## <a name="scoping-filter-construction"></a>Konstrukcja filtra zakresu

Filtr zakresu składa się z co najmniej jednej *klauzul*. Klauzule określają, którzy użytkownicy mogą przechodzić przez filtr zakresu, oceniając atrybuty każdego użytkownika. Na przykład może mieć jedną klauzulę, która wymaga, że atrybut "Stan" użytkownika jest równy "Nowy Jork", więc tylko użytkownicy w Nowym Jorku są aprowizacji do aplikacji. 

Pojedyncza klauzula definiuje pojedynczy warunek dla pojedynczej wartości atrybutu. Jeśli wiele klauzul są tworzone w filtrze pojedynczego zakresu, są one oceniane razem przy użyciu logiki "AND". Oznacza to, że wszystkie klauzule muszą ocenić jako "true", aby użytkownik ma być aprowidywny.

Na koniec można utworzyć wiele filtrów zakresu dla pojedynczej aplikacji. Jeśli istnieje wiele filtrów zakresu, są one oceniane razem przy użyciu logiki "OR". Oznacza to, że jeśli wszystkie klauzule w dowolnym z skonfigurowanych filtrów zakresu ocenić jako "true", użytkownik jest aprowizacji.

Każdy użytkownik lub grupa przetworzona przez usługę inicjowania obsługi administracyjnej usługi Azure AD jest zawsze oceniana indywidualnie względem każdego filtru zakresu.

Na przykład należy wziąć pod uwagę następujący filtr zakresu:

![Filtr zakresu](./media/define-conditional-rules-for-provisioning-user-accounts/scoping-filter.PNG) 

Zgodnie z tym filtrem zakresu użytkownicy muszą spełniać następujące kryteria, które mają być aprowizacji:

* Muszą być w Nowym Jorku.
* Muszą pracować w dziale inżynierii.
* Identyfikator pracownika firmy musi wynosić od 1 000 000 do 2 000 000.
* Ich tytuł pracy nie może być zerowy ani pusty.

## <a name="create-scoping-filters"></a>Tworzenie filtrów zakresu
Filtry zakresu są konfigurowane jako część mapowań atrybutów dla każdego łącznika inicjowania obsługi administracyjnej użytkownika usługi Azure AD. Poniższa procedura zakłada, że już skonfigurowano automatyczne inicjowanie obsługi administracyjnej dla [jednej z obsługiwanych aplikacji](../saas-apps/tutorial-list.md) i dodajesz do niej filtr zakresu.

### <a name="create-a-scoping-filter"></a>Tworzenie filtru zakresu
1. W [witrynie Azure portal](https://portal.azure.com)przejdź do sekcji **Usługi Azure Active Directory** > **Enterprise Applications** > **Wszystkie aplikacje.**

2. Wybierz aplikację, dla której skonfigurowano automatyczne inicjowanie obsługi administracyjnej: na przykład "ServiceNow".

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

4. W sekcji **Mapowania** wybierz mapowanie, które chcesz skonfigurować filtr zakresu dla: na przykład "Synchronizuj użytkowników usługi Azure Active Directory z ServiceNow".

5. Wybierz menu **Zakres obiektu źródłowego.**

6. Wybierz **dodaj filtr zakresu**.

7. Zdefiniuj klauzulę, wybierając źródło nazwę **atrybutu,** **operator**i **wartość atrybutu,** z które mają być zgodne. Obsługiwane są następujące operatory:

   a. **RÓWNA SIĘ**. Klauzula zwraca wartość "true", jeśli oceniony atrybut dokładnie odpowiada wartości ciągu wejściowego (wielkość liter).

   b. **NIE JEST RÓWNA**. Klauzula zwraca wartość "true", jeśli oceniony atrybut nie jest zgodny z wartością ciągu wejściowego (z uwzględnieniem wielkości liter).

   d. **TO PRAWDA**. Klauzula zwraca wartość "true", jeśli oceniony atrybut zawiera wartość logiczną true.

   d. **JEST FALSE**. Klauzula zwraca wartość "true", jeśli oceniony atrybut zawiera wartość logiczną false.

   e. **JEST NULL**. Klauzula zwraca wartość "true", jeśli oceniany atrybut jest pusty.

   f. **NIE MA WARTOŚCI NULL**. Klauzula zwraca wartość "true", jeśli oceniany atrybut nie jest pusty.

   g. **REGEX MATCH**. Klauzula zwraca wartość "true", jeśli oceniany atrybut pasuje do wzorca wyrażenia regularnego. Na przykład: ([1-9][0-9]) pasuje do dowolnej liczby z 10 do 99.

   h. **NIE REGEX PASUJE**. Klauzula zwraca wartość "true", jeśli oceniany atrybut nie pasuje do wzorca wyrażenia regularnego.
   
   i. **Greater_Than.** Klauzula zwraca wartość "true", jeśli oceniony atrybut jest większy niż wartość. Wartość określona w filtrze zakresu musi być całkowitej liczby i atrybut na użytkownika musi być całkowitej [0,1,2,...]. 
   
   j. **Greater_Than_OR_EQUALS.** Klauzula zwraca wartość "true", jeśli oceniony atrybut jest większy lub równy wartości. Wartość określona w filtrze zakresu musi być całkowitej liczby i atrybut na użytkownika musi być całkowitej [0,1,2,...]. 
   
   k. **Zawiera.** Klauzula zwraca wartość "true", jeśli oceniony atrybut zawiera wartość ciągu (z uwzględnieniem wielkości liter) zgodnie z opisem [w tym miejscu.](https://docs.microsoft.com/dotnet/api/system.string.contains?view=netframework-4.8) 


>[!IMPORTANT] 
> - IsMemberOf filtr nie jest obecnie obsługiwany.
> - EQUALS i NOT EQUALS nie są obsługiwane dla atrybutów wielowartościowych

9. Opcjonalnie powtórz kroki 7-8, aby dodać więcej klauzul zakresu.

10. W **polu Tytuł filtru zakresu**dodaj nazwę filtru zakresu.

11. Kliknij przycisk **OK**.

12. Ponownie wybierz **przycisk OK** na ekranie **Filtry zakresu.** Opcjonalnie powtórz kroki 6-11, aby dodać kolejny filtr zakresu.

13. Wybierz **pozycję Zapisz** na ekranie **Mapowanie atrybutów.** 

>[!IMPORTANT] 
> Zapisanie nowego filtru zakresu wyzwala nową pełną synchronizację dla aplikacji, gdzie wszyscy użytkownicy w systemie źródłowym są ponownie oceniani względem nowego filtru zakresu. Jeśli użytkownik w aplikacji był wcześniej w zakresie inicjowania obsługi administracyjnej, ale wypada poza zakresem, jego konto jest wyłączone lub anulowane w aplikacji. Aby zastąpić to zachowanie domyślne, zapoznaj się [z pomiń usuwanie dla kont użytkowników, które wykraczają poza zakres](../app-provisioning/skip-out-of-scope-deletions.md).


## <a name="common-scoping-filters"></a>Typowe filtry zakresu
| Atrybut docelowy| Operator | Wartość | Opis|
|----|----|----|----|
|userPrincipalName|REGEX MECZU|.\*@domain.com |Wszyscy użytkownicy z userPrincipal, który ma domenę, @domain.com będą mogli inicjować inicjowanie obsługi administracyjnej|
|userPrincipalName|NIE REGEX MECZU|.\*@domain.com|Wszyscy użytkownicy z userPrincipal, który ma domenę @domain.com będzie poza zakresem inicjowania obsługi administracyjnej|
|department|Równa|Sprzedaży|Wszyscy użytkownicy z działu sprzedaży mają w zakresie inicjowania obsługi administracyjnej|
|pracownikID|REGEX MECZU|(1[0-9][0-9][0-9][0-9][0-9][0-9])| Wszyscy pracownicy z identyfikatorami workerIds między 1000000 i 2000000 są w zakresie inicjowania obsługi administracyjnej.|

## <a name="related-articles"></a>Pokrewne artykuły:
* [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS](../app-provisioning/user-provisioning.md)
* [Dostosowywanie mapowań atrybutów do inicjowania obsługi administracyjnej przez użytkowników](../app-provisioning/customize-application-attributes.md)
* [Pisanie wyrażeń do mapowania atrybutów](functions-for-customizing-application-data.md)
* [Powiadomienia o inicjowaniu obsługi administracyjnej kont](../app-provisioning/user-provisioning.md)
* [Użyj funkcji SCIM, aby umożliwić automatyczne inicjowanie obsługi administracyjnej użytkowników i grup z usługi Azure Active Directory do aplikacji](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Lista samouczków dotyczących integrowania aplikacji SaaS](../saas-apps/tutorial-list.md)

