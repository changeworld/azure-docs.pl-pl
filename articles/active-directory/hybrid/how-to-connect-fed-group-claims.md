---
title: Skonfiguruj oświadczenia grupy dla aplikacji z Azure Active Directory | Microsoft Docs
description: Informacje na temat konfigurowania oświadczeń grupy do użycia z usługą Azure AD.
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
ms.openlocfilehash: 3cb53656adb1dbeb5e5597d02edfe5be4dbec6a8
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71170492"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory-public-preview"></a>Konfigurowanie oświadczeń grup dla aplikacji z Azure Active Directory (publiczna wersja zapoznawcza)

Azure Active Directory może zapewnić użytkownikom informacje o członkostwie w grupach, które mają być używane w aplikacjach.  Obsługiwane są dwa główne wzorce:

- Grupy identyfikowane przez ich Azure Active Directory atrybut identyfikatora obiektu (OID) (ogólnie dostępny)
- Grupy identyfikowane przez atrybuty sAMAccountName lub GroupSID dla grup i użytkowników zsynchronizowanych z usługą Active Directory (publiczna wersja zapoznawcza)

> [!IMPORTANT]
> Istnieją pewne zastrzeżenia dotyczące tej funkcji w wersji zapoznawczej:
>
>- Obsługa używania atrybutów sAMAccountName i identyfikatora zabezpieczeń (SID) synchronizowanych ze środowiska lokalnego została zaprojektowana, aby umożliwić przeniesienie istniejących aplikacji z AD FS i innych dostawców tożsamości. Grupy zarządzane w usłudze Azure AD nie zawierają atrybutów niezbędnych do emisji tych oświadczeń.
>- W dużych organizacjach liczba grup, do których należy użytkownik, może przekroczyć limit, który Azure Active Directory doda do tokenu. 150 grup dla tokenu SAML i 200 dla JWT. Może to prowadzić do nieprzewidzianych wyników. Jeśli jest to potencjalny problem, zalecamy przetestowanie, a jeśli to konieczne, dopóki nie dodamy ulepszeń, aby umożliwić ograniczenie oświadczeń do odpowiednich grup aplikacji.  
>- W przypadku tworzenia nowych aplikacji lub w przypadkach, w których można dla nich skonfigurować aplikację, a w przypadku, gdy obsługa grup zagnieżdżonych nie jest wymagana, zaleca się, aby autoryzacja w aplikacji była oparta na rolach aplikacji, a nie w grupach.  Ogranicza to ilość informacji potrzebnych do przechodzenia do tokenu, jest bezpieczniejsze i oddziela przypisanie użytkowników od konfiguracji aplikacji.

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>Oświadczenia grupy dla aplikacji migrowanych z AD FS i innych dostawców tożsamości

Wiele aplikacji skonfigurowanych do uwierzytelniania za pomocą AD FS polega na informacjach dotyczących członkostwa w grupach w postaci atrybutów grupy usługi Windows AD.   Te atrybuty to nazwa sAMAccountName grupy, która może być kwalifikowana według nazwy domeny lub identyfikatora zabezpieczeń grupy systemu Windows (GroupSID).  Gdy aplikacja jest federacyjny z AD FS, AD FS używa funkcji TokenGroups, aby pobrać członkostwo w grupach dla użytkownika.

Aby dopasować token do aplikacji otrzymanej od AD FS, oświadczenia grup i ról mogą być emitowane z kwalifikowaną nazwą domeny, a nie identyfikatorem objectID Azure Active Directory grupy.

Obsługiwane formaty oświadczeń grup są następujące:

- Identyfikator **objectid grupy Azure Active Directory** (Dostępne dla wszystkich grup)
- Nazwa **konta sam** (Dostępne dla grup synchronizowanych z Active Directory)
- **NetbiosDomain\sAMAccountName** (Dostępne dla grup synchronizowanych z Active Directory)
- **DNSDomainName\sAMAccountName** (Dostępne dla grup synchronizowanych z Active Directory)
- **Identyfikator zabezpieczeń grupy lokalnej** (Dostępne dla grup synchronizowanych z Active Directory)

