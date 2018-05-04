Używa funkcji Mobile Apps w usłudze Azure App Service [usługi Azure Notification Hubs] do wysyłania wypchnięć, więc będą konfigurowane Centrum powiadomień dla aplikacji mobilnej.

1. W [portalu Azure], przejdź do **usługi aplikacji**, a następnie wybierz Twojej aplikacji zaplecza. W obszarze **ustawienia**, wybierz pozycję **Push**.
2. Aby dodać zasobów Centrum powiadomień do aplikacji, wybierz **Connect**. Można utworzyć koncentratora lub nawiązać połączenia z jednego z istniejących.

    ![Konfigurowanie Centrum](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

Teraz połączyły Centrum powiadomień do projektu zaplecza aplikacji mobilnej. Później można skonfigurować tego Centrum powiadomień, aby połączyć się z systemem powiadomień platformy (PNS) do urządzenia.

[portalu Azure]: https://portal.azure.com/
[usługi Azure Notification Hubs]: https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/
