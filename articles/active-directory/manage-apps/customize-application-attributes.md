---
title: Dostosowywanie mapowań atrybutów usługi Azure AD | Dokumentacja firmy Microsoft
description: Dowiedz się, jakie mapowań atrybutów dla aplikacji SaaS w usłudze Azure Active Directory są, jak możesz modyfikować je w celu zaradzenia potrzeb firmy.
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
ms.date: 09/09/2018
ms.author: celested
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 179bd519500d95755ef56331e26ff83d379e4c75
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2019
ms.locfileid: "55964887"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Dostosowywanie użytkownika aprowizacji mapowań atrybutów dla aplikacji SaaS w usłudze Azure Active Directory
Microsoft Azure AD zapewnia obsługę aprowizacji użytkowników do aplikacji SaaS innych firm, takich jak Salesforce, Google Apps i innych. Jeśli aprowizacja użytkowników dla aplikacji SaaS innych firm, włączone, witryny Azure portal steruje jego wartości atrybutów w formie mapowania atrybutów.

Istnieje wstępnie skonfigurowany zestaw atrybutów i mapowania atrybutów między obiektami użytkownika usługi Azure AD i obiektów użytkowników każdej aplikacji SaaS. Niektóre aplikacje zarządzania innymi typami obiektów oprócz użytkowników, takich jak grupy. <br> 
 Mapowania atrybutów domyślne można dostosować zgodnie z potrzebami firmy. Oznacza to, że można zmienić lub usunąć istniejące mapowania atrybutów lub utworzyć nowe mapowania atrybutów.
 
## <a name="editing-user-attribute-mappings"></a>Edytowanie mapowania atrybutów użytkownika

W portalu usługi Azure AD możesz korzystać z tej funkcji, klikając **mapowania** Konfiguracja **aprowizacji** w **Zarządzaj** części  **Aplikacja dla przedsiębiorstw**.


![SalesForce](./media/customize-application-attributes/21.png) 

Klikając **mapowania** konfiguracji otwiera powiązane **mapowanie atrybutu** ekranu. Brak mapowań atrybutów, które są wymagane przez aplikację SaaS, aby działo poprawnie. W przypadku wymaganych atrybutów **Usuń** funkcja jest niedostępna.


![SalesForce](./media/customize-application-attributes/22.png)

W powyższym przykładzie widać **Username** atrybut zarządzanego obiektu w usłudze Salesforce jest wypełniana przy użyciu **userPrincipalName** wartość połączonej Azure obiektu usługi Active Directory.

Można dostosować istniejący **mapowania atrybutów** , klikając mapowania. Spowoduje to otwarcie **Edytuj atrybut** ekranu.

![SalesForce](./media/customize-application-attributes/23.png)


### <a name="understanding-attribute-mapping-types"></a>Opis typów Mapowanie atrybutów
Za pomocą mapowania atrybutów można kontrolować, jak atrybuty są wypełniane w aplikacji SaaS innych firm. Istnieją cztery typy innego mapowania obsługiwane:

* **Bezpośrednie** — atrybut docelowy jest wypełniana wartością atrybutu połączonego obiektu w usłudze Azure AD.
* **Stałe** — atrybut docelowy jest wypełniana przy użyciu określonych parametrów określono.
* **Wyrażenie** — atrybut docelowy jest wypełniana na podstawie wyniku wyrażenia podobne do skryptu. 
  Aby uzyskać więcej informacji, zobacz [pisania wyrażeń do mapowania atrybutów w usłudze Azure Active Directory](functions-for-customizing-application-data.md).
* **Brak** — atrybut docelowy zostanie pozostawiony zostały zmodyfikowane. Jednak w przypadku kiedykolwiek pusty atrybut docelowy jest wypełniana wartością domyślną, który określisz.

Oprócz tych czterech typów podstawowych, niestandardowe mapowania atrybutów wspiera koncepcję opcjonalny **domyślne** przypisanie wartości. Przypisanie wartości domyślne gwarantuje, że atrybut docelowy jest wypełniona wartości, które znajduje się żadna wartość w usłudze Azure AD, ani w obiekcie docelowym. Najbardziej typowe konfiguracji jest to pole puste.


### <a name="understanding-attribute-mapping-properties"></a>Opis właściwości Mapowanie atrybutów

W poprzedniej sekcji możesz już zostały wprowadzone do właściwości typu atrybutu mapowania.
Oprócz tej właściwości mapowania atrybutów obsługują następujące atrybuty:

