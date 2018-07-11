### <a name="prepare-for-a-push-installation-on-a-linux-server"></a>Przygotowanie do instalacji wypychanej na serwerze z systemem Linux

1. Upewnij się, że istnieje połączenie sieciowe między komputer z systemem Linux a serwerem przetwarzania.
2. Utwórz konto, za pomocą którego serwer przetwarzania będzie mógł uzyskać dostęp do komputera. Konto powinno być użytkownikiem **root** na źródłowym serwerze z systemem Linux. Tylko w przypadku instalacji wypychanej i aktualizacje, należy używać tego konta.
3. Sprawdź, czy plik /etc/hosts na źródłowym serwerze z systemem Linux zawiera wpisy mapujące lokalną nazwę hosta na adres IP skojarzony ze wszystkimi kartami sieciowymi.
4. Zainstaluj najnowsze pakiety openssh, openssh-server i openssl na komputerze, który chcesz replikować.
5. Upewnij się, że protokół Secure Shell (SSH) jest włączony i uruchomiony na porcie 22.
6. Włącz podsystem SFTP i hasło uwierzytelnianie w pliku sshd_config. Wykonaj następujące kroki:

    a. Zaloguj się jako użytkownik **root**.

    b. W **/etc/ssh/sshd_config** plików, znajdź wiersz, który rozpoczyna się od **PasswordAuthentication**.

    c. Usuń znaczniki komentarza i zmień wartość na **tak**.

    d. Znajdź wiersz, który rozpoczyna się od **podsystemu**, i usuń znaczniki komentarza.

      ![Linux](./media/site-recovery-prepare-push-install-mob-svc-lin/mobility2.png)

    e. Uruchom ponownie usługę **sshd**.

7. Dodaj konto utworzone w narzędziu CSPSConfigtool. Wykonaj następujące kroki:

    a. Zaloguj się do serwera konfiguracji.

    b. Otwórz plik **cspsconfigtool.exe**. Jest on dostępny jako skrót na pulpicie i w folderze %ProgramData%\home\svsystems\bin.

    c. Na **Zarządzanie kontami** zaznacz **Dodaj konto**.

    d. Dodaj utworzone konto.

    d. Wprowadź używane poświadczenia po włączeniu replikacji dla komputera.
