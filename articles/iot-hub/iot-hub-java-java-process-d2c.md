---
title: Routing komunikatów z Centrum IoT Azure (Java) | Dokumentacja firmy Microsoft
description: Jak komunikaty do przetwarzania komunikatów urządzenia do chmury Azure IoT Hub przy użyciu reguł routingu oraz niestandardowe punkty końcowe wysłania wiadomości do innych usług zaplecza.
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: dobett
ms.openlocfilehash: ff45e9d717b93f89eb8f751294788f08a2fd4592
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="routing-messages-with-iot-hub-java"></a>Routing komunikatów z Centrum IoT (Java)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

Centrum IoT Azure jest w pełni zarządzaną usługę, która zapewnia niezawodne i bezpieczną komunikację dwukierunkową między milionów urządzeń i rozwiązanie zaplecza. Innych samouczków ([Rozpoczynanie pracy z Centrum IoT] i [wysyłać chmury do urządzenia z Centrum IoT][lnk-c2d]) opisano, jak korzystać z podstawowego urządzenia do chmury i chmury do urządzenia Obsługa wiadomości funkcji Centrum IoT.

W tym samouczku opiera się na kodzie pokazanym w [Rozpoczynanie pracy z Centrum IoT] samouczek oraz przedstawiono sposób do rozsyłania wiadomości przetwarzania komunikatów urządzenia do chmury w sposób skalowalny. Samouczek przedstawia sposób przetwarzania komunikatów, które wymagają natychmiastowego działania z zaplecza rozwiązania. Na przykład urządzenie może wysłać komunikat alarmu, które wyzwala Wstawianie biletu do systemu CRM. Z kolei wiadomości punktu danych źródła danych po prostu do aparatu analizy. Na przykład dane telemetryczne temperatury z urządzenia, które mają być przechowywane w celu późniejszej analizy jest komunikat punktu danych.

Na końcu tego samouczka możesz uruchomić trzech aplikacji Java w konsoli:

* **Symulowane urządzenie**, zmodyfikowanej wersji aplikacji utworzonych w [Rozpoczynanie pracy z Centrum IoT] samouczek, wysyła komunikaty urządzenia do chmury punktu danych co sekundę i interaktywne urządzenia do chmury wiadomości co 10 sekund . Ta aplikacja korzysta z protokołu AMQP do komunikowania się z Centrum IoT.
* **Odczyt — d2c — liczba komunikatów** Wyświetla telemetrii wysyłane przez aplikację na urządzeniu.
* **Odczyt krytyczne kolejki** usuwania kolejki krytycznych wiadomości z kolejki usługi Service Bus dołączony do Centrum IoT.

> [!NOTE]
> Centrum IoT obsługuje zestawu SDK dla wielu platform urządzeń i języków, w tym C, Java i JavaScript. Aby uzyskać instrukcje dotyczące sposobu Zamień w tym samouczku urządzenie fizyczne urządzenia oraz sposób podłączania urządzeń do Centrum IoT, zobacz [Azure IoT Developer Center].

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Pełną wersję pracy [Rozpoczynanie pracy z Centrum IoT] samouczka.
* Najnowszy zestaw [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).

Zalecamy również informacje o [usługi Azure Storage] i [Azure Service Bus].

## <a name="send-interactive-messages-from-a-device-app"></a>Wysyłanie wiadomości interaktywne z aplikacji przez urządzenia
W tej sekcji możesz zmodyfikować aplikację urządzenia, utworzony w [Rozpoczynanie pracy z Centrum IoT] samouczek czasami wysłać wiadomości, które wymagają natychmiastowego przetwarzania.

1. Użyj edytora tekstów, aby otworzyć plik simulated-device\src\main\java\com\mycompany\app\App.java. Ten plik zawiera kod **symulowane urządzenie** aplikacji utworzony w [Rozpoczynanie pracy z Centrum IoT] samouczka.

2. Zastąp **MessageSender** klasy następującym kodem:

    ```java
    private static class MessageSender implements Runnable {

        public void run()  {
            try {
                double minTemperature = 20;
                double minHumidity = 60;
                Random rand = new Random();

                while (true) {
                    String msgStr;
                    Message msg;
                    if (new Random().nextDouble() > 0.7) {
                        if (new Random().nextDouble() > 0.5) {
                            msgStr = "This is a critical message.";
                            msg = new Message(msgStr);
                            msg.setProperty("level", "critical");
                        } else {
                            msgStr = "This is a storage message.";
                            msg = new Message(msgStr);
                            msg.setProperty("level", "storage");
                        }
                    } else {
                        double currentTemperature = minTemperature + rand.nextDouble() * 15;
                        double currentHumidity = minHumidity + rand.nextDouble() * 20; 
                        TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
                        telemetryDataPoint.deviceId = deviceId;
                        telemetryDataPoint.temperature = currentTemperature;
                        telemetryDataPoint.humidity = currentHumidity;

                        msgStr = telemetryDataPoint.serialize();
                        msg = new Message(msgStr);
                    }
                    
                    System.out.println("Sending: " + msgStr);

                    Object lockobj = new Object();
                    EventCallback callback = new EventCallback();
                    client.sendEventAsync(msg, callback, lockobj);

                    synchronized (lockobj) {
                        lockobj.wait();
                    }
                    Thread.sleep(1000);
                }
            } catch (InterruptedException e) {
                System.out.println("Finished.");
            }
        }
    }
    ```
   
    Ta metoda losowo dodaje właściwość `"level": "critical"` i `"level": "storage"` dla komunikatów wysyłanych przez urządzenia, która symuluje komunikat, który wymaga natychmiastowego działania przez zaplecza aplikacji lub jeden, który musi być trwale przechowywane. Aplikacja obsługuje routing wiadomości opartych na treść komunikatu.
   
   > [!NOTE]
   > Można użyć właściwości wiadomości do przesyłania wiadomości dla różnych scenariuszy, w tym chłodni ścieżką podczas przetwarzania, oprócz tu przykładzie aktywnej ścieżki.

