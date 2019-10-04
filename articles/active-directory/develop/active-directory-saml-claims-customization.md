---
title: Dostosowywanie oświadczeń tokenów SAML dla aplikacji dla przedsiębiorstw w usłudze Azure AD | Microsoft Docs
description: Informacje o dostosowywaniu oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw w usłudze Azure AD.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: ryanwi
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e7681afe3f5361b17670312c8391349c650a89d
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936767"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Instrukcje: Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw

Obecnie usługa Azure Active Directory (Azure AD) obsługuje logowanie jednokrotne (SSO) z większością aplikacji przedsiębiorstwa, w tym zarówno aplikacje wstępnie zintegrowane w galerii aplikacji usługi Azure AD, jak i aplikacje niestandardowe. Gdy użytkownik uwierzytelnia się w aplikacji za pośrednictwem usługi Azure AD przy użyciu protokołu SAML 2,0, usługa Azure AD wysyła token do aplikacji (za pośrednictwem protokołu HTTP POST). Następnie aplikacja sprawdza poprawność i używa tokenu do zarejestrowania użytkownika w zamiast monitowania o podanie nazwy użytkownika i hasła. Te tokeny SAML zawierają informacje o użytkowniku znanym jako *oświadczenia*.

Jest *to* informacja, którą dostawca tożsamości informuje o użytkowniku w tokenie, który wystawia dla tego użytkownika. W [tokenie SAML](https://en.wikipedia.org/wiki/SAML_2.0)dane te są zazwyczaj zawarte w instrukcji języka SAML. Unikatowy identyfikator użytkownika jest zazwyczaj reprezentowany przez element SAML subject nazywany również identyfikatorem nazwy.

Domyślnie usługa Azure AD wystawia token języka SAML dla aplikacji, która zawiera `NameIdentifier`, z wartością nazwy użytkownika (znanej także jako główna nazwa użytkownika) w usłudze Azure AD, która może jednoznacznie identyfikować użytkownika. Token SAML zawiera również dodatkowe oświadczenia zawierające adres e-mail użytkownika, imię i nazwisko.

Aby wyświetlić lub edytować oświadczenia wystawione w tokenie SAML dla aplikacji, Otwórz aplikację w Azure Portal. Następnie otwórz sekcję **atrybuty użytkownika & oświadczenia** .

![Otwórz sekcję atrybuty użytkownika & oświadczenia w Azure Portal](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

Istnieją dwie możliwe przyczyny, dla których może być konieczne edytowanie oświadczeń wystawionych w tokenie SAML:

* Aplikacja wymaga, aby wartość `NameIdentifier` lub NameID była inna niż nazwa użytkownika (lub główna nazwa użytkownika) przechowywana w usłudze Azure AD.
* Aplikacja została zapisywana, aby wymagać innego zestawu identyfikatorów URI lub wartości zgłoszeń.

## <a name="editing-nameid"></a>Edytowanie NameID

Aby edytować NameID (wartość identyfikatora nazwy):

1. Otwórz stronę **wartości identyfikatora nazwy** .
1. Wybierz atrybut lub przekształcenie, które chcesz zastosować do atrybutu. Opcjonalnie możesz określić format, który ma mieć NameID.

   ![Edytuj wartość NameID (identyfikator nazwy)](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>Format NameID

Jeśli żądanie SAML zawiera element NameIDPolicy o określonym formacie, usługa Azure AD będzie przestrzegać formatu żądania.

Jeśli żądanie SAML nie zawiera elementu NameIDPolicy, usługa Azure AD wystawia NameID z określonym formatem. Jeśli żaden format nie zostanie określony, usługa Azure AD będzie używać domyślnego formatu źródłowego skojarzonego z wybranym źródłem usługi.

Z listy rozwijanej **Wybierz format identyfikatora nazwy** można wybrać jedną z następujących opcji.

| Format NameID | Opis |
|---------------|-------------|
| **Domyślne** | Usługa Azure AD będzie używać domyślnego formatu źródła. |
| **Trwałe** | Usługa Azure AD będzie używać trwałego formatu NameID. |
| **EmailAddress** | Usługa Azure AD będzie używać EmailAddress jako formatu NameID. |
| **Nieokreślony** | Usługa Azure AD będzie używać nieokreślone jako formatu NameID. |

Aby dowiedzieć się więcej o atrybucie NameIDPolicy, zobacz Logowanie jednokrotne [protokołu SAML](single-sign-on-saml-protocol.md).

### <a name="attributes"></a>Atrybuty

Wybierz żądane źródło dla żądania `NameIdentifier` (lub NameID). Można wybrać jedną z następujących opcji.

| Nazwa | Opis |
|------|-------------|
| Adres e-mail | Adres e-mail użytkownika |
| userprincipalName | Główna nazwa użytkownika (UPN) użytkownika |
| onpremisessamaccount | Nazwa konta SAM, które zostało zsynchronizowane z lokalnej usługi Azure AD |
| obiektu | Identyfikator obiektu użytkownika w usłudze Azure AD |
| employeeid | IDPracownika użytkownika |
| Rozszerzenia katalogów | Rozszerzenia katalogu [zsynchronizowane z Active Directory lokalnego przy użyciu synchronizacji Azure AD Connect](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Atrybuty rozszerzenia 1-15 | Atrybuty rozszerzenia lokalnego używane do rozszerzania schematu usługi Azure AD |

Aby uzyskać więcej informacji, zobacz [tabela 3: prawidłowe wartości identyfikatorów na źródło](active-directory-claims-mapping.md#table-3-valid-id-values-per-source).

### <a name="special-claims---transformations"></a>Specjalne oświadczenia — przekształcenia

Można również użyć funkcji przekształcenia oświadczeń.

| Funkcja | Opis |
|----------|-------------|
| **ExtractMailPrefix()** | Usuwa sufiks domeny z adresu e-mail lub głównej nazwy użytkownika. Spowoduje to wyodrębnienie tylko pierwszej części nazwy użytkownika, która jest przenoszona przez (na przykład "joe_smith" zamiast joe_smith@contoso.com). |
| **Join ()** | Sprzęga atrybut z zweryfikowaną domeną. Jeśli wybrana wartość identyfikatora użytkownika ma domenę, Wyodrębnij nazwę użytkownika w celu dołączenia wybranej zweryfikowanej domeny. Na przykład w przypadku wybrania adresu e-mail (joe_smith@contoso.com) jako wartości identyfikatora użytkownika i wybrania contoso.onmicrosoft.com jako zweryfikowanej domeny spowoduje to joe_smith@contoso.onmicrosoft.com. |
| **ToLower ()** | Konwertuje znaki wybranego atrybutu do małych liter. |
| **ToUpper ()** | Konwertuje znaki wybranego atrybutu na wielkie litery. |

## <a name="adding-application-specific-claims"></a>Dodawanie oświadczeń specyficznych dla aplikacji

Aby dodać oświadczenia specyficzne dla aplikacji:

1. W polu **atrybuty użytkownika & oświadczenia**wybierz pozycję **Dodaj nowe oświadczenie** , aby otworzyć stronę **Zarządzanie oświadczeniami użytkowników** .
1. Wprowadź **nazwę** oświadczenia. Wartość nie musi być ściśle zgodna ze wzorcem URI dla specyfikacji SAML. Jeśli potrzebujesz wzorca URI, możesz go umieścić w polu **przestrzeń nazw** .
1. Wybierz **Źródło** , do którego ma zostać pobrana wartość. Można wybrać atrybut użytkownika z listy rozwijanej atrybutów źródłowych lub zastosować transformację do atrybutu użytkownika przed wyemitowaniem go jako roszczeń.

### <a name="application-specific-claims---transformations"></a>Oświadczenia specyficzne dla aplikacji — przekształcenia

Można również użyć funkcji przekształcenia oświadczeń.

| Funkcja | Opis |
|----------|-------------|
| **ExtractMailPrefix()** | Usuwa sufiks domeny z adresu e-mail lub głównej nazwy użytkownika. Spowoduje to wyodrębnienie tylko pierwszej części nazwy użytkownika, która jest przenoszona przez (na przykład "joe_smith" zamiast joe_smith@contoso.com). |
| **Join ()** | Tworzy nową wartość przez Sprzęganie dwóch atrybutów. Opcjonalnie można użyć separatora między dwoma atrybutami. |
| **ToLower ()** | Konwertuje znaki wybranego atrybutu do małych liter. |
| **ToUpper ()** | Konwertuje znaki wybranego atrybutu na wielkie litery. |
| **Zawiera ()** | Wyprowadza atrybut lub stałą, jeśli dane wejściowe pasują do określonej wartości. W przeciwnym razie można określić inne dane wyjściowe, jeśli nie ma żadnego dopasowania.<br/>Na przykład jeśli chcesz emitować, gdzie wartość jest adresem e-mail użytkownika, jeśli zawiera on domenę "@contoso.com", w przeciwnym razie chcesz utworzyć dane wyjściowe głównej nazwy użytkownika. W tym celu należy skonfigurować następujące wartości:<br/>*Parametr 1 (dane wejściowe)* : User. email<br/>*Wartość*: "@contoso.com"<br/>Parametr 2 (dane wyjściowe): User. email<br/>Parametr 3 (dane wyjściowe w przypadku braku dopasowania): User. userPrincipalName |
| **EndWith()** | Wyprowadza atrybut lub stałą, jeśli dane wejściowe kończą się określoną wartością. W przeciwnym razie można określić inne dane wyjściowe, jeśli nie ma żadnego dopasowania.<br/>Na przykład, jeśli chcesz emitować w przypadku, gdy wartość jest wartością IDPracownika użytkownika, jeśli element IDPracownika zostanie zakończony znakiem "000", w przeciwnym razie chcesz wyprowadzić atrybut rozszerzenia. W tym celu należy skonfigurować następujące wartości:<br/>*Parametr 1 (dane wejściowe)* : User. IDPracownika<br/>*Wartość*: "000"<br/>Parametr 2 (Output): User. IDPracownika<br/>Parametr 3 (dane wyjściowe w przypadku braku dopasowania): User. extensionAttribute1 |
| **StartWith()** | Wyprowadza atrybut lub stałą, jeśli dane wejściowe zaczynają się od określonej wartości. W przeciwnym razie można określić inne dane wyjściowe, jeśli nie ma żadnego dopasowania.<br/>Na przykład, jeśli chcesz emitować w przypadku, gdy wartość jest wartością IDPracownika użytkownika, jeśli kraj/region zaczyna się od "US", w przeciwnym razie chcesz wyprowadzić atrybut rozszerzenia. W tym celu należy skonfigurować następujące wartości:<br/>*Parametr 1 (wejście)* : User. Country<br/>*Wartość*: "US"<br/>Parametr 2 (Output): User. IDPracownika<br/>Parametr 3 (dane wyjściowe w przypadku braku dopasowania): User. extensionAttribute1 |
| **Wyodrębnij () — po dopasowaniu** | Zwraca podciąg po dopasowaniu określonej wartości.<br/>Na przykład, jeśli wartość wejściowa to "Finance_BSimon", odpowiadająca wartość to "Finance_", wówczas wynikiem żądania jest "BSimon". |
| **Extract () — przed dopasowaniem** | Zwraca podciąg, dopóki nie pasuje do określonej wartości.<br/>Na przykład, jeśli wartość wejściowa to "BSimon_US", odpowiadająca wartość to "_US", wówczas wynikiem żądania jest "BSimon". |
| **Extract () — między dopasowaniem** | Zwraca podciąg, dopóki nie pasuje do określonej wartości.<br/>Na przykład, jeśli wartością wejściową jest "Finance_BSimon_US", pierwszą zgodną wartością jest "Finance_", druga zgodna wartość to "_US", a następnie dane wyjściowe żądania to "BSimon". |
| **ExtractAlpha () — prefiks** | Zwraca wartość alfabetyczną prefiksu ciągu.<br/>Na przykład, jeśli wartość wejściowa to "BSimon_123", zwraca "BSimon". |
| **ExtractAlpha () — sufiks** | Zwraca alfabetyczną część ciągu.<br/>Na przykład, jeśli wartość wejściowa to "123_Simon", zwraca "Simon". |
| **ExtractNumeric () — prefiks** | Zwraca część liczbową prefiksu ciągu.<br/>Na przykład, jeśli wartość wejściowa to "123_BSimon", zwraca "123". |
| **ExtractNumeric () — sufiks** | Zwraca numeryczną część ciągu.<br/>Na przykład, jeśli wartość wejściowa to "BSimon_123", zwraca "123". |
| **IfEmpty()** | Wyprowadza atrybut lub stałą, jeśli dane wejściowe mają wartość null lub są puste.<br/>Na przykład jeśli chcesz, aby dane wyjściowe były przechowywane w atrybucie ExtensionAttribute, jeśli IDPracownika dla danego użytkownika jest pusty. W tym celu należy skonfigurować następujące wartości:<br/>Parametr 1 (dane wejściowe): User. IDPracownika<br/>Parametr 2 (Output): User. extensionAttribute1<br/>Parametr 3 (dane wyjściowe w przypadku braku dopasowania): User. IDPracownika |
| **IfNotEmpty()** | Wyprowadza atrybut lub stałą, jeśli dane wejściowe nie mają wartości null ani nie są puste.<br/>Na przykład jeśli chcesz, aby dane wyjściowe były przechowywane w atrybucie ExtensionAttribute, jeśli IDPracownika dla danego użytkownika nie jest pusty. W tym celu należy skonfigurować następujące wartości:<br/>Parametr 1 (dane wejściowe): User. IDPracownika<br/>Parametr 2 (Output): User. extensionAttribute1 |

Jeśli potrzebujesz dodatkowych transformacji, prześlij swój pomysł na [forum opinii w usłudze Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) w kategorii *aplikacji SaaS* .

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie aplikacjami w usłudze Azure AD](../manage-apps/what-is-application-management.md)
* [Konfigurowanie logowania jednokrotnego w aplikacjach, które nie znajdują się w galerii aplikacji usługi Azure AD](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Rozwiązywanie problemów z logowaniem jednokrotnym opartym na protokole SAML](howto-v1-debug-saml-sso-issues.md)
