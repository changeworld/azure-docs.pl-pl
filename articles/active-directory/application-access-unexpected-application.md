---
title: Nieoczekiwany aplikacji na liście aplikacji | Dokumentacja firmy Microsoft
description: Jak wyświetlić wszystkie aplikacje w dzierżawie i zrozumieć, jak aplikacje pojawiają się na liście wszystkie aplikacje w aplikacjach dla przedsiębiorstw
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 6d8c1a251bcc4a7c0bb736df64c4c701a2fc748a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36333879"
---
# <a name="unexpected-application-in-my-applications-list"></a>Nieoczekiwany aplikacji na liście aplikacji

Ten artykuł ułatwia zrozumienie, jak aplikacje pojawiają się w sieci **wszystkie aplikacje** w obszarze **aplikacje dla przedsiębiorstw**. 

## <a name="how-to-see-all-applications-in-your-tenant"></a>Jak sprawdzić wszystkie aplikacje w Twojej dzierżawie

Aby wyświetlić wszystkie aplikacje w dzierżawie, należy użyć **filtru** sterowania, aby wyświetlić **wszystkie aplikacje** w obszarze **wszystkie aplikacje** listy. Wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **ko-administratora.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

6.  Kliknij opcję Użyj **filtru** kontroli nad **listę wszystkich aplikacji**.

7.  Na **filtru** ustawić okienku **Pokaż** opcji w celu **wszystkich aplikacji.**

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Dlaczego określonej aplikacji jest wyświetlany na liście wszystkie aplikacje

Gdy filtrowane do **wszystkie aplikacje**, **wszystkie aplikacje** **listy** przedstawiono każdy obiekt nazwy głównej usługi w dzierżawie. Obiekty nazwy głównej usługi mogą być wyświetlane na liście na różne sposoby:

1.  Po dodaniu dowolnej aplikacji w galerii aplikacji, w tym:

   1. **Azure AD galerii aplikacji** — aplikację, która została wstępnie zintegrowanych dla rejestracji jednokrotnej z usługą Azure AD

   2. **Aplikacje serwera Proxy aplikacji** — aplikacji działających w środowisku lokalnym, który chcesz zapewnić bezpieczne jednokrotnego do zewnętrznie

   3. **Niestandardowe opracowanych aplikacji** — aplikacji, który organizacja chce tworzenie aplikacji na platformie rozwoju aplikacji Azure AD, ale który nie istnieje jeszcze

   4. **Aplikacje inne niż galerii** — Przenoszenie własnych aplikacji! Wszelkie link sieci web, które mają lub dowolnej aplikacji, która renderuje pole nazwy użytkownika i hasła, obsługuje protokoły SAML lub OpenID Connect lub obsługuje SCIM, który chcesz zintegrować dla rejestracji jednokrotnej z usługą Azure AD.

2.  Gdy skorzystania lub zalogowaniu się do 3<sup>usług pulpitu zdalnego</sup> aplikacji zintegrowany z usługą Azure Active Directory. Przykładem jest [Smartsheet](https://app.smartsheet.com/b/home) lub [DocuSign](https://www.docusign.net/member/MemberLogin.aspx).

3.  Gdy skorzystania lub dodawanie licencję do użytkownika lub grupy do pierwszej aplikacji firm, takich jak [Microsoft Office 365](http://products.office.com/)

4.  Po dodaniu nowej rejestracji aplikacji, tworząc niestandardowe rozwinięte aplikacji przy użyciu [rejestru aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration)

5.  Po dodaniu nowej rejestracji aplikacji, tworząc niestandardowe rozwinięte aplikacji przy użyciu [portalu rejestracji aplikacji w wersji 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration#visit-the-microsoft-app-registration-portal)

6.  Po dodaniu aplikacji tworzony jest przy użyciu programu Visual Studio [metod uwierzytelniania ASP.net](http://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) lub [usług połączonych](http://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx)

7.  Po utworzeniu, obiekt główny usługi przy użyciu [modułu programu PowerShell usługi Azure AD](/powershell/azure/install-adv2?view=azureadps-2.0)

8.  Gdy zostanie [wyrażenia zgody na aplikację](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent) jako administrator, aby użyć danych w dzierżawie

9.  Gdy [użytkownik zgadza się na aplikacji](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent) wykorzystanie danych w Twojej dzierżawie

10. Po włączeniu pewnych usług, które przechowują dane w dzierżawie. Przykładem jest resetowania hasła, która ma formę zasad resetowania nazwy głównej usługi, aby zapisać hasło w bezpieczny sposób.

Aby uzyskać więcej informacji na temat sposobu aplikacji są dodawane do katalogu, przeczytaj [metody i przyczyny dodawania aplikacji do usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added).

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Usunąć przypisanie określonego użytkownika lub grupy do aplikacji

Aby usunąć przypisanie grupy do aplikacji lub użytkownika, wykonaj czynności opisane w [Usuń przypisanie użytkownika lub grupy z aplikacji przedsiębiorstwa w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) artykułu.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Chcę wyłączyć dostęp do aplikacji dla każdego użytkownika

Aby wyłączyć wszystkie logowania użytkownika do aplikacji, wykonaj czynności opisane w [wyłączyć logowania użytkowników dla aplikacji przedsiębiorstwa w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) artykułu.

## <a name="i-want-to-delete-an-application-entirely"></a>Chcę, aby całkowicie usunąć aplikację

Aby **usunąć aplikację**, wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **ko-administratora.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

  * Jeśli nie ma aplikacji ma tutaj będą wyświetlane, użyj **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu **wszystkich Aplikacje.**

6.  Wybierz aplikację, którą chcesz usunąć.

7.  Po załadowaniu aplikacji, kliknij przycisk **usunąć** ikony z najwyższym aplikacji **omówienie** okienka.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Chcę wyłączyć wszystkie operacje zgody przyszłych użytkownika do aplikacji

Wyłączanie zgody użytkownika dla całego katalogu uniemożliwić użytkownikom końcowym zgodę dowolnej aplikacji. Administratorzy mogą nadal oznacza zgodę na behalves użytkownika. Dowiedz się więcej o zgodę aplikacji i dlaczego może lub nie może być o zgodę, przeczytaj [zgody administratora i użytkownika opis](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent).

Aby **Wyłącz wszystkie operacje zgody użytkownika w przyszłości w katalogu cały**, wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **ustawienia użytkownika**.

6.  Wyłącz wszystkie operacje zgody użytkownika w przyszłości przez ustawienie **użytkownicy mogą zezwolić aplikacjom na dostęp do danych** Przełącz, aby **nr** i kliknij przycisk **zapisać** przycisku.

## <a name="next-steps"></a>Kolejne kroki
[Managing Applications with Azure Active Directory (Zarządzanie aplikacjami za pomocą usługi Azure Active Directory)](manage-apps/what-is-application-management.md)
