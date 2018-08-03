---
title: Integrowanie aplikacji z usługą Azure Active Directory
description: Jak dodawanie, aktualizowanie lub usuwanie aplikacji w usłudze Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: luleon
ms.openlocfilehash: 6214d8aa6be17223ec3f630206249676a47783ec
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449890"
---
# <a name="integrating-applications-with-azure-active-directory"></a>Integrowanie aplikacji z usługą Azure Active Directory
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Deweloperów w przedsiębiorstwach i dostawców software-as-a-service (SaaS) mogą powstać komercyjnych usług chmurowych lub aplikacje line-of-business, które można zintegrować z usługą Azure Active Directory (Azure AD) w celu zapewnienia bezpiecznego logowania i autoryzacji ich usługi. Aby zintegrować aplikację lub usługę z usługą Azure AD, deweloper musi najpierw zarejestrować ją z usługą Azure AD.

W tym artykule pokazano, jak dodać, zaktualizować lub usunąć rejestrację aplikacji w usłudze Azure AD. Dowiedz się więcej o różnych typach aplikacji, które można zintegrować z usługą Azure AD, jak skonfigurować aplikacje, aby uzyskać dostęp do innych zasobów, takich jak interfejsy API sieci web i inne.

Aby dowiedzieć się więcej na temat dwa obiekty usługi Azure AD, które reprezentują zarejestrowaną aplikację i relacji między nimi, zobacz [obiekty aplikacji i jednostki usługi](active-directory-application-objects.md); Aby dowiedzieć się więcej na temat wytycznych znakowania, należy Podczas tworzenia aplikacji w usłudze Azure Active Directory, zobacz [znakowania wytyczne dotyczące zintegrowanych aplikacji](active-directory-branding-guidelines.md).

## <a name="adding-an-application"></a>Dodawanie aplikacji
Każda aplikacja, która chce korzystać z możliwości usługi Azure AD, najpierw musi być zarejestrowana w dzierżawę usługi Azure AD. Ten proces rejestracji obejmuje, zapewniając usługi Azure AD szczegółów dotyczących aplikacji, na przykład adres URL, gdzie znajduje się, adres URL, aby wysyłać odpowiedzi, po uwierzytelnieniu użytkownika identyfikatora URI, który identyfikuje aplikację i tak dalej.

### <a name="to-register-a-new-application-using-the-azure-portal"></a>Aby zarejestrować nową aplikację w witrynie Azure portal
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Jeśli dzierżawa udostępnia swoje konto, dostęp do więcej niż jeden, kliknij konto w prawym górnym rogu i ustaw sesję portalu do żądanej usługi Azure AD.
3. W okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** usługi, kliknij przycisk **rejestracje aplikacji**i kliknij przycisk **rejestrowanie nowej aplikacji**.

   ![Rejestrowanie nowej aplikacji](./media/active-directory-integrating-applications/add-app-registration.png)

