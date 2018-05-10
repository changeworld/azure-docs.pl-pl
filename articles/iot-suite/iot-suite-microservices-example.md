---
title: Zmiany i ponownie wdrożyć mikrousługi | Dokumentacja firmy Microsoft
description: W tym samouczku przedstawiono sposób zmiany i ponownie wdrożyć mikrousługi w monitorowania zdalnego
services: ''
suite: iot-suite
author: giyeh
manager: hegate
ms.author: giyeh
ms.service: iot-suite
ms.date: 04/19/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: dc18437d554889655963270cdc051fb52325f09e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="customize-and-redeploy-a-microservice"></a>Dostosowywanie i Wdróż ponownie mikrousługi

Ten samouczek pokazuje, jak edytować jeden mikrousług w rozwiązaniu Monitorowanie zdalne, utworzyć obraz z mikrousługi wdrożenia obrazu do Centrum docker i następnie użyć jej w rozwiązaniu Monitorowanie zdalne. Aby wprowadzić tę koncepcję, w samouczku podstawowy scenariusz, w którym wywołać mikrousługi interfejsu API i zmienić komunikat o stanie z "Aktywności i również" do "New edytuje Made tutaj!"

Zdalne rozwiązania monitorowanie używa mikrousług, które są tworzone przy użyciu rozwiązania docker obrazów, które są pobierane z Centrum docker. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

>[!div class="checklist"]
> * Edytuj i kompilacji mikrousługi w rozwiązaniu Monitorowanie zdalne
> * Utwórz obraz docker
> * Wypychanie do Centrum docker obrazu docker
> * Pobierać nowy obraz docker
> * Wizualizuj zmiany 

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego samouczka, potrzebne są:

