---
title: Set up the Microsoft Authenticator app as your authentication method - Azure AD
description: How to set up your Security info to verify your identity using the Microsoft Authenticator app as your authentication method.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 761c9d3ed4cb49a7dc95ea98ce6f884930185643
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233194"
---
# <a name="set-up-the-microsoft-authenticator-app-as-your-authentication-method-from-the-security-info-preview-page"></a>Set up the Microsoft Authenticator app as your authentication method from the Security info (preview) page

You can follow these steps to add your two-factor verification and password reset methods. After you've set this up the first time, you can return to the **Security info** page to add, update, or delete your security information.

If you're prompted to set this up immediately after you sign in to your work or school account, see the detailed steps in the [Set up your security info from the sign-in page prompt](security-info-setup-signin.md) article.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>If you don't see an authenticator app option, it's possible that your organization doesn't allow you to use an authentication app for verification. In this case, you'll need to choose another method or contact your administrator for more help.

## <a name="set-up-the-microsoft-authenticator-app-from-the-security-info-page"></a>Set up the Microsoft Authenticator app from the Security info page

Depending on your organization’s settings, you might be able to use an authentication app as one of your security info methods. You aren't required to use the Microsoft Authenticator app, and you can choose a different app during the set up process. However, this article uses the Microsoft Authenticator app.

### <a name="to-set-up-the-microsoft-authenticator-app"></a>To set up the Microsoft Authenticator app

1. Sign in to your work or school account and then go to your https://myprofile.microsoft.com/ page.

    ![My Profile page, showing highlighted Security info links](media/security-info/securityinfo-myprofile-page.png)

2. Select **Security info** from the left navigation pane or from the link in the **Security info** block, and then select **Add method** from the **Security info** page.

    ![Security info page with highlighted Add method option](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. On the **Add a method** page, select **Authenticator app** from the drop-down list, and then select **Add**.

    ![Add method box, with Authenticator app selected](media/security-info/securityinfo-myprofile-addauthapp.png)

4. On the **Start by getting the app** page, select **Download now** to download and install the Microsoft Authenticator app on your mobile device, and then select **Next**.

    Aby uzyskać więcej informacji o pobieraniu i instalowaniu aplikacji, zobacz [Pobieranie i instalowanie aplikacji Microsoft Authenticator](user-help-auth-app-download-install.md).

    ![Start by getting the app page](media/security-info/securityinfo-myprofile-getauthapp.png)

   > [!Note]
   > Jeśli chcesz użyć aplikacji uwierzytelniającej innej niż aplikacja Microsoft Authenticator, wybierz link **Chcę użyć innej aplikacji uwierzytelniania**.
   >
   > Jeśli organizacja umożliwia wybranie innej metody niż aplikacja uwierzytelniająca, możesz wybrać **link Chcę skonfigurować inną metodę**.

5. Pozostań na stronie **Konfigurowanie konta** podczas konfigurowania aplikacji Microsoft Authenticator na urządzeniu mobilnym.

    ![Set up the authenticator app page](media/security-info/securityinfo-myprofile-setupauthapp.png)

6. Otwórz aplikację Microsoft Authenticator, zezwól na powiadomienia (jeśli pojawi się monit), wybierz pozycję **Dodaj konto** z menu ikony **Dostosowywanie i kontrolowanie** w prawym górnym rogu, a następnie wybierz pozycję **Konto służbowe**.

    >[!Note]
    >If this is the first time you're setting up the Microsoft Authenticator app, you might receive a prompt asking whether to allow the app to access your camera (iOS) or to allow the app to take pictures and record video (Android). You must select **Allow** so the authenticator app can access your camera to take a picture of the QR code in the next step. If you don't allow the camera, you can still set up the authenticator app, but you'll need to add the code information manually. For information about how to add the code manually, see see [Manually add an account to the app](user-help-auth-app-add-account-manual.md).

7. Wróć na stronę **Konfigurowanie konta** na komputerze, a następnie wybierz pozycję **Dalej**.

    Zostanie wyświetlona strona **Skanowanie kodu QR**.

    ![Skanowanie kodu QR przy użyciu aplikacji Authenticator](media/security-info/securityinfo-myprofile-qrcodeauthapp.png)

8. Scan the provided code with the Microsoft Authenticator app QR code reader, which appeared on your mobile device after you created your work or school account in Step 6.

    Aplikacja Authenticator powinna pomyślnie dodać konto służbowe bez konieczności podawania żadnych dodatkowych informacji przez użytkownika. Jednak jeśli czytnik kodów QR nie może odczytać kodu, możesz wybrać **link Nie mogę zeskanować kodu QR** oraz ręcznie podać kod i adres URL w aplikacji Microsoft Authenticator. Aby uzyskać więcej informacji na temat ręcznego dodawania kodu, zobacz [Ręcznie dodawanie konta do aplikacji](user-help-auth-app-add-account-manual.md).

9. Wybierz pozycję **Dalej** na stronie **Zeskanuj kod QR** na komputerze.

    Do aplikacji Microsoft Authenticator na urządzeniu przenośnym zostanie wysłane powiadomienie o konieczności przetestowania konta.

    ![Testowanie konta za pomocą aplikacji Authenticator](media/security-info/securityinfo-myprofile-tryitauthapp.png)

10. Zatwierdź powiadomienie w aplikacji Microsoft Authenticator, a następnie wybierz pozycję **Dalej**.

     ![Powiadomienie o powodzeniu łączenia aplikacji z kontem](media/security-info/securityinfo-myprofile-successauthapp.png)

     Informacje zabezpieczające zostały zaktualizowane, tak aby aplikacja Microsoft Authenticator była domyślnie używana do weryfikowania tożsamości podczas korzystania z weryfikacji dwuetapowej lub resetowania hasła.

## <a name="delete-your-authenticator-app-from-your-security-info-methods"></a>Delete your authenticator app from your security info methods

If you no longer want to use your authenticator app as a security info method, you can remove it from the **Security info** page. This works for all authenticator apps, not just the Microsoft Authenticator app. After you delete the app, you'll have to go into the authenticator app on your mobile device and delete the account.

>[!Important]
>If you delete the authenticator app by mistake, there's no way to undo it. You'll have to add the authenticator app again, following the steps in the [Set up the authenticator app](#set-up-the-microsoft-authenticator-app-from-the-security-info-page) section of this article.

### <a name="to-delete-the-authenticator-app"></a>To delete the authenticator app

1. On the **Security info** page, select the **Delete** link next to the Authenticator app.

    ![Link to delete the authenticator app from security info](media/security-info/securityinfo-myprofile-deleteauthapp.png)

2. Select **Yes** from the confirmation box to delete the authenticator app. After the authenticator app is deleted, it's removed from your security info and it disappears from the **Security info** page. If the authenticator app is your default method, the default will change to another available method.

3. Open the authenticator app on your mobile device, select **Edit accounts**, and then delete your work or school account from the authenticator app.

    Your account is completely removed from the authenticator app for two-factor verification and password reset requests.

## <a name="change-your-default-security-info-method"></a>Change your default security info method

If you want the authenticator app to be the default method used when you sign-in to your work or school account using two-factor verification or for password reset requests, you can set it from the Security **info** page.

### <a name="to-change-your-default-security-info-method"></a>To change your default security info method

1. On the **Security info** page, select the **Change** link next to the **Default sign-in method** information.

    ![Change link for default sign-in method](media/security-info/securityinfo-myprofile-changedefaultauthapp.png)

2. Choose **Microsoft Authenticator - notification** from the drop-down list of available methods. If you're not using the Microsoft Authenticator app, select the **Authenticator app or hardware token** option.

    ![Choose method for default sign-in](media/security-info/securityinfo-myprofile-defaultauthapp.png)

3. Select **Confirm**.

    The default method used for sign-in changes to the Microsoft Authenticator app.

## <a name="additional-security-info-methods"></a>Additional security info methods

You have additional options for how your organization contacts you to verify your identity, based on what's you're trying to do. Dostępne są następujące opcje:

- **Mobile device text.** Enter your mobile device number and get a text a code you'll use for two-step verification or password reset. For step-by-step instructions about how to verify your identity with a text message (SMS), see [Set up security info to use text messaging (SMS)](security-info-setup-text-msg.md).

- **Mobile device or work phone call.** Enter your mobile device number and get a phone call for two-step verification or password reset. For step-by-step instructions about how to verify your identity with a phone number, see [Set up security info to use phone calls](security-info-setup-phone-number.md).

- **Security key.** Register your Microsoft-compatible security key and use it along with a PIN for two-step verification or password reset. For step-by-step instructions about how to verify your identity with a security key, see [Set up security info to use a security key](security-info-setup-security-key.md).

- **Email address.** Enter your work or school email address to get an email for password reset. This option isn't available for two-step verification. For step-by-step instructions about how to set up your email, see [Set up security info to use email](security-info-setup-email.md).

- **Security questions.** Answer some security questions created by your administrator for your organization. This option is only available for password reset and not for two-step verification. For step-by-step instructions about how to set up your security questions, see the [Set up security info to use security questions](security-info-setup-questions.md) article.

    >[!Note]
    >If some of these options are missing, it's most likely because your organization doesn't allow those methods. If this is the case, you'll need to choose an available method or contact your administrator for more help.

## <a name="next-steps"></a>Następne kroki

- Sign-in using the Microsoft Authenticator app, following steps in the [Sign in using two-step verification or security info](security-info-setup-signin.md) article.

- Zagubione lub zapomniane hasło możesz zresetować w [portalu resetowania hasła](https://passwordreset.microsoftonline.com/) lub wykonując kroki przedstawione w artykule [Resetowanie hasła służbowego](active-directory-passwords-update-your-own-password.md).

- Wskazówki i pomoc dotyczące rozwiązywania problemów z logowaniem znajdują się w artykule [Nie można zalogować się na konto Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