> [!NOTE]
> atrybuty sAMAccountName i właściwości identyfikatora SID grupy lokalnej są dostępne tylko dla obiektów grup synchronizowanych z Active Directory.   Nie są one dostępne w grupach utworzonych w Azure Active Directory ani usługi Office 365.   Aplikacje skonfigurowane w Azure Active Directory, aby uzyskać zsynchronizowane atrybuty grupy lokalnej, pobrać je tylko dla zsynchronizowanych grup.

## <a name="options-for-applications-to-consume-group-information"></a>Opcje dla aplikacji korzystających z informacji o grupach

Jednym ze sposobów na uzyskanie informacji o grupach przez aplikacje jest wywołanie punktu końcowego grup grafów w celu pobrania członkostwa w grupie dla uwierzytelnionego użytkownika. To wywołanie zapewnia, że wszystkie grupy, do których należy użytkownik, są dostępne nawet wtedy, gdy istnieje duża liczba grup, a aplikacja musi wyliczyć wszystkie grupy, do których należy użytkownik.  Wyliczanie grupowe jest następnie niezależne od ograniczeń rozmiaru tokenu.

Jeśli jednak istniejąca aplikacja już oczekuje na korzystanie z informacji o grupach przez oświadczenia w tokenie, który odbiera, Azure Active Directory można skonfigurować przy użyciu wielu różnych opcji oświadczeń w celu dopasowania do potrzeb aplikacji.  należy wziąć pod uwagę następujące opcje:

- W przypadku korzystania z członkostwa w grupie w celach autoryzacji w aplikacji zaleca się użycie identyfikatora obiektu grupy, który jest niezmienny i unikatowy w Azure Active Directory i dostępne dla wszystkich grup.
- Jeśli używasz konta sAMAccountName grupy lokalnej do autoryzacji, użyj nazw kwalifikowanych dla domeny;  istnieje mniej możliwości wystąpienia kolizji nazw. Nazwa sAMAccountName może być unikatowa w ramach domeny Active Directory, ale jeśli więcej niż jedna domena Active Directory jest synchronizowana z dzierżawą Azure Active Directory, istnieje możliwość, że więcej niż jedna grupa ma taką samą nazwę.
- Rozważ użycie [ról aplikacji](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) , aby zapewnić warstwę pośrednią między członkostwem grupy a aplikacją.   Następnie aplikacja wykonuje wewnętrzne decyzje dotyczące autoryzacji na podstawie Clams ról w tokenie.
- Jeśli aplikacja jest skonfigurowana do pobierania atrybutów grup, które są synchronizowane z Active Directory, a grupa nie zawiera tych atrybutów, nie zostanie ona uwzględniona w oświadczeniach.
- Oświadczenia grupowe w tokenach obejmują grupy zagnieżdżone.   Jeśli użytkownik należy do GroupB, a GroupB jest członkiem grupy, wówczas oświadczenia grupy dla użytkownika będą zawierać zarówno grupę, jak i GroupB. W przypadku organizacji z dużym użyciem zagnieżdżonych grup i użytkowników z dużą liczbą członkostw w grupie liczba grup wymienionych w tokenie może zwiększyć rozmiar tokenu.   Azure Active Directory ogranicza liczbę grup, które będą emitowane w tokenie do 150 dla zatwierdzeń SAML i 200 dla tokenu JWT, aby zapobiec zbyt dużym tokenom.  Jeśli użytkownik jest członkiem większej liczby grup niż limit, grupy są emitowane wraz z linkiem do punktu końcowego grafu, aby uzyskać informacje o grupie.

> Wymagania wstępne dotyczące używania atrybutów grup synchronizowanych z Active Directory:   Grupy muszą być synchronizowane z Active Directory przy użyciu Azure AD Connect.

Istnieją dwie kroki konfigurowania Azure Active Directory, aby emitować nazwy grup dla Active Directory grup.

