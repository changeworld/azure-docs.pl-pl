---
title: Dostosowywanie oświadczeń wystawionych w tokenie języka SAML dla aplikacji dla przedsiębiorstw w usłudze Azure AD | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dostosowywanie oświadczeń wystawionych w tokenie języka SAML dla aplikacji korporacyjnych w usłudze Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2018
ms.author: celested
ms.reviewer: luleon, jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9396fbc470f25e3cf6fad883ab525af1f96e96a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56188753"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Instrukcje: Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw

Już dzisiaj usługi Azure Active Directory (Azure AD) obsługuje funkcję logowania jednokrotnego na z większością aplikacji przedsiębiorstwa, w tym aplikacje, zarówno wstępnie zintegrowane w galerii aplikacji Azure AD, jak również aplikacji niestandardowych. Jeśli użytkownik uwierzytelnia się do aplikacji za pomocą usługi Azure AD przy użyciu protokołu SAML 2.0, usługi Azure AD wysyła token do aplikacji (za pośrednictwem metody POST protokołu HTTP). A następnie ją sprawdza poprawność tokenu i używa go do logowania użytkownika zamiast monitowania o nazwę użytkownika i hasło. Te tokeny SAML zawierają informacje o użytkowniku, znane jako "oświadczenia".

A *oświadczenia* informacje informacją przez dostawcę tożsamości użytkownika w tokenie są wydania dla tego użytkownika. W [tokenu SAML](https://en.wikipedia.org/wiki/SAML_2.0), tych danych jest zwykle są zawarte w instrukcji atrybutów SAML. Unikatowy identyfikator użytkownika jest zazwyczaj reprezentowany w podmiocie SAML, nazywany również jako identyfikator nazwy.

Domyślnie usługa Azure AD wystawia SAML token do aplikacji, która zawiera NameIdentifier roszczenia z wartością nazwy użytkownika (zwane również główną nazwę użytkownika) w usłudze Azure AD. Ta wartość może jednoznacznie zidentyfikować użytkownika. SAML token zawiera również dodatkowe oświadczenia, zawierającego adres e-mail użytkownika, imię i nazwisko.

Aby wyświetlić lub edytować oświadczeń wystawionych w tokenie SAML do aplikacji, Otwórz aplikację w witrynie Azure portal. Następnie wybierz pozycję **Wyświetl i Edytuj wszystkie inne atrybuty użytkownika** pola wyboru w **atrybutów użytkownika** części aplikacji.

![Sekcję atrybutów użytkownika][1]

Istnieją dwie możliwe przyczyny, dlaczego może być konieczne edytowanie oświadczeń wystawionych w tokenie SAML:
* Aplikacja została zapisana wymaga innego zestawu oświadczeń identyfikatorów URI lub wartości oświadczeń.
* Aplikacja została wdrożona w sposób, który wymaga NameIdentifier roszczenia inne niż nazwa użytkownika (zwane również główną nazwę użytkownika) przechowywanych w usłudze Azure AD.

Można edytować domyślnych wartości oświadczenia. Wybierz wiersz dotyczący oświadczenia w tabeli atrybuty tokenu języka SAML. Spowoduje to otwarcie **Edytuj atrybut** sekcji, a następnie edytować nazwy oświadczenia, wartości i skojarzony z oświadczeniem przestrzeni nazw.

![Edytowanie atrybutu użytkownika][2]

Można również usunąć oświadczeń (inne niż NameIdentifier) za pomocą menu kontekstowego, co spowoduje otwarcie, klikając **...**  ikony. Możesz również dodać nowy oświadczeń przy użyciu **Dodaj atrybut** przycisku.

![Edytowanie atrybutu użytkownika][3]

## <a name="editing-the-nameidentifier-claim"></a>Edytowanie oświadczenia NameIdentifier

Aby rozwiązać problem, gdzie aplikacja została wdrożona przy użyciu innej nazwy użytkownika, wybierz **identyfikator użytkownika** listy rozwijanej **atrybutów użytkownika** sekcji. Ta akcja wyświetlanie okna dialogowego przy użyciu kilku różnych opcji:

![Edytowanie atrybutu użytkownika][4]

### <a name="attributes"></a>Atrybuty

Wybierz żądane źródło `NameIdentifier` (lub NameID) oświadczenia. Możesz wybrać spośród następujących opcji.

| Name (Nazwa) | Opis |
|------|-------------|
| Email | Adres e-mail użytkownika |
| userprincipalName | Główna nazwa użytkownika (UPN) |
| onpremisessamaccount | Nazwa konta SAM, który jest synchronizowany ze środowiska lokalnego usługi Azure AD |
| Identyfikator obiektu | Identyfikator obiektu użytkownika w usłudze Azure AD |
| employeeID | EmployeeID użytkownika |
| Rozszerzenia katalogów | Rozszerzenia katalogów [synchronizowanych z lokalnej przy użyciu usługi Azure AD Connect Sync Active Directory.](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Atrybuty rozszerzenia 1 – 15 | W środowisku lokalnym atrybutów rozszerzenia, używanej do rozszerzania schematu usługi Azure AD |

### <a name="transformations"></a>Przekształcenia

Można również użyć funkcji przekształcenia oświadczeń specjalne.

| Funkcja | Opis |
|----------|-------------|
| **ExtractMailPrefix()** | Usuwa sufiks domeny od adresu e-mail, nazwa konta SAM lub główną nazwę użytkownika. Spowoduje to wyodrębnienie tylko pierwszą część nazwy użytkownika, są przekazywane (na przykład "joe_smith" zamiast joe_smith@contoso.com). |
| **join()** | Dołącza atrybut o zweryfikowanej domeny. Wartość identyfikatora wybranego użytkownika ma domenę, wyodrębnia nazwę użytkownika, aby dołączyć wybranego zweryfikowanej domeny. Na przykład, jeśli wybierzesz adres e-mail (joe_smith@contoso.com) jako wartość identyfikatora użytkownika i wybierz contoso.onmicrosoft.com jako zweryfikowanej domeny, spowoduje to joe_smith@contoso.onmicrosoft.com. |
| **ToLower()** | Konwertuje znaki wybranego atrybutu na małe litery. |
| **ToUpper()** | Konwertuje znaki wybranego atrybutu na wielkie litery. |

## <a name="adding-claims"></a>Dodawanie oświadczeń

Podczas dodawania roszczenia, można określić nazwę atrybutu, (które ściśle nie należy wykonywać wzorzec identyfikatora URI, zgodnie z specyfikacja języka SAML). Ustaw wartość na dowolny atrybut użytkownika, który jest przechowywany w katalogu lub użyj wartości zawartość jako statyczny wpis dla wszystkich użytkowników w Twojej organizacji.

![Dodaj atrybut użytkownika][7]

Na przykład trzeba będzie wysłać działu, w której należy użytkownik w organizacji jako oświadczenia (na przykład, sprzedaż). Wprowadź nazwę oświadczenia, zgodnie z oczekiwaniami przez aplikację, a następnie wybierz **user.department** jako wartość.

> [!NOTE]
> Jeśli dla danego użytkownika nie ma wartości przechowywane dla wybranego atrybutu, to roszczenie nie jest on wystawionych w tokenie.

> [!TIP]
> **User.onpremisesecurityidentifier** i **user.onpremisesamaccountname** są obsługiwane tylko podczas synchronizowania danych użytkownika z lokalnej usługi Active Directory przy użyciu [usługi Azure AD Połącz narzędzie](../hybrid/whatis-hybrid-identity.md).

## <a name="restricted-claims"></a>Ograniczone oświadczeń

Obejmuje niektóre oświadczenia ograniczeniami SAML. Jeśli dodasz te oświadczenia, następnie usługi Azure AD nie będzie wysyłać te oświadczenia. Poniżej przedstawiono SAML ograniczony zestaw oświadczeń:

    | Typ oświadczenia (identyfikator URI) |
    | ------------------- |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/expired |
    | http://schemas.microsoft.com/identity/claims/accesstoken |
    | http://schemas.microsoft.com/identity/claims/openid2_id |
    | http://schemas.microsoft.com/identity/claims/identityprovider |
    | http://schemas.microsoft.com/identity/claims/objectidentifier |
    | http://schemas.microsoft.com/identity/claims/puid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier[MR1] |
    | http://schemas.microsoft.com/identity/claims/tenantid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod |
    | http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/groups |
    | http://schemas.microsoft.com/claims/groups.link |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/role |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/wids |
    | http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant |
    | http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown |
    | http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged |
    | http://schemas.microsoft.com/2014/03/psso |
    | http://schemas.microsoft.com/claims/authnmethodsreferences |
    | http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier |
    | http://schemas.microsoft.com/identity/claims/scope |

## <a name="next-steps"></a>Kolejne kroki

* [Zarządzanie aplikacjami w usłudze Azure AD](../manage-apps/what-is-application-management.md)
* [Konfigurowanie logowania jednokrotnego w aplikacjach, które nie znajdują się w galerii aplikacji usługi Azure AD](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Rozwiązywanie problemów z opartej na SAML logowania jednokrotnego](howto-v1-debug-saml-sso-issues.md)

<!--Image references-->
[1]: ./media/active-directory-saml-claims-customization/user-attribute-section.png
[2]: ./media/active-directory-saml-claims-customization/edit-claim-name-value.png
[3]: ./media/active-directory-saml-claims-customization/delete-claim.png
[4]: ./media/active-directory-saml-claims-customization/user-identifier.png
[5]: ./media/active-directory-saml-claims-customization/extractemailprefix-function.png
[6]: ./media/active-directory-saml-claims-customization/join-function.png
[7]: ./media/active-directory-saml-claims-customization/add-attribute.png
