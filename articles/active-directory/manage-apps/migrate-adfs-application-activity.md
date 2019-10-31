---
title: Użyj raportu działania, aby przenieść AD FS aplikacje do Azure Active Directory | Microsoft Docs "
description: Raport działania aplikacji Active Directory Federation Services (AD FS) umożliwia szybkie Migrowanie aplikacji z AD FS do Azure Active Directory (Azure AD). To narzędzie do migracji AD FS identyfikuje zgodność z usługą Azure AD i zapewnia wskazówki dotyczące migracji.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/30/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10a267811b7e7ac8715b6823a24c2b3a1f0d430c
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73180539"
---
# <a name="use-the-ad-fs-application-activity-report-preview-to-migrate-applications-to-azure-ad"></a>Korzystanie z raportu działania aplikacji AD FS (wersja zapoznawcza) w celu migrowania aplikacji do usługi Azure AD

Wiele organizacji używa Active Directory Federation Services (AD FS), aby zapewnić Logowanie jednokrotne do aplikacji w chmurze. Istnieją znaczne korzyści związane z przenoszenium AD FS aplikacji do usługi Azure AD w celu uwierzytelniania, szczególnie w zakresie zarządzania kosztami, zarządzania ryzykiem, produktywności, zgodności i zarządzania. Należy jednak zrozumieć, które aplikacje są zgodne z usługą Azure AD, a określenie konkretnych kroków migracji może być czasochłonne.

Raport aktywność aplikacji AD FS (wersja zapoznawcza) w Azure Portal pozwala szybko określić, które aplikacje mogą być migrowane do usługi Azure AD. Ocenia wszystkie AD FS aplikacje pod kątem zgodności z usługą Azure AD, sprawdza pod kątem problemów i zapewnia wskazówki dotyczące przygotowania poszczególnych aplikacji do migracji. Za pomocą raportu działania aplikacji AD FS można:

* **Odkryj AD FS aplikacje i zakres migracji.** Raport aktywność aplikacji AD FS zawiera listę wszystkich aplikacji AD FS w organizacji i wskazuje ich gotowość do migracji do usługi Azure AD.
* **Ustalanie priorytetów aplikacji do migracji.** Uzyskaj liczbę unikatowych użytkowników, którzy zalogowali się do aplikacji w ciągu ostatnich 1, 7 lub 30 dni, aby pomóc w ustaleniu zagrożenia lub ryzyka migracji aplikacji.
* **Uruchamianie testów migracji i rozwiązywanie problemów.** Usługa raportowania automatycznie uruchamia testy, aby określić, czy aplikacja jest gotowa do migracji. Wyniki są wyświetlane w raporcie AD FS działanie aplikacji jako stan migracji. W przypadku zidentyfikowania potencjalnych problemów z migracją należy uzyskać szczegółowe wskazówki dotyczące rozwiązywania problemów.

AD FS dane działania aplikacji są dostępne dla użytkowników, którym przypisano dowolne z tych ról administratora: Administrator globalny, czytelnik raportu, czytelnik zabezpieczeń, administrator aplikacji lub administrator aplikacji w chmurze.

## <a name="prerequisites"></a>Wymagania wstępne

