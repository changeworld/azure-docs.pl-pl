
W poprzednim przykładzie pokazano standardowego logowania, która wymaga od klienta do kontaktowania się z zarówno dostawcy tożsamości, jak i zaplecza usługi Azure każdym uruchomieniu aplikacji. Ta metoda jest nieefektywne i może mieć problemy związane z użycia, jeśli wielu klientów próbuje jednocześnie uruchomiona aplikacja. Lepszym rozwiązaniem jest buforowanie token autoryzacji zwrócony przez usługę Azure, spróbuj użyć pierwsze przed użyciem opartej na dostawcy logowania.

> [!NOTE]
> Może buforować token wystawiony przez zaplecza usługi Azure, niezależnie od tego, czy używasz zarządzanych przez klienta lub zarządzane przez usługę uwierzytelniania. W tym samouczku korzysta z zarządzanymi przez usługę uwierzytelniania.
>
>

1. Otwórz plik ToDoActivity.java i dodaj następujące instrukcje importu:

    ```java
    import android.content.Context;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;
    ```

2. Dodaj następujące elementy członkowskie do `ToDoActivity` klasy.

    ```java
    public static final String SHAREDPREFFILE = "temp";
    public static final String USERIDPREF = "uid";
    public static final String TOKENPREF = "tkn";
    ```

3. W pliku ToDoActivity.java, dodaj następującą definicję dla `cacheUserToken` metody.

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

    Ta metoda identyfikator użytkownika i tokenu są przechowywane w pliku preferencji, który jest oznaczony jako prywatny. Należy chronić dostęp do pamięci podręcznej, dzięki czemu inne aplikacje na urządzeniu nie mają dostępu do tokenu. Preferencje jest w trybie piaskownicy dla aplikacji. Jednak jeśli ktoś uzyska dostęp do urządzenia, istnieje możliwość, że mogą one uzyskać dostęp do pamięci podręcznej tokenu w inny sposób.

   > [!NOTE]
   > Można dodatkowo zabezpieczyć token z szyfrowaniem, jeśli token dostępu do danych jest uznawany za bardzo ważne, a ktoś może uzyskać dostępu do urządzenia. Całkowicie bezpieczna wykracza poza zakres tego samouczka, jednak i zależy od wymagań dotyczących zabezpieczeń.
   >
   >

4. W pliku ToDoActivity.java, dodaj następującą definicję dla `loadUserTokenCache` metody.

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

5. W *ToDoActivity.java* pliku, Zastąp `authenticate` i `onActivityResult` metody o następujących te, których używa tokenu pamięć podręczną. Zmień dostawcy logowania, jeśli chcesz użyć innego konta niż Google.

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

6. Tworzenie aplikacji i test uwierzytelniania przy użyciu prawidłowego konta. Uruchom co najmniej dwa razy. Przy pierwszym uruchomieniu powinien zostać wyświetlony monit, zaloguj się i utworzyć tokenu pamięć podręczną. Po wykonaniu tej każdym uruchomieniu próbuje załadować pamięci podręcznej tokenu uwierzytelniania. Należy nie trzeba się zalogować.
