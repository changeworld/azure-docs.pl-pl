## <a name="view-device-telemetry-in-the-dashboard"></a>Wyświetlanie danych telemetrycznych z urządzeń na pulpicie nawigacyjnym
Pulpit nawigacyjny w rozwiązaniu do monitorowania zdalnego umożliwia wyświetlenie danych telemetrycznych wysyłanych z Twoich urządzeń do usługi IoT Hub.

1. W przeglądarce powróć do pulpitu nawigacyjnego rozwiązania do monitorowania zdalnego i kliknij pozycję **Urządzenia** w lewym panelu, aby przejść do **listy urządzeń**.
2. Na **liście urządzeń** powinien być widoczny stan urządzenia **Uruchomione**. W przeciwnym razie kliknij pozycję **Włącz urządzenie** w panelu **Szczegóły urządzenia**.
   
    ![Wyświetlanie stanu urządzenia][18]
3. Kliknij pozycję **Pulpit nawigacyjny**, aby powrócić do pulpitu nawigacyjnego, a następnie wybierz swoje urządzenie z listy rozwijanej **Urządzenie do wyświetlenia**, aby wyświetlić z niego dane telemetryczne. Dane telemetryczne z przykładowej aplikacji to 50 jednostek temperatury wewnętrznej, 55 — temperatury zewnętrznej i 50 — wilgotności.
   
    ![Wyświetlanie danych telemetrycznych z urządzenia][img-telemetry]

## <a name="invoke-a-method-on-your-device"></a>Wywoływanie metody na urządzeniu
Pulpit nawigacyjny w rozwiązaniu do monitorowania zdalnego umożliwia wywoływanie metod na urządzaniach za pomocą usługi IoT Hub. W rozwiązaniu do monitorowania zdalnego możesz na przykład wywołać metodę symulacji ponownego uruchomienia urządzenia.

1. Na pulpicie nawigacyjnym rozwiązania do monitorowania zdalnego kliknij pozycję **Urządzenia** w lewym panelu, aby przejść do **listy urządzeń**.
2. Kliknij pozycję **Identyfikator urządzenia** dla swojego urządzenia na **liście urządzeń**.
3. W panelu **Szczegóły urządzenia** kliknij pozycję **Metody**.
   
    ![Metody urządzenia][13]
4. Na liście rozwijanej **Metoda** wybierz metodę **InitiateFirmwareUpdate** (inicjowanie aktualizacji oprogramowania układowego), a następnie w polu **FWPACKAGEURI** (identyfikator URI pakietu oprogramowania układowego) wprowadź zastępczy adres URL. Kliknij pozycję **Wywołaj metodę**, aby wywołać metodę na urządzeniu.
   
    ![Wywoływanie metody urządzenia][14]
   

5. Gdy urządzenie obsłuży metodę, zostanie wyświetlony komunikat w konsoli uruchamiania kodu urządzenia. Wyniki metody zostaną dodane do historii w portalu rozwiązania:

    ![Wyświetlanie historii metod][img-method-history]

## <a name="next-steps"></a>Kolejne kroki
W artykule [Dostosowywanie wstępnie skonfigurowanych rozwiązań][lnk-customize] opisano niektóre sposoby pozwalające rozszerzyć ten przykład. Możliwe rozszerzenia obejmują użycie rzeczywistych czujników i implementację dodatkowych poleceń.

Więcej informacji na temat [uprawnień podano w witrynie azureiotsuite.com][lnk-permissions].

[13]: ./media/iot-suite-v1-visualize-connecting/suite4.png
[14]: ./media/iot-suite-v1-visualize-connecting/suite7-1.png
[18]: ./media/iot-suite-v1-visualize-connecting/suite10.png
[img-telemetry]: ./media/iot-suite-v1-visualize-connecting/telemetry.png
[img-method-history]: ./media/iot-suite-v1-visualize-connecting/history.png
[lnk-customize]: ../articles/iot-suite/iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md
[lnk-permissions]: ../articles/iot-suite/iot-suite-v1-permissions.md
