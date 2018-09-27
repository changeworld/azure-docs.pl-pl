---
title: Pakiet Azure IoT Suite i usługi Logic Apps | Dokumentacja firmy Microsoft
description: Samouczek dotyczący sposobu podpiąć aplikacji logiki do usługi Azure IoT Suite dla procesów biznesowych.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 4629a7af-56ca-4b21-a769-5fa18bc3ab07
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: corywink
ms.openlocfilehash: 4a1db86f4b715533dfea545365eaf66de0574c5e
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47106924"
---
# <a name="tutorial-connect-logic-app-to-your-azure-iot-suite-remote-monitoring-preconfigured-solution"></a>Samouczek: Łączenie aplikacji logiki do usługi Azure IoT Suite zdalne monitorowanie wstępnie skonfigurowanego rozwiązania
[Microsoft Azure IoT Suite] [ lnk-internetofthings] we wstępnie skonfigurowanym rozwiązaniu monitorowania zdalnego jest to doskonały sposób, aby szybko rozpocząć pracę z zestawem funkcji end-to-end, który exemplifies rozwiązania IoT. W tym samouczku przedstawiono sposób dodawania aplikacji logiki do usługi Microsoft Azure IoT Suite ze wstępnie skonfigurowanym rozwiązaniem monitorowania zdalnego. Te kroki pokazują, jak można wykonać jeszcze bardziej rozwiązania IoT, łącząc go proces biznesowy.

*Jeśli szukasz przewodnika dotyczącego aprowizacji, zdalne monitorowanie ze wstępnie skonfigurowanym rozwiązaniem, zobacz [samouczek: wprowadzenie do wstępnie skonfigurowanych rozwiązań IoT][lnk-getstarted].*

Przed rozpoczęciem tego samouczka, należy:

* Aprowizowanie wstępnie skonfigurowanego rozwiązania do monitorowania zdalnego w ramach subskrypcji platformy Azure.
* Utwórz konto usługi SendGrid umożliwia wysyłanie wiadomości e-mail, która powoduje uruchomienie konkretnego procesu biznesowego. Użytkownik może Załóż bezpłatne konto próbne w [SendGrid](https://sendgrid.com/) , klikając **Wypróbuj za darmo**. Po zarejestrowaniu się dla bezpłatnego konta wersji próbnej, musisz utworzyć [klucz interfejsu API](https://sendgrid.com/docs/User_Guide/Settings/api_keys.html) w SendGrid, który przyznaje uprawnienia do wysyłania wiadomości e-mail. Ten klucz interfejsu API są potrzebne w dalszej części tego samouczka.

Do ukończenia tego samouczka, potrzebujesz programu Visual Studio 2015 lub Visual Studio 2017, aby zmodyfikować akcje wstępnie skonfigurowanego rozwiązania zaplecza.

Zakładając, że został już aprowizowany, zdalne monitorowanie wstępnie skonfigurowane rozwiązanie, przejdź do grupy zasobów dla tego rozwiązania w [witryny Azure portal][lnk-azureportal]. Grupa zasobów ma taką samą nazwę jak nazwa rozwiązania została wybrana opcja podczas aprowizacji rozwiązania do monitorowania zdalnego. W grupie zasobów widać wszystkie aprowizowane zasoby platformy Azure dla Twojego rozwiązania. Poniższy zrzut ekranu przedstawia przykład **grupy zasobów** bloku do zdalnego monitorowania wstępnie skonfigurowanego rozwiązania:

![](media/iot-suite-v1-logic-apps-tutorial/resourcegroup.png)

Aby rozpocząć, skonfiguruj aplikacji logiki, za pomocą wstępnie skonfigurowanego rozwiązania.

## <a name="set-up-the-logic-app"></a>Konfigurowanie aplikacji logiki
1. Kliknij przycisk **Dodaj** w górnej części bloku grupy zasobów w witrynie Azure portal.
2. Wyszukaj **aplikacji logiki**, zaznacz go, a następnie kliknij przycisk **Utwórz**.
3. Wypełnij **nazwa** i używać tego samego **subskrypcji** i **grupy zasobów** które zostały użyte podczas aprowizacji rozwiązania do monitorowania zdalnego. Kliknij pozycję **Utwórz**.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/createlogicapp.png)
4. Po zakończeniu wdrożenia, widać, że aplikacja logiki jest wyświetlany jako zasób w grupie zasobów.
5. Kliknij aplikację logiki, aby przejść do bloku aplikacji logiki, wybierz opcję **pusta aplikacja logiki** szablonu, aby otworzyć **Projektant aplikacji logiki**.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/logicappsdesigner.png)
6. Wybierz **żądania**. Ta akcja określa, czy przychodzące żądania HTTP z określonym JSON sformatowane ładunku działa jako wyzwalacz.
7. Wklej następujący kod do schemat JSON treści żądania:
   
    ```json
    {
      "$schema": "http://json-schema.org/draft-04/schema#",
      "id": "/",
      "properties": {
        "DeviceId": {
          "id": "DeviceId",
          "type": "string"
        },
        "measuredValue": {
          "id": "measuredValue",
          "type": "integer"
        },
        "measurementName": {
          "id": "measurementName",
          "type": "string"
        }
      },
      "required": [
        "DeviceId",
        "measurementName",
        "measuredValue"
      ],
      "type": "object"
    }
    ```
   
   > [!NOTE]
   > Po zakończeniu Zapisz aplikację logiki, ale najpierw należy dodać akcję, możesz skopiować adres URL HTTP post.
   > 
   > 
