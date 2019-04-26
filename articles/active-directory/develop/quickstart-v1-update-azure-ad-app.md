---
title: Integrowanie aplikacji za pomocą usługi Azure Active Directory
description: Dowiedz się, jak zaktualizować aplikację w usłudze Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: bee16ed8205453546702946628c98c73b0f34b15
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60299164"
---
# <a name="quickstart-update-an-application-in-azure-active-directory"></a>Szybki start: aktualizowanie aplikacji w usłudze Azure Active Directory

Deweloperzy w przedsiębiorstwach oraz dostawcy oprogramowania jako usługi (SaaS), którzy zarejestrowali aplikacje w usłudze Azure Active Directory (Azure AD), mogą musieć skonfigurować aplikacje na potrzeby uzyskiwania dostępu do innych zasobów, takich jak internetowe interfejsy API, udostępniania ich w innych organizacjach i nie tylko.

W tym przewodniku Szybki start poznasz różne sposoby konfigurowania lub aktualizowania aplikacji w celu spełnienia Twoich wymagań oraz wymagań innych organizacji.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy upewnij się, że zostały wykonane następujące czynności:

* Zapoznanie się z omówieniem [platformy wyrażania zgody w usłudze Azure AD](consent-framework.md), ponieważ jej zrozumienie jest istotne podczas tworzenia aplikacji, które mają być używane przez innych użytkowników lub aplikacje.
* Posiadanie dzierżawy usługi Azure AD z zarejestrowanymi w niej aplikacjami.
  * Jeśli nie masz jeszcze dzierżawy, [dowiedz się, jak ją uzyskać](quickstart-create-new-tenant.md).
  * Jeśli nie masz aplikacji zarejestrowanych w Twojej dzierżawie, [dowiedz się, jak dodać aplikację do usługi Azure AD](quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="configure-a-client-application-to-access-web-apis"></a>Konfigurowanie aplikacji klienckiej na potrzeby uzyskiwania dostępu do internetowych interfejsów API

Aby aplikacje klienckie przeznaczone dla Internetu lub aplikacje poufne mogły uczestniczyć w przepływie przydziałów autoryzacji, który wymaga uwierzytelniania (i uzyskania tokenu dostępu), muszą ustanowić bezpieczne poświadczenia. Domyślną metodą uwierzytelniania obsługiwaną w witrynie Azure Portal jest użycie identyfikatora klienta i klucza tajnego. W tej sekcji opisano czynności konfiguracyjne wymagane w celu udostępnienia klucza tajnego przy użyciu poświadczeń klienta.

Zanim klient będzie mógł uzyskać dostęp do internetowego interfejsu API uwidocznionego przez aplikację zasobów (np. interfejsu API programu Microsoft Graph), platforma wyrażania zgody zapewnia, że klient uzyskuje udzielenie wymaganych uprawnień na podstawie żądanych uprawnień. Domyślnie wszystkie aplikacje mogą wybierać uprawnienia z usługi **Azure Active Directory** (interfejs API programu Graph) i klasycznego modelu wdrażania platformy Azure. [Uprawnienie „Loguj się i odczytuj profil użytkownika” interfejsu API programu Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes#PermissionScopeDetails) jest również domyślnie zaznaczone. Jeśli Twój klient jest rejestrowany w dzierżawie, która ma konta z subskrypcją usługi Office 365, do wyboru są dostępne internetowe interfejsy API i uprawnienia dla programu SharePoint i usługi Exchange Online. Dla każdego żądanego internetowego interfejsu API możesz wybrać spośród dwóch rodzajów uprawnień:

- Uprawnienia aplikacji: twoja aplikacja kliencka musi mieć bezpośredni dostęp do internetowego interfejsu API w swoim imieniu (bez kontekstu użytkownika). Tego rodzaju uprawnienia wymagają zgody administratora i nie są dostępne dla natywnych aplikacji klienckich.
- Uprawnienia delegowane: twoja aplikacja kliencka musi mieć dostęp do internetowego interfejsu API w imieniu zalogowanego użytkownika, ale z dostępem ograniczonym przez wybrane uprawnienie. Tego rodzaju uprawnienie może być udzielane przez użytkownika, chyba że wymaga ono zgody administratora.

  > [!NOTE]
  > Dodanie uprawnienia delegowanego do aplikacji nie powoduje automatycznego udzielenia zgody dla użytkowników w dzierżawie. Użytkownicy muszą nadal ręcznie wyrażać zgodę na dodane uprawnienia delegowane w czasie wykonywania, chyba że administrator wyrazi zgodę w imieniu wszystkich użytkowników.

### <a name="add-application-credentials-or-permissions-to-access-web-apis"></a>Dodawanie poświadczeń lub uprawnień aplikacji na potrzeby uzyskiwania dostępu do internetowych interfejsów API

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
3. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory**, wybierz pozycję **Rejestracje aplikacji**, a następnie znajdź lub wybierz aplikację, którą chcesz skonfigurować.

   ![Aktualizowanie rejestracji aplikacji](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration.png)

4. Nastąpi przekierowanie do głównej strony rejestracji aplikacji, która zawiera stronę **Ustawienia** aplikacji. Aby dodać poświadczenia dla aplikacji internetowej, wykonaj następujące czynności:
   1. Wybierz sekcję **Klucze** na stronie **Ustawienia**.
   1. Aby dodać certyfikat, wykonaj następujące czynności:
      - Wybierz pozycję **Przekaż klucz publiczny**.
      - Wybierz plik, który chcesz przekazać. Plik musi być plikiem typu cer, pem lub crt.
   1. Aby dodać hasło, wykonaj poniższe kroki:
      - Dodaj opis klucza.
      - Wybierz czas trwania.
      - Wybierz pozycję **Zapisz**. Po zapisaniu zmian w konfiguracji kolumna najdalej z prawej strony będzie zawierać wartość klucza. **Pamiętaj o skopiowaniu klucza** do użycia w kodzie aplikacji klienckiej, ponieważ nie będzie on dostępny po opuszczeniu tej strony.

5. Dodawanie uprawnień na potrzeby uzyskiwania dostępu do interfejsów API zasobu z poziomu klienta
   1. Wybierz sekcję **Wymagane uprawnienia** na stronie **Ustawienia**, a następnie wybierz pozycję **Dodaj**.
   1. Wybierz pozycję **Wybierz interfejs API**, aby określić typ zasobów, z których ma zostać dokonany wybór.
   1. Przejrzyj listę dostępnych interfejsów API lub użyj pola wyszukiwania, aby wybrać z dostępnych aplikacji zasobów w katalogu, który uwidocznia internetowy interfejs API. Zaznacz interesujący Cię zasób, a następnie kliknij pozycję **Wybierz**.
   1. Na stronie **Włączanie dostępu** wybierz uprawnienia aplikacji i/lub uprawnienia delegowane wymagane przez aplikację podczas uzyskiwania dostępu do interfejsu API.
   
   ![Aktualizowanie rejestracji aplikacji — interfejs API uprawnień](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-permissions-api.png)

   ![Aktualizowanie rejestracji aplikacji — uprawnienia](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-permissions-perms.png)

6. Po zakończeniu wybierz przycisk **Wybierz** znajdujący się na stronie **Włączanie dostępu**, a następnie przycisk **Gotowe** na stronie **Dodaj dostęp do interfejsu API**. Nastąpi powrót na stronę **Wymagane uprawnienia**, na której nowy zasób zostanie dodany do listy interfejsów API.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Konfigurowanie aplikacji zasobów na potrzeby uwidoczniania internetowych interfejsów API

Opracowane internetowe interfejsy API można udostępniać aplikacjom klienckim przez uwidocznianie [zakresów](developer-glossary.md#scopes) i [ról](developer-glossary.md#roles) dostępu. Prawidłowo skonfigurowany internetowy interfejs API jest udostępniany podobnie jak inne internetowe interfejsy API firmy Microsoft, w tym interfejs API programu Graph i interfejsy API usługi Office 365. Zakresy i role dostępu są uwidoczniane za pośrednictwem [manifestu aplikacji](developer-glossary.md#application-manifest), czyli pliku JSON, który reprezentuje konfigurację tożsamości aplikacji.

W poniższej sekcji pokazano, jak uwidocznić zakresy dostępu, modyfikując manifest aplikacji zasobów.

### <a name="add-access-scopes-to-your-resource-application"></a>Dodawanie zakresów dostępu do aplikacji zasobów

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
3. W okienku nawigacji po lewej stronie wybierz pozycję **Azure Active Directory > Rejestracje aplikacji**, a następnie znajdź lub wybierz aplikację, którą chcesz skonfigurować.

   ![Aktualizowanie rejestracji aplikacji](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration.png)

4. Nastąpi przekierowanie do głównej strony rejestracji aplikacji, która otwiera stronę **Ustawienia** aplikacji. Przełącz się do strony **Edytowanie manifestu**, klikając pozycję **Manifest** na stronie rejestracji aplikacji. Zostanie otwarty internetowy edytor manifestu umożliwiający **edycję** manifest w obrębie portalu. Opcjonalnie można kliknąć pozycję **Pobierz** i edytować manifest lokalnie, a następnie użyć pozycji **Przekaż** w celu ponownego zastosowania go do aplikacji.
5. W tym przykładzie uwidocznimy nowy zakres o nazwie `Employees.Read.All` w naszym zasobie/interfejsie API, dodając następujący element JSON do kolekcji `oauth2Permissions`. Istniejący zakres `user_impersonation` jest domyślnie udostępniany podczas rejestracji. Zakres `user_impersonation` umożliwia aplikacji klienckiej zażądanie uprawnienia dostępu do zasobu w imieniu tożsamości zalogowanego użytkownika. Pamiętaj o dodaniu przecinka po istniejącym elemencie zakresu `user_impersonation` i o zmianie wartości właściwości zgodnie z potrzebami zasobu. 

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
   > Wartość `id` musi zostać wygenerowana programowo lub za pomocą narzędzia do generowania identyfikatora GUID, takiego jak [guidgen](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx). Wartość `id` reprezentuje unikatowy identyfikator dla zakresu w formie uwidocznionej przez internetowy interfejs API. Po odpowiednim skonfigurowaniu klienta z uprawnieniami dostępu do internetowego interfejsu API usługa Azure AD wystawi token dostępu OAuth 2.0. Gdy klient wywołuje internetowy interfejs API, prezentuje on token dostępu, który ma oświadczenie zakresu (scp) ustawione na uprawnienia żądane w jego rejestracji aplikacji.
   >
   > Jeśli jest to konieczne, później można uwidocznić dodatkowe zakresy. Należy pamiętać, że internetowy interfejs API może uwidoczniać wiele zakresów powiązanych z różnymi funkcjami. Zasób może kontrolować dostęp do internetowego interfejsu API w czasie wykonywania, oceniając oświadczenia zakresu (`scp`) w odebranym tokenie dostępu OAuth 2.0.

6. Po skończeniu kliknij przycisk **Zapisz**. Teraz internetowy interfejs API jest skonfigurowany do użycia przez inne aplikacje w katalogu.

   ![Aktualizowanie rejestracji aplikacji](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-manifest.png)

### <a name="verify-the-web-api-is-exposed-to-other-applications-in-your-tenant"></a>Weryfikowanie, czy internetowy interfejs API jest uwidoczniany dla innych aplikacji w dzierżawie

1. Wróć do swojej dzierżawy usługi Azure AD, wybierz ponownie pozycję **Rejestracje aplikacji**, a następnie znajdź lub wybierz aplikację kliencką, którą chcesz skonfigurować.

   ![Aktualizowanie rejestracji aplikacji](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration.png)

2. Powtórz krok 5 opisany w sekcji [Konfigurowanie aplikacji klienckiej na potrzeby uzyskiwania dostępu do internetowych interfejsów API](#configure-a-client-application-to-access-web-apis). Po dojściu do kroku **Wybierz interfejs API** wyszukaj zasób, wprowadzając nazwę jego aplikacji w polu wyszukiwania, i kliknij przycisk **Wybierz**.

3. Na stronie **Włączanie dostępu** powinien zostać wyświetlony nowy zakres dostępny dla żądań uprawnień klienta.

   ![Nowe uprawnienia są wyświetlane](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-permissions-perms-newscopes.png)

### <a name="more-on-the-application-manifest"></a>Więcej informacji na temat manifestu aplikacji

Manifest aplikacji pełni rolę mechanizmu aktualizowania jednostki Application, która definiuje wszystkie atrybuty konfiguracji tożsamości aplikacji usługi Azure AD, w tym opisane wcześniej zakresy dostępu do interfejsu API. Aby uzyskać więcej informacji na temat jednostki Application i jej schematu, zobacz [dokumentację jednostki Application interfejsu API programu Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity). Ten artykuł zawiera wyczerpujące informacje na temat elementów członkowskich jednostki Application używanych do określania uprawnień dla interfejsu API, w tym następujących elementów:  

- Element członkowski appRoles, który jest kolekcją jednostek [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type). Jest on używany do definiowania [uprawnień aplikacji](developer-glossary.md#permissions) dla internetowego interfejsu API. 
- Element członkowski oauth2Permissions, który jest kolekcją jednostek [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type). Jest on używany do definiowania [uprawnień delegowanych](developer-glossary.md#permissions) dla internetowego interfejsu API.

Aby uzyskać więcej informacji o ogólnych pojęciach dotyczących manifestu aplikacji, zobacz [Manifest aplikacji usługi Azure AD](reference-app-manifest.md).

## <a name="accessing-the-azure-ad-graph-and-office-365-via-microsoft-graph-apis"></a>Uzyskiwanie dostępu do funkcji Azure AD Graph i usługi Office 365 za pośrednictwem interfejsów API programu Microsoft Graph  

Jak wspomniano wcześniej, oprócz uwidoczniania/uzyskiwania dostępu do interfejsów API na potrzeby własnych aplikacji, można zarejestrować aplikację kliencką na potrzeby dostępu do interfejsów API uwidocznionych przez zasoby firmy Microsoft. Interfejs API programu Microsoft Graph, określany jako „Microsoft Graph” na liście zasobów/interfejsów API portalu, jest dostępny dla wszystkich aplikacji, które są zarejestrowane w usłudze Azure AD. W przypadku rejestracji aplikacji klienckiej w dzierżawie zawierającej konta, które zostały zarejestrowane w celu uzyskania subskrypcji usługi Office 365, można także uzyskać dostęp do zakresów uwidocznianych przez różne zasoby usługi Office 365.

Wyczerpujące omówienie zakresów uwidocznianych przez interfejs API programu Microsoft Graph można znaleźć w artykule [Microsoft Graph permissions reference](https://docs.microsoft.com/graph/permissions-reference) (Dokumentacja uprawnień programu Microsoft Graph).

> [!NOTE]
> Ze względu na aktualne ograniczenie natywne aplikacje klienckie mogą wywoływać interfejs API programu Graph usługi Azure AD tylko wtedy, gdy korzystają z uprawnienia „Dostęp do katalogu organizacji”. To ograniczenie nie dotyczy aplikacji internetowych.

## <a name="configuring-multi-tenant-applications"></a>Konfigurowanie aplikacji wielodostępnych

Podczas rejestrowania aplikacji w usłudze Azure AD możesz chcieć, aby aplikacja była dostępna tylko dla użytkowników w Twojej organizacji. Alternatywnie możesz również chcieć, aby aplikacja była dostępna dla użytkowników w zewnętrznych organizacjach. Te dwa rodzaje aplikacji są nazywane aplikacjami pojedynczej dzierżawy i aplikacjami wielodostępnymi. W tej sekcji omówiono sposób modyfikowania konfiguracji aplikacji pojedynczej dzierżawy, aby stała się aplikacją wielodostępną.

Warto zwrócić uwagę na różnice między aplikacjami pojedynczej dzierżawy i aplikacjami wielodostępnymi:  

- Aplikacja pojedynczej dzierżawy jest przeznaczona do użytku w jednej organizacji. Jest to zwykle aplikacja biznesowa (LoB) napisana przez dewelopera w przedsiębiorstwie. Dostęp do aplikacji pojedynczej dzierżawy może być uzyskiwany przez użytkowników mających konta w tej samej dzierżawie co rejestracja aplikacji. W rezultacie musi ona być aprowizowana tylko w jednym katalogu.
- Aplikacja wielodostępna jest przeznaczona do użytku w wielu organizacjach. Jest ona określana jako aplikacja internetowa typu oprogramowanie jako usługa (SaaS) i jest zwykle napisana przez niezależnego dostawcę oprogramowania (ISV). Aplikacje wielodostępne muszą być aprowizowane w każdej dzierżawie, do której użytkownicy potrzebują dostępu. W dzierżawach innych niż ta, w której aplikacja jest zarejestrowana, na jej zarejestrowanie wymagana jest zgoda użytkownika lub administratora. Należy pamiętać, że natywne aplikacje klienckie są domyślnie wielodostępne, ponieważ są instalowane na urządzeniu właściciela zasobu. Zobacz poprzednią sekcję Omówienie platformy wyrażania zgody, aby uzyskać szczegółowe informacje na temat platformy wyrażania zgody.

Przekształcenie aplikacji w wielodostępną wymaga zarówno zmian rejestracji aplikacji, jak i zmian w samej aplikacji internetowej. W poniższych sekcjach opisano oba te procesy.

### <a name="changing-the-application-registration-to-support-multi-tenant"></a>Zmiana rejestrowania aplikacji do obsługi wielodostępności

Jeśli piszesz aplikację, którą ma być dostępna dla klientów lub partnerów spoza organizacji, musisz zaktualizować definicję aplikacji w witrynie Azure Portal.

> [!IMPORTANT]
> Usługa Azure AD wymaga, aby identyfikator URI identyfikatora aplikacji dla aplikacji wielodostępnych był globalnie unikatowy. Identyfikator URI identyfikatora aplikacji jest jednym ze sposobów, w jaki aplikacja jest identyfikowana w komunikatach protokołu. W przypadku aplikacji pojedynczej dzierżawy wystarczy, aby identyfikator URI identyfikatora aplikacji był unikatowy w obrębie tej dzierżawy. W przypadku aplikacji wielodostępnej ten identyfikator musi być globalnie unikatowy, dzięki czemu usługa Azure AD będzie mogła znaleźć aplikację we wszystkich dzierżawach.
> Globalna unikatowość jest wymuszana poprzez wymaganie, aby identyfikator URI identyfikatora aplikacji miał nazwę hosta, która jest zgodna ze zweryfikowaną domeną dzierżawy usługi Azure AD. Jeśli na przykład nazwą dzierżawy jest contoso.onmicrosoft.com, prawidłowy identyfikator URI identyfikatora aplikacji będzie miał postać https://contoso.onmicrosoft.com/myapp. Jeśli Twoja dzierżawa ma zweryfikowaną domenę contoso.com, prawidłowy identyfikator URI identyfikatora aplikacji będzie również miał postać https://contoso.com/myapp. Jeśli identyfikator URI identyfikatora aplikacji nie jest zgodny z tym wzorcem, ustawienie aplikacji jako aplikacji wielodostępnej zakończy się niepowodzeniem.

Aby umożliwić użytkownikom zewnętrznym dostęp do aplikacji, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, kliknij konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
3. W okienku nawigacji po lewej stronie kliknij usługę **Azure Active Directory**, kliknij pozycję **Rejestracje aplikacji**, a następnie znajdź lub kliknij aplikację, którą chcesz skonfigurować. Nastąpi przekierowanie do głównej strony rejestracji aplikacji, która otwiera stronę **Ustawienia** aplikacji.
4. Na stronie **Ustawienia** kliknij pozycję **Właściwości** i zmień przełącznik **Z wieloma dzierżawami** na wartość **Tak**.

Po dokonaniu tych zmian użytkownicy i administratorzy w innych organizacjach będą mogli zezwalać swoim użytkownikom na logowanie się do Twojej aplikacji w celu uzyskiwania dostępu do zasobów zabezpieczanych przez ich dzierżawę.

### <a name="changing-the-application-to-support-multi-tenant"></a>Zmiana aplikacji na potrzeby obsługi wielodostępności

Obsługa aplikacji wielodostępnych jest oparta głównie na platformie wyrażania zgody usługi Azure AD. Zgoda to mechanizm, który umożliwia użytkownikowi z innej dzierżawy przyznanie dostępu aplikacji do zasobów zabezpieczonych przez dzierżawę użytkownika. To środowisko jest określane jako „zgoda użytkownika”.

Aplikacja internetowa może także oferować następujące mechanizmy:

- Możliwość „zarejestrowania mojej firmy” przez administratorów. To środowisko, określane jako „zgoda administratora”, daje administratorowi możliwość udzielenia zgody w imieniu *wszystkich użytkowników* w swojej organizacji. Zgodę administratora może udzielić tylko użytkownik uwierzytelniony przy użyciu konta należącego do roli administratora globalnego. W przypadku innych użytkowników zostanie wyświetlony błąd.
- Środowisko rejestrowania się dla użytkowników. Oczekuje się, że użytkownik będzie miał możliwość użycia przycisku „Zarejestruj się”, który przekieruje przeglądarkę do punktu końcowego OAuth2.0 `/authorize` usługi Azure AD lub do punktu końcowego `/userinfo` OpenID Connect. Te punkty końcowe umożliwiają aplikacji uzyskanie informacji na temat nowego użytkownika przez zbadanie elementu id_token. Po fazie rejestracji użytkownikowi zostanie wyświetlony monit o wyrażenie zgody podobny do monitu przedstawionego w sekcji Omówienie platformy wyrażania zgody.

Aby uzyskać więcej informacji na temat zmian aplikacji, które są wymagane do obsługi dostępu z wieloma dzierżawami oraz środowisk logowania/rejestracji, zobacz:

- [How to sign in any Azure Active Directory (AD) user using the multi-tenant application pattern](howto-convert-app-to-be-multi-tenant.md) (Jak zalogować dowolnego użytkownika usługi Azure Active Directory (AD) za pomocą wzorca aplikacji wielodostępnych)
- Lista [przykładów kodu wielodostępnego](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant).
- [Szybki start: dodawanie znakowania firmowego do strony logowania w usłudze Azure AD](../fundamentals/customize-branding.md)

## <a name="enabling-oauth-20-implicit-grant-for-single-page-applications"></a>Włączanie przyznawania niejawnego protokołu OAuth 2.0 dla aplikacji jednostronicowych

Aplikacje jednostronicowe (SPA) mają zwykle utworzony przy użyciu języka JavaScript fronton działający w przeglądarce, który wywołuje internetowy interfejs API aplikacji zaplecza w celu wykonania logiki biznesowej. W przypadku aplikacji jednostronicowych hostowanych w usłudze Azure AD do uwierzytelniania użytkownika w usłudze Azure AD i uzyskiwania tokenu, którego można używać do zabezpieczania wywołań z klienta JavaScript aplikacji do internetowego interfejsu API zaplecza, używane jest przyznawanie niejawne protokołu OAuth 2.0.

Po udzieleniu zgody przez użytkownika tego samego protokołu uwierzytelniania można użyć w celu uzyskania tokenów służących do zabezpieczania wywołań między klientem i innymi zasobami internetowego interfejsu API skonfigurowanymi dla aplikacji. Aby dowiedzieć się więcej o niejawnym przyznawaniu autoryzacji i ułatwić sobie podjęcie decyzji, czy jest to rozwiązanie odpowiednie dla Twojego scenariusza aplikacji, zobacz [Understanding the OAuth2 implicit grant flow in Azure Active Directory](v1-oauth2-implicit-grant-flow.md) (Omówienie przepływu przyznawania niejawnego protokołu OAuth2 w usłudze Azure Active Directory).

Domyślnie przyznawanie niejawne protokołu OAuth 2.0 jest wyłączone dla aplikacji. Przyznawanie niejawne protokołu OAuth 2.0 można włączyć dla aplikacji, ustawiając wartość `oauth2AllowImplicitFlow` w [manifeście aplikacji](reference-app-manifest.md).

### <a name="to-enable-oauth-20-implicit-grant"></a>Włączanie przyznawania niejawnego protokołu OAuth 2.0

> [!NOTE]
> Aby uzyskać szczegółowe informacje na temat sposobu edytowania manifestu aplikacji, należy się najpierw zapoznać z poprzednią sekcją — [Konfigurowanie aplikacji zasobów na potrzeby uwidoczniania internetowych interfejsów API](#configuring-a-resource-application-to-expose-web-apis).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, kliknij konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
3. W okienku nawigacji po lewej stronie kliknij usługę **Azure Active Directory**, kliknij pozycję **Rejestracje aplikacji**, a następnie znajdź lub kliknij aplikację, którą chcesz skonfigurować. Nastąpi przekierowanie do głównej strony rejestracji aplikacji, która otwiera stronę **Ustawienia** aplikacji.
4. Przełącz się do strony **Edytowanie manifestu**, klikając pozycję **Manifest** na stronie rejestracji aplikacji. Zostanie otwarty internetowy edytor manifestu umożliwiający **edycję** manifest w obrębie portalu. Znajdź i ustaw element „oauth2AllowImplicitFlow” na wartość „true”. Domyślnie jest on ustawiony na wartość „false”.
   
   ```json
   "oauth2AllowImplicitFlow": true,
   ```
5. Zapisz zaktualizowany manifest. Po zapisaniu Twój internetowy interfejs API jest skonfigurowany do korzystania z przyznawania niejawnego protokołu OAuth 2.0 na potrzeby uwierzytelniania użytkowników.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat innych powiązanych podręczników Szybki start dotyczących zarządzania aplikacjami korzystającymi z punktu końcowego usługi Azure AD w wersji 1.0:
- [Add an application to Azure AD](quickstart-v1-integrate-apps-with-azure-ad.md) (Dodawanie aplikacji do usługi Azure AD)
- [Remove an application from Azure AD](quickstart-v1-remove-azure-ad-app.md) (Usuwanie aplikacji z usługi Azure AD)

Aby dowiedzieć się więcej na temat dwóch obiektów usługi Azure AD, które reprezentują zarejestrowaną aplikację i związek między nimi, zobacz [Application objects and service principal objects](app-objects-and-service-principals.md) (Obiekty aplikacji i obiekty jednostek usługi).

Aby dowiedzieć się więcej o wytycznych dotyczących oznaczania marką, które należy stosować podczas opracowywania aplikacji przy użyciu usługi Azure Active Directory, zobacz [Wytyczne dotyczące oznaczania aplikacji marką](howto-add-branding-in-azure-ad-apps.md).
