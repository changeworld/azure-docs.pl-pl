---
title: Integrowanie aplikacji z usługą Azure Active Directory
description: Jak dodać, zaktualizować lub usunąć aplikacji w usłudze Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: PatAltimore
manager: mtillman
editor: mbaldwin
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/04/2017
ms.author: bryanla
ms.custom: aaddev
ms.reviewer: luleon
ms.openlocfilehash: 472a1746a338857d457a7b8d5e7fec3ddbf65895
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="integrating-applications-with-azure-active-directory"></a>Integrowanie aplikacji z usługą Azure Active Directory
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Deweloperów w przedsiębiorstwach i dostawców oprogramowania — jako — usługa (SaaS) mogą powstać komercyjnych usług chmurowych lub aplikacji — biznesowych, które można zintegrować z usługą Azure Active Directory (Azure AD) w celu zapewnienia bezpiecznego logowania i autoryzacji ich usługi. Integracja aplikacji lub usługi z usługą Azure AD, deweloper musi najpierw zarejestrować aplikacji z usługą Azure AD.

W tym artykule przedstawiono sposób dodawania, zaktualizować lub usunąć rejestracji aplikacji w usłudze Azure AD. Dowiedz się więcej o różnych typów aplikacji, które można zintegrować z usługą Azure AD, jak skonfigurować dostęp do innych zasobów, takich jak interfejsów API sieci web i inne aplikacje.

Aby dowiedzieć się więcej na temat dwa obiekty usługi Azure AD, które reprezentują zarejestrowaną aplikację i relacji między nimi, zobacz [obiekty aplikacji i nazwy głównej usługi](active-directory-application-objects.md); Aby dowiedzieć się więcej o znakowaniu wytyczne, wykonaj następujące czynności Podczas tworzenia aplikacji w usłudze Azure Active Directory, zobacz [znakowanie wytyczne dotyczące zintegrowanych aplikacji](active-directory-branding-guidelines.md).

## <a name="adding-an-application"></a>Dodawanie aplikacji
Każda aplikacja, która chce korzystać z funkcji usługi Azure AD musi najpierw zostać zarejestrowana w dzierżawie usługi Azure AD. Ten proces rejestracji polega na zapewnieniu usługi Azure AD szczegóły aplikacji, na przykład adres URL go ma lokalizację, adres URL do wysyłania odpowiedzi po uwierzytelnieniu użytkownika identyfikator URI, który identyfikuje aplikację i tak dalej.

### <a name="to-register-a-new-application-using-the-azure-portal"></a>Aby zarejestrować nową aplikację przy użyciu portalu Azure
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Jeśli Twoje zapewnia konta dostępu do więcej niż jeden, kliknij konto w prawym górnym rogu i ustaw sesji portalu do żądanej usługi Azure AD dzierżawy.
3. W okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** usługi, kliknij przycisk **rejestracji aplikacji**i kliknij przycisk **nowej rejestracji aplikacji**.

   ![Zarejestrować nową aplikację](./media/active-directory-integrating-applications/add-app-registration.png)

