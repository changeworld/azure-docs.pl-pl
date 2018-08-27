W tym przewodniku zostanie dowiesz się, jak używać **Salesforce — po utworzeniu obiektu** wyzwalacz inicjujący przepływ pracy aplikacji logiki po utworzeniu nowego potencjalnego klienta w aplikacji Salesforce.

> [!NOTE]
> Zostanie wyświetlony monit do logowania się do konta usługi Salesforce, jeśli nie została jeszcze utworzona *połączenia* do usługi Salesforce.  
> 
> 

1. Wprowadź *salesforce* w polu wyszukiwania w Projektancie aplikacji usługi Logic Apps a następnie zaznacz **Salesforce — po utworzeniu obiektu** wyzwalacza.  
   ![Obraz wyzwalacza usługi SalesForce 1](./media/connectors-create-api-salesforce/trigger-1.png)   
2. **Po utworzeniu obiektu** jest wyświetlany formantu.  
   ![Obraz wyzwalacza usługi SalesForce 2](./media/connectors-create-api-salesforce/trigger-2.png)   
3. Wybierz **typu obiektu** polecenie *prowadzić* z listy obiektów. W tym kroku wskazujesz, że utworzysz wyzwalacz, który powiadomi aplikację logiki, po każdym utworzeniu nowego potencjalnego klienta w usłudze Salesforce.   
   ![Obraz wyzwalacza usługi SalesForce 3](./media/connectors-create-api-salesforce/trigger-3.png)   
4. To już wszystko. Utworzono wyzwalacz. Jednak należy utworzyć co najmniej jedną akcję, aby Uczyń tę aplikację logiki prawidłowe.    
   ![Obraz wyzwalacza usługi SalesForce 4](./media/connectors-create-api-salesforce/trigger-4.png)   

W tym momencie skonfigurowano aplikację logiki z wyzwalaczem, który spowoduje rozpoczęcie wykonywania innych wyzwalaczy i akcji w przepływie pracy, po utworzeniu nowego elementu w aplikacji Salesforce.  

