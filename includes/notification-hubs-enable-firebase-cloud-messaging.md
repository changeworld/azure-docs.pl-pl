

1. Zaloguj się do [konsoli Firebase](https://firebase.google.com/console/). Utwórz nowy projekt Firebase, jeśli jeszcze go nie masz.
2. Po utworzeniu projektu wybierz pozycję **Dodaj Firebase do swojej aplikacji dla systemu Android**. Następnie postępuj zgodnie z wyświetlanymi instrukcjami.

    ![Dodawanie usługi Firebase do aplikacji systemu Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. W konsoli usługi Firebase wybierz koło zębate dla projektu. Następnie wybierz pozycję **Ustawienia projektu**.

    ![Wybieranie ustawień projektu](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Wybierz kartę **Ogólne** w ustawieniach projektu. Następnie pobierz plik **google-services.json**, który zawiera klucz interfejsu API serwera i identyfikator klienta.
5. Wybierz kartę **Cloud Messaging** w ustawieniach projektu, a następnie skopiuj wartość **Starszy klucz serwera**. Ta wartość służy do skonfigurowania zasad dostępu do centrum powiadomień.
