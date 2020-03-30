---
title: Konfigurowanie oświadczeń grup dla aplikacji za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft
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
ms.openlocfilehash: b8708aec1137836516852135412c4c7cec2feba4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408406"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory-public-preview"></a>Konfigurowanie oświadczeń grup dla aplikacji za pomocą usługi Azure Active Directory (publiczna wersja zapoznawcza)

Usługa Azure Active Directory może dostarczać użytkownikom informacje o członkostwie grupy w tokenach do użytku w aplikacjach.  Obsługiwane są dwa główne wzorce:

- Grupy identyfikowane przez atrybut OID (OID) usługi Azure Active Directory (ogólnie dostępne)
- Grupy identyfikowane przez atrybuty sAMAccountName lub GroupSID dla grup i użytkowników zsynchronizowanego usługi Active Directory (AD) (publiczna wersja zapoznawcza)

> [!IMPORTANT]
> Istnieje wiele zastrzeżeń, które należy zwrócić uwagę na tę funkcję w wersji zapoznawczej:
>
>- Obsługa używania atrybutów sAMAccountName i identyfikator zabezpieczeń (SID) zsynchronizowanych z lokalnego ma na celu umożliwienie przenoszenia istniejących aplikacji z usług AD FS i innych dostawców tożsamości. Grupy zarządzane w usłudze Azure AD nie zawierają atrybutów niezbędnych do emisji tych oświadczeń.
>- W większych organizacjach liczba grup, których użytkownik jest członkiem, może przekroczyć limit dodania usługi Azure Active Directory do tokenu. 150 grup dla tokenu SAML i 200 dla JWT. Może to prowadzić do nieprzewidywalnych wyników. Jeśli użytkownicy mają dużą liczbę członkostw w grupach, zalecamy użycie opcji, aby ograniczyć grupy emitowane w roszczeniach do odpowiednich grup dla aplikacji.  
>- W przypadku tworzenia nowych aplikacji lub w przypadkach, gdy można skonfigurować aplikację dla niej i gdzie obsługa grup zagnieżdżonych nie jest wymagana, zaleca się, że autoryzacja w aplikacji opiera się na rolach aplikacji, a nie na grupach.  Ogranicza to ilość informacji, które muszą przejść do tokenu, jest bezpieczniejsze i oddziela przypisanie użytkownika od konfiguracji aplikacji.

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>Oświadczenia grupowe dla aplikacji migrujących z usług AD FS i innych dostawców tożsamości

Wiele aplikacji skonfigurowanych do uwierzytelniania przy użyciu usług AD FS polega na informacjach o członkostwie grupy w postaci atrybutów grupy usługi Windows AD.   Te atrybuty są grupy sAMAccountName, które mogą być kwalifikowane przez nazwę domeny lub identyfikator zabezpieczeń grupy systemu Windows (GroupSID).  Gdy aplikacja jest sfederowana z usługami AD FS, usługi AD FS używają funkcji TokenGroups do pobierania członkostwa w grupach dla użytkownika.

Aplikacja przeniesiona z usług AD FS wymaga oświadczeń w tym samym formacie. Oświadczenia grupy i ról mogą być emitowane z usługi Azure Active Directory zawierającej adres sAMAccountName kwalifikowany do domeny lub groupSID zsynchronizowany z usługi Active Directory, a nie z identyfikatora obiektu usługi Azure Active Directory grupy.

Obsługiwane formaty oświadczeń grupowych to:

- **Obiekt grupy usługi Azure Active Directory** (dostępny dla wszystkich grup)
- **sAMAccountName** (dostępne dla grup zsynchronizowanych z usługi Active Directory)
- **NetbiosDomain\sAMAccountName** (dostępne dla grup zsynchronizowanych z usługi Active Directory)
- **DNSDomainName\sAMAccountName** (dostępne dla grup zsynchronizowanych z usługi Active Directory)
- **Identyfikator zabezpieczeń grupy lokalnej** (dostępny dla grup zsynchronizowanych z usługi Active Directory)

> [!NOTE]
> Atrybuty sid grupy sAMAccountName i On Locals Group są dostępne tylko w obiektach grupy zsynchronizowanych z usługi Active Directory.   Nie są one dostępne w grupach utworzonych w usłudze Azure Active Directory lub usłudze Office365.   Aplikacje skonfigurowane w usłudze Azure Active Directory w celu uzyskania synchronizowanych atrybutów grup lokalnych, aby uzyskać je tylko dla grup zsynchronizowanych.

