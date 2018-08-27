Teraz, że dodano warunek, jego czas się czymś interesujących danych, który jest generowany przez wyzwalacz. Wykonaj następujące kroki, aby dodać **Salesforce — Pobierz obiekt** akcji. Ta akcja będzie Pobierz dane w każdym utworzeniu nowego potencjalnego klienta. Dodasz również drugiej akcji, która będzie używać danych z usług Salesforce - Get akcję obiektu, aby wysłać wiadomość e-mail za pomocą łącznika usługi Office 365.  

Aby skonfigurować tej akcji, należy podać następujące informacje. Można zauważyć, że to łatwy w użyciu dane generowane przez wyzwalacz jako dane wejściowe dla niektórych właściwości dla nowego pliku:

| Utwórz właściwość pliku | Opis |
| --- | --- |
| Typ obiektu |Jest to typ obiektu usług Salesforce, który Cię interesuje. Przykłady są potencjalnymi klientami, kont itd. |
| Identyfikator obiektu |Reprezentuje identyfikator obiektu. |

1. Wybierz **Dodaj akcję** łącza. To otwiera, które pola wyszukiwania, w którym można wyszukać dowolną akcję należy podjąć. W tym przykładzie akcje Salesforce są przedmiotem zainteresowania.      
   ![Obraz akcji SalesForce 1](./media/connectors-create-api-salesforce/action-1.png)  
2. Wprowadź *salesforce* do wyszukania akcje związane z usługą salesforce.
3. Wybierz **Salesforce — Pobierz obiekt** jako akcję do wykonania.   **Uwaga**: zostanie wyświetlony monit, aby autoryzować aplikację logiki, aby uzyskać dostęp do konta usługi Salesforce, gdy użytkownik nie zostało zrobione wcześniej.    
   ![Obraz akcji SalesForce 2](./media/connectors-create-api-salesforce/action-2.png)    
4. **Pobierz obiekt** sterowania zostanie otwarta.  
5. Wybierz *prowadzić* jako typ obiektu.
6. Wybierz **obiektu o identyfikatorze** kontroli.
7. Wybierz **...**  rozwiń listę tokenów, które mogą służyć jako dane wejściowe dla akcji.       
   ![Obraz akcji SalesForce 3](./media/connectors-create-api-salesforce/action-3.png)    
8. Wybierz **prowadzić identyfikator** sterowania zostanie otwarta.   
   ![Obraz akcji SalesForce 4](./media/connectors-create-api-salesforce/action-4.png)     
9. Zauważ, że tokenu Identyfikacyjnego prowadzić jest teraz w kontrolce obiektu o identyfikatorze wskazujący akcję obiektu Get wyszuka potencjalny klient o identyfikatorze, który jest taki sam identyfikator potencjalny klient potencjalny klient, który wyzwolił tej aplikacji logiki.  
   ![Obraz akcji SalesForce 5](./media/connectors-create-api-salesforce/action-5.png)  
10. Zapisz pracę. To wszystko, Akcja obiektu Get zostały dodane do aplikacji logiki. Pobierz obiekt formantu powinien wyglądać następująco:    
    ![Obraz akcji SalesForce 6](./media/connectors-create-api-salesforce/action-6.png)  

Po dodaniu akcji, aby pobrać potencjalny klient, może chcesz zrobić coś interesującego przy użyciu nowo utworzony potencjalny klient. W przedsiębiorstwie możesz wysłać wiadomość e-mail listy dystrybucyjnej utworzeniu nowego potencjalnego klienta. Użyjmy łącznik usługi Office 365, aby wysłać wiadomość e-mail z niektórymi odpowiednich informacji z nowego obiektu potencjalnego klienta w usłudze Salesforce.  

1. Wybierz **Dodaj akcję** wprowadź *e-mail* w kontrolce wyszukiwania. Filtruje działania do tych, które są związane z wysyłaniem i odbieraniem wiadomości e-mail.  
2. Wybierz **Office 365 Outlook — Wyślij wiadomość e-mail** elementu listy. Jeśli nie utworzono jeszcze *połączenia* do swojego konta usługi Office 365 użytkownik jest monitowany wprowadzić swoje poświadczenia usługi Office 365, aby utworzyć go teraz. Po wykonaniu czynności **Wyślij wiadomość e-mail** sterowania zostanie otwarta.        
   ![Obraz akcji SalesForce 7](./media/connectors-create-api-salesforce/action-7.png)  
3. Wprowadź adres e-mail, który chcesz wysłać wiadomość e-mail w **do** kontroli.
4. W **podmiotu** sterowania, wprowadź *nowe prowadzić utworzone* — a następnie wybierz pozycję *firmy* tokenu. Spowoduje to wyświetlenie *firmy* pola z nowego potencjalnego klienta, utworzony w usłudze Salesforce.  
5. W **treści** kontrolki, można wybrać dowolne tokeny od obiektu nowych potencjalnych klientów i możesz też wprowadzić niezależnie od tekstu, które chcesz wyświetlić w treści wiadomości e-mail. Oto przykład:  
   ![Obraz akcji SalesForce 8](./media/connectors-create-api-salesforce/action-8.png)   
6. Zapisz przepływ pracy.  

To już wszystko. Twoja aplikacja logiki została zakończona.  

Teraz można przetestować aplikację logiki: w usłudze Salesforce, Utwórz nowego potencjalnego klienta, który spełnia warunek został utworzony.  Po wykonaniu tego przewodnika w pełni, po prostu Utwórz potencjalnego klienta za pomocą adresu e-mail, który zawiera *amazon.com* w nim. Po kilku sekundach aplikacja logiki powinna być wyzwolona, a wyniki mogą wyglądać mniej więcej tak:  
![Obraz akcji SalesForce 9](./media/connectors-create-api-salesforce/action-9.png)  

