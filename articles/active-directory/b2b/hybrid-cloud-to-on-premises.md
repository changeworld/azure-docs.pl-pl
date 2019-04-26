---
title: B2B udzielanie użytkownikom dostęp do aplikacji w środowisku lokalnym — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Pokazuje, jak zapewnić chmurze użytkownikom B2B dostępu do aplikacji lokalnych przy użyciu funkcji współpracy B2B usługi Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0e9536f009d478796bc9367a66630c02019dcd2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60412706"
---
# <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications"></a>Użytkowników B2B przydział w usłudze Azure AD dostęp do aplikacji w środowisku lokalnym

Jako organizacja, która używa funkcji współpracy B2B usługi Azure Active Directory (Azure AD), aby zaprosić użytkowników-gości z organizacji partnerskich do usługi Azure AD teraz możesz podać tych użytkowników B2B dostępu do aplikacji lokalnych. Te aplikacje w środowisku lokalnym za pomocą uwierzytelnianie oparte na protokole SAML lub zintegrowanego Windows Authentication (Zintegrowane) ograniczonego delegowania protokołu Kerberos (KCD).

## <a name="access-to-saml-apps"></a>Dostęp do aplikacji SAML

Jeśli aplikacji lokalnych korzysta z uwierzytelniania opartego na SAML, można łatwo udostępnić te aplikacje dla użytkowników współpracy B2B usługi Azure AD w witrynie Azure portal.

Należy wykonać obie z następujących czynności:

- Integracja aplikacji SAML przy użyciu szablonu aplikacji spoza galerii, zgodnie z opisem w [Konfigurowanie logowania jednokrotnego do aplikacji, które nie znajdują się w galerii aplikacji usługi Azure Active Directory](../manage-apps/configure-single-sign-on-non-gallery-applications.md). Upewnij się, że należy zwrócić uwagę na to, czego używasz dla **adres URL logowania** wartość.
-  Umożliwia publikowanie aplikacji w środowisku lokalnym przy użyciu serwera Proxy aplikacji usługi Azure AD **usługi Azure Active Directory** skonfigurowany jako źródło uwierzytelniania. Aby uzyskać instrukcje, zobacz [publikowania aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](../manage-apps/application-proxy-publish-azure-portal.md). 

   Po skonfigurowaniu **wewnętrzny adres Url** ustawienia, użyj logowania jednokrotnego adres URL, który określono w szablonie aplikacji spoza galerii. W ten sposób użytkownicy mogą korzystać z aplikacji poza granicami organizacji. Serwer Proxy aplikacji wykonuje SAML logowania jednokrotnego dla aplikacji w środowisku lokalnym.
 
   ![Zawiera wewnętrzny adres URL lokalnej aplikacji ustawienia i uwierzytelniania](media/hybrid-cloud-to-on-premises/OnPremAppSettings.PNG)

## <a name="access-to-iwa-and-kcd-apps"></a>Dostęp do aplikacji IWA i ograniczonego delegowania protokołu Kerberos

Aby zapewnić użytkownikom B2B dostępu do aplikacji lokalnych, które są zabezpieczone zintegrowane uwierzytelnianie Windows i ograniczone delegowanie protokołu Kerberos, potrzebne są następujące składniki:

