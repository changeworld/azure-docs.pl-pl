Dodajmy wyzwalacza.

1. Wprowadź *sftp* w polu wyszukiwania w Projektancie aplikacji usługi Logic Apps a następnie zaznacz **SFTP — po dodaniu lub zmodyfikowaniu pliku** wyzwalacza   
   ![Obraz wyzwalacza SFTP 1](./media/connectors-create-api-sftp/trigger-1.png)  
2. **Po dodaniu lub zmodyfikowaniu pliku** otwiera kontrolki  
   ![Obraz wyzwalacza SFTP 2](./media/connectors-create-api-sftp/trigger-2.png)  
3. Wybierz **...**  znajdujący się po prawej stronie formantu. Spowoduje to otwarcie kontrolkę selektora folderów  
   ![Obraz wyzwalacza SFTP 3](./media/connectors-create-api-sftp/action-1.png)  
4. Wybierz **SFTP** do folderu głównego jako folder do monitorowania, czy są nowe lub zmodyfikowane pliki. Zwróć uwagę, folder główny jest teraz wyświetlana w **folderu** kontroli.  
   ![Obraz wyzwalacza SFTP 4](./media/connectors-create-api-sftp/action-2.png)   

W tym momencie skonfigurowano aplikację logiki z wyzwalaczem, który spowoduje rozpoczęcie wykonywania innych wyzwalaczy i akcji w przepływie pracy, gdy plik zostanie zmodyfikowany lub utworzony w określonym folderze SFTP. 

> [!NOTE]
> Dla aplikacji logiki działała prawidłowo musi zawierać co najmniej jeden wyzwalacz i jedną akcję. Wykonaj kroki opisane w następnej sekcji, aby dodać akcję.  
> 
> 

