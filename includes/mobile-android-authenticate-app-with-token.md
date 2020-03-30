---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: deb94cab97bd9a402676cdc5c0239da8d07ed8b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183919"
---
W poprzednim przykładzie pokazano standardowe logowanie, które wymaga od klienta, aby skontaktować się zarówno z dostawcą tożsamości, jak i usługą platformy Azure zaplecza przy każdym uruchomieniu aplikacji. Ta metoda jest nieefektywne i może mieć problemy związane z użyciem, jeśli wielu klientów próbuje uruchomić aplikację jednocześnie. Lepszym rozwiązaniem jest buforowanie tokenu autoryzacji zwróconego przez usługę platformy Azure i spróbuj użyć tego najpierw przed użyciem logowania opartego na dostawcy.

> [!NOTE]
> Token wystawiony przez usługę back-end azure można buforować, niezależnie od tego, czy używasz uwierzytelniania zarządzanego przez klienta, czy z usługą. W tym samouczku użyto uwierzytelniania zarządzanego przez usługę.
>
>

1. Otwórz plik ToDoActivity.java i dodaj następujące instrukcje importu:

    ```java
    import android.content.Context;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;
    ```

2. Dodaj następujące elementy `ToDoActivity` członkowskie do klasy.

    ```java
    public static final String SHAREDPREFFILE = "temp";
    public static final String USERIDPREF = "uid";
    public static final String TOKENPREF = "tkn";
    ```

3. W pliku ToDoActivity.java dodaj następującą `cacheUserToken` definicję metody.

    ```java
    private void cacheUserToken(MobileServiceUser user)
    {
        SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
        Editor editor = prefs.edit();
        editor.putString(USERIDPREF, user.getUserId());
        editor.putString(TOKENPREF, user.getAuthenticationToken());
        editor.commit();
    }
    ```

    Ta metoda przechowuje identyfikator użytkownika i token w pliku preferencji, który jest oznaczony jako prywatny. Powinno to chronić dostęp do pamięci podręcznej, tak aby inne aplikacje na urządzeniu nie miały dostępu do tokenu. Preferencja jest w trybie piaskownicy dla aplikacji. Jednak jeśli ktoś uzyska dostęp do urządzenia, jest możliwe, że mogą uzyskać dostęp do pamięci podręcznej tokenu za pomocą innych środków.

   > [!NOTE]
   > Możesz dodatkowo chronić token za pomocą szyfrowania, jeśli dostęp tokenu do danych jest uważany za wysoce poufny i ktoś może uzyskać dostęp do urządzenia. Całkowicie bezpieczne rozwiązanie wykracza jednak poza zakres tego samouczka i zależy od wymagań dotyczących zabezpieczeń.
   >
   >

4. W pliku ToDoActivity.java dodaj następującą `loadUserTokenCache` definicję metody.

    ```java
    private boolean loadUserTokenCache(MobileServiceClient client)
    {
        SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
        String userId = prefs.getString(USERIDPREF, null);
        if (userId == null)
            return false;
        String token = prefs.getString(TOKENPREF, null);
        if (token == null)
            return false;

        MobileServiceUser user = new MobileServiceUser(userId);
        user.setAuthenticationToken(token);
        client.setCurrentUser(user);

        return true;
    }
    ```

5. W pliku *ToDoActivity.java* zastąp `authenticate` metody i `onActivityResult` metody następującymi, które używają pamięci podręcznej tokenu. Zmień dostawcę logowania, jeśli chcesz korzystać z konta innego niż Google.

    ```java
    private void authenticate() {
        // We first try to load a token cache if one exists.
        if (loadUserTokenCache(mClient))
        {
            createTable();
        }
        // If we failed to load a token cache, sign in and create a token cache
        else
        {
            // Sign in using the Google provider.
            mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
        }
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        // When request completes
        if (resultCode == RESULT_OK) {
            // Check the request code matches the one we send in the sign-in request
            if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
                MobileServiceActivityResult result = mClient.onActivityResult(data);
                if (result.isLoggedIn()) {
                    // sign-in succeeded
                    createAndShowDialog(String.format("You are now signed in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                    cacheUserToken(mClient.getCurrentUser());
                    createTable();
                } else {
                    // sign-in failed, check the error message
                    String errorMessage = result.getErrorMessage();
                    createAndShowDialog(errorMessage, "Error");
                }
            }
        }
    }
    ```

6. Tworzenie aplikacji i testowanie uwierzytelniania przy użyciu prawidłowego konta. Uruchom go co najmniej dwa razy. Podczas pierwszego uruchomienia powinien pojawić się monit o zalogowanie się i utworzenie pamięci podręcznej tokenu. Następnie każde uruchomienie próbuje załadować pamięć podręczną tokenu do uwierzytelniania. Nie musisz się logować.