- **Uwierzytelnianie za pośrednictwem serwera Proxy aplikacji usługi Azure AD**. Użytkowników B2B musi mieć możliwość uwierzytelniania do aplikacji w środowisku lokalnym. Aby to zrobić, należy opublikować aplikację w środowisku lokalnym za pośrednictwem serwera Proxy aplikacji usługi AD systemu Azure. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z usługą serwera Proxy aplikacji i zainstalować łącznik](../manage-apps/application-proxy-enable.md) i [publikowania aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](../manage-apps/application-proxy-publish-azure-portal.md).
- **Autoryzacja za pomocą obiektu użytkownika B2B, w katalogu lokalnym**. Aplikacja musi być mogą wykonywać sprawdzanie uprawnień dostępu użytkownika i udzielać dostępu do zasobów poprawne. IWA i ograniczonego delegowania protokołu Kerberos wymaga obiektu użytkownika w lokalnej Windows Server Active Directory do ukończenia tego autoryzacji. Zgodnie z opisem w [jak logowanie jednokrotne przy użyciu ograniczonego delegowania protokołu Kerberos działa](../manage-apps/application-proxy-configure-single-sign-on-with-kcd.md#how-single-sign-on-with-kcd-works), serwer Proxy aplikacji wymaga tego obiektu użytkownika w celu personifikacji użytkownika i uzyskać token protokołu Kerberos do aplikacji. 

   Scenariusz użytkownika B2B są dostępne, że można użyć do tworzenia obiektów użytkownika gościa, które są wymagane do autoryzacji w katalogu lokalnym dwóch metod:

   - Microsoft Identity Manager (MIM) i agenta zarządzania programu MIM dla programu Microsoft Graph. 
   - [Skrypt programu PowerShell](#create-b2b-guest-user-objects-through-a-script-preview). Za pomocą skryptu jest uproszczone rozwiązanie, które nie wymaga programu MIM. 

Na poniższym diagramie przedstawiono ogólne omówienie serwera Proxy aplikacji jak usługa Azure AD oraz jego generacji B2B obiektu użytkownika w lokalnym katalogu roboczego ze sobą, aby udzielić użytkownikom B2B dostępu do lokalnych aplikacji IWA i ograniczonego delegowania protokołu Kerberos. Kroki wymienione zostały szczegółowo opisane w poniższym diagramie.

![Diagram rozwiązania skryptu programu MIM i B2B](media/hybrid-cloud-to-on-premises/MIMScriptSolution.PNG)

1.  Użytkownik z organizacji partnerskiej (dzierżawa Fabrikam) jest proszona o dzierżawie Contoso.
2.  Obiekt użytkownika gościa jest tworzony w dzierżawie Contoso (na przykład obiekt użytkownika za pomocą nazwy UPN guest_fabrikam.com#EXT#@contoso.onmicrosoft.com).
3.  Gość Fabrikam jest importowany z firmy Contoso za pośrednictwem programu MIM lub skrypt programu B2B PowerShell.
4.  Reprezentacja lub "zużycie" Fabrikam obiektu użytkownika gościa (gość EXT #) jest tworzony w katalogu lokalnym, Contoso.com, za pośrednictwem programu MIM lub skrypt programu B2B PowerShell.
5.  Aplikacja uzyskuje dostęp do lokalnego użytkownika gościa, app.contoso.com.
6.  Żądanie uwierzytelniania jest autoryzowane przez serwer Proxy aplikacji, za pomocą ograniczonego delegowania protokołu Kerberos. 
7.  Ponieważ gości obiekt użytkownika znajduje się lokalnie, uwierzytelnianie zakończy się pomyślnie.

### <a name="lifecycle-management-policies"></a>Zasady zarządzania cyklem życia

Możesz zarządzać obiektów użytkownika B2B w środowisku lokalnym przy użyciu zasad zarządzania cyklem życia. Na przykład:

- Zasady uwierzytelniania wieloskładnikowego (MFA) dla użytkownika gościa można skonfigurować tak, aby usługa MFA jest używany podczas uwierzytelniania serwera Proxy aplikacji. Aby uzyskać więcej informacji, zobacz [dostęp warunkowy dla użytkowników współpracy B2B](conditional-access.md).
- Wszelkie dostęp sponsorowany przeglądów dostępu, weryfikacji konta, itp., które są wykonywane w chmurze B2B, po którym użytkownik ma zastosowanie do użytkowników w środowisku lokalnym. Na przykład usunięcie użytkownika chmury za pomocą zasad zarządzania cyklem życia użytkownika lokalnego również jest usunięte, usługa synchronizacji programu MIM lub za pomocą synchronizacji programu Azure AD Connect. Aby uzyskać więcej informacji, zobacz [przeglądy zarządzanie dostępem gości za pomocą usługi Azure AD access](../governance/manage-guest-access-with-access-reviews.md).

### <a name="create-b2b-guest-user-objects-through-mim"></a>Tworzenie obiektów użytkowników gości B2B przy użyciu programu MIM

Aby uzyskać informacje o tym, jak używać programu MIM 2016 Service Pack 1 i agenta zarządzania programu MIM dla programu Microsoft Graph w celu utworzenia obiektów użytkownika gościa w katalogu lokalnym, zobacz [współpracy business-to-business (B2B) usługi Azure AD za pomocą programu Microsoft Identity Manager (MIM) 2016 z dodatkiem SP1 za pomocą serwera Proxy aplikacji platformy Azure](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016-graph-b2b-scenario).

### <a name="create-b2b-guest-user-objects-through-a-script-preview"></a>Tworzenie obiektów użytkowników gości B2B za pomocą skryptu (wersja zapoznawcza)

Brak przykładowy skrypt programu PowerShell dostępny, można użyć jako punktu wyjścia do tworzenia obiektów użytkownika gościa w usłudze Active Directory w środowisku lokalnym.

Możesz pobrać skrypt i plik Readme z [Centrum pobierania](https://www.microsoft.com/download/details.aspx?id=51495). Wybierz **skrypt i plik Readme do ściągania usługi Azure AD B2B użytkowników na prem.zip** pliku.

Przed użyciem skryptu, upewnij się, należy przejrzeć wymagania wstępne i istotne zagadnienia w skojarzony plik Readme. Ponadto Dowiedz się, czy skrypt jest udostępniana jedynie jako przykład. Twój zespół deweloperów lub partnera należy dostosować i przejrzyj skrypt przed jej uruchomieniem.

## <a name="license-considerations"></a>Uwagi dotyczące licencji

Upewnij się, że masz prawidłowe licencje dostępu klienta (CAL) dla użytkowników zewnętrznych gości, którzy uzyskują dostęp aplikacje lokalne. Aby uzyskać więcej informacji, zobacz sekcję "Łączniki zewnętrzne" [licencji dostępu klienta i zarządzanie licencjami](https://www.microsoft.com/licensing/product-licensing/client-access-license.aspx). Zapoznaj się z przedstawicielem handlowym firmy Microsoft lub lokalnym sprzedawcą dotyczące konkretnych potrzeb licencjonowania.

## <a name="next-steps"></a>Kolejne kroki

- [Współpracy usługi Azure Active Directory B2B dla organizacji hybrydowych](hybrid-organizations.md)

- Omówienie programu Azure AD Connect, zobacz [integrowanie katalogów lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