4. Gdy **Utwórz** zostanie wyświetlona strona, wprowadź informacje o rejestracji aplikacji: 

  - **Nazwa:** wprowadź nazwę opisową aplikacji
  - **Typ aplikacji:** 
    - Wybierz "Native" dla [aplikacje klienckie](active-directory-dev-glossary.md#client-application) są instalowane lokalnie na urządzeniu. To ustawienie jest stosowane do publicznego OAuth [klientach natywnych](active-directory-dev-glossary.md#native-client).
    - Wybierz "aplikacji sieci Web / interfejs API" dla [aplikacje klienckie](active-directory-dev-glossary.md#client-application) i [aplikacji zasobów/API](active-directory-dev-glossary.md#resource-server) są instalowane na serwerze bezpieczne. To ustawienie jest stosowane do OAuth poufne [sieci web klientów](active-directory-dev-glossary.md#web-client) i publiczne [klientów z systemem agenta użytkownika](active-directory-dev-glossary.md#user-agent-based-client). Ta sama aplikacja również mogą uwidaczniać zarówno klient, jak i zasobów/interfejsu API.
  - **Adres URL logowania:** dla "aplikacja sieci Web / interfejs API" aplikacji, podaj podstawowy adres URL aplikacji. Na przykład `http://localhost:31544` może być adresem URL dla aplikacji sieci web uruchomiony na komputerze lokalnym. Użytkownicy użyje tego adresu URL, aby zalogować się do aplikacji klienta sieci web. 
  - **Identyfikator URI przekierowania:** "Native" aplikacji, podaj identyfikator URI używany przez usługę Azure AD do zwracania odpowiedzi tokenu. Wprowadź wartości określonych aplikacji, na przykład `http://MyFirstAADApp`

   ![Zarejestrować nową aplikację — tworzenie](./media/active-directory-integrating-applications/add-app-registration-create.png)

   Jeśli chcesz szczegółowe przykłady dla aplikacji sieci web lub natywnych aplikacji, zapoznaj się naszego [quickstarts](active-directory-developers-guide.md#get-started).

5. Gdy skończysz, kliknij przycisk **Utwórz**. Usługi Azure AD przypisuje unikatowy identyfikator aplikacji do aplikacji i jest przekierowanie do strony głównej rejestracji aplikacji. W zależności od tego, czy aplikacja jest sieci web lub aplikacji natywnej Aby dodać dodatkowe funkcje do aplikacji znajdują się różne opcje. W następnej sekcji Omówienie zgody i szczegółowe informacje na temat włączania funkcji dodatkowa konfiguracja w rejestracji aplikacji (poświadczeń, uprawnień, Włącz logowanie użytkowników od pozostałych dzierżawców.)

  > [!NOTE]
  > Domyślnie nowo zarejestrowanych aplikacji jest skonfigurowane i umożliwiają **tylko** użytkowników z tej samej dzierżawy logować się do aplikacji.
  > 
  > 

## <a name="updating-an-application"></a>Aktualizowanie aplikacji
Po zarejestrowaniu aplikacji z usługą Azure AD może być konieczne zostać zaktualizowany w celu zapewnienia dostępu do interfejsów API sieci web, dostępne w innych organizacji i inne. W tej sekcji opisano różne sposoby, w których można skonfigurować więcej aplikacji. Najpierw Możemy zaczynać Omówienie struktury zgody, która wziąć pod uwagę podczas tworzenia aplikacji, które będzie używane przez innych użytkowników lub aplikacji.

### <a name="overview-of-the-consent-framework"></a>Omówienie struktury zgody

Framework zgody usługi Azure AD można łatwo rozwijać wielodostępnej sieci web i aplikacji natywnej klienta, w tym aplikacje wielowarstwowe. Te aplikacje zezwalanie logowania według konta użytkowników z dzierżawy usługi Azure AD, innego niż ten, na którym aplikacja jest zarejestrowany. Może muszą uzyskać dostępu do sieci web API innych usług firmy Microsoft, oprócz własnego interfejsów API sieci web i interfejsów API, np. interfejsu API programu Microsoft Graph (Aby uzyskać dostęp do usługi Azure Active Directory, Intune i usług w usłudze Office 365). Platformę opiera się na użytkownika lub administratora wyrażenia zgody do aplikacji, która żąda rejestrowana w ich katalogu, które mogą dotyczyć uzyskiwanie dostępu do danych katalogu.

Na przykład jeśli wymaga aplikacji klienta sieci web odczytać kalendarza informacji o użytkowniku z usługi Office 365, ten użytkownik będzie musiał wyrazić zgodę na kliencie. Po zgody, aplikacja kliencka będzie można wywołać interfejsu API programu Microsoft Graph w imieniu użytkownika i użyć tych informacji kalendarza, zgodnie z potrzebami. [Interfejsu API programu Microsoft Graph](https://graph.microsoft.io) zapewnia dostęp do danych w usłudze Office 365 (na przykład kalendarzy i komunikaty z programu Exchange, witryn i list programu SharePoint, dokumentów z usługi OneDrive, komputery przenośne z programu OneNote, zadania planowania, skoroszyty programu Excel, itp.), a także użytkowników i grup z usługi Azure AD i innych obiektów danych z więcej usług chmurowych firmy Microsoft. 

Framework zgody jest oparty na OAuth 2.0 i jego różnych przepływów, takie jak udzielić kod autoryzacji poświadczenia grant i klienta, za pomocą publicznego lub poufnych klientów. Za pomocą protokołu OAuth 2.0, usługi Azure AD umożliwia tworzenie wielu różnych typów aplikacji klienckich, takich jak na telefon, tablecie, serwera lub aplikacji sieci web i uzyskanie dostępu do wymaganych zasobów.

Aby uzyskać więcej informacji o użyciu framework zgody OAuth2.0 przyznaje autoryzacji, zobacz [autoryzowania dostępu do aplikacji sieci web przy użyciu protokołu OAuth 2.0 i usługi Azure AD](active-directory-protocols-oauth-code.md) i[scenariusze uwierzytelniania dla usługi Azure AD](active-directory-authentication-scenarios.md). Aby dowiedzieć się, jak autoryzowany dostęp do usługi Office 365 za pomocą programu Microsoft Graph, zobacz [uwierzytelniania aplikacji za pomocą programu Microsoft Graph](https://graph.microsoft.io/docs/authorization/auth_overview).

#### <a name="example-of-the-consent-experience"></a>Przykład środowisko zgody

W poniższej procedurze pokazano, jak zgody wystąpić działa dla deweloperów aplikacji i użytkownika.

1. Załóżmy, że masz aplikacji klienta sieci web, która wymaga określonych uprawnień dostępu do zasobów/interfejs API do żądania. Dowiesz się, jak to zrobić w tej konfiguracji w następnej sekcji, ale zasadniczo portalu Azure służy do deklarowania żądania uprawnień w czasie konfigurowania. Podobnie jak inne ustawienia konfiguracji staną się częścią aplikacji usługi Azure AD rejestracji:
   
  ![Uprawnienia do innych aplikacji](./media/active-directory-integrating-applications/requiredpermissions.png)
    
2. Należy wziąć pod uwagę uprawnienia aplikacji zostały zaktualizowane, aplikacja jest uruchomiona, czy użytkownik ma być używany po raz pierwszy. Najpierw trzeba uzyskać kod autoryzacji z usługi Azure AD aplikacji `/authorize` punktu końcowego. Aby uzyskać nowe dostępu i token odświeżania można następnie kod autoryzacji.

3. Jeśli użytkownik nie jest już uwierzytelniony, Azure AD `/authorize` punktu końcowego wyświetla monit o logowaniu.
   
  ![Użytkownik lub administrator logowania do usługi Azure AD](./media/active-directory-integrating-applications/usersignin.png)

4. Po zalogowaniu się użytkownika, usługi Azure AD określi, jeśli użytkownik chce można wyświetlić strony zgody. To zależy od tego, czy użytkownik (lub jego organizację administratora) już udzielił zgody aplikacji. Jeśli zgody nie ma już udzielone, usługi Azure AD monituje użytkownika o zgodę i wyświetla wymaganych uprawnień wymaganych do działania. Zestaw uprawnień, które są wyświetlane w oknie dialogowym zgody odpowiada wybranych w delegowane uprawnienia w portalu Azure.
   
  ![Środowisko zgody użytkownika](./media/active-directory-integrating-applications/consent.png)

5. Po użytkownika przyznaje zgody, Kod autoryzacji jest zwracana do aplikacji, co jest zrealizowane uzyskać token dostępu i token odświeżania. Aby uzyskać więcej informacji na temat tego przepływu, zobacz [sieci web aplikacji sieci web interfejsu API części scenariusze uwierzytelniania dla usługi Azure AD](active-directory-authentication-scenarios.md#web-application-to-web-api).

6. Administrator może również wyrażenia zgody na aplikacji uprawnień delegowanych w imieniu wszystkich użytkowników w dzierżawie. Administracyjne zgody uniemożliwia okna dialogowego zgody pojawiające się dla wszystkich użytkowników w dzierżawie i odbywa się do aplikacji w obszarze [portalu Azure](https://portal.azure.com). Z **ustawienia** aplikacji kliknij pozycję **wymagane uprawnienia** i wybierz polecenie **udzielanie uprawnień** przycisku. 

  ![Udzielanie uprawnień o wyrażenie zgody jawnej administratora](./media/active-directory-integrating-applications/grantpermissions.png)
    
  > [!NOTE]
  > Udzielanie jawne zgodę za pomocą **udzielanie uprawnień** przycisk jest obecnie wymagane dla aplikacji jednej strony (SPA), które używają ADAL.js. W przeciwnym razie aplikacja kończy się niepowodzeniem podczas żądania tokenu dostępu.   

### <a name="configure-a-client-application-to-access-web-apis"></a>Skonfigurować aplikację klienta do dostępu do interfejsów API sieci web
Aby sieci web/poufnych aplikacji klienckiej można było uczestniczyć w przepływie grant autoryzacji, który wymaga uwierzytelniania (i Uzyskaj token dostępu) jego ustanawiania bezpiecznych poświadczeń. Domyślną metodą uwierzytelniania obsługiwanych przez Azure portal jest identyfikator klienta i klucz tajny. W tej sekcji opisano kroki konfiguracji, wymagane jest podanie klucza tajnego przy użyciu poświadczeń klienta.

Ponadto, zanim klient może uzyskać dostęp do składnika web API udostępnianych przez aplikację zasobów (takich jak Microsoft Graph API), framework zgody zapewnia klient uzyskuje Udziel uprawnienia wymagane, na podstawie uprawnień żądanie. Domyślnie wszystkie aplikacje dostępne uprawnienia "Systemu Windows Azure Active Directory" (interfejs API programu Graph) i "Systemu Windows Azure interfejs API zarządzania usługami". [Uprawnienie "Logowanie i profilu użytkownika odczytu" interfejsu API programu Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes#PermissionScopeDetails) również jest domyślnie zaznaczona. Jeśli klient jest rejestrowana w dzierżawie z konta subskrypcji usługi Office 365, interfejsów API sieci Web i uprawnienia do usługi Exchange Online i SharePoint są dostępne do wyboru. Możesz wybrać z [dwa typy uprawnień](active-directory-dev-glossary.md#permissions) dla każdego żądanego interfejsu API sieci web:

- Uprawnienia aplikacji: Aplikacja kliencka musi uzyskać dostępu do interfejsu API sieci web bezpośrednio jako samego (bez kontekstu użytkownika). Ten typ uprawnień wymaga zgody administratora i nie jest również dostępna dla aplikacji klienckich natywnego.

- Delegowane uprawnienia: Aplikacja kliencka musi uzyskać dostępu do interfejsu API sieci web jako zalogowany użytkownik, ale dostęp ograniczony przez wybrane ustawienia. Ten typ uprawnień może zostać przydzielony przez użytkownika, chyba że uprawnienie wymaga zgody administratora. 

  > [!NOTE]
  > Dodawanie delegowane uprawnienia do aplikacji nie udziela automatycznie zgody dla użytkowników w dzierżawie. Użytkownicy muszą nadal ręcznie zgoda na dodano uprawnień delegowanych w czasie wykonywania, chyba że kliknięciu przez administratora **udzielanie uprawnień** przycisk z **wymagane uprawnienia** sekcji Strona aplikacji w portalu Azure. 

#### <a name="to-add-application-credentials-or-permissions-to-access-web-apis"></a>Aby dodać poświadczeń aplikacji lub uprawnienia dostępu do interfejsów API sieci web
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Jeśli Twoje zapewnia konta dostępu do więcej niż jeden, kliknij konto w prawym górnym rogu i ustaw sesji portalu do żądanej usługi Azure AD dzierżawy.
3. W okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** usługi, kliknij przycisk **rejestracji aplikacji**, następnie Znajdź/kliknij aplikacji, którą chcesz skonfigurować.

   ![Zaktualizuj rejestrację aplikacji](./media/active-directory-integrating-applications/update-app-registration.png)

4. Zostają przeniesieni do strony głównej rejestracji aplikacji, która otwiera **ustawienia** strony dla aplikacji. Aby dodać klucz tajny dla aplikacji sieci web poświadczeń:
  - Kliknij przycisk **klucze** sekcji na **ustawienia** strony.  
  - Dodaj opis klucza.
  - Wybierz jednego lub dwóch lat czasu trwania.
  - Kliknij pozycję **Zapisz**. Kolumna prawej będzie zawierać wartości klucza, po zapisaniu zmian konfiguracji. **Pamiętaj skopiować klucz** do użycia w kodzie aplikacji klienta, ponieważ nie jest dostępny jeden raz opuścisz tę stronę.

  ![Zaktualizuj rejestrację aplikacji — klawisze](./media/active-directory-integrating-applications/update-app-registration-settings-keys.png)

5. Aby dodać uprawnienia dostępu do zasobu interfejsy API z klienta
  - Kliknij przycisk **wymagane uprawnienia** sekcji na **ustawienia** strony. 
  - Kliknij przycisk **Dodaj**.
  - Kliknij przycisk **wybierz interfejs API** wybierz typ zasobów, które mają zostać pobrane z.
  - Przejrzyj listę dostępnych interfejsach API lub użyj pola wyszukiwania można wybierać z poziomu aplikacji dostępnych zasobów w katalogu, które udostępniają interfejs API sieci web. Kliknij zasób interesuje, następnie kliknij przycisk **wybierz**.
  - W przypadku podjąć w celu **Włącz dostęp** strony. Wybierz uprawnienia dostępu do aplikacji i/lub delegowane uprawnienia aplikacji musi podczas uzyskiwania dostępu do interfejsu API.
   
  ![Aktualizacja rejestracji aplikacji - uprawnień interfejsu api](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-api.png)

  ![Zaktualizuj rejestrację aplikacji - perms uprawnień](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-perms.png)

6. Po zakończeniu kliknij przycisk **wybierz** znajdującego się na **Włącz dostęp** strony, a następnie **gotowe** znajdującego się na **dostępu Dodaj interfejsu API** strony. Nastąpi powrót do **wymagane uprawnienia** strony, w którym nowy zasób został dodany do listy interfejsów API.

  > [!NOTE]
  > Kliknięcie przycisku **gotowe** przycisk automatycznie ustawia uprawnienia dla aplikacji w katalogu na podstawie uprawnień do innych aplikacji, które można skonfigurować.  Te uprawnienia aplikacji można wyświetlić, sprawdzając aplikacji **ustawienia** strony.
  > 
  > 

### <a name="configuring-a-resource-application-to-expose-web-apis"></a>Konfigurowanie aplikacji zasobów, aby ujawnić interfejsów API sieci web

Mogą tworzyć interfejs API sieci web i udostępnić go dla aplikacji klienckich w przypadku wystawianego dostępu [zakresy](active-directory-dev-glossary.md#scopes) i [ról](active-directory-dev-glossary.md#roles). Podobnie jak inne Microsoft interfejsów API sieci web, łącznie z interfejsu API programu Graph i interfejsami API usługi Office 365 jest udostępniana przez interfejs API sieci web poprawnie skonfigurowana. Zakresy dostępu i ról są dostępne za pośrednictwem sieci [manifest aplikacji](active-directory-dev-glossary.md#application-manifest), czyli pliku JSON, który reprezentuje konfigurację tożsamości aplikacji. 

Poniższej sekcji przedstawiono sposób ujawniać zakresy dostępu, modyfikując manifest aplikacji zasobów.

#### <a name="adding-access-scopes-to-your-resource-application"></a>Dodawanie zakresów dostępu do zasobów aplikacji

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Jeśli Twoje zapewnia konta dostępu do więcej niż jeden, kliknij konto w prawym górnym rogu i ustaw sesji portalu do żądanej usługi Azure AD dzierżawy.

3. W okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** usługi, kliknij przycisk **rejestracji aplikacji**, następnie Znajdź/kliknij aplikacji, którą chcesz skonfigurować.

   ![Zaktualizuj rejestrację aplikacji](./media/active-directory-integrating-applications/update-app-registration.png)

4. Zostają przeniesieni do strony głównej rejestracji aplikacji, która otwiera **ustawienia** strony dla aplikacji. Przełącz się do **Edytuj manifest** strony, klikając **manifestu** ze strony rejestracji aplikacji. Zostanie otwarty Edytor manifestu z opartych na sieci web, co umożliwia **Edytuj** manifest w portalu. Opcjonalnie możesz kliknąć **Pobierz** i edytować lokalnie, a następnie użyj **przekazać** Aby ponownie zastosować go do aplikacji.

5. W tym przykładzie mamy powoduje to udostępnienie nowego zakresu o nazwie `Employees.Read.All` naszych zasobów/interfejs API, dodając następujący element JSON do `oauth2Permissions` kolekcji. Istniejące `user_impersonation` zakresu podano domyślnie podczas rejestracji. `user_impersonation` umożliwia aplikacji klienckiej na żądanie zezwolenia na dostęp do zasobu z tożsamością zalogowanego użytkownika. Pamiętaj dodać przecinek po istniejącej `user_impersonation` zakres elementu i zmiany wartości właściwości do potrzeb użytkownika zasobu. 

  ```json
  {
    "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
    "adminConsentDisplayName": "Read-only access to Employee records",
    "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
    "isEnabled": true,
    "type": "User",
    "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
    "userConsentDisplayName": "Read-only access to your Employee records",
    "value": "Employees.Read.All"
  }
  ```
  > [!NOTE]
  > Wartość "id" musi zostać wygenerowany przy użyciu narzędzia do generowania identyfikatora GUID, takich jak [guidgen](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx) lub programowo. Reprezentuje unikatowy identyfikator dla zakresu jak udostępniany przez interfejs API sieci web. Gdy klient jest skonfigurowany prawidłowo z uprawnieniami dostępu do interfejsu API sieci web, wystawieniu tokenu dostępu OAuth2.0 przez usługę Azure AD. Podczas wywołania klienta interfejsu API sieci web stanowi tokenu dostępu, który ma zakres (scp) oświadczenia ustawione uprawnienia wymagane w jego rejestracji aplikacji.
  >
  > Pozwala udostępnić dodatkowe zakresy, później niezbędne. Należy wziąć pod uwagę, że interfejs API sieci web może udostępniać wielu zakresów skojarzone z wieloma różnymi funkcjami. Zasób kontrolować dostęp do interfejsu API sieci web w czasie wykonywania, oceniając zakresu (`scp`) co najmniej jedno oświadczenie odebranego tokenu dostępu protokołu OAuth 2.0.
  > 

6. Gdy skończysz, kliknij przycisk **zapisać**. Teraz Twój interfejs API sieci web jest skonfigurowana do użycia przez inne aplikacje w katalogu.  

  ![Zaktualizuj rejestrację aplikacji](./media/active-directory-integrating-applications/update-app-registration-manifest.png)

#### <a name="verify-the-web-api-is-exposed-to-other-applications-in-your-tenant"></a>Sprawdź interfejsu API jest widoczne dla innych aplikacji w dzierżawie w sieci web
1. Przejdź z powrotem do usługi Azure AD dzierżawy, kliknij przycisk **rejestracji aplikacji** ponownie, następnie Znajdź/kliknij aplikacji klienta, którą chcesz skonfigurować.

   ![Zaktualizuj rejestrację aplikacji](./media/active-directory-integrating-applications/update-app-registration.png)

2. Powtórz krok 5, tak jak w [skonfigurować aplikację klienta do dostępu do interfejsów API sieci web](#configure-a-client-application-to-access-web-apis). Po przejściu do **wybierz interfejs API** krok, wyszukaj zasobu, wprowadzając jej nazwę aplikacji w polu wyszukiwania, a następnie kliknij przycisk **wybierz**. 

3. Na **Włącz dostęp** strony powinien zostać wyświetlony nowy zakres dostępny dla żądanych uprawnień klienta.

  ![Nowe uprawnienia są wyświetlane.](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-perms-newscopes.png)

#### <a name="more-on-the-application-manifest"></a>Więcej informacji na temat manifest aplikacji

Manifest aplikacji faktycznie służy jako mechanizm aktualizowanie jednostek aplikacji, który definiuje wszystkie atrybuty konfiguracji tożsamość aplikacji usługi Azure AD, w tym zakresy dostępu interfejsu API, które rozmawialiśmy. Aby uzyskać więcej informacji na obiekt aplikacji i jej schematu, zobacz [dokumentacji jednostek aplikacji interfejsu API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity). Ten artykuł zawiera informacje pełną dokumentację dla elementów członkowskich jednostki aplikacji, można określić uprawnienia do interfejsu API, w tym:  

- AppRoles elementu członkowskiego, który jest kolekcją z [roli aplikacji](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type) jednostek, używany do definiowania [uprawnienia aplikacji](active-directory-dev-glossary.md#permissions) dla interfejsu API sieci web. 
- Oauth2Permissions elementu członkowskiego, który jest kolekcją z [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type) jednostek, używany do definiowania [delegowane uprawnienia](active-directory-dev-glossary.md#permissions) dla interfejsu API sieci web.

Aby uzyskać więcej informacji na temat aplikacji manifestu pojęcia ogólnie rzecz biorąc, zobacz [opis manifestu aplikacji usługi Azure Active Directory](active-directory-application-manifest.md).

### <a name="accessing-the-azure-ad-graph-and-office-365-via-microsoft-graph-apis"></a>Uzyskiwanie dostępu do usługi Azure AD Graph i za pomocą programu Microsoft Graph API usługi Office 365  

Jak wspomniano wcześniej, oprócz udostępnianie/dostęp do interfejsów API dla własnej aplikacji, można zarejestrować aplikacji klienta dostępu do interfejsów API udostępnianych przez zasoby firmy Microsoft. Microsoft interfejsu API programu Graph, określane jako "Microsoft Graph" na liście zasobów/interfejsu API w portalu jest dostępny dla wszystkich aplikacji, które są zarejestrowane w usłudze Azure AD. W przypadku rejestracji aplikacji klienckiej w dzierżawie zawierającą konta, które jest zarejestrowany w subskrypcji usługi Office 365, można także przejść zakresy udostępnianych przez różnych zasobów usługi Office 365.

Pełne omówienie na zakresy udostępnianych przez interfejs API programu Graph firmy Microsoft, zobacz [zakresy uprawnień | Pojęcia dotyczące interfejsu API programu Microsoft Graph](https://graph.microsoft.io/docs/authorization/permission_scopes) artykułu.

> [!NOTE]
> Ze względu na to aktualne ograniczenie natywnego klienta aplikacji można wywołać tylko do interfejsu API programu Azure AD Graph użycie uprawnienia "Dostęp do katalogu organizacji". To ograniczenie nie ma zastosowania dla aplikacji sieci web.
> 
> 

### <a name="configuring-multi-tenant-applications"></a>Konfigurowanie aplikacji z wieloma dzierżawcami

Podczas rejestrowania aplikacji w usłudze Azure AD, możesz aplikacji jest dostępna tylko dla użytkowników w organizacji. Alternatywnie możesz aplikacja ma być dostępna dla użytkowników w organizacji zewnętrznych. Typy dwóch aplikacji noszą nazwę aplikacji dzierżawy jednym i wieloma dzierżawcami. W tej sekcji omówiono sposób zmodyfikować konfigurację aplikacji pojedynczej dzierżawy, aby była aplikacji wielodostępnej.

Ważne jest, aby zwrócić uwagę na różnice między aplikacją dzierżawy jednym i wieloma dzierżawcami:  

- Stosowanie pojedynczej dzierżawy jest przeznaczony do użytku w jednej z organizacji. Zazwyczaj jest to aplikacja (LoB)-biznesowych napisane przez autora przedsiębiorstwa. Aplikacja pojedynczego dzierżawcy można uzyskać tylko przez użytkowników z kontami w tej samej dzierżawy jako rejestracji aplikacji. W związku z tym go tylko powinna być przygotowana w jednym katalogu.
- Stosowanie wielu dzierżawców jest przeznaczony do użytku w wielu organizacjach. Określone jako aplikacji sieci web oprogramowania jako usługa (SaaS) jest zwykle napisany przez niezależnego dostawcy oprogramowania (ISV). Należy udostępnić aplikacje wielodostępne w każdej dzierżawy, gdzie użytkownicy muszą mieć dostęp. W przypadku dzierżaw innego niż ten, na którym aplikacja jest zarejestrowany zgody użytkownika lub administratora jest wymagane do zarejestrowania. Należy zauważyć, że aplikacje klienckie natywnego wielodostępne domyślnie, które są instalowane na urządzeniu właściciela zasobów. Zobacz poprzedni [omówienie framework zgody](#overview-of-the-consent-framework) sekcji, aby uzyskać szczegółowe informacje w ramach zgody.

Tworzenie wielodostępnych aplikacji wymaga zmiany rejestracji aplikacji, a także zmienia się na aplikacji sieci web. W poniższych częściach omówiono jednocześnie.

#### <a name="changing-the-application-registration-to-support-multi-tenant"></a>Zmiana rejestracji aplikacji do obsługi wielu dzierżawców

Jeśli piszesz aplikację, która ma być dostępna do klientów lub partnerów spoza organizacji, należy zaktualizować definicję aplikacji w portalu Azure.

> [!IMPORTANT]
> Usługa Azure AD wymaga aplikacji wielodostępnych być globalnie unikatowy identyfikator URI aplikacji. Identyfikator URI aplikacji jest jednym ze sposobów, który aplikacja zostanie zidentyfikowana w wiadomości protokołu. Dla aplikacji pojedynczej dzierżawy jest wystarczająca dla identyfikator URI aplikacji być unikatowe w obrębie tej dzierżawy. Aplikacji wielodostępnych musi być globalnie unikatowe dzięki usłudze Azure AD można znaleźć aplikacji we wszystkich dzierżawców. Globalne unikatowości jest wymuszana przez wymaganie identyfikator URI aplikacji ma nazwę hosta pasującą zweryfikowanej domeny dzierżawy usługi Azure AD. Na przykład, jeśli nazwa dzierżawy jest contoso.onmicrosoft.com prawidłowy identyfikator URI aplikacji będzie https://contoso.onmicrosoft.com/myapp. Jeśli dzierżawy ma zweryfikowanej domeny contoso.com, a następnie jest prawidłowy identyfikator URI aplikacji również https://contoso.com/myapp. Jeśli identyfikator URI aplikacji nie będzie zgodna z tego wzorca ustawienia aplikacji, ponieważ wielodostępne zakończy się niepowodzeniem.
> 

Aby udzielić dostępu aplikacji do użytkowników zewnętrznych: 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Jeśli Twoje zapewnia konta dostępu do więcej niż jeden, kliknij konto w prawym górnym rogu i ustaw sesji portalu do żądanej usługi Azure AD dzierżawy.
3. W okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** usługi, kliknij przycisk **rejestracji aplikacji**, następnie Znajdź/kliknij aplikacji, którą chcesz skonfigurować. Zostają przeniesieni do strony głównej rejestracji aplikacji, która otwiera **ustawienia** strony dla aplikacji.
4. Z **ustawienia** kliknij przycisk **właściwości** i zmienić **dzierżawcza Multi** przełączyć się do **tak**.

Po dokonaniu zmiany użytkowników i administratorów z innych organizacji mogą udzielenia użytkownikom zalogować się do aplikacji, umożliwiając aplikacji dostęp do zasobów zabezpieczonych przez swojej dzierżawy.

#### <a name="changing-the-application-to-support-multi-tenant"></a>Zmiana aplikację do obsługi wielu dzierżawców

Obsługa aplikacji wielodostępnych odgrywa framework zgody usługi Azure AD. Zgoda jest mechanizm, który umożliwia użytkownikom z innej dzierżawy, aby umożliwić aplikacji dostęp do zasobów zabezpieczonych przez użytkownika dzierżawcy. To środowisko jest określany jako "zgody użytkownika".

Aplikacja sieci web mogą także oferować:

- Możliwości dla administratorów na "podpisanie zapasową mojej firmy". To środowisko, określany jako "zgody administratora," umożliwiają administratorowi możliwość udzielić zgody w imieniu *wszyscy użytkownicy* w organizacji. Tylko użytkownik, który dokonuje uwierzytelnień przy użyciu konta należącego do roli administratora globalnego może zapewnić zgody administratora; inne wystąpienie błędu.

- Rejestracji środowisko dla użytkowników. Oczekuje się, że użytkownik znajduje się przycisk "rejestracji", który spowoduje przekierowanie przeglądarki do usługi Azure AD OAuth2.0 `/authorize` punktu końcowego lub OpenID Connect `/userinfo` punktu końcowego. Te punkty końcowe umożliwiają aplikacji w celu uzyskania informacji na temat nowego użytkownika, sprawdzając żądaniu. Po fazie tworzenia konta, użytkownik zobaczy monit o zgodę, podobny do przedstawionego na [omówienie framework zgody](#overview-of-the-consent-framework) sekcji.

Aby uzyskać więcej informacji o zmianach aplikacji wymagane do obsługi wielu dzierżawcza dostępu i środowiska logowania — w/tworzenia konta Zobacz:

- [How to sign in any Azure Active Directory (AD) user using the multi-tenant application pattern](active-directory-devhowto-multi-tenant-overview.md) (Jak zalogować dowolnego użytkownika usługi Azure Active Directory (AD) za pomocą wzorca aplikacji wielodostępnych)
- Lista [przykłady kodu wielodostępne](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant). 
- [Szybki Start: Dodawanie znakowania firmowego do stron logowania w usłudze Azure AD](../customize-branding.md)

### <a name="enabling-oauth-20-implicit-grant-for-single-page-applications"></a>Włączanie OAuth 2.0 niejawne Przyznaj dla jednej strony aplikacji

Jednej strony aplikacji (źródła) są zwykle struktury z JavaScript ciężki fronton, który jest uruchamiany w przeglądarce, która wywołuje interfejs API sieci web aplikacji zaplecza, aby wykonać jego logiki biznesowej. Dla źródła hostowanych w usłudze Azure AD umożliwia OAuth 2.0 niejawne Przyznaj uwierzytelnianie użytkowników przy użyciu usługi Azure AD i uzyskać token, który służy do zabezpieczania wywołań z aplikacji JavaScript klienta do jego zaplecza interfejsu API sieci web. 

Po udzieleniu zgody użytkownika tego samego protokołu uwierzytelniania może zostać użyty do uzyskania tokenów do zabezpieczenia połączenia między klientem i innych sieci web zasobów interfejsu API skonfigurowane dla aplikacji. Aby dowiedzieć się więcej o udzielania autoryzacji niejawne i pomóc zdecydować, czy jest odpowiednie dla danego scenariusza aplikacji, zobacz [opis OAuth2 niejawne Przyznaj przepływu w usłudze Azure Active Directory](active-directory-dev-understanding-oauth2-implicit-grant.md).

Domyślnie niejawne Przyznaj OAuth 2.0 jest wyłączone dla aplikacji. Można włączyć protokołu OAuth 2.0 niejawne Przyznaj aplikacji przez ustawienie `oauth2AllowImplicitFlow` wartości w jego [manifest aplikacji](active-directory-application-manifest.md).

#### <a name="to-enable-oauth-20-implicit-grant"></a>Aby włączyć niejawne Przyznaj OAuth 2.0

> [!NOTE]
> Dla szczegółowe informacje na temat Edytuj manifest aplikacji, najpierw zapoznać się w poprzedniej sekcji [Konfigurowanie zasobów aplikacji na uwidocznienie interfejsów API sieci web](#configuring-a-resource-application-to-expose-web-apis).
>

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Jeśli Twoje zapewnia konta dostępu do więcej niż jeden, kliknij konto w prawym górnym rogu i ustaw sesji portalu do żądanej usługi Azure AD dzierżawy.
3. W okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** usługi, kliknij przycisk **rejestracji aplikacji**, następnie Znajdź/kliknij aplikacji, którą chcesz skonfigurować. Zostają przeniesieni do strony głównej rejestracji aplikacji, która otwiera **ustawienia** strony dla aplikacji.
4. Przełącz się do **Edytuj manifest** strony, klikając **manifestu** ze strony rejestracji aplikacji. Zostanie otwarty Edytor manifestu z opartych na sieci web, co umożliwia **Edytuj** manifest w portalu. Zlokalizuj i ustaw wartość "oauth2AllowImplicitFlow" wartość "true". Domyślnie jest ustawiona na wartość "false".
   
  ```json
  "oauth2AllowImplicitFlow": true,
  ```
5. Zapisz zaktualizowany manifestu. Po zapisaniu interfejsu API sieci web jest skonfigurowany do używania uwierzytelniania OAuth 2.0 niejawne Przyznaj do uwierzytelniania użytkowników.

## <a name="removing-an-application"></a>Usuwanie aplikacji
W tej sekcji opisano, jak do usunięcia rejestracji aplikacji z dzierżawy usługi Azure AD.

### <a name="removing-an-application-authored-by-your-organization"></a>Usuwanie aplikacji przypisany przez organizację
Pokaż aplikacjom w Twojej organizacji został zarejestrowany w obszarze "Moje aplikacje" filtru na stronie głównej "rejestracji aplikacji" swojej dzierżawy. Te aplikacje są te, które należy ręcznie zarejestrować za pomocą portalu Azure lub programistycznie za pomocą programu PowerShell lub interfejsu API programu Graph. W szczególności są one reprezentowane przez zarówno aplikacji i nazwy głównej usługi obiekt w dzierżawie. Aby uzyskać więcej informacji, zobacz [obiekty aplikacji i nazwy głównej usługi](active-directory-application-objects.md).

#### <a name="to-remove-a-single-tenant-application-from-your-directory"></a>Aby usunąć aplikację pojedynczej dzierżawy z katalogu
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Jeśli Twoje zapewnia konta dostępu do więcej niż jeden, kliknij konto w prawym górnym rogu i ustaw sesji portalu do żądanej usługi Azure AD dzierżawy.
3. W okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** usługi, kliknij przycisk **rejestracji aplikacji**, następnie Znajdź/kliknij aplikacji, którą chcesz skonfigurować. Zostają przeniesieni do strony głównej rejestracji aplikacji, która otwiera **ustawienia** strony dla aplikacji.
4. Na stronie głównej rejestracji aplikacji, kliknij **usunąć**.
5. Kliknij przycisk **tak** w komunikacie potwierdzenia.

#### <a name="to-remove-a-multi-tenant-application-from-its-home-directory"></a>Aby usunąć aplikację wielodostępne z jego katalogu macierzystego
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Jeśli Twoje zapewnia konta dostępu do więcej niż jeden, kliknij konto w prawym górnym rogu i ustaw sesji portalu do żądanej usługi Azure AD dzierżawy.
3. W okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** usługi, kliknij przycisk **rejestracji aplikacji**, następnie Znajdź/kliknij aplikacji, którą chcesz skonfigurować. Zostają przeniesieni do strony głównej rejestracji aplikacji, która otwiera **ustawienia** strony dla aplikacji.
4. Z **ustawienia** wybierz pozycję **właściwości** i zmień **dzierżawcza Multi** przełączyć się do **nr**, aby najpierw zmień aplikacji pojedynczego dzierżawcy, następnie kliknij przycisk **zapisać**. Obiekty główne usługi aplikacji pozostają w dzierżawcami wszystkie organizacje, które już wyraża zgodę na jej.
5. Polecenie **usunąć** przycisk ze strony głównej rejestracji aplikacji.
6. Kliknij przycisk **tak** w komunikacie potwierdzenia.

### <a name="removing-a-multi-tenant-application-authorized-by-another-organization"></a>Usuwanie aplikacji wielodostępnych, autoryzowany w innej organizacji
Część aplikacji, które Pokaż w filtrze "Wszystkie aplikacje" (z wyłączeniem rejestracji "Moje aplikacje") na stronie głównej "Rejestracji aplikacji" Twojej dzierżawy są aplikacje wielodostępne. Te aplikacje wielodostępne pochodzą z innego dzierżawcę pod względem technicznym, a podczas procesu zgody zostały zarejestrowane w dzierżawie. W szczególności są one reprezentowane przez tylko usługa główna obiektu w dzierżawie, bez odpowiedniego obiektu aplikacji. Aby uzyskać więcej informacji na temat różnic między aplikacją a obiekty główne usług, zobacz [aplikacji i usług obiektów principal w usłudze Azure AD](active-directory-application-objects.md).

Aby usunąć aplikację wielodostępne dostępu do katalogu (po udzielenia zgody), administrator firmy, należy usunąć jego nazwy głównej usługi. Administrator musi mieć dostęp administratora globalnego i można usunąć za pomocą portalu Azure lub użyj [poleceń cmdlet programu Azure AD PowerShell](http://go.microsoft.com/fwlink/?LinkId=294151) Aby usunąć dostęp.

## <a name="next-steps"></a>Kolejne kroki
- Aby uzyskać więcej informacji dotyczących działania uwierzytelniania w usłudze Azure AD, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](active-directory-authentication-scenarios.md).
- Zobacz [znakowanie wytyczne dotyczące zintegrowanych aplikacji](active-directory-branding-guidelines.md) dotyczące visual wskazówki dotyczące aplikacji.
- Aby uzyskać więcej informacji na relacje między obiektów aplikacji i nazwę główną usługi dla aplikacji, zobacz [obiekty aplikacji i nazwy głównej usługi](active-directory-application-objects.md).
- Aby dowiedzieć się więcej na temat roli pełni manifestu aplikacji, zobacz [opis manifestu aplikacji usługi Azure Active Directory](active-directory-application-manifest.md)
- Zobacz [słownik dewelopera usługi Azure AD](active-directory-dev-glossary.md) definicje niektóre podstawowe koncepcje dewelopera usługi Azure Active Directory (AD).
- Odwiedź stronę [przewodnik dewelopera usługi Active Directory](active-directory-developers-guide.md) omówienie związane z programowaniem całą jego zawartość.

