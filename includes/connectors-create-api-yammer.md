### <a name="prerequisites"></a>Wymagania wstępne
* A [Yammer](https://www.yammer.com/) konta 

Aby korzystać z konta usługi Yammer, w aplikacji logiki, należy autoryzować aplikację logiki, aby połączyć się z kontem usługi Yammer. Na szczęście możesz można to zrobić z poziomu aplikacji logiki w witrynie Azure Portal. 

Poniżej przedstawiono kroki, aby autoryzować aplikację logiki, aby połączyć się z kontem usługi Yammer:

1. Aby utworzyć połączenie do usługi Yammer, w Projektancie aplikacji logiki, wybierz pozycję **Pokaż Microsoft zarządzane interfejsy API** na liście rozwijanej wprowadź *Yammer* w polu wyszukiwania. Wybierz wyzwalacz lub akcję, którą Ci się spodoba do użycia:  
   ![](./media/connectors-create-api-yammer/yammer-1.png)
2. Nie utworzono żadnych połączeń w Yammerze, zanim będzie uzyskać monit podaj swoje poświadczenia usługi Yammer. Te poświadczenia będą używane do autoryzowania aplikację logiki, aby nawiązać połączenie i dostęp do danych na koncie usługi Yammer:  
   ![](./media/connectors-create-api-yammer/yammer-2.png)
3. Podaj Yammer, nazwę użytkownika i hasło, aby autoryzować aplikację logiki:  
   ![](./media/connectors-create-api-yammer/yammer-3.png)   
4. Zwróć uwagę, połączenie zostanie utworzone i są teraz bezpłatne wykonaj pozostałe kroki w aplikacji logiki:  
   ![](./media/connectors-create-api-yammer/yammer-4.png)   

