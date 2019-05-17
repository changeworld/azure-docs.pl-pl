---
title: Samouczek, aby filtrować, analizowania danych na potrzeby zaawansowanego wdrożenia z mocą obliczeniową na krawędzi pola danych platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować rolę obliczeń na krawędzi pola danych i używać go do przekształcania danych na potrzeby zaawansowanego wdrożenia przepływu przed wysłaniem do platformy Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 05/13/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge for advanced deployment flow so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 836a3f3c17c4cf11ac972b7cc129fb3f83093024
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794009"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge-for-advanced-deployment-flow"></a>Samouczek: Przekształcanie danych za pomocą krawędź pola danych platformy Azure dla wdrożenia zaawansowanego przepływu

W tym samouczku opisano sposób konfigurowania roli obliczeniowej dla przepływu wdrożenia zaawansowanego na urządzeniu z systemem usługi Azure Data Box Edge. Po skonfigurowaniu roli obliczeniowej urządzenie Data Box Edge może przekształcać dane przed wysłaniem ich na platformę Azure.

Obliczeniowe można skonfigurować dla przepływ prostych lub zaawansowanych wdrożenia na urządzeniu.

|                  | Proste wdrażanie                                | Zaawansowanego wdrażania                   |
|------------------|--------------------------------------------------|---------------------------------------|
| Przeznaczony dla     | Administratorzy systemów informatycznych                                | Deweloperzy                            |
| Type             | Użyj usługi krawędź pola danych, aby wdrożyć moduły      | Użyj usługi IoT Hub, aby wdrożyć moduły |
| Wdrożone moduły | Single                                           | Tworzenie łańcucha lub wiele modułów           |


Ta procedura może zająć około 20 – 30 minut.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie obliczeń
> * Dodaj udziały
> * Dodaj wyzwalacz
> * Dodawanie modułu obliczeniowego
> * Weryfikowanie przekształcania danych i transferu

 
## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem roli obliczeniowej na urządzeniu Data Box Edge upewnij się, że zostały spełnione następujące warunki:

