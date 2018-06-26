W tym przykładzie, I opisano sposób użycia **usługi SharePoint Online — po utworzeniu nowego elementu** trigger, aby inicjować przepływ pracy aplikacji logiki, podczas tworzenia nowego elementu na liście programu SharePoint Online.

> [!NOTE]
> Zostanie wyświetlony monit, aby zalogować się do konta programu SharePoint, jeśli nie została jeszcze utworzona *połączenia* do usługi SharePoint Online.  
> 
> 

1. Wprowadź *sharepoint* w polu wyszukiwania w Projektancie aplikacji logiki wybiorą **usługi SharePoint Online — po utworzeniu nowego elementu** wyzwalacza.  
   ![Obraz wyzwalacza online programu SharePoint ](./media/connectors-create-api-sharepointonline/trigger-1.png)  
2. **Podczas tworzenia nowego elementu** formant jest wyświetlany.  
   ![SharePoint online wyzwalacza obraz 2](./media/connectors-create-api-sharepointonline/trigger-2.png)   
3. Wybierz **adres URL witryny**. To jest witryna programu SharePoint online, które chcesz monitorować dla nowych elementów do wyzwalania przepływu pracy.  
   ![SharePoint online wyzwalacza obrazu 3](./media/connectors-create-api-sharepointonline/trigger-3.png)   
4. Wybierz **Nazwa listy**. To jest lista na witryny usługi SharePoint Online, które chcesz monitorować dla nowych elementów, które wyzwolą przepływ pracy.  
   ![SharePoint online wyzwalacza obrazu 4](./media/connectors-create-api-sharepointonline/trigger-4.png)   

W tym momencie aplikację logiki została skonfigurowana z wyzwalaczy, które rozpocznie się do wykonywania innych wyzwalacze i akcje w przepływie pracy. Będzie to mieć miejsce każdym razem, gdy nowy element jest tworzony w wybranej listy programu SharePoint Online.  

