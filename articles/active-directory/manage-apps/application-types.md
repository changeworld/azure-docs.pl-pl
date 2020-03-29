---
title: Nieoczekiwane zastosowanie na mojej liście aplikacji | Dokumenty firmy Microsoft
description: Jak wyświetlić wszystkie aplikacje w dzierżawie i zrozumieć, jak aplikacje są wyświetlane na liście Wszystkie aplikacje w obszarze Aplikacje dla przedsiębiorstw
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1cb4eeb52d0680695bda266ad1a563b2ef5ee02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65781090"
---
# <a name="unexpected-application-in-my-applications-list"></a>Nieoczekiwana aplikacja na mojej liście aplikacji

Ten artykuł ułatwia zrozumienie, jak aplikacje są wyświetlane na liście **Wszystkie aplikacje** w obszarze **Aplikacje dla przedsiębiorstw**. 

## <a name="how-to-see-all-applications-in-your-tenant"></a>Jak wyświetlić wszystkie aplikacje w dzierżawie

Aby wyświetlić wszystkie aplikacje w dzierżawie, należy użyć **filtru** kontroli, aby wyświetlić **wszystkie aplikacje** w obszarze **wszystkie aplikacje** listy. Wykonaj następujące kroki:

1.  Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2.  Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4.  kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  kliknij pozycję **Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

6.  kliknij przycisk użyj **formantu Filtr** u góry **listy Wszystkie aplikacje**.

7.  W okienku **Filtruj** opcję **Pokaż** na **Wszystkie aplikacje.**

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Dlaczego określona aplikacja pojawia się na mojej liście wszystkich aplikacji?

Po przefiltrowaniu do **wszystkich aplikacji,** **lista** **Wszystkie aplikacje** pokazuje każdy obiekt jednostki usługi w dzierżawie. Obiekty jednostki usługi mogą pojawić się na tej liście na różne sposoby:

1. Po dodaniu dowolnej aplikacji z galerii aplikacji, w tym:

   1. **Aplikacje usługi Azure AD Gallery** — aplikacja, która została wstępnie zintegrowana dla logowania jednokrotnego za pomocą usługi Azure AD

   2. **Aplikacje proxy aplikacji** — aplikacja działająca w środowisku lokalnym, która ma zapewnić bezpieczne logowanie jednokrotne na zewnątrz

   3. **Aplikacje opracowane na zamówienie** — aplikacja, którą twoja organizacja chce opracować na platformie azure ad application development, ale która może jeszcze nie istnieć

   4. **Aplikacje spoza galerii** — przynieś własne aplikacje! Dowolne łącze internetowe lub dowolna aplikacja renderujca pola nazwy użytkownika i hasła obsługuje protokoły SAML lub OpenID Connect lub obsługuje scim, które chcesz zintegrować dla logowania jednokrotnego z usługą Azure AD.

2. Podczas rejestrowania się lub logowania się do aplikacji<sup>3-tej strony</sup> zintegrowanej z usługą Azure Active Directory. Jednym z przykładów jest [Smartsheet](https://app.smartsheet.com/b/home) lub [DocuSign](https://www.docusign.net/member/MemberLogin.aspx).

3. Podczas rejestrowania się lub dodawania licencji do użytkownika lub grupy do aplikacji innej firmy, takiej jak [Microsoft Office 365](https://products.office.com/)

4. Po dodaniu nowej rejestracji aplikacji przez utworzenie aplikacji opracowanej na zamówienie przy użyciu [rejestru aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration)

5. Po dodaniu nowej rejestracji aplikacji przez utworzenie aplikacji opracowanej na zamówienie za pomocą [portalu rejestracji aplikacji w wersji 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration)

6. Po dodaniu aplikacji, którą tworzysz przy użyciu [metod uwierzytelniania ASP.net](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) programu Visual Studio lub [połączonych usług](https://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx)

7. Podczas tworzenia obiektu jednostkowego usługi przy użyciu [modułu programu Azure AD PowerShell](/powershell/azure/install-adv2?view=azureadps-2.0)

8. Gdy [użytkownik wyraża zgodę na](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) wykorzystanie danych w dzierżawie przez aplikację jako administratora

9. Gdy [użytkownik wyraża zgodę na](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) korzystanie z danych w dzierżawie

10. Po włączeniu niektórych usług, które przechowują dane w dzierżawie. Jednym z przykładów jest resetowanie hasła, który jest modelowany jako jednostki usługi do bezpiecznego przechowywania zasad resetowania hasła.

Aby uzyskać więcej informacji na temat sposobu dodawania aplikacji do katalogu, przeczytaj [artykuł Jak i dlaczego aplikacje są dodawane do usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added).

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Chcę usunąć przypisanie określonego użytkownika lub grupy do aplikacji

Aby usunąć przypisanie użytkownika lub grupy do aplikacji, wykonaj kroki wymienione w [artykule Usuwanie przypisania użytkownika lub grupy z aplikacji przedsiębiorstwa w usłudze Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal)

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Chcę wyłączyć cały dostęp do aplikacji dla każdego użytkownika

Aby wyłączyć wszystkie logowania użytkowników do aplikacji, wykonaj kroki wymienione w [artykule Wyłącz logowanie użytkowników dla aplikacji dla przedsiębiorstwa w usłudze Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal)

## <a name="i-want-to-delete-an-application-entirely"></a>Chcę całkowicie usunąć aplikację

Aby **usunąć aplikację,** wykonaj następujące czynności:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5. kliknij pozycję **Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być pokazana w tym miejscu, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje** i ustaw opcję **Pokaż** na **Wszystkie aplikacje.**

6. Wybierz aplikację, którą chcesz usunąć.

7. Po załadowaniu aplikacji kliknij ikonę **Usuń** z górnego okienka **Przegląd** aplikacji.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Chcę wyłączyć wszystkie przyszłe operacje zgody użytkownika do dowolnej aplikacji

Wyłączenie zgody użytkownika na cały katalog uniemożliwia użytkownikom końcowym wyrażenie zgody na dowolną aplikację. Administratorzy mogą nadal wyrażać zgodę w imieniu użytkownika. Aby dowiedzieć się więcej o zgód aplikacji i dlaczego możesz lub nie chcesz wyrazić zgody, przeczytaj [Opis zgody użytkownika i administratora](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

Aby **wyłączyć wszystkie przyszłe operacje zgody użytkownika w całym katalogu,** wykonaj następujące kroki:

1.  Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4.  kliknij **pozycję Użytkownicy i grupy** w menu nawigacyjnym.

5.  Kliknij **pozycję Ustawienia użytkownika**.

6.  Wyłącz wszystkie przyszłe operacje zgody użytkownika, ustawiając, że **Użytkownicy mogą zezwolić aplikacjom na dostęp do przełącznika danych** na **Nie** i kliknij przycisk **Zapisz.**

## <a name="next-steps"></a>Następne kroki
[Zarządzanie aplikacjami za pomocą usługi Azure Active Directory](what-is-application-management.md)
