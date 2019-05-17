---
title: Nieoczekiwaną aplikację na liście aplikacji | Dokumentacja firmy Microsoft
description: Jak wyświetlać wszystkie aplikacje w Twojej dzierżawie i zrozumieć, jak aplikacje pojawiają się na liście wszystkich aplikacji w obszarze aplikacje dla przedsiębiorstw
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
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65781090"
---
# <a name="unexpected-application-in-my-applications-list"></a>Nieoczekiwaną aplikację na liście aplikacji

Ten artykuł ułatwia zrozumienie, jak aplikacje pojawiają się w Twojej **wszystkie aplikacje** w obszarze **aplikacje dla przedsiębiorstw**. 

## <a name="how-to-see-all-applications-in-your-tenant"></a>Jak wyświetlić wszystkie aplikacje w Twojej dzierżawie

Aby wyświetlić wszystkie aplikacje w Twojej dzierżawie, należy użyć **filtru** kontrolki **wszystkie aplikacje** w obszarze **wszystkie aplikacje** listy. Wykonaj następujące kroki:

1.  Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **Współadministratora.**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

6.  Kliknij opcję Użyj **filtru** formant w górnej części **listę wszystkich aplikacji**.

7.  Na **filtru** Ustaw okienku **Pokaż** opcję **wszystkie aplikacje.**

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Dlaczego określonej aplikacji jest wyświetlana na liście wszystkie aplikacje

Gdy filtrowana w celu **wszystkie aplikacje**, **wszystkie aplikacje** **listy** pokazuje każdego obiektu nazwy głównej usługi w dzierżawie. Obiekty nazwy głównej usługi mogą być wyświetlane na tej liście na różne sposoby:

1. Po dodaniu aplikacji z galerii aplikacji, w tym:

   1. **Aplikacje galerii usługi Azure AD** — aplikacji, który został wstępnie zintegrowanych, aby uzyskać logowanie jednokrotne z usługą Azure AD

   2. **Aplikacje serwera Proxy aplikacji** — aplikacja działająca w środowisku w środowisku lokalnym, które chcesz udostępnić bezpiecznego logowania jednokrotnego na zewnątrz

   3. **Niestandardowej aplikacji** — aplikacji, które Twoja organizacja chce programowanie na platformie Azure programowanie aplikacji usługi AD, ale który nie istnieje jeszcze

   4. **Aplikacje spoza galerii** — Przenoszenie własnych aplikacji! Dowolny link sieci web, który ma, każda aplikacja, która renderuje pole nazwy użytkownika i hasła, obsługuje protokoły SAML lub OpenID Connect lub obsługuje standard SCIM, którą chcesz zintegrować, aby logowanie jednokrotne z usługą Azure AD.

2. Podczas rejestracji w usłudze lub logowania się do 3<sup>usług pulpitu zdalnego</sup> aplikacji innej firmy zintegrowana z usługą Azure Active Directory. Przykładem jest [Smartsheet](https://app.smartsheet.com/b/home) lub [DocuSign](https://www.docusign.net/member/MemberLogin.aspx).

3. Podczas rejestracji w usłudze lub dodanie licencji do użytkownika lub grupy do pierwszej aplikacji innych firm, takich jak [Microsoft Office 365](https://products.office.com/)

4. Po dodaniu nowej rejestracji aplikacji, tworząc niestandardowej aplikacji przy użyciu [rejest aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration)

5. Po dodaniu nowej rejestracji aplikacji, tworząc niestandardowej aplikacji przy użyciu [portalu rejestracji aplikacji w wersji 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration)

6. Po dodaniu aplikacji opracowujesz przy użyciu programu Visual Studio [metod uwierzytelniania ASP.net](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) lub [podłączone usługi](https://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx)

7. Podczas tworzenia obiektu jednostki usługi przy użyciu [modułu Azure AD PowerShell](/powershell/azure/install-adv2?view=azureadps-2.0)

8. Gdy użytkownik [zgoda na aplikację](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) jako administrator na korzystanie z danych w Twojej dzierżawie

9. Gdy [użytkownik wyraża zgodę na aplikację](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) na korzystanie z danych w Twojej dzierżawie

10. Po włączeniu pewnych usług, które przechowują dane w dzierżawie. Przykładem jest resetowania hasła, który jest modelowane zgodnie z jednostki usługi do przechowywania hasła zasad resetowania bezpieczne.

Aby uzyskać szczegółowe informacje na temat sposobu aplikacje są dodawane do katalogu, przeczytaj [jak i dlaczego aplikacje są dodawane do usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added).

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Aby usunąć przypisanie określonego użytkownika lub grupy do aplikacji

Aby usunąć użytkownika lub przypisania grupy do aplikacji, wykonaj czynności opisane w [usunąć przypisania użytkownika lub grupy z aplikacji przedsiębiorstwa w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) artykułu.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Chcę wyłączyć dostęp do aplikacji dla każdego użytkownika

Aby wyłączyć wszystkie logowania użytkowników do aplikacji, wykonaj czynności opisane w [wyłączyć logowania użytkowników dla aplikacji przedsiębiorstwa w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) artykułu.

## <a name="i-want-to-delete-an-application-entirely"></a>Chcę, aby całkowicie usunąć aplikację

Aby **usunąć aplikację**, wykonaj następujące kroki:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **Współadministratora.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, którą chcesz usunąć.

7. Po załadowaniu aplikacji, kliknij przycisk **Usuń** ikonę z najważniejszych aplikacji **Przegląd** okienka.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Chcę wyłączyć wszystkie operacje zgody użytkownika przyszłych do dowolnej aplikacji

Wyłączanie zgody użytkownika dla całego katalogu uniemożliwić użytkownikom wyrażanie zgody na dowolnej aplikacji. Administratorzy nadal mogą wyrazić zgodę w imieniu użytkownika. Dowiedz się więcej na temat zgody aplikacji i dlatego może być lub może nie chcieć zgodę, przeczytaj [opis użytkowników i zgody administratora](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

Aby **Wyłącz wszystkie operacje zgody użytkownika przyszłych całego katalogu**, wykonaj następujące kroki:

1.  Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **ustawienia użytkownika**.

6.  Wyłącz wszystkie operacje zgody użytkownika w przyszłości, ustawiając **użytkownicy mogą zezwalać aplikacjom na dostęp do swoich danych na** Przełącz, aby **nie** i kliknij przycisk **Zapisz** przycisku.

## <a name="next-steps"></a>Kolejne kroki
[Managing Applications with Azure Active Directory (Zarządzanie aplikacjami za pomocą usługi Azure Active Directory)](what-is-application-management.md)
