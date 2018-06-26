W tym przewodniku będzie Dowiedz się jak używać **Salesforce — po utworzeniu obiektu** trigger, aby inicjować przepływ pracy aplikacji logiki, podczas tworzenia nowego potencjalnego klienta w Salesforce.

> [!NOTE]
> Zostanie wyświetlony monit, aby zalogować się do konta usług Salesforce, jeśli nie została jeszcze utworzona *połączenia* do usług Salesforce.  
> 
> 

1. Wprowadź *salesforce* w polu wyszukiwania w Projektancie aplikacji logiki wybiorą **Salesforce — po utworzeniu obiektu** wyzwalacza.  
   ![Obraz wyzwalacza SalesForce 1](./media/connectors-create-api-salesforce/trigger-1.png)   
2. **Podczas tworzenia obiektu** formant jest wyświetlany.  
   ![Wyzwalacz SalesForce — obraz 2](./media/connectors-create-api-salesforce/trigger-2.png)   
3. Wybierz **typ obiektu** następnie wybierz *prowadzić* z listy obiektów. W tym kroku wskazujesz, tworzysz wyzwalacz, który powiadomi aplikację logiki, zawsze, gdy nowy realizacji jest tworzony w usłudze Salesforce.   
   ![Obraz wyzwalacza SalesForce 3](./media/connectors-create-api-salesforce/trigger-3.png)   
4. To już wszystko. Po utworzeniu wyzwalacza. Jednak należy utworzyć co najmniej jedną akcję w celu Uczyń tę aplikację logiki prawidłowe.    
   ![Obraz wyzwalacza SalesForce 4](./media/connectors-create-api-salesforce/trigger-4.png)   

W tym momencie aplikację logiki została skonfigurowana z wyzwalaczy, które rozpocznie wykonywania innych wyzwalacze i akcje w przepływie pracy, podczas tworzenia nowego elementu w Salesforce.  