## <a name="options-for-applications-to-consume-group-information"></a>Opcje korzystania z informacji o grupach przez aplikacje

Aplikacje mogą wywoływać punkt końcowy grup MS Graph w celu uzyskania informacji o grupie dla uwierzytelnionego użytkownika. To wywołanie gwarantuje, że wszystkie grupy, których użytkownik jest członkiem są dostępne, nawet jeśli istnieje duża liczba grup zaangażowanych.  Wyliczenie grupy jest następnie niezależne od ograniczeń rozmiaru tokenu.

Jeśli jednak istniejąca aplikacja spodziewa się korzystać z informacji o grupie za pośrednictwem oświadczeń, usługa Azure Active Directory można skonfigurować za pomocą wielu różnych formatów oświadczeń.  Rozważmy następujące opcje:

- W przypadku korzystania z członkostwa w grupie do celów autoryzacji w aplikacji zaleca się użycie identyfikatora obiektu grupy. Obiekt grupy jest niezmienny i unikatowy w usłudze Azure Active Directory i jest dostępny dla wszystkich grup.
- Jeśli używasz lokalnej grupy sAMAccountName do autoryzacji, użyj nazw kwalifikowanych domen;  jest mniejsza szansa na zderzenie nazwisk. SAMAccountName może być unikatowa w domenie usługi Active Directory, ale jeśli więcej niż jedna domena usługi Active Directory jest synchronizowana z dzierżawą usługi Azure Active Directory, istnieje możliwość, że więcej niż jedna grupa ma taką samą nazwę.
- Należy rozważyć użycie [ról aplikacji,](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) aby zapewnić warstwę pośrednie między członkostwem w grupie i aplikacji.   Aplikacja następnie sprawia, że wewnętrzne decyzje dotyczące autoryzacji na podstawie klamer ról w tokenie.
- Jeśli aplikacja jest skonfigurowana do uzyskania atrybutów grupy, które są synchronizowane z usługi Active Directory i grupa nie zawiera tych atrybutów, nie zostaną uwzględnione w oświadczeniach.
- Oświadczenia grupowania w tokenach obejmują grupy zagnieżdżone, z wyjątkiem sytuacji, gdy jest ona obsługiwana przez opcję ograniczania oświadczeń grupy do grup przypisanych do aplikacji.  Jeśli użytkownik jest członkiem GrupyB, a GroupB jest członkiem GroupA, oświadczenia grupy dla użytkownika będą zawierać zarówno GroupA, jak i GroupB. Gdy użytkownicy organizacji mają dużą liczbę członkostw w grupach, liczba grup wymienionych w tokenie może zwiększyć rozmiar tokenu.  Usługa Azure Active Directory ogranicza liczbę grup, które będzie emitować w tokenie do 150 dla potwierdzeń SAML i 200 dla JWT.  Jeśli użytkownik jest członkiem większej liczby grup, grupy są pomijane i zamiast tego znajduje się łącze do punktu końcowego wykresu w celu uzyskania informacji o grupie.

## <a name="prerequisites-for-using-group-attributes-synchronized-from-active-directory"></a>Wymagania wstępne dotyczące używania atrybutów grupy zsynchronizowanych z usługi Active Directory

Oświadczenia o członkostwie grupy mogą być emitowane w tokenach dla dowolnej grupy, jeśli używasz formatu ObjectId. Aby używać oświadczeń grup w formatach innych niż objectid grupy, grupy muszą być synchronizowane z usługi Active Directory przy użyciu usługi Azure AD Connect.

Istnieją dwa kroki konfigurowania usługi Azure Active Directory do emitowania nazw grup dla grup usługi Active Directory.

1. **Synchronizowanie nazw grup z usługi Active Directory** Zanim usługa Azure Active Directory może emitować nazwy grup lub identyfikator SID grupy lokalnej w roszczeniach grup lub ról, wymagane atrybuty muszą być synchronizowane z usługi Active Directory.  Musisz być uruchomiony usługi Azure AD Connect w wersji 1.2.70 lub nowszej.   Starsze wersje usługi Azure AD Connect niż 1.2.70 zsynchronizuje obiekty grupy z usługi Active Directory, ale nie będą zawierać wymaganych atrybutów nazwy grupy.  Uaktualnienie do bieżącej wersji.

