---
title: 'Samouczek: AuthN/AuthO end-to-end'
description: Dowiedz się, jak używać uwierzytelniania i autoryzacji usługi App Service w celu zabezpieczenia aplikacji usługi App Service, w tym dostępu do zdalnych interfejsów API.
keywords: app service, azure app service, authN, authZ, secure, security, multi-tiered, azure active directory, azure ad
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 08/14/2019
ms.custom: seodec18
ms.openlocfilehash: 46750383c1436a2d053d6db7fed858c7c0f8a9fe
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78226310"
---
# <a name="tutorial-authenticate-and-authorize-users-end-to-end-in-azure-app-service"></a>Samouczek: kompleksowe uwierzytelnianie i autoryzacja użytkowników w usłudze Azure App Service

[Usługa Azure App Service](overview.md) zapewnia wysoce skalowalną, samoładującą się usługę hostingu. Ponadto usługa App Service zapewnia wbudowaną obsługę [uwierzytelniania i autoryzacji użytkowników](overview-authentication-authorization.md). Ten samouczek przedstawia sposób zabezpieczania aplikacji za pomocą uwierzytelniania i autoryzacji usługi App Service. W samouczku użyto aplikacji ASP.NET Core z frontonem Angular.js, ale jest to tylko przykład. Uwierzytelnianie i autoryzacja usługi App Service obsługuje wszystkie środowiska uruchomieniowe języków, a wykonując kroki samouczka, można dowiedzieć się, jak zastosować je w przypadku preferowanego języka.

