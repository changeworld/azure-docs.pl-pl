1. W przeglądarce przejdź do maszyny wirtualnej Wpięć. Ponieważ Wpięć pulpitu nawigacyjnego jest niedostępna za pośrednictwem protokołu HTTP niezabezpieczoną, zostanie wyświetlony komunikat, trzeba umożliwia tunelu SSH w maszynie wirtualnej.

    ![Wpięć zawiera informacje opisujące sposób używanie protokołu SSH do nawiązania połączenia z maszyną wirtualną Wpięć.](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-ssh-instructions.png)

1. Otwórz okno bash lub terminalu, dostęp do SSH.

1. Wprowadź następujące `ssh` polecenie, zastępując  **&lt;nazwa użytkownika >** i  **&lt;domeny >** symbole zastępcze wartości określone podczas tworzenia Wpięć Maszyna wirtualna:

    ```bash
    ssh -L 127.0.0.1:8080:localhost:8080 <username>@<domain>.eastus.cloudapp.azure.com
    ```

1. Wykonaj `ssh` monitami, aby zalogować się do maszyny wirtualnej Wpięć.

1. Wprowadź następujące polecenie, aby pobrać początkowej hasła do odblokowywania Wpięć.

    ```bash
    sudo cat /var/lib/jenkins/secrets/initialAdminPassword
    ```

1. Kopiuj do Schowka hasło, które są wyświetlane w oknie bash lub terminalu.

1. W przeglądarce przejdź do `http://localhost:8080`.

1. Wklej hasła wcześniej pobrany do **hasło administracyjne** pola i wybierz pozycję **Kontynuuj**.

    ![Wpięć przechowuje początkowe hasło, którego musisz użyć do odblokowania Wpięć maszyny wirtualnej po raz pierwszy należy się z nim połączyć.](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-unlock.png)

1. Wybierz **Instalowanie wtyczki sugerowane**.

    ![Wpięć umożliwia łatwe instalowanie kolekcji sugerowane wtyczek wejścia początkowej](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-customize.png)

1. Na **Tworzenie pierwszego użytkownika administratora** , Utwórz użytkownika administracyjnego i hasło dla pulpitu nawigacyjnego Wpięć i wybrać opcję **zapisać i zakończenia**.

1. Na **Wpięć jest gotowy!** Wybierz opcję **Rozpoczynanie korzystania z Wpięć**.

    ![Po Wpięć jest zainstalowana i skonfigurowana dla dostępu, możesz rozpocząć używać go do tworzenia zadania kompilacji.](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-ready.png)