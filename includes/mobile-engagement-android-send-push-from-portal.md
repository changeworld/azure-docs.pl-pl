### <a name="grant-mobile-engagement-access-to-your-gcm-api-key"></a>Przyznawanie dostępu usługi Mobile Engagement do klucza interfejsu API usługi GCM

Aby zezwolić usłudze Mobile Engagement na wysyłanie powiadomień wypychanych w swoim imieniu, musisz przyznać jej dostęp do klucza interfejsu API. W tym celu należy skonfigurować i wprowadzić klucz w portalu usługi Mobile Engagement.

1. W witrynie Azure Portal upewnij się, że znajdujesz się w aplikacji używanej w tym projekcie, a następnie kliknij przycisk **Engage** (Włącz) widoczny u dołu:

    ![](./media/mobile-engagement-create-app-in-portal-new/engage-button.png)

2. Następnie kliknij pozycje **Ustawienia** -> **Native Push** (Natywne powiadomienia wypychane), aby wprowadzić klucz GCM:

    ![](./media/mobile-engagement-android-send-push/engagement-portal.png)

3. Kliknij ikonę **Edytuj** obok pola **Klucz interfejsu API** w sekcji ustawień usługi **GCM**, jak pokazano poniżej:

    ![](./media/mobile-engagement-android-send-push/native-push-settings.png)

4. W oknie podręcznym wklej uzyskany wcześniej klucz serwera usługi GCM, a następnie kliknij przycisk **OK**.

    ![](./media/mobile-engagement-android-send-push/api-key.png)

## <a id="send"></a>Wysyłanie powiadomienia do aplikacji

Teraz utworzymy prostą kampanię z użyciem powiadomień wypychanych, która wyśle powiadomienie wypychane do aplikacji.

1. Przejdź do karty **REACH** (Zasięg) w portalu usługi Mobile Engagement.

2. Kliknij przycisk **New Announcement** (Nowy anons), aby utworzyć kampanię z użyciem powiadomień wypychanych.

    ![](./media/mobile-engagement-android-send-push/new-announcement.png)
3. Skonfiguruj pierwsze pole kampanii w następujący sposób:

    ![](./media/mobile-engagement-android-send-push/campaign-first-params.png)

    a. Nadaj nazwę kampanii.

    b. Wybierz typ dostarczania w obszarze **Delivery type** (Typ dostarczania), wybierając opcje *System notification (Powiadomienie systemowe) -> Simple (Proste)*. Jest to typ prostego powiadomienia wypychanego w systemie Android, które zawiera tytuł i krótki wiersz tekstu.

    c. W obszarze **Delivery time** (Godzina dostarczania) wybierz opcję *Any time* (W dowolnym momencie), co umożliwi aplikacji odebranie powiadomienia bez względu na to, czy jest ona uruchomiona.

    d. W tekście powiadomienia w polu **Title** (Tytuł) wpisz tytuł, który w powiadomieniu wypychanym będzie wyróżniony pogrubioną czcionką.

    e. Następnie wpisz komunikat w polu **Message** (Komunikat).
4. Przewiń w dół i w sekcji **Content** (Zawartość) wybierz opcję **Notification only** (Tylko powiadomienie).

    ![](./media/mobile-engagement-android-send-push/campaign-content.png)
5. Na tym kończy się konfigurowanie najbardziej podstawowej kampanii. Teraz przewiń ponownie w dół i kliknij przycisk **Create** (Utwórz), aby zapisać kampanię.
6. Ostatni krok: kliknij opcję **Activate** (Aktywuj), aby aktywować kampanię w celu wysyłania powiadomień wypychanych.
   
    ![](./media/mobile-engagement-android-send-push/campaign-activate.png)

