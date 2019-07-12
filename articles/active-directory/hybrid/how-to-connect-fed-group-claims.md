---
title: Konfigurowanie oświadczenia grupy dla aplikacji przy użyciu usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Informacje o sposobie konfigurowania grupy oświadczenia do użycia z usługą Azure AD.
services: active-directory
documentationcenter: ''
ms.reviewer: paulgarn
manager: daveba
ms.subservice: hybrid
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/27/2019
ms.author: billmath
author: billmath
ms.openlocfilehash: 2d547c73137605e4666499b568bdcebce394935a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595224"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory-public-preview"></a>Konfigurowanie oświadczenia grupy dla aplikacji przy użyciu usługi Azure Active Directory (publiczna wersja zapoznawcza)

Usługa Azure Active Directory może zapewnić użytkownikom informacje dotyczące członkostwa grupy w tokenach do użytku w aplikacjach.  Obsługiwane są dwa główne wzorce:

- Grupy identyfikowane za pomocą ich identyfikatora obiektu w usłudze Azure Active Directory (dostępne ogólnie) atrybutu (OID)
- Grupy określone przez element sAMAccountName lub identyfikatora GroupSID atrybuty dla Active Directory (AD) synchronizowane grupy i użytkownicy (publiczna wersja zapoznawcza)

> [!IMPORTANT]
> Istnieje szereg ostrzeżenia, należy pamiętać, dla tej funkcji w wersji zapoznawczej:
>
>- Obsługa użycia sAMAccountName zabezpieczeń identyfikator (SID) dla atrybutów i synchronizowane z lokalnej umożliwiających przenoszenie istniejących aplikacji z usług AD FS i innych dostawców tożsamości. Grupy zarządzane w usłudze Azure AD nie zawierają atrybuty, które są niezbędne do emitowania te oświadczenia.
>- W przypadku większych organizacji liczby grup, do których należy użytkownik może przekroczyć limit usługi Azure Active Directory zostanie dodany do tokenu. 150 grupy dla tokenu SAML i 200 dla token JWT. Może to prowadzić do nieoczekiwanych rezultatów. Jeśli jest to potencjalny problem Zalecamy przeprowadzenie testowania, a jeśli to konieczne, oczekiwanie, aż do dodamy udoskonalenia pozwalają ograniczyć oświadczenia do odpowiednich grup dla aplikacji.  
>- Rozwijaniu nowych aplikacji lub w sytuacjach, w którym aplikacji można skonfigurować dla niego i której Obsługa grup zagnieżdżonych nie jest wymagane zaleca się, że autoryzacji w aplikacji zależy od ról aplikacji, a nie grupy.  Ogranicza to ilość informacji, który wymaga przejść do tokenu, zapewnia większe bezpieczeństwo i oddziela przypisanie użytkownika z konfiguracji aplikacji.

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>Oświadczenia grupy dla aplikacji, migrowanie z usług AD FS i innych dostawców tożsamości

Wiele aplikacji, które są skonfigurowane do uwierzytelniania za pomocą usług AD FS opierają się na informacje o członkostwie w grupie w formularzu Windows AD grupy atrybutów.   Te atrybuty są sAMAccountName grupy, która może być kwalifikowana przez — nazwa domeny lub identyfikator zabezpieczeń grupy systemu Windows (identyfikatora GroupSID).  Gdy aplikacja jest Sfederowane przy użyciu usług AD FS, usługi AD FS funkcja TokenGroups pobrać członkostwa w grupach użytkownika.

Aby dopasować token, który aplikacja może odbierać z usług AD FS, być emitowane oświadczenia grupy i roli, zawierające sam domeny kwalifikowana, a nie grupy usługi Azure Active Directory objectID.

Obsługiwane formaty oświadczenia grupy są:

- **Identyfikator obiektu grupy usługi Azure Active Directory** (dostępne dla wszystkich grup)
- **Element SAMAccountName** (dostępne dla grupy synchronizowane z usługi Active Directory)
- **NetbiosDomain\sAMAccountName** (dostępne dla grupy synchronizowane z usługi Active Directory)
- **DNSDomainName\sAMAccountName** (dostępne dla grupy synchronizowane z usługi Active Directory)
- **Na identyfikator zabezpieczeń grupy lokalnej** (dostępne dla grupy synchronizowane z usługi Active Directory)

