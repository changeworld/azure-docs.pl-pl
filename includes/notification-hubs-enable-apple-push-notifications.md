---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 11/21/2019
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: ef2b98821b28d8a49e5f16bf1c6ac176eb8b5793
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74407258"
---
## <a name="generate-the-certificate-signing-request-file"></a>Generuj plik żądania podpisania certyfikatu

Apple Push Notification Service (APNs) używa certyfikatów do uwierzytelniania powiadomień wypychanych. Wykonaj instrukcje, aby utworzyć niezbędny certyfikat powiadomień wypychanych umożliwiający ich wysyłanie i odbieranie. Więcej informacji dotyczących tych pojęć można znaleźć w oficjalnej dokumentacji usługi [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Wygeneruj plik żądania podpisania certyfikatu (CSR), którego firma Apple używa do generowania podpisanego certyfikatu wypychania.

1. Na komputerze Mac uruchom narzędzie Keychain Access. Można go otworzyć z folderu **Utilities** lub **innego** folderu w programie Launchpad.

1. Wybierz pozycję **dostęp do łańcucha kluczy**, rozwiń węzeł **Asystent certyfikatów**, a następnie wybierz pozycję **Żądaj certyfikatu z urzędu certyfikacji**.

    ![Żądanie nowego certyfikatu za pomocą narzędzia Keychain Access](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

1. Wybierz **adres E-mail użytkownika**, wprowadź wartość **Nazwa pospolita** , upewnij się, że określono opcję **Zapisano na dysku**, a następnie wybierz pozycję **Kontynuuj**. Pozostaw puste **adresy E-mail urzędu certyfikacji** , ponieważ nie jest to wymagane.

    ![Wymagane informacje o certyfikacie](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. Wprowadź nazwę pliku CSR w obszarze **Zapisz jako**, wybierz lokalizację w **miejscu**, a następnie wybierz pozycję **Zapisz**.

    ![Wybierz nazwę pliku dla certyfikatu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Ta akcja zapisuje plik CSR w wybranej lokalizacji. Domyślna lokalizacja to **Desktop**. Zapamiętaj wybraną lokalizację pliku.

Następnie zarejestruj aplikację za pomocą firmy Apple, Włącz powiadomienia wypychane i przekaż wyeksportowany certyfikat CSR w celu utworzenia certyfikatu wypychania.

## <a name="register-your-app-for-push-notifications"></a>Rejestrowanie aplikacji dla usługi powiadomień wypychanych

Aby wysyłać powiadomienia wypychane do aplikacji systemu iOS, zarejestruj swoją aplikację za pomocą firmy Apple, a także Zarejestruj się w celu otrzymywania powiadomień wypychanych.  

1. Jeśli aplikacja nie została jeszcze zarejestrowana, przejdź do [portalu aprowizacji systemu iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456) w centrum deweloperów firmy Apple. Zaloguj się do portalu przy użyciu identyfikatora Apple ID, a następnie wybierz pozycję **identyfikatory**. Następnie wybierz pozycję **+** , aby zarejestrować nową aplikację.

    ![Strona identyfikatorów aplikacji w portalu aprowizowania dla systemu iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. Na ekranie **Rejestrowanie nowego identyfikatora** wybierz przycisk radiowy **identyfikatory aplikacji** . Następnie wybierz pozycję **Kontynuuj**.

    ![nowy identyfikator strony rejestracji w portalu aprowizacji systemu iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids-new.png)

3. Zaktualizuj następujące trzy wartości dla nowej aplikacji, a następnie wybierz pozycję **Kontynuuj**:

   * **Opis**: wpisz nazwę opisową aplikacji.

   * **Identyfikator pakietu**: Wprowadź identyfikator pakietu formularza **identyfikator organizacji. Nazwa produktu** , jak wspomniano w [przewodniku po dystrybucji aplikacji](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). *Identyfikator organizacji* i wartości *nazwy produktu* muszą być zgodne z identyfikatorem organizacji i nazwą produktu używaną podczas tworzenia projektu Xcode. Na poniższym zrzucie ekranu wartość **NotificationHubs** jest używana jako identyfikator organizacji, a wartość **getstarted** jest używana jako nazwa produktu. Upewnij się, że wartość **identyfikatora pakietu** pasuje do wartości w projekcie Xcode, tak aby Xcode używała poprawnego profilu publikacji.

      ![Strona identyfikatora aplikacji w portalu aprowizacji systemu iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-bundle.png)

   * **Powiadomienia wypychane**: zaznacz opcję **powiadomienia wypychane** w sekcji **możliwości** .

      ![Formularz rejestracji nowego identyfikatora aplikacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-push.png)

      Ta akcja spowoduje wygenerowanie identyfikatora aplikacji i żądań potwierdzających informacje. Wybierz pozycję **Kontynuuj**, a następnie wybierz pozycję **zarejestruj** , aby potwierdzić nowy identyfikator aplikacji.

      ![Potwierdź nowy identyfikator aplikacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-register.png)

      Po wybraniu pozycji **zarejestruj**nowy identyfikator aplikacji zostanie wyświetlony jako element wiersza na stronie **certyfikaty, identyfikatory & profile** .

4. Na stronie **certyfikaty, identyfikatory & profile** w obszarze **identyfikatory**Znajdź element wiersza identyfikatora aplikacji, który został właśnie utworzony, a następnie wybierz jego wiersz w celu wyświetlenia ekranu **Edytowanie konfiguracji identyfikatora aplikacji** .

5. Przewiń w dół do opcji sprawdzone **powiadomienia wypychane** , a następnie wybierz pozycję **Konfiguruj** , aby utworzyć certyfikat.

    ![Edytowanie strony identyfikatorów aplikacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

6. Zostanie wyświetlone okno **Certyfikaty SSL usługi powiadomień wypychanych firmy Apple** . Wybierz przycisk **Utwórz certyfikat** w sekcji **certyfikat protokołu SSL dla deweloperów** .

    ![Przycisk tworzenia certyfikatu dla identyfikatora aplikacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Zostanie wyświetlony ekran **Utwórz nowy certyfikat** .

    > [!NOTE]
    > Instrukcje w tym samouczku obejmują użycie certyfikatu deweloperskiego. Ten sam proces jest używany podczas rejestrowania certyfikatu produkcyjnego. Należy po prostu pamiętać, aby używać tego samego typu certyfikatu podczas wysyłania powiadomień.

1. Wybierz pozycję **Wybierz plik**, przejdź do lokalizacji, w której ZAPISANO plik CSR z pierwszego zadania, a następnie kliknij dwukrotnie nazwę certyfikatu w celu jego załadowania. Następnie wybierz pozycję **Kontynuuj**.

1. Po utworzeniu certyfikatu przez portal wybierz przycisk **Pobierz** . Zapisz certyfikat i Zapamiętaj lokalizację, w której został zapisany.

    ![Strona pobierania wygenerowanego certyfikatu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Certyfikat zostanie pobrany i zapisany na komputerze w folderze **pobierania** .

    ![Lokalizowanie pliku certyfikatu w folderze Pobrane](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Domyślnie pobrany certyfikat programistyczny nosi nazwę **aps_development. cer**.

1. Kliknij dwukrotnie pobrany certyfikat powiadomień wypychanych **aps_development.cer**. Ta akcja powoduje zainstalowanie nowego certyfikatu w narzędziu Keychain, jak przedstawiono na poniższym rysunku:

    ![Lista certyfikatów narzędzia Keychain Access z nowym certyfikatem](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Mimo że nazwa w certyfikacie może być inna, nazwa zostanie poprzedzona przez **Apple Development iOS usługi wypychania**.

1. W narzędziu Keychain Access kliknij prawym przyciskiem myszy nowy certyfikat powiadomień wypychanych utworzony w kategorii **Certyfikaty**. Wybierz pozycję **Eksportuj**, Nazwij plik, wybierz format **. p12** , a następnie wybierz pozycję **Zapisz**.

    ![Eksportowanie certyfikatu w formacie p12](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Możesz zdecydować się na ochronę certyfikatu hasłem, ale jest to opcjonalne. Kliknij przycisk **OK** , jeśli chcesz pominąć tworzenie haseł. Zanotuj nazwę pliku i lokalizację wyeksportowanego certyfikatu .p12. Są one używane do włączania uwierzytelniania przy użyciu usługi APNs.

    > [!NOTE]
    > Nazwa i lokalizacja pliku. p12 mogą różnić się od informacji w tym samouczku.

## <a name="create-a-provisioning-profile-for-the-app"></a>Tworzenie profilu inicjowania obsługi dla aplikacji

1. Wróć do [portalu aprowizacji systemu iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456), wybierz pozycję **certyfikaty, identyfikatory & profile**, wybierz pozycję **Profile** z menu po lewej stronie, a następnie wybierz pozycję **+** , aby utworzyć nowy profil. Zostanie wyświetlony ekran **Zarejestruj nowy profil aprowizacji** .

1. Wybierz pozycję **Programowanie aplikacji dla systemu iOS** w obszarze **programowanie** jako typ profilu aprowizacji, a następnie wybierz pozycję **Kontynuuj**.

    ![Lista profili aprowizacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Następnie wybierz identyfikator aplikacji utworzony na podstawie listy rozwijanej **Identyfikator aplikacji** , a następnie wybierz pozycję **Kontynuuj**.

    ![Wybieranie identyfikatora aplikacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. W oknie **Wybieranie certyfikatów** wybierz certyfikat programistyczny używany do podpisywania kodu, a następnie wybierz pozycję **Kontynuuj**. Ten certyfikat nie jest utworzonym certyfikatem wypychania. Jeśli taki nie istnieje, należy go utworzyć. Jeśli certyfikat istnieje, przejdź do następnego kroku. Aby utworzyć certyfikat programistyczny, jeśli taki nie istnieje:

    1. Jeśli widzisz **Brak dostępnych certyfikatów**, wybierz pozycję **Utwórz certyfikat**.
    2. W sekcji **oprogramowanie** wybierz pozycję **programowanie firmy Apple**. Następnie wybierz pozycję **Kontynuuj**.
    3. Na ekranie **Tworzenie nowego certyfikatu** wybierz pozycję **Wybierz plik**.
    4. Przejdź do utworzonego wcześniej certyfikatu **żądania podpisania certyfikatu** , zaznacz go, a następnie wybierz pozycję **Otwórz**.
    5. Wybierz przycisk **Kontynuuj**.
    6. Pobierz certyfikat programistyczny i Zapamiętaj lokalizację, w której został zapisany.

1. Wróć do strony **certyfikaty, identyfikatory & profile** , wybierz pozycję **Profile** z menu po lewej stronie, a następnie wybierz pozycję **+** , aby utworzyć nowy profil. Zostanie wyświetlony ekran **Zarejestruj nowy profil aprowizacji** .

1. W oknie **Wybieranie certyfikatów** wybierz właśnie utworzony certyfikat programistyczny. Następnie wybierz pozycję **Kontynuuj**.

1. Następnie wybierz urządzenia, które mają być używane do testowania, a następnie wybierz pozycję **Kontynuuj**.

1. Na koniec wybierz nazwę profilu w polu **Nazwa profilu aprowizacji**, a następnie wybierz pozycję **Generuj**.

    ![Wybieranie nazwy profilu aprowizacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. Po utworzeniu nowego profilu aprowizacji wybierz pozycję **Pobierz**. Zapamiętaj lokalizację, w której została zapisana.

1. Przejdź do lokalizacji profilu aprowizacji, a następnie kliknij go dwukrotnie, aby go zainstalować na maszynie deweloperskiej Xcode.

## <a name="create-a-notification-hub"></a>Tworzenie centrum powiadomień

W tej sekcji utworzysz centrum powiadomień i skonfigurujesz uwierzytelnianie za pomocą usługi APNs przy użyciu utworzonego wcześniej certyfikatu wypychania. p12. Jeśli chcesz użyć już utworzonego centrum powiadomień, możesz przejść do kroku 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>Konfigurowanie centrum powiadomień przy użyciu informacji dotyczących usługi APNs

1. W obszarze **Usługi powiadomień** wybierz pozycję **Apple (APNS)** .

1. Wybierz pozycję **Certyfikat**.

1. Wybierz ikonę pliku.

1. Wybierz wyeksportowany wcześniej plik. P12, a następnie wybierz pozycję **Otwórz**.

1. W razie potrzeby określ poprawne hasło.

1. Wybierz tryb **Piaskownica**. Trybu **Produkcja** używaj wyłącznie wtedy, gdy chcesz wysyłać powiadomienia push do użytkowników, którzy kupili Twoją aplikację w sklepie.

    ![Konfigurowanie certyfikacji APNs w witrynie Azure Portal](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

1. Wybierz pozycję **Zapisz**.

Twoje centrum powiadomień zostało już skonfigurowane za pomocą usługi APNs. Istnieją także parametry połączenia do rejestrowania aplikacji i wysyłania powiadomień wypychanych.
