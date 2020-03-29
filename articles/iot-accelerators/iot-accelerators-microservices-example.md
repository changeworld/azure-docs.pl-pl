---
title: Zmienianie i ponowne rozmieszczanie mikrousług — platforma Azure | Dokumenty firmy Microsoft
description: W tym samouczku pokazano, jak zmienić i ponownie wdrożyć mikrousługę w zdalnym monitorowaniu
author: dominicbetts
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 04/19/2018
ms.topic: conceptual
ms.openlocfilehash: 1552c54afe2195d58a032e9cc7bfa5aa70c844b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447628"
---
# <a name="customize-and-redeploy-a-microservice"></a>Dostosowywanie i ponowne wdrażanie mikrousługi

W tym samouczku pokazano, jak edytować jedną z [mikrousług](https://azure.com/microservices) w rozwiązaniu zdalnego monitorowania, utworzyć obraz mikrousługi, wdrożyć obraz w centrum docker, a następnie użyć go w rozwiązaniu zdalnego monitorowania. Aby wprowadzić tę koncepcję, samouczek używa podstawowego scenariusza, w którym można wywołać interfejs API mikrousług i zmienić komunikat o stanie z "Alive and Well" na "Nowe zmiany wprowadzone tutaj!"

Rozwiązanie zdalnego monitorowania używa mikrousług, które są tworzone przy użyciu obrazów docker, które są pobierane z centrum docker. 

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

>[!div class="checklist"]
> * Edytowanie i tworzenie mikrousług w rozwiązaniu do zdalnego monitorowania
> * Tworzenie obrazu platformy docker
> * Wypychanie obrazu platformy docker do centrum platformy docker
> * Ciągnienie nowego obrazu dokowane
> * Wizualizuj zmiany 

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać ten samouczek, potrzebujesz:

>[!div class="checklist"]
> * [Lokalne wdrażanie akceleratora rozwiązań do zdalnego monitorowania](iot-accelerators-remote-monitoring-deploy-local.md)
> * [Konto platformy Docker](https://hub.docker.com/)
> * [Listonosz](https://www.getpostman.com/) — potrzebne do wyświetlenia odpowiedzi interfejsu API

## <a name="call-the-api-and-view-response-status"></a>Wywoływanie interfejsu API i wyświetlanie stanu odpowiedzi

W tej części wywołać domyślny interfejs API mikrousług menedżera centrum IoT. Interfejs API zwraca komunikat o stanie, który można zmienić później, dostosowując mikrousługi.

1. Upewnij się, że rozwiązanie do zdalnego monitorowania działa lokalnie na komputerze.
2. Znajdź miejsce, w którym pobrano listonosza i otwórz go.
3. W pliku Postman wprowadź następujące `http://localhost:8080/iothubmanager/v1/status`informacje w pliku GET: .
4. Zobacz zwrot i powinieneś zobaczyć"Status": "OK:Alive and Well".

    ![Żywa i dobrze listonosz wiadomość](./media/iot-accelerators-microservices-example/postman-alive-well.png)

## <a name="change-the-status-and-build-the-image"></a>Zmienianie stanu i tworzenie obrazu

Teraz zmień komunikat o stanie mikrousługi Iot Hub Manager na "Nowe zmiany wprowadzone tutaj!" a następnie odbuduj obraz docker z tym nowym stanem. Jeśli napotkasz problemy tutaj, zapoznaj się z naszą [sekcją Rozwiązywanie problemów.](#Troubleshoot)

1. Upewnij się, że terminal jest otwarty i zmień katalog, w którym sklonowane jest rozwiązanie do zdalnego monitorowania. 
1. Zmień katalog na "azure-iot-pcs-remote-monitoring-dotnet/services/iothub-manager/Services".
1. Otwórz StatusService.cs w dowolnym edytorze tekstu lub IDE, który ci się podoba. 
1. Znajdź następujący kod:

    ```csharp
    var result = new StatusServiceModel(true, "Alive and well!");
    ```

    i zmień go na poniższy kod i zapisz go.

    ```csharp
    var result = new StatusServiceModel(true, "New Edits Made Here!");
    ```

5. Wróć do terminalu, ale teraz zmień na następujący katalog: "azure-iot-pcs-remote-monitoring-dotnet/services/iothub-manager/scripts/docker".
6. Aby utworzyć nowy obraz docker, wpisz

    ```sh
    sh build
    ```
    
    lub w systemie Windows:
    
    ```cmd
    ./build.cmd
    ```

7. Aby sprawdzić, czy nowy obraz został pomyślnie utworzony, wpisz

    ```cmd/sh
    docker images 
    ```

Repozytorium powinno być "azureiotpcs/iothub-manager-dotnet".

![Pomyślny obraz platformy docker](./media/iot-accelerators-microservices-example/successful-docker-image.png)

## <a name="tag-and-push-the-image"></a>Tagowanie i wypychanie obrazu
Zanim będzie można wypchnąć nowy obraz docker do centrum docker, docker oczekuje, że obrazy mają być oznaczone. Jeśli napotkasz problemy tutaj, zapoznaj się z naszą [sekcją Rozwiązywanie problemów.](#Troubleshoot)

1. Znajdź identyfikator obrazu docker utworzonego przez wpisanie:

    ```cmd/sh
    docker images
    ```

2. Aby oznaczyć obraz typem "testowania"

    ```cmd/sh
    docker tag [Image ID] [docker ID]/iothub-manager-dotnet:testing 
    ```

3. Aby wypchnąć nowo oznakowany obraz do centrum docker, wpisz

    ```cmd/sh
    docker push [docker ID]/iothub-manager-dotnet:testing
    ```

4. Otwórz przeglądarkę internetową i przejdź do [centrum docker](https://hub.docker.com/) i zaloguj się.
5. Nowo wypchnięty obraz platformy docker powinien być teraz widoczny w centrum docker.
![Obraz platformy Docker w centrum docker](./media/iot-accelerators-microservices-example/docker-image-in-docker-hub.png)

## <a name="update-your-remote-monitoring-solution"></a>Aktualizowanie rozwiązania do zdalnego monitorowania
Teraz musisz zaktualizować lokalny plik docker-compose.yml, aby wyciągnąć nowy obraz platformy docker z centrum docker. Jeśli napotkasz problemy tutaj, zapoznaj się z naszą [sekcją Rozwiązywanie problemów.](#Troubleshoot)

1. Wróć do terminala i zmień na następujący katalog: "azure-iot-pcs-remote-monitoring-dotnet/services/scripts/local".
2. Otwórz docker-compose.yml w dowolnym edytorze tekstu lub IDE, który ci się podoba.
3. Znajdź następujący kod:

    ```yml
    image: azureiotpcs/iothub-manager-dotnet:testing
    ```

    i zmień go tak, aby wyglądał jak obrazek poniżej i zapisz go.

    ```yml
    image: [docker ID]/iothub-manager-dotnet:testing
    ```

## <a name="view-the-new-response-status"></a>Wyświetlanie nowego stanu odpowiedzi
Zakończ, ponownie rozmieszczając lokalne wystąpienie rozwiązania zdalnego monitorowania i wyświetlając nową odpowiedź stanu w postmanie.

1. Wróć do terminalu i zmień do następującego katalogu: "azure-iot-pcs-remote-monitoring-dotnet/scripts/local".
2. Uruchom lokalne wystąpienie rozwiązania do zdalnego monitorowania, wpisując następujące polecenie w terminalu:

    ```cmd/sh
    docker-compose up
    ```

3. Znajdź miejsce, w którym pobrano listonosza i otwórz go.
4. W pliku Postman wprowadź następujące żądanie `http://localhost:8080/iothubmanager/v1/status`w pliku GET: . Powinieneś teraz zobaczyć "Status": "OK: Nowe zmiany wprowadzone tutaj!".

![Nowe zmiany wykonane tutaj wiadomość listonosza](./media/iot-accelerators-microservices-example/new-postman-message.png)

## <a name="troubleshoot"></a><a name="Troubleshoot"></a>Rozwiązywanie problemów

Jeśli występują problemy, spróbuj usunąć obrazy platformy docker i kontenery na komputerze lokalnym.

1. Aby usunąć wszystkie kontenery, musisz najpierw zatrzymać wszystkie uruchomione kontenery. Otwórz terminal i wpisz

    ```cmd/sh
    docker stop $(docker ps -aq)
    docker rm $(docker ps -aq)
    ```
    
2. Aby usunąć wszystkie obrazy, otwórz terminal i wpisz 

    ```cmd/sh
    docker rmi $(docker images -q)
    ```

3. Możesz sprawdzić, czy na urządzeniu znajdują się jakieś pojemniki, wpisując

    ```cmd/sh
    docker ps -aq 
    ```

    Jeśli pomyślnie usunięto wszystkie kontenery, nic nie powinno się pojawiać.

4. Możesz sprawdzić, czy na urządzeniu są jakieś obrazy, wpisując

    ```cmd/sh
    docker images
    ```

    Jeśli pomyślnie usunięto wszystkie kontenery, nic nie powinno się pojawiać.

## <a name="next-steps"></a>Następne kroki

W tym samouczku, widziałeś, jak:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Edytowanie i tworzenie mikrousług w rozwiązaniu do zdalnego monitorowania
> * Tworzenie obrazu platformy docker
> * Wypychanie obrazu platformy docker do centrum platformy docker
> * Ciągnienie nowego obrazu dokowane
> * Wizualizuj zmiany 

Następną rzeczą do wypróbowania jest [dostosowanie mikrousługi symulatora urządzenia w rozwiązaniu Do zdalnego monitorowania](iot-accelerators-microservices-example.md)

Aby uzyskać więcej informacji dewelopera na temat rozwiązania do zdalnego monitorowania, zobacz:

* [Przewodnik informacyjny dla deweloperów](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
<!-- Next tutorials in the sequence -->

