---
title: Uprawnienia w usłudze Azure AD | Microsoft Docs
description: Dowiedz się więcej na temat zakresów i uprawnień w usłudze Azure Active Directory i sposobu ich użycia
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 6c0dc122-2cd8-4d70-be5a-3943459d308e
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 04/20/2017
ms.author: celested
ms.reviewer: justhu
ms.custom: aaddev
ms.openlocfilehash: 749253d6a082bcdc2b80c5984f20c4b8c4039ad0
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="permissions-in-azure-ad"></a>Uprawnienia w usłudze Azure AD
Usługa Azure Active Directory (Azure AD) szeroko wykorzystuje użycie uprawnień dla przepływów OAuth i OIDC (OpenID Connect). Po odebraniu przez aplikację tokenu dostępu od usługi Azure AD aplikacja uwzględnia oświadczenia z opisami uprawnień, które aplikacja ma w odniesieniu do określonego zasobu (są one znane także jako zakresy). Ułatwia to autoryzację zasobu, ponieważ wystarczy sprawdzić, czy token zawiera odpowiednie uprawnienie, niezależnie od wywoływanego interfejsu API. 

## <a name="types-of-permissions"></a>Typy uprawnień
W usłudze Azure AD zdefiniowano dwa rodzaje uprawnień: 
* **Uprawnienia delegowane** — są używane przez aplikacje, w których użytkownik jest obecnie zalogowany. W przypadku takich aplikacji zgodę na uprawnienia żądane przez aplikację może wyrazić użytkownik lub administrator. Podczas wywołań interfejsu API do aplikacji są delegowane uprawnienia do działania w roli zalogowanego użytkownika. W zależności od interfejsu API użytkownik może nie mieć możliwości wyrażenia bezpośrednio zgody w interfejsie i zamiast tego może [poprosić administratora o udzielenie „zgody administratora”.](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent)
* **Uprawnienia aplikacji** — są używane przez aplikacje i nie wymagają zalogowanego użytkownika. Są to np. aplikacje działające jako usługi w tle lub demony. Uprawnienia aplikacji mogą być [nadane wyłącznie przez administratora](/azure/active-directory/develop/active-directory-v2-scopes#requesting-consent-for-an-entire-tenant), ponieważ stanowią potężne narzędzia i umożliwiają dostęp do danych przekraczający granice użytkownika lub dotyczą danych, dostęp do których byłby w przeciwnym razie ograniczony do administratorów. 

Czynne uprawnienia to uprawnienia, które aplikacja będzie posiadać podczas wysyłania żądań do interfejsu API. 

* W przypadku uprawnień delegowanych czynne uprawnienia aplikacji będą najmniejszą wspólną częścią delegowanych uprawnień przyznanych aplikacji (poprzez wyrażenie zgody) oraz uprawnień aktualnie zalogowanego użytkownika. Aplikacja nigdy nie może mieć większych uprawnień niż zalogowany użytkownik. Uprawnienia zalogowanego użytkownika mogą być określone w organizacji na podstawie zasad lub członkostwa w co najmniej jednej roli administratora. Aby uzyskać więcej informacji o rolach administratorów, zobacz [Przypisywanie ról administratorów w usłudze Azure AD](/azure/active-directory/active-directory-assign-admin-roles-azure-portal.md).
    Załóżmy na przykład, że aplikacja ma przyznane uprawnienie delegowane `User.ReadWrite.All` w programie Microsoft Graph. Uprawnienie to przyznaje aplikacji nominalne uprawnienia do odczytu i aktualizowania profilu każdego użytkownika w organizacji. Jeśli zalogowany użytkownik jest administratorem globalnym, aplikacja będzie mogła zaktualizować profil każdego użytkownika w organizacji. Jednak jeśli zalogowany użytkownik nie ma roli administratora, aplikacja będzie mogła aktualizować tylko profil zalogowanego użytkownika. Użytkownik nie będzie mógł zaktualizować profili innych użytkowników w organizacji, ponieważ użytkownik mający uprawnienia do działania w imieniu innych osób nie ma odpowiednich uprawnień.
* W przypadku uprawnień aplikacji efektywne uprawnienia aplikacji są na poziomie przywilejów wynikających z uprawnień. Na przykład aplikacja, która ma uprawnienie aplikacji `User.ReadWrite.All`, może aktualizować profile każdego użytkownika w organizacji. 

## <a name="permission-attributes"></a>Atrybuty uprawnień
Uprawnienia w usłudze Azure AD mają wiele właściwości, które pomagają użytkownikom, administratorom i deweloperom aplikacji w podejmowaniu świadomych decyzji dotyczących przydzielania uprawnień. 

> [!NOTE]
> W witrynie Azure Portal i programie PowerShell można wyświetlić uprawnienia udostępniane przez aplikację usługi Azure AD lub Service Principal. Uruchom ten skrypt, aby wyświetlić uprawnienia udostępniane przez program Microsoft Graph.
> ```powershell
> Connect-AzureAD
> 
> # Get OAuth2 Permissions/delegated permissions
> (Get-AzureADServicePrincipal -filter "DisplayName eq 'Microsoft Graph'").OAuth2Permissions
> 
> # Get App roles/application permissions
> (Get-AzureADServicePrincipal -filter "DisplayName eq 'Microsoft Graph'").AppRoles
> ```

| Nazwa właściwości | Opis | Przykład | 
| --- | --- | --- |
| ID | Jest to wartość identyfikatora GUID, która unikatowo identyfikuje to uprawnienie. | 570282fd-fa5c-430d-a7fd-fc8dc98a9dca | 
| IsEnabled | Wskazuje, czy ten zakres jest dostępny do użycia. | true | 
| Typ | Wskazuje, czy to uprawnienie wymaga zgody użytkownika, czy też zgody administratora. | Użytkownik | 
| AdminConsentDescription | Jest to opis widziany przez administratorów podczas procesu wyrażania przez nich zgody. | Zezwala aplikacji na odczytywanie wiadomości e-mail w skrzynkach pocztowych użytkowników. | 
| AdminConsentDisplayName | Jest to przyjazna nazwa widziana przez administratorów podczas procesu wyrażania przez nich zgody. | Odczytuj pocztę użytkownika | 
| UserConsentDescription | Jest to opis widziany przez użytkowników podczas procesu wyrażania przez nich zgody. |  Zezwala aplikacji na odczytywanie wiadomości e-mail w skrzynce pocztowej użytkownika. | 
| UserConsentDisplayName | Jest to przyjazna nazwa widziana przez użytkowników podczas procesu wyrażania przez nich zgody. | Odczytywanie wiadomości | 
| Wartość | Jest to ciąg, który służy do identyfikowania uprawnienia podczas przepływów autoryzacji OAuth 2.0. Można go również być połączyć z ciągiem identyfikatora URI aplikacji w celu utworzenia nazwy FQDN uprawnień. | `Mail.Read` | 

## <a name="types-of-consent"></a>Typy zgody
Przy uzyskiwaniu dostępu do niezbędnych zasobów lub interfejsów API aplikacje w usłudze Azure AD muszą uzyskać zgodę. Istnieje wiele rodzajów zgody, które powinny być znane aplikacji, aby jej praca zakończyła się pomyślnie. Podczas definiowania uprawnień należy także znać sposoby używane przez użytkowników podczas uzyskiwania dostępu do Twojej aplikacji lub interfejsu API.

* **Statyczna zgoda użytkownika** — zachodzi automatycznie podczas [przepływu autoryzacji OAuth 2.0](/azure/active-directory/develop/active-directory-protocols-oauth-code.md#request-an-authorization-code) po podaniu zasobu, z którym chce współdziałać Twoja aplikacja. W scenariuszu statycznej zgody użytkownika aplikacja musi już znać wszystkie uprawnienia, które są niezbędne do skonfigurowania aplikacji w witrynie Azure Portal. Jeśli użytkownik (lub administrator, zależnie od potrzeb) nie udzielił zgody dla tej aplikacji, wtedy usługa Azure AD będzie monitować użytkownika o zgodę. 

    Dowiedz się więcej o rejestrowaniu aplikacji usługi Azure AD, która żąda dostępu do statycznego zestawu interfejsów API.
* **Dynamiczna zgoda użytkownika** — jest to funkcja dostępna w modelu aplikacji usługi Azure AD w wersji 2. W tym scenariuszu aplikacja żąda zbioru zakresów, których potrzebuje w [przepływie autoryzacji OAuth 2.0 dla aplikacji v2](/azure/active-directory/develop/active-directory-v2-scopes#requesting-individual-user-consent). Jeśli użytkownik nie wyraził jeszcze zgody, w tym momencie zostanie wyświetlony monit o jej wyrażenie. [Dowiedz się więcej na temat dynamicznej zgody](/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent).

    > [!NOTE]
    > Dynamiczne udzielanie zgody może być wygodne, ale stanowi duże wyzwanie w przypadku uprawnień wymagających zgody administratora, ponieważ w momencie wyrażania zgody administrator nie dysponuje informacjami dotyczącymi uprawnień. Jeśli zachodzi konieczność posiadania uprawnień administratora, używana aplikacja musi zarejestrować je w witrynie Azure Portal.
  
* **Zgoda administratora** — jest wymagana, gdy aplikacja potrzebuje dostępu do niektórych uprawnień wysokiego poziomu. Dzięki temu administratorzy mają dodatkową kontrolę nad autoryzowaniem dostępu aplikacji lub użytkowników do danych organizacji wymagających szczególnych uprawnień. [Dowiedz się więcej na temat sposobu udzielania zgody administratora](/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

## <a name="best-practices"></a>Najlepsze praktyki

### <a name="resource-best-practices"></a>Najlepsze praktyki dotyczące zasobów
Zasoby, które udostępniają interfejsy API, powinny bardzo szczegółowo definiować uprawnienia dla chronionych przez nie danych lub akcji. Dzięki temu klienci nie otrzymują uprawnień do niepotrzebnych danych oraz są poinformowani, do jakich danych mają dostęp.

Zasoby powinny w sposób jawny osobno definiować uprawnienia `Read` i `ReadWrite`. 

Zasoby powinny oznaczyć wszystkie uprawnienia umożliwiające dostęp do danych przekraczający granice użytkownika jako uprawnienia `Admin`. 

Zasoby powinny stosować następujący wzorzec nazewnictwa `Subject.Permission[.Modifier]`, gdzie `Subject` odpowiada typowi dostępnych danych, `Permission` odpowiada działaniu, które użytkownik może wykonać na tych danych, i `Modifier` służy opcjonalnie do opisu specjalizacji innego uprawnienia. Na przykład: 
* Mail.Read — umożliwia użytkownikom odczytywanie wiadomości e-mail. 
* Mail.ReadWrite — umożliwia użytkownikom odczytywanie i pisanie wiadomości e-mail.
* Mail.ReadWrite.All — umożliwia administratorowi lub użytkownikom dostęp do wszystkich wiadomości w organizacji.

### <a name="client-best-practices"></a>Najlepsze rozwiązania klientów
Żądaj wyłącznie uprawnień dla zakresów wymaganych przez Twoją aplikację. Aplikacje ze zbyt dużą liczbą uprawnień niosą ze sobą ryzyko ujawnienia danych użytkowników w przypadku naruszenia bezpieczeństwa aplikacji.

Klienci nie powinni jednocześnie wymagać uprawnień dla aplikacji i delegować ich z tej samej aplikacji. Może to spowodować podniesienie poziomu uprawnień i zezwolić użytkownikowi na uzyskanie dostępu do danych, do których nie posiada on własnych uprawnień. 