2. Zapisz i zamknij plik simulated-device\src\main\java\com\mycompany\app\App.java.

    > [!NOTE]
    > Firma Microsoft zaleca, aby zaimplementować zasady ponawiania wykładniczego wycofywania, zgodnie z sugestią podaną w artykuł w witrynie MSDN np. [obsługi błędów przejściowych].

3. Aby utworzyć aplikację **simulated-device** przy użyciu narzędzia Maven, wykonaj następujące polecenie w wierszu polecenia w folderze simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="add-a-queue-to-your-iot-hub-and-route-messages-to-it"></a>Dodaj kolejki do IoT hub i tras wiadomości do niego

W tej sekcji Tworzenie kolejki usługi Service Bus, połącz go z Centrum IoT i skonfigurować do wysyłania wiadomości do kolejki na podstawie obecności właściwość w komunikacie Centrum IoT. Aby uzyskać więcej informacji o sposobie przetwarzania komunikatów z kolejek usługi Service Bus, zobacz [Rozpoczynanie pracy z kolejkami][lnk-sb-queues-java].

1. Tworzenie kolejki usługi Service Bus, zgodnie z opisem w [Rozpoczynanie pracy z kolejkami][lnk-sb-queues-java]. Zanotuj nazwę przestrzeni nazw i kolejki.

    > [!NOTE]
    > Tematy dotyczące używany jako punkty końcowe Centrum IoT nie może mieć i kolejek usługi Service Bus **sesji** lub **wykrywania duplikatów** włączone. Jeśli dowolna z tych opcji są włączone, punkt końcowy jest wyświetlany jako **informujący** w portalu Azure.

2. W portalu Azure Otwórz Centrum IoT i kliknij przycisk **punkty końcowe**.

    ![Punkty końcowe Centrum IoT][30]

3. W **punkty końcowe** bloku, kliknij przycisk **Dodaj** u góry, aby dodać kolejki do Centrum IoT. Nazwa punktu końcowego **CriticalQueue** i umożliwia wybranie listach rozwijanych **kolejki usługi Service Bus**, przestrzeń nazw magistrali usług, w której znajduje się kolejki i nazwy kolejki. Gdy wszystko będzie gotowe, kliknij przycisk **zapisać** u dołu.

    ![Dodawanie punktu końcowego][31]

4. Teraz kliknij **tras** w Centrum IoT. Kliknij przycisk **Dodaj** w górnej części bloku, aby utworzyć regułę routingu kieruje komunikaty do kolejki właśnie został dodany. Wybierz **DeviceTelemetry** jako źródło danych. Wprowadź `level="critical"` jako warunek i wybierz polecenie kolejki właśnie został dodany jako punkt końcowy niestandardowych jako routingu końcowy reguły. Gdy wszystko będzie gotowe, kliknij przycisk **zapisać** u dołu.

    ![Dodawanie trasy][32]

    Upewnij się, że rezerwowy trasy ma ustawioną wartość **ON**. To ustawienie jest domyślną konfigurację Centrum IoT.

    ![Trasy rezerwowej][33]

## <a name="optional-read-from-the-queue-endpoint"></a>(Opcjonalnie) Odczyt z kolejki punktu końcowego

Można opcjonalnie odczytywać wiadomości z kolejki punktu końcowego, postępując zgodnie z instrukcjami w [Rozpoczynanie pracy z kolejkami][lnk-sb-queues-java]. Nazwa aplikacji **odczytu krytycznych kolejki**.

## <a name="run-the-applications"></a>Uruchamianie aplikacji

Teraz można przystąpić do uruchomienia trzech aplikacji.

1. Aby uruchomić **odczytu — d2c — liczba komunikatów** aplikacji, w wierszu polecenia lub powłoki przejdź do folderu d2c odczytu i uruchom następujące polecenie:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![Uruchom odczytu — d2c — liczba komunikatów][readd2c]