> [!NOTE]
> Element sAMAccountName i atrybutów w lokalnym identyfikator SID grupy są dostępne tylko w obiektach grupy synchronizowane z usługi Active Directory.   Nie są dostępne dla grup utworzonych w usłudze Azure Active Directory lub usługi Office 365.   Aplikacji skonfigurowanych w usłudze Azure Active Directory w celu uzyskania lokalnego zsynchronizowanych atrybuty grupy pobranie tylko grupy zsynchronizowane.

## <a name="options-for-applications-to-consume-group-information"></a>Opcje dla aplikacji korzystających ze informacje o grupie

Jednym ze sposobów, w przypadku aplikacji uzyskać informacje o grupie jest wywołać punkt końcowy programu Graph grup w celu pobrania członkostwa w grupie dla tego uwierzytelnionego użytkownika. To wywołanie powoduje, że wszystkich grup, do których użytkownik jest członkiem są dostępne, nawet wtedy, gdy są zaangażowani dużą liczbę grup i wymagane przez aplikację do wyliczenia wszystkich grup, których użytkownik jest członkiem.  Wyliczenie grupy następnie jest niezależna od ograniczenia rozmiaru tokenu.

Jednak jeśli już istniejąca aplikacja oczekuje na korzystanie z informacji o grupie za pomocą oświadczenia w tokenie, które otrzymuje, usługi Azure Active Directory można skonfigurować za pomocą szereg opcji różne oświadczenia do potrzeb aplikacji.  należy wziąć pod uwagę następujące opcje:

- Na potrzeby autoryzacji w aplikacji za pomocą członkostwa w grupie jest lepiej jest używać identyfikator obiektu grupy, który jest niezmienny i unikatowy w usłudze Azure Active Directory i dostępne dla wszystkich grup.
- Jeśli używasz lokalny element sAMAccountName grupy do autoryzacji, należy używać nazw domeny kwalifikowana;  ma mniej szansy sytuacji powstałych było konflikt nazw. SAMAccountName samodzielnie mogą być unikatowe w obrębie domeny usługi Active Directory, ale jeśli więcej niż jedną domenę usługi Active Directory jest zsynchronizowana z dzierżawą usługi Azure Active Directory istnieje możliwość mają taką samą nazwę więcej niż jednej grupy.
- Należy rozważyć użycie [ról aplikacji](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) zapewnić warstwę pośredni między członkostwa w grupie i aplikacji.   Następnie aplikacja sprawia, że decyzji dotyczących autoryzacji wewnętrznego, oparte na rolach piaskołaz w tokenie.
- Jeśli aplikacja jest skonfigurowane do otrzymywania atrybuty grupy, które są synchronizowane z usługi Active Directory, a grupy nie zawierają te atrybuty nie będą uwzględniane w oświadczeniach.
- Grupy oświadczenia w tokenach Dołącz grupy zagnieżdżone.   Jeśli użytkownik jest członkiem GroupB GroupB jest elementem członkowskim GroupA, oświadczenia grupy dla użytkownika będzie zawierać zarówno GroupA, jak i GroupB. Organizacje mające duże użycie zagnieżdżonych grup i użytkowników z dużą liczbą członkostwa w grupach liczby grup token na liście można powiększać rozmiar tokenu.   Usługa Azure Active Directory ogranicza liczbę grup, które będzie naliczana w tokenie do 150 dla asercji SAML i 200 dla tokenów JWT zapobiec tokenów wprowadzenie zbyt duży.  Jeśli użytkownik jest członkiem większej liczby grup niż limit, są emitowane grupy i łącza do punktu końcowego wykres, aby uzyskać informacje o grupie.

> Wymagania wstępne dotyczące korzystania z atrybuty grupy synchronizowane z usługą Active Directory:   Grupy musi być synchronizowane z usługi Active Directory za pomocą usługi Azure AD Connect.

Istnieją dwie instrukcje konfigurowania usługi Azure Active Directory, aby emitować nazwy grupy dla grupy usługi Active Directory.