1. **Synchronizuj nazwy grup z Active Directory** Zanim Azure Active Directory mogą emitować nazwy grup lub lokalny identyfikator SID grupy w oświadczeniach grup lub ról, wymagane atrybuty muszą być synchronizowane z Active Directory.  Musisz mieć uruchomiony program Azure AD Connect w wersji 1.2.70 lub nowszej.   Przed wersjami 1.2.70 Azure AD Connect zsynchronizuje obiekty grupy z Active Directory, ale domyślnie nie zawiera wymaganych atrybutów nazwy grupy.  Należy uaktualnić do bieżącej wersji.

2. **Skonfiguruj rejestrację aplikacji w Azure Active Directory, aby uwzględnić oświadczenia grupy w tokenach** Oświadczenia grup można skonfigurować w sekcji aplikacje dla przedsiębiorstw w portalu dla galerii lub aplikacji rejestracji jednokrotnej SAML w galerii, lub za pomocą manifestu aplikacji w sekcji rejestracje aplikacji.  Aby skonfigurować oświadczenia grupy w manifeście aplikacji, zobacz sekcję "Konfigurowanie rejestracji aplikacji Azure Active Directory dla atrybutów grup" poniżej.

## <a name="configure-group-claims-for-saml-applications-using-sso-configuration"></a>Konfigurowanie oświadczeń grupy dla aplikacji SAML przy użyciu konfiguracji logowania jednokrotnego

Aby skonfigurować oświadczenia grupy dla galerii lub aplikacji SAML innej niż Galeria, Otwórz aplikacje dla przedsiębiorstw, kliknij aplikację na liście i wybierz opcję Konfiguracja logowania jednokrotnego.

Wybierz ikonę edycji obok pozycji "grupy zwracane w tokenie"

