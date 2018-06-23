
1. W pliku MainPage.xaml.cs projekt, Dodaj następujący **przy użyciu** instrukcji:
   
        using System.Linq;        
        using Windows.Security.Credentials;
2. Zastąp **metody AuthenticateAsync** metodę z następującym kodem:
   
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
   
            // This sample uses the Facebook provider.
            var provider = MobileServiceAuthenticationProvider.Facebook;
   
            // Use the PasswordVault to securely store and access credentials.
            PasswordVault vault = new PasswordVault();
            PasswordCredential credential = null;
   
            try
            {
                // Try to get an existing credential from the vault.
                credential = vault.FindAllByResource(provider.ToString()).FirstOrDefault();
            }
            catch (Exception)
            {
                // When there is no matching resource an error occurs, which we ignore.
            }
   
            if (credential != null)
            {
                // Create a user from the stored credentials.
                user = new MobileServiceUser(credential.UserName);
                credential.RetrievePassword();
                user.MobileServiceAuthenticationToken = credential.Password;
   
                // Set the user from the stored credentials.
                App.MobileService.CurrentUser = user;
   
                // Consider adding a check to determine if the token is 
                // expired, as shown in this post: http://aka.ms/jww5vp.
   
                success = true;
                message = string.Format("Cached credentials for user - {0}", user.UserId);
            }
            else
            {
                try
                {
                    // Sign in with the identity provider.
                    user = await App.MobileService
                        .LoginAsync(provider, "{url_scheme_of_your_app}");
   
                    // Create and store the user credentials.
                    credential = new PasswordCredential(provider.ToString(),
                        user.UserId, user.MobileServiceAuthenticationToken);
                    vault.Add(credential);
   
                    success = true;
                    message = string.Format("You are now signed in - {0}", user.UserId);
                }
                catch (MobileServiceInvalidOperationException)
                {
                    message = "You must sign in. Sign-In Required";
                }
            }
   
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
   
            return success;
        }
   
    W tej wersji programu **metody AuthenticateAsync**, aplikacja próbuje użyć poświadczeń przechowywanych w **PasswordVault** dostęp do usługi. Regularne logowanie również jest wykonywane, gdy nie istnieje żadne przechowywanych poświadczeń.
   
   > [!NOTE]
   > Buforowany token wygasł, a po uwierzytelnieniu wygaśnięcia tokenu może również wystąpić, gdy aplikacja jest w użyciu. Aby dowiedzieć się, jak ustalić, czy token wygasł, zobacz [Sprawdź, czy tokeny uwierzytelniania wygasła](http://aka.ms/jww5vp). Jako rozwiązanie do obsługi autoryzacji błędy związane z tokenami wygasające, zobacz wpis [buforowania i obsługa wygaśnięcia tokenów w usłudze Azure Mobile Services zarządzane SDK](http://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx). 
   > 
   > 
3. Uruchom ponownie aplikację dwa razy.
   
    Należy zauważyć, że w pierwszym rozruchu, logowania z dostawcą ponownie jest wymagana. Jednak na drugie ponowne uruchomienie buforowane poświadczenia są używane i logowania jest pomijane. 