4. Gdy **Utwórz** zostanie wyświetlona strona, wprowadź informacje o rejestracji aplikacji: 

  - **Nazwa:** wprowadź nazwę opisową aplikacji
  - **Typ aplikacji:** 
    - Wybierz "Native" [aplikacje klienckie](active-directory-dev-glossary.md#client-application) są instalowane lokalnie na urządzeniu. To ustawienie jest używane w OAuth publicznego [klientów natywnych](active-directory-dev-glossary.md#native-client).
    - Wybierz pozycję "aplikacja sieci Web / interfejsu API" dla [aplikacje klienckie](active-directory-dev-glossary.md#client-application) i [aplikacji interfejsu API i zasobów](active-directory-dev-glossary.md#resource-server) są instalowane na zabezpieczonym serwerze. To ustawienie jest używane do uwierzytelniania OAuth poufne [sieci web klientów](active-directory-dev-glossary.md#web-client) i publicznych [klientów z systemem agenta użytkownika](active-directory-dev-glossary.md#user-agent-based-client). Ta sama aplikacja również udostępnić zarówno klient, jak i interfejsu API i zasobów.
  - **Adres URL logowania:** dla "aplikacja sieci Web / interfejs API" aplikacji, podaj podstawowy adres URL aplikacji. Na przykład `http://localhost:31544` może być adres URL aplikacji internetowej uruchomionej na komputerze lokalnym. Użytkownicy użyje tego adresu URL, aby zalogować się do aplikacji klienta sieci web. 
  - **Identyfikator URI przekierowania:** "Natywnego" aplikacji, podaj identyfikator URI używany przez usługę Azure AD do zwracania odpowiedzi tokenu. Na przykład wprowadź wartość specyficzną dla twojej aplikacji `http://MyFirstAADApp`

   ![Rejestrowanie nowej aplikacji — tworzenie](./media/active-directory-integrating-applications/add-app-registration-create.png)

   Jeśli chcesz konkretne przykłady dla aplikacji sieci web lub aplikacji natywnych, zapoznaj się z naszym [przewodników Szybki Start](azure-ad-developers-guide.md#get-started).

5. Po zakończeniu kliknij pozycję **Gotowe**. Usługa Azure AD przypisuje unikatowy identyfikator aplikacji do aplikacji i nastąpi przekierowanie do strony głównej rejestracji aplikacji. W zależności od tego, czy aplikacja działa w sieci web lub aplikacji natywnej Aby dodać funkcje do aplikacji znajdują się różne opcje. Zobacz następną sekcję Omówienie wyrażania zgody i szczegółowe informacje na temat włączania funkcji dodatkowych czynności konfiguracyjnych w rejestracji aplikacji (poświadczeń, uprawnień, włączenie logowania dla użytkowników z innych dzierżaw).

  > [!NOTE]
  > Domyślnie aplikacja sieci web nowo zarejestrowanego jest skonfigurowany do zezwalania **tylko** użytkowników z tej samej dzierżawie, aby zalogować się do aplikacji.
  > 
  > 

## <a name="updating-an-application"></a>Aktualizowanie aplikacji
Po zarejestrowaniu aplikacji z usługą Azure AD może być konieczne można zaktualizować w celu zapewnienia dostępu do interfejsów API sieci web, były dostępne w innych organizacji i nie tylko. W tej sekcji opisano różne sposoby, w których można skonfigurować dalszych aplikacji. Najpierw Rozpoczniemy pracę omówienie platformy wyrażania zgody, które należy zrozumieć podczas kompilowania aplikacji, które muszą być używane przez innych użytkowników lub aplikacji.

### <a name="overview-of-the-consent-framework"></a>Omówienie platformy wyrażania zgody

Platformy wyrażania zgody w usłudze Azure AD ułatwia opracowywanie sieci web z wieloma dzierżawami i natywne aplikacje klienckie. Te aplikacje Zezwalaj na logowanie za pomocą kont użytkownika z dzierżawy usługi Azure AD, inny niż ten, na którym aplikacja jest zarejestrowany. Mogą one również potrzebować dostępu do sieci web, interfejsów API, np. interfejsu API programu Microsoft Graph (Aby uzyskać dostęp do usługi Azure Active Directory, Intune i usługi w usłudze Office 365) i innych usług firmy Microsoft, oprócz własnych interfejsów API sieci web. Struktura opiera się na użytkownika lub administratora wyrażenia zgody na aplikację, która prosi do zarejestrowania się w jego katalogu, który może obejmować dostęp do danych katalogu.

Na przykład jeśli wymaga aplikacji klienta sieci web na odczytywanie kalendarza informacje o użytkowniku z usługi Office 365, tego użytkownika jest wymagane do wyrażenia zgody aplikacji klienckiej. Po zgoda zostanie podany, aplikacja kliencka będzie do wywołania interfejsu API programu Microsoft Graph w imieniu użytkownika, skorzystaj z informacji kalendarza, zgodnie z potrzebami. [Interfejsu API Microsoft Graph](https://graph.microsoft.io) zapewnia dostęp do danych w usłudze Office 365 (np. kalendarzy i komunikaty z programu Exchange, witryn i list programu SharePoint, dokumentów z poziomu usługi OneDrive, notesów z programu OneNote, zadań przy użyciu narzędzia planista skoroszytów programu Excel, itp.), a także użytkownicy i grupy z usługi Azure AD i innych obiektów danych z więcej usług w chmurze firmy Microsoft. 

Platformy wyrażania zgody jest oparta na protokołu OAuth 2.0 i jego różnych przepływów, takie jak udzielić kod autoryzacji poświadczenia przydział i klienta, przy użyciu publicznego lub poufnych klientów. Za pomocą protokołu OAuth 2.0, usługi Azure AD umożliwia tworzenie wiele różnych typów aplikacji klienckich, takich jak na telefonie, tablecie, serwera lub aplikacji sieci web i uzyskiwania dostępu do wymaganych zasobów.

Aby uzyskać więcej informacji o użyciu platformy wyrażania zgody przydziałów autoryzacji OAuth 2.0, zobacz [Autoryzowanie dostępu do aplikacji sieci web przy użyciu protokołu OAuth 2.0 i usługi Azure AD](active-directory-protocols-oauth-code.md) i [scenariusze uwierzytelniania dla usługi Azure AD](active-directory-authentication-scenarios.md). Aby uzyskać informacje o pobieraniu autoryzowanego dostępu do usługi Office 365 za pomocą programu Microsoft Graph, zobacz [uwierzytelniania aplikacji za pomocą programu Microsoft Graph](https://graph.microsoft.io/docs/authorization/auth_overview).

#### <a name="example-of-the-consent-experience"></a>Przykład środowiska zgody

Poniższe kroki pokazują, jak zgody środowisko działa dla deweloperów aplikacji i użytkownika.

1. Załóżmy, że masz klienta aplikacji sieci web, który wymaga, aby żądać określonych uprawnień dostępu do zasobów/interfejsu API. Dowiesz się, jak przeprowadzić tę konfigurację w następnej sekcji, ale zasadniczo witryny Azure portal jest używane do deklarowania żądań dotyczących uprawnień podczas konfiguracji. Podobnie jak inne ustawienia konfiguracji staną się częścią aplikacji usługi Azure AD rejestracji:
   
  ![Uprawnienia do innych aplikacji](./media/active-directory-integrating-applications/requiredpermissions.png)
    
2. Należy wziąć pod uwagę uprawnienia aplikacji zostały zaktualizowane, aplikacja jest uruchomiona i użytkownik ma użyć go po raz pierwszy. Najpierw aplikację Aby uzyskać kod autoryzacji z usługi Azure AD `/authorize` punktu końcowego. Kod autoryzacji mogą następnie służyć do uzyskania nowego dostępu i token odświeżania.

3. Jeśli użytkownik nie jest już uwierzytelniony, Azure AD `/authorize` punktu końcowego monity podczas logowania.
   
  ![Użytkownik lub administrator, zaloguj się do usługi Azure AD](./media/active-directory-integrating-applications/usersignin.png)

4. Po użytkownik zalogował się w usłudze Azure AD określi, jeśli użytkownik powinien być wyświetlony strona zgody użytkownika. To jest na podstawie tego, czy użytkownik (lub administratora w organizacji) już udzielił zgody aplikacji. Jeśli zgody nie ma już przyznane, usługi Azure AD monituje użytkownika o zgodę i wyświetli wymagane uprawnienia niezbędne do działania. Zestaw uprawnień, które są wyświetlane w oknie dialogowym wyrażania zgody są zgodne z typami, które wybrano w delegowane uprawnienia w witrynie Azure portal.
   
  ![Środowisko zgody użytkownika](./media/active-directory-integrating-applications/consent.png)

5. Po użytkownik udziela zgody, Kod autoryzacji jest zwracana do aplikacji jest zrealizowany uzyskiwanie tokenu dostępu i token odświeżania. Aby uzyskać więcej informacji na temat tego przepływu, zobacz [sieci web aplikacji sieci Web interfejsu API sekcji scenariusze uwierzytelniania dla usługi Azure AD](active-directory-authentication-scenarios.md#web-application-to-web-api).

6. Jako administrator mogą również wyrazić zgodę na delegowane uprawnienia aplikacji w imieniu wszystkich użytkowników w dzierżawie. Zgody administratora, okno dialogowe ze zgodą zapobiega wyświetlane dla każdego użytkownika w dzierżawie i może odbywać się [witryny Azure portal](https://portal.azure.com) przez użytkowników z roli administratora. Z **ustawienia** stronie aplikacji, kliknij przycisk **wymagane uprawnienia** i kliknij pozycję **Udziel uprawnień** przycisku. 

  ![Przyznawanie uprawnień dla zgody administratora jawne](./media/active-directory-integrating-applications/grantpermissions.png)
    
  > [!NOTE]
  > Udzielanie jawnej zgody, za pomocą **Udziel uprawnień** przycisk jest obecnie wymagane dla aplikacji jednostronicowej (SPA), które używają ADAL.js. W przeciwnym razie aplikacja ulegnie awarii, gdy żąda tokenu dostępu. 

### <a name="configure-a-client-application-to-access-web-apis"></a>Konfigurowanie aplikacji klienckiej dostęp do interfejsów API sieci web
Aby dla sieci web/poufnych aplikacji klienckiej móc uczestniczyć w przepływie przyznawania autoryzacji, który wymaga uwierzytelniania (i uzyskania tokenu dostępu) jego ustanowienia bezpiecznych poświadczeń. Domyślną metodą uwierzytelniania obsługiwane przez witryny Azure portal jest identyfikator klienta i klucz tajny. W tej sekcji opisano czynności konfiguracyjne wymagane jest podanie klucza tajnego przy użyciu poświadczeń klienta.

Ponadto, zanim klient może uzyskać dostęp do internetowego interfejsu API jest udostępniany przez aplikację zasobu (np. interfejsu API Microsoft Graph), platformy wyrażania zgody zapewnia klient uzyskuje udzielenie uprawnienia wymagane, na podstawie uprawnień żądany. Domyślnie wszystkie aplikacje mogą wybierać uprawnienia "Windows Azure Active Directory" (interfejs API programu Graph) i "Windows interfejsu API zarządzania usługami platformy Azure." [Uprawnienie "Sign-in and profilu użytkownika odczytu" interfejsu API programu Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes#PermissionScopeDetails) również jest domyślnie zaznaczone. Jeśli Twój klient jest rejestrowany w dzierżawie, który ma kont subskrypcji usługi Office 365, do wyboru dostępne są interfejsy API sieci web i uprawnienia dla programu SharePoint i usługi Exchange Online. Możesz wybrać z [dwa typy uprawnień](active-directory-dev-glossary.md#permissions) dla każdego żądanego interfejsu API sieci web:

- Uprawnienia aplikacji: Twoja aplikacja kliencka potrzebuje dostępu do interfejsu API sieci web bezpośrednio w swoim imieniu (Brak kontekstu użytkownika). Tego rodzaju uprawnień wymaga zgody administratora i nie jest również dostępna dla aplikacji klienta natywnego.

- Delegowane uprawnienia: Twoja aplikacja kliencka potrzebuje dostępu do sieci web interfejsu API zalogowanego użytkownika, ale z dostęp ograniczony przez wybrane uprawnienie. Tego rodzaju uprawnień mogą być udzielane przez użytkownika, chyba że uprawnień wymaga zgody administratora. 

  > [!NOTE]
  > Dodawanie uprawnienia delegowane do aplikacji nie powoduje automatycznego przyznania wyrażania zgody dla użytkowników w dzierżawie. Użytkownicy muszą nadal ręcznie wyrazić zgodę dodano uprawnienia delegowanej w czasie wykonywania, chyba że administrator kliknie **Udziel uprawnień** przycisk **wymagane uprawnienia** sekcji Strona aplikacji w witrynie Azure portal. 

#### <a name="to-add-application-credentials-or-permissions-to-access-web-apis"></a>Aby dodać poświadczenia aplikacji lub uprawnienia dostępu do interfejsów API sieci web
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Jeśli dzierżawa udostępnia swoje konto, dostęp do więcej niż jeden, kliknij konto w prawym górnym rogu i ustaw sesję portalu do żądanej usługi Azure AD.
3. W okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** usługi, kliknij przycisk **rejestracje aplikacji**, następnie Znajdź/kliknij aplikację, którą chcesz skonfigurować.

   ![Aktualizowanie rejestracji aplikacji](./media/active-directory-integrating-applications/update-app-registration.png)

4. Nastąpi przekierowanie do strony głównej rejestracji aplikacji, która otwiera **ustawienia** strony aplikacji. Aby dodać klucz tajny dla aplikacji sieci web poświadczeń:
  - Kliknij przycisk **klucze** sekcji na **ustawienia** strony. 
  - Dodaj opis klucza.
  - Wybierz jednego lub dwóch lat czasu trwania.
  - Kliknij pozycję **Zapisz**. Kolumna najdalej z prawej strony będzie zawierać wartości klucza, po zapisaniu zmian w konfiguracji. **Pamiętaj skopiować klucz** do użycia w kodzie aplikacji klienta, ponieważ nie jest dostępny po opuścisz tę stronę.

  ![Aktualizowanie rejestracji aplikacji — klucze](./media/active-directory-integrating-applications/update-app-registration-settings-keys.png)

5. Aby dodać uprawnienia dostępu do zasobu interfejsy API z klienta
  - Kliknij przycisk **wymagane uprawnienia** sekcji na **ustawienia** strony. 
  - Kliknij przycisk **Dodaj**.
  - Kliknij przycisk **wybierz interfejs API** aby wybrać typ zasoby, które mają do wyboru.
  - Przejrzyj listę dostępnych interfejsów API lub użyj pola wyszukiwania, które można wybierać aplikacje dostępnych zasobów w katalogu, które udostępniają interfejs API sieci web. Kliknij zasób, możesz interesuje, a następnie kliknij przycisk **wybierz**.
  - Nastąpi przekierowanie do **Włącz dostęp za pomocą** strony. Wybierz uprawnienia aplikacji i/lub delegowane uprawnienia wymaganych przez aplikację podczas uzyskiwania dostępu do interfejsu API.
   
  ![Aktualizowanie rejestracji aplikacji — uprawnienia interfejsu api](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-api.png)

  ![Aktualizowanie rejestracji aplikacji — perms uprawnień](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-perms.png)

6. Po zakończeniu kliknij przycisk **wybierz** znajdujący się na **Włącz dostęp za pomocą** strony, a następnie **gotowe** znajdujący się na **dostępu Dodaj interfejs API** strony. Nastąpi powrót do **wymagane uprawnienia** strony, w którym nowy zasób zostanie dodany do listy interfejsów API.

  > [!NOTE]
  > Klikając **gotowe** przycisku automatycznie ustawia uprawnienia dla aplikacji w katalogu, w oparciu o uprawnienia do innych aplikacji, które można skonfigurować. Można wyświetlić te uprawnienia aplikacji, analizując aplikacji **ustawienia** strony.
  > 
  > 

### <a name="configuring-a-resource-application-to-expose-web-apis"></a>Konfigurowanie aplikacji zasobów, aby udostępnić interfejsów API sieci web

Można rozwijać interfejs API sieci web i udostępnić go aplikacjom klienckim, zapewniając dostęp [zakresy](active-directory-dev-glossary.md#scopes) i [role](active-directory-dev-glossary.md#roles). Prawidłowo skonfigurowany, internetowy interfejs API jest udostępniany podobnie jak inne firmy Microsoft interfejsy API sieci web, łącznie z interfejsu API programu Graph i interfejsów API usługi Office 365. Zakresy dostępu i role, które są udostępniane za pośrednictwem usługi [manifest aplikacji](active-directory-dev-glossary.md#application-manifest), czyli pliku JSON, który reprezentuje konfigurację tożsamości aplikacji. 

Poniższej sekcji pokazano, jak udostępnić zakresy dostępu, modyfikując manifest aplikacji zasobu.

#### <a name="adding-access-scopes-to-your-resource-application"></a>Dodawanie zakresów dostępu do zasobów aplikacji

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Jeśli dzierżawa udostępnia swoje konto, dostęp do więcej niż jeden, kliknij konto w prawym górnym rogu i ustaw sesję portalu do żądanej usługi Azure AD.

3. W okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** usługi, kliknij przycisk **rejestracje aplikacji**, następnie Znajdź/kliknij aplikację, którą chcesz skonfigurować.

   ![Aktualizowanie rejestracji aplikacji](./media/active-directory-integrating-applications/update-app-registration.png)

4. Nastąpi przekierowanie do strony głównej rejestracji aplikacji, która otwiera **ustawienia** strony aplikacji. Przełącz się do **Edytuj manifest** strony, klikając **manifestu** na stronie rejestracji aplikacji. Zostanie otwarty Edytor manifestu z opartej na sieci web, umożliwiając **Edytuj** manifest w portalu. Opcjonalnie można kliknąć **Pobierz** i edytować lokalnie, a następnie użyj **przekazywanie** Aby ponownie zastosować go do aplikacji.

5. W tym przykładzie firma Microsoft udostępni nowy zakres o nazwie `Employees.Read.All` na naszych zasobów/interfejsu API, dodając następujący element JSON do `oauth2Permissions` kolekcji. Istniejące `user_impersonation` zakres znajduje się domyślnie podczas rejestracji. `user_impersonation` umożliwia aplikacji klienta zażądać uprawnień dostępu do zasobu, w obszarze tożsamość zalogowanego użytkownika. Pamiętaj dodać przecinek po istniejącej `user_impersonation` zakres elementu i zmiany wartości właściwości do potrzeb Twojego zasobu. 

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
  > Wartość "id" musi zostać wygenerowany przy użyciu narzędzia do generowania identyfikatora GUID, takich jak [guidgen](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx) lub programowo. Jak udostępnianych przez internetowy interfejs API reprezentuje unikatowy identyfikator dla zakresu. Gdy klient został odpowiednio skonfigurowany z uprawnieniami dostępu do interfejsu API sieci web, wystawiono tokenu dostępu OAuth 2.0 usługa Azure AD. Gdy klient wywołań interfejsu API sieci web, prezentuje tokenu dostępu, który ma zakres (scp) zestawu oświadczeń uprawnień wymaganych w jego rejestracji aplikacji.
  >
  > Aby uwidocznić dodatkowe zakresy, później w razie. Należy rozważyć, czy internetowy interfejs API może udostępniać wiele zakresów, skojarzone z szeregu różnych funkcji. Zasób można kontrolować dostęp do interfejsu API sieci web w czasie wykonywania, oceniając zakresu (`scp`) oświadczenia w tokenie dostępu OAuth 2.0 odebrane.
  > 

6. Po zakończeniu kliknij przycisk **Zapisz**. Teraz interfejs API sieci web jest skonfigurowana do użycia przez inne aplikacje w katalogu. 

  ![Aktualizowanie rejestracji aplikacji](./media/active-directory-integrating-applications/update-app-registration-manifest.png)

#### <a name="verify-the-web-api-is-exposed-to-other-applications-in-your-tenant"></a>Sprawdzić, czy internetowy, który interfejs API jest udostępniany innym aplikacjom w Twojej dzierżawie
1. Przejdź z powrotem do usługi Azure AD dzierżawy, kliknij przycisk **rejestracje aplikacji** ponownie, następnie Znajdź/kliknij aplikacja kliencka, którą chcesz skonfigurować.

   ![Aktualizowanie rejestracji aplikacji](./media/active-directory-integrating-applications/update-app-registration.png)

2. Powtórz krok 5, tak jak w [skonfigurować aplikację klienta dostępu do interfejsów API sieci web](#configure-a-client-application-to-access-web-apis). Po przejściu do **wybierz interfejs API** krok, wyszukaj zasób, wprowadzając jego nazwę aplikacji w polu wyszukiwania, a następnie kliknij przycisk **wybierz**. 

3. Na **Włącz dostęp za pomocą** stronie powinien zostać wyświetlony nowy zakres, dostępne dla żądań dotyczących uprawnień klienta.

  ![Nowe uprawnienia są wyświetlane.](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-perms-newscopes.png)

#### <a name="more-on-the-application-manifest"></a>Więcej informacji na temat manifest aplikacji

Manifest aplikacji faktycznie służy jako mechanizm aktualizowanie jednostek aplikacji, który definiuje wszystkie atrybuty konfiguracji tożsamość aplikacji usługi Azure AD, w tym zakresy dostępu do interfejsu API, które Omówiliśmy. Aby uzyskać więcej informacji na temat jednostek aplikacji i jego schematu, zobacz [dokumentacja jednostek aplikacji interfejsu API programu Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity). Ten artykuł zawiera pełną dokumentację informacji na temat elementów członkowskich jednostki aplikacji można określić uprawnienia dla interfejsu API, w tym:  

- AppRoles elementu członkowskiego, który jest kolekcją z [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type) jednostki używane do definiowania [uprawnienia aplikacji](active-directory-dev-glossary.md#permissions) dla internetowego interfejsu API. 
- Oauth2Permissions elementu członkowskiego, który jest kolekcją z [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type) jednostki używane do definiowania [delegowane uprawnienia](active-directory-dev-glossary.md#permissions) dla internetowego interfejsu API.

Aby uzyskać więcej informacji na temat aplikacji manifestu pojęcia ogólnie rzecz biorąc, zobacz [opis manifestu aplikacji usługi Azure Active Directory](active-directory-application-manifest.md).

### <a name="accessing-the-azure-ad-graph-and-office-365-via-microsoft-graph-apis"></a>Uzyskiwanie dostępu do funkcji Azure AD Graph i usługi Office 365 za pośrednictwem interfejsów API programu Microsoft Graph  

Jak wspomniano wcześniej, oprócz udostępnianie/uzyskiwania dostępu do interfejsów API na potrzeby własnych aplikacji można zarejestrować aplikacji klienckiej dostęp do interfejsów API udostępnianych przez zasoby firmy Microsoft. Microsoft interfejsu API programu Graph, określane jako "Programu Microsoft Graph" na liście portalu interfejsu API i zasobów, jest dostępna dla wszystkich aplikacji, które są zarejestrowane w usłudze Azure AD. W przypadku rejestracji aplikacji klienckiej w dzierżawie, zawierającą konta, które zostało zarejestrowane w celu uzyskania subskrypcji usługi Office 365, można także przejść zakresy udostępnianych przez różnych zasobów usługi Office 365.

Aby uzyskać wyczerpujące omówienie na zakresy udostępnianych przez interfejs API programu Microsoft Graph, zobacz [odwołanie do uprawnień programu Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/concepts/permissions_reference) artykułu.

> [!NOTE]
> Ze względu na to aktualne ograniczenie natywne aplikacje klienckie może wywoływać tylko interfejsu API programu Graph usługi Azure AD, gdy korzystają uprawnienie "Dostęp do katalogu organizacji". To ograniczenie nie dotyczy aplikacji sieci web.
> 
> 

### <a name="configuring-multi-tenant-applications"></a>Konfigurowanie aplikacji wielodostępnych

Podczas rejestrowania aplikacji w usłudze Azure AD, możesz aplikację, aby był dostępny tylko dla użytkowników w Twojej organizacji. Alternatywnie można również aplikację, aby mieć dostęp użytkownicy w organizacjach zewnętrznych. Te typy dwóch aplikacji są nazywane pojedynczej dzierżawy i wielodostępnej aplikacji. W tej sekcji omówiono sposób zmodyfikować konfigurację aplikacji pojedynczej dzierżawy, aby stał się aplikacji z wieloma dzierżawami.

Warto zwrócić uwagę na różnice między jedną dzierżawą i wielodostępnych aplikacji:  

- Aplikacja jednej dzierżawy jest przeznaczony do użytku w jednej z organizacji. Zazwyczaj jest to aplikacja (LoB) line-of-business, napisane przez Deweloper w przedsiębiorstwie. Aplikacja jednej dzierżawy może zostać oceniony jedynie przez użytkowników z kontami w tej samej dzierżawy, ponieważ rejestracja aplikacji. W rezultacie go tylko powinna być przygotowana w jednym katalogu.
- Aplikacja wielodostępna jest przeznaczony do użytku w wielu organizacjach. Określone jako aplikacja internetowa, oprogramowanie jako usługa (SaaS), jest zazwyczaj napisany przez niezależnego dostawcę oprogramowania (ISV). Aplikacje wielodostępne musi być obsługiwana w każdej dzierżawy, w których użytkownicy muszą mieć dostęp. W przypadku dzierżaw niż to, gdzie aplikacja będzie zarejestrowana zgody użytkownika lub administratora jest wymagane w celu zarejestrowania. Należy pamiętać, że natywne aplikacje klienckie są wielodostępne domyślnie, które są instalowane na urządzeniu właściciel zasobu. Zobacz poprzedni [omówienie platformy wyrażania zgody](#overview-of-the-consent-framework) sekcji, aby uzyskać szczegółowe informacje na temat platformy wyrażania zgody.

Tworzenie wielodostępnych aplikacji wymaga obu zmian rejestracji aplikacji, a także zmienia się na samej aplikacji sieci web. W poniższych częściach omówiono zarówno.

#### <a name="changing-the-application-registration-to-support-multi-tenant"></a>Zmiana rejestrowanie aplikacji do obsługi wielu dzierżaw

Jeśli piszesz aplikację, którą chcesz udostępnić swoich klientów lub partnerów spoza organizacji, musisz zaktualizować definicję aplikacji w witrynie Azure portal.

> [!IMPORTANT]
> Usługa Azure AD wymaga identyfikator URI Identyfikatora aplikacji dla aplikacji wielodostępnych być globalnie unikatowa. Identyfikator URI Identyfikatora aplikacji jest jednym ze sposobów, w których aplikacja jest identyfikowana w komunikatach protokołu. W przypadku aplikacji pojedynczej dzierżawy jest wystarczająca dla URI Identyfikatora aplikacji była unikatowa w obrębie tej dzierżawy. W przypadku aplikacji wielodostępnych go musi być globalnie unikatowa dzięki usłudze Azure AD można znaleźć aplikacji we wszystkich dzierżawach. Unikatowość globalne są wymuszane, wymagając identyfikator URI Identyfikatora aplikacji ma nazwę hosta, który pasuje do zweryfikowanej domeny dzierżawy usługi Azure AD. Na przykład, jeśli nazwa dzierżawy jest contoso.onmicrosoft.com prawidłowy identyfikator URI Identyfikatora aplikacji będzie https://contoso.onmicrosoft.com/myapp. Jeśli Twoja dzierżawa ma zweryfikowanej domeny contoso.com, a następnie prawidłowy identyfikator URI aplikacji będą również https://contoso.com/myapp. Jeśli identyfikator URI Identyfikatora aplikacji nie korzystać z tego wzoru, ustawienia aplikacji, ponieważ wielodostępnych zakończy się niepowodzeniem.
> 

Aby udzielić użytkownikom zewnętrznym uzyskiwanie dostępu do aplikacji: 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Jeśli dzierżawa udostępnia swoje konto, dostęp do więcej niż jeden, kliknij konto w prawym górnym rogu i ustaw sesję portalu do żądanej usługi Azure AD.
3. W okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** usługi, kliknij przycisk **rejestracje aplikacji**, następnie Znajdź/kliknij aplikację, którą chcesz skonfigurować. Nastąpi przekierowanie do strony głównej rejestracji aplikacji, która otwiera **ustawienia** strony aplikacji.
4. Z **ustawienia** kliknij **właściwości** i zmień **wielodostępnych** przełączyć się do **tak**.

Po dokonaniu zmiany użytkowników i administratorów w innych organizacjach mogą udostępnić użytkownikom możliwość Zaloguj się do aplikacji, pozwalając na dostęp do zasobów zabezpieczonych przez ich dzierżawy aplikacji.

#### <a name="changing-the-application-to-support-multi-tenant"></a>Wprowadzanie zmian do aplikacji do obsługi wielu dzierżaw

Wsparcie dla aplikacji wielodostępnych rolę odgrywa platformy wyrażania zgody w usłudze Azure AD. Zgoda jest mechanizm, który umożliwia użytkownikom z innej dzierżawy, aby przyznać aplikacji dostęp do zasobów zabezpieczonych przez dzierżawę użytkownika. To środowisko jest określany jako "zgoda użytkownika".

Aplikacja sieci web mogą także oferować:

- Możliwości dla administratorów "Zarejestruj się Moja firma." To doświadczenie, określane jako "zgoda administratora" umożliwiają administratorowi możliwość wyrazić zgody w imieniu *wszyscy użytkownicy* w swojej organizacji. Tylko użytkownik, który jest uwierzytelniany przy użyciu konta należącego do roli administratora globalnego może zapewnić zgody administratora; inne wystąpienie błędu.

- Zarejestruj się środowisko dla użytkowników. Oczekuje się, że użytkownik został podany "Zarejestruj się" przycisk, który będzie przekierowywać przeglądarkę do Azure AD OAuth2.0 `/authorize` punkt końcowy lub OpenID Connect `/userinfo` punktu końcowego. Te punkty końcowe umożliwiają aplikacji w celu uzyskania informacji na temat nowego użytkownika, sprawdzając id_token. Następujące fazy rejestracji użytkownik zobaczy monit o wyrażenie zgody, podobny do przedstawionego na [omówienie platformy wyrażania zgody](#overview-of-the-consent-framework) sekcji.

Aby uzyskać więcej informacji na temat zmian aplikacji wymagane do obsługi wielodostępnych dostępu i logowania — w/rejestracją środowisk, zobacz:

- [How to sign in any Azure Active Directory (AD) user using the multi-tenant application pattern](active-directory-devhowto-multi-tenant-overview.md) (Jak zalogować dowolnego użytkownika usługi Azure Active Directory (AD) za pomocą wzorca aplikacji wielodostępnych)
- Lista [przykłady kodu z wieloma dzierżawami](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant). 
- [Szybki Start: Dodawanie znakowania firmowego do stron logowania w usłudze Azure AD](../fundamentals/customize-branding.md)

### <a name="enabling-oauth-20-implicit-grant-for-single-page-applications"></a>Włączanie protokołu OAuth 2.0 niejawne udziela do aplikacje jednostronicowe

Aplikacja jednostronicowa firmy (źródła) zazwyczaj są skonstruowane przy użyciu języka JavaScript ciężkich fronton, który działa w przeglądarce, która wywołuje aplikację interfejsu API sieci web zaplecza w celu wykonania logiki biznesowej, jego. Dla aplikacji jednostronicowych hostowanej w usłudze Azure AD przyznawanie niejawne protokołu OAuth 2.0 służy do uwierzytelniania użytkownika w usłudze Azure AD i uzyskania tokenu, który służy do zabezpieczania wywołań z aplikacji JavaScript klienta do jego zaplecza interfejsu API sieci web. 

Po użytkownik udzielił zgody, tego samego protokołu uwierzytelniania mogą służyć do uzyskiwania tokenów, aby zabezpieczyć połączenia między klientem i innymi aplikacjami internetowymi zasobów interfejsu API skonfigurowane dla aplikacji. Aby dowiedzieć się więcej o niejawnym przyznawaniu autoryzacji i pomóc w podjęciu decyzji, czy jest odpowiedni dla scenariusza aplikacji, zobacz [zrozumienie OAuth2 niejawne udzielić przepływ w usłudze Azure Active Directory](active-directory-dev-understanding-oauth2-implicit-grant.md).

Domyślnie przyznawanie niejawne protokołu OAuth 2.0 jest wyłączone dla aplikacji. Przyznawanie niejawne protokołu OAuth 2.0 możesz włączyć dla aplikacji, ustawiając `oauth2AllowImplicitFlow` wartości w jego [manifest aplikacji](active-directory-application-manifest.md).

#### <a name="to-enable-oauth-20-implicit-grant"></a>Aby włączyć przyznawanie niejawne protokołu OAuth 2.0

> [!NOTE]
> Aby szczegółowe informacje na temat Edytuj manifest aplikacji, pamiętaj najpierw zapoznać się z poprzedniej sekcji [konfigurowania aplikacji zasobów, aby udostępnić interfejsy API sieci web](#configuring-a-resource-application-to-expose-web-apis).
>

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Jeśli dzierżawa udostępnia swoje konto, dostęp do więcej niż jeden, kliknij konto w prawym górnym rogu i ustaw sesję portalu do żądanej usługi Azure AD.
3. W okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** usługi, kliknij przycisk **rejestracje aplikacji**, następnie Znajdź/kliknij aplikację, którą chcesz skonfigurować. Nastąpi przekierowanie do strony głównej rejestracji aplikacji, która otwiera **ustawienia** strony aplikacji.
4. Przełącz się do **Edytuj manifest** strony, klikając **manifestu** na stronie rejestracji aplikacji. Zostanie otwarty Edytor manifestu z opartej na sieci web, umożliwiając **Edytuj** manifest w portalu. Znajdź, a następnie ustaw wartość "oauth2AllowImplicitFlow" na wartość "true". Domyślnie jest ustawiona na "false".
   
  ```json
  "oauth2AllowImplicitFlow": true,
  ```
5. Zapisz zaktualizowany manifest. Po zapisaniu interfejsu API sieci web jest teraz skonfigurowane na potrzeby uwierzytelniania użytkowników, przyznawanie niejawne protokołu OAuth 2.0.

## <a name="removing-an-application"></a>Usuwanie aplikacji
W tej sekcji opisano sposób usuwania rejestracji aplikacji z dzierżawą usługi Azure AD.

### <a name="removing-an-application-authored-by-your-organization"></a>Usuwanie aplikacji utworzone przez Twoją organizację
Aplikacje, które Twoja organizacja zarejestrowała są wyświetlane w polu filtru "Moje aplikacje" na stronie głównej "rejestracje aplikacji" Twojej dzierżawy. Te aplikacje są te, które możesz ręcznie zarejestrować w witrynie Azure portal lub programowo przy użyciu programu PowerShell lub interfejsu API programu Graph. Dokładniej mówiąc są one reprezentowane przez zarówno obiekt aplikacji i jednostki usługi w dzierżawie. Aby uzyskać więcej informacji, zobacz [obiekty aplikacji i jednostki usługi](active-directory-application-objects.md).

#### <a name="to-remove-a-single-tenant-application-from-your-directory"></a>Aby usunąć aplikację pojedynczej dzierżawy z katalogiem
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Jeśli dzierżawa udostępnia swoje konto, dostęp do więcej niż jeden, kliknij konto w prawym górnym rogu i ustaw sesję portalu do żądanej usługi Azure AD.
3. W okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** usługi, kliknij przycisk **rejestracje aplikacji**, następnie Znajdź/kliknij aplikację, którą chcesz skonfigurować. Nastąpi przekierowanie do strony głównej rejestracji aplikacji, która otwiera **ustawienia** strony aplikacji.
4. Na stronie głównej rejestracji aplikacji, kliknij **Usuń**.
5. Kliknij przycisk **tak** w komunikacie potwierdzenia.

#### <a name="to-remove-a-multi-tenant-application-from-its-home-directory"></a>Aby usunąć aplikację wielodostępną z jego katalogu macierzystego
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Jeśli dzierżawa udostępnia swoje konto, dostęp do więcej niż jeden, kliknij konto w prawym górnym rogu i ustaw sesję portalu do żądanej usługi Azure AD.
3. W okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** usługi, kliknij przycisk **rejestracje aplikacji**, następnie Znajdź/kliknij aplikację, którą chcesz skonfigurować. Nastąpi przekierowanie do strony głównej rejestracji aplikacji, która otwiera **ustawienia** strony aplikacji.
4. Z **ustawienia** wybierz **właściwości** i zmień **wielodostępnych** przełączyć się do **nie**, aby najpierw zmienić aplikację w usłudze pojedynczej dzierżawy, następnie kliknij przycisk **Zapisz**. Obiektów nazw głównych usług aplikacji pozostają w dzierżawcy już wyrażono zgodę na jego posiadać wszystkie organizacje.
5. Kliknij pozycję **Usuń** przycisk strony głównej rejestracji aplikacji.
6. Kliknij przycisk **tak** w komunikacie potwierdzenia.

### <a name="removing-a-multi-tenant-application-authorized-by-another-organization"></a>Usuwanie aplikacji z wieloma dzierżawami, autoryzowany przez inną organizację
Część aplikacji, które są wyświetlane w polu filtru "Wszystkie aplikacje" (bez rejestracji "Moje aplikacje") na stronie głównej "Rejestracje aplikacji" Twojej dzierżawy są wielodostępne aplikacje. Pod względem technicznym te aplikacje wielodostępne pochodzą z innej dzierżawy i zostały zarejestrowane do dzierżawy podczas wyrażania zgody. Dokładniej mówiąc są one reprezentowane przez tylko obiektu jednostki usługi w swojej dzierżawie, bez odpowiedniego obiektu aplikacji. Aby uzyskać więcej informacji na temat różnic między aplikacją i obiektów nazw głównych usług, zobacz [aplikacji i obiektów nazw głównych usług w usłudze Azure AD](active-directory-application-objects.md).

Aby usunąć aplikację wielodostępną dostępu do katalogu (po o uzyska zatwierdzenie), administrator firmy, należy usunąć jego nazwy głównej usługi. Administrator musi mieć dostęp administratora globalnego i można go usunąć za pomocą witryny Azure portal lub użyć [poleceń cmdlet programu Azure AD PowerShell](http://go.microsoft.com/fwlink/?LinkId=294151).

## <a name="next-steps"></a>Kolejne kroki
- Aby uzyskać więcej informacji na temat działania uwierzytelniania w usłudze Azure AD, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](active-directory-authentication-scenarios.md).
- Zobacz [znakowania wytyczne dotyczące zintegrowanych aplikacji](active-directory-branding-guidelines.md) zawiera porady na temat visual wskazówki dotyczące aplikacji.
- Aby uzyskać więcej informacji na temat relacji między obiekty aplikacji i nazwy głównej usługi aplikacji, zobacz [obiekty aplikacji i jednostki usługi](active-directory-application-objects.md).
- Aby dowiedzieć się więcej o roli odtwarzania manifestu aplikacji, zobacz [opis manifestu aplikacji usługi Azure Active Directory](active-directory-application-manifest.md)
- Zobacz [słownik deweloperów usługi Azure AD](active-directory-dev-glossary.md) uzyskać definicje niektórych pojęcia dla deweloperów core usługi Azure AD.
- Odwiedź stronę [przewodnik dewelopera usługi Active Directory](azure-ad-developers-guide.md) omówienie całą zawartość związane z programowaniem.

