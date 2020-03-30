---
title: Konfigurowanie powiadomień wypychanych w centrach powiadomień platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować usługi Azure Notification Hubs w witrynie Azure portal przy użyciu ustawień systemu powiadomień platformy (PNS).
services: notification-hubs
author: sethmanheim
manager: femila
editor: dbradish-microsoft
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: 78afb124ee1d1ab9b212197fb7a7140f88de9940
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349509"
---
# <a name="quickstart-set-up-push-notifications-in-a-notification-hub"></a>Szybki start: konfigurowanie powiadomień wypychanych w Centrum powiadomień

Usługa Azure Notification Hubs udostępnia aparat wypychania, który jest łatwy w użyciu i który jest skalowany w poziomie. Centra powiadomień za pomocą centrów powiadomień będą wysyłać powiadomienia na dowolną platformę (iOS, Android, Windows, Baidu) i z dowolnego zaplecza (chmura lub lokalnie). Aby uzyskać więcej informacji, zobacz [Co to są usługi Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

W tym przewodniku Szybki start użyjesz ustawień systemu powiadomień platformy (PNS) w Centrach powiadomień, aby skonfigurować powiadomienia wypychane na wielu platformach. Przewodnik Szybki start pokazuje kroki, które należy wykonać w witrynie Azure portal.  [Usługa Google Firebase Cloud Messaging](/azure/notification-hubs/configure-notification-hub-portal-pns-settings?tabs=azure-cli#google-firebase-cloud-messaging-fcm) zawiera instrukcje dotyczące korzystania z interfejsu wiersza polecenia platformy Azure.

Jeśli centrum powiadomień nie zostało jeszcze utworzone, utwórz je teraz. Aby uzyskać więcej informacji, zobacz [Tworzenie centrum powiadomień platformy Azure w portalu Azure](create-notification-hub-portal.md) lub tworzenie centrum [powiadomień platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](create-notification-hub-azure-cli.md)

## <a name="apple-push-notification-service"></a>Apple Push Notification Service

Aby skonfigurować usługę powiadomień push (APNS) firmy Apple:

1. W witrynie Azure portal na stronie **Centrum powiadomień** wybierz pozycję **Apple (APNS)** z lewego menu.

1. W **trybie uwierzytelniania**wybierz **certyfikat** lub **token**.

   a. W przypadku **wybrania opcji Certyfikat**:
   * Wybierz ikonę pliku, a następnie wybierz plik *p12,* który chcesz przekazać.
   * Wprowadź hasło.
   * Wybierz tryb **Piaskownica**. Aby wysłać powiadomienia wypychane do użytkowników, którzy zakupili aplikację ze sklepu, wybierz tryb **produkcji.**

     ![Zrzut ekranu przedstawiający konfigurację certyfikatu APNS w witrynie Azure portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. Jeśli wybierzesz **Token:**

   * Wprowadź wartości **identyfikatora klucza,** **identyfikatora pakietu,** **identyfikatora zespołu**i **tokenu.**
   * Wybierz tryb **Piaskownica**. Aby wysłać powiadomienia wypychane do użytkowników, którzy zakupili aplikację ze sklepu, wybierz tryb **produkcji.**

     ![Zrzut ekranu przedstawiający konfigurację tokenu APNS w witrynie Azure portal](./media/configure-notification-hub-portal-pns-settings/notification-hubs-apple-config-token.png)

Aby uzyskać więcej informacji, zobacz [Powiadomienia wypychania do systemu iOS przy użyciu usługi Azure Notification Hubs](notification-hubs-ios-apple-push-notification-apns-get-started.md).

## <a name="google-firebase-cloud-messaging-fcm"></a>Google Firebase Cloud Messaging (FCM)

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby skonfigurować powiadomienia push dla Google FCM:

1. W witrynie Azure portal na stronie **Centrum powiadomień** wybierz pozycję **Google (GCM/FCM)** z lewego menu.
2. Wklej **klucz interfejsu API** dla projektu Google FCM, który został zapisany wcześniej.
3. Wybierz **pozycję Zapisz**.

   ![Zrzut ekranu przedstawiający sposób konfigurowania centrów powiadomień dla Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

Po wykonaniu tych kroków alert wskazuje, że centrum powiadomień zostało pomyślnie zaktualizowane. Przycisk **Save** (Zapisz) będzie wyłączony.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

### <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy mieć następujące właściwości:

* Interfejsu [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w wersji 2.0.67 lub nowszej.

* Rozszerzenie interfejsu wiersza polecenia platformy Azure [dla centrów powiadomień](/cli/azure/ext/notification-hub/notification-hub).
* **Klucz interfejsu API** dla projektu google firebase cloud messaging (FCM).

### <a name="set-up-push-notifications-for-google-fcm"></a>Konfigurowanie powiadomień wypychanych dla Google FCM

1. Użyj polecenia [aktualizacji poświadczeń az-hub,](/cli/azure/ext/notification-hub/notification-hub/credential/gcm#ext-notification-hub-az-notification-hub-credential-gcm-update) aby dodać klucz interfejsu API Google do Centrum powiadomień.

   ```azurecli
   az notification-hub credential gcm update --resource-group spnhubrg --namespace-name spnhubns    --notification-hub-name spfcmtutorial1nhub --google-api-key myKey
   ```

2. Aplikacja systemu Android potrzebuje ciągu połączenia, aby połączyć się z centrum powiadomień.  Użyj polecenia [az notification-hub authorization-rule,](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) aby wyświetlić listę dostępnych zasad dostępu.  Użyj polecenia [az notification-hub authorization-rule list-keys,](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) aby uzyskać parametry połączenia zasad dostępu.  Określ **primaryConnectionString** lub **secondaryConnectionString** w parametrze, `--query` aby uzyskać bezpośredni ciąg połączenia podstawowego.

   ```azurecli
   #list access policies for a notification hub
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table

   #list keys and connection strings for a notification hub access policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name myAccessPolicyName --output json

   #get the primaryConnectionString for an access policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name myAccessPolicyName --query primaryConnectionString
   ```

3. Użyj polecenia [az notification-hub test-send,](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-test-send) aby przetestować wysyłanie wiadomości do aplikacji na Androida.

   ```azurecli
   #test with message body
   az notification-hub test-send --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --notification-format gcm --message "my message body"

   #test with JSON string
   az notification-hub test-send --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --notification-format gcm --payload "{\"data\":{\"message\":\"my JSON string\"}}"
   ```

Pobierz odwołania do interfejsu wiersza polecenia platformy Azure dla innych platform za pomocą polecenia [poświadczeń az notification-hub.](/cli/azure/ext/notification-hub/notification-hub/credential)

---

Aby uzyskać więcej informacji na temat przekazywania powiadomień do aplikacji na Androida, zobacz [Wysyłanie powiadomień wypychanych do urządzeń z systemem Android za pomocą Firebase](notification-hubs-android-push-notification-google-fcm-get-started.md).

## <a name="windows-push-notification-service"></a>Usługa powiadomień wypychaowych systemu Windows

Aby skonfigurować usługę powiadomień wypychaniem systemu Windows (WNS):

1. W witrynie Azure portal na stronie **Centrum powiadomień** wybierz pozycję **Windows (WNS)** z lewego menu.
2. Wprowadź wartości w polach **Identyfikator SID pakietu** i **Klucz zabezpieczeń**.
3. Wybierz **pozycję Zapisz**.

   ![Zrzut ekranu przedstawiający pola Identyfikator SID pakietu i klucz zabezpieczeń](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

Aby uzyskać więcej informacji, zobacz [Wysyłanie powiadomień do aplikacji platformy uniwersalnej systemu Windows przy użyciu usługi Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

## <a name="microsoft-push-notification-service-for-windows-phone"></a>Usługa powiadomień wypychaowych firmy Microsoft dla systemu Windows Phone

Aby skonfigurować usługę mpns (Microsoft Push Notification Service) dla systemu Windows Phone:

1. W witrynie Azure portal na stronie **Centrum powiadomień** wybierz pozycję **Windows Phone (MPNS)** z lewego menu.
1. Włącz nieuwierzyciowane lub uwierzytelnione powiadomienia wypychane:

   a. Aby włączyć nieuwierzyte powiadomienia wypychane, wybierz **pozycję Włącz nieuwierzytyny wypychacz** > **Zapisz**.

      ![Zrzut ekranu przedstawiający sposób włączania nieuwierzywalszych powiadomień wypychanych](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Aby włączyć uwierzytelnione powiadomienia wypychane:
      * Na pasku narzędzi wybierz pozycję **Przekaż certyfikat**.
      * Wybierz ikonę pliku, a następnie wybierz plik certyfikatu.
      * Wprowadź hasło certyfikatu.
      * Kliknij przycisk **OK**.
      * Na stronie **Windows Phone (MPNS)** wybierz pozycję **Zapisz**.

Aby uzyskać więcej informacji, zobacz [Przekazywanie powiadomień do aplikacji systemu Windows Phone przy użyciu Centrów powiadomień](notification-hubs-windows-mobile-push-notifications-mpns.md).

## <a name="baidu-android-china"></a>Baidu (Android China)

Aby skonfigurować powiadomienia wypychane dla Baidu:

1. W witrynie Azure portal na stronie **Centrum powiadomień** wybierz **polecenie Baidu (Android China)** z lewego menu.
2. Wprowadź **klucz api uzyskany** z konsoli Baidu w projekcie wypychania w chmurze Baidu.
3. Wprowadź **tajny klucz** uzyskany z konsoli Baidu w projekcie wypychania w chmurze Baidu.
4. Wybierz **pozycję Zapisz**.

    ![Zrzut ekranu przedstawiający centra powiadomień, który pokazuje konfigurację Baidu (Android China) dla powiadomień wypychanych](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

Po wykonaniu tych kroków alert wskazuje, że centrum powiadomień zostało pomyślnie zaktualizowane. Przycisk **Save** (Zapisz) będzie wyłączony.

Aby uzyskać więcej informacji, zobacz [Wprowadzenie do centrów powiadomień przy użyciu programu Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak skonfigurować ustawienia systemu powiadomień platformy dla centrum powiadomień w witrynie Azure portal.

Aby dowiedzieć się więcej o tym, jak wypychać powiadomienia na różne platformy, zobacz następujące samouczki:

-[Powiadomienia wypychane na urządzenia z systemem iOS za pomocą centrów powiadomień powiadomień APNS](notification-hubs-ios-apple-push-notification-apns-get-started.md)
-[push do urządzeń z systemem Android przy użyciu powiadomień i](notification-hubs-android-push-notification-google-fcm-get-started.md)
-powiadomień Google FCM[push do aplikacji PLATFORMY UNIWERSALNEJ systemu Windows działających na urządzeniu z systemem](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
-Windows[Powiadomienia wypychane do aplikacji Windows Phone 8 za pomocą](notification-hubs-windows-mobile-push-notifications-mpns.md)
-powiadomień push MPNS[za pomocą powiadomień Hubów powiadomień i funkcji Push w chmurze Baidu](notification-hubs-baidu-china-android-notifications-get-started.md)
