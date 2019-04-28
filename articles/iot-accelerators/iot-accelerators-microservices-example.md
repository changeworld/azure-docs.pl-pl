---
title: Zmień i ponowne wdrażanie mikrousługi — Azure | Dokumentacja firmy Microsoft
description: W tym samouczku dowiesz się, jak zmienić i ponowne wdrażanie mikrousługi do zdalnego monitorowania
author: dominicbetts
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 04/19/2018
ms.topic: conceptual
ms.openlocfilehash: 1552c54afe2195d58a032e9cc7bfa5aa70c844b1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61447628"
---
# <a name="customize-and-redeploy-a-microservice"></a>Dostosowywanie i ponowne wdrażanie mikrousługi

W tym samouczku pokazano, jak edytować jeden z [mikrousług](https://azure.com/microservices) w rozwiązaniu monitorowania zdalnego tworzenia obrazu z mikrousług, wdrażania obrazu usługi docker hub, a następnie użyj go w rozwiązaniu do zdalnego monitorowania. Aby wprowadzić tę koncepcję, w tym samouczku użyto podstawowy scenariusz, w którym wywołanie interfejsu API mikrousługi i zmienić komunikat o stanie z "Aktywności i dobrze" do "New edytuje Made tutaj!"

Rozwiązania do zdalnego monitorowania korzysta z mikrousług, które zostały utworzone przy użyciu obrazów platformy docker, które są pobierane z usługi docker hub. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

>[!div class="checklist"]
> * Edytowanie i tworzenie mikrousług w rozwiązaniu do zdalnego monitorowania
> * Zbuduj obraz docker
> * Wypychanie obrazu platformy docker do usługi docker hub
> * Ściągnij nowy obraz platformy docker
> * Wizualizuj zmiany 

## <a name="prerequisites"></a>Wymagania wstępne

Aby skorzystać z tego samouczka, potrzebne są:

>[!div class="checklist"]
> * [Wdrażanie zdalne monitorowanie akceleratora rozwiązań lokalnie](iot-accelerators-remote-monitoring-deploy-local.md)
> * [Konta platformy Docker](https://hub.docker.com/)
> * [Postman](https://www.getpostman.com/) — jest to wymagane, aby wyświetlić odpowiedź interfejsu API

## <a name="call-the-api-and-view-response-status"></a>Wywołanie interfejsu API i Wyświetl stan odpowiedzi

W tej części jest wywoływana w mikrousługach Menedżera Centrum IoT domyślnego interfejsu API. Interfejs API zwraca komunikat o stanie, które później zmienić, dostosowując mikrousług.

1. Upewnij się, że rozwiązania do zdalnego monitorowania jest uruchomiony lokalnie na komputerze.
2. Znajdź, której pobrano narzędzie Postman i otwórz go.
3. W narzędziu Postman, wprowadź następujące informacje w GET: `http://localhost:8080/iothubmanager/v1/status`.
4. Wyświetl zwracany i powinien zostać wyświetlony, "Status": "OK: aktywności i dobrze".

    ![Komunikat o aktywności i dobrze narzędzia Postman](./media/iot-accelerators-microservices-example/postman-alive-well.png)

## <a name="change-the-status-and-build-the-image"></a>Zmiany stanu i tworzenia obrazu

Teraz zmienić komunikat o stanie z mikrousług Menedżera Centrum Iot "Nowe zmiany wprowadzone w tym miejscu!" a następnie ponownie utworzyć obraz platformy docker za pomocą tego nowego stanu. Jeśli napotkasz problemy, w tym miejscu można znaleźć na naszej [Rozwiązywanie problemów](#Troubleshoot) sekcji.

1. Upewnij się, że rozwarcia terminal i przejdź do katalogu, w którym zostały sklonowane rozwiązania do zdalnego monitorowania. 
1. Zmień katalog na "azure-iot-pcs-remote-monitoring-dotnet/services/iothub-manager/Services".
1. Otwórz StatusService.cs w dowolnym edytorze tekstu lub IDE, które chcesz. 
1. Znajdź następujący kod:

    ```csharp
    var result = new StatusServiceModel(true, "Alive and well!");
    ```

    Zmień go na poniższy kod i zapisz go.

    ```csharp
    var result = new StatusServiceModel(true, "New Edits Made Here!");
    ```

5. Wróć do terminala, ale teraz zmienić do następującego katalogu: "azure-iot-pcs-remote-monitoring-dotnet/services/iothub-manager/scripts/docker".
6. Aby utworzyć nowy obraz platformy docker, wpisz:

    ```sh
    sh build
    ```
    
    lub Windows:
    
    ```cmd
    ./build.cmd
    ```

7. Aby sprawdzić, czy nowy obraz został pomyślnie utworzony, wpisz

    ```cmd/sh
    docker images 
    ```

Repozytorium powinien być "azureiotpcs/iothub-manager-dotnet".

![Obraz pomyślnie platformy docker](./media/iot-accelerators-microservices-example/successful-docker-image.png)

## <a name="tag-and-push-the-image"></a>Tagowanie i wypychanie obrazu
Zanim będzie można wypchnąć nowy obraz platformy docker do usługi docker hub, Docker oczekuje obrazów do być oznakowane. Jeśli napotkasz problemy, w tym miejscu można znaleźć na naszej [Rozwiązywanie problemów](#Troubleshoot) sekcji.

1. Zlokalizuj identyfikator obrazu obrazu platformy docker, który został utworzony przez wpisanie:

    ```cmd/sh
    docker images
    ```

2. Tagowanie obrazu z typem "Testowanie"

    ```cmd/sh
    docker tag [Image ID] [docker ID]/iothub-manager-dotnet:testing 
    ```

3. Aby wypchnąć obraz nowo oznakowane do usługi docker hub, wpisz:

    ```cmd/sh
    docker push [docker ID]/iothub-manager-dotnet:testing
    ```

4. Otwórz przeglądarkę internetową i przejdź do swojej [usługi docker hub](https://hub.docker.com/) i zaloguj się.
5. Powinien zostać wyświetlony obraz nowo wypychanie platformy docker w usłudze docker hub.
![Obraz platformy docker w usłudze docker hub](./media/iot-accelerators-microservices-example/docker-image-in-docker-hub.png)

## <a name="update-your-remote-monitoring-solution"></a>Aktualizowanie rozwiązania do zdalnego monitorowania
Teraz musisz zaktualizować swoje lokalne docker-compose.yml, aby ściągnąć nowy obraz platformy docker z usługi docker hub. Jeśli napotkasz problemy, w tym miejscu można znaleźć na naszej [Rozwiązywanie problemów](#Troubleshoot) sekcji.

1. Wróć do terminala i przejdź do katalogu, do następujących: "azure-iot-pcs-remote-monitoring-dotnet/services/scripts/local".
2. Otwórz docker-compose.yml w dowolnym edytorze tekstu lub IDE, które chcesz.
3. Znajdź następujący kod:

    ```yml
    image: azureiotpcs/iothub-manager-dotnet:testing
    ```

    i zmień ją na wyglądać jak na poniższej ilustracji i zapisz go.

    ```yml
    image: [docker ID]/iothub-manager-dotnet:testing
    ```

## <a name="view-the-new-response-status"></a>Wyświetl nowy stan odpowiedzi
Aby zakończyć, wyświetlanie nowej odpowiedzi stanu w narzędziu Postman i ponowne wdrożenie lokalne wystąpienie programu rozwiązania do zdalnego monitorowania.

1. Wróć do terminala i przejdź do katalogu, do następujących: "azure-iot-pcs-remote-monitoring-dotnet/scripts/local".
2. Rozpocznij lokalnym wystąpieniem rozwiązania do zdalnego monitorowania, wpisując następujące polecenie w terminalu:

    ```cmd/sh
    docker-compose up
    ```

3. Znajdź, której pobrano narzędzie Postman i otwórz go.
4. W narzędziu Postman, wprowadź następujące żądanie w GET: `http://localhost:8080/iothubmanager/v1/status`. Powinien zostać wyświetlony, "Status": "OK: Nowe zmiany wprowadzone w tym miejscu! ".

![Nowa modyfikacje wprowadzone w tym miejscu postman wiadomość](./media/iot-accelerators-microservices-example/new-postman-message.png)

## <a name="Troubleshoot"></a>Rozwiązywanie problemów

Jeśli korzystasz z problemy, spróbuj usuwanie obrazów platformy docker i kontenerów na komputerze lokalnym.

1. Aby usunąć wszystkie kontenery, musisz najpierw Zatrzymaj wszystkie uruchomione kontenery. Otwórz terminal i wpisz polecenie usługi

    ```cmd/sh
    docker stop $(docker ps -aq)
    docker rm $(docker ps -aq)
    ```
    
2. Aby usunąć wszystkie obrazy, należy otworzyć terminal i typ 

    ```cmd/sh
    docker rmi $(docker images -q)
    ```

3. Możesz sprawdzić, czy wszystkie kontenery na maszynie, wpisując

    ```cmd/sh
    docker ps -aq 
    ```

    Jeśli pomyślnie usunięto wszystkie kontenery, nic nie powinny być widoczne.

4. Możesz sprawdzić, czy wszystkie obrazy na komputerze, wpisując

    ```cmd/sh
    docker images
    ```

    Jeśli pomyślnie usunięto wszystkie kontenery, nic nie powinny być widoczne.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku pokazano, jak:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Edytowanie i tworzenie mikrousług w rozwiązaniu do zdalnego monitorowania
> * Zbuduj obraz docker
> * Wypychanie obrazu platformy docker do usługi docker hub
> * Ściągnij nowy obraz platformy docker
> * Wizualizuj zmiany 

W następnej kolejności spróbować jest [Dostosowywanie mikrousług symulator urządzenia w rozwiązaniu do zdalnego monitorowania](iot-accelerators-microservices-example.md)

Aby uzyskać więcej informacji dla deweloperów o rozwiązaniu monitorowania zdalnego Zobacz:

* [Przewodnik informacyjny dla deweloperów](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
<!-- Next tutorials in the sequence -->

