---
title: Korzystanie z trybu współużytkowania urządzenia z MSAL Android | Azure
description: Dowiedz się, jak przygotować urządzenie z systemem Android do pracy w trybie udostępnionym i uruchomić aplikację procesową pierwszej linii.
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 1/15/2020
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: e74ff320f26a4b6fa7d1caf3d4effca5e10669f4
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546231"
---
# <a name="tutorial-use-shared-device-mode-in-your-android-application"></a>Samouczek: Użyj trybu udostępnionego urządzenia w aplikacji na Androida

> [!NOTE]
> Ta funkcja jest dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="developer-guide"></a>Przewodnik dla deweloperów

Ten przewodnik zawiera wskazówki dla deweloperów dotyczące implementowania trybu udostępnionego urządzenia w aplikacji systemu Android przy użyciu biblioteki uwierzytelniania firmy Microsoft (MSAL). Zobacz [samouczek MSAL android,](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-android) aby zobaczyć, jak zintegrować MSAL z aplikacją systemu Android, zalogować się do użytkownika, wywołać wykres firmy Microsoft i wylogować użytkownika.

### <a name="download-the-sample"></a>Pobierz przykład

Sklonuj [przykładową aplikację](https://github.com/Azure-Samples/ms-identity-android-java/) z usługi GitHub. Próbka ma możliwość pracy w [trybie pojedynczego lub wielokrotnego konta](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account).

### <a name="add-the-msal-sdk-to-your-local-maven-repository"></a>Dodawanie sdk MSAL do lokalnego repozytorium Maven

Jeśli nie używasz przykładowej aplikacji, dodaj bibliotekę MSAL jako zależność w pliku build.gradle, na przykład:

```gradle
dependencies{
  implementation 'com.microsoft.identity.client.msal:1.0.+'
}
```

### <a name="configure-your-app-to-use-shared-device-mode"></a>Konfigurowanie aplikacji do korzystania z trybu urządzenia współdzielonego

Więcej informacji na temat konfigurowania pliku konfiguracyjnego można znaleźć w [dokumentacji konfiguracyjnej.](https://docs.microsoft.com/azure/active-directory/develop/msal-configuration)

Ustaw `"shared_device_mode_supported"` `true` w pliku konfiguracyjnym MSAL.

Być może nie planujesz obsługi trybu z wieloma kontami. Może to być, jeśli nie używasz urządzenia udostępnionego, a użytkownik może zalogować się do aplikacji z więcej niż jednym kontem w tym samym czasie. Jeśli tak, `"account_mode"` `"SINGLE"`ustaw na . Gwarantuje to, że aplikacja `ISingleAccountPublicClientApplication`zawsze otrzyma i znacznie upraszcza integrację z msal. Domyślną `"account_mode"` wartością `"MULTIPLE"`jest , więc ważne jest, aby zmienić tę `"single account"` wartość w pliku konfiguracyjnym, jeśli używasz trybu.

Oto przykład pliku auth_config.json zawartego w**katalogu głównym**>**res**>**raw** **aplikacji**>aplikacji:

```json
{
 "client_id":"Client ID after app registration at https://aka.ms/MobileAppReg",
 "authorization_user_agent":"DEFAULT",
 "redirect_uri":"Redirect URI after app registration at https://aka.ms/MobileAppReg",
 "account_mode":"SINGLE",
 "broker_redirect_uri_registered": true,
 "shared_device_mode_supported": true,
 "authorities":[
  {
   "type":"AAD",
   "audience":{
     "type": "AzureADandPersonalMicrosoftAccount",
     "tenant_id":"common"
   }
  }
 ]
}
```

### <a name="detect-shared-device-mode"></a>Wykrywanie trybu urządzenia współdzielonego

Tryb urządzenia współdzielonego umożliwia skonfigurowanie urządzeń z systemem Android do udostępniania przez wielu pracowników, zapewniając jednocześnie zarządzanie urządzeniem z pomocą sieciOwą tożsamości firmy Microsoft Identity. Pracownicy mogą logować się do swoich urządzeń i szybko uzyskiwać dostęp do informacji o klientach. Po zakończeniu zmiany lub zadania będą mogli wylogować się ze wszystkich aplikacji na urządzeniu udostępnionym za pomocą jednego kliknięcia, a urządzenie będzie natychmiast gotowe do użycia przez następnego pracownika.

Służy `isSharedDevice()` do określania, czy aplikacja jest uruchomiona na urządzeniu, które jest w trybie urządzenia udostępnionego. Aplikacja może użyć tej flagi, aby określić, czy należy odpowiednio zmodyfikować środowisko użytkownika.

Oto fragment kodu, który pokazuje, jak można `isSharedDevice()`użyć .  Pochodzi z klasy `SingleAccountModeFragment` w przykładowej aplikacji:

```Java
deviceModeTextView.setText(mSingleAccountApp.isSharedDevice() ?"Shared" :"Non-Shared");
```

### <a name="initialize-the-publicclientapplication-object"></a>Inicjowanie obiektu PublicClientApplication

Jeśli ustawisz `"account_mode":"SINGLE"` w pliku konfiguracyjnym MSAL, można `ISingleAccountPublicCLientApplication`bezpiecznie oddać zwrócony obiekt aplikacji jako plik .

```java
private ISingleAccountPublicClientApplication mSingleAccountApp;

/*Configure your sample app and save state for this activity*/
PublicClientApplication.create(this.getApplicationCOntext(),
  R.raw.auth_config,
  new PublicClientApplication.ApplicationCreatedListener(){
  @Override
  public void onCreated(IPublicClientApplication application){
  mSingleAccountApp = (ISingleAccountPublicClientApplication)application;
  loadAccount();
  }
  @Override
  public void onError(MsalException exception{
  /*Fail to initialize PublicClientApplication */
  }
});
```

### <a name="detect-single-vs-multiple-account-mode"></a>Wykrywanie trybu pojedynczego i wielokrotnego konta

Jeśli piszesz aplikację, która będzie używana tylko dla pracowników pierwszej linii na urządzeniu udostępnionym, zalecamy zapisanie aplikacji tylko w trybie obsługi jednego konta. Obejmuje to większość aplikacji, które koncentrują się na zadaniach, takich jak aplikacje dokumentacji medycznej, aplikacje do faktur i większość aplikacji biznesowych. Uprości to rozwój, ponieważ wiele funkcji sdk nie musi być dostosowane.

Jeśli aplikacja obsługuje wiele kont, a także tryb urządzenia udostępnionego, należy wykonać sprawdzanie typu i rzutować do odpowiedniego interfejsu, jak pokazano poniżej.

```java
private IPublicClientApplication mApplication;

        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        } else if (mApplication instanceOf    ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>Pobierz zalogowanego użytkownika i określ, czy użytkownik zmienił się na urządzeniu

Metoda `loadAccount` pobiera konto zalogowanego użytkownika. Metoda `onAccountChanged` określa, czy zalogowany użytkownik zmienił się, a jeśli tak, wyczyść:

```java
private void loadAccount()
{
  mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback()
  {
    @Overide
    public void onAccountLoaded(@Nullable IAccount activeAccount)
    {
      if (activeAccount != null)
      {
        signedInUser = activeAccount;
        mSingleAccountApp.acquireTokenSilentAsync(SCOPES,"http://login.microsoftonline.com/common",getAuthSilentCallback());
      }
    }
    @Override
    public void on AccountChanged(@Nullable IAccount priorAccount, @Nullable Iaccount currentAccount)
    {
      if (currentAccount == null)
      {
        //Perform a cleanup task as the signed-in account changed.
        updateSingedOutUI();
      }
    }
    @Override
    public void onError(@NonNull Exception exception)
    {
    }
  }
}
```

### <a name="globally-sign-in-a-user"></a>Globalnie zaloguj się do użytkownika

Następujące znaki użytkownika na urządzeniu do innych aplikacji, które używają MSAL z aplikacją Authenticator:

```java
private void onSignInClicked()
{
  mSingleAccountApp.signIn(getActivity(), SCOPES, null, getAuthInteractiveCallback());
}
```

### <a name="globally-sign-out-a-user"></a>Globalnie wyloguj użytkownika

Poniżej powoduje usunięcie zalogowane konto i czyści tokeny buforowane nie tylko z aplikacji, ale także z urządzenia, które jest w trybie urządzenia udostępnionego:

```java
private void onSignOutClicked()
{
  mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback()
  {
    @Override
    public void onSignOut()
    {
      updateSignedOutUI();
    }
    @Override
    public void onError(@NonNull MsalException exception)
    {
      /*failed to remove account with an exception*/
    }
  });
}
```

## <a name="administrator-guide"></a>Podręcznik administratora

W poniższych krokach opisano konfigurowanie aplikacji w witrynie Azure portal i przepuszczanie urządzenia w tryb urządzenia udostępnionego.

### <a name="register-your-application-in-azure-active-directory"></a>Rejestrowanie aplikacji w usłudze Azure Active Directory

Najpierw zarejestruj aplikację w dzierżawie organizacyjnej. Następnie podaj te wartości poniżej w auth_config.json, aby aplikacja działała poprawnie.

Aby uzyskać informacje na temat tego, jak to zrobić, zobacz [Zarejestruj aplikację](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-android#register-your-application).

> [!NOTE]
> Po zarejestrowaniu aplikacji skorzystaj z przewodnika szybki start po lewej stronie, a następnie wybierz **pozycję Android**. Spowoduje to wyświetlenie strony, na której zostaniesz poproszony o podanie **nazwy pakietu** i **skrótu podpisu** dla aplikacji. Są one bardzo ważne, aby upewnić się, że konfiguracja aplikacji będzie działać. Następnie otrzymasz obiekt konfiguracji, którego możesz użyć w aplikacji, który zostanie wycięty i wklejy do pliku auth_config.json.

![Ekran](media/tutorial-v2-shared-device-mode/register-app.png) rejestracji aplikacji Należy wybrać opcję **Dokonaj tej zmiany dla mnie,** a następnie podać wartości, o które prosi przewodnik Szybki start w witrynie Azure portal. Gdy to zrobisz, wygenerujemy wszystkie pliki konfiguracyjne, których potrzebujesz.

![Ekran informacji o konfiguracji aplikacji](media/tutorial-v2-shared-device-mode/config-info.png)

## <a name="set-up-a-tenant"></a>Konfigurowanie dzierżawy

Do celów testowych należy skonfigurować następujące elementy w dzierżawie: co najmniej dwóch pracowników, jednego administratora urządzeń w chmurze i jednego administratora globalnego. W witrynie Azure portal ustaw administratora urządzeń w chmurze, modyfikując role organizacyjne. W witrynie Azure portal uzyskaj dostęp do ról organizacji, wybierając pozycję **Role usługi Azure Active Directory** > i Administrator urządzeń w**chmurze administratora urządzeń w chmurze administratora.****Roles and Administrators** >  Dodaj użytkowników, którzy mogą umieścić urządzenie w trybie udostępnionym.

## <a name="set-up-an-android-device-in-shared-mode"></a>Konfigurowanie urządzenia z systemem Android w trybie udostępnionym

### <a name="download-the-authenticator-app"></a>Pobierz aplikację Authenticator

Pobierz aplikację Microsoft Authenticator ze sklepu Google Play. Jeśli aplikacja została już pobrana, upewnij się, że jest to najnowsza wersja.

### <a name="authenticator-app-settings--registering-the-device-in-the-cloud"></a>Ustawienia aplikacji authenticatora & rejestrowanie urządzenia w chmurze

Uruchom aplikację Authenticator i przejdź do strony konta głównego. Po wyświetleniu strony **Dodaj konto** możesz przystąpić do udostępniania urządzenia.

![Ekran dodawania konta przez wystawcę uwierzytelniającego](media/tutorial-v2-shared-device-mode/authenticator-add-account.png)

 Przejdź do okienka **Ustawienia** za pomocą paska menu po prawej stronie. Wybierz **pozycję Rejestracja urządzenia** w obszarze Konta & **szkolne**.

 ![Ekran dodawania konta przez wystawcę uwierzytelniającego](media/tutorial-v2-shared-device-mode/authenticator-settings.png)

 Po kliknięciu tego przycisku zostaniesz poproszony o autoryzację dostępu do kontaktów urządzenia. Wynika to z integracji konta Androida na urządzeniu. Wybierz **pozycję Zezwalaj**.

 ![Ekran dodawania konta przez wystawcę uwierzytelniającego](media/tutorial-v2-shared-device-mode/authenticator-allow-screen.png)

Administrator urządzeń w chmurze powinien wprowadzić swoją organizacyjną wiadomość e-mail **w obszarze Lub zarejestrować się jako urządzenie udostępnione.** Następnie kliknij przycisk **zarejestruj się jako urządzenie udostępnione** i wprowadź ich poświadczenia.

![ekran urządzenia rejestrującego](media/tutorial-v2-shared-device-mode/register-device.png)

![sign-in](media/tutorial-v2-shared-device-mode/sign-in.png)

Urządzenie jest teraz w trybie udostępnionym.

![ekran urządzenia rejestrującego](media/tutorial-v2-shared-device-mode/shared-device-mode-screen.png)

 Wszystkie logowania i wylogowywania na urządzeniu będą globalne, co oznacza, że mają zastosowanie do wszystkich aplikacji, które są zintegrowane z MSAL i Microsoft Authenticator na urządzeniu. Teraz można wdrażać aplikacje na urządzeniu korzystającym z funkcji trybu urządzenia udostępnionego.

## <a name="view-the-shared-device-in-the-azure-portal"></a>Wyświetlanie urządzenia udostępnionego w witrynie Azure portal

Po umieszczeniu urządzenia w trybie udostępnionym staje się znane organizacji i jest śledzone w dzierżawie organizacji. Możesz wyświetlić udostępnione urządzenia, patrząc na **typ sprzężenia** w bloku usługi Azure Active Directory w witrynie Azure portal.

![Wszystkie urządzenia w witrynie Azure portal](media/tutorial-v2-shared-device-mode/registered-device-screen.png)

## <a name="running-the-sample-app"></a>Uruchamianie przykładowej aplikacji

Przykładowa aplikacja to prosta aplikacja, która wywoła interfejs API wykresu w organizacji. Przy pierwszym uruchomieniu zostanie wyświetlony monit o wyrażenie zgody, ponieważ aplikacja jest nowa na koncie pracownika.

![Ekran informacji o konfiguracji aplikacji](media/tutorial-v2-shared-device-mode/run-app-permissions-requested.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o trybie udostępnionym w [trybie urządzenia udostępnionego dla urządzeń z systemem Android](msal-android-shared-devices.md)