

1. Na komputerze Mac, należy uruchomić **dostęp do pęku kluczy**. Na pasku nawigacyjnym po lewej stronie w obszarze **kategorii**, otwórz **moje certyfikaty**. Znajdź certyfikat SSL, który został pobrany w poprzedniej sekcji, a następnie ujawnić jego zawartość. Wybierz tylko certyfikat (nie należy wybierać klucz prywatny). Następnie [go wyeksportować](https://support.apple.com/kb/PH20122?locale=en_US).
2. W [witryny Azure portal](https://portal.azure.com/), wybierz opcję **Przeglądaj wszystkie** > **App Services**. Następnie wybierz usługę Mobile Apps zapleczem. 
3. W obszarze **ustawienia**, wybierz opcję **wypychania usługi App Service**. Następnie wybierz nazwę Centrum powiadomień. 
3. Przejdź do **Apple Push Notification Services** > **Przekaż certyfikat**. Przekazywanie pliku p12, wybierając poprawny **tryb** (w zależności od tego, czy certyfikat klienta SSL z wcześniejszego to piaskownica lub produkcji). Zapisz zmiany.

Usługa jest teraz skonfigurowane do pracy za pomocą powiadomień wypychanych w systemie iOS.

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
