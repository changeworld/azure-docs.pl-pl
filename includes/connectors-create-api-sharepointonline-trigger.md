W tym przykładzie I opisano, jak używać **usługi SharePoint Online — po utworzeniu nowego elementu** wyzwalacz inicjujący przepływ pracy aplikacji logiki po utworzeniu nowego elementu na liście usługi SharePoint Online.

> [!NOTE]
> Zostanie wyświetlony monit do logowania się do swojego konta programu SharePoint, jeśli nie została jeszcze utworzona *połączenia* do usługi SharePoint Online.  
> 
> 

1. Wprowadź *sharepoint* w polu wyszukiwania w Projektancie aplikacji usługi Logic Apps a następnie zaznacz **usługi SharePoint Online — po utworzeniu nowego elementu** wyzwalacza.  
   ![Obraz wyzwalacza online programu SharePoint ](./media/connectors-create-api-sharepointonline/trigger-1.png)  
2. **Po utworzeniu nowego elementu** jest wyświetlany formantu.  
   ![SharePoint online wyzwalacza obraz 2](./media/connectors-create-api-sharepointonline/trigger-2.png)   
3. Wybierz **adres URL witryny**. Jest to witryny programu SharePoint online, które chcesz monitorować pod kątem nowych elementów do wyzwalania przepływu pracy.  
   ![SharePoint online wyzwalacza obraz 3](./media/connectors-create-api-sharepointonline/trigger-3.png)   
4. Wybierz **Nazwa listy**. To jest lista w witrynie usługi SharePoint Online, które chcesz monitorować dla nowych elementów, które będą wyzwalać Twój przepływ pracy.  
   ![Obraz wyzwalacza online programu SharePoint 4](./media/connectors-create-api-sharepointonline/trigger-4.png)   

W tym momencie skonfigurowano aplikację logiki z wyzwalaczem, który rozpocznie się do wykonywania innych wyzwalaczy i akcji w przepływie pracy. Będzie to mieć miejsce każdym razem, gdy tworzony jest nowy element w wybranej listy usługi SharePoint Online.  

