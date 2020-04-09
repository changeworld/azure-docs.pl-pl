---
title: Dostosowywanie oświadczeń tokenów SAML aplikacji usługi Azure AD
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak dostosować oświadczenia wystawione w tokenie SAML dla aplikacji dla przedsiębiorstw w usłudze Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: article
ms.date: 10/22/2019
ms.author: ryanwi
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.openlocfilehash: 87a9632ec2433b8698e3ae3761ba733aa6bc63a5
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885688"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Jak: dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji korporacyjnych

Obecnie usługa Azure Active Directory (Azure AD) obsługuje logowanie jednokrotne (SSO) z większością aplikacji korporacyjnych, w tym zarówno aplikacjami wstępnie zintegrowanymi w galerii aplikacji usługi Azure AD, jak i aplikacjami niestandardowymi. Gdy użytkownik uwierzytelnia się w aplikacji za pośrednictwem usługi Azure AD przy użyciu protokołu SAML 2.0, usługa Azure AD wysyła token do aplikacji (za pośrednictwem protokołu HTTP POST). A następnie aplikacja sprawdza poprawność i używa tokenu do logowania użytkownika zamiast monitowania o nazwę użytkownika i hasło. Te tokeny SAML zawierają informacje o użytkowniku, znane jako *oświadczenia.*

