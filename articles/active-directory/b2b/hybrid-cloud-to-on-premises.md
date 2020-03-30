---
title: Udzielanie użytkownikom B2B dostępu do aplikacji lokalnych — usługa Azure AD
description: Pokazuje, jak przyznać użytkownikom chmury B2B dostęp do aplikacji lokalnych dzięki współpracy usługi Azure AD B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 098f464b6af5f10866403e1cd1549d571d883ac1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272796"
---
# <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications"></a>Przyznawanie użytkownikom B2B w usłudze Azure AD dostępu do aplikacji lokalnych

Jako organizacja korzystająca z funkcji współpracy B2B usługi Azure Active Directory (Azure AD) do zapraszania użytkowników-gości z organizacji partnerskich do usługi Azure AD, możesz teraz zapewnić tym użytkownikom B2B dostęp do aplikacji lokalnych. Te aplikacje lokalne mogą używać uwierzytelniania opartego na SAML lub zintegrowanego uwierzytelniania systemu Windows (IWA) z delegowaniem z ograniczeniami protokołu Kerberos (KCD).

## <a name="access-to-saml-apps"></a>Dostęp do aplikacji SAML

Jeśli aplikacja lokalna korzysta z uwierzytelniania opartego na SAML, możesz łatwo udostępnić te aplikacje użytkownikom współpracy usługi Azure AD B2B za pośrednictwem witryny Azure portal.

Należy wykonać obie czynności:

- Zintegruj aplikację SAML przy użyciu szablonu aplikacji spoza galerii, zgodnie z opisem w [konfigurowanie logowania jednokrotnego do aplikacji, które nie znajdują się w galerii aplikacji usługi Azure Active Directory](../manage-apps/configure-single-sign-on-non-gallery-applications.md). Pamiętaj, aby zwrócić uwagę na to, czego używasz do wartości **adresu URL logowania.**
-  Użyj usługi Azure AD Application Proxy, aby opublikować aplikację lokalną, z **usługą Azure Active Directory** skonfigurowany jako źródło uwierzytelniania. Aby uzyskać instrukcje, zobacz [Publikowanie aplikacji przy użyciu serwera proxy aplikacji usługi Azure AD](../manage-apps/application-proxy-publish-azure-portal.md). 

   Podczas konfigurowania ustawienia **Adres url wewnętrzny** użyj adresu URL logowania określonego w szablonie aplikacji spoza galerii. W ten sposób użytkownicy mogą uzyskać dostęp do aplikacji spoza granicy organizacji. Serwer proxy aplikacji wykonuje logowanie jednokrotne SAML dla aplikacji lokalnej.
 
   ![Pokazuje wewnętrzny adres URL i uwierzytelnianie ustawień aplikacji lokalnej](media/hybrid-cloud-to-on-premises/OnPremAppSettings.PNG)

## <a name="access-to-iwa-and-kcd-apps"></a>Dostęp do aplikacji IWA i KCD

Aby zapewnić użytkownikom B2B dostęp do aplikacji lokalnych zabezpieczonych za pomocą zintegrowanego uwierzytelniania systemu Windows i delegowania z ograniczeniami protokołu Kerberos, potrzebne są następujące składniki:

- **Uwierzytelnianie za pośrednictwem serwera proxy aplikacji usługi Azure AD**. Użytkownicy B2B muszą mieć możliwość uwierzytelniania w aplikacji lokalnej. Aby to zrobić, należy opublikować aplikację lokalną za pośrednictwem serwera proxy aplikacji usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do serwera proxy aplikacji i instalowanie łącznika](../manage-apps/application-proxy-enable.md) i publikowania aplikacji przy użyciu serwera proxy aplikacji usługi Azure [AD](../manage-apps/application-proxy-publish-azure-portal.md).
- **Autoryzacja za pośrednictwem obiektu użytkownika B2B w katalogu lokalnym**. Aplikacja musi mieć możliwość przeprowadzania kontroli dostępu użytkowników i udzielania dostępu do odpowiednich zasobów. IWA i KCD wymagają obiektu użytkownika w lokalnej usłudze Windows Server Active Directory w celu ukończenia tej autoryzacji. Jak opisano w [jak działa logowanie jednokrotne za pomocą KCD,](../manage-apps/application-proxy-configure-single-sign-on-with-kcd.md#how-single-sign-on-with-kcd-works)serwer proxy aplikacji potrzebuje tego obiektu użytkownika do personifikacji użytkownika i uzyskania tokenu Kerberos do aplikacji. 

   W przypadku scenariusza użytkownika B2B dostępne są dwie metody, których można użyć do utworzenia obiektów użytkownika-gościa, które są wymagane do autoryzacji w katalogu lokalnym:

   - Microsoft Identity Manager (MIM) i agent zarządzania programem MIM dla programu Microsoft Graph. 
   - [Skrypt programu PowerShell](#create-b2b-guest-user-objects-through-a-script-preview). Korzystanie ze skryptu jest bardziej odciążone rozwiązanie, które nie wymaga mim. 

Poniższy diagram zawiera omówienie wysokiego poziomu, jak usługi Azure AD Application Proxy i generowania obiektu użytkownika B2B w katalogu lokalnym współpracują ze sobą, aby udzielić użytkownikom B2B dostępu do lokalnych aplikacji IWA i KCD. Ponumerowane kroki są szczegółowo opisane poniżej diagramu.

![Diagram rozwiązań skryptów MIM i B2B](media/hybrid-cloud-to-on-premises/MIMScriptSolution.PNG)

1.  Użytkownik z organizacji partnerskiej (dzierżawy firmy Fabrikam) jest zapraszany do dzierżawy contoso.
2.  Obiekt użytkownika-gościa jest tworzony w dzierżawie Contoso (na przykład obiekt użytkownika o numerze@contoso.onmicrosoft.comUPN guest_fabrikam.com#EXT# ).
3.  Gość programu Fabrikam jest importowany z programu Contoso za pośrednictwem programu MIM lub skryptu programu B2B PowerShell.
4.  Reprezentacja lub "footprint" obiektu użytkownika gościa programu Fabrikam (Gość#EXT#) jest tworzony w katalogu lokalnym, Contoso.com, za pośrednictwem programu MIM lub za pośrednictwem skryptu programu B2B PowerShell.
5.  Użytkownik-gość uzyskuje dostęp do aplikacji lokalnej, app.contoso.com.
6.  Żądanie uwierzytelnienia jest autoryzowane za pośrednictwem serwera proxy aplikacji przy użyciu delegowania ograniczonego protokołu Kerberos. 
7.  Ponieważ obiekt użytkownika gościa istnieje lokalnie, uwierzytelnianie zakończy się pomyślnie.

### <a name="lifecycle-management-policies"></a>Zasady zarządzania cyklem życia

Lokalne obiekty użytkownika B2B można zarządzać za pomocą zasad zarządzania cyklem życia. Przykład:

- Zasady uwierzytelniania wieloskładnikowego (MFA) dla użytkownika gościa można skonfigurować, tak aby usługa MFA była używana podczas uwierzytelniania serwera proxy aplikacji. Aby uzyskać więcej informacji, zobacz [Dostęp warunkowy dla użytkowników współpracy B2B](conditional-access.md).
- Wszelkie sponsoring, przeglądy dostępu, weryfikacje kont itp., które są wykonywane na użytkowników B2B w chmurze, mają zastosowanie do użytkowników lokalnych. Na przykład jeśli użytkownik w chmurze zostanie usunięty za pośrednictwem zasad zarządzania cyklem życia, użytkownik lokalny jest również usuwany przez synchronizację MIM lub za pośrednictwem synchronizacji usługi Azure AD Connect. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem gościa za pomocą przeglądów dostępu usługi Azure AD](../governance/manage-guest-access-with-access-reviews.md).

### <a name="create-b2b-guest-user-objects-through-mim"></a>Tworzenie obiektów użytkownika-gościa B2B za pomocą mimu

Aby uzyskać informacje dotyczące używania programu MIM 2016 z dodatkiem Service Pack 1 i agenta zarządzania programem MIM dla programu Microsoft Graph do tworzenia obiektów użytkowników-gościa w katalogu lokalnym, zobacz [Współpraca między firmami usługi Azure AD (B2B) z dodatkiem SP1 programu Microsoft Identity Manager (MIM) 2016 za pomocą serwera proxy aplikacji platformy Azure](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016-graph-b2b-scenario).

### <a name="create-b2b-guest-user-objects-through-a-script-preview"></a>Tworzenie obiektów użytkownika-gościa B2B za pomocą skryptu (wersja zapoznawcza)

Dostępny jest przykładowy skrypt programu PowerShell, którego można użyć jako punktu wyjścia do tworzenia obiektów użytkownika-gościa w lokalnej usłudze Active Directory.

Skrypt i plik Readme można pobrać z [Centrum pobierania](https://www.microsoft.com/download/details.aspx?id=51495). Wybierz **skrypt i readme, aby przyciągnąć użytkowników usługi Azure AD B2B na plik prem.zip.**

Przed użyciem skryptu należy zapoznać się z wymaganiami wstępnymi i ważnymi zagadnieniami w skojarzonym pliku Readme. Ponadto należy zrozumieć, że skrypt jest udostępniany tylko jako przykład. Zespół deweloperów lub partner musi dostosować i przejrzeć skrypt przed jego uruchomieniem.

## <a name="license-considerations"></a>Zagadnienia dotyczące licencji

Upewnij się, że masz odpowiednie licencje dostępu klienta (CAL) dla zewnętrznych użytkowników-gości, którzy uzyskują dostęp do aplikacji lokalnych. Aby uzyskać więcej informacji, zobacz sekcję "Łączniki zewnętrzne" w sekcji [Licencje dostępu klienta i licencje zarządzania](https://www.microsoft.com/licensing/product-licensing/client-access-license.aspx). Skonsultuj się z przedstawicielem firmy Microsoft lub lokalnym sprzedawcą w sprawie konkretnych potrzeb licencyjnych.

## <a name="next-steps"></a>Następne kroki

- [Współpraca b2b usługi Azure Active Directory dla organizacji hybrydowych](hybrid-organizations.md)

- Aby zapoznać się z omówieniem usługi Azure AD Connect, zobacz [Integrowanie katalogów lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

