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
ms.openlocfilehash: 3e4549a21ec32f1a2c1c869c3b2e0bd8c2e4204e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446566"
---
## <a name="generate-the-certificate-signing-request-file"></a>Generowanie pliku żądania podpisania certyfikatu

Apple Push Notification Service (APNs) używa certyfikatów do uwierzytelniania powiadomień wypychanych. Wykonaj instrukcje, aby utworzyć niezbędny certyfikat powiadomień wypychanych umożliwiający ich wysyłanie i odbieranie. Więcej informacji dotyczących tych pojęć można znaleźć w oficjalnej dokumentacji usługi [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Generowanie pliku żądania podpisania certyfikatu (CSR), który korzysta z firmy Apple do wygenerowania podpisanego certyfikatu powiadomień wypychanych.

1. Na komputerze Mac uruchom narzędzie Keychain Access. Można otworzyć z **narzędzia** folderu lub **innych** folderu na Launchpad.

1. Wybierz **dostęp do pęku kluczy**, rozwiń węzeł **Asystent certyfikatów**, a następnie wybierz pozycję **żądania certyfikatu od urzędu certyfikacji**.

    ![Żądanie nowego certyfikatu za pomocą narzędzia Keychain Access](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

1. Wybierz usługi **adres E-mail użytkownika**, wprowadź usługi **nazwa pospolita** wartość, upewnij się, że podajesz **zapisano na dysku**, a następnie wybierz pozycję **Kontynuuj**. Pozostaw **adres E-mail urzędu certyfikacji** puste, ponieważ nie jest wymagana.

    ![Wymagane informacje o certyfikacie](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. Wprowadź nazwę dla pliku CSR w **Zapisz jako**, wybierz lokalizację na liście **gdzie**, a następnie wybierz pozycję **Zapisz**.

    ![Wybierz nazwę pliku dla certyfikatu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Ta akcja zapisze plik CSR w wybranej lokalizacji. Domyślna lokalizacja to **pulpitu**. Zapamiętaj wybraną lokalizację pliku.

Następnie Zarejestruj swoją aplikację z firmą Apple, Włącz powiadomienia wypychane i przekaż wyeksportowany plik CSR do utworzenia certyfikatu wypychania.

## <a name="register-your-app-for-push-notifications"></a>Rejestrowanie aplikacji dla usługi powiadomień wypychanych

Wypychać powiadomienia do aplikacji systemu iOS, Zarejestruj swoją aplikację z firmą Apple i zarejestrować się w usłudze powiadomień wypychanych.  

1. Jeśli aplikacja nie została jeszcze zarejestrowana, przejdź do [portalu aprowizacji systemu iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456) w Centrum deweloperów firmy Apple. Po tym, zaloguj się przy użyciu identyfikatora Apple ID, wybierz **identyfikatory**, wybierz opcję **identyfikatory aplikacji**, a następnie wybierz polecenie **+** zarejestrować nową aplikację.

    ![Strona identyfikatorów aplikacji w portalu aprowizowania dla systemu iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

1. Zaktualizuj następujące trzy wartości dla nowej aplikacji, a następnie wybierz **Kontynuuj**:

   * **Nazwa**: Wpisz nazwę opisową dla aplikacji **nazwa** pole w **opis Identyfikatora aplikacji** sekcji.

   * **Identyfikator pakietu**: W **jawny identyfikator aplikacji** sekcji, wprowadź **identyfikatora pakietu** formularza `<Organization Identifier>.<Product Name>` zgodnie z opisem w [podręczniku dystrybucji aplikacji](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). *Identyfikator organizacji* i *nazwa produktu* wartości muszą być zgodne organizacji identyfikatora i nazwy produktu można użyć podczas tworzenia projektu Xcode. Poniższy zrzut ekranu *NotificationHubs* wartość jest używana jako identyfikator organizacji i *GetStarted* wartość jest używana jako nazwa produktu. Upewnij się, że **identyfikatora pakietu** wartość pasuje do wartości w projekcie Xcode, dzięki czemu narzędzia Xcode będzie używać poprawnego profilu publikowania.

   * **Powiadomienia wypychane**: Sprawdź **powiadomień wypychanych** opcji **App Services** sekcji.

     ![Formularz rejestracji nowego identyfikatora aplikacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

     Ta akcja generuje Identyfikatora aplikacji i żądania, że potwierdzenie informacji. Wybierz **zarejestrować** aby potwierdzić nowy identyfikator aplikacji.

     Po wybraniu **zarejestrować**, zostanie wyświetlony **rejestracja ukończona** ekranu, jak pokazano na poniższej ilustracji. Wybierz pozycję **Done** (Gotowe).

     ![Ukończona rejestracja identyfikatora aplikacji z wyświetlanymi uprawnieniami](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)

1. W Centrum deweloperów w ramach **identyfikatory aplikacji**, zlokalizuj identyfikator aplikacji, który został utworzony i wybierz jego wiersz.

    ![Lista identyfikatorów aplikacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)

    Wybierz identyfikator aplikacji, aby wyświetlić szczegóły dotyczące aplikacji, a następnie wybierz **Edytuj** znajdujący się u dołu.

    ![Edytowanie strony identyfikatorów aplikacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

1. Przewiń w dół ekranu, a następnie wybierz pozycję **Tworzenie certyfikatu** przycisku w obszarze **certyfikat SSL wypychania rozwoju** sekcji.

    ![Przycisk tworzenia certyfikatu dla identyfikatora aplikacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Pojawi się informacja **Dodaj certyfikat iOS** Asystenta ustawień.

    > [!NOTE]
    > Instrukcje w tym samouczku obejmują użycie certyfikatu deweloperskiego. Ten sam proces jest używany podczas rejestrowania certyfikatu produkcyjnego. Należy po prostu pamiętać, aby używać tego samego typu certyfikatu podczas wysyłania powiadomień.

1. Wybierz **wybierz plik**, przejdź do lokalizacji, w którym został zapisany plik CSR z pierwszego zadania, a następnie wybierz **Generuj**.

    ![Strona przekazywania wygenerowanego pliku CSR certyfikatu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

1. Po utworzeniu portalu certyfikat, wybierz **Pobierz** przycisk, a następnie wybierz pozycję **gotowe**.

    ![Strona pobierania wygenerowanego certyfikatu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Certyfikat jest pobierane i zapisywane na komputerze użytkownika w Twojej **pliki do pobrania** folderu.

    ![Lokalizowanie pliku certyfikatu w folderze Pobrane](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Domyślnie, nosi nazwę certyfikatu deweloperskiego pobrany **aps_development.cer**.

1. Zaznacz pobrany certyfikat powiadomień wypychanych **aps_development.cer**.

    Ta akcja powoduje zainstalowanie nowego certyfikatu w narzędziu Keychain, jak przedstawiono na poniższym rysunku:

    ![Lista certyfikatów narzędzia Keychain Access z nowym certyfikatem](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Chociaż nazwa w certyfikacie mogą się różnić, nazwa będzie zaczynała się od **Apple Development iOS Push Services**.

1. W narzędziu Keychain Access kliknij prawym przyciskiem myszy nowy certyfikat powiadomień wypychanych utworzony w kategorii **Certyfikaty**. Wybierz **wyeksportować**, nadaj plikowi nazwę, wybierz **.p12** formatowania, a następnie wybierz **Zapisz**.

    ![Eksportowanie certyfikatu w formacie p12](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Zanotuj nazwę pliku i lokalizację wyeksportowanego certyfikatu .p12. Służą one do włączenia uwierzytelniania za pomocą usługi APNs.

    > [!NOTE]
    > Ten samouczek tworzy plik o nazwie **QuickStart.p12**. Nazwa i lokalizacja pliku mogą się różnić.

## <a name="create-a-provisioning-profile-for-the-app"></a>Tworzenie profilu inicjowania obsługi dla aplikacji

1. W [portalu aprowizacji systemu iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456), wybierz opcję **profilów aprowizacji**, wybierz opcję **wszystkich**, a następnie wybierz pozycję **+** do utworzenia nowy profil. Zostanie wyświetlony **dodać profil aprowizacji systemu iOS** kreatora.

    ![Lista profili aprowizacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Wybierz **programowanie aplikacji dla systemu iOS** w obszarze **rozwoju** jako aprowizacji typ profilu, a następnie wybierz pozycję **Kontynuuj**.

1. Następnie wybierz identyfikator aplikacji, utworzonym na podstawie **Identyfikatora aplikacji** listy rozwijanej i wybierz pozycję **Kontynuuj**.

    ![Wybieranie identyfikatora aplikacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. W **wybierz certyfikaty** oknie Wybierz certyfikat programowania zwykle używany do podpisywania kodu, a następnie wybierz **Kontynuuj**. Ten certyfikat nie jest utworzony certyfikat wypychania.

    ![Wybieranie certyfikatu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)

1. Następnie wybierz urządzenia na potrzeby testowania i wybierz **Kontynuuj**.

    ![Wybieranie urządzeń](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)

1. Na koniec wybierz nazwę profilu w **nazwa profilu**i wybierz **Generuj**.

    ![Wybieranie nazwy profilu aprowizacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. Po utworzeniu nowego profilu aprowizowania, należy wybrać opcję Pobierz i zainstaluj go na komputerze deweloperskim programu Xcode. Następnie wybierz pozycję **Done** (Gotowe).

    ![Pobieranie profilu aprowizacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)

## <a name="create-a-notification-hub"></a>Tworzenie centrum powiadomień

W tej sekcji Tworzenie Centrum powiadomień i skonfigurować uwierzytelnianie za pomocą usługi APNs przy użyciu certyfikatu wypychania p12, która została wcześniej utworzona. Jeśli chcesz użyć Centrum powiadomień, która została już utworzona, możesz przejść do kroku 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>Konfigurowanie Centrum powiadomień przy użyciu informacji z usługi APNs

1. W obszarze **Usługi powiadomień** wybierz pozycję **Apple (APNS)** .

1. Wybierz pozycję **Certyfikat**.

1. Wybierz ikonę pliku.

1. Wybierz wcześniej wyeksportowany plik p12.

1. Określ prawidłowe hasło.

1. Wybierz tryb **Piaskownica**. Trybu **Produkcja** używaj wyłącznie wtedy, gdy chcesz wysyłać powiadomienia push do użytkowników, którzy kupili Twoją aplikację w sklepie.

    ![Konfigurowanie certyfikacji APNs w witrynie Azure Portal](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

Twoje Centrum powiadomień zostało teraz skonfigurowane za pomocą usługi APNs. Masz również parametry połączenia do rejestrowania aplikacji oraz wysyłać powiadomienia wypychane.