*Oświadczenie* to informacje, które dostawca tożsamości stwierdza o użytkowniku wewnątrz tokenu, który wystawia dla tego użytkownika. W [tokenie SAML](https://en.wikipedia.org/wiki/SAML_2.0)dane te są zazwyczaj zawarte w instrukcji atrybutu SAML. Unikatowy identyfikator użytkownika jest zazwyczaj reprezentowany w temacie SAML nazywanym również identyfikatorem nazwy.

Domyślnie usługa Azure AD wystawia token SAML `NameIdentifier` do aplikacji, który zawiera oświadczenie o wartości nazwy użytkownika (znanej również jako nazwa główna użytkownika) w usłudze Azure AD, która może jednoznacznie identyfikować użytkownika. Token SAML zawiera również dodatkowe oświadczenia zawierające adres e-mail użytkownika, imię i nazwisko.

Aby wyświetlić lub edytować oświadczenia wystawione w tokenie SAML do aplikacji, otwórz aplikację w witrynie Azure portal. Następnie otwórz sekcję **Atrybuty & oświadczenia użytkownika.**

![Otwórz sekcję Atrybuty & oświadczenia użytkownika w witrynie Azure portal](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

Istnieją dwa możliwe powody, dla których może być konieczne edytowanie oświadczeń wystawionych w tokenie SAML:

* Aplikacja wymaga `NameIdentifier` lub NameID oświadczenia jest coś innego niż nazwa użytkownika (lub nazwa główna użytkownika) przechowywane w usłudze Azure AD.
* Aplikacja została napisana, aby wymagać innego zestawu identyfikatorów URI oświadczeń lub wartości oświadczeń.

## <a name="editing-nameid"></a>Edytowanie nameID

Aby edytować NameID (wartość identyfikatora nazwy):

1. Otwórz stronę **Wartość identyfikatora nazwy.**
1. Wybierz atrybut lub transformację, którą chcesz zastosować do atrybutu. Opcjonalnie można określić format, który ma mieć oświadczenie NameID.

   ![Edytowanie wartości NameID (identyfikatora nazwy)](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>Format NameID

Jeśli żądanie SAML zawiera element NameIDPolicy w określonym formacie, a następnie usługi Azure AD będzie honorować format w żądaniu.

Jeśli żądanie SAML nie zawiera elementu nameidpolicy, a następnie usługi Azure AD wystawi NameID z formatem, który określisz. Jeśli żaden format nie jest określony usługi Azure AD użyje domyślnego formatu źródła skojarzonego z wybranym źródłem oświadczeń.

Z listy rozwijanej **Wybierz format identyfikatora nazwy** można wybrać jedną z następujących opcji.

| Format NameID | Opis |
|---------------|-------------|
| **Domyślny** | Usługa Azure AD użyje domyślnego formatu źródłowego. |
| **Stale** | Usługa Azure AD użyje trwałe jako format NameID. |
| **Emailaddress** | Usługa Azure AD użyje adresu e-mail jako formatu NameID. |
| **Nie określono** | Usługa Azure AD użyje unspecified jako format NameID. |
| **Nazwa kwalifikowana domeny systemu Windows** | Usługa Azure AD użyje systemu WindowsDomainQualifiedName jako formatu NameID. |

Transient NameID jest również obsługiwany, ale nie jest dostępny w rozwijanie i nie można skonfigurować po stronie platformy Azure. Aby dowiedzieć się więcej o atrybucie NameIDPolicy, zobacz [Protokół SAML logowania jednokrotnego.](single-sign-on-saml-protocol.md)

### <a name="attributes"></a>Atrybuty

Wybierz żądane źródło `NameIdentifier` oświadczenia (lub NameID). Można wybrać jedną z następujących opcji.

| Nazwa | Opis |
|------|-------------|
| Adres e-mail | Adres e-mail użytkownika |
| Userprincipalname | Główna nazwa użytkownika (UPN) użytkownika |
| onpremisessamaccount | Nazwa konta SAM, która została zsynchronizowana z lokalnej usługi Azure AD |
| Objectid | Obiekt użytkownika w usłudze Azure AD |
| employeeid | Identyfikator pracownika użytkownika |
| Rozszerzenia katalogów | Rozszerzenia katalogów [zsynchronizowane z lokalnej](../hybrid/how-to-connect-sync-feature-directory-extensions.md) usługi Active Directory przy użyciu usługi Azure AD Connect Sync |
| Atrybuty rozszerzenia 1-15 | Lokalne atrybuty rozszerzenia używane do rozszerzania schematu usługi Azure AD |

Aby uzyskać więcej informacji, zobacz [Tabela 3: Prawidłowe wartości identyfikatorów na źródło](active-directory-claims-mapping.md#table-3-valid-id-values-per-source).

Można również przypisać dowolną stałą (statyczną) wartość do wszelkich oświadczeń zdefiniowanych w usłudze Azure AD. Wykonaj poniższe czynności, aby przypisać stałą wartość:

1. W [witrynie Azure portal](https://portal.azure.com/)w sekcji **Atrybuty & oświadczenia użytkownika** kliknij ikonę **Edytuj,** aby edytować oświadczenia.

1. Kliknij wymagane oświadczenie, które chcesz zmodyfikować.

1. Wprowadź wartość stałą bez cudzysłowów w **atrybucie Źródło** zgodnie z organizacją i kliknij przycisk **Zapisz**.

    ![Otwórz sekcję Atrybuty & oświadczenia użytkownika w witrynie Azure portal](./media/active-directory-saml-claims-customization/organization-attribute.png)

1. Stała wartość zostanie wyświetlona poniżej.

    ![Otwórz sekcję Atrybuty & oświadczenia użytkownika w witrynie Azure portal](./media/active-directory-saml-claims-customization/edit-attributes-claims.png)

### <a name="special-claims---transformations"></a>Roszczenia specjalne - przekształcenia

Można również użyć funkcji przekształcenia oświadczeń.

| Funkcja | Opis |
|----------|-------------|
| **ExtractMailPrefix()** | Usuwa sufiks domeny z adresu e-mail lub głównej nazwy użytkownika. Spowoduje to wyodrębnianie tylko pierwszej części nazwy użytkownika przekazywanej przez (na przykład joe_smith@contoso.com"joe_smith" zamiast ). |
| **Join()** | Dołącza do atrybutu ze zweryfikowaną domeną. Jeśli wybrana wartość identyfikatora użytkownika ma domenę, wyodrębni nazwę użytkownika w celu dołączeniu wybranej zweryfikowanej domeny. Jeśli na przykład wybierzeszjoe_smith@contoso.comwiadomość e-mail ( ) jako wartość identyfikatora użytkownika i wybierzesz contoso.onmicrosoft.com jako zweryfikowaną domenę, spowoduje joe_smith@contoso.onmicrosoft.comto wyświetlenie . |
| **ToLower()** | Konwertuje znaki zaznaczonego atrybutu na małe litery. |
| **Toupper()** | Konwertuje znaki zaznaczonego atrybutu na wielkie litery. |

## <a name="adding-application-specific-claims"></a>Dodawanie oświadczeń specyficznych dla aplikacji

Aby dodać oświadczenia specyficzne dla aplikacji:

1. W **obszarze Atrybuty & oświadczenia**użytkownika wybierz pozycję Dodaj nowe **oświadczenie,** aby otworzyć stronę **Zarządzanie roszczeniami użytkowników.**
1. Wprowadź **nazwę** oświadczeń. Wartość nie musi ściśle przestrzegać wzorca identyfikatora URI, zgodnie ze specyfikacją SAML. Jeśli potrzebujesz wzorca identyfikatora URI, możesz umieścić go w polu **Obszar nazw.**
1. Wybierz **źródło,** w którym oświadczenie ma pobrać jego wartość. Można wybrać atrybut użytkownika z listy rozwijanej atrybutu źródłowego lub zastosować transformację do atrybutu użytkownika przed emisją go jako oświadczenia.

### <a name="claim-transformations"></a>Przekształcenia oświadczeń

Aby zastosować transformację do atrybutu użytkownika:

1. W **obszarze Zarządzanie oświadczeniam**wybierz *pozycję Transformacja* jako źródło oświadczeń, aby otworzyć stronę **Zarządzanie transformacją.**
2. Wybierz funkcję z listy rozwijanej transformacji. W zależności od wybranej funkcji należy podać parametry i stałą wartość do oceny w transformacji. Więcej informacji na temat dostępnych funkcji można znaleźć w poniższej tabeli.
3. Aby zastosować wiele transformacji, kliknij **dodaj transformację**. Do oświadczenia można zastosować maksymalnie dwie transformacje. Na przykład można najpierw wyodrębnić prefiks wiadomości e-mail `user.mail`. Następnie zrób wielkie litery ciągu.

   ![Edytowanie wartości NameID (identyfikatora nazwy)](./media/active-directory-saml-claims-customization/sso-saml-multiple-claims-transformation.png)

Do przekształcania oświadczeń można użyć następujących funkcji.

| Funkcja | Opis |
|----------|-------------|
| **ExtractMailPrefix()** | Usuwa sufiks domeny z adresu e-mail lub głównej nazwy użytkownika. Spowoduje to wyodrębnianie tylko pierwszej części nazwy użytkownika przekazywanej przez (na przykład joe_smith@contoso.com"joe_smith" zamiast ). |
| **Join()** | Tworzy nową wartość, łącząc dwa atrybuty. Opcjonalnie można użyć separatora między dwoma atrybutami. W przypadku transformacji oświadczeń NameID sprzężenie jest ograniczone do zweryfikowanej domeny. Jeśli wybrana wartość identyfikatora użytkownika ma domenę, wyodrębni nazwę użytkownika w celu dołączeniu wybranej zweryfikowanej domeny. Jeśli na przykład wybierzeszjoe_smith@contoso.comwiadomość e-mail ( ) jako wartość identyfikatora użytkownika i wybierzesz contoso.onmicrosoft.com jako zweryfikowaną domenę, spowoduje joe_smith@contoso.onmicrosoft.comto wyświetlenie . |
| **ToLower()** | Konwertuje znaki zaznaczonego atrybutu na małe litery. |
| **Toupper()** | Konwertuje znaki zaznaczonego atrybutu na wielkie litery. |
| **Contains()** | Wyprowadza atrybut lub stałą, jeśli dane wejściowe są zgodne z określoną wartością. W przeciwnym razie można określić inne dane wyjściowe, jeśli nie ma dopasowania.<br/>Na przykład jeśli chcesz emitować oświadczenie, gdzie wartość jest adresem e-mail@contoso.comużytkownika, jeśli zawiera domenę " ", w przeciwnym razie chcesz wyprowadzić nazwę głównego użytkownika. W tym celu należy skonfigurować następujące wartości:<br/>*Parametr 1(input)*: user.email<br/>*Wartość*:@contoso.com" " "<br/>Parametr 2 (dane wyjściowe): user.email<br/>Parametr 3 (dane wyjściowe, jeśli nie ma dopasowania): user.userprincipalname |
| **EndWith()** | Wyprowadza atrybut lub stałą, jeśli dane wejściowe kończy się określoną wartością. W przeciwnym razie można określić inne dane wyjściowe, jeśli nie ma dopasowania.<br/>Na przykład jeśli chcesz emitować oświadczenie, w którym wartość jest identyfikatorem pracownika użytkownika, jeśli identyfikator pracownika kończy się na "000", w przeciwnym razie chcesz wyprowadzić atrybut rozszerzenia. W tym celu należy skonfigurować następujące wartości:<br/>*Parametr 1(dane wejściowe)*: user.employeeid<br/>*Wartość*: "000"<br/>Parametr 2 (dane wyjściowe): user.employeeid<br/>Parametr 3 (wyjście, jeśli nie ma dopasowania): user.extensionattribute1 |
| **StartWith()** | Wyprowadza atrybut lub stałą, jeśli dane wejściowe rozpoczyna się od określonej wartości. W przeciwnym razie można określić inne dane wyjściowe, jeśli nie ma dopasowania.<br/>Na przykład jeśli chcesz emitować oświadczenie, w którym wartość jest identyfikator pracownika użytkownika, jeśli kraj/region zaczyna się od "US", w przeciwnym razie chcesz wyprowadzić atrybut rozszerzenia. W tym celu należy skonfigurować następujące wartości:<br/>*Parametr 1(input)*: user.country<br/>*Wartość*: "US"<br/>Parametr 2 (dane wyjściowe): user.employeeid<br/>Parametr 3 (wyjście, jeśli nie ma dopasowania): user.extensionattribute1 |
| **Extract() - Po dopasowaniu** | Zwraca podciąg po dopasowaniu określonej wartości.<br/>Na przykład jeśli wartość danych wejściowych jest "Finance_BSimon", pasujące wartość jest "Finance_", a następnie wynik oświadczenia jest "BSimon". |
| **Extract() - Przed dopasowaniem** | Zwraca podciąg, dopóki nie będzie zgodny z określoną wartością.<br/>Na przykład jeśli wartość danych wejściowych jest "BSimon_US", pasujące wartość jest "_US", a następnie wynik oświadczenia jest "BSimon". |
| **Extract() - Między dopasowywaniem** | Zwraca podciąg, dopóki nie będzie zgodny z określoną wartością.<br/>Na przykład jeśli wartość danych wejściowych jest "Finance_BSimon_US", pierwszą dopasowaną wartością jest "Finance_", drugą dopasowaną wartością jest "_US", a następnie dane wyjściowe oświadczenia to "BSimon". |
| **ExtractAlpha() - Prefiks** | Zwraca alfabetyczną część ciągu prefiksu.<br/>Na przykład jeśli wartość danych wejściowych jest "BSimon_123", a następnie zwraca "BSimon". |
| **ExtractAlpha() - Przyrostek** | Zwraca alfabetyczną część sufiksu ciągu.<br/>Na przykład jeśli wartość danych wejściowych jest "123_Simon", a następnie zwraca "Simon". |
| **ExtractNumeric() - Prefiks** | Zwraca numeryczną część prefiksu ciągu.<br/>Na przykład jeśli wartość danych wejściowych jest "123_BSimon", a następnie zwraca "123". |
| **ExtractNumeric() - Przyrostek** | Zwraca numeryczną część sufiksu ciągu.<br/>Na przykład jeśli wartość danych wejściowych jest "BSimon_123", a następnie zwraca "123". |
| **IfEmpty()** | Wyprowadza atrybut lub stałą, jeśli dane wejściowe są zerowe lub puste.<br/>Na przykład jeśli chcesz wyprowadzić atrybut przechowywany w extensionattribute, jeśli identyfikator pracownika dla danego użytkownika jest pusty. W tym celu należy skonfigurować następujące wartości:<br/>Parametr 1(dane wejściowe): user.employeeid<br/>Parametr 2 (wyjście): user.extensionattribute1<br/>Parametr 3 (dane wyjściowe, jeśli nie ma dopasowania): user.employeeid |
| **IfNotEmpty()** | Wyprowadza atrybut lub stałą, jeśli dane wejściowe nie jest null lub puste.<br/>Na przykład jeśli chcesz wyprowadzić atrybut przechowywany w extensionattribute, jeśli identyfikator pracownika dla danego użytkownika nie jest pusty. W tym celu należy skonfigurować następujące wartości:<br/>Parametr 1(dane wejściowe): user.employeeid<br/>Parametr 2 (wyjście): user.extensionattribute1 |

Jeśli potrzebujesz dodatkowych przekształceń, prześlij swój pomysł na [forum opinii w usłudze Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) w kategorii aplikacji *SaaS.*

## <a name="emitting-claims-based-on-conditions"></a>Emitowanie oświadczeń na podstawie warunków

Można określić źródło oświadczenia na podstawie typu użytkownika i grupy, do której należy użytkownik. 

Typ użytkownika może być:
- **Dowolny**: Wszyscy użytkownicy mogą uzyskać dostęp do aplikacji.
- **Elementy członkowskie**: Natywny członek dzierżawcy
- **Wszyscy goście:** Użytkownik jest przenoszony z organizacji zewnętrznej z usługą Azure AD lub bez niej.
- **Goście usługi AAD:** Użytkownik-gość należy do innej organizacji korzystającej z usługi Azure AD.
- **Goście zewnętrzni:** użytkownik-gość należy do organizacji zewnętrznej, która nie ma usługi Azure AD.


Jednym ze scenariuszy, w którym jest to przydatne, jest, gdy źródło oświadczenia jest inny dla gościa i pracownika uzyskującego dostęp do aplikacji. Można określić, że jeśli użytkownik jest pracownikiem NameID pochodzi z user.email, ale jeśli użytkownik jest gościem, nameID pochodzi z user.extensionattribute1.

Aby dodać warunek oświadczenia:

1. W **zakładce Zarządzaj roszczeniem**rozwiń warunki oświadczenia.
2. Wybierz typ użytkownika.
3. Wybierz grupy, do których powinien należeć użytkownik. Można wybrać maksymalnie 10 unikatowych grup we wszystkich zgłoszeniach dla danej aplikacji. 
4. Wybierz **źródło,** w którym oświadczenie ma pobrać jego wartość. Można wybrać atrybut użytkownika z listy rozwijanej atrybutu źródłowego lub zastosować transformację do atrybutu użytkownika przed emisją go jako oświadczenia.

Kolejność dodawania warunków jest ważna. Usługa Azure AD ocenia warunki od góry do dołu, aby zdecydować, która wartość ma być emitowana w żądaniu. 

Na przykład Brita Simon jest użytkownikiem-gościem w dzierżawie Contoso. Należy do innej organizacji, która również korzysta z usługi Azure AD. Biorąc pod uwagę poniższą konfigurację dla aplikacji Fabrikam, gdy Brita próbuje zalogować się do usługi Fabrikam, usługa Azure AD oceni warunki zgodnie z poniższymi.

Po pierwsze usługa Azure AD sprawdza, czy `All guests`typ użytkownika Brita jest . Ponieważ jest to prawdą, a następnie usługi Azure `user.extensionattribute1`AD przypisuje źródło oświadczenia do . Po drugie usługa Azure AD sprawdza, czy `AAD guests`typ użytkownika Brita jest , ponieważ jest to `user.mail`również prawdą, a następnie usługi Azure AD przypisuje źródło oświadczenia do . Wreszcie, roszczenie jest emitowane `user.email` z wartością dla Brita.

![Konfiguracja warunkowa oświadczeń](./media/active-directory-saml-claims-customization/sso-saml-user-conditional-claims.png)

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie aplikacjami w usłudze Azure AD](../manage-apps/what-is-application-management.md)
* [Konfigurowanie aplikacji logowania jednokrotnego, które nie znajdują się w galerii aplikacji usługi Azure AD](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Rozwiązywanie problemów z logowaniem jednokrotnym oparte na saml](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
