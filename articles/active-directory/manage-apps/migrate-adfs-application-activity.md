---
title: Przenoszenie aplikacji usług AD FS do usługi Azure Active Directory za pomocą raportu aktywności | Dokumenty firmy Microsoft
description: Raport aktywności aplikacji usług federacyjnych Active Directory (AD FS) umożliwia szybką migrację aplikacji z usług AD FS do usługi Azure Active Directory (Azure AD). To narzędzie do migracji dla usług AD FS identyfikuje zgodność z usługą Azure AD i zawiera wskazówki dotyczące migracji.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/14/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 333e440fdd5f5062dda45fb12a83543c63e66c04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978031"
---
# <a name="use-the-ad-fs-application-activity-report-preview-to-migrate-applications-to-azure-ad"></a>Użyj raportu aktywności aplikacji usług AD FS (wersja zapoznawcza) do migrowania aplikacji do usługi Azure AD

Wiele organizacji korzysta z usług federacyjnych Active Directory (AD FS) w celu zapewnienia logowania jednokrotnego w aplikacjach w chmurze. Przenoszenie aplikacji ad fs do usługi Azure AD ad w celu uwierzytelniania ma istotne znaczenie, szczególnie w zakresie zarządzania kosztami, zarządzania ryzykiem, wydajności, zgodności i nadzoru. Jednak zrozumienie, które aplikacje są zgodne z usługą Azure AD i identyfikowanie określonych kroków migracji może być czasochłonne.

Raport aktywności aplikacji usług AD FS (wersja zapoznawcza) w witrynie Azure portal umożliwia szybkie określenie, które aplikacje mogą być migrowane do usługi Azure AD. Ocenia wszystkie aplikacje usług AD FS pod kątem zgodności z usługą Azure AD, sprawdza wszelkie problemy i zawiera wskazówki dotyczące przygotowywania poszczególnych aplikacji do migracji. Za pomocą raportu aktywności aplikacji usług AD FS można:

* **Odnajduj aplikacje usług AD FS i zakres migracji.** Raport aktywności aplikacji usług AD FS zawiera listę wszystkich aplikacji ad fs w organizacji i wskazuje ich gotowość do migracji do usługi Azure AD.
* **Ustalanie priorytetów aplikacji do migracji.** Uzyskaj liczbę unikatowych użytkowników, którzy zalogowali się do aplikacji w ciągu ostatnich 1, 7 lub 30 dni, aby określić krytyczność lub ryzyko migracji aplikacji.
* **Uruchom testy migracji i rozwiąż problemy.** Usługa raportowania automatycznie uruchamia testy w celu ustalenia, czy aplikacja jest gotowa do migracji. Wyniki są wyświetlane w raporcie aktywności aplikacji ad fs jako stan migracji. Jeśli zostaną zidentyfikowane potencjalne problemy z migracją, otrzymasz szczegółowe wskazówki dotyczące rozwiązywania problemów.

Dane aktywności aplikacji usług AD FS są dostępne dla użytkowników, którym przypisano dowolną z tych ról administratora: administratora globalnego, czytnika raportów, czytnika zabezpieczeń, administratora aplikacji lub administratora aplikacji w chmurze.

## <a name="prerequisites"></a>Wymagania wstępne

* Organizacja musi obecnie korzystać z usług AD FS, aby uzyskać dostęp do aplikacji.
* Usługa Azure AD Connect Health musi być włączona w dzierżawie usługi Azure AD.
   * [Dowiedz się więcej o usłudze Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)
   * [Wprowadzenie do konfigurowania usługi Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

## <a name="discover-ad-fs-applications-that-can-be-migrated"></a>Odnajdowanie aplikacji usług AD FS, które można migrować 

Raport aktywności aplikacji usług AD FS jest dostępny w witrynie Azure portal w obszarze **Raportowanie & szczegółowych informacji dotyczących użycia** usługi Azure AD. Raport aktywności aplikacji usług AD FS analizuje każdą aplikację ad fs, aby ustalić, czy można ją migrować w stanie, w jakim jest, lub czy potrzebny jest dodatkowy przegląd. 

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) za pomocą roli administratora, która ma dostęp do danych aktywności aplikacji usług AD FS (administrator globalny, czytnik raportów, czytnik zabezpieczeń, administrator aplikacji lub administrator aplikacji w chmurze).