- **Atrybut źródłowy** — atrybut użytkownika z systemu źródłowego (przykład: Azure Active Directory).
- **Atrybut docelowy** — atrybut użytkownika w systemie docelowym (przykład: ServiceNow).
- **Zgodne obiekty korzystające z tego atrybutu** — czy to mapowanie powinien być używany do jednoznacznego identyfikowania użytkowników między systemami źródłowym i docelowym. Jest to zazwyczaj ustawiana na atrybut userPrincipalName lub wiadomości e-mail w usłudze Azure AD, która zwykle jest mapowana do pola nazwy użytkownika w aplikacji docelowej.
- **Pierwszeństwo dopasowania** — wiele pasujące atrybuty można ustawić. Jeśli dostępnych jest wiele, są one obliczane w kolejności, zdefiniowanych przez to pole. Gdy tylko zostanie znalezione dopasowanie, żadne dodatkowe dopasowania atrybuty są oceniane.
- **Zastosuj to mapowanie**
    - **Zawsze** — Zastosuj to mapowanie na obu tworzenia użytkownika i aktualizowanie działań
    - **Tylko podczas tworzenia** — Zastosuj to mapowanie tylko w akcji tworzenia użytkownika


## <a name="editing-group-attribute-mappings"></a>Mapowania atrybutów grupy do edycji

Wybranej liczby aplikacji, takich jak usługi ServiceNow, Box i Google Apps obsługuje możliwość inicjowania obsługi grupy obiektów, oprócz obiektów użytkownika. Obiekty grupy może zawierać właściwości grupy, takie jak nazwy wyświetlane i wiadomości e-mail aliasy, oprócz członków grupy.

![ServiceNow](./media/customize-application-attributes/24.png)

Grupy inicjowania obsługi można opcjonalnie włączać lub wyłączać, wybierając mapowania grup w ramach **mapowania**i ustawienie **włączone** na odpowiednią opcję w **mapowanie atrybutu** ekranu.

Atrybuty aprowizowany w ramach grupy obiektów można dostosować w taki sam sposób, jak obiekty użytkownika, opisanych powyżej. 

>[!TIP]
>Inicjowanie obsługi obiektów grupy (właściwości i elementów członkowskich) to odrębne koncepcji z [Przypisywanie grup](assign-user-or-group-access-portal.md) do aplikacji. Istnieje możliwość przypisania grupy do aplikacji, ale tylko aprowizować obiektów użytkowników znajdujących się w grupie. Obsługa administracyjna obiektów dla całej grupy nie należy używać grup w przypisaniach.


## <a name="editing-the-list-of-supported-attributes"></a>Listę obsługiwanych atrybutów do edycji

Atrybuty użytkownika, obsługiwane dla danej aplikacji są wstępnie skonfigurowane. Zarządzanie użytkownikami w większości aplikacji interfejsów API nie obsługują wykrywania schematu, w związku z tym usługa aprowizacji usługi Azure AD nie jest w stanie można dynamicznie wygenerować listę atrybutów obsługiwane przez wykonywanie wywołań do aplikacji. 

Jednak niektóre aplikacje obsługują atrybutów niestandardowych. Aby dla usługi Azure AD, usługa aprowizacji mieć możliwość odczytu i zapisu do atrybutów niestandardowych, należy podać ich definicje do usługi Azure portal przy użyciu **Pokaż opcje zaawansowane** pole wyboru w dolnej części  **Mapowanie atrybutów** ekranu.

Aplikacje i systemy, które obsługują dostosowania listy atrybutów to:

* SalesForce
* ServiceNow
* Dzień roboczy
* Usługa Azure Active Directory ([atrybutów domyślnych interfejsu API usługi Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity) i rozszerzenia katalogów niestandardowych są obsługiwane)
* Aplikacje, które obsługują [2.0 Standard SCIM](https://tools.ietf.org/html/rfc7643), w którym atrybuty zdefiniowane w [schematu core](https://tools.ietf.org/html/rfc7643) muszą zostać dodane

>[!NOTE]
>Edytowanie listy atrybutów obsługiwane jest zalecane tylko dla administratorów, którzy dostosowali schematu swoich aplikacji i systemów i dysponują wiedzą pierwszej ręki na temat sposobu ich atrybuty niestandardowe zostały zdefiniowane. Czasami wymaga to znajomość interfejsów API i narzędzia dla deweloperów aplikacji lub systemu. 

![Edytor](./media/customize-application-attributes/25.png) 

Podczas edytowania listę obsługiwanych atrybutów, dostępne są następujące właściwości:

* **Nazwa** — Nazwa systemowa atrybut, zgodnie z definicją w schemacie obiektu docelowego. 
* **Typ** — typ danych atrybutu są przechowywane, zgodnie z definicją w schemacie obiektu docelowego. Może to być jeden z następujących czynności:
   * *Binarny* — atrybut zawiera dane binarne.
   * *Wartość logiczna* — atrybut zawiera wartość PRAWDA lub FAŁSZ.
   * *Data i godzina* — atrybut zawiera ciąg daty.
   * *Liczba całkowita* — atrybut zawiera liczbę całkowitą.
   * *Odwołanie* — atrybut zawiera identyfikator, który odwołuje się do wartości przechowywane w innej tabeli w aplikacji docelowej.
   * *Ciąg* — atrybut zawiera ciąg tekstowy. 
* **Klucz podstawowy?** -Czy atrybut jest zdefiniowany jako klucz podstawowy w schemacie obiektu docelowego.
* **Wymagane?** -Czy atrybut jest wymagany do wypełnienia w aplikacji docelowej lub w systemie.
* **Multi-value?** -Czy ten atrybut obsługuje wiele wartości.
* **Dokładne dopasowanie wielkości liter?** -Czy wartości atrybutów są obliczane w taki sposób, wielkość liter.
* **Wyrażenie interfejsu API** — nie jest używany, o ile nie zdecyduje, aby to zrobić przez w dokumentacji dla określonego łącznika inicjowania obsługi administracyjnej (na przykład Workday).
* **Odwołanie do atrybutu obiektu** — Jeśli to jest odwołanie do atrybutu typu, to menu umożliwia wybranie tabeli i atrybutów w aplikacji docelowej, która zawiera wartość skojarzoną z atrybutem. Na przykład w przypadku atrybutu o nazwie "Dział", którego przechowywana wartość odwołuje się do obiektu, w osobnej tabeli "Działów" Wybierz "Departments.Name". Należy zauważyć, że tabele odwołań i pól identyfikator podstawowego obsługiwane dla danej aplikacji są wstępnie skonfigurowane i obecnie nie można edytować za pomocą witryny Azure portal, ale można edytować za pomocą [interfejsu API programu Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes).

Aby dodać nowy atrybut, przewiń do końca listę obsługiwanych atrybutów, wypełnij pola powyżej przy użyciu podanych danych wejściowych, a wybierz **Dodawanie atrybutu**. Wybierz **Zapisz** po zakończeniu dodawania atrybutów. Następnie konieczne będzie ponowne załadowanie **aprowizacji** kartę dla nowych atrybutów, które staną się dostępne w edytorze mapowanie atrybutu.

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>Przywracanie domyślnych atrybutów i mapowania atrybutów

Potrzebujesz zacznij od nowa w celu resetowania istniejące mapowania z powrotem do stanu domyślnego, możesz wybrać **przywrócić domyślne mapowania** pole wyboru i zapisać konfigurację. Ustawia wszystkie mapowania, tak, jakby aplikacja miała właśnie został dodany do dzierżawy usługi Azure AD za pomocą galerii aplikacji. 

Wybranie tej opcji wymusi skutecznie ponownej synchronizacji wszystkich użytkowników po uruchomieniu usługi aprowizacji. 

>[!IMPORTANT]
>Zdecydowanie zalecane jest, **stan aprowizacji** można ustawić **poza** przed wywołaniem tej opcji.


## <a name="what-you-should-know"></a>Co należy wiedzieć

* Microsoft Azure AD oferuje wydajne implementację procesu synchronizacji. W środowisku zainicjowany tylko te obiekty, które wymagających aktualizacji są przetwarzane podczas cyklu synchronizacji. 

* Aktualizowanie mapowań atrybutów ma wpływ na wydajność cykl synchronizacji. Aktualizacja konfiguracji mapowanie atrybutu wymaga wszystkie obiekty zarządzane go obliczyć ponownie. 

* Jest zalecanym najlepszym rozwiązaniem jest przechowywać swoje mapowania atrybutów, co najmniej liczbę kolejnych zmian.


## <a name="next-steps"></a>Kolejne kroki

* [Automatyzowanie użytkownika aprowizacji/Deprovisioning do aplikacji SaaS](user-provisioning.md)
* [Pisanie wyrażeń do mapowania atrybutów](functions-for-customizing-application-data.md)
* [Filtrów określania zakresu na potrzeby aprowizacji użytkownika](define-conditional-rules-for-provisioning-user-accounts.md)
* [Włączanie automatycznej aprowizacji użytkowników i grup z usługi Azure Active Directory do aplikacji przy użyciu SCIM](use-scim-to-provision-users-and-groups.md)
* [Lista samouczków dotyczących integrowania aplikacji SaaS](../saas-apps/tutorial-list.md)