1. **Synchronizuj nazwy grup z usługi Active Directory** przed usługi Azure Active Directory może emitować nazwy grup lub w lokalnej grupie oświadczenia identyfikatora SID grupy lub roli, wymaganych atrybutów, które muszą zostać zsynchronizowane z usługą Active Directory.  Wymagany jest program Azure AD Connect w wersji 1.2.70 lub nowszej.   Przed wersją 1.2.70 program Azure AD Connect będzie synchronizować obiekty grupy z usługi Active Directory, ale nie zawiera wymaganej grupy atrybutów nazw domyślnie.  Należy uaktualnić do wersji bieżącej.

2. **Skonfiguruj rejestrowanie aplikacji w usłudze Azure Active Directory, które mają zostać objęte oświadczenia grupy tokenów** oświadczenia grupy mogą być skonfigurowane w sekcji aplikacje dla przedsiębiorstw w portalu dla aplikacji lub logowania jednokrotnego SAML spoza galerii w galerii lub w sekcji rejestracje aplikacji przy użyciu Manifest aplikacji.  Aby skonfigurować oświadczenia grupy, zobacz manifestu aplikacji "Konfigurowanie usługi Azure Active Directory aplikacji rejestracji dla grupy atrybutów" poniżej.

## <a name="configure-group-claims-for-saml-applications-using-sso-configuration"></a>Konfigurowanie oświadczenia grupy dla aplikacji SAML przy użyciu konfiguracji logowania jednokrotnego

Aby skonfigurować oświadczenia grupy dla aplikacji spoza galerii SAML lub w galerii, otwórz aplikacje dla przedsiębiorstw, kliknij przyciskiem myszy aplikację na liście i wybierz konfigurację logowania jednokrotnego.

Wybierz ikonę edycji obok "Grup zwrócone w tokenie"

