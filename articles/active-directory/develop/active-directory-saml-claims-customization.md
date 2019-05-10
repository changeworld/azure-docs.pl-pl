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
ms.date: 04/03/2019
ms.author: celested
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b137b8cd4e3a2b7a308170904e9b3d09b11137f9
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231349"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Instrukcje: Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw

Obecnie usługi Azure Active Directory (Azure AD) obsługuje logowanie jednokrotne (SSO) z większością aplikacji przedsiębiorstwa, w tym aplikacje, zarówno wstępnie zintegrowane w galerii aplikacji Azure AD, jak również aplikacji niestandardowych. Jeśli użytkownik uwierzytelnia się do aplikacji za pomocą usługi Azure AD przy użyciu protokołu SAML 2.0, usługi Azure AD wysyła token do aplikacji (za pośrednictwem metody POST protokołu HTTP). A następnie ją sprawdza poprawność tokenu i używa go do logowania użytkownika zamiast monitowania o nazwę użytkownika i hasło. Te tokeny SAML zawierają informacje o użytkowniku, znane jako *oświadczeń*.

A *oświadczenia* informacje informacją przez dostawcę tożsamości użytkownika w tokenie są wydania dla tego użytkownika. W [tokenu SAML](https://en.wikipedia.org/wiki/SAML_2.0), tych danych jest zwykle są zawarte w instrukcji atrybutów SAML. Unikatowy identyfikator użytkownika jest zazwyczaj reprezentowany w podmiocie SAML, nazywany również jako identyfikator nazwy.

Domyślnie usługa Azure AD wystawia SAML token do aplikacji, która zawiera `NameIdentifier` oświadczenia o wartości nazwy użytkownika (znany także jako nazwa główna użytkownika) w usłudze Azure AD, która może jednoznacznie zidentyfikować użytkownika. SAML token zawiera również dodatkowe oświadczenia, zawierającego adres e-mail użytkownika, imię i nazwisko.

Aby wyświetlić lub edytować oświadczeń wystawionych w tokenie SAML do aplikacji, Otwórz aplikację w witrynie Azure portal. Następnie otwórz **atrybutów użytkowników i oświadczeń** sekcji.

![Sekcja atrybutów użytkowników i oświadczeń](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

Istnieją dwie możliwe przyczyny, dlaczego może być konieczne edytowanie oświadczeń wystawionych w tokenie SAML:

* Aplikacja wymaga `NameIdentifier` lub NameID uważają się coś innego niż nazwa użytkownika (lub główna nazwa użytkownika), przechowywane w usłudze Azure AD.
* Aplikacja została zapisana wymaga innego zestawu oświadczeń identyfikatorów URI lub wartości oświadczeń.

## <a name="editing-nameid"></a>Edytowanie NameID

Aby edytować NameID (wartość nazwy identyfikatora):

1. Otwórz **nazwę wartości identyfikatora** strony.
1. Wybierz atrybut lub przekształcenie, które chcesz zastosować do atrybutu. Opcjonalnie można określić format ma on oświadczenia NameID mieć.

   ![Edytuj wartość NameID (identyfikator nazwy)](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>Format identyfikatora NameID

Jeśli żądanie języka SAML zawiera element NameIDPolicy w określonym formacie, usługa Azure AD będzie honorować formatu w żądaniu.

Jeśli żądanie języka SAML nie zawiera elementu dla NameIDPolicy, usługi Azure AD będzie wystawiać NameID w formacie, które określisz. Jeśli format nie jest określony, usługi Azure AD będzie używać domyślnego formatu źródła skojarzone z źródłem oświadczeń wybrane.

Z **format identyfikatora nazwy wybierz** listy rozwijanej można wybrać jedną z następujących opcji.

| Format identyfikatora NameID | Opis |
|---------------|-------------|
| **Domyślne** | Usługa Azure AD użyje domyślnego formatu źródła. |
| **Trwałe** | Usługa Azure AD będzie używać trwały jako format identyfikatora NameID. |
| **EmailAddress** | Usługa Azure AD będzie używać EmailAddress jako format identyfikatora NameID. |
| **Nie określono tego parametru** | Usługa Azure AD będzie używać nieokreślony jako format identyfikatora NameID. |
| **Przejściowe** | Usługa Azure AD będzie używać przejściowy, jako format identyfikatora NameID. |

Aby dowiedzieć się więcej o atrybucie NameIDPolicy, zobacz [protokołu SAML rejestracji jednokrotnej](single-sign-on-saml-protocol.md).

### <a name="attributes"></a>Atrybuty

Wybierz żądane źródło `NameIdentifier` (lub NameID) oświadczenia. Możesz wybrać spośród następujących opcji.

| Name (Nazwa) | Opis |
|------|-------------|
| Poczta e-mail | Adres e-mail użytkownika |
| userprincipalName | Główna nazwa użytkownika (UPN) użytkownika |
| onpremisessamaccount | Nazwa konta SAM, który jest synchronizowany ze środowiska lokalnego usługi Azure AD |
| Identyfikator obiektu | Identyfikator obiektu użytkownika w usłudze Azure AD |
| EmployeeID | EmployeeID użytkownika |
| Rozszerzenia katalogów | Rozszerzenia katalogów [synchronizowanych z lokalnej przy użyciu usługi Azure AD Connect Sync Active Directory.](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Atrybuty rozszerzenia 1 – 15 | W środowisku lokalnym atrybutów rozszerzenia, używanej do rozszerzania schematu usługi Azure AD |

Aby uzyskać więcej informacji, zobacz [tabela 3: Prawidłowe wartości Identyfikatora dla każdego źródła](active-directory-claims-mapping.md#table-3-valid-id-values-per-source).

### <a name="special-claims---transformations"></a>Specjalne oświadczeń — przekształcenia

Można również użyć funkcji przekształcenia oświadczeń.

| Funkcja | Opis |
|----------|-------------|
| **ExtractMailPrefix()** | Usuwa sufiks domeny od adresu e-mail lub nazwa główna użytkownika. Spowoduje to wyodrębnienie tylko pierwszą część nazwy użytkownika, są przekazywane (na przykład "joe_smith" zamiast joe_smith@contoso.com). |
| **Join()** | Dołącza atrybut o zweryfikowanej domeny. Wartość identyfikatora wybranego użytkownika ma domenę, wyodrębnia nazwę użytkownika, aby dołączyć wybranego zweryfikowanej domeny. Na przykład, jeśli wybierzesz adres e-mail (joe_smith@contoso.com) jako wartość identyfikatora użytkownika i wybierz contoso.onmicrosoft.com jako zweryfikowanej domeny, spowoduje to joe_smith@contoso.onmicrosoft.com. |
| **ToLower()** | Konwertuje znaki wybranego atrybutu na małe litery. |
| **ToUpper()** | Konwertuje znaki wybranego atrybutu na wielkie litery. |

## <a name="adding-application-specific-claims"></a>Dodawanie oświadczeń specyficzne dla aplikacji

Aby dodać specyficzne dla aplikacji oświadczeń:

1. W **atrybutów użytkowników i oświadczeń**, wybierz opcję **Dodaj nowe oświadczenie** otworzyć **Zarządzanie oświadczenia użytkownika** strony.
1. Wprowadź **nazwa** oświadczeń. Wartość nie musi ściśle wzorca identyfikatora URI, na specyfikacji protokołu SAML. Jeśli potrzebujesz wzorzec URI używany, można umieścić, **Namespace** pola.
1. Wybierz **źródła** oświadczenie gdzie pobrać jego wartość. Możesz wybrać atrybut użytkownika z listy rozwijanej atrybutu źródłowego lub zastosować przekształcenie do atrybutu użytkownika przed emitując je jako oświadczenia.

### <a name="application-specific-claims---transformations"></a>Specyficzne dla aplikacji oświadczeń — przekształcenia

Można również użyć funkcji przekształcenia oświadczeń.

| Funkcja | Opis |
|----------|-------------|
| **ExtractMailPrefix()** | Usuwa sufiks domeny od adresu e-mail lub nazwa główna użytkownika. Spowoduje to wyodrębnienie tylko pierwszą część nazwy użytkownika, są przekazywane (na przykład "joe_smith" zamiast joe_smith@contoso.com). |
| **Join()** | Tworzy nową wartość po dołączeniu do dwa atrybuty. Opcjonalnie można użyć separator między dwa atrybuty. |
| **ToLower()** | Konwertuje znaki wybranego atrybutu na małe litery. |
| **ToUpper()** | Konwertuje znaki wybranego atrybutu na wielkie litery. |
| **Contains()** | Generuje atrybut lub stała, jeśli dane wejściowe odpowiada określonej wartości. W przeciwnym razie można określić innego dane wyjściowe, jeśli nie zostanie odnaleziony odpowiednik.<br/>Na przykład, aby emitować oświadczeń, których wartość jest adres e-mail użytkownika, jeśli zawiera on domeny "@contoso.com", w przeciwnym razie ma zostać wyprowadzony główną nazwę użytkownika. Aby to zrobić, czy skonfiguruj następujące wartości:<br/>*Parametr 1(input)*: user.email<br/>*Wartość*: "@contoso.com"<br/>Parametr 2 (dane wyjściowe): user.email<br/>Parametr 3 (dane wyjściowe, jeśli nie zostanie odnaleziony odpowiednik): user.userprincipalname |
| **EndWith()** | Generuje atrybut lub stała, gdy dane wejściowe kończy się określoną wartością. W przeciwnym razie można określić innego dane wyjściowe, jeśli nie zostanie odnaleziony odpowiednik.<br/>Na przykład chcąc do wysyłania oświadczeń, których wartość jest employeeid użytkownika, jeśli identyfikatorem employeeid kończy się ciągiem "000", w przeciwnym razie ma służący do wypełniania wyjściowego atrybutu rozszerzenia. Aby to zrobić, czy skonfiguruj następujące wartości:<br/>*Parametr 1(input)*: user.employeeid<br/>*Wartość*: "000"<br/>Parametr 2 (dane wyjściowe): user.employeeid<br/>Parametr 3 (dane wyjściowe, jeśli nie zostanie odnaleziony odpowiednik): user.extensionattribute1 |
| **StartWith()** | Generuje atrybut lub stała, jeśli dane wejściowe rozpoczyna się od określonej wartości. W przeciwnym razie można określić innego dane wyjściowe, jeśli nie zostanie odnaleziony odpowiednik.<br/>Na przykład chcąc do wysyłania oświadczeń, których wartość jest employeeid użytkownika, jeśli kraj/region, który rozpoczyna się od "PL", w przeciwnym razie ma służący do wypełniania wyjściowego atrybutu rozszerzenia. Aby to zrobić, czy skonfiguruj następujące wartości:<br/>*Parametr 1(input)*: user.country<br/>*Wartość*: "US"<br/>Parametr 2 (dane wyjściowe): user.employeeid<br/>Parametr 3 (dane wyjściowe, jeśli nie zostanie odnaleziony odpowiednik): user.extensionattribute1 |
| **Extract() — po dopasowania** | Zwraca podciąg po odpowiada określonej wartości.<br/>Na przykład, jeśli wartość wejściowa jest "Finance_BSimon", wartość jest "Finance_", a następnie oświadczeń danych wyjściowych jest "BSimon". |
| **Extract() — przed dopasowania** | Zwraca podciąg, dopóki nie odpowiada określonej wartości.<br/>Na przykład, jeśli wartość wejściowa jest "BSimon_US", wartość jest "_US", a następnie oświadczeń danych wyjściowych jest "BSimon". |
| **Extract() - między dopasowania** | Zwraca podciąg, dopóki nie odpowiada określonej wartości.<br/>Na przykład jeśli wartość wejściowa jest "Finance_BSimon_US", pierwsza wartość dopasowania jest "Finance_", druga wartość dopasowania jest "_US", a następnie oświadczeń danych wyjściowych jest "BSimon". |
| **ExtractAlpha() — prefiks** | Zwraca część alfabetycznej prefiks ciągu.<br/>Na przykład jeśli wartość wejściowa jest "BSimon_123", następnie zwraca "BSimon". |
| **ExtractAlpha() - sufiks** | Zwraca część alfabetycznej sufiks ciągu.<br/>Na przykład jeśli wartość wejściowa jest "123_Simon", następnie zwraca "BSimon". |
| **ExtractNumeric() — prefiks** | Zwraca wartości liczbowych prefiks część ciągu.<br/>Na przykład jeśli wartość wejściowa jest "123_BSimon", następnie zwraca "123". |
| **ExtractNumeric() - sufiks** | Zwraca część wartości liczbowych sufiks ciągu.<br/>Na przykład jeśli wartość wejściowa jest "BSimon_123", następnie zwraca "123". |
| **IfEmpty()** | Generuje atrybut lub stała, jeśli dane wejściowe są wartości null ani być pusta.<br/>Na przykład, aby dane wyjściowe przechowywane w extensionattribute, jeśli employeeid dla danego użytkownika jest pusty atrybut. Aby to zrobić, czy skonfiguruj następujące wartości:<br/>Parametr 1(input): user.employeeid<br/>Parametr 2 (dane wyjściowe): user.extensionattribute1<br/>Parametr 3 (dane wyjściowe, jeśli nie zostanie odnaleziony odpowiednik): user.employeeid |
| **IfNotEmpty()** | Generuje atrybut lub stała, jeśli dane wejściowe nie jest zerowa lub pusta.<br/>Na przykład, aby dane wyjściowe przechowywane w extensionattribute, jeśli employeeid dla danego użytkownika nie jest pusty atrybut. Aby to zrobić, czy skonfiguruj następujące wartości:<br/>Parametr 1(input): user.employeeid<br/>Parametr 2 (dane wyjściowe): user.extensionattribute1 |

Jeśli potrzebujesz dodatkowych przekształcenia, Prześlij swój pomysł w [forum z opiniami w usłudze Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) w obszarze *aplikacji SaaS* kategorii.

## <a name="next-steps"></a>Kolejne kroki

* [Zarządzanie aplikacjami w usłudze Azure AD](../manage-apps/what-is-application-management.md)
* [Konfigurowanie logowania jednokrotnego w aplikacjach, które nie znajdują się w galerii aplikacji usługi Azure AD](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Rozwiązywanie problemów z opartej na SAML logowania jednokrotnego](howto-v1-debug-saml-sso-issues.md)