![Interfejs użytkownika oświadczeń](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

Użyj przycisków radiowych, aby wybrać grupy do uwzględnienia w tokenie

![Interfejs użytkownika oświadczeń](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| Wybór | Opis |
|----------|-------------|
| **Wszystkie grupy** | Emituje grupy zabezpieczeń i listy dystrybucyjne.   Powoduje również, że role katalogu, do których użytkownik jest przypisany, są emitowane w ramach roszczeń "wids" i wszystkie role aplikacji, do których użytkownik jest przypisany, w ramach roszczeń ról. |
| **Grupy zabezpieczeń** | Emituje grupy zabezpieczeń, których użytkownik jest członkiem w ramach roszczeń grupowych |
| **Listy dystrybucyjne** | Emituje listy dystrybucyjne, których użytkownik jest członkiem |
| **Role katalogu** | Jeśli użytkownik ma przypisane role katalogu, są emitowane jako wids ' (nie będą wysyłane żądania grup) |

Na przykład, aby wyemitować wszystkie grupy zabezpieczeń, których użytkownik jest członkiem, wybierz pozycję grupy zabezpieczeń

![Interfejs użytkownika oświadczeń](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

Aby emitować grupy przy użyciu atrybutów Active Directory synchronizowanych z Active Directory zamiast obiektów objectID usługi Azure AD wybierz wymagany format z listy rozwijanej.  Spowoduje to zastąpienie identyfikatora obiektu w oświadczeniach z wartościami ciągu zawierającymi nazwy grup.   Oświadczenia będą uwzględniane tylko w przypadku grup synchronizowanych z Active Directory.

![Interfejs użytkownika oświadczeń](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

### <a name="advanced-options"></a>Opcje zaawansowane

Sposób emitowania oświadczeń grup można modyfikować przez ustawienia w obszarze Opcje zaawansowane

Dostosuj nazwę żądania grupy:  W przypadku wybrania tego elementu można określić inny typ oświadczenia dla oświadczeń grupy.   Wprowadź typ zgłoszenia w polu Nazwa i opcjonalną przestrzeń nazw dla żądania w polu przestrzeń nazw.

![Interfejs użytkownika oświadczeń](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

Niektóre aplikacje wymagają, aby informacje o członkostwie w grupie były wyświetlane w ramach roszczeń "role". Opcjonalnie można emitować grupy użytkownika jako role, zaznaczając pole "Emituj grupy jako oświadczenia roli".

![Interfejs użytkownika oświadczeń](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> Jeśli opcja wyemituj dane grupy jako role są używane, w ramach żądania roli będą wyświetlane tylko grupy.  Wszystkie role aplikacji, do których użytkownik jest przypisany, nie będą wyświetlane w ramach roszczeń ról.

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>Skonfiguruj rejestrację aplikacji usługi Azure AD dla atrybutów grupy

Oświadczenia grup można również skonfigurować w sekcji [opcjonalne oświadczenia](../../active-directory/develop/active-directory-optional-claims.md) [manifestu aplikacji](../../active-directory/develop/reference-app-manifest.md).

1. W portalu > Azure Active Directory > — Rejestracja aplikacji — > Wybierz manifest > aplikacji

2. Włącz oświadczenia członkostwa w grupie, zmieniając groupMembershipClaim

   Prawidłowe wartości to:

   - Całą
   - Grupy securitygroup
   - "DistributionList"
   - "DirectoryRole"

   Na przykład:

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Domyślnie identyfikatory obiektów grupy będą emitowane w wartości Claim Group.  Aby zmodyfikować wartość żądania w taki sposób, aby zawierała atrybuty grupy lokalnej, lub aby zmienić typ typu "rola", użyj konfiguracji OptionalClaims w następujący sposób:

3. Określ ustawienia nazw grup opcjonalne oświadczenia.

   Jeśli chcesz, aby grupy w tokenie zawierały atrybuty lokalnych grup usługi AD w sekcji opcjonalne oświadczenia, określ, które opcjonalne oświadczenie typu tokenu należy zastosować do, nazwę opcjonalnego oświadczenia, a także wszystkie wymagane właściwości.  Można wymienić wiele typów tokenów:

   - idToken dla tokenu identyfikatora OIDC
   - accessToken dla tokenu dostępu OAuth/OIDC
   - Saml2Token dla tokenów SAML.

   > [!NOTE]
   > Typ Saml2Token ma zastosowanie do tokenów formatu SAML 1.1 i SAML 2.0

   Dla każdego odpowiedniego typu tokenu zmodyfikuj je, aby użyć sekcji OptionalClaims w manifeście. Schemat OptionalClaims jest następujący:

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | Opcjonalny schemat oświadczeń | Value |
   |----------|-------------|
   | **Nazwij** | Musi być "grupami" |
   | **zewnętrz** | Nieużywane. Pomiń lub określ wartość null |
   | **olejk** | Nieużywane. Pomiń lub określ wartość false |
   | **AdditionalProperties** | Lista dodatkowych właściwości.  Prawidłowe opcje to "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   W additionalProperties są wymagane tylko jeden z "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name".  Jeśli istnieje więcej niż jeden, zostanie użyta pierwsza wartość i wszystkie pozostałe zostały zignorowane.

   Niektóre aplikacje wymagają informacji o grupie dla użytkownika w ramach roszczeń ról.  Aby zmienić typ roszczeń na z na podstawie zgłoszenia do roli, należy dodać "emit_as_roles" do dodatkowych właściwości.  Wartości grupy będą emitowane w ramach roszczeń ról.

   > [!NOTE]
   > Jeśli jest używana wartość "emit_as_roles", wszystkie role aplikacji skonfigurowane do przypisania użytkownika nie będą wyświetlane w ramach roszczeń ról

### <a name="examples"></a>Przykłady

Emituj grupy jako nazwy grup w tokenach dostępu OAuth w formacie dnsDomainName\SAMAccountName

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Aby emitować nazwy grup, które mają być zwracane w formacie netbiosDomain\samAccountName jako rolę role w tokenach identyfikatorów SAML i OIDC:

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