![oświadczenia interfejsu użytkownika](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

Użyj przycisków radiowych, aby wybrać grupy, które powinny być uwzględnione w tokenie

![oświadczenia interfejsu użytkownika](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| Wybór | Opis |
|----------|-------------|
| **Wszystkie grupy** | Emituje grup zabezpieczeń i dystrybucji listy.   Powoduje również role katalogu, które są przypisane do użytkownika był emitowany w oświadczenie "wids" i ewentualnych ról aplikacji, przypisanego był emitowany w oświadczenia ról użytkownika. |
| **Grupy zabezpieczeń** | Emituje grup zabezpieczeń, których użytkownik jest członkiem oświadczenia grupy |
| **Listy dystrybucyjne** | Emituje list dystrybucyjnych, w których należy użytkownik |
| **Role katalogu** | Jeśli użytkownik jest przypisany ról w katalogu są emitowane jako wids oświadczenia (grupy, który nie będzie emitowane oświadczeń) |

Na przykład aby emitować wszystkich grup zabezpieczeń, użytkownik jest członkiem, wybierz grupy zabezpieczeń

![oświadczenia interfejsu użytkownika](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

Aby emitować grup przy użyciu atrybutów usługi Active Directory synchronizowane z usługi Active Directory zamiast identyfikatory obiektów usługi Azure AD wybierz wymagany format z listy rozwijanej.  Spowoduje to zastąpienie Identyfikatora obiektu w oświadczeniach przy użyciu wartości ciągu zawierającej nazwy grupy.   Tylko grupy synchronizowane z usługą Active Directory zostaną uwzględnione w oświadczeniach.

![oświadczenia interfejsu użytkownika](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

### <a name="advanced-options"></a>Opcje zaawansowane

Sposób oświadczenia grupy są emitowane może być modyfikowana przez ustawienia w obszarze Opcje zaawansowane

Dostosowywanie nazwy oświadczenia grupy:  Jeśli zaznaczone, można określić na inny typ oświadczenia dla oświadczenia grupy.   Wprowadź w polu nazwę i opcjonalny przestrzeni nazw typu oświadczenia oświadczenie, w polu obszar nazw.

![oświadczenia interfejsu użytkownika](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

Niektóre aplikacje wymagają informacji o członkostwie grupy pojawią się w oświadczenie "roli". Grupy użytkowników może opcjonalnie emitować jako role, zaznaczając pole "Emitować grup, które oświadczenia roli".

![oświadczenia interfejsu użytkownika](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> Jeśli jest używana opcja do emitowania danych grupy jako role, tylko grupy zostaną wyświetlone w oświadczenie roli.  Wszystkie role aplikacji, które są przypisane do użytkownika nie będą widoczne w oświadczenie roli.

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>Konfigurowanie platformy Azure rejestrowanie aplikacji usługi AD dla grupy atrybutów

Można również skonfigurować oświadczenia grupy w [opcjonalnych oświadczeń](../../active-directory/develop/active-directory-optional-claims.md) części [Manifest aplikacji](../../active-directory/develop/reference-app-manifest.md).

1. W portalu usługi -> Azure Active Directory -> Aplikacja rejestracje -> Wybierz aplikacji -> manifestu

2. Włączyć oświadczenia członkostwo grupy, zmieniając groupMembershipClaim

   Prawidłowe wartości to:

   - "Wszystkie"
   - "SecurityGroup"
   - "DistributionList"
   - "DirectoryRole"

   Na przykład:

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Domyślnie, których identyfikatory obiektów grupy będzie obliczanie w grupie wartości oświadczenia.  Aby zmodyfikować wartość oświadczenia, zawierają atrybuty grupy lokalnej lub zmienić typ oświadczenia roli, należy użyć OptionalClaims konfiguracji w następujący sposób:

3. Ustaw oświadczenia opcjonalny konfiguracji nazwę grupy.

   Jeśli chcesz, aby grupy w tokenie zawierać lokalnych, w których atrybuty grupy usługi AD w sekcji opcjonalnych oświadczeń, określ, której typ tokenu opcjonalnego roszczenia powinny być stosowane do, nazwę opcjonalnego roszczenia, żądane i wszelkie dodatkowe właściwości żądanego.  Mogą być wyświetlane wiele typów tokenu:

   - idToken dla tokenu Identyfikacyjnego OIDC
   - accessToken dla tokenu dostępu OAuth/OIDC
   - Saml2Token tokeny SAML.

   > [!NOTE]
   > Typ Saml2Token dotyczy zarówno SAML1.1 i SAML2.0 tokeny format

   Dla każdego odpowiedniego typu tokenu zmodyfikuj oświadczenia grupy do użycia w sekcji OptionalClaims w manifeście. Schemat OptionalClaims jest w następujący sposób:

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | Schemat opcjonalnych oświadczeń | Wartość |
   |----------|-------------|
   | **Nazwa:** | Musi być "groups" |
   | **Źródło:** | Nie jest używany. Pomiń lub określ wartość null |
   | **podstawowe:** | Nie jest używany. Pomiń lub określić wartość false |
   | **additionalProperties:** | Lista właściwości dodatkowych.  Prawidłowe opcje to "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   W dodatkowe właściwości, tylko jeden "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name" są wymagane.  Jeżeli istnieje więcej niż jeden, pierwszy jest używany i ignorowane przez inne osoby.

   Niektóre aplikacje wymagają grupy informacje o użytkowniku w oświadczenie roli.  Aby zmienić typ oświadczenia z oświadczenia grupy oświadczenie roli, należy dodać "emit_as_roles" do właściwości dodatkowych.  Wartości grupy będzie emitowane w oświadczenie roli.

   > [!NOTE]
   > Jeśli jest używany "emit_as_roles" wszystkie role aplikacji skonfigurowane są przypisane do użytkownika zostanie pojawia się w oświadczenie roli

### <a name="examples"></a>Przykłady

Emituj grup jako nazwy grup w tokenów dostępu protokołu OAuth w formacie dnsDomainName\SAMAccountName

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Aby emitować nazw grup, które mają zostać zwrócone w formacie netbiosDomain\samAccountName, zgodnie z rolami oświadczenia języka SAML i tokeny Identyfikatora OIDC:

```json
"optionalClaims": {
    "saml2Token": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }],

    "idToken": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }]
 }
 ```

## <a name="next-steps"></a>Następne kroki

[Co to jest tożsamość hybrydowa?](whatis-hybrid-identity.md)
