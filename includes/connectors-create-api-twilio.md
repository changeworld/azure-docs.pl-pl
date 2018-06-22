### <a name="prerequisites"></a>Wymagania wstępne
* Konta usługi Twilio
* Zweryfikowano numer telefonu usługi Twilio, który może odbierać programu SMS
* Zweryfikowano numer telefonu usługi Twilio, która umożliwia wysyłanie SMS

> [!NOTE]
> Jeśli używasz konta próbnego usługi Twilio programu SMS można wysłać tylko **zweryfikować** numerów telefonów.  
> 
> 

Aby korzystać z konta usługi Twilio, w aplikacji logiki, należy zezwolić aplikacji logiki do łączenia się z kontem usługi Twilio. Na szczęście można w tym z aplikacji logiki w portalu Azure. 

Poniżej przedstawiono kroki, aby zezwolić aplikacji logiki do łączenia się z kontem usługi Twilio:

1. Aby utworzyć połączenie z usługi Twilio, w Projektancie aplikacji logiki, wybierz **Pokaż Microsoft zarządzanych interfejsów API** na liście rozwijanej wprowadź *usługi Twilio* w polu wyszukiwania. Wybierz wyzwalacz lub akcja będzie chcesz użyć:  
   ![](./media/connectors-create-api-twilio/twilio-0.png)
2. Jeśli nie utworzono wszystkie połączenia z usługi Twilio, zanim będzie zostanie wyświetlony monit o podanie poświadczeń usługi Twilio. Te poświadczenia będą używane do autoryzacji aplikację logiki, aby nawiązać połączenie i dostęp do danych konta usługi Twilio:  
   ![](./media/connectors-create-api-twilio/twilio-1.png)  
3. Będziesz potrzebować **identyfikator konta usługi Twilio** i **token dostępu usługi Twilio** z poziomu pulpitu nawigacyjnego usługi Twilio, więc Zaloguj się do konta usługi Twilio teraz do pobrania informacji o tych dwóch elementów:  
   ![](./media/connectors-create-api-twilio/twilio-2.png)  
4. Usługi Twilio i logiki aplikacji użyj innej nazwy, aby zidentyfikować te dwa rodzaje informacji. Oto, jak należy zamapować je do okna dialogowego aplikacje logiki: ![](./media/connectors-create-api-twilio/twilio-3.png)  
5. Wybierz **utworzyć połączenie** przycisk:  
   ![](./media/connectors-create-api-twilio/twilio-4.png)
6. Zwróć uwagę, utworzono połączenie i wszystko jest teraz mógł kontynuować wykonywanie innych czynności w aplikacji logiki:  
   ![](./media/connectors-create-api-twilio/twilio-5.png)

