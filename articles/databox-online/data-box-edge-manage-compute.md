---
title: Azure krawędź pola danych zarządzania przetwarzaniem | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób zarządzania ustawieniami obliczeń brzegowych, takich jak wyzwalacz, moduły, konfiguracja obliczeń widoku, Usuń konfigurację za pomocą witryny Azure portal na krawędzi sieci Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/26/2019
ms.author: alkohli
ms.openlocfilehash: 58c4f42859f735a81a3e3edc801daff5d26194a0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60757978"
---
# <a name="manage-compute-on-your-azure-data-box-edge"></a>Zarządzanie obliczeniami na krawędzi sieci Azure Data Box

W tym artykule opisano sposób zarządzania Obliczanie na krawędzi sieci Azure Data Box. Można zarządzać zasobami obliczeniowymi za pomocą witryny Azure portal lub za pomocą lokalnego interfejsu użytkownika sieci web. Używać witryny Azure portal do zarządzania modułami, wyzwalacze, obliczeniowych i konfiguracji i lokalnego internetowego interfejsu użytkownika do zarządzania ustawieniami obliczeń.

> [!IMPORTANT]
> Usługa Data Box Edge jest dostępna w wersji zapoznawczej. Przed zamówieniem i wdrożeniem tego rozwiązania zapoznaj się z [warunkami świadczenia usług Azure w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Zarządzanie wyzwalaczy
> * Zarządzanie konfiguracją obliczeniową


## <a name="manage-triggers"></a>Zarządzanie wyzwalaczy

Zdarzenia są rzeczy, które odbywa się w Twoim środowisku chmury lub na urządzeniu, które prawdopodobnie chcą podejmij działanie. Na przykład gdy tworzony jest plik w udziale, to zdarzenie. Wyzwalacze zgłaszać zdarzenia. Dla Twojej przeglądarki Edge pole danych wyzwalaczy może być w odpowiedzi na zdarzenia w pliku lub harmonogramu.

- **Plik**: Te wyzwalacze są w odpowiedzi na zdarzenia plików takie jak utworzenie pliku modyfikacji pliku.
- **Zaplanowane**: Te wyzwalacze są w odpowiedzi na harmonogram, który można zdefiniować przy użyciu datę rozpoczęcia, godzinę rozpoczęcia i interwału powtarzania.


### <a name="add-a-trigger"></a>Dodaj wyzwalacz

Wykonaj następujące kroki w witrynie Azure portal, aby utworzyć wyzwalacz.

1. W witrynie Azure portal przejdź do zasobu krawędź pola danych, a następnie przejdź do **obliczeń brzegowych > wyzwalacza**. Wybierz **+ Dodaj wyzwalacz** na pasku poleceń.

    ![Wybieranie opcji dodawania wyzwalacza](media/data-box-edge-manage-compute/add-trigger-1.png)

2. W **Dodaj wyzwalacz** bloku, Podaj unikatową nazwę dla wyzwalacza.
    
    <!--Trigger names can only contain numbers, lowercase letters, and hyphens. The share name must be between 3 and 63 characters long and begin with a letter or a number. Each hyphen must be preceded and followed by a non-hyphen character.-->

3. Wybierz **typu** wyzwalacza. Wybierz **pliku** gdy wyzwalacz nie działa w odpowiedzi na zdarzenie otrzymania pliku. Wybierz **zaplanowane** , aby wyzwalacz, aby uruchomić w określonym czasie i przeprowadzić z określonym interwałem Powtórz tę procedurę. W zależności od wyboru zobaczy innego zbioru opcji.

    - **Wyzwalacz pliku** — wybierz z listy rozwijanej zainstalowanego udziału. Gdy plik jest wywoływane w tym udziale, wyzwalacz powodowałoby wywołanie pliku wykonywalnego funkcji platformy Azure.

        ![Dodawanie udziału SMB](media/data-box-edge-manage-compute/add-file-trigger.png)

    - **Zaplanowane wyzwalanie** — Podaj Data/Godzina rozpoczęcia i interwału powtarzania w godzinach, minuty lub sekundy. Ponadto wprowadź nazwę tematu. Temat zapewniają elastyczność przekierowywać wyzwalacza do moduł, który został wdrożony na urządzeniu.

        To przykładowy ciąg znaków trasy: `"route3": "FROM /* WHERE topic = 'topicname' INTO BrokeredEndpoint("modules/modulename/inputs/input1")"`.

        ![Dodawanie udziału NFS](media/data-box-edge-manage-compute/add-scheduled-trigger.png)

4. Wybierz **Dodaj** do utworzenia wyzwalacza. Powiadomienie zawiera informację, że trwa tworzenie wyzwalacza. Po utworzeniu wyzwalacza bloku aktualizowany w celu odzwierciedlenia nowego wyzwalacza.
 
    ![Lista wyzwalaczy zaktualizowane](media/data-box-edge-manage-compute/add-trigger-2.png)

### <a name="delete-a-trigger"></a>Usuwanie wyzwalacza

Wykonaj następujące kroki w witrynie Azure portal, można usunąć wyzwalacza.

1. Z listy wyzwalaczy wybierz wyzwalacz, który chcesz usunąć.

    ![Wybierz wyzwalacz](media/data-box-edge-manage-compute/add-trigger-1.png)

2. Kliknij prawym przyciskiem myszy, a następnie wybierz pozycję **Usuń**.

    ![Wybierz opcję Usuń](media/data-box-edge-manage-compute/add-trigger-1.png)

3. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**.

    ![Potwierdzenie usunięcia](media/data-box-edge-manage-compute/add-trigger-1.png)

Lista aktualizacji wyzwalaczy w celu odzwierciedlenia usunięcia.

## <a name="manage-compute-configuration"></a>Zarządzanie konfiguracją obliczeniową

Użyj witryny Azure portal, aby wyświetlić konfigurację obliczeniową, usuń istniejącą konfigurację obliczeń lub Odśwież konfigurację obliczeń, aby zsynchronizować klucze dostępu dla urządzeń IoT i urządzenia usługi IoT Edge na krawędzi sieci pola danych.

### <a name="view-compute-configuration"></a>Wyświetlanie konfiguracji obliczeniowej

Wykonaj następujące kroki w witrynie Azure portal, aby wyświetlić konfigurację obliczeniowe dla danego urządzenia.

1. W witrynie Azure portal przejdź do zasobu krawędź pola danych, a następnie przejdź do **obliczeń brzegowych > modułów**. Wybierz **wyświetlić obliczeń** na pasku poleceń.

    ![Wybierz opcję Oblicz widoku](media/data-box-edge-manage-compute/view-compute-1.png)

2. Zanotuj konfigurację obliczeniową na urządzeniu. Po skonfigurowaniu obliczeń utworzono zasób usługi IoT Hub. W ramach tego zasobu usługi IoT Hub skonfigurowano urządzenia IoT i urządzenia usługi IoT Edge. Uruchamianie na urządzeniu usługi IoT Edge, obsługiwane są tylko moduły systemu Linux.

    ![Wyświetl konfigurację](media/data-box-edge-manage-compute/view-compute-2.png)


### <a name="remove-compute-configuration"></a>Usuń konfigurację obliczeniową

Wykonaj następujące kroki w witrynie Azure portal, aby usunąć istniejącą konfigurację obliczeń brzegowych dla Twojego urządzenia.

1. W witrynie Azure portal przejdź do zasobu krawędź pola danych, a następnie przejdź do **obliczeń brzegowych > Rozpocznij**. Wybierz **Usuń obliczeń** na pasku poleceń.

    ![Wybierz opcję Oblicz Remove](media/data-box-edge-manage-compute/remove-compute-1.png)

2. Jeśli usuniesz konfigurację obliczeniową, należy zmienić konfigurację urządzenia, w razie potrzeby ponownie użyć obliczeń. Po wyświetleniu monitu o potwierdzenie, wybierz **tak**.

    ![Wybierz opcję Oblicz Remove](media/data-box-edge-manage-compute/remove-compute-2.png)

### <a name="sync-up-iot-device-and-iot-edge-device-access-keys"></a>Synchronizowanie urządzeń IoT i urządzeniami usługi IoT Edge klucze dostępu

Po skonfigurowaniu obliczeń na krawędzi pola danych, sieci urządzeń IoT i urządzenia usługi IoT Edge są tworzone. Te urządzenia są automatycznie przypisywane klucze symetryczne dostępu. Ze względów bezpieczeństwa te klucze są obracane regularnie za pośrednictwem usługi IoT Hub.

Aby obrócić tych kluczy, możesz przejść do usługi IoT Hub, który został utworzony i wybierz urządzenia IoT lub urządzenie usługi IoT Edge. Każde urządzenie ma podstawowy klucz dostępu i klawisze dostępu dodatkowej. Przypisz podstawowy klucz dostępu do pomocniczy klucz dostępu, a następnie ponownie Wygeneruj podstawowy klucz dostępu.

Jeśli obracać urządzeniu IoT i klucze urządzeń usługi IoT Edge, należy odświeżyć konfigurację sieci krawędź pola danych można pobrać najnowsze kluczy dostępu. Synchronizacja pomaga urządzenie, Pobierz najnowszy klucze dla urządzeń IoT i urządzeniami usługi IoT Edge. Krawędź pola danych używa tylko klucze dostępu.

Wykonaj następujące kroki w witrynie Azure portal, aby zsynchronizować klucze dostępu dla urządzenia.

1. W witrynie Azure portal przejdź do zasobu krawędź pola danych, a następnie przejdź do **obliczeń brzegowych > Rozpocznij**. Wybierz **odświeżanie konfiguracji** na pasku poleceń.

    ![Wybierz konfigurację odświeżania](media/data-box-edge-manage-compute/refresh-configuration-1.png)

2. Wybierz **tak** po wyświetleniu monitu o potwierdzenie.

     ![Wybierz pozycję Tak, po wyświetleniu monitu](media/data-box-edge-manage-compute/refresh-configuration-2.png)

3. Po zakończeniu synchronizacji zamknij okno dialogowe.

## <a name="enable-a-network-interface-for-compute"></a>Włączanie interfejsu sieciowego dla środowiska obliczeniowego

Może być konieczne dostępu modułu, uruchomione na urządzeniu usługi Edge pola danych. Aby uzyskać dostęp do modułu zewnętrznie, musisz przypisać adres IP do interfejsu sieciowego na urządzeniu z systemem. Możesz zarządzać te obliczenia ustawienia z Twojego lokalnego internetowego interfejsu użytkownika.

Wykonaj następujące kroki w lokalnej sieci web UI, aby skonfigurować ustawienia obliczeń.

1. W lokalnym internetowym interfejsie użytkownika, przejdź do **konfiguracji > Compute ustawienia**.  

2. **Włącz** interfejsu sieciowego, który ma być używana do łączenia z modułami obliczeń na urządzeniu. 

    - Jeśli używasz statycznych adresów IP, wprowadź adres IP dla interfejsu sieciowego.
    - Jeśli używasz protokołu DHCP, następnie adresów IP zostanie automatycznie przypisana.

3. Wybierz **Zastosuj** mają zostać zastosowane ustawienia.

    ![Włącz ustawienia obliczeń](media/data-box-edge-manage-compute/compute-settings-1.png)


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [Zarządzać użytkownikami w witrynie Azure Portal](data-box-edge-manage-users.md).