- Urządzenie Data Box Edge zostało aktywowane zgodnie z opisem w temacie [Podłączanie, konfigurowanie i aktywowanie urządzenia Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Konfigurowanie obliczeń

Aby skonfigurować obliczeń na krawędzi sieci pole danych, należy utworzyć zasób usługi IoT Hub.

1. W witrynie Azure portal zasobu krawędź pola danych, przejdź do **Przegląd**. W okienku po prawej stronie na **obliczenia** kafelka, wybierz opcję **wprowadzenie**.

    ![Rozpoczynanie pracy z mocą obliczeniową](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-1.png)

2. Na **obliczeń skonfigurować krawędzi** kafelka, wybierz opcję **Konfigurowanie obliczeń**.

    ![Rozpoczynanie pracy z mocą obliczeniową](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-2.png)

3. Na **obliczeń skonfigurować krawędzi** bloku wprowadzić następujące:

   
    |Pole  |Wartość  |
    |---------|---------|
    |Usługa IoT Hub     | Wybierz z **nowe** lub **istniejących**. <br> Domyślnie w warstwie standardowa (S1) służy do tworzenia zasobu usługi IoT. Aby korzystać z zasobów IoT w warstwie bezpłatna, utwórz ją, a następnie wybierz istniejący zasób. <br> W każdym przypadku zasobu usługi IoT Hub korzysta z tej samej subskrypcji i grupie zasobów, który jest używany przez zasób krawędź pola danych.     |
    |Name (Nazwa)     |Wprowadź nazwę zasobu usługi IoT Hub.         |

    ![Rozpoczynanie pracy z mocą obliczeniową](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-3.png)

4. Wybierz pozycję **Utwórz**. Tworzenie zasobów usługi IoT Hub zajmuje kilka minut. Po utworzeniu zasobu usługi IoT Hub **obliczeń skonfigurować krawędzi** Kafelek aktualizacji, aby wyświetlić konfigurację obliczeniową. Aby potwierdzić, czy skonfigurowano roli obliczeń brzegowych, zaznacz **config widoku** na **Konfigurowanie obliczeń** kafelka.
    
    ![Rozpoczynanie pracy z mocą obliczeniową](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-4.png)

    Po skonfigurowaniu roli funkcji obliczeniowej Edge na urządzeniu Edge tworzone są dwa urządzenia: urządzenie IoT i urządzenie IoT Edge. Oba urządzenia można wyświetlić w zasobie usługi IoT Hub. Środowisko uruchomieniowe IoT Edge jest także uruchomiona na tym urządzeniu usługi IoT Edge.

    W chwili obecnej dla urządzenia IoT Edge jest dostępna tylko platforma Linux.


## <a name="add-shares"></a>Dodaj udziały

Zaawansowane wdrożenia w ramach tego samouczka będą potrzebne dwa udziały: jeden udział krawędzi i innego udziału lokalnego krawędzi.

1. Dodaj udział Edge na urządzeniu, wykonując następujące czynności:

    1. W swoim zasobie krawędź pola danych, przejdź do **obliczeń brzegowych > Rozpocznij**.
    2. Na **Dodawanie udziałów** kafelka, wybierz opcję **Dodaj**.
    3. Na **Dodaj udział** bloku, podaj nazwę udziału, a następnie wybierz typ udziału.
    4. Aby zainstalować udział krawędzi, zaznacz pole wyboru dla **udziału za pomocą obliczeń brzegowych**.
    5. Wybierz **konta magazynu**, **usługi Storage**, istniejącego użytkownika, a następnie wybierz **Utwórz**.

        ![Dodaj udział do krawędzi](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-1.png)

    <!--If you created a local NFS share, use the following remote sync (rsync) command option to copy files onto the share:

    `rsync <source file path> < destination file path>`

    For more information about the rsync command, go to [Rsync documentation](https://www.computerhope.com/unix/rsync.htm).-->

    Po utworzeniu udziału krawędzi, otrzymasz powiadomienie o pomyślnym utworzeniu. Lista udziału jest aktualizowana w celu odzwierciedlenia nowego udziału.

2. Dodawanie udziału lokalnego usługi Edge na urządzeniu usługi Edge powtarzające się wszystkie kroki opisane w poprzednim kroku, a następnie zaznaczając pole wyboru **Konfigurowanie udziału lokalnego krawędzi**. Dane w udziale lokalnym pozostaje na urządzeniu.

    ![Dodawanie udziału lokalnego usługi Edge](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-2.png)

3. Na **udziałów** bloku można wyświetlić zaktualizowaną listę udziałów.

    ![Zaktualizowana lista udziałów](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-3.png)

4. Aby wyświetlić właściwości nowo utworzonej udziału lokalnego, wybierz udział z listy. W polu **Lokalny punkt instalacji dla modułów funkcji obliczeniowej Edge** skopiuj wartość odpowiadającą temu udziałowi.

    Użyjesz tego lokalnego punktu instalacji podczas wdrażania modułu.

    ![Pole „Lokalny punkt instalacji dla modułów funkcji obliczeniowej Edge”](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-4.png)
 
5. Aby wyświetlić właściwości udziału krawędzi, który został utworzony, wybierz udział z listy. W polu **Lokalny punkt instalacji dla modułów funkcji obliczeniowej Edge** skopiuj wartość odpowiadającą temu udziałowi.

    Użyjesz tego lokalnego punktu instalacji podczas wdrażania modułu.

    ![Dodawanie modułu niestandardowego](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-5.png)


## <a name="add-a-trigger"></a>Dodaj wyzwalacz

1. Przejdź do **obliczeń brzegowych > wyzwalaczy**. Wybierz **+ Dodaj wyzwalacz**.

    ![Dodaj wyzwalacz](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-1.png)

2. W **Dodaj wyzwalacz** bloku, wprowadź następujące wartości.

    |Pole  |Wartość  |
    |---------|---------|
    |Nazwa wyzwalacza     | Unikatowa nazwa wyzwalacza.         |
    |Typ wyzwalacza     | Wybierz **pliku** wyzwalacza. Plik wyzwalacza przy każdym wystąpieniu zdarzenia pliku, np. plik jest zapisywany do udziału danych wejściowych. Zaplanowane wyzwalacz z drugiej strony, uruchamia się zgodnie z harmonogramem, zdefiniowane przez użytkownika. W tym przykładzie należy wyzwalacza pliku.    |
    |Udział w danych wejściowych     | Wybierz udział danych wejściowych. Krawędź lokalny udział w tym przypadku jest udział w danych wejściowych. Moduł, który został użyty tutaj przenosi pliki z udziału lokalnego krawędź z udziałem krawędzi gdzie są przekazywane do chmury.        |

    ![Dodaj wyzwalacz](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-2.png)

3. Otrzymasz powiadomienie po utworzeniu wyzwalacza. Na liście wyzwalaczy jest zaktualizowany i będzie pokazywał nowo utworzonego wyzwalacza. Wybierz wyzwalacz, który został utworzony.

    ![Dodaj wyzwalacz](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-3.png)

4. Skopiuj i Zapisz trasy próbki. Będzie zmodyfikować tę trasę próbki i używać go później w Centrum IoT Hub.

    `"sampleroute": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/modulename/inputs/input1\")"`

    ![Dodaj wyzwalacz](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-4.png)

## <a name="add-a-module"></a>Dodaj moduł

Nie istnieją żadne moduły niestandardowe na tym urządzeniu usługi Edge. Można dodać niestandardowy lub wstępnie skompilowanych modułu. Aby dowiedzieć się, jak utworzyć moduł niestandardowy, przejdź do artykułu [Tworzenie modułu C# dla urządzenia Data Box Edge](data-box-edge-create-iot-edge-module.md).

W tej sekcji dodasz do urządzenia usługi IoT Edge moduł niestandardowy utworzony w temacie [Tworzenie modułu C# dla urządzenia Data Box Edge](data-box-edge-create-iot-edge-module.md). Ten moduł niestandardowego pobiera pliki z udziałem lokalnym Edge na urządzeniu usługi Edge i przenosi je do udziału krawędzi (w chmurze) na urządzeniu. Następnie udział chmurowy wypycha pliki na konto magazynu platformy Azure skojarzone z tym udziałem chmurowym.

1. Przejdź do **obliczeń brzegowych > Rozpocznij**. Na **Dodaj moduły** kafelka, wybierz typ scenariusza jako **zaawansowane**. Wybierz **przejdź do Centrum IoT Hub**.

    ![Wybieranie pozycji wdrożenie zaawansowane](./media/data-box-edge-deploy-configure-compute-advanced/add-module-1.png)

<!--2. In the **Configure and add module** blade, input the following values:  

    |Input share     | Select an input share. The Edge local share is the input share in this case. The module used here moves files from the Edge local share to an Edge share where they are uploaded into the cloud.        |
    |Output share     | Select an output share. The Edge share is the output share in this case.        |
-->

2. W zasobie usługi IoT Hub, przejdź do **urządzenie usługi IoT Edge** a następnie wybierz urządzenia usługi IoT Edge.

    ![Przejdź do usługi IoT Edge urządzenia w usłudze IoT Hub](./media/data-box-edge-deploy-configure-compute-advanced/add-module-2.png)

3. Na **szczegóły urządzenia**, wybierz opcję **Ustaw moduły**.

    ![Link Ustawianie modułów](./media/data-box-edge-deploy-configure-compute-advanced/add-module-3.png)

4. W obszarze **Dodawanie modułów** wykonaj następujące czynności:

    1. Wprowadź nazwę, adres, nazwę użytkownika i hasło dla ustawień rejestru kontenerów dla modułu niestandardowego.
    Nazwa, adres i wymienione poświadczenia są używane do pobierania modułów za pomocą zgodnego adresu URL. Aby wdrożyć ten moduł, w obszarze **Deployment modules** (Moduły wdrażania) wybierz opcję **IoT Edge module** (Moduł usługi IoT Edge). Ten moduł usługi IoT Edge to kontener platformy Docker, który można wdrożyć na urządzeniu usługi IoT Edge skojarzonym z Twoim urządzeniem Data Box Edge.

        ![Strona Ustawianie modułów](./media/data-box-edge-deploy-configure-compute-advanced/add-module-4.png) 
 
    2. Określ ustawienia dla niestandardowego modułu usługi IoT Edge. Wprowadź następujące wartości.
     
        |Pole  |Wartość  |
        |---------|---------|
        |Name (Nazwa)     | Unikatowa nazwa modułu. Ten moduł jest kontener platformy docker, którą można wdrożyć na urządzeniu usługi IoT Edge skojarzonych krawędzi pola sieci danych.        |
        |Identyfikator URI obrazu     | Identyfikator URI obrazu dla odpowiadających im obrazów kontenera dla modułu.        |
        |Poświadczenia są wymagane     | Jeśli zaznaczone, nazwę użytkownika i hasło są używane do pobierania modułów za pomocą dopasowywania adresu URL.        |
    
        W **opcje tworzenia kontenera** wprowadź punkty instalacji lokalnej moduły graniczne, które kopiowane w poprzednich krokach dla udziału Edge i przeglądarki Edge lokalny udział.

        > [!IMPORTANT]
        > Ścieżki używane w tym miejscu są instalowane w Twoim kontenerze, więc musi odpowiadać oczekiwaniom funkcji w kontenerze. Jeśli wykonujesz [Tworzenie niestandardowego modułu](data-box-edge-create-iot-edge-module.md#update-the-module-with-custom-code), określony w tym module kod oczekuje, że skopiowany ścieżki. Nie należy modyfikować tych ścieżek.
    
        W polu **Opcje tworzenia kontenera** możesz wkleić poniższy przykład:
    
        ```
        {
            "HostConfig": {
            "Binds": [
            "/home/hcsshares/mydbesmbedgelocalshare1:/home/input",
            "/home/hcsshares/mydbesmbedgeshare1:/home/output"
            ]
            }
        }
        ```

        Podaj wszelkie zmienne środowiskowe używane dla modułu. Zmienne środowiskowe zawierają informacje opcjonalne ułatwiające definiują środowisko, w którym działa modułu.

        ![Pole Opcje tworzenia kontenera](./media/data-box-edge-deploy-configure-compute-advanced/add-module-5.png) 
 
    4. Jeśli to konieczne, skonfiguruj zaawansowane ustawienia środowiska uruchomieniowego Edge, a następnie kliknij pozycję **Dalej**.

        ![Dodawanie modułu niestandardowego](./media/data-box-edge-deploy-configure-compute-advanced/add-module-6.png)
 
5.  W obszarze **Określanie tras** ustaw trasy między modułami.  
    
    ![Określanie tras](./media/data-box-edge-deploy-configure-compute-advanced/add-module-7.png)

    Możesz zastąpić *trasy* przy użyciu następujących parametrów trasy, skopiowany wcześniej. W tym przykładzie podaj nazwę udziału lokalnego, który wypchnie dane do udziału chmurowego. Zastąp `modulename` z nazwą modułu. Wybierz opcję **Dalej**.
        
    ```
    "route": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/filemove/inputs/input1\")"
    ```

    ![Sekcja Określanie tras](./media/data-box-edge-deploy-configure-compute-advanced/add-module-8.png)

6.  W obszarze **Przejrzyj wdrożenie** sprawdź wszystkie ustawienia, a następnie wybierz pozycję **Prześlij**, aby przesłać moduł do wdrożenia.

    ![Strona Ustawianie modułów](./media/data-box-edge-deploy-configure-compute-advanced/add-module-9.png)
 
    Ta akcja rozpoczyna się wdrażanie modułu. Po zakończeniu wdrożenia **stan czasu wykonywania** modułu jest **systemem**.

    ![Dodawanie modułu niestandardowego](./media/data-box-edge-deploy-configure-compute-advanced/add-module-10.png)

## <a name="verify-data-transform-transfer"></a>Weryfikowanie danych przekształcania, przesunięcia

Ostatnim krokiem jest upewnienie się, że moduł jest połączony i działa zgodnie z oczekiwaniami. Stan środowiska uruchomieniowego modułu powinien pokazywać uruchomienie dla urządzenia usługi IoT Edge w zasobie usługi IoT Hub.

Wykonaj następujące kroki, aby sprawdzić, przekształcania danych i przesyłanie danych do platformy Azure.
 
1.  W Eksploratorze plików połączyć do krawędzi lokalnych i udziałów Edge, która została utworzona wcześniej.

    ![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-2.png)
 
1.  Dodaj dane do udziału lokalnego.

    ![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-3.png)
 
    Dane zostaną przeniesione do udziału chmurowego.

    ![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-4.png)  

    Następnie dane zostaną wypchnięte z udziału chmurowego na konto magazynu. Aby wyświetlić dane, przejdź do swojego konta magazynu, a następnie wybierz **Eksploratora usługi Storage**. Możesz wyświetlić przekazane dane na koncie magazynu.

    ![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-5.png)
 
Ukończono proces walidacji.

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie obliczeń
> * Dodaj udziały
> * Dodaj wyzwalacz
> * Dodawanie modułu obliczeniowego
> * Weryfikowanie przekształcania danych i transferu

Aby dowiedzieć się, jak administrować urządzeniem Data Box Edge, zobacz:

> [!div class="nextstepaction"]
> [Administrowanie usługą Data Box Edge za pomocą lokalnego internetowego interfejsu użytkownika](data-box-edge-manage-access-power-connectivity-mode.md)