Samouczek korzysta z aplikacji przykładowej w celu pokazania, jak zabezpieczyć aplikację samodzielną (w sekcji [Włączanie uwierzytelniania i autoryzacji w aplikacji zaplecza](#enable-authentication-and-authorization-for-back-end-app)).

![Proste uwierzytelnianie i autoryzacja](./media/app-service-web-tutorial-auth-aad/simple-auth.png)

Samouczek przedstawia również, jak zabezpieczyć aplikację wielowarstwową przez uzyskanie dostępu do zabezpieczonego interfejsu API zaplecza w imieniu uwierzytelnionego użytkownika, zarówno z poziomu [kodu serwera](#call-api-securely-from-server-code), jak i [kodu przeglądarki](#call-api-securely-from-browser-code).

![Zaawansowane uwierzytelnianie i autoryzacja](./media/app-service-web-tutorial-auth-aad/advanced-auth.png)

To tylko niektóre z możliwych scenariuszy uwierzytelniania i autoryzacji w usłudze App Service. 

Oto pełniejsza lista tego, czego nauczysz się w ramach samouczka:

> [!div class="checklist"]
> * Włączanie wbudowanego uwierzytelniania i autoryzacji
> * Zabezpieczanie aplikacji przed nieuwierzytelnionymi żądaniami
> * Używanie usługi Azure Active Directory jako dostawcy tożsamości
> * Uzyskiwanie dostępu do zdalnej aplikacji w imieniu zalogowanego użytkownika
> * Zabezpieczanie wywołań między usługami, korzystając z uwierzytelniania przy użyciu tokenów
> * Używanie tokenów dostępu z poziomu kodu serwera
> * Używanie tokenów dostępu z poziomu kodu klienta (przeglądarki)

Kroki opisane w tym samouczku można wykonać w systemie macOS, Linux i Windows.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

* [Zainstaluj Git](https://git-scm.com/).
* [Zainstaluj program .NET Core](https://www.microsoft.com/net/core/).

## <a name="create-local-net-core-app"></a>Tworzenie lokalnej aplikacji .NET Core

Ten krok umożliwia skonfigurowanie lokalnego projektu platformy .NET Core. Ten sam projekt zostanie użyty do wdrożenia aplikacji interfejsu API zaplecza i aplikacji internetowej frontonu.

### <a name="clone-and-run-the-sample-application"></a>Klonowanie i uruchamianie aplikacji przykładowej

Uruchom następujące polecenia w celu sklonowania przykładowego repozytorium i uruchomienia go.

```bash
git clone https://github.com/Azure-Samples/dotnet-core-api
cd dotnet-core-api
dotnet run
```

Przejdź na adres `http://localhost:5000` i spróbuj dodać, edytować i usunąć zadania do wykonania. 

![Interfejs API platformy ASP.NET Core uruchomiony lokalnie](./media/app-service-web-tutorial-auth-aad/local-run.png)

Aby zatrzymać platformę ASP.NET Core w dowolnym momencie, naciśnij kombinację klawiszy `Ctrl+C` w terminalu.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-apps-to-azure"></a>Wdrażanie aplikacji na platformie Azure

W tym kroku wdrożysz projekt w dwóch aplikacjach usługi App Service. Jedna aplikacja to aplikacja frontonu, a druga — zaplecza.

### <a name="configure-a-deployment-user"></a>Konfigurowanie użytkownika wdrożenia

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-azure-resources"></a>Tworzenie zasobów platformy Azure

W usłudze Cloud Shell uruchom następujące polecenia, aby utworzyć dwie aplikacje internetowe. Zastąp `0-9` `-` _ \<nazwę aplikacji front-end>_ i _ \<>nazw aplikacji zaplecza_ dwiema unikatowymi nazwami `a-z`aplikacji globalnie (prawidłowe znaki to , i ). Aby uzyskać więcej informacji na temat każdego polecenia, zobacz [Interfejs API RESTful z mechanizmem CORS w usłudze Azure App Service](app-service-web-tutorial-rest-api.md).

```azurecli-interactive
az group create --name myAuthResourceGroup --location "West Europe"
az appservice plan create --name myAuthAppServicePlan --resource-group myAuthResourceGroup --sku FREE
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <front-end-app-name> --deployment-local-git --query deploymentLocalGitUrl
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <back-end-app-name> --deployment-local-git --query deploymentLocalGitUrl
```

> [!NOTE]
> Zapisz adresy URL pilotów Git dla aplikacji front-end i back-end, `az webapp create`które są wyświetlane na wyjściu z .
>

### <a name="push-to-azure-from-git"></a>Wypychanie z narzędzia Git na platformę Azure

Wróć do _lokalnego okna terminala_ i uruchom następujące polecenia Git, aby wdrożyć w aplikacji zaplecza. Zastąp _ \<deploymentLocalGitUrl-of-back-end-app>_ adresem URL pilota Git zapisanego z [zasobów platformy Azure](#create-azure-resources). Po wyświetleniu monitu o poświadczenia przez Menedżera poświadczeń Git, upewnij się, że wprowadzono [poświadczenia wdrożenia,](deploy-configure-credentials.md)a nie poświadczenia używane do logowania się do witryny Azure portal.

```bash
git remote add backend <deploymentLocalGitUrl-of-back-end-app>
git push backend master
```

W lokalnym oknie terminala uruchom następujące polecenia Git, aby wdrożyć ten sam kod w aplikacji frontonu. Zastąp _ \<deploymentLocalGitUrl-of-front-end-app>_ adres url pilota Git zapisanego z [zasobów platformy Azure](#create-azure-resources).

```bash
git remote add frontend <deploymentLocalGitUrl-of-front-end-app>
git push frontend master
```

### <a name="browse-to-the-apps"></a>Przechodzenie do aplikacji

Przejdź na następujące adresy URL w przeglądarce i zobacz, że te dwie aplikacje działają.

```
http://<back-end-app-name>.azurewebsites.net
http://<front-end-app-name>.azurewebsites.net
```

![Interfejs API platformy ASP.NET Core uruchomiony w usłudze Azure App Service](./media/app-service-web-tutorial-auth-aad/azure-run.png)

> [!NOTE]
> Jeśli aplikacja zostanie ponownie uruchomiona, możesz zauważyć, że nowe dane zostały usunięte. To zachowanie jest oczekiwane, ponieważ przykładowa aplikacja platformy ASP.NET Core używa bazy danych w pamięci.
>
>

## <a name="call-back-end-api-from-front-end"></a>Wywoływanie interfejsu API zaplecza z frontonu

W tym kroku zmienisz kod serwera aplikacji frontonu w celu uzyskiwania dostępu do interfejsu API zaplecza. Później włączysz dostęp uwierzytelniony z frontonu do zaplecza.

### <a name="modify-front-end-code"></a>Modyfikowanie kodu frontonu

W repozytorium lokalnym otwórz plik _Controllers/TodoController.cs_. Na początku `TodoController` klasy dodaj następujące wiersze i zastąp _ \<nazwę zaplecza aplikacji>_ nazwą aplikacji zaplecza:

```cs
private static readonly HttpClient _client = new HttpClient();
private static readonly string _remoteUrl = "https://<back-end-app-name>.azurewebsites.net";
```

Znajdź metodę `GetAll()` i zastąp kod wewnątrz nawiasów klamrowych następującym kodem:

```cs
var data = _client.GetStringAsync($"{_remoteUrl}/api/Todo").Result;
return JsonConvert.DeserializeObject<List<TodoItem>>(data);
```

Pierwszy wiersz wykonuje wywołanie `GET /api/Todo` względem aplikacji interfejsu API zaplecza.

Następnie znajdź metodę `GetById(long id)` i zastąp kod wewnątrz nawiasów klamrowych następującym kodem:

```cs
var data = _client.GetStringAsync($"{_remoteUrl}/api/Todo/{id}").Result;
return Content(data, "application/json");
```

Pierwszy wiersz wykonuje wywołanie `GET /api/Todo/{id}` względem aplikacji interfejsu API zaplecza.

Następnie znajdź metodę `Create([FromBody] TodoItem item)` i zastąp kod wewnątrz nawiasów klamrowych następującym kodem:

```cs
var response = _client.PostAsJsonAsync($"{_remoteUrl}/api/Todo", item).Result;
var data = response.Content.ReadAsStringAsync().Result;
return Content(data, "application/json");
```

Pierwszy wiersz wykonuje wywołanie `POST /api/Todo` względem aplikacji interfejsu API zaplecza.

Następnie znajdź metodę `Update(long id, [FromBody] TodoItem item)` i zastąp kod wewnątrz nawiasów klamrowych następującym kodem:

```cs
var res = _client.PutAsJsonAsync($"{_remoteUrl}/api/Todo/{id}", item).Result;
return new NoContentResult();
```

Pierwszy wiersz wykonuje wywołanie `PUT /api/Todo/{id}` względem aplikacji interfejsu API zaplecza.

Następnie znajdź metodę `Delete(long id)` i zastąp kod wewnątrz nawiasów klamrowych następującym kodem:

```cs
var res = _client.DeleteAsync($"{_remoteUrl}/api/Todo/{id}").Result;
return new NoContentResult();
```

Pierwszy wiersz wykonuje wywołanie `DELETE /api/Todo/{id}` względem aplikacji interfejsu API zaplecza.

Zapisz wszystkie zmiany. W lokalnym oknie terminala wdróż zmiany w aplikacji frontonu za pomocą następujących poleceń Git:

```bash
git add .
git commit -m "call back-end API"
git push frontend master
```

### <a name="check-your-changes"></a>Sprawdzanie zmian

Przejdź na adres `http://<front-end-app-name>.azurewebsites.net` i dodaj kilka elementów, np. `from front end 1` i `from front end 2`.

Przejdź na adres `http://<back-end-app-name>.azurewebsites.net`, aby zobaczyć elementy dodane z poziomu aplikacji frontonu. Dodaj również kilka elementów, np. `from back end 1` i `from back end 2`, a następnie odśwież aplikację frontonu, aby zobaczyć, czy zmiany zostały uwzględnione.

![Interfejs API platformy ASP.NET Core uruchomiony w usłudze Azure App Service](./media/app-service-web-tutorial-auth-aad/remote-api-call-run.png)

## <a name="configure-auth"></a>Konfigurowanie uwierzytelniania

W tym kroku włączysz uwierzytelnianie i autoryzację dla dwóch aplikacji. Skonfigurujesz również aplikację frontonu tak, aby generowała token dostępu, którego można używać do uwierzytelnionego wywoływania aplikacji zaplecza.

Jako dostawcy tożsamości użyjesz usługi Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Konfigurowanie uwierzytelniania usługi Azure Active Directory dla aplikacji usługi App Services](configure-authentication-provider-aad.md).

### <a name="enable-authentication-and-authorization-for-back-end-app"></a>Włączanie uwierzytelniania i autoryzacji w aplikacji zaplecza

1. W menu [portalu platformy Azure](https://portal.azure.com) wybierz **grupę zasobów** lub wyszukaj i wybierz grupy *zasobów* z dowolnej strony.

1. W **grupach zasobów**znajdź i wybierz grupę zasobów. W **obszarze Przegląd**wybierz stronę zarządzania aplikacji zaplecza.

   ![Interfejs API platformy ASP.NET Core uruchomiony w usłudze Azure App Service](./media/app-service-web-tutorial-auth-aad/portal-navigate-back-end.png)

1. W menu po lewej stronie aplikacji zaplecza wybierz **pozycję Uwierzytelnianie / Autoryzacja,** a następnie włącz uwierzytelnianie usługi app service, wybierając opcję **Włączone**.

1. Z listy **Akcja do wykonania w przypadku nieuwierzytelnionego żądania** wybierz pozycję **Zaloguj się za pomocą usługi Azure Active Directory**.

1. W obszarze **Dostawcy uwierzytelniania**wybierz pozycję **Usługa Azure Active Directory** 

   ![Interfejs API platformy ASP.NET Core uruchomiony w usłudze Azure App Service](./media/app-service-web-tutorial-auth-aad/configure-auth-back-end.png)

1. Wybierz **express**, a następnie zaakceptuj ustawienia domyślne, aby utworzyć nową aplikację AD i wybierz **przycisk OK**.

1. Na stronie **Uwierzytelnianie / Autoryzacja** wybierz pozycję **Zapisz**.

   Gdy zostanie wyświetlone powiadomienie z komunikatem `Successfully saved the Auth Settings for <back-end-app-name> App`, odśwież stronę.

1. Ponownie wybierz **pozycję Azure Active Directory,** a następnie wybierz **aplikację Usługi Azure AD**.

1. Skopiuj **identyfikator klienta** aplikacji usługi Azure AD do notatnika. Ta wartość będzie potrzebna później.

   ![Interfejs API platformy ASP.NET Core uruchomiony w usłudze Azure App Service](./media/app-service-web-tutorial-auth-aad/get-application-id-back-end.png)

### <a name="enable-authentication-and-authorization-for-front-end-app"></a>Włączanie uwierzytelniania i autoryzacji w aplikacji frontonu

Wykonaj te same kroki dla aplikacji zaplecza, ale pomiń ostatni krok. Nie potrzebujesz identyfikatora klienta dla aplikacji front-end.

Jeśli chcesz, przejdź na adres `http://<front-end-app-name>.azurewebsites.net`. Teraz powinno nastąpić przekierowanie do bezpiecznej strony logowania. Po zalogowaniu nadal nie masz dostępu do danych z aplikacji zaplecza, ponieważ trzeba jeszcze wykonać trzy czynności:

- Udzielenie frontonowi dostępu do zaplecza
- Skonfigurowanie usługi App Service do zwracania tokenu nadającego się do użycia
- Użycie tokenu w kodzie

> [!TIP]
> Jeśli napotkasz błędy i zmienisz konfigurację ustawień uwierzytelniania/autoryzacji aplikacji, tokeny w magazynie tokenów mogą nie zostać ponownie wygenerowane na podstawie nowych ustawień. Aby mieć pewność, że tokeny zostaną ponownie wygenerowane, musisz wylogować się z aplikacji i zalogować się do niej ponownie. Łatwo to zrobić, korzystając z przeglądarki w trybie prywatnym. Po zmianie ustawień w aplikacjach zamknij przeglądarkę i otwórz ją ponownie w trybie prywatnym.

### <a name="grant-front-end-app-access-to-back-end"></a>Udzielanie aplikacji frontonu dostępu do zaplecza

Teraz, po włączeniu uwierzytelniania i autoryzacji dla obu aplikacji, każda z nich jest wspierana przez aplikację usługi AD. W tym kroku nadasz aplikacji frontonu uprawnienia dostępu do zaplecza w imieniu użytkownika. (W praktyce nadasz _aplikacji usługi AD_ frontonu uprawnienia umożliwiające dostęp do _aplikacji usługi AD_ zaplecza w imieniu użytkownika).

1. W menu [portalu Azure](https://portal.azure.com) wybierz pozycję Azure **Active Directory** lub wyszukaj i wybierz *usługę Azure Active Directory* z dowolnej strony.

1. Wybierz **pozycję Rejestracje aplikacji posiadanych** > **przez aplikacje**. Wybierz nazwę aplikacji front-end, a następnie wybierz **pozycję Uprawnienia interfejsu API**.

   ![Interfejs API platformy ASP.NET Core uruchomiony w usłudze Azure App Service](./media/app-service-web-tutorial-auth-aad/add-api-access-front-end.png)

1. Wybierz **pozycję Dodaj uprawnienie**, a następnie wybierz pozycję Nazwa**\<zaplecza aplikacji interfejsów ** **My APIs** > API>.

1. Na stronie **Żądania uprawnień interfejsu API** dla aplikacji zaplecza wybierz pozycję **Delegowane uprawnienia** i **user_impersonation**, a następnie wybierz pozycję **Dodaj uprawnienia**.

   ![Interfejs API platformy ASP.NET Core uruchomiony w usłudze Azure App Service](./media/app-service-web-tutorial-auth-aad/select-permission-front-end.png)

### <a name="configure-app-service-to-return-a-usable-access-token"></a>Konfigurowanie usługi App Service do zwracania nadającego się do użycia tokenu dostępu

Aplikacja front-end ma teraz wymagane uprawnienia dostępu do aplikacji zaplecza jako zalogowany użytkownik. W tym kroku skonfigurujesz uwierzytelnianie i autoryzację usługi App Service, aby zapewnić nadający się do użycia token dostępu na potrzeby uzyskiwania dostępu do zaplecza. W tym kroku potrzebny jest identyfikator klienta zaplecza, który został skopiowany z [włącz uwierzytelnianie i autoryzację dla aplikacji zaplecza](#enable-authentication-and-authorization-for-back-end-app).

Zaloguj się do witryny [Azure Resource Explorer](https://resources.azure.com). U góry strony kliknij pozycję **Odczyt/zapis**, aby włączyć edytowanie zasobów platformy Azure.

![Interfejs API platformy ASP.NET Core uruchomiony w usłudze Azure App Service](./media/app-service-web-tutorial-auth-aad/resources-enable-write.png)

W lewej przeglądarce kliknij **subskrypcje** > **_\<subskrypcji>_**  >  **resourceGroups** > **myAuthResourceGroup** > **providers** > **Microsoft.Web** > **sites** > **_\<front-end-app-name>_**  >  **config** > **authsettings**.

W widoku **authsettings** kliknij pozycję **Edytuj**. Ustaw `additionalLoginParams` następujący ciąg JSON przy użyciu skopiowanego identyfikatora klienta. 

```json
"additionalLoginParams": ["response_type=code id_token","resource=<back-end-client-id>"],
```

![Interfejs API platformy ASP.NET Core uruchomiony w usłudze Azure App Service](./media/app-service-web-tutorial-auth-aad/additional-login-params-front-end.png)

Zapisz ustawienia, klikając pozycję **PUT**.

Aplikacje są teraz skonfigurowane. Fronton może teraz uzyskiwać dostęp do zaplecza za pomocą odpowiedniego tokenu dostępu.

Aby uzyskać informacje na temat konfigurowania tokenu dostępu dla innych dostawców, zobacz [Odśwież tokeny dostawcy tożsamości](app-service-authentication-how-to.md#refresh-identity-provider-tokens).

## <a name="call-api-securely-from-server-code"></a>Bezpieczne wywoływanie interfejsu API z poziomu kodu serwera

W tym kroku umożliwisz wcześniej zmodyfikowanemu kodowi serwera wykonywanie uwierzytelnionych wywołań interfejsu API zaplecza.

Aplikacja front-end ma teraz wymagane uprawnienia, a także dodaje identyfikator klienta zaplecza do parametrów logowania. W związku z tym może uzyskać token dostępu na potrzeby uwierzytelniania w aplikacji zaplecza. Usługa App Service dostarcza ten token do kodu serwera przez wprowadzenie nagłówka `X-MS-TOKEN-AAD-ACCESS-TOKEN` do każdego uwierzytelnionego żądania (zobacz [Pobieranie tokenów w kodzie aplikacji](app-service-authentication-how-to.md#retrieve-tokens-in-app-code)).

> [!NOTE]
> Te nagłówki są wprowadzane dla wszystkich obsługiwanych języków. Dostęp do nich możesz uzyskiwać przy użyciu standardowego wzorca dla każdego języka.

W repozytorium lokalnym ponownie otwórz plik _Controllers/TodoController.cs_. W obszarze konstruktora `TodoController(TodoContext context)` dodaj następujący kod:

```cs
public override void OnActionExecuting(ActionExecutingContext context)
{
    base.OnActionExecuting(context);

    _client.DefaultRequestHeaders.Accept.Clear();
    _client.DefaultRequestHeaders.Authorization =
        new AuthenticationHeaderValue("Bearer", Request.Headers["X-MS-TOKEN-AAD-ACCESS-TOKEN"]);
}
```

Ten kod dodaje standardowy nagłówek HTTP `Authorization: Bearer <access-token>` do wszystkich zdalnych wywołań interfejsu API. W potoku wykonywania żądania MVC ASP.NET Core metoda `OnActionExecuting` jest wykonywana bezpośrednio przed odpowiednią metodą akcji (taką jak `GetAll()`), więc każde wychodzące wywołanie interfejsu API przedstawia teraz token dostępu.

Zapisz wszystkie zmiany. W lokalnym oknie terminala wdróż zmiany w aplikacji frontonu za pomocą następujących poleceń Git:

```bash
git add .
git commit -m "add authorization header for server code"
git push frontend master
```

Ponownie zaloguj się do aplikacji `https://<front-end-app-name>.azurewebsites.net`. Na stronie umowy użycia danych użytkownika kliknij przycisk **Akceptuj**.

Teraz, tak jak wcześniej, powinno być możliwe tworzenie, odczytywanie, aktualizowanie i usuwanie danych aplikacji zaplecza. Jedyną różnicą jest to, że teraz obie aplikacje, w tym także wywołania między usługami, są zabezpieczone przez uwierzytelnianie i autoryzację usługi App Service.

Gratulacje! Kod serwera uzyskuje teraz dostęp do danych zaplecza w imieniu uwierzytelnionego użytkownika.

## <a name="call-api-securely-from-browser-code"></a>Bezpieczne wywoływanie interfejsu API z poziomu kodu przeglądarki

W tym kroku wskażesz interfejs API zaplecza w aplikacji Angular.js frontonu. W ten sposób dowiesz się, jak pobierać token dostępu i wykonywać przy jego użyciu wywołania interfejsu API względem aplikacji zaplecza.

Kod serwera ma dostęp do nagłówków żądań, natomiast kod klienta może uzyskiwać dostęp do elementu `GET /.auth/me` w celu pobrania tych samych tokenów dostępu (zobacz [Pobieranie tokenów w kodzie aplikacji](app-service-authentication-how-to.md#retrieve-tokens-in-app-code)).

> [!TIP]
> W tej sekcji użyto standardowych metod HTTP w celu zademonstrowania bezpiecznych wywołań HTTP. Można jednak użyć [biblioteki ADAL (Active Directory Authentication Library) dla języka JavaScript](https://github.com/AzureAD/azure-activedirectory-library-for-js) w celu uproszczenia wzorca aplikacji Angular.js.
>

### <a name="configure-cors"></a>Konfigurowanie mechanizmu CORS

W aplikacji Cloud Shell włącz usługę CORS w [`az resource update`](/cli/azure/resource#az-resource-update) adresie URL klienta za pomocą polecenia. Zastąp _ \<>nazwy aplikacji zaplecza_ i _ \<symbole zastępcze>nazw aplikacji typu front-end._

```azurecli-interactive
az resource update --name web --resource-group myAuthResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<back-end-app-name> --set properties.cors.allowedOrigins="['https://<front-end-app-name>.azurewebsites.net']" --api-version 2015-06-01
```

Ten krok nie jest powiązany z uwierzytelnianiem i autoryzacją. Należy go jednak wykonać, aby przeglądarka zezwalała na międzydomenowe wywołania interfejsu API z poziomu aplikacji Angular.js. Aby uzyskać więcej informacji, zobacz [Dodawanie funkcji obsługi mechanizmu CORS](app-service-web-tutorial-rest-api.md#add-cors-functionality).

### <a name="point-angularjs-app-to-back-end-api"></a>Wskazywanie interfejsu API zaplecza w aplikacji Angular.js

W repozytorium lokalnym otwórz plik _wwwroot/index.html_.

W wierszu 51 ustaw zmienną `apiEndpoint` na adres URL aplikacji zaplecza (`https://<back-end-app-name>.azurewebsites.net`). Zastąp _ \<nazwę aplikacji zaplecza>_ nazwą aplikacji w usłudze App Service.

W repozytorium lokalnym otwórz plik _wwwroot/app/scripts/todoListSvc.js_ i zobacz, że zmienna `apiEndpoint` jest dołączona na początku wszystkich wywołań interfejsu API. Twoja aplikacja Angular.js wywołuje teraz interfejsy API zaplecza. 

### <a name="add-access-token-to-api-calls"></a>Dodawanie tokenu dostępu do wywołań interfejsu API

W pliku _wwwroot/app/scripts/todoListSvc.js_ powyżej listy wywołań interfejsu API (powyżej wiersza `getItems : function(){`) dodaj następującą funkcję do listy:

```javascript
setAuth: function (token) {
    $http.defaults.headers.common['Authorization'] = 'Bearer ' + token;
},
```

Ta funkcja jest wywoływana, aby ustawić token dostępu w domyślnym nagłówku `Authorization`. Zostanie ona wywołana w następnym kroku.

W repozytorium lokalnym otwórz plik _wwwroot/app/scripts/app.js_ i znajdź następujący kod:

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
}).otherwise({ redirectTo: "/Home" });
```

Zastąp cały blok kodu poniższym kodem:

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
    resolve: {
        token: ['$http', 'todoListSvc', function ($http, todoListSvc) {
            return $http.get('/.auth/me').then(function (response) {
                todoListSvc.setAuth(response.data[0].access_token);
                return response.data[0].access_token;
            });
        }]
    },
}).otherwise({ redirectTo: "/Home" });
```

Nowa zmiana dodaje mapowanie `resolve`, które wywołuje element `/.auth/me` i ustawia token dostępu. Daje to pewność, że dysponujesz tokenem dostępu przed utworzeniem wystąpienia kontrolera `todoListCtrl`. Dzięki temu wszystkie wywołania interfejsu API przez kontroler zawierają token.

### <a name="deploy-updates-and-test"></a>Wdrażanie aktualizacji i testowanie

Zapisz wszystkie zmiany. W lokalnym oknie terminala wdróż zmiany w aplikacji frontonu za pomocą następujących poleceń Git:

```bash
git add .
git commit -m "add authorization header for Angular"
git push frontend master
```

Ponownie przejdź na adres `https://<front-end-app-name>.azurewebsites.net`. Teraz powinno być możliwe tworzenie, odczytywanie, aktualizowanie i usuwanie danych aplikacji zaplecza bezpośrednio w aplikacji Angular.js.

Gratulacje! Kod klienta uzyskuje teraz dostęp do danych zaplecza w imieniu uwierzytelnionego użytkownika.

## <a name="when-access-tokens-expire"></a>Wygasanie tokenów dostępu

Token dostępu wygasa po pewnym czasie. Aby uzyskać informacje na temat odświeżania tokenów dostępu bez konieczności ponownego uwierzytelniania użytkowników w aplikacji, zobacz [Odświeżanie tokenów dostawców tożsamości](app-service-authentication-how-to.md#refresh-identity-provider-tokens).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W poprzednich krokach utworzono zasoby platformy Azure w grupie zasobów. Jeśli te zasoby nie będą raczej potrzebne w przyszłości, usuń grupę zasobów, uruchamiając następujące polecenie w usłudze Cloud Shell:

```azurecli-interactive
az group delete --name myAuthResourceGroup
```

Wykonanie tego polecenia może potrwać około minutę.

<a name="next"></a>
## <a name="next-steps"></a>Następne kroki

Które czynności umiesz wykonać:

> [!div class="checklist"]
> * Włączanie wbudowanego uwierzytelniania i autoryzacji
> * Zabezpieczanie aplikacji przed nieuwierzytelnionymi żądaniami
> * Używanie usługi Azure Active Directory jako dostawcy tożsamości
> * Uzyskiwanie dostępu do zdalnej aplikacji w imieniu zalogowanego użytkownika
> * Zabezpieczanie wywołań między usługami, korzystając z uwierzytelniania przy użyciu tokenów
> * Używanie tokenów dostępu z poziomu kodu serwera
> * Używanie tokenów dostępu z poziomu kodu klienta (przeglądarki)

Przejdź do następnego samouczka, aby dowiedzieć się, jak zmapować niestandardową nazwę DNS na aplikację internetową.

> [!div class="nextstepaction"]
> [Mapowanie istniejącej niestandardowej nazwy DNS na usługę Azure App Service](app-service-web-tutorial-custom-domain.md)
