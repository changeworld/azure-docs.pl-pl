## <a name="test"></a>Testowanie funkcji

Przetestuj wdrożoną funkcję za pomocą programu cURL na komputerze Mac lub komputerze z systemem Linux bądź za pomocą powłoki Bash na komputerze z systemem Windows. Uruchom następujące polecenie programu cURL, wstawiając w miejsce symbolu zastępczego `<app_name>` nazwę aplikacji funkcji. Dołącz ciąg zapytania `&name=<yourname>` do adresu URL.

```bash
curl https://<app_name>.azurewebsites.net/api/HttpTrigger?name=<yourname>
```  

![Odpowiedź funkcji wyświetlona w przeglądarce.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png)  

Jeśli w wierszu polecenia nie jest dostępny program cURL, wprowadź ten sam adres URL w pasku adresu przeglądarki. W miejsce symbolu zastępczego `<app_name>` wstaw nazwę aplikacji funkcji i dołącz ciąg zapytania `&name=<yourname>` do adresu URL, a następnie wykonaj żądanie.

    https://<app_name>.azurewebsites.net/api/HttpTrigger?name=<yourname>
   
![Odpowiedź funkcji wyświetlona w przeglądarce.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
