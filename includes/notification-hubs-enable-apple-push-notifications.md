---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 08/28/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2fc4f26f187301ea7a7a1e3051038f75da728547
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60426687"
---
## <a name="generate-the-certificate-signing-request-file"></a>Generowanie pliku żądania podpisania certyfikatu

Usługa Apple Push Notification Service (APNS) używa certyfikatów do uwierzytelniania powiadomień wypychanych. Wykonaj instrukcje, aby utworzyć niezbędny certyfikat powiadomień wypychanych umożliwiający ich wysyłanie i odbieranie. Więcej informacji dotyczących tych pojęć można znaleźć w oficjalnej dokumentacji usługi [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Wygeneruj plik żądania podpisania certyfikatu (CSR), który jest używany przez firmę Apple do wygenerowania podpisanego certyfikatu powiadomień wypychanych.

1. Na komputerze Mac uruchom narzędzie **Keychain Access**. Można je otworzyć z folderu **Narzędzia** lub folderu **Inne** na konsoli uruchamiania.
2. Kliknij opcję **Keychain Access**, rozwiń węzeł **Asystent certyfikatu**, a następnie kliknij opcję **Żądaj certyfikatu od urzędu certyfikacji...**.

    ![Żądanie nowego certyfikatu za pomocą narzędzia Keychain Access](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)
3. Wybierz wartości pól **Adres e-mail użytkownika** i **Nazwa pospolita**, upewnij się, że pole **Zapisano na dysku** zostało zaznaczone, a następnie kliknij przycisk **Kontynuuj**. Pozostaw pole **Adres e-mail urzędu certyfikacji** puste, ponieważ nie jest wymagane.

    ![Wymagane informacje o certyfikacie](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

4. Wpisz nazwę pliku żądania podpisania certyfikatu (CSR) w polu **Zapisz jako**, wybierz lokalizację na liście **Gdzie**, następnie kliknij przycisk **Zapisz**.

    ![Wybieranie nazwy pliku certyfikatu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Ta akcja powoduje zapisanie pliku CSR w wybranej lokalizacji; lokalizacja domyślna znajduje się na pulpicie. Zapamiętaj wybraną lokalizację pliku.

Następnie zarejestrujesz aplikację w firmie Apple, włączysz powiadomienia push i przekażesz wyeksportowany plik CSR, aby utworzyć certyfikat wypychania.

## <a name="register-your-app-for-push-notifications"></a>Rejestrowanie aplikacji dla usługi powiadomień wypychanych

Aby móc wysyłać powiadomienia wypychane do aplikacji dla systemu iOS, należy zarejestrować aplikację w firmie Apple oraz zarejestrować się w usłudze powiadomień wypychanych.  

1. Jeśli aplikacja nie została jeszcze zarejestrowana, przejdź do [portalu inicjowania obsługi systemu iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456) w Centrum deweloperów firmy Apple, zaloguj się za pomocą identyfikatora firmy Apple, kliknij opcję **Identyfikatory**, następnie kliknij opcję **Identyfikatory aplikacji**, a na koniec kliknij znak **+**, aby zarejestrować nową aplikację.

    ![Strona identyfikatorów aplikacji w portalu aprowizowania dla systemu iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. Zaktualizuj następujące trzy pola dla nowej aplikacji, a następnie kliknij przycisk **Kontynuuj**:

   * **Nazwa**: Wpisz nazwę opisową dla aplikacji **nazwa** pole **opis Identyfikatora aplikacji** sekcji.
   * **Identyfikator pakietu**: W obszarze **jawny identyfikator aplikacji** sekcji, wprowadź **identyfikatora pakietu** w formie `<Organization Identifier>.<Product Name>` zgodnie z opisem w [podręczniku dystrybucji aplikacji](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). *Identyfikator organizacji* i *Nazwa produktu*, których używasz, muszą być zgodne z identyfikatorem organizacji i nazwą produktu, których używasz podczas tworzenia projektu w programie XCode. Na poniższym zrzucie ekranu pozycja *NotificationHubs* stanowi identyfikator organizacji, a *GetStarted* nazwę produktu. Zagwarantowanie, że wartość ta jest zgodna z wartością używaną w projekcie w programie XCode, pozwoli na użycie poprawnego profilu publikowania w programie XCode.
   * **Powiadomienia wypychane**: Sprawdź **powiadomień wypychanych** opcji **App Services** sekcji.

     ![Formularz rejestracji nowego identyfikatora aplikacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

     Ta akcja spowoduje wygenerowanie identyfikatora aplikacji, a użytkownik zostanie poproszony o potwierdzenie podanych informacji. Kliknij przycisk **Zarejestruj**, aby potwierdzić nowy identyfikator aplikacji.

     Po kliknięciu przycisku **Zarejestruj** jest wyświetlany ekran **Rejestracja ukończona**, jak przedstawiono na poniższym rysunku. Kliknij przycisk **Gotowe**.

     ![Ukończona rejestracja identyfikatora aplikacji z wyświetlanymi uprawnieniami](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)

3. W Centrum deweloperów w obszarze Identyfikatory aplikacji zlokalizuj utworzony identyfikator aplikacji i kliknij jego wiersz.

    ![Lista identyfikatorów aplikacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)

    Kliknięcie identyfikatora aplikacji powoduje wyświetlenie jej szczegółów. Kliknij przycisk **Edytuj** widoczny u dołu.

    ![Edytowanie strony identyfikatorów aplikacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

4. Przewiń w dół ekranu, a następnie kliknij przycisk **Utwórz certyfikat...** w sekcji **Certyfikat SSL deweloperskich powiadomień wypychanych**.

    ![Przycisk tworzenia certyfikatu dla identyfikatora aplikacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Zostanie wyświetlony **Asystent usługi „Dodaj certyfikat iOS”**.

    > [!NOTE]
    > Instrukcje w tym samouczku obejmują użycie certyfikatu deweloperskiego. Ten sam proces jest używany podczas rejestrowania certyfikatu produkcyjnego. Należy po prostu pamiętać, aby używać tego samego typu certyfikatu podczas wysyłania powiadomień.

5. Kliknij opcję **Wybierz plik**, przejdź do lokalizacji, w której został zapisany plik CSR utworzony w pierwszym zadaniu, a następnie kliknij przycisk **Generuj**.

    ![Strona przekazywania wygenerowanego pliku CSR certyfikatu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

6. Po utworzeniu certyfikatu przez portal kliknij przycisk **Pobierz** a następnie kliknij przycisk **Gotowe**.

    ![Strona pobierania wygenerowanego certyfikatu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Powoduje to pobranie certyfikatu i zapisanie go na komputerze w folderze **Pobrane**.

    ![Lokalizowanie pliku certyfikatu w folderze Pobrane](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Domyślnie pobrany plik certyfikatu deweloperskiego nosi nazwę **aps_development.cer**.

7. Kliknij dwukrotnie pobrany certyfikat powiadomień wypychanych **aps_development.cer**.

    Ta akcja powoduje zainstalowanie nowego certyfikatu w narzędziu Keychain, jak przedstawiono na poniższym rysunku:

    ![Lista certyfikatów narzędzia Keychain Access z nowym certyfikatem](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Nazwa w certyfikacie może być inna, ale będzie ona poprzedzona ciągiem **Apple Development iOS Push Services**.

8. W narzędziu Keychain Access kliknij prawym przyciskiem myszy nowy certyfikat powiadomień wypychanych utworzony w kategorii **Certyfikaty**. Kliknij opcję **Eksportuj**, nazwij plik, wybierz format **.p12**, a następnie kliknij przycisk **Zapisz**.

    ![Eksportowanie certyfikatu w formacie p12](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Zanotuj nazwę pliku i lokalizację wyeksportowanego certyfikatu .p12. Jest on używany do włączania uwierzytelniania za pomocą usługi APNS.

    > [!NOTE]
    > W tym samouczku opisano proces tworzenia pliku QuickStart.p12. Nazwa i lokalizacja pliku mogą się różnić.

## <a name="create-a-provisioning-profile-for-the-app"></a>Tworzenie profilu inicjowania obsługi dla aplikacji

1. Po powrocie do [portalu aprowizowania dla systemu iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456) wybierz pozycję **Profile aprowizacji**, wybierz pozycję **Wszystkie**, a następnie kliknij przycisk **+** (plus), aby utworzyć nowy profil. Zostanie wyświetlony kreator **dodawania profilu aprowizacji w systemie iOS**:

    ![Lista profili aprowizacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

2. Wybierz pozycję **Tworzenie aplikacji dla systemu iOS** w obszarze **Programowanie** jako typ profilu aprowizacji i kliknij przycisk **Kontynuuj**.

3. Następnie wybierz utworzony identyfikator aplikacji z listy rozwijanej **Identyfikator aplikacji** i kliknij przycisk **Kontynuuj**

    ![Wybieranie identyfikatora aplikacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

4. Na ekranie **Wybierz certyfikaty** wybierz certyfikat programowania zwykle używany do podpisywania kodu, a następnie kliknij przycisk **Kontynuuj**. Ten certyfikat jest inny niż utworzony certyfikat wypychania.

    ![Wybieranie certyfikatu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)

5. Następnie wybierz pozycję **Urządzenia**, aby użyć jej do testowania, po czym kliknij przycisk **Kontynuuj**.

    ![Wybieranie urządzeń](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)

6. Na koniec wybierz nazwę profilu na karcie **Nazwa profilu** i kliknij przycisk **Generuj**.

    ![Wybieranie nazwy profilu aprowizacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

7. Podczas tworzenia nowego profilu inicjowania obsługi kliknij go, aby go pobrać i zainstalować na komputerze deweloperskim z zainstalowanym programem Xcode. Następnie kliknij przycisk **Gotowe**.

    ![Pobieranie profilu aprowizacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)
