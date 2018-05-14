---
title: Użytkownicy GRANT B2B w usłudze Azure AD dostęp do aplikacji lokalnych | Dokumentacja firmy Microsoft
description: Pokazuje, jak umożliwiają chmury B2B użytkownikom dostępu do aplikacji lokalnych przy użyciu współpracy B2B usługi Azure AD.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/20/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 028bbb28c7091db3c3ebea321ca2e167b999949d
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications"></a>Użytkownicy GRANT B2B w usłudze Azure AD dostęp do aplikacji lokalnych

Jako organizacja korzysta z możliwości współpracy B2B usługi Azure Active Directory (Azure AD) z zaproszeniem dla gości z organizacji partnera do usługi Azure AD można teraz podasz tych użytkowników B2B dostęp do aplikacji lokalnych. Te aplikacje lokalnej można używać uwierzytelniania opartego na SAML lub zintegrowane uwierzytelnianie systemu Windows (IWA) z ograniczonego delegowania protokołu Kerberos (KCD).

## <a name="access-to-saml-apps"></a>Dostęp do aplikacji SAML

Jeśli aplikacja lokalnymi korzysta z uwierzytelniania opartego na SAML, można łatwo udostępnić te aplikacje do użytkowników współpracy B2B usługi Azure AD za pomocą portalu Azure.

Należy wykonać obie następujące czynności:

- Integrowanie aplikacji SAML przy użyciu szablonu z systemem innym niż galerii aplikacji, zgodnie z opisem w [konfigurowania rejestracji jednokrotnej do aplikacji, które nie znajdują się w galerii aplikacji usługi Azure Active Directory](active-directory-saas-custom-apps.md). Upewnij się, że możesz użyć dla **adres URL logowania** wartość.
-  Użyj serwera Proxy aplikacji usługi Azure AD do publikowania aplikacji lokalnej za pomocą **usługi Azure Active Directory** skonfigurowany jako źródło uwierzytelniania. Aby uzyskać instrukcje, zobacz [publikowanie aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](manage-apps/application-proxy-publish-azure-portal.md). 

   Po skonfigurowaniu **wewnętrznego adresu Url** ustawienie, użyj logowania jednokrotnego adres URL określony w szablonie aplikacji z systemem innym niż galerii. W ten sposób użytkownicy mogą korzystać z aplikacji z granicą organizacji. Serwer Proxy aplikacji wykonuje SAML logowania jednokrotnego dla aplikacji lokalnych.
 
   ![Wewnętrzny adres URL lokalnej aplikacji ustawienia i uwierzytelniania](media/active-directory-b2b-hybrid-cloud-to-on-premises/OnPremAppSettings.PNG)

## <a name="access-to-iwa-and-kcd-apps"></a>Dostęp do aplikacji IWA i KCD

Użytkownikom B2B dostęp do aplikacji lokalnych, które są zabezpieczone zintegrowane uwierzytelnianie systemu Windows i ograniczone delegowanie protokołu Kerberos, potrzebne są następujące składniki:

- **Uwierzytelnianie za pośrednictwem serwera Proxy aplikacji usługi Azure AD**. B2B użytkownicy muszą mieć możliwość uwierzytelniania do aplikacji lokalnych. Aby to zrobić, należy opublikować aplikacji lokalnych poprzez serwer Proxy aplikacji usługi Azure AD. Aby uzyskać więcej informacji, zobacz [rozpoczęcie pracy z serwerem Proxy aplikacji i zainstalować łącznik](manage-apps/application-proxy-enable.md) i [publikowanie aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](manage-apps/application-proxy-publish-azure-portal.md).
- **Autoryzacji za pośrednictwem obiektu użytkownika B2B w katalogu lokalnym**. Aplikacja musi mieć możliwość wykonywania kontroli dostępu użytkownika i udzielać dostępu do zasobów poprawne. IWA i KCD wymagają obiektu użytkownika w lokalnej Windows Server Active Directory do ukończenia tego autoryzacji. Zgodnie z opisem w [jak rejestracji jednokrotnej z KCD działa](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md#how-single-sign-on-with-kcd-works), serwer Proxy aplikacji wymaga tego obiektu użytkownika w celu personifikacji użytkownika i uzyskać token protokołu Kerberos do aplikacji. 

   B2B scenariusza użytkownika są dostępne, której można tworzyć obiektów użytkownika gościa, które są wymagane do autoryzacji w katalogu lokalnym dwóch metod:

   - Program Microsoft Identity Manager (MIM) i [agenta zarządzania MIM dla programu Microsoft Graph](#create-b2b-guest-user-objects-through-mim-preview). 
   - [Skrypt programu PowerShell](#create-b2b-guest-user-objects-through-a-script-preview). Przy użyciu skryptu jest bardziej lekkie rozwiązanie, które nie wymaga MIM. 

Na poniższym diagramie przedstawiono ogólne omówienie serwera Proxy aplikacji jak Azure AD i generowanie B2B obiektu użytkownika w lokalnym katalogu działają razem, udzielić dostępu do aplikacji lokalnych IWA i KCD B2B użytkowników. Ponumerowane kroki opisano szczegółowo poniżej diagramu.

![Diagram MIM i B2B rozwiązań skryptu](media/active-directory-b2b-hybrid-cloud-to-on-premises/MIMScriptSolution.PNG)

1.  Użytkownik z organizacji partnerskiej (dzierżawy firmy Fabrikam) zachęca się do dzierżawy firmy Contoso.
2.  Tworzony jest obiekt użytkownika gościa w dzierżawie Contoso (na przykład obiekt użytkownika za pomocą nazwy UPN guest_fabrikam.com#EXT#@contoso.onmicrosoft.com).
3.  Gość Fabrikam zostaną zaimportowane z Contoso za pośrednictwem MIM lub skrypt programu B2B PowerShell.
4.  Reprezentacja lub "podstawę" Fabrikam obiektu użytkownika gościa (gość EXT #) jest tworzony w katalogu lokalnym Contoso.com, za pośrednictwem MIM lub skrypt programu B2B PowerShell.
5.  Aplikacja uzyskuje dostęp do lokalnego użytkownika gościa, app.contoso.com.
6.  Żądanie uwierzytelniania jest autoryzowane za pośrednictwem serwera Proxy aplikacji, za pomocą ograniczonego delegowania protokołu Kerberos. 
7.  Ponieważ obiekt użytkownika gościa istnieje lokalnie, uwierzytelnianie zakończy się pomyślnie.

### <a name="lifecycle-management-policies"></a>Zasady zarządzania cyklem życia

Można zarządzać obiektami lokalnymi B2B użytkownika przy użyciu zasad zarządzania cyklem życia. Na przykład:

- Zasady uwierzytelniania wieloskładnikowego (MFA) dla użytkownika gościa można skonfigurować tak, aby uwierzytelnianie wieloskładnikowe jest używany podczas uwierzytelniania serwera Proxy aplikacji. Aby uzyskać więcej informacji, zobacz [dostępu warunkowego dla użytkowników współpracy B2B](active-directory-b2b-mfa-instructions.md).
- Wszelkie reklamy sponsorowane przeglądami dostępu, weryfikacji konta, itp., które są wykonywane na chmurze B2B użytkownika dotyczą użytkowników lokalnych. Na przykład usunięcie użytkownikiem w chmurze za pomocą zasad zarządzania cyklem życia lokalnego jest również usunąć użytkownika przez synchronizacji programu MIM lub za pomocą synchronizacji usługi Azure AD Connect. Aby uzyskać więcej informacji, zobacz [monitoruje zarządzanie dostępem gościa z dostępem do usługi Azure AD](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md).

### <a name="create-b2b-guest-user-objects-through-mim-preview"></a>Tworzenie obiektów użytkownika gościa B2B za pośrednictwem MIM (wersja zapoznawcza)

Aby uzyskać informacje o sposobie używania dodatku Service Pack 1 dla MIM 2016 i agenta zarządzania programu MIM dla programu Microsoft Graph do tworzenia obiektów użytkownika gościa w katalogu lokalnym, zobacz [współpracy między firmami (B2B) usługi Azure AD z pakietu Microsoft Identity Z dodatkiem SP1 Manager (MIM) 2016 przy użyciu serwera Proxy aplikacji Azure (publicznej wersji zapoznawczej)](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016-graph-b2b-scenario).

### <a name="create-b2b-guest-user-objects-through-a-script-preview"></a>Tworzenie obiektów użytkownika gościa B2B za pomocą skryptu (wersja zapoznawcza)

Brak przykładowy skrypt programu PowerShell dostępne, czy służy jako punkt początkowy do tworzenia obiektów użytkownika gościa w lokalnej usługi Active Directory.

Możesz pobrać skrypt i plik Readme z [Centrum pobierania](https://www.microsoft.com/download/details.aspx?id=51495). Wybierz **skrypt i plik Readme ściągania B2B usługi Azure AD użytkownicy na — prem.zip** pliku.

Przed użyciem skryptu, upewnij się, należy przejrzeć wymagania wstępne i istotne zagadnienia dotyczące skojarzony plik Readme. Ponadto zrozumieć skrypt jest udostępniana jedynie jako przykład. Zespół deweloperów lub partnera należy dostosować i przejrzeć skrypt przed jej uruchomieniem.

## <a name="license-considerations"></a>Uwagi dotyczące licencji

Upewnij się, że masz prawidłowe licencje dostępu klienta (CAL) dla gości zewnętrznych, którzy uzyskują dostęp do aplikacji lokalnych. Aby uzyskać więcej informacji, zobacz sekcję "Łączniki zewnętrzne" [licencji dostępu klienta i zarządzanie licencjami](https://www.microsoft.com/en-us/licensing/product-licensing/client-access-license.aspx). Zapoznaj się z przedstawicielem firmy Microsoft lub lokalnym sprzedawcą dotyczące określonych potrzeb licencjonowania.

## <a name="next-steps"></a>Kolejne kroki

- [Azure współpracy B2B usługi Active Directory dla organizacji hybrydowego](active-directory-b2b-hybrid-organizations.md)

- Omówienie programu Azure AD Connect zobacz [integrację katalogów lokalnych z usługą Azure Active Directory](connect/active-directory-aadconnect.md).

