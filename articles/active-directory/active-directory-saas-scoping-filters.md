---
title: Udostępnianie aplikacji z filtrami zakresów | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać filtrów zakresu, aby zapobiec obiektów w aplikacji, które obsługują Inicjowanie obsługi użytkowników automatycznych z obsługiwana administracyjnie, jeśli obiekt nie spełniają wymagań biznesowych.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: bcfbda74-e4d4-4859-83bc-06b104df3918
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: markvi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d6f4f257d380d6521774afd23dbeaf6a94711c6d
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35293079"
---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>Udostępniania aplikacji na podstawie atrybutów z filtrami zakresów
Celem tego artykułu jest wyjaśnienie, jak używać filtrów zakresu do definiowania reguł na podstawie atrybutów, które określają, użytkowników, którzy są udostępnione do aplikacji.

## <a name="scoping-filter-use-cases"></a>Określanie zakresu filtru przypadki użycia

Filtr zakresu umożliwia Azure Active Directory (Azure AD) inicjowania obsługi usługi do dołączania lub wykluczania wszyscy użytkownicy, którzy mają odpowiadający określonej wartości atrybutu. Na przykład podczas inicjowania obsługi użytkowników z usługi Azure AD do aplikacji SaaS używana przez zespół sprzedaży, można można określić, że tylko użytkownicy z atrybutem "Dział", "Sprzedaży" musi należeć do zakresu do inicjowania obsługi.

Filtrami zakresów można inaczej w zależności od typu łącznika inicjowania obsługi administracyjnej:

* **Wychodzące alokacji z usługi Azure AD dla aplikacji SaaS**. W przypadku usługi Azure AD w systemie źródłowym [przypisań użytkowników i grup](manage-apps/assign-user-or-group-access-portal.md) są najczęściej spotykaną metodą określania użytkowników, którzy znajdują się w zakresie udostępniania. Te przydziały również są używane do włączania rejestracji jednokrotnej i podaj jedną metodę, aby zarządzać dostępem i inicjowania obsługi administracyjnej. Filtrami zakresów można opcjonalnie oprócz przydziałów lub zamiast je, aby odfiltrować użytkowników na podstawie wartości atrybutu.

    >[!TIP]
    > Możesz wyłączyć inicjowania obsługi administracyjnej, zmieniając ustawienia w oparciu o przypisania aplikacji przedsiębiorstwa [zakres](active-directory-saas-app-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) menu w obszarze Ustawienia inicjowania obsługi administracyjnej **Synchronizuj wszystkich użytkowników i grup**. Przy użyciu tej opcji plus opartych na atrybutach filtrów zakresu zapewnia większą wydajność niż za pomocą przypisań na podstawie grupy.  

* **Przychodzące inicjowania obsługi administracyjnej z HCM aplikacji do usługi Azure AD i usługi Active Directory**. Gdy [HCM aplikacji, takie jak produktu Workday](active-directory-saas-workday-tutorial.md) jest w systemie źródłowym zakresu filtry są podstawową metodą określania użytkowników, którzy powinny być pobranego z aplikacji HCM do usługi Active Directory lub Azure AD.

Domyślnie łączniki inicjowania obsługi usługi Azure AD nie ma żadnych opartych na atrybutach filtrów zakresu skonfigurowane. 

## <a name="scoping-filter-construction"></a>Określanie zakresu filtru konstrukcji

Filtr zakresu składa się z co najmniej jeden *klauzule*. Klauzule określić użytkowników, którzy mogą przechodzić przez filtr zakresu wyniku obliczenia atrybutów każdego użytkownika. Na przykład może być jedną klauzulę wymagającego użytkownika atrybutu "Stan" równa się "Warszawa", więc tylko użytkownicy z nowego Jorku są udostępnione do aplikacji. 

Jedną klauzulę definiuje jednego warunku dla wartości jeden atrybut. Jeśli wiele klauzul są tworzone w jeden filtr zakresu, są one oceniane razem przy użyciu logiki "I". Oznacza to, że wszystkie klauzule musi mieć wartość "true" w kolejności dla użytkownika na potrzeby aprowizacji.

Ponadto można tworzyć wiele filtrów zakresu dla jednej aplikacji. Jeśli podano wiele filtrów zakresu, są one oceniane razem przy użyciu logiki "Lub". Oznacza to, że jeśli wszystkie warunki w żadnym skonfigurowane filtry zakresu na wartość "true", użytkownik zostanie zainicjowana.

Każdy użytkownik lub grupa przetworzone przez usługę inicjowania obsługi usługi Azure AD zawsze jest obliczane indywidualnie dla każdego zakresu filtru.

Na przykład rozważmy następujący filtr zakresu:

![Filtr zakresów](./media/active-directory-saas-scoping-filters/scoping-filter.PNG) 

Zgodnie z tego zakresu filtru użytkowników musi spełniać następujące kryteria do obsługi administracyjnej:

* Muszą być w Nowym Jorku.
* Muszą one pracuje w dziale zespołu inżynieryjnego.
* Ich identyfikator pracownika firmy musi należeć do zakresu od 1 000 000 i 2,000,000.
* Ich stanowisko nie może być zerowa ani pusta.

## <a name="create-scoping-filters"></a>Tworzenie zakresu filtrów
Filtry zakresu są skonfigurowane jako część mapowań atrybutów dla każdego użytkownika usługi Azure AD, inicjowania obsługi administracyjnej łącznika. W poniższej procedurze przyjęto, że zostały już skonfigurowane automatyczne Inicjowanie obsługi administracyjnej dla [jedną z obsługiwanych aplikacji](active-directory-saas-tutorial-list.md) i dodawania do niej zakresu filtru.

### <a name="create-a-scoping-filter"></a>Tworzenie zakresu filtru
1. W [portalu Azure](https://portal.azure.com), przejdź do **usługi Azure Active Directory** > **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje** sekcji.

2. Wybierz aplikację, dla której skonfigurowano automatyczne udostępnianie: na przykład "ServiceNow".

3. Wybierz **inicjowania obsługi administracyjnej** kartę.

4. W **mapowania** wybierz mapowanie, które chcesz skonfigurować filtr zakresu dla: na przykład "synchronizacji Azure Active Directory użytkowników do usługi ServiceNow".

5. Wybierz **źródła zakres obiektu** menu.

6. Wybierz **Dodaj Filtr zakresu**.

7. Określ klauzulę, wybierając źródło **nazwa atrybutu**, **Operator**i **wartość atrybutu** do dopasowywania. Obsługiwane są następujące operatory:

   a. **EQUALS**. Klauzula zwraca "true", jeśli atrybut obliczane są dokładnie wejściową wartość ciągu (z uwzględnieniem wielkości liter).

   b. **NIE RÓWNA SIĘ**. Klauzula zwraca wartość "true", jeśli nie jest zgodna oceniona atrybutu wejściową wartość ciągu (z uwzględnieniem wielkości liter).

   c. **MA WARTOŚĆ TRUE**. Klauzula zwraca wartość "true", jeśli obliczane atrybutu zawiera wartość logiczną PRAWDA.

   d. **MA WARTOŚĆ FALSE**. Klauzula zwraca wartość "true", jeśli obliczane atrybutu zawiera wartość logiczną FAŁSZ.

   e. **MA WARTOŚĆ NULL**. Klauzula zwraca wartość "true", jeśli obliczane atrybut jest pusty.

   f. **NIE MA WARTOŚCI NULL**. Klauzula zwraca wartość "true", jeśli obliczane atrybut nie jest pusty.

   g. **WYRAŻENIA REGULARNEGO DOPASOWANIE**. Klauzula zwraca wartość "true", jeśli obliczane atrybutu jest zgodna z wzorcem wyrażenia regularnego. Na przykład: ([1-9][0-9]) dopasowuje dowolną liczbę od 10 do 99.

   h. **NIE WYRAŻENIA REGULARNEGO DOPASOWANIE**. Klauzula zwraca wartość "true", jeśli nie jest zgodna oceniona atrybutu wzorzec wyrażenia regularnego.

8. Wybierz **Dodaj nowego zakresu klauzuli**.

9. Opcjonalnie powtórz kroki 7-8, aby dodać więcej klauzule zakresu.

10. W **zakresu filtru tytuł**, Dodaj nazwę zakresu filtru.

11. Kliknij przycisk **OK**.

12. Wybierz **OK** ponownie na **filtry zakresu** ekranu. Opcjonalnie powtórz kroki 6-11, aby dodać inny filtr zakresu.

13. Wybierz **zapisać** na **mapowanie atrybutu** ekranu. 

>[!IMPORTANT] 
> Zapisywanie nowego zakresu wyzwalaczy filtr do nowej pełnej synchronizacji dla aplikacji, w której wszyscy użytkownicy w systemie źródłowym są sprawdzane ponownie dla nowego zakresu filtru. Jeśli użytkownik w aplikacji został wcześniej w zakresie inicjowania obsługi administracyjnej, ale wypada poza zakresem, ich konto zostało wyłączone lub cofnąć jej zainicjowania jej w aplikacji.


## <a name="related-articles"></a>Pokrewne artykuły:
* [Indeks artykułów dotyczących zarządzania aplikacjami w usłudze Azure Active Directory](active-directory-apps-index.md)
* [Automatyzowanie użytkownika alokowania i anulowania alokowania do aplikacji SaaS](active-directory-saas-app-provisioning.md)
* [Dostosowywanie mapowań atrybutów do inicjowania obsługi użytkowników](active-directory-saas-customizing-attribute-mappings.md)
* [Pisanie wyrażeń do mapowania atrybutów](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Powiadomienia aprowizacji kont](active-directory-saas-account-provisioning-notifications.md)
* [Włącz automatyczne Inicjowanie obsługi użytkowników i grup z usługi Azure Active Directory do aplikacji za pomocą SCIM](manage-apps/use-scim-to-provision-users-and-groups.md)
* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS](active-directory-saas-tutorial-list.md)