2. **Konfigurowanie rejestracji aplikacji w usłudze Azure Active Directory w celu uwzględnienia oświadczeń grup w tokenach** Oświadczenia grupy można skonfigurować w sekcji Aplikacje dla przedsiębiorstw w portalu lub przy użyciu manifestu aplikacji w sekcji Rejestracje aplikacji.  Aby skonfigurować oświadczenia grupy w manifeście aplikacji, zobacz "Konfigurowanie rejestracji aplikacji usługi Azure Active Directory dla atrybutów grupy" poniżej.

## <a name="add-group-claims-to-tokens-for-saml-applications-using-sso-configuration"></a>Dodawanie oświadczeń grupy do tokenów dla aplikacji SAML przy użyciu konfiguracji SSO

Aby skonfigurować oświadczenia grupowe dla aplikacji SAML z galerii lub spoza galerii, otwórz **aplikację Enterprise Applications**, kliknij aplikację na liście, wybierz opcję **Logowanie jednokrotne na,** a następnie wybierz pozycję **Atrybuty użytkownika & oświadczenia**.

Kliknij **dodaj roszczenie grupy**  

![roszczenia UI](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

Użyj przycisków radiowych, aby wybrać grupy, które powinny być zawarte w tokenie

![roszczenia UI](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| Wybór | Opis |
|----------|-------------|
| **Wszystkie grupy** | Emituje grupy zabezpieczeń i listy dystrybucyjne i role.  |
| **Grupy zabezpieczeń** | Emituje grupy zabezpieczeń, do które użytkownik jest członkiem oświadczenia grup |
| **Role katalogu** | Jeśli użytkownikowi przypisano role katalogu, są one emitowane jako oświadczenie "wids" (oświadczenie grup nie zostanie wyemitowane) |
| **Grupy przypisane do aplikacji** | Emituje tylko grupy, które są jawnie przypisane do aplikacji, a użytkownik jest członkiem |

Na przykład, aby emitować wszystkie grupy zabezpieczeń, do które należy użytkownik, wybierz pozycję Grupy zabezpieczeń

![roszczenia UI](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

Aby emitować grupy przy użyciu atrybutów usługi Active Directory zsynchronizowanych z usługi Active Directory zamiast identyfikatorów obiektów usługi Azure AD, wybierz wymagany format z listy rozwijanej. W oświadczeniach uwzględnione zostaną tylko grupy zsynchronizowane z usługi Active Directory.

![roszczenia UI](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

Aby emitować tylko grupy przypisane do aplikacji, wybierz **grupy przypisane do aplikacji**

![roszczenia UI](media/how-to-connect-fed-group-claims/group-claims-ui-4-1.png)

Grupy przypisane do aplikacji zostaną uwzględnione w tokenie.  Inne grupy, do które użytkownik jest członkiem, zostaną pominięte.  Z tej opcji zagnieżdżone grupy nie są uwzględniane i użytkownik musi być bezpośrednim członkiem grupy przypisanej do aplikacji.

Aby zmienić grupy przypisane do aplikacji, wybierz aplikację z listy **Aplikacje przedsiębiorstwa,** a następnie kliknij polecenie **Użytkownicy i grupy** z menu nawigacji po lewej stronie aplikacji.

Szczegółowe informacje dotyczące zarządzania przypisaniem grupy do aplikacji można znaleźć w dokumencie [Przypisywanie użytkownika lub grupy do aplikacji.](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)

### <a name="advanced-options"></a>Opcje zaawansowane

Sposób emisji oświadczeń grup może być modyfikowany przez ustawienia w obszarze Opcje zaawansowane

Dostosuj nazwę oświadczenia grupy: Jeśli ta opcja jest zaznaczona, można określić inny typ oświadczenia dla oświadczeń grupy.   Wprowadź typ oświadczenia w polu Nazwa i opcjonalny obszar nazw dla oświadczenia w polu obszaru nazw.

![roszczenia UI](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

Niektóre aplikacje wymagają, aby informacje o członkostwie w grupie były wyświetlane w żądaniu "roli". Opcjonalnie można emitować grupy użytkowników jako role, zaznaczając pole "Emituj grupy oświadczeń roli".

![roszczenia UI](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> Jeśli używana jest opcja emitowania danych grupy jako ról, w żądaniu roli pojawią się tylko grupy.  Wszystkie role aplikacji, które użytkownik jest przypisany nie pojawi się w oświadczeń roli.

### <a name="edit-the-group-claims-configuration"></a>Edytowanie konfiguracji oświadczeń grupy

Po dodaniu konfiguracji oświadczenia grupy do konfiguracji Atrybuty użytkownika & Oświadczenia opcja dodania oświadczenia grupy zostanie wyszarzona.  Aby zmienić konfigurację oświadczenia grupy, kliknij oświadczenie grupy na liście **Oświadczenia dodatkowe.**

![roszczenia UI](media/how-to-connect-fed-group-claims/group-claims-ui-7.png)

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>Konfigurowanie rejestracji aplikacji usługi Azure AD dla atrybutów grupy

Oświadczenia grupy można również skonfigurować w sekcji [Oświadczenia opcjonalne](../../active-directory/develop/active-directory-optional-claims.md) [manifestu aplikacji](../../active-directory/develop/reference-app-manifest.md).

1. W >portalu Azure Active Directory -> Application Registrations->Select Application->Manifest

2. Włączanie oświadczeń o członkostwie w grupach przez zmianę groupMembershipClaim

Prawidłowe wartości:

| Wybór | Opis |
|----------|-------------|
| **"Wszystko w porządku"** | Emituje grupy zabezpieczeń, listy dystrybucyjne i role |
| **"Grupa zabezpieczeń"** | Emituje grupy zabezpieczeń, do które użytkownik jest członkiem oświadczenia grup |
| **"DirectoryRole** | Jeśli użytkownikowi przypisano role katalogu, są one emitowane jako oświadczenie "wids" (oświadczenie grup nie zostanie wyemitowane) |
| **"ApplicationGroup (Grupa aplikacji)** | Emituje tylko grupy, które są jawnie przypisane do aplikacji, a użytkownik jest członkiem |

   Przykład:

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Domyślnie identyfikatory obiektów grupy będą emitowane w wartości oświadczenia grupy.  Aby zmodyfikować wartość oświadczenia, która ma zawierać atrybuty grupy lokalnej lub zmienić typ oświadczenia na rolę, należy użyć konfiguracji OptionalClaims w następujący sposób:

3. Ustawianie opcjonalnych oświadczeń konfiguracji nazwy grupy.

   Jeśli chcesz, aby grupy w tokenie zawierały lokalne atrybuty grupy AD, określ, do którego typu tokenu należy zastosować opcjonalne oświadczenie w sekcji oświadczeń opcjonalnych.  Można wymienić wiele typów tokenów:

   - idToken dla tokenu identyfikatora OIDC
   - accessToken dla tokenu dostępu OAuth/OIDC
   - Saml2Token dla tokenów SAML.

   > [!NOTE]
   > Typ Saml2Token ma zastosowanie zarówno do tokenów w formacie SAML1.1, jak i SAML2.0

   Dla każdego odpowiedniego typu tokenu zmodyfikuj oświadczenia grup, aby użyć sekcji OptionalClaims w manifeście. Schemat OptionalClaims jest następujący:

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | Schemat oświadczeń opcjonalnych | Wartość |
   |----------|-------------|
   | **Nazwa:** | Musi być "grupy" |
   | **Źródła:** | Nie używany. Pomiń lub określ wartość null |
   | **Niezbędne:** | Nie używany. Pomiń lub określ fałsz |
   | **additionalProperties:** | Lista dodatkowych właściwości.  Prawidłowe opcje to "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   W additionalProperties wymagany jest tylko jeden z "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name".  Jeśli więcej niż jeden jest obecny, pierwszy jest używany, a inne ignorowane.

   Niektóre aplikacje wymagają informacji o grupie o użytkowniku w żądaniu roli.  Aby zmienić typ oświadczenia z oświadczenia grupy na oświadczenie roli, dodaj "emit_as_roles" do dodatkowych właściwości.  Wartości grupy zostaną wyemitowane w żądaniu roli.

   > [!NOTE]
   > Jeśli "emit_as_roles" jest używany wszystkie role aplikacji skonfigurowane, że użytkownik jest przypisany nie pojawi się w oświadczeń roli

### <a name="examples"></a>Przykłady

Emitowanie grup jako nazw grup w tokenach dostępu OAuth w formacie dnsDomainName\SAMAccountName

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Aby emitować nazwy grup, które mają być zwracane w formacie netbiosDomain\samAccountName jako oświadczenie ról w tokenach IDENTYFIKATORÓW SAML i OIDC:

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

[Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)

[Konfigurowanie oświadczeń roli](../../active-directory/develop/active-directory-enterprise-app-role-management.md)
