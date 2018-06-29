---
title: Dostosowywanie mapowań atrybutów usługi Azure AD | Dokumentacja firmy Microsoft
description: Dowiedz się, jakie mapowań atrybutów dla aplikacji SaaS w usłudze Azure Active Directory są, jak można dostosować je do adresów potrzeb biznesowych.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 549e0b8c-87ce-4c9b-b487-b7bf0155dc77
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2018
ms.author: markvi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7dad9f3e688c43de3eabd430bf5618ad4632ca3d
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036447"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Dostosowywanie użytkownika udostępniania mapowań atrybutów dla aplikacji SaaS w usłudze Azure Active Directory
Inicjowanie obsługi użytkowników do aplikacji SaaS innych firm, takie jak Salesforce, Google Apps i innych użytkowników usługi Microsoft Azure AD zapewnia obsługę. Jeśli Inicjowanie obsługi użytkowników dla aplikacji SaaS innych firm włączone, portalu Azure określa jego wartości atrybutów w formie mapowań atrybutów.

Brak wstępnie skonfigurowane zestaw atrybutów i mapowania atrybutów między obiektami użytkownika usługi Azure AD i każda aplikacja SaaS użytkownika. Niektóre aplikacje zarządzania innymi typami obiektów oprócz użytkowników, takich jak grupy. <br> 
 Mapowanie atrybutów domyślne można dostosować zgodnie z potrzebami firmy. Oznacza to, że można zmienić lub usunąć istniejące mapowanie atrybutów lub utworzyć nowe mapowanie atrybutów.
 
## <a name="editing-user-attribute-mappings"></a>Edytowanie mapowań atrybutów użytkownika

W portalu usługi Azure AD, możesz korzystać z tej funkcji, klikając **mapowania** Konfiguracja **inicjowania obsługi administracyjnej** w **Zarządzaj** części  **Aplikacja całościowa**.


![SalesForce][5] 

Kliknięcie przycisku **mapowania** konfiguracji, otwiera pokrewny **mapowanie atrybutu** ekranu. Brak mapowań atrybutów, które są wymagane przez aplikacji SaaS, aby mógł działać poprawnie. Dla wymaganych atrybutów **usunąć** funkcja jest niedostępna.


![SalesForce][6]  

W powyższym przykładzie można stwierdzić, że **Username** atrybutu zarządzanego obiektu w usłudze Salesforce jest wypełniana **userPrincipalName** wartość połączonego Azure obiektu usługi Active Directory.

Można dostosować istniejący **mapowań atrybutów** klikając mapowania. Spowoduje to otwarcie **atrybutu Edytuj** ekranu.

![SalesForce][7]  


### <a name="understanding-attribute-mapping-types"></a>Opis typów mapowanie atrybutu
Z mapowań atrybutów możesz kontrolować sposób atrybuty są wypełnione w aplikacji SaaS innych firm. Istnieją cztery typy innego mapowania obsługiwane:

* **Bezpośrednie** — atrybut docelowy jest wypełniane przy użyciu wartości atrybutu połączonego obiektu w usłudze Azure AD.
* **Stała** — atrybut docelowy jest wypełniana określony ciąg został określony.
* **Wyrażenie** — atrybut docelowy jest wypełniana na podstawie wyniku wyrażenia przypominającej skryptu. 
  Aby uzyskać więcej informacji, zobacz [pisać wyrażenia potrzeby mapowań atrybutów w usłudze Azure Active Directory](active-directory-saas-writing-expressions-for-attribute-mappings.md).
* **Brak** — atrybut docelowy zostanie pozostawiony bez modyfikacji. Jednak jeśli atrybut docelowy jest kiedykolwiek pusta, jest wypełnione wartością domyślną, który określisz.

Oprócz tych cztery podstawowe typy niestandardowe Mapowanie atrybutów obsługuje pojęcie opcjonalny **domyślne** przypisanie wartości. Przypisanie wartości domyślne gwarantuje, że atrybut docelowy jest wypełniana wartości, które nie istnieje żadna wartość w usłudze Azure AD, ani na obiekcie docelowym. Najbardziej typowych konfiguracji jest to pole pozostanie puste.


### <a name="understanding-attribute-mapping-properties"></a>Opis właściwości mapowanie atrybutu

W poprzedniej sekcji możesz już zostały wprowadzone do właściwości typu atrybutu mapowania.
Oprócz tej właściwości mapowań atrybutów obsługują następujące atrybuty:

- **Atrybut źródłowy** — atrybut użytkownika z systemu źródłowego (przykład: Azure Active Directory).
- **Atrybut TARGET** — atrybut użytkownika w systemie docelowym (przykład: ServiceNow).
- **Zgodne obiektów przy użyciu tego atrybutu** — czy to mapowanie powinien być używany do jednoznacznego identyfikowania użytkowników między systemami źródłowym i docelowym. Jest to zazwyczaj ustawiana na atrybut userPrincipalName lub poczty w usłudze Azure AD, która zwykle jest mapowana na pole nazwy użytkownika w aplikacji docelowej.
- **Dopasowywanie pierwszeństwo** — wiele pasujących atrybuty można ustawić. Jeśli dostępnych jest wiele, są oceniane pod w kolejności wynika z tego pola. Jak dopasowania zostanie znaleziony, żadne dodatkowe dopasowania atrybuty są oceniane.
- **Zastosuj to mapowanie**
    - **Zawsze** — Zastosuj to mapowanie na obu Tworzenie użytkownika i aktualizowanie akcji
    - **Tylko podczas tworzenia** -Zastosuj to mapowanie tylko akcje creation użytkownika


## <a name="editing-group-attribute-mappings"></a>Edytowanie mapowań atrybutów grupy

Wybraną liczbę aplikacji, takich jak usługi ServiceNow, pole i usługi Google Apps obsługuje możliwość udostępnienia obiektów grupy oprócz obiekty użytkownika. Obiekty grupy może zawierać właściwości grupy, takie jak nazwy wyświetlane oraz wiadomości e-mail aliasy, oprócz członków grupy.

![ServiceNow][8]  

Grupy inicjowania obsługi administracyjnej może być opcjonalnie włączona lub wyłączona wybierając mapowanie grupy w obszarze **mapowania**i ustawienie **włączone** do odpowiednią opcję w **mapowanie atrybutu** ekranu.

Atrybuty udostępnione jako część grupy obiektów można dostosować w taki sam sposób jak obiekty użytkownika, opisanych powyżej. 

>[!TIP]
>Inicjowanie obsługi obiektów grupy (właściwości i elementów członkowskich) to pojęcie różne od [Przypisywanie grup](manage-apps/assign-user-or-group-access-portal.md) do aplikacji. Istnieje możliwość przypisania grupy do aplikacji, ale tylko udostępniania obiektów użytkowników znajdujących się w grupie. Obsługa administracyjna obiektów dla całej grupy nie należy używać grup w przypisaniach.


## <a name="editing-the-list-of-supported-attributes"></a>Edytowanie listę obsługiwanych atrybutów

Atrybuty użytkownika, obsługiwane dla danej aplikacji są wstępnie skonfigurowane. Zarządzanie użytkownikami większości aplikacji interfejsów API nie obsługują wykrywania schematu, w związku z tym usługi Azure AD, świadczenie usługi nie jest w stanie można dynamicznie wygenerować listę obsługiwanych atrybutów przez wykonywania wywołań do aplikacji. 

Jednak niektóre aplikacje obsługują atrybutów niestandardowych. Aby dla usługi Azure AD, świadczenie usługi mieć możliwość odczytu i zapisu do atrybutów niestandardowych, należy podać ich definicje w portalu Azure using **Pokaż zaawansowane opcje** pole wyboru w dolnej części  **Mapowanie atrybutu** ekranu.

Aplikacje i systemy, które obsługują dostosowania listy atrybutów obejmują:

* SalesForce
* ServiceNow
* Dzień roboczy
* Usługa Azure Active Directory ([interfejsu API usługi Azure AD Graph domyślne atrybuty](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity) i katalog niestandardowych rozszerzeń są obsługiwane)
* Aplikacje, które obsługują [SCIM 2.0](https://tools.ietf.org/html/rfc7643), gdzie atrybuty zdefiniowane w [schematu core](https://tools.ietf.org/html/rfc7643) musi zostać dodany

>[!NOTE]
>Edytowanie listę obsługiwanych atrybutów jest zalecane tylko dla administratorów, którzy został dostosowany schematu z aplikacjami i systemami i korzystają z pierwszej ręki jak zdefiniowano ich atrybutów niestandardowych. Czasami wymaga znajomości interfejsów API i deweloperów narzędzi dostarczanych przez aplikację lub systemu. 

![Edytor][9]  

Podczas edytowania listę obsługiwanych atrybutów, dostępne są następujące właściwości:

* **Nazwa** — Nazwa systemowa atrybutu, zgodnie z definicją w schemacie obiektu docelowego. 
* **Typ** — typ danych atrybutu są przechowywane, zgodnie z definicją w schemacie obiektu docelowego. Może to być jeden z następujących czynności:
   * *Binarny* — atrybut zawiera dane binarne.
   * *Wartość logiczna* — atrybut ma wartość PRAWDA lub FAŁSZ.
   * *Data i godzina* — atrybut zawiera ciąg daty.
   * *Liczba całkowita* — atrybut zawiera liczbę całkowitą.
   * *Odwołanie* — atrybut zawiera identyfikator, który odwołuje się do wartości przechowywane w innej tabeli w aplikacji docelowej.
   * *Ciąg* — atrybut zawiera ciąg tekstowy. 
* **Klucz podstawowy?** — Czy ten atrybut jest zdefiniowany jako klucz podstawowy w schemacie obiektu docelowego.
* **Wymagane?** — Czy ten atrybut jest wymagany w aplikacji docelowej lub systemu.
* **Multi-value?** — Czy ten atrybut obsługuje wiele wartości.
* **Dokładnej?** — Czy wartości atrybutów są oceniane w taki sposób, z uwzględnieniem wielkości liter.
* **Wyrażenie interfejsu API** — nie należy używać, chyba że zalecił tak dokumentacji dla określonych inicjowania obsługi administracyjnej łącznika (na przykład produktu Workday).
* **Odwołanie do atrybutu obiektu** — Jeśli to jest atrybut typu odwołania, to menu służy do wybierania tabeli i atrybutów w aplikacji docelowej, która zawiera wartość skojarzoną z atrybutem. Na przykład jeśli istnieje atrybut o nazwie "Dział", którego wartość przechowywana odwołuje się do obiektu w osobnej tabeli "Działów", wybierz "Departments.Name". Należy pamiętać, że tabele odwołań i pól identyfikator podstawowego obsługiwane dla danej aplikacji są wstępnie skonfigurowane i obecnie nie można edytować za pomocą portalu Azure, ale mogą być edytowane przy użyciu [interfejsu API programu Graph](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes).

Aby dodać nowy atrybut, przewiń do końca listę obsługiwanych atrybutów, wypełnij pola powyżej przy użyciu podanych danych wejściowych i wybierz **Dodawanie atrybutu**. Wybierz **zapisać** po zakończeniu dodawania atrybutów. Następnie należy ponownie załadować **inicjowania obsługi administracyjnej** kartę dla nowych atrybutów stanie się dostępne w edytorze mapowanie atrybutu.

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>Przywracanie atrybutów domyślnych i mapowań atrybutów

Potrzebujesz zacznij od nowa i istniejące mapowania z powrotem do ich domyślny stan resetowania, można wybrać **Przywróć domyślne mapowania** pole wyboru i zapisać konfigurację. Ustawia wszystkie mapowania, tak jakby aplikacji po prostu dodane do dzierżawy usługi Azure AD z galerii aplikacji. 

Ta opcja wymusi skutecznie ponownej synchronizacji dla wszystkich użytkowników podczas inicjowania obsługi administracyjnej usługi. 

>[!IMPORTANT]
>Zdecydowanie zalecane jest **stanu udostępniania** można ustawić **poza** przed wywołaniem tej opcji.


## <a name="what-you-should-know"></a>Co należy wiedzieć

* Microsoft Azure AD zapewnia wydajne wykonania procesu synchronizacji. W środowisku zainicjowana tylko obiekty wymagające aktualizacji są przetwarzane podczas cyklu synchronizacji. 

* Aktualizowanie mapowań atrybutów ma wpływ na wydajność cyklu synchronizacji. Aktualizacja konfiguracji mapowanie atrybutu wymaga, aby wszystkie obiekty zarządzane go obliczyć ponownie. 

* Jest zalecanym najlepszym rozwiązaniem aby utrzymać liczbę kolejnych zmian do mapowania użytkownika atrybutów co najmniej.


## <a name="next-steps"></a>Kolejne kroki

* [Automatyzowanie użytkownika udostępniania/anulowania obsługi do aplikacji SaaS](active-directory-saas-app-provisioning.md)
* [Tworzenie wyrażeń na potrzeby mapowań atrybutów](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Filtry zakresu dla Inicjowanie obsługi użytkowników](active-directory-saas-scoping-filters.md)
* [Włączanie automatycznej aprowizacji użytkowników i grup z usługi Azure Active Directory do aplikacji przy użyciu SCIM](manage-apps/use-scim-to-provision-users-and-groups.md)
* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS](saas-apps/tutorial-list.md)

<!--Image references-->
[5]: ./media/active-directory-saas-customizing-attribute-mappings/21.png
[6]: ./media/active-directory-saas-customizing-attribute-mappings/22.png
[7]: ./media/active-directory-saas-customizing-attribute-mappings/23.png
[8]: ./media/active-directory-saas-customizing-attribute-mappings/24.png
[9]: ./media/active-directory-saas-customizing-attribute-mappings/25.PNG