2. Aby uruchomić **odczytu krytycznych kolejki** aplikacji, w wierszu polecenia lub powłoki przejdź do folderu odczytu krytycznych kolejki i uruchom następujące polecenie:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![Uruchom odczytu — krytyczne — liczba komunikatów][readqueue]

3. Aby uruchomić **symulowane urządzenie** aplikacji, w wierszu polecenia lub powłoki przejdź do folderu symulowane urządzenie i uruchom następujące polecenie:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![Uruchom symulowane urządzenie][simulateddevice]

## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>(Opcjonalnie) Dodaj kontener magazynu do IoT hub i tras wiadomości do niego

W tej sekcji Utwórz konto magazynu, połącz go z Centrum IoT i skonfigurować do wysyłania komunikatów do konta, na podstawie obecności właściwość w komunikacie Centrum IoT. Aby uzyskać więcej informacji o sposobie zarządzania magazynu, zobacz [Rozpoczynanie pracy z magazynem Azure][usługi Azure Storage].

 > [!NOTE]
   > Jeśli nie masz tylko jeden **punktu końcowego**, może skonfigurować **StorageContainer** oprócz **CriticalQueue** i uruchom oba simulatneously.

1. Utwórz konto magazynu, zgodnie z opisem w [dokumentacji platformy Azure magazynu] [lnk magazynu]. Zanotuj nazwę konta.

2. W portalu Azure Otwórz Centrum IoT i kliknij przycisk **punkty końcowe**.

3. W **punkty końcowe** bloku, wybierz opcję **CriticalQueue** punktu końcowego, a następnie kliknij przycisk **usunąć**. Kliknij przycisk **tak**, a następnie kliknij przycisk **Dodaj**. Nazwa punktu końcowego **StorageContainer** i umożliwia wybranie listach rozwijanych **kontenera magazynu Azure**i Utwórz **konta magazynu** i **magazynu kontener**.  Zanotuj nazwy.  Gdy wszystko będzie gotowe, kliknij przycisk **OK** u dołu. 

 > [!NOTE]
   > Jeśli nie masz tylko jeden **punktu końcowego**, nie trzeba usunąć **CriticalQueue**.

4. Kliknij przycisk **tras** w Centrum IoT. Kliknij przycisk **Dodaj** w górnej części bloku, aby utworzyć regułę routingu kieruje komunikaty do kolejki właśnie został dodany. Wybierz **komunikaty** jako źródło danych. Wprowadź `level="storage"` jako warunek i wybierz polecenie **StorageContainer** jako punktu końcowego niestandardowych jako routingu końcowy reguły. Kliknij przycisk **zapisać** u dołu.  

    Upewnij się, że rezerwowy trasy ma ustawioną wartość **ON**. To ustawienie jest domyślną konfigurację Centrum IoT.

1. Upewnij się, że nadal działają poprzedniej aplikacji. 

1. W portalu Azure, przejdź do konta magazynu, w obszarze **usługa Blob**, kliknij przycisk **Przeglądaj obiekty BLOB...** .  Wybierz kontener użytkownika, przejdź do kliknij plik JSON i kliknij przycisk **Pobierz** do wyświetlania danych.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób niezawodny sposób wysyłania wiadomości urządzenia do chmury przy użyciu funkcji routing komunikatów z Centrum IoT.

[Sposób wysyłania wiadomości chmury do urządzenia z Centrum IoT] [ lnk-c2d] przedstawiono sposób wysyłania komunikatów do urządzeń z Twojej zaplecza rozwiązania.

Aby zapoznać się przykładem kompletnych rozwiązań end-to-end korzystających z Centrum IoT, zobacz [zdalnego monitorowania Azure IoT akcelerator rozwiązań][lnk-suite].

Aby dowiedzieć się więcej na temat tworzenia rozwiązań z Centrum IoT, zobacz [Centrum IoT — przewodnik dewelopera].

Aby dowiedzieć się więcej na temat w Centrum IoT rozsyłania wiadomości, zobacz [wysyłania i odbierania wiadomości z Centrum IoT][lnk-devguide-messaging].

<!-- Images. -->
<!-- TODO: UPDATE PICTURES -->
[simulateddevice]: ./media/iot-hub-java-java-process-d2c/runsimulateddevice.png
[readd2c]: ./media/iot-hub-java-java-process-d2c/runprocessinteractive.png
[readqueue]: ./media/iot-hub-java-java-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-java-java-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-java-java-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-java-java-process-d2c/route-creation.png
[33]: ./media/iot-hub-java-java-process-d2c/fallback-route.png

<!-- Links -->

[lnk-sb-queues-java]: ../service-bus-messaging/service-bus-java-how-to-use-queues.md

[usługi Azure Storage]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/

[Centrum IoT — przewodnik dewelopera]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Rozpoczynanie pracy z Centrum IoT]: iot-hub-java-java-getstarted.md
[Azure IoT Developer Center]: https://azure.microsoft.com/develop/iot
[obsługi błędów przejściowych]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Obsługa błędu przejściowego]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-c2d]: iot-hub-java-java-c2d.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-free-trial]: https://azure.microsoft.com/free/
