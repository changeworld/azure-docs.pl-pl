<!--author=alkohli last changed: 02/06/17-->

#### <a name="to-install-an-update-from-the-azure-portal"></a>Aby zainstalować aktualizację z witryny Azure Portal

1. Na stronie usługi StorSimple wybierz swoje urządzenie. Przejdź do pozycji **Urządzenia** > **Konserwacja**.
2. W dolnej części strony kliknij pozycję **Skanuj aktualizacje**. Zostanie utworzone zadanie skanowania dostępnych aktualizacji. Otrzymasz powiadomienie, gdy zadanie zostanie pomyślnie ukończone.
3. W sekcji **Aktualizacje oprogramowania** na tej samej stronie dostępne są nowe aktualizacje oprogramowania. Przed zastosowaniem aktualizacji na urządzeniu zalecamy przejrzenie informacji o wersji.
4. W dolnej części strony kliknij pozycję **Zainstaluj aktualizacje**, a następnie kliknij przycisk **OK**.
5. W oknie dialogowym **Instalowanie aktualizacji** upewnij się, że zostały wypełnione zalecenia, wybierz pozycję **Rozumiem powyższe wymaganie i chcę uaktualnić moje urządzenie**, a następnie kliknij przycisk ze znacznikiem wyboru.
   
    ![Komunikat z potwierdzeniem](./media/storsimple-install-update2-via-portal/InstallUpdate12_2M.png)
6. Zostanie rozpoczęty zestaw testów wymagań wstępnych. Testy te obejmują:
   
   * **Testy kondycji kontrolera** w celu sprawdzenia, czy oba kontrolery urządzeń są w dobrej kondycji i w trybie online.
   * **Testy kondycji składnika sprzętowego** w celu sprawdzenia, czy wszystkie składniki sprzętowe Twojego urządzenia StorSimple są w dobrej kondycji.
   * **Testy interfejsu DATA 0** w celu sprawdzenia, czy interfejs DATA 0 jest włączony na Twoim urządzeniu. Jeśli ten interfejs nie jest włączony, należy go włączyć i ponowić próbę.
   * **Testy interfejsów DATA 2 i DATA 3** w celu sprawdzenia, czy interfejsy sieciowe DATA 2 i DATA 3 nie są włączone. Jeśli te interfejsy są włączone, należy je wyłączyć, a następnie spróbować zaktualizować urządzenie. Ten test jest przeprowadzany tylko wtedy, gdy aktualizowane jest urządzenie z oprogramowaniem w wersji ogólnie dostępnej. Urządzenia z wersjami oprogramowania 0.1, 0.2 i 0.3 nie wymagają tego testu.
   * **Test bramy** na wszystkich urządzeniach z wersją oprogramowania wcześniejszą niż Update 1. Ten test jest przeprowadzany na wszystkich urządzeniach z oprogramowaniem wcześniejszym niż wersja Update 1, ale kończy się niepowodzeniem na urządzeniach, które mają bramę skonfigurowaną dla interfejsu sieciowego innego niż DATA 0.
     
     Aktualizacja jest stosowana, jeśli wszystkie testy zostaną wykonane pomyślnie. Zobaczysz powiadomienie o trwających testach.
     
     ![Powiadomienie poprzedzające testy](./media/storsimple-install-update2-via-portal/InstallUpdate12_3M.png)
     
     Poniżej pokazano przykład nieudanych testów. Musisz sprawdzić, czy oba kontrolery urządzeń są w dobrej kondycji i w trybie online. Musisz też sprawdzić kondycję składników sprzętowych. W tym przykładzie uwagi wymagają składniki Controller 0 i Controller 1. Jeśli nie będziesz w stanie samodzielnie rozwiązać tych problemów, konieczne może być skontaktowanie się z pomocą techniczną firmy Microsoft.
     
       ![Nieudane testy](./media/storsimple-install-update2-via-portal/HCS_PreUpgradeChecksFailed-include.png)
7. Gdy testy zostaną pomyślnie ukończone, tworzone jest zadanie aktualizacji. Otrzymasz powiadomienie o pomyślnym utworzeniu zadania aktualizacji.
   
    ![Tworzenie zadania aktualizacji](./media/storsimple-install-update2-via-portal/InstallUpdate12_44M.png)
   
    Następnie aktualizacja jest stosowania do Twojego urządzenia.
    
8. Aby monitorować postęp zadania aktualizacji, kliknij pozycję **Wyświetl zadanie**. Na stronie **Zadania** możesz sprawdzać postęp aktualizacji.
9. Aktualizacja zajmuje kilka godzin. Aby w dowolnym momencie wyświetlić szczegóły zadania, wybierz zadanie aktualizacji i kliknij pozycję **Szczegóły**.
10. Po zakończeniu zadania przejdź do strony **Konserwacja** i przewiń ją do pozycji **Aktualizacje oprogramowania**.

