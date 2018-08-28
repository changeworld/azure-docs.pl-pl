Teraz, że dodano wyzwalacza, jego czas się czymś interesujących danych, który jest generowany przez wyzwalacz. Wykonaj następujące kroki, aby dodać **SharePoint Online — Utwórz plik** akcji. Ta akcja spowoduje utworzenie pliku w usłudze SharePoint Online każdorazowo nowego elementu wyzwalacza. 

Aby skonfigurować tej akcji, należy podać następujące informacje. Jak te informacje zostaną podane, można zauważyć, że to łatwy w użyciu dane generowane przez wyzwalacz jako dane wejściowe dla niektórych właściwości dla nowego pliku:

| Utwórz właściwość pliku | Opis |
| --- | --- |
| Adres URL witryny |Jest to adres URL witryny usługi SharePoint Online, gdzie chcesz utworzyć nowy plik. Wybierz lokację z listy przedstawione. |
| Ścieżka folderu |Jest to folder (pod adresem URL lokacji wybranej w poprzednim kroku), gdy nowy plik zostanie umieszczona. Wyszukaj i wybierz folder. |
| Nazwa pliku |Jest to nazwa pliku tworzona. |
| Zawartość pliku |Zawartość, który zostanie zapisany do pliku. |

1. Wybierz **+ nowy krok** Aby dodać akcję.  
   ![Obraz akcji online programu SharePoint 1](./media/connectors-create-api-sharepointonline/action-1.png)  
2. Wybierz **Dodaj akcję** łącza. To otwiera, które pola wyszukiwania, w którym można wyszukać dowolną akcję należy podjąć. W tym przykładzie akcje programu SharePoint są przedmiotem zainteresowania.    
   ![SharePoint online akcji obrazu 2](./media/connectors-create-api-sharepointonline/action-2.png)    
3. Wprowadź *sharepoint* wyszukiwania akcji związanych z programu SharePoint.
4. Wybierz **SharePoint Online — Utwórz plik** jako akcję do wykonania.   **Uwaga**: zostanie wyświetlony monit, aby autoryzować aplikację logiki, aby uzyskać dostęp do swojego konta programu SharePoint, gdy połączenie do usługi SharePoint Online nie zostało utworzone wcześniej.    
   ![SharePoint online akcji obrazu 3](./media/connectors-create-api-sharepointonline/action-3.png)    
5. **Utwórz plik** sterowania zostanie otwarta.   
   ![Obraz akcji online programu SharePoint 4](./media/connectors-create-api-sharepointonline/action-4.png)     
6. Wybierz **adres URL witryny** i Przeglądaj, aby znaleźć witryny, w której chcesz utworzyć plik.     
   ![SharePoint online akcji obrazu 5](./media/connectors-create-api-sharepointonline/action-5.png)  
7. Wybierz **ścieżka folderu** i przejdź do folderu, w którym zostaną umieszczone nowy plik.  
   ![SharePoint online akcji obrazu 6](./media/connectors-create-api-sharepointonline/action-6.png)  
8. Wybierz **nazwy pliku** sterowania, a następnie wprowadź nazwę pliku, w której chcesz utworzyć. W tym miejscu można wprowadzić nazwę pliku bezpośrednio lub możesz użyć dowolnej właściwości z wyzwalacza, która została utworzona wcześniej. Można to zrobić, wybierając polecenie Właściwości, z listy **dane wyjściowe z po utworzeniu nowego elementu**. Ta lista jest wyświetlana tylko po wybraniu **nazwy pliku** kontroli. W tym walkthough wybrano ID (identyfikator nowego elementu listy) jako nazwę pliku, tworzonego przez **SharePoint Online — Utwórz plik** akcji.    
   ![SharePoint online akcji obrazu 7](./media/connectors-create-api-sharepointonline/action-7.png)  
9. Wybierz **zawartość pliku** sterowania, a następnie wprowadź zawartości, który zostanie zapisany do pliku, który zostanie utworzony. Zawartość pliku Zauważ, że można użyć dowolnej z właściwości z wyzwalacza, która została utworzona wcześniej. Po prostu zaznacz właściwości, z listy przedstawione. Alternatywnie można wprowadzić **zawartość pliku** tekst bezpośrednio w kontrolce. W tym przykładzie wybrano niektóre właściwości i dodać miejsca do magazynowania oraz łącznik między każdej właściwości.        
   ![SharePoint online akcji obrazu 8](./media/connectors-create-api-sharepointonline/action-8.png)  
10. Zapisz zmiany w przepływie pracy  
11. Gratulacje, masz teraz aplikację logiki w pełni funkcjonalny, która jest wyzwalana, gdy nowy element zostanie dodany do listy usługi SharePoint Online. Aplikacja, spowoduje to utworzenie pliku przy użyciu niektórych właściwości z nowego elementu listy.  Można teraz przetestować, tworząc nowy element na liście programu SharePoint. 

