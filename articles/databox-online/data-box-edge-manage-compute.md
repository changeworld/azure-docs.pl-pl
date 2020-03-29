---
title: Zarządzanie obliczeniami usługi Azure Data Box Edge | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób zarządzania ustawieniami obliczeniowymi urządzenia Edge, takimi jak wyzwalacz, moduły, wyświetlanie konfiguracji obliczeniowej, usuwanie konfiguracji za pośrednictwem witryny Azure portal w witrynie Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/20/2019
ms.author: alkohli
ms.openlocfilehash: a9daf1d59b03d283be999aaab559c6d60f6405dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65953124"
---
# <a name="manage-compute-on-your-azure-data-box-edge"></a>Zarządzanie obliczeniami na platformie Azure Data Box Edge

W tym artykule opisano sposób zarządzania obliczeniami na platformie Azure Data Box Edge. Można zarządzać obliczenia za pośrednictwem witryny Azure portal lub za pośrednictwem lokalnego interfejsu użytkownika sieci web. Użyj witryny Azure Portal do zarządzania modułami, wyzwalaczami i konfiguracją obliczeniową oraz lokalnym interfejsem użytkownika sieci web do zarządzania ustawieniami obliczeniowymi.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Zarządzanie wyzwalaczami
> * Zarządzanie konfiguracją obliczeniową


## <a name="manage-triggers"></a>Zarządzanie wyzwalaczami

Zdarzenia to zdarzenia, które dzieją się w środowisku chmury lub na urządzeniu, na których możesz chcieć podjąć działania. Na przykład, gdy plik jest tworzony w udziale, jest to zdarzenie. Wyzwalacze podnieść zdarzenia. W przypadku usługi Data Box Edge wyzwalacze mogą być odpowiedzią na zdarzenia plików lub harmonogram.

- **Plik**: Te wyzwalacze są odpowiedzią na zdarzenia pliku, takie jak tworzenie pliku, modyfikacja pliku.
- **Zaplanowane**: Te wyzwalacze są odpowiedzią na harmonogram, który można zdefiniować za pomocą daty rozpoczęcia, godziny rozpoczęcia i interwału powtarzania.


### <a name="add-a-trigger"></a>Dodawanie wyzwalacza

Aby utworzyć wyzwalacz, należy wykonać następujące kroki w witrynie Azure portal.

1. W witrynie Azure portal przejdź do zasobu usługi Data Box Edge, a następnie przejdź do **aplikacji Edge compute > Trigger**. Wybierz **+ Dodaj wyzwalacz** na pasku poleceń.

    ![Wybierz wyzwalacz dodawania](media/data-box-edge-manage-compute/add-trigger-1.png)

2. W **dodaj blok wyzwalacza** podaj unikatową nazwę wyzwalacza.
    
    <!--Trigger names can only contain numbers, lowercase letters, and hyphens. The share name must be between 3 and 63 characters long and begin with a letter or a number. Each hyphen must be preceded and followed by a non-hyphen character.-->

3. Wybierz **typ** dla wyzwalacza. Wybierz **pozycję Plik,** gdy wyzwalacz jest odpowiedzią na zdarzenie pliku. Wybierz **opcję Zaplanowane,** gdy wyzwalacz ma zostać uruchomiony o zdefiniowanej godzinie i uruchomiony w określonym przedziale powtarzania. W zależności od wyboru zostanie przedstawiony inny zestaw opcji.

    - **Wyzwalacz pliku** — wybierz z listy rozwijanej zainstalowany udział. Gdy zdarzenie pliku jest uruchamiany w tej usłudze, wyzwalacz wywoła funkcję platformy Azure.

        ![Dodawanie udziału SMB](media/data-box-edge-manage-compute/add-file-trigger.png)

    - **Zaplanowany wyzwalacz** — określ datę/godzinę rozpoczęcia oraz interwał powtarzania w godzinach, minutach lub sekundach. Należy również wprowadzić nazwę tematu. Temat daje elastyczność, aby przekierować wyzwalacz do modułu wdrożonego na urządzeniu.

        Przykładowy ciąg trasy `"route3": "FROM /* WHERE topic = 'topicname' INTO BrokeredEndpoint("modules/modulename/inputs/input1")"`to: .

        ![Dodawanie udziału NFS](media/data-box-edge-manage-compute/add-scheduled-trigger.png)

4. Wybierz **dodaj,** aby utworzyć wyzwalacz. Powiadomienie pokazuje, że tworzenie wyzwalacza jest w toku. Po utworzeniu wyzwalacza bloku jest aktualizowana w celu odzwierciedlenia nowego wyzwalacza.
 
    ![Zaktualizowana lista wyzwalaczy](media/data-box-edge-manage-compute/add-trigger-2.png)

### <a name="delete-a-trigger"></a>Usuwanie wyzwalacza

Aby usunąć wyzwalacz, należy wykonać następujące kroki w witrynie Azure portal.

1. Z listy wyzwalaczy wybierz wyzwalacz, który chcesz usunąć.

    ![Wybierz wyzwalacz](media/data-box-edge-manage-compute/add-trigger-1.png)