2. Wybierz **pozycję Azure Active Directory**, a następnie wybierz pozycję **Aplikacje przedsiębiorstwa**.

3. W obszarze **Aktywność**wybierz **pozycję Użycie & Insights (Wersja zapoznawcza),** a następnie wybierz pozycję Działanie aplikacji usług AD **FS,** aby otworzyć listę wszystkich aplikacji usług AD FS w organizacji.

   ![Działanie aplikacji usług AD FS](media/migrate-adfs-application-activity/adfs-application-activity.png)

4. Dla każdej aplikacji na liście działań aplikacji AD FS wyświetl **stan migracji:**

   * **Gotowość do migracji** oznacza, że konfiguracja aplikacji usług AD FS jest w pełni obsługiwana w usłudze Azure AD i może być migrowana w stanie, w jakim jest.

   * **Przegląd potrzeb** oznacza, że niektóre ustawienia aplikacji można migrować do usługi Azure AD, ale musisz przejrzeć ustawienia, których nie można migrować w takiej stanie, w jakim się znajduje.

   * **Dodatkowe kroki wymagane** oznacza, że usługa Azure AD nie obsługuje niektórych ustawień aplikacji, więc aplikacji nie można migrować w bieżącym stanie.

## <a name="evaluate-the-readiness-of-an-application-for-migration"></a>Ocena gotowości aplikacji do migracji 

1. Na liście działań aplikacji usług AD FS kliknij stan w kolumnie **Stan migracji,** aby otworzyć szczegóły migracji. Zobaczysz podsumowanie testów konfiguracji, które przeszły, wraz z potencjalnymi problemami z migracją.

   ![Szczegóły dotyczące migracji](media/migrate-adfs-application-activity/migration-details.png)

2. Kliknij wiadomość, aby otworzyć dodatkowe szczegóły reguły migracji. Aby uzyskać pełną listę przetestowanych właściwości, zobacz tabelę [testów konfiguracji aplikacji usług AD FS](#ad-fs-application-configuration-tests) poniżej.

   ![Szczegóły reguły migracji](media/migrate-adfs-application-activity/migration-rule-details.png)

### <a name="ad-fs-application-configuration-tests"></a>Testy konfiguracji aplikacji usług AD FS

W poniższej tabeli wymieniono wszystkie testy konfiguracji wykonywane w aplikacjach usług AD FS.

|Wynik  |Przełęcz/Ostrzeżenie/Niepowodzenie  |Opis  |
|---------|---------|---------|
|Test-ADFSRPAdditionalAuthenticationRules <br> Wykryto co najmniej jedną regułę niezdyskładunku do migracji dla additionalauthentication.       | Przełęcz/Ostrzeżenie          | Jednostka uzależniająca ma reguły monitowania o uwierzytelnianie wieloskładnikowe (MFA). Aby przejść do usługi Azure AD, przetłumacz te reguły na zasady dostępu warunkowego. Jeśli używasz lokalnego usługi MFA, zaleca się przejście do usługi Azure MFA. [Dowiedz się więcej o dostępie warunkowym](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).        |
|Test-ADFSRPAdodattionalWSFedEndpoint <br> Jednostka uzależniona ma additionalwsfedendpoint ustawiona na true.       | Powodzenie/niepowodzenie          | Jednostka uzależniona w u usług AD FS zezwala na wiele punktów końcowych asercji WS-Fed.Obecnie usługa Azure AD obsługuje tylko jeden.Jeśli masz scenariusz, w którym ten wynik blokuje migrację, [pouczym nas o tym](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695621-allow-multiple-ws-fed-assertion-endpoints).     |
|Liczba odwołań klasowych usługi test-ADFSRPAllowedAuthenticationReferences <br> Jednostka uzależniona ustawiła AllowedAuthenticationClassReferences.       | Powodzenie/niepowodzenie          | To ustawienie w uściśli usług AD FS umożliwia określenie, czy aplikacja jest skonfigurowana tak, aby zezwalać tylko na określone typy uwierzytelniania. Zaleca się użycie dostępu warunkowego, aby osiągnąć tę możliwość. Jeśli masz scenariusz, w którym ten wynik blokuje migrację, [pouczym nas o tym](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695672-allow-in-azure-ad-to-specify-certain-authentication).  [Dowiedz się więcej o dostępie warunkowym](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).          |
|Test-ADFSRPAlwaysRequireAuthentication <br> ZawszeRequireAuthenticationCheckResult      | Powodzenie/niepowodzenie          | To ustawienie w ujmowaniu usług AD FS umożliwia określenie, czy aplikacja jest skonfigurowana do ignorowania plików cookie sytego i **zawsze monituj o uwierzytelnienie**. W usłudze Azure AD można zarządzać sesją uwierzytelniania przy użyciu zasad dostępu warunkowego, aby osiągnąć podobne zachowanie. [Dowiedz się więcej o konfigurowaniu zarządzania sesjami uwierzytelniania za pomocą programu Dostęp warunkowy](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime).          |
|Test-ADFSRPAutoUpdateEnabled <br> Jednostka uzależniona ma zestaw autoupdateenabled na true       | Przełęcz/Ostrzeżenie          | To ustawienie w usłudze AD FS umożliwia określenie, czy usługi AD FS są skonfigurowane do automatycznego aktualizowania aplikacji na podstawie zmian w metadanych federacji. Usługa Azure AD nie obsługuje tego dzisiaj, ale nie należy blokować migracji aplikacji do usługi Azure AD.           |
|Test-ADFSRPClaimsProviderName <br> Jednostka uzależniona ma włączoną obsługę wielu claimsproviders       | Powodzenie/niepowodzenie          | To ustawienie w ujmowaniu usługi AD wywołuje dostawców tożsamości, z których jednostka uzależniająca przyjmuje oświadczenia. W usłudze Azure AD można włączyć współpracę zewnętrzną przy użyciu usługi Azure AD B2B. [Dowiedz się więcej o usłudze Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b).          |
|Test-ADFSRPDelegationAutorizationRules      | Powodzenie/niepowodzenie          | Aplikacja ma zdefiniowane niestandardowe reguły autoryzacji delegowania. Jest to koncepcja WS-Trust, którą obsługuje usługa Azure AD przy użyciu nowoczesnych protokołów uwierzytelniania, takich jak OpenID Connect i OAuth 2.0. [Dowiedz się więcej o platformie Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc).          |
|Test-ADFSRPImpersonationAuthorizationRules       | Przełęcz/Ostrzeżenie          | Aplikacja ma zdefiniowane niestandardowe reguły autoryzacji personifikacji.Jest to koncepcja WS-Trust, którą obsługuje usługa Azure AD przy użyciu nowoczesnych protokołów uwierzytelniania, takich jak OpenID Connect i OAuth 2.0. [Dowiedz się więcej o platformie Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc).          |
|Test-ADFSRPIsuanceAuhorizationRules <br> Wykryto co najmniej jedną regułę niezdyskładnialną dla autoryzacji.       | Przełęcz/Ostrzeżenie          | Aplikacja ma niestandardowe reguły autoryzacji emisji zdefiniowane w u usług AD FS.Usługa Azure AD obsługuje tę funkcję za pomocą usługi Azure AD. [Dowiedz się więcej o dostępie warunkowym](https://docs.microsoft.com/azure/active-directory/conditional-access/overview). <br> Można również ograniczyć dostęp do aplikacji przez użytkownika lub grupy przypisane do aplikacji. [Dowiedz się więcej o przypisywaniu użytkowników i grup do uzyskiwania dostępu do aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups).            |
|Test-ADFSRPIsuancePrzekaźnikitransformrule <br> Wykryto co najmniej jedną regułę niezdyskładnialną dla issuanceTransform.       | Przełęcz/Ostrzeżenie          | Aplikacja ma niestandardowe reguły przekształcania emisji zdefiniowane w u usług AD FS. Usługa Azure AD obsługuje dostosowywanie oświadczeń wystawionych w tokenie. Aby dowiedzieć się więcej, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji korporacyjnych.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)           |
|Test-ADFSRPMonitoringEnabled <br> Jednostka uzależniona ma MonitoringEnabled ustawiona na true.       | Przełęcz/Ostrzeżenie          | To ustawienie w usłudze AD FS umożliwia określenie, czy usługi AD FS są skonfigurowane do automatycznego aktualizowania aplikacji na podstawie zmian w metadanych federacji. Usługa Azure AD nie obsługuje tego dzisiaj, ale nie należy blokować migracji aplikacji do usługi Azure AD.           |
|Test-ADFSRPNotBeforeSkew <br> NotBeforeSkewCheckResult      | Przełęcz/Ostrzeżenie          | Usługi AD FS umożliwia pochylenie czasu na podstawie NotBefore i NotOnOrAfter razy w tokenie SAML. Usługa Azure AD domyślnie obsługuje tę usługę.          |
|Test-ADFSRPRequestMFAFromClaimsProviders <br> Jednostka uzależniona ma RequestMFAFromClaimsProviders ustawiona na true.       | Przełęcz/Ostrzeżenie          | To ustawienie w ujmowania usługi AD określa zachowanie usługi MFA, gdy użytkownik pochodzi z innego dostawcy oświadczeń. W usłudze Azure AD można włączyć współpracę zewnętrzną przy użyciu usługi Azure AD B2B. Następnie można zastosować zasady dostępu warunkowego, aby chronić dostęp gościa. Dowiedz się więcej o [usłudze Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b) i [dostępie warunkowym](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).          |
|Wymagane testy ADFSRPSignedSamlRequests <br> Jednostka uzależniająca ma signedsamlRequests wymagany zestaw do true       | Powodzenie/niepowodzenie          | Aplikacja jest skonfigurowana w u usług AD FS w celu zweryfikowania podpisu w żądaniu SAML. Usługa Azure AD akceptuje podpisane żądanie SAML; jednak nie zweryfikuje podpisu. Usługa Azure AD ma różne metody ochrony przed złośliwymi wywołaniami. Na przykład usługa Azure AD używa adresów URL odpowiedzi skonfigurowanych w aplikacji do sprawdzania poprawności żądania SAML. Usługa Azure AD wyśle tylko token do odpowiedzi adresów URL skonfigurowanych dla aplikacji. Jeśli masz scenariusz, w którym ten wynik blokuje migrację, [pouczym nas o tym](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13394589-saml-signature).          |
|Test-ADFSRPTokenLifetime <br> TokenLifetimeCheckResult        | Przełęcz/Ostrzeżenie         | Aplikacja jest skonfigurowana dla niestandardowego okresu istnienia tokenu. Domyślnie usługi AD FS to jedna godzina.Usługa Azure AD obsługuje tę funkcję przy użyciu dostępu warunkowego. Aby dowiedzieć się więcej, zobacz [Konfigurowanie zarządzania sesjami uwierzytelniania za pomocą programu Dostęp warunkowy](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime).          |
|Jednostka uzależniająca jest ustawiona na szyfrowanie oświadczeń. Jest to obsługiwane przez usługę Azure AD       | Przekazać          | Za pomocą usługi Azure AD można zaszyfrować token wysłany do aplikacji. Aby dowiedzieć się więcej, zobacz [Konfigurowanie szyfrowania tokenów saml usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).          |
|EncryptedNameIdRequiredCheckResult      | Powodzenie/niepowodzenie          | Aplikacja jest skonfigurowana do szyfrowania oświadczenia nameID w tokenie SAML.Za pomocą usługi Azure AD można zaszyfrować cały token wysłany do aplikacji.Szyfrowanie określonych oświadczeń nie jest jeszcze obsługiwane. Aby dowiedzieć się więcej, zobacz [Konfigurowanie szyfrowania tokenów saml usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).         |

## <a name="check-the-results-of-claim-rule-tests"></a>Sprawdź wyniki testów reguł oświadczeń

Jeśli skonfigurowano regułę oświadczeń dla aplikacji w u usług AD FS, środowisko zapewni szczegółową analizę dla wszystkich reguł oświadczeń. Zobaczysz, które reguły oświadczeń można przenieść do usługi Azure AD, a które wymagają dalszego przeglądu.

1. Na liście działań aplikacji usług AD FS kliknij stan w kolumnie **Stan migracji,** aby otworzyć szczegóły migracji. Zobaczysz podsumowanie testów konfiguracji, które przeszły, wraz z potencjalnymi problemami z migracją.

2. Na stronie **Szczegóły reguły migracji** rozwiń wyniki, aby wyświetlić szczegółowe informacje o potencjalnych problemach z migracją i uzyskać dodatkowe wskazówki. Aby uzyskać szczegółową listę wszystkich przetestowanych reguł oświadczeń, zobacz [sprawdź wyniki testów reguł oświadczeń](#check-the-results-of-claim-rule-tests) poniżej.

   W poniższym przykładzie przedstawiono szczegóły reguły migracji dla reguły IssuanceTransform. Wyświetla listę określonych części oświadczenia, które muszą zostać sprawdzone i rozwiązane przed migracją aplikacji do usługi Azure AD.

   ![Reguła migracji zawiera szczegółowe informacje o dodatkowych wskazówkach](media/migrate-adfs-application-activity/migration-rule-details-guidance.png)

### <a name="claim-rule-tests"></a>Testy reguł oświadczeń

W poniższej tabeli wymieniono wszystkie testy reguł oświadczeń, które są wykonywane w aplikacjach ad fs.

|Właściwość  |Opis  |
|---------|---------|
|UNSUPPORTED_CONDITION_PARAMETER      | Instrukcja condition używa wyrażeń regularnych do oceny, czy oświadczenie pasuje do określonego wzorca.Aby osiągnąć podobną funkcjonalność w usłudze Azure AD, można użyć wstępnie zdefiniowanej transformacji, takiej jak IfEmpty(), StartWith(), Contains(), Contains(), między innymi. Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji korporacyjnych](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|UNSUPPORTED_CONDITION_CLASS      | Instrukcja condition ma wiele warunków, które muszą zostać ocenione przed uruchomieniem instrukcji wydawania.Usługa Azure AD może obsługiwać tę funkcję z funkcji przekształcania oświadczenia, gdzie można ocenić wiele wartości oświadczeń.Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji korporacyjnych](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|UNSUPPORTED_RULE_TYPE      | Nie można rozpoznać reguły oświadczeń. Aby uzyskać więcej informacji na temat konfigurowania oświadczeń w usłudze Azure AD, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji korporacyjnych.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)          |
|CONDITION_MATCHES_UNSUPPORTED_ISSUER      | Instrukcja condition używa wystawcy, który nie jest obsługiwany w usłudze Azure AD.Obecnie usługa Azure AD nie źródła oświadczeń ze sklepów innych niż usługi Active Directory lub usługi Azure AD. Jeśli to blokuje migrację aplikacji do usługi Azure AD, [poudź nas do tego.](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire)         |
|UNSUPPORTED_CONDITION_FUNCTION      | Instrukcja condition używa funkcji agregacji do wystawiania lub dodawania pojedynczego oświadczenia, niezależnie od liczby dopasowań.W usłudze Azure AD można ocenić atrybut użytkownika, aby zdecydować, jaką wartość użyć dla oświadczenia z funkcjami takimi jak IfEmpty(), StartWith(), Contains(), między innymi.Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji korporacyjnych](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|RESTRICTED_CLAIM_ISSUED      | Instrukcja condition używa oświadczenia, które jest ograniczone w usłudze Azure AD. Możesz wystawiać ograniczone oświadczenie, ale nie można zmodyfikować jego źródła ani zastosować żadnej transformacji. Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń emitowanych w tokenach dla określonej aplikacji w usłudze Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping)          |
|EXTERNAL_ATTRIBUTE_STORE      | Instrukcja emisji używa magazynu atrybutów innego niż usługa Active Directory. Obecnie usługa Azure AD nie źródła oświadczeń ze sklepów innych niż usługi Active Directory lub usługi Azure AD. Jeśli ten wynik blokuje migrację aplikacji do usługi Azure AD, [poinformuj nas o tym](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).          |
|UNSUPPORTED_ISSUANCE_CLASS      | Instrukcja emisji używa ADD, aby dodać oświadczenia do zestawu oświadczeń przychodzących. W usłudze Azure AD może to być skonfigurowane jako wiele przekształceń oświadczeń.Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji korporacyjnych](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).         |
|UNSUPPORTED_ISSUANCE_TRANSFORMATION      | Instrukcja emisji używa wyrażeń regularnych do przekształcenia wartości oświadczenia, które mają być emitowane.Aby osiągnąć podobną funkcjonalność w usłudze Azure AD, można użyć wstępnie zdefiniowanej transformacji, takiej jak Extract(), Trim(), ToLower, między innymi. Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji korporacyjnych](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |


## <a name="next-steps"></a>Następne kroki

- [Klip wideo: jak przeprowadzić migrację aplikacji za pomocą raportu aktywności usług AD FS](https://www.youtube.com/watch?v=OThlTA239lU)
- [Zarządzanie aplikacjami przy użyciu usługi Azure Active Directory](what-is-application-management.md)
- [Zarządzanie dostępem do aplikacji](what-is-access-management.md)
- [Federacja usługi Azure AD Connect](../hybrid/how-to-connect-fed-whatis.md)
