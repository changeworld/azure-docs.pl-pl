---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: d71d52257b6e8cfa243207c9bfdb5c7de7d3dd37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183912"
---
1. W pliku projektu MainPage.xaml.cs dodaj następujące **instrukcje:**
   
        using System.Linq;        
        using Windows.Security.Credentials;
2. Zastąp metodę **AuthenticateAsync** następującym kodem:
   
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
                // expired, as shown in this post: https://aka.ms/jww5vp.
   
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
   
    W tej wersji **AuthenticateAsync**aplikacja próbuje użyć poświadczeń przechowywanych w **PasswordVault,** aby uzyskać dostęp do usługi. Regularne logowanie jest również wykonywane, gdy nie ma przechowywanych poświadczeń.
   
   > [!NOTE]
   > Token buforowany może wygasnąć, a wygaśnięcie tokenu może również wystąpić po uwierzytelnieniu, gdy aplikacja jest w użyciu. Aby dowiedzieć się, jak ustalić, czy token wygasł, zobacz [Sprawdzanie wygasłych tokenów uwierzytelniania.](https://aka.ms/jww5vp) Aby uzyskać rozwiązanie do obsługi błędów autoryzacji związanych z wygasającymi tokenami, zobacz [buforowanie i obsługę wygasłych tokenów w zarządzanym sdk azure mobile services](https://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx). 
   > 
   > 
3. Uruchom ponownie aplikację dwukrotnie.
   
    Należy zauważyć, że przy pierwszym uruchomieniu ponowne logowanie się u dostawcy jest wymagane. Jednak przy drugim ponownym uruchomieniu są używane poświadczenia buforowane i logowania jest pomijany. 