>[!div class="checklist"]
> * [Wdrażanie zdalne wstępnie skonfigurowane rozwiązanie monitorowania lokalnie](iot-suite-remote-monitoring-deploy-local.md)
> * [Konto Docker](https://hub.docker.com/)
> * [Postman](https://www.getpostman.com/) — jest to wymagane, aby wyświetlić odpowiedzi interfejsu API

## <a name="call-the-api-and-view-response-status"></a>Wywołanie interfejsu API i sprawdź stan odpowiedzi

W tej części należy wywołać mikrousługi Menedżera Centrum IoT domyślnego interfejsu API. Interfejs API zwraca komunikat o stanie później zmienić dostosowując mikrousługi.

1. Upewnij się, że to rozwiązanie monitorowanie zdalnego działa lokalnie na komputerze.
2. Znajdź, którego pobrano Postman i otwórz go.
3. W Postman, wprowadź następujące opcje w GET: http://localhost:8080/iothubmanager/v1/status.
4. Wyświetlanie zwracany i powinien zostać wyświetlony, "Status": "OK: aktywności i dobrze".
![Komunikat aktywności i dobrze Postman](media/iot-suite-microservices-example/postman-alive-well.png)

## <a name="change-the-status-and-build-the-image"></a>Zmień stan, a następnie utworzyć obraz

Teraz zmienić komunikat o stanie z mikrousługi Menedżera Centrum Iot "Nowe wprowadzone w tym miejscu!" a następnie ponownie obrazu docker o nowy stan. Jeśli wystąpią problemy, w tym miejscu można skorzystać z naszych [Rozwiązywanie problemów](#Troubleshoot) sekcji.

1. Upewnij się, że jest otwarty terminala i przejdź do katalogu, w którym zostały sklonowane rozwiązanie monitorowania zdalnego. 
2. Zmień katalog na "... azure-iot-pcs-remote-monitoring-dotnet/iothub-manager/WebService/v1/Controllers".
3. Otwórz StatusController.cs w dowolnym edytorze tekstów lub IDE, który chcesz. 
4. Znajdź następujący kod:

    ```javascript
    return new StatusApiModel(true, "Alive and well");
    ```

    i zmień ją na poniższy kod i zapisz go.

    ```javascript
    return new StatusApiModel(true, "New Edits Made Here!");
    ```

5. Wróć do terminalu, ale teraz zmienić do następującego katalogu: "... azure-iot-pcs-remote-monitoring-dotnet/iothub-manager/scripts/docker".
6. Aby utworzyć nowy obraz docker, wpisz:

    ```cmd/sh
    sh build
    ```

7. Aby sprawdzić, czy pomyślnie utworzono nowy obraz, wpisz

    ```cmd/sh
    docker images 
    ```

Repozytorium powinien być "azureiotpcs/Centrum iothub-manager-dotnet".

![Obraz pomyślnie docker](media/iot-suite-microservices-example/successful-docker-image.png)

## <a name="tag-and-push-the-image"></a>Tag i wypychania obrazu
Zanim nowy obraz docker można wypchnąć do Centrum docker, Docker oczekuje obrazów do oznaczone tagami. Jeśli wystąpią problemy, w tym miejscu można skorzystać z naszych [Rozwiązywanie problemów](#Troubleshoot) sekcji.

1. Zlokalizuj identyfikator obrazu obrazu docker utworzonego przez wpisanie:

    ```cmd/sh
    docker images
    ```

2. Aby dodać tagi obrazu z typu "Testowanie"

    ```cmd/sh
    docker tag [Image ID] [docker ID]/iothub-manager-dotnet:testing 
    ```

3. Aby wypchnąć nowo oznakowanych obrazu do Centrum docker, wpisz

    ```cmd/sh
    docker push [docker ID]/iothub-manager-dotnet:testing
    ```

4. Otwórz przeglądarkę internetową i przejdź do Twojej [Centrum docker](https://hub.docker.com/) i zaloguj się.
5. Obraz nowo wciśnięcia docker powinien zostać wyświetlony w Centrum docker.
![Obraz docker w Centrum docker](media/iot-suite-microservices-example/docker-image-in-docker-hub.png)

## <a name="update-your-remote-monitoring-solution"></a>Zaktualizuj rozwiązanie monitorowania zdalnego
Należy teraz zaktualizować sieci lokalnej docker-compose.yml, ściągania nowego obrazu docker z Centrum docker. Jeśli wystąpią problemy, w tym miejscu można skorzystać z naszych [Rozwiązywanie problemów](#Troubleshoot) sekcji.

1. Wróć do terminala i przejdź do następującego katalogu: "... Azure-iot-PCs-Remote-Monitoring-DotNet/scripts/Local".
2. Otwórz docker-compose.yml w dowolnym edytorze tekstów lub IDE, który chcesz.
3. Znajdź następujący kod:

    ```docker
    image: azureiotpcs/pcs-auth-dotnet:testing
    ```

    i zmień go na wygląda jak obraz poniżej i zapisz go.

    ```cmd/sh
    image: [docker ID]/pcs-auth-dotnet:testing
    ```

## <a name="view-the-new-response-status"></a>Nowy stan odpowiedzi
Aby zakończyć, ponownego wdrażania wystąpienie lokalne rozwiązanie monitorowania zdalnego i wyświetlanie nowych odpowiedź o stanie w Postman.

1. Wróć na terminalu i przejdź do następującego katalogu: "... Azure-iot-PCs-Remote-Monitoring-DotNet/scripts/Local".
2. Uruchom z lokalnego wystąpienia rozwiązanie monitorowania zdalnego, wpisując następujące polecenie w terminalu:

    ```cmd/sh
    docker-compose up
    ```

3. Znajdź, którego pobrano Postman i otwórz go.
4. W Postman, wprowadź następujące żądania w GET: http://localhost:8080/iothubmanager/v1/status. Powinien zostać wyświetlony, "Status": "OK: nowe wprowadzone w tym miejscu!".

![Nowy edytuje wprowadzone w tym miejscu postman komunikat](media/iot-suite-microservices-example/new-postman-message.png)

## <a name="Troubleshoot"></a>Rozwiązywanie problemów

Jeśli pracujesz na problemy, spróbuj usunąć obrazy usługi docker i kontenerów na komputerze lokalnym.

1. Aby usunąć wszystkie kontenery, musisz najpierw Zatrzymaj wszystkie uruchomione kontenerów. Otworzyć terminal i typ

    ```cmd/sh
    docker stop $(docker ps -aq)
    docker rm $(docker ps -aq)
    ```
    
2. Aby usunąć wszystkie obrazy, należy otworzyć terminal i typ 

    ```cmd/sh
    docker rmi $(docker images -q)
    ```

3. Można sprawdzić, czy wszystkie kontenery na maszynie, wpisując

    ```cmd/sh
    docker ps -aq 
    ```

    Jeśli pomyślnie usunięto wszystkich kontenerów, nic nie powinny być widoczne.

4. Można sprawdzić, czy wszystkie obrazy na komputerze, wpisując

    ```cmd/sh
    docker images
    ```

    Jeśli pomyślnie usunięto wszystkich kontenerów, nic nie powinny być widoczne.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Edytuj i kompilacji mikrousługi w rozwiązaniu Monitorowanie zdalne
> * Utwórz obraz docker
> * Wypychanie do Centrum docker obrazu docker
> * Pobierać nowy obraz docker
> * Wizualizuj zmiany 

Operacją dalej, aby spróbować jest [Dostosowywanie mikrousługi symulator urządzeń w rozwiązaniu Monitorowanie zdalne](iot-suite-remote-monitoring-test.md)

Aby uzyskać więcej informacji o deweloperów o rozwiązaniu monitorowania zdalnego Zobacz:

* [Przewodnik informacyjny dla deweloperów](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
<!-- Next tutorials in the sequence -->