2. Kliknij prawym przyciskiem myszy, a następnie wybierz polecenie **Usuń**.

    ![Wybierz usuń](media/data-box-edge-manage-compute/add-trigger-1.png)

3. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**.

    ![Potwierdzenie usunięcia](media/data-box-edge-manage-compute/add-trigger-1.png)

Lista wyzwalaczy jest aktualizowana w celu odzwierciedlenia usunięcia.

## <a name="manage-compute-configuration"></a>Zarządzanie konfiguracją obliczeniową

Użyj witryny Azure Portal, aby wyświetlić konfigurację obliczeń, usunąć istniejącą konfigurację obliczeniową lub odświeżyć konfigurację obliczeniową, aby zsynchronizować klucze dostępu dla urządzenia IoT i urządzenia Usługi IoT Edge dla usługi Data Box Edge.

### <a name="view-compute-configuration"></a>Wyświetlanie konfiguracji obliczeniowej

Aby wyświetlić konfigurację obliczeniową urządzenia, należy wykonać następujące kroki w witrynie Azure Portal.

1. W witrynie Azure portal przejdź do zasobu usługi Data Box Edge, a następnie przejdź do **modułów obliczeniowych > modułów.** Wybierz **opcję Wyświetl obliczenia** na pasku poleceń.

    ![Wybierz widok obliczeń](media/data-box-edge-manage-compute/view-compute-1.png)

2. Zanotuj konfigurację obliczeń na urządzeniu. Podczas konfigurowania obliczeń utworzono zasób Usługi IoT Hub. W ramach tego zasobu Usługi IoT Hub skonfigurowane jest urządzenie IoT i urządzenie usługi IoT Edge. Tylko moduły Systemu Linux są obsługiwane do uruchamiania na urządzeniu IoT Edge.

    ![Konfiguracja widoku](media/data-box-edge-manage-compute/view-compute-2.png)


### <a name="remove-compute-configuration"></a>Usuwanie konfiguracji obliczeniowej

Aby usunąć istniejącą konfigurację obliczeniową urządzenia, należy wykonać następujące kroki w witrynie Azure Portal.

1. W witrynie Azure portal przejdź do zasobu usługi Data Box Edge, a następnie przejdź do **aplikacji Edge > Wprowadzenie**. Wybierz **pozycję Usuń obliczenia** na pasku poleceń.

    ![Wybierz pozycję Usuń obliczenia](media/data-box-edge-manage-compute/remove-compute-1.png)

2. Jeśli usuniesz konfigurację obliczeniową, musisz ponownie skonfigurować urządzenie w przypadku, gdy konieczne jest ponowne użycie obliczeń. Po wyświetleniu monitu o potwierdzenie wybierz pozycję **Tak**.

    ![Wybierz pozycję Usuń obliczenia](media/data-box-edge-manage-compute/remove-compute-2.png)

### <a name="sync-up-iot-device-and-iot-edge-device-access-keys"></a>Synchronizowanie funkcji Urządzenia IoT i kluczy dostępu do urządzenia Usługi IoT Edge

Podczas konfigurowania obliczeń na krawędzi pola danych tworzone jest urządzenie IoT i urządzenie usługi IoT Edge. Urządzenia te są automatycznie przypisywane symetryczne klucze dostępu. Ze względów bezpieczeństwa te klucze są regularnie obracane za pośrednictwem usługi IoT Hub.

Aby obrócić te klawisze, można przejść do utworzonej usługi IoT Hub i wybrać urządzenie IoT lub urządzenie Usługi IoT Edge. Każde urządzenie ma podstawowy klucz dostępu i dodatkowe klucze dostępu. Przypisz podstawowy klucz dostępu do pomocniczego klucza dostępu, a następnie ponownie wygeneruj podstawowy klucz dostępu.

Jeśli urządzenie IoT i klucze urządzenia Usługi IoT Edge zostały obrócene, należy odświeżyć konfigurację na urządzeniu Data Box Edge, aby uzyskać najnowsze klucze dostępu. Synchronizacja pomaga urządzeniu uzyskać najnowsze klucze dla urządzenia IoT i urządzenia Usługi IoT Edge. Data Box Edge używa tylko podstawowych kluczy dostępu.

Aby zsynchronizować klucze dostępu do urządzenia, należy wykonać następujące kroki w witrynie Azure Portal.

1. W witrynie Azure portal przejdź do zasobu usługi Data Box Edge, a następnie przejdź do **aplikacji Edge > Wprowadzenie**. Wybierz **pozycję Odśwież konfigurację** na pasku poleceń.

    ![Wybierz konfigurację odświeżania](media/data-box-edge-manage-compute/refresh-configuration-1.png)

2. Po wyświetleniu monitu o potwierdzenie wybierz pozycję **Tak.**

     ![Po wyświetleniu monitu wybierz pozycję Tak](media/data-box-edge-manage-compute/refresh-configuration-2.png)

3. Po zakończeniu synchronizacji zamknij okno dialogowe.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [zarządzać siecią obliczeniową edge za pośrednictwem witryny Azure portal](data-box-edge-extend-compute-access-modules.md).
