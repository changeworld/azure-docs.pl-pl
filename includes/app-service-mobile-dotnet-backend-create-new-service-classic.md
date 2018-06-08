1. Zaloguj się na stronie [Azure Portal].
2. Wybierz **+ nowy** > **sieci Web i mobilność** > **aplikacji mobilnej**, a następnie podaj nazwę dla zaplecza aplikacji mobilnej.
3. Aby uzyskać **grupy zasobów**, wybierz istniejącą grupę zasobów lub Utwórz nową (przy użyciu tej samej nazwie co aplikacja). 
4. Dla **planu usługi aplikacji**, plan domyślny (w [warstwy standardowa](https://azure.microsoft.com/pricing/details/app-service/)) jest zaznaczone. Możesz też wybrać inny plan lub [Utwórz nową](../articles/app-service/app-service-plan-manage.md#create-an-app-service-plan). 

   Ustawienia planu usługi aplikacji określają [lokalizację, funkcje, koszt i zasoby obliczeniowe](https://azure.microsoft.com/pricing/details/app-service/) skojarzone z aplikacją. Aby uzyskać więcej informacji na temat usługi aplikacji planów oraz sposobu tworzenia nowego planu w innej cennik warstwy i w preferowanej lokalizacji, zobacz [szczegółowe omówienie planów usługi aplikacji Azure](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
   
5. Wybierz pozycję **Utwórz**. Spowoduje to utworzenie zaplecza aplikacji mobilnej. 
6. W **ustawienia** okienko nowe aplikacje mobilne zaplecza, wybierz **szybki start** > platforma aplikacji klienta > **połączyć z bazą danych**. 
   
   ![Wybrane połączenie bazy danych](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)
7. W **Dodaj połączenie danych** okienku wybierz **bazy danych SQL** > **Utwórz nową bazę danych**. Wprowadź nazwę bazy danych, wybierz warstwę cenową, a następnie wybierz **serwera**. Możesz użyć tej nowej bazy danych ponownie. Jeśli masz już bazę danych w tej samej lokalizacji, możesz zamiast tego wybrać opcję **Użyj istniejącej bazy danych**. Nie zaleca się korzystanie z bazy danych w innej lokalizacji, z powodu kosztów przepustowości i większego opóźnienia.
   
   ![Wybieranie bazy danych](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)
8. W **nowy serwer** okienku, wprowadź unikatową nazwą serwera w **nazwy serwera** Podaj identyfikator logowania i hasło, wybierz **usług Azure Zezwalaj na dostęp do serwera**i wybierz  **OK**. Spowoduje to utworzenie nowej bazy danych.
9. W **Dodaj połączenie danych** okienku wybierz **ciąg połączenia**, wprowadź wartości identyfikator logowania i hasło dla bazy danych i wybierz **OK**. 

   Poczekaj kilka minut dla bazy danych zostanie pomyślnie wdrożona przed kontynuowaniem.

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/