* Twoja organizacja musi obecnie używać AD FS, aby uzyskiwać dostęp do aplikacji.
* Należy włączyć Azure AD Connect Health w dzierżawie usługi Azure AD.
   * [Dowiedz się więcej o Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)
   * [Wprowadzenie do konfigurowania Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

## <a name="discover-ad-fs-applications-that-can-be-migrated"></a>Odkryj AD FS aplikacje, które można migrować 

Raport aktywność aplikacji AD FS jest dostępny w Azure Portal w obszarze usługi Azure AD **Usage & Insights** Reporting. Raport aktywność aplikacji AD FS analizuje każdą aplikację AD FS, aby określić, czy można przeprowadzić migrację w niezmienionej postaci lub jeśli jest wymagana dodatkowa weryfikacja. 

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu roli administratora, która ma dostęp do AD FS danych działania aplikacji (Administrator globalny, czytelnik raportu, czytelnik zabezpieczeń, administrator aplikacji lub administrator aplikacji w chmurze).

2. Wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **aplikacje dla przedsiębiorstw**.

3. W obszarze **działanie**wybierz pozycję **użycie & Insights (wersja zapoznawcza)** , a następnie wybierz pozycję **AD FS aktywność aplikacji** , aby otworzyć listę wszystkich AD FS aplikacji w organizacji.

   ![Działanie aplikacji AD FS](media/migrate-adfs-application-activity/adfs-application-activity.png)

4. Dla każdej aplikacji na liście działanie AD FS aplikacji Wyświetl **stan migracji**:

   * **Przygotowana do migracji** oznacza, że konfiguracja aplikacji AD FS jest w pełni obsługiwana w usłudze Azure AD i można ją migrować w taki sam sposób.

   * **Wymaga przeglądu** oznacza, że niektóre ustawienia aplikacji można migrować do usługi Azure AD, ale musisz przejrzeć ustawienia, których nie można migrować w stanie takim, w jakim się znajdują.

   * **Wymagane dodatkowe kroki** oznacza, że usługa Azure AD nie obsługuje niektórych ustawień aplikacji, więc nie można migrować aplikacji w jej bieżącym stanie.

## <a name="evaluate-the-readiness-of-an-application-for-migration"></a>Oceń gotowość aplikacji do migracji 

1. Na liście działanie aplikacji AD FS kliknij stan w kolumnie **stan migracji** , aby otworzyć Szczegóły migracji. Zobaczysz podsumowanie testów konfiguracji, które zakończono, wraz z potencjalnymi problemami migracji.

   ![Szczegóły migracji](media/migrate-adfs-application-activity/migration-details.png)

2. Kliknij komunikat, aby otworzyć dodatkowe szczegóły reguły migracji. Aby zapoznać się z pełną listą przetestowanych właściwości, zapoznaj się z tabelą [testy konfiguracji aplikacji AD FS](#ad-fs-application-configuration-tests) poniżej.

   ![Szczegóły reguły migracji](media/migrate-adfs-application-activity/migration-rule-details.png)

### <a name="ad-fs-application-configuration-tests"></a>AD FS testy konfiguracji aplikacji

W poniższej tabeli wymieniono wszystkie testy konfiguracji, które są wykonywane na AD FS aplikacji.

|Właściwość  |Stan  |Opis  |
|---------|---------|---------|
|Wykryto co najmniej jedną regułę migrowalna dla AdditionalAuthentication.       | Przekaż/Ostrzegaj          | Jednostka uzależniona w AD FS korzysta z lokalnego dostawcy usługi MFA. Aby przejść do usługi Azure AD, zalecamy przejście do integracji usługi Azure MFA lub kontrolek niestandardowych z dostawcą MFA innej firmy.  [Dowiedz się więcej o usłudze Azure MFA](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).        |
|Jednostka uzależniona ma AdditionalWSFedEndpoint ustawioną wartość true.       | Przebieg/niepowodzenie          | Jednostka uzależniona w AD FS umożliwia korzystanie z wielu punktów końcowych potwierdzeń usługi WS-karmione. Usługa Azure AD obsługuje tylko jeden z tych obecnych. Jeśli masz scenariusz, w którym jest blokowany proces migracji, [daj nam znać](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695621-allow-multiple-ws-fed-assertion-endpoints).     |
|Jednostka uzależniona ustawił AllowedAuthenticationClassReferences.       | Przebieg/niepowodzenie          | Jest to ustawienie w AD FS, które pozwala określić, czy aplikacja jest skonfigurowana tak, aby zezwalać tylko na określone typy uwierzytelniania. Usługa Azure AD nie obsługuje tego obecnie. Jeśli masz scenariusz, w którym jest blokowany proces migracji, [daj nam znać](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695672-allow-in-azure-ad-to-specify-certain-authentication).          |
|AlwaysRequireAuthentication jest skonfigurowany.      | Przebieg/niepowodzenie          | Określa, czy aplikacja jest skonfigurowana do ignorowania plików cookie logowania jednokrotnego i "zawsze Monituj o uwierzytelnienie". Obecnie nie jest obsługiwane przez usługę Azure AD.          |
|Jednostka uzależniona ma AutoUpdateEnabled ustawioną wartość PRAWDA       | Przekaż/Ostrzegaj          | Jest to ustawienie w AD FS, które pozwala określić, czy AD FS jest skonfigurowany do aktualizowania aplikacji na podstawie zmian w metadanych Federacji. Usługa Azure AD nie obsługuje tego dzisiaj, ale nie należy blokować migracji aplikacji do usługi Azure AD.           |
|Jednostka uzależniona ma włączoną wiele ClaimsProviders       | Przebieg/niepowodzenie          | Jednostka uzależniona jest skonfigurowana do oświadczeń źródłowych od dostawcy oświadczeń innego niż Active Directory.  Ta usługa nie jest obsługiwana w usłudze Azure AD. Jeśli masz scenariusz, w którym jest blokowany proces migracji, [daj nam znać](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).          |
|DelegationAuthorization jest prawidłowy      | Przebieg/niepowodzenie          | Aplikacja ma zdefiniowane niestandardowe reguły autoryzacji delegowania. Usługa Azure AD nie obsługuje tego obecnie. Jeśli masz scenariusz, w którym jest blokowany proces migracji, [daj nam znać](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695747-allow-to-define-delegation-authorization-rules).          |
|Jednostka uzależniona ma ADFSRPImpersonationAuthorizationRules       | Przekaż/Ostrzegaj          | Aplikacja ma zdefiniowane niestandardowe reguły autoryzacji personifikacji. Usługa Azure AD nie obsługuje tego dzisiaj, ale nie należy blokować migracji aplikacji do usługi Azure AD.          |
|Wykryto co najmniej jedną regułę migrowalna dla IssuanceAuthorization.       | Przekaż/Ostrzegaj          | Aplikacja ma niestandardowe reguły autoryzacji wystawiania zdefiniowane w AD FS. Usługa Azure AD obsługuje tę funkcję przy użyciu dostępu warunkowego usługi Azure AD. [Dowiedz się więcej na temat dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/overview). Ponadto w usłudze Azure AD można ograniczyć dostęp do aplikacji dla użytkowników lub grup przypisanych do aplikacji. [Dowiedz się więcej o przypisywaniu użytkowników i grup w celu uzyskania dostępu do aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups). Zapoznaj się z tabelą [testów reguł dotyczących roszczeń](#check-the-results-of-claim-rule-tests)poniżej, aby uzyskać informacje na temat nieprzetworzonych reguł IssuanceAuthorization skonfigurowanych w AD FS.           |
|Wykryto co najmniej jedną regułę migrowalna dla IssuanceTransform.       | Przekaż/Ostrzegaj          | Aplikacja ma niestandardowe reguły przekształcania wystawiania zdefiniowane w AD FS. Usługa Azure AD obsługuje Dostosowywanie oświadczeń wystawionych w tokenie. Aby dowiedzieć się więcej, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).  Zapoznaj się z tabelą [testów reguł dotyczących roszczeń](#check-the-results-of-claim-rule-tests)poniżej, aby uzyskać informacje na temat nieprzetworzonych reguł IssuanceTransformation skonfigurowanych w AD FS.          |
|Jednostka uzależniona ma parametry monitoringenabled ustawioną wartość true.       | Przekaż/Ostrzegaj          | Jest to ustawienie w AD FS, które pozwala określić, czy AD FS jest skonfigurowany do monitorowania metadanych Federacji dla tej aplikacji. Ta operacja nie jest obsługiwana w usłudze Azure AD, ale nie powinna blokować migracji aplikacji do usługi Azure AD.          |
|Jednostka uzależniona ma NotBeforeSkew konfigurację.      | Przekaż/Ostrzegaj          | AD FS umożliwia przechylenie czasu na podstawie czasów NotBefore i NotOnOrAfter w tokenie SAML. Usługa Azure AD nie obsługuje tego dzisiaj, ale nie należy blokować migracji aplikacji do usługi Azure AD.          |
|Jednostka uzależniona ma RequestMFAFromClaimsProviders ustawioną wartość true.       | Przekaż/Ostrzegaj          | Jest to ustawienie w AD FS, które pozwala określić, czy aplikacja jest stałe do innego dostawcy roszczeń, i wymaga uwierzytelniania wieloskładnikowego. Aby przejść do usługi Azure AD, zalecamy przejście do integracji usługi Azure MFA lub formantów niestandardowych z dostawcą MFA innej firmy.  [Dowiedz się więcej o usłudze Azure MFA](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).          |
|Jednostka uzależniona ma SignedSamlRequestsRequired ustawioną wartość PRAWDA       | Przebieg/niepowodzenie          | Aplikacja jest skonfigurowana w AD FS, aby zweryfikować podpis w żądaniu SAML. Jest to ustawienie opcjonalne i nie powinno blokować migracji. Azure Active Directory używa adresów URL odpowiedzi do zweryfikowania dostawcy usług.  Jeśli masz scenariusz, w którym jest blokowany proces migracji, [daj nam znać](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13394589-saml-signature).          |
|TokenLifetimeCheckResult        | Przebieg/niepowodzenie         | Aplikacja jest skonfigurowana pod kątem niestandardowego okresu istnienia tokenu. Wartość domyślna AD FS to 1 godzina. Usługa Azure AD obsługuje tę funkcję przy użyciu dostępu warunkowego. Aby dowiedzieć się więcej, zobacz [Konfigurowanie zarządzania sesjami uwierzytelniania przy użyciu dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime).          |
|Jednostka uzależniona jest ustawiona na szyfrowanie oświadczeń. Jest to obsługiwane przez usługę Azure AD       | Chodzenia          | Za pomocą usługi Azure AD można zaszyfrować wysyłanie tokenu do aplikacji. Aby dowiedzieć się więcej, zobacz [Konfigurowanie szyfrowania tokenów SAML usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).          |
|Jednostka uzależniona jest ustawiona na szyfrowanie NameID w tokenie SAML.      | Przebieg/niepowodzenie          | Aplikacja jest skonfigurowana do szyfrowania żądania nameID w tokenie SAML. Za pomocą usługi Azure AD można zaszyfrować cały token wysłany do aplikacji. Szyfrowanie określonych oświadczeń nie jest jeszcze obsługiwane. Aby dowiedzieć się więcej, zobacz [Konfigurowanie szyfrowania tokenów SAML usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).         |

## <a name="check-the-results-of-claim-rule-tests"></a>Sprawdź wyniki testów reguł dotyczących roszczeń

Jeśli skonfigurowano regułę dotyczącej roszczeń dla aplikacji w AD FS, środowisko to zapewni szczegółową analizę wszystkich reguł dotyczących roszczeń. Zobaczysz, które reguły dotyczące roszczeń można przenieść do usługi Azure AD, a które wymagają dalszych przeglądów.

1. Na liście działanie aplikacji AD FS kliknij stan w kolumnie **stan migracji** , aby otworzyć Szczegóły migracji. Zobaczysz podsumowanie testów konfiguracji, które zakończono, wraz z potencjalnymi problemami migracji.

2. Na stronie **szczegóły reguły migracji** rozwiń wyniki, aby wyświetlić szczegółowe informacje o potencjalnych problemach migracji i uzyskać dodatkowe wskazówki. Aby uzyskać szczegółową listę wszystkich przetestowanych reguł dotyczących roszczeń, zobacz sekcję [Sprawdź wyniki testów reguł dotyczących roszczeń](#check-the-results-of-claim-rule-tests) poniżej.

   W poniższym przykładzie przedstawiono szczegóły reguły migracji dla reguły IssuanceTransform. Zawiera on listę konkretnych części, które należy przejrzeć i rozwiązać, aby można było przeprowadzić migrację aplikacji do usługi Azure AD.

   ![Dodatkowe wskazówki dotyczące reguł migracji](media/migrate-adfs-application-activity/migration-rule-details-guidance.png)

### <a name="claim-rule-tests"></a>Testy reguły dotyczącej roszczeń

W poniższej tabeli wymieniono wszystkie testy reguł dotyczących roszczeń, które są wykonywane na AD FS aplikacjach.

|Właściwość  |Opis  |
|---------|---------|
|UNSUPPORTED_CONDITION_PARAMETER      | Instrukcja Condition używa wyrażeń regularnych w celu obliczenia, czy oświadczenie pasuje do określonego wzorca.  Aby osiągnąć podobną funkcjonalność w usłudze Azure AD, możesz użyć wstępnie zdefiniowanego przekształcenia, takiego jak IfEmpty (), StartWith (), zawiera (), między innymi. Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|UNSUPPORTED_CONDITION_CLASS      | Instrukcja Condition ma wiele warunków, które muszą być oceniane przed uruchomieniem instrukcji wystawiania. Usługa Azure AD może obsługiwać tę funkcję za pomocą funkcji przekształcania roszczeń, w których można oszacować wiele wartości.  Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|UNSUPPORTED_RULE_TYPE      | Nie można rozpoznać reguły dotyczącej roszczeń. Aby uzyskać więcej informacji na temat konfigurowania oświadczeń w usłudze Azure AD, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|CONDITION_MATCHES_UNSUPPORTED_ISSUER      | Instrukcja Condition używa wystawcy, który nie jest obsługiwany w usłudze Azure AD. Obecnie usługa Azure AD nie jest źródłem oświadczeń z magazynów innych niż Active Directory lub Azure AD. Jeśli uniemożliwiasz Migrowanie aplikacji do usługi Azure AD, [daj nam znać](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).         |
|UNSUPPORTED_CONDITION_FUNCTION      | Instrukcja Condition używa funkcji agregującej, aby wystawić lub dodać pojedyncze oświadczenie, niezależnie od liczby dopasowań.  W usłudze Azure AD można oszacować atrybut użytkownika w celu określenia wartości, która ma być używana dla tego żądania z funkcjami takimi jak IfEmpty (), StartWith (), zawiera (), między innymi. Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|RESTRICTED_CLAIM_ISSUED      | Instrukcja Condition używa oświadczenia, które jest ograniczone w usłudze Azure AD. Może być możliwe wystawienie ograniczonego odszkodowania, ale nie można zmodyfikować jego źródła ani zastosować żadnego przekształcenia. Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń emitowanych w tokenach dla określonej aplikacji w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).          |
|EXTERNAL_ATTRIBUTE_STORE      | Instrukcja wystawiania używa magazynu atrybutów innego, który Active Directory. Obecnie usługa Azure AD nie jest źródłem oświadczeń z magazynów innych niż Active Directory lub Azure AD. Jeśli uniemożliwiasz Migrowanie aplikacji do usługi Azure AD, [daj nam znać](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).          |
|UNSUPPORTED_ISSUANCE_CLASS      | Instrukcja wystawiania służy do dodawania oświadczeń do zestawu oświadczeń przychodzących. W usłudze Azure AD można skonfigurować jako wiele przekształceń roszczeń.  Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).         |
|UNSUPPORTED_ISSUANCE_TRANSFORMATION      | Instrukcja wystawiania używa wyrażeń regularnych w celu przekształcenia wartości oświadczenia do emisji. Aby osiągnąć podobną funkcjonalność w usłudze Azure AD, możesz użyć wstępnie zdefiniowanego przekształcenia, takiego jak Extract (), Trim (), ToLower, między innymi. Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |


## <a name="next-steps"></a>Następne kroki

- [Zarządzanie aplikacjami przy użyciu usługi Azure Active Directory](what-is-application-management.md)
- [Zarządzanie dostępem do aplikacji](what-is-access-management.md)
- [Program Azure AD Connect a federacja](../hybrid/how-to-connect-fed-whatis.md)