8. Kliknij przycisk **+ nowy krok** w obszarze wyzwalacza ręcznego. Następnie kliknij przycisk **Dodaj akcję**.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/logicappcode.png)
9. Wyszukaj **SendGrid — Wyślij wiadomość e-mail** i kliknij ją.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/logicappaction.png)
10. Wprowadź nazwę połączenia, taką jak **SendGridConnection**, wprowadź **klucz interfejsu API SendGrid** zostały utworzone podczas konfigurowania konta usługi SendGrid i kliknij przycisk **Utwórz**.
    
    ![](media/iot-suite-v1-logic-apps-tutorial/sendgridconnection.png)
11. Dodaj adresy e-mail, jesteś właścicielem zarówno **z** i **do** pola. Dodaj **zdalnego alert monitorowania [DeviceId]** do **podmiotu** pola. W **treść wiadomości E-mail** pola, Dodaj **urządzenia [DeviceId] zgłosił [measurementName] za pomocą wartości [measuredValue].** Możesz dodać **[DeviceId]**, **[measurementName]**, i **[measuredValue]** , klikając w **można wstawić danych z poprzednich kroków** sekcja.
    
    ![](media/iot-suite-v1-logic-apps-tutorial/sendgridaction.png)
12. Kliknij przycisk **Zapisz** w górnym menu.
13. Kliknij przycisk **żądania** wyzwalacza i skopiuj **żądania Http Post do tego adresu URL** wartość. Ten adres URL jest potrzebne w dalszej części tego samouczka.

> [!NOTE]
> Aplikacje logiki umożliwiają uruchamianie [wiele różnych typów akcji] [ lnk-logic-apps-actions] łącznie z działaniami w usłudze Office 365. 
> 
> 

## <a name="set-up-the-eventprocessor-web-job"></a>Konfigurowanie zadania Web EventProcessor
W tej sekcji wstępnie skonfigurowanego rozwiązania jest połączenie z utworzoną aplikację logiki. Aby wykonać to zadanie, należy dodać adres URL wyzwalacza aplikacji logiki na akcję w przypadku przekroczenia progu przez wartość czujników urządzenia.

1. Użyj klienta git, aby sklonować najnowszą wersję [azure-iot-remote-monitoring repozytorium github][lnk-rmgithub]. Na przykład:
   
    ```cmd
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```
2. W programie Visual Studio, otwórz **RemoteMonitoring.sln** z lokalnej kopii repozytorium.
3. Otwórz **ActionRepository.cs** w pliku **infrastruktury\\repozytorium** folderu.
4. Aktualizacja **actionIds** słownika przy użyciu **żądania Http Post do tego adresu URL** zauważyć z aplikacji logiki w następujący sposób:
   
    ```csharp
    private Dictionary<string,string> actionIds = new Dictionary<string, string>()
    {
        { "Send Message", "<Http Post to this URL>" },
        { "Raise Alarm", "<Http Post to this URL>" }
    };
    ```
5. Zapisz zmiany w rozwiązaniu, a następnie zamknij program Visual Studio.

## <a name="deploy-from-the-command-line"></a>Wdrażanie z wiersza polecenia
W tej sekcji możesz wdrożyć zaktualizowaną wersję rozwiązania monitorowania zdalnego, aby zastąpić aktualnie uruchomione na platformie Azure w wersji.

1. Następujące [konfiguracji dev] [ lnk-devsetup] instrukcje dotyczące konfigurowania środowiska na potrzeby wdrożenia.
2. Aby wdrożyć lokalnie, wykonaj [lokalne wdrożenie] [ lnk-localdeploy] instrukcje.
3. Aby wdrożyć w chmurze i zaktualizować istniejące wdrożenia w chmurze, postępuj zgodnie z [wdrożenie w chmurze] [ lnk-clouddeploy] instrukcje. Użyj nazwy oryginalnego wdrożenia jako nazwa wdrożenia. Na przykład jeśli oryginalne wdrożenie zostało wywołane **demologicapp**, użyj następującego polecenia:
   
   ```cmd
   build.cmd cloud release demologicapp
   ```
   
   Po uruchomieniu skryptu kompilacji należy używać tego samego konta platformy Azure, subskrypcji, regionów i wystąpieniem usługi Active Directory, które zostały użyte podczas aprowizacji rozwiązania.

## <a name="see-your-logic-app-in-action"></a>Zobacz w działaniu aplikacji logiki
Wstępnie skonfigurowane rozwiązania do monitorowania zdalnego ma dwie reguły konfigurowane domyślnie podczas przeprowadzania aprowizacji rozwiązania. Obie reguły znajdują się na **SampleDevice001** urządzenia:

* Temperatura > 38.00
* Wilgotność > 48.00

Wyzwolenie reguły temperatury **alarmów zwiększyć** akcji i wilgotność reguły wyzwalaczy **SendMessage** akcji. Zakładając, że używasz tego samego adresu URL dla obu akcji **ActionRepository** klasy użytkownika wyzwalacze aplikacji logiki dla każdej reguły. Obie reguły za pośrednictwem usługi SendGrid, Wyślij wiadomość e-mail do **do** adres ze szczegółami alertu.

> [!NOTE]
> Aplikacja logiki w dalszym ciągu wyzwalać za każdym razem, gdy jest próg. Aby uniknąć niepotrzebnych wiadomości e-mail, można wyłączyć reguły w portalu rozwiązania lub wyłączyć aplikację logiki w [witryny Azure portal][lnk-azureportal].
> 
> 

Oprócz otrzymywania wiadomości e-mail, będzie również widoczna podczas uruchomienia aplikacji logiki w portalu:

![](media/iot-suite-v1-logic-apps-tutorial/logicapprun.png)

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy wykorzystano aplikację logiki procesu biznesowego nawiązać połączenia wstępnie skonfigurowane rozwiązanie, możesz dowiedzieć się więcej opcji dostosowywania wstępnie skonfigurowanych rozwiązań:

* [Korzystanie z telemetrii dynamicznej z wstępnie skonfigurowanego rozwiązania do monitorowania zdalnego][lnk-dynamic]
* [Urządzenie informacji o metadanych w wstępnie skonfigurowanego rozwiązania do monitorowania zdalnego][lnk-devinfo]

[lnk-dynamic]: iot-suite-v1-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-v1-remote-monitoring-device-info.md

[lnk-internetofthings]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-getstarted]: iot-suite-v1-getstarted-preconfigured-solutions.md
[lnk-azureportal]: https://portal.azure.com
[lnk-logic-apps-actions]: ../connectors/apis-list.md
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devsetup]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/dev-setup.md
[lnk-localdeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/local-deployment.md
[lnk-clouddeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/cloud-deployment.md
