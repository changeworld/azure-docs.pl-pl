---
title: ADAL do MSAL przewodnik migracji dla Systemu Android | Azure
description: Dowiedz się, jak przeprowadzić migrację aplikacji Biblioteki uwierzytelniania usługi Azure Active Directory (ADAL) w systemie Android do biblioteki uwierzytelniania Microsoft (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Android
ms.workload: identity
ms.date: 09/6/2019
ms.author: marsma
ms.reviewer: shoatman
ms.custom: aaddev
ms.openlocfilehash: 21866bb7dab3d5a093ffc4655161b80853eadfc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084057"
---
# <a name="adal-to-msal-migration-guide-for-android"></a>Przewodnik po migracji ADAL do MSAL dla systemu Android

W tym artykule przedstawiono zmiany, które należy wprowadzić w celu migracji aplikacji korzystającej z biblioteki uwierzytelniania usługi Azure Active Directory (ADAL) do korzystania z biblioteki uwierzytelniania firmy Microsoft (MSAL).

## <a name="difference-highlights"></a>Wyróżnienia różnic

Usługa ADAL współpracuje z punktem końcowym usługi Azure Active Directory w wersji 1.0. Biblioteka uwierzytelniania firmy Microsoft (MSAL) współpracuje z platformą tożsamości firmy Microsoft — wcześniej znaną jako punkt końcowy usługi Azure Active Directory w wersji 2.0. Platforma tożsamości firmy Microsoft różni się od usługi Azure Active Directory w wersji 1.0 tym, że:

Obsługuje:
  - Tożsamość organizacyjna (usługa Azure Active Directory)
  - Tożsamości nieorganizacyjne, takie jak Outlook.com, Xbox Live itd.
  - (tylko B2C) Federacyjne logowanie w Google, Facebook, Twitter i Amazon

- Czy normy są zgodne z:
  - OAuth v2.0
  - OpenID Connect (OIDC)

Publiczny interfejs API MSAL wprowadza istotne zmiany, w tym:

- Nowy model uzyskiwania dostępu do tokenów:
  - Usługa ADAL zapewnia dostęp `AuthenticationContext`do tokenów za pośrednictwem programu , który reprezentuje serwer. MSAL zapewnia dostęp do `PublicClientApplication`tokenów za pośrednictwem , który reprezentuje klienta. Deweloperzy klienta nie muszą tworzyć `PublicClientApplication` nowego wystąpienia dla każdego urzędu, z którego muszą współpracować. Wymagana `PublicClientApplication` jest tylko jedna konfiguracja.
  - Obsługa żądania tokenów dostępu przy użyciu zakresów oprócz identyfikatorów zasobów.
  - Obsługa przyrostowej zgody. Deweloperzy mogą żądać zakresów, ponieważ użytkownik uzyskuje dostęp do coraz większej liczby funkcji w aplikacji, w tym tych, które nie są uwzględniane podczas rejestracji aplikacji.
  - Urzędy nie są już weryfikowane w czasie wykonywania. Zamiast tego deweloper deklaruje listę "znanych władz" podczas rozwoju.
- Zmiany interfejsu API tokenu:
  - W ADAL `AcquireToken()` najpierw sprawia, że ciche żądanie. W przeciwnym razie sprawia, że interaktywne żądanie. To zachowanie spowodowało, że niektórzy `AcquireToken`deweloperzy polegali tylko na , co spowodowało, że użytkownik był nieoczekiwanie monitowany o poświadczenia czasami. MSAL wymaga deweloperzy być zamierzone o tym, gdy użytkownik odbiera monit interfejsu użytkownika.
    - `AcquireTokenSilent`zawsze powoduje ciche żądanie, które zakończy się powodzeniem lub niepowodzeniem.
    - `AcquireToken`zawsze powoduje żądanie, które monituje użytkownika za pośrednictwem interfejsu użytkownika.
- Usługa MSAL obsługuje logowanie z domyślnej przeglądarki lub osadzonego widoku sieci Web:
  - Domyślnie używana jest domyślna przeglądarka na urządzeniu. Dzięki temu msal używać stanu uwierzytelniania (pliki cookie), które mogą być już obecne dla jednego lub więcej zalogowanych kont. Jeśli stan uwierzytelniania nie jest obecny, uwierzytelnianie podczas autoryzacji za pośrednictwem protokołu MSAL powoduje utworzenie stanu uwierzytelniania (plików cookie) na rzecz innych aplikacji sieci web, które będą używane w tej samej przeglądarce.
- Nowy model wyjątku:
  - Wyjątki jaśniej definiują typ błędu, który wystąpił i co deweloper musi zrobić, aby go rozwiązać.
- MSAL obsługuje obiekty `AcquireToken` `AcquireTokenSilent` parametrów dla i wywołania.
- MSAL obsługuje konfigurację deklaratywną dla:
  - Identyfikator klienta, przekieruj identyfikator URI.
  - Osadzona a domyślna przeglądarka
  - Władze
  - Ustawienia HTTP, takie jak limit czasu odczytu i połączenia

## <a name="your-app-registration-and-migration-to-msal"></a>Rejestracja i migracja aplikacji do usługi MSAL

Nie musisz zmieniać istniejącej rejestracji aplikacji, aby używać usługi MSAL. Jeśli chcesz skorzystać z przyrostowej/progresywnej zgody, może być konieczne przejrzenie rejestracji w celu zidentyfikowania określonych zakresów, które mają być wymagane przyrostowo. Więcej informacji na temat zakresów i przyrostowej zgody następuje.

W rejestracji aplikacji w portalu zostanie wyświetlenia karty **uprawnień interfejsu API.** Dzięki temu udostępnia listę interfejsów API i uprawnień (zakresów), które aplikacja jest obecnie skonfigurowana do żądania dostępu do. Pokazuje również listę nazw zakresu skojarzonych z każdym uprawnieniem interfejsu API.

### <a name="user-consent"></a>Zgoda użytkownika

Z ADAL i AAD punktu końcowego, zgody użytkownika do zasobów, które posiadają została udzielona przy pierwszym użyciu. Za pomocą msal i platformy tożsamości firmy Microsoft można żądać zgody przyrostowo. Zgoda przyrostowa jest przydatna w przypadku uprawnień, które użytkownik może uznać za wysokie uprawnienia lub może w inny sposób zapytać, jeśli nie zostanie do okazanie, dlaczego uprawnienie jest wymagane. W ADAL te uprawnienia mogły spowodować, że użytkownik porzuci logowanie się do aplikacji.

> [!TIP]
> Zaleca się użycie przyrostowej zgody w scenariuszach, w których należy podać dodatkowy kontekst dla użytkownika o tym, dlaczego aplikacja potrzebuje uprawnień.

### <a name="admin-consent"></a>zgoda administratora

Administratorzy instytucji mogą wyrazić zgodę na uprawnienia wymagane przez aplikację w imieniu wszystkich członków ich organizacji. Niektóre organizacje zezwalają tylko administratorom na wyrażanie zgody na aplikacje. Zgoda administratora wymaga uwzględnienia wszystkich uprawnień i zakresów interfejsu API używanych przez aplikację w rejestracji aplikacji.

> [!TIP]
> Mimo że można zażądać zakresu przy użyciu usługi MSAL dla czegoś, co nie zostało uwzględnione w rejestracji aplikacji, zaleca się zaktualizowanie rejestracji aplikacji, aby uwzględnić wszystkie zasoby i zakresy, które użytkownik może kiedykolwiek udzielić uprawnień.

## <a name="migrating-from-resource-ids-to-scopes"></a>Migrowanie z identyfikatorów zasobów do zakresów

### <a name="authenticate-and-request-authorization-for-all-permissions-on-first-use"></a>Uwierzytelnianie i żądanie autoryzacji dla wszystkich uprawnień przy pierwszym użyciu

Jeśli obecnie używasz biblioteki ADAL i nie trzeba używać zgody przyrostowej, najprostszym sposobem, aby `acquireToken` rozpocząć korzystanie `AcquireTokenParameter` z msal jest żądanie przy użyciu nowego obiektu i ustawienie wartości identyfikatora zasobu.

> [!CAUTION]
> Nie można ustawić zarówno zakresów, jak i identyfikatora zasobu. Próba skonfigurowania obu spowoduje `IllegalArgumentException`utworzenie pliku .

 Spowoduje to takie samo zachowanie w wersji 1, które są używane. Wszystkie uprawnienia wymagane w rejestracji aplikacji są wymagane od użytkownika podczas ich pierwszej interakcji.

### <a name="authenticate-and-request-permissions-only-as-needed"></a>Uwierzytelnij i żądaj uprawnień tylko w razie potrzeby

Aby skorzystać z przyrostowej zgody, należy utworzyć listę uprawnień (zakresów), których aplikacja używa z rejestracji aplikacji, i zorganizuj je w dwie listy na podstawie:

- Zakresy, które chcesz zażądać podczas pierwszej interakcji użytkownika z aplikacją podczas logowania.
- Uprawnienia, które są skojarzone z ważną funkcją aplikacji, które należy również wyjaśnić użytkownikowi.

Po zorganizowaniu zakresów zorganizuj każdą listę według zasobu (INTERFEJSU API), dla którego chcesz zażądać tokenu. Jak również wszelkie inne zakresy, które użytkownik ma autoryzować w tym samym czasie.

Obiekt parametrów używany do żądania do msal obsługuje:

- `Scope`: Lista zakresów, dla których chcesz zażądać autoryzacji i odbierania tokenu dostępu.
- `ExtraScopesToConsent`: Dodatkowa lista zakresów, dla których chcesz zażądać autoryzacji podczas żądania tokenu dostępu dla innego zasobu. Ta lista zakresów pozwala zminimalizować liczbę razy, że trzeba zażądać autoryzacji użytkownika. Oznacza to mniej monitów o autoryzację lub zgodę użytkownika.

## <a name="migrate-from-authenticationcontext-to-publicclientapplications"></a>Migrowanie z authenticationContext do PublicClientApplications

### <a name="constructing-publicclientapplication"></a>Konstruowanie aplikacji PublicClientApplication

Korzystając z usługi MSAL, można `PublicClientApplication`utworzyć wystąpienie pliku . Ten obiekt modeluje tożsamość aplikacji i jest używany do żądania do jednego lub więcej urzędów. Za pomocą tego obiektu skonfigurujesz tożsamość klienta, przekierujesz identyfikator URI, domyślny urząd, czy ma być używany przeglądarka urządzeń a osadzony widok sieci web, poziom dziennika i inne.

Można deklaratywnie skonfigurować ten obiekt za pomocą JSON, który można podać jako plik lub przechowywać jako zasób w pliku APK.

Chociaż ten obiekt nie jest singleton, wewnętrznie `Executors` używa udostępnione dla żądań interaktywnych i cichych.

### <a name="business-to-business"></a>Biznes dla biznesu

W ADAL każda organizacja, z której żądasz tokenów `AuthenticationContext`dostępu, wymaga osobnego wystąpienia pliku . W MSAL nie jest to już wymagane. Można określić urząd, z którego chcesz zażądać tokenu w ramach żądania dyskretnego lub interaktywnego.

### <a name="migrate-from-authority-validation-to-known-authorities"></a>Migracja z sprawdzania poprawności władzy do znanych organów

MSAL nie ma flagi, aby włączyć lub wyłączyć sprawdzanie poprawności urzędu. Sprawdzanie poprawności urzędu jest funkcją w ADAL i we wczesnych wersjach msal, który uniemożliwia kodu z żądania tokenów z potencjalnie złośliwego urzędu. Usługa MSAL pobiera teraz listę urzędów znanych firmie Microsoft i scala tę listę z urzędami określonymi w konfiguracji.

> [!TIP]
> Jeśli jesteś użytkownikiem usługi Azure Business to Consumer (B2C), oznacza to, że nie musisz już wyłączać sprawdzania poprawności urzędu. Zamiast tego należy uwzględnić każdą z obsługiwanych zasad usługi Azure AD B2C jako urzędy w konfiguracji msal.

Jeśli spróbujesz użyć urzędu, który nie jest znany firmie Microsoft i nie jest `UnknownAuthorityException`uwzględniony w konfiguracji, otrzymasz plik .

### <a name="logging"></a>Rejestrowanie
Teraz można deklaratywnie skonfigurować rejestrowanie w ramach konfiguracji, w następujący sposób:

 ```
 "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": true
  }
  ```

## <a name="migrate-from-userinfo-to-account"></a>Migrowanie z userinfo na konto

W ADAL `AuthenticationResult` udostępnia `UserInfo` obiekt używany do pobierania informacji o uwierzytelnionym koncie. Termin "użytkownik", co oznaczało agenta ludzkiego lub oprogramowania, został zastosowany w sposób, który utrudniał komunikowanie się, że niektóre aplikacje obsługują jednego użytkownika (czy to agenta ludzkiego lub oprogramowania), który ma wiele kont.

Rozważmy konto bankowe. Możesz mieć więcej niż jedno konto w więcej niż jednej instytucji finansowej. Po otwarciu konta użytkownik (użytkownik) wydaje poświadczenia, takie jak karta bankomatowa & numeru PIN, które są używane do uzyskiwania dostępu do salda, płatności rachunków itd. Te poświadczenia mogą być używane tylko w instytucji finansowej, która je wystawiła.

Analogicznie, podobnie jak konta w instytucji finansowej, konta na platformie tożsamości firmy Microsoft są uzyskiwać dostęp przy użyciu poświadczeń. Te poświadczenia są zarejestrowane lub wystawione przez firmę Microsoft. Lub przez firmę Microsoft w imieniu organizacji.

Gdy platforma tożsamości firmy Microsoft różni się od instytucji finansowej, w tej analogii jest to, że platforma tożsamości firmy Microsoft zapewnia platformę, która umożliwia użytkownikowi korzystanie z jednego konta i skojarzonych z nim poświadczeń, aby uzyskać dostęp do zasobów, które należą do osób i organizacji. To jest jak możliwość korzystania z karty wydanej przez jeden bank, w jeszcze innej instytucji finansowej. Działa to, ponieważ wszystkie organizacje, o których mowa, korzystają z platformy tożsamości firmy Microsoft, która umożliwia korzystanie z jednego konta w wielu organizacjach. Oto przykład:

Sam pracuje dla Contoso.com ale zarządza maszynami wirtualnymi platformy Azure, które należą do Fabrikam.com. Aby Sam mógł zarządzać maszynami wirtualnymi firmy Fabrikam, musi być upoważniony do uzyskania do nich dostępu. Ten dostęp można udzielić, dodając konto Sama do Fabrikam.com i przyznając jego konto rolę, która pozwala mu pracować z maszynami wirtualnymi. Można to zrobić za pomocą witryny Azure portal.

Dodanie konta Contoso.com Sam jako członka Fabrikam.com spowodowałoby utworzenie nowego rekordu w usłudze Azure Active Directory firmy Fabrikam.com dla Sam. Rekord Sama w usłudze Azure Active Directory jest znany jako obiekt użytkownika. W takim przypadku ten obiekt użytkownika wskazywałby z powrotem do obiektu użytkownika Sama w Contoso.com. Sam's Fabrikam user object is the local representation of Sam, and would be used to store information about the account associated with Sam in the context of Fabrikam.com. W Contoso.com, Sam tytuł jest Senior DevOps Consultant. W programie Fabrikam tytuł Sam to Maszyny wirtualne wykonawcy. W Contoso.com Sam nie jest odpowiedzialny ani upoważniony do zarządzania maszynami wirtualnymi. W Fabrikam.com to jego jedyna funkcja zawodowa. Jednak Sam nadal ma tylko jeden zestaw poświadczeń do śledzenia, które są poświadczenia wydane przez Contoso.com.

Po pomyślnym `acquireToken` wywołaniu zostanie wyświetlone odwołanie `IAccount` do obiektu, który `acquireTokenSilent` może być używany w późniejszych żądaniach.

### <a name="imultitenantaccount"></a>Konto IMultiTenantAccount

Jeśli masz aplikację, która uzyskuje dostęp do oświadczeń dotyczących konta od każdego z dzierżaw, `IAccount` w `IMultiTenantAccount`których konto jest reprezentowane, możesz rzutować obiekty na program . Ten interfejs zawiera `ITenantProfiles`mapę , wpisane przez identyfikator dzierżawy, który umożliwia dostęp do oświadczeń, które należą do konta w każdej z dzierżawy, który zażądał tokenu od, względem bieżącego konta.

Roszczenia u źródła `IAccount` i `IMultiTenantAccount` zawsze zawierają roszczenia od najemcy domu. Jeśli nie zostały jeszcze złożone żądanie tokenu w dzierżawie domowej, ta kolekcja będzie pusta.

## <a name="other-changes"></a>Inne zmiany

### <a name="use-the-new-authenticationcallback"></a>Korzystanie z nowego powrotu zwrotnego authenticationa

```java
// Existing ADAL Interface
public interface AuthenticationCallback<T> {

    /**
     * This will have the token info.
     *
     * @param result returns <T>
     */
    void onSuccess(T result);

    /**
     * Sends error information. This can be user related error or server error.
     * Cancellation error is AuthenticationCancelError.
     *
     * @param exc return {@link Exception}
     */
    void onError(Exception exc);
}
```

```java
// New Interface for Interactive AcquireToken
public interface AuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException}
     */
    void onError(final MsalException exception);

    /**
     * Will be called if user cancels the flow.
     */
    void onCancel();
}

// New Interface for Silent AcquireToken
public interface SilentAuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException} or
     *                  {@link MsalUiRequiredException}.
     */
    void onError(final MsalException exception);
}


```

## <a name="migrate-to-the-new-exceptions"></a>Migracja do nowych wyjątków

W ADAL istnieje jeden typ wyjątku, `AuthenticationException`który zawiera metodę `ADALError` pobierania wartości wyliczenia.
W msal istnieje hierarchia wyjątków i każdy ma swój własny zestaw skojarzonych kodów określonych błędów.

Lista wyjątków MSAL

|Wyjątek  | Opis  |
|---------|---------|
| `MsalException`     | Domyślny zaznaczony wyjątek zgłoszony przez msal.  |
| `MsalClientException`     | Generowane, jeśli błąd jest po stronie klienta. |
| `MsalArgumentException`     | Generowane, jeśli jeden lub więcej argumentów danych wejściowych są nieprawidłowe. |
| `MsalClientException`     | Generowane, jeśli błąd jest po stronie klienta. |
| `MsalServiceException`     | Jest generowany, jeśli błąd jest po stronie serwera. |
| `MsalUserCancelException`     | Generowane, jeśli użytkownik anulował przepływ uwierzytelniania.  |
| `MsalUiRequiredException`     | Jest generowany, jeśli token nie może być odświeżany w trybie dyskretnym.  |
| `MsalDeclinedScopeException`     | Generowane, jeśli jeden lub więcej żądanych zakresów zostało odrzuconych przez serwer.  |
| `MsalIntuneAppProtectionPolicyRequiredException` | Generowane, jeśli zasób ma włączone zasady ochrony MAMCA. |

### <a name="adalerror-to-msalexception-errorcode"></a>ADALError do MsalException ErrorCode

### <a name="adal-logging-to-msal-logging"></a>Rejestrowanie ADAL do rejestrowania msal

```java
// Legacy Interface
    StringBuilder logs = new StringBuilder();
    Logger.getInstance().setExternalLogger(new ILogger() {
            @Override
            public void Log(String tag, String message, String additionalMessage, LogLevel logLevel, ADALError errorCode) {
                logs.append(message).append('\n');
            }
        });
```

```java
// New interface
  StringBuilder logs = new StringBuilder();
  Logger.getInstance().setExternalLogger(new ILoggerCallback() {
            @Override
            public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII) {
                logs.append(message).append('\n');
            }
        });

// New Log Levels:
public enum LogLevel
{
        /**
         * Error level logging.
         */
        ERROR,
        /**
         * Warning level logging.
         */
        WARNING,
        /**
         * Info level logging.
         */
        INFO,
        /**
         * Verbose level logging.
         */
        VERBOSE
}
```
