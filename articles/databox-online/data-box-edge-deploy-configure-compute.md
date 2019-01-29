---
title: Przekształcanie danych za pomocą usługi Azure Data Box Edge | Microsoft Docs
description: Dowiedz się, jak skonfigurować rolę obliczeniową na urządzeniu Data Box Edge i przy jej użyciu przekształcić dane przed wysłaniem ich na platformę Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 11/27/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: c52c311f1e1cd1335ea5797eadacd0bc89e1b36c
ms.sourcegitcommit: c31a2dd686ea1b0824e7e695157adbc219d9074f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/18/2019
ms.locfileid: "54402119"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge-preview"></a>Samouczek: przekształcanie danych za pomocą usługi Azure Data Box Edge (wersja zapoznawcza)

W tym samouczku opisano sposób konfigurowania roli obliczeniowej na urządzeniu Azure Data Box Edge. Po skonfigurowaniu roli obliczeniowej urządzenie Data Box Edge może przekształcać dane przed wysłaniem ich na platformę Azure.

Wykonanie tej procedury może zająć od 30 do 45 minut.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zasobu usługi Azure IoT Hub
> * Konfigurowanie roli obliczeniowej
> * Dodawanie modułu obliczeniowego
> * Weryfikowanie przekształcania danych i transferu

> [!IMPORTANT]
> Usługa Data Box Edge jest dostępna w wersji zapoznawczej. Przed zamówieniem i wdrożeniem tego rozwiązania zapoznaj się z [warunkami świadczenia usług Azure w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem roli obliczeniowej na urządzeniu Data Box Edge upewnij się, że zostały spełnione następujące warunki:

* Urządzenie Data Box Edge zostało aktywowane zgodnie z opisem w temacie [Podłączanie, konfigurowanie i aktywowanie urządzenia Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="create-an-iot-hub-resource"></a>Tworzenie zasobu usługi IoT Hub

Zanim skonfigurujesz rolę obliczeniową na urządzeniu Data Box Edge, musisz utworzyć zasób usługi IoT Hub.

Aby uzyskać szczegółowe instrukcje, przejdź do tematu [Tworzenie centrum IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#create-an-iot-hub). Użyj tej samej subskrypcji i grupy zasobów, które zostały użyte dla zasobu usługi Data Box Edge.

![Tworzenie zasobu usługi IoT Hub](./media/data-box-edge-deploy-configure-compute/create-iothub-resource-1.png)

Jeśli rola funkcji obliczeniowej Edge nie została skonfigurowana, obowiązują następujące zastrzeżenia:

- zasób usługi IoT Hub nie ma żadnych urządzeń Azure IoT ani urządzeń Azure IoT Edge,
- nie możesz tworzyć udziałów lokalnych usługi Edge, podczas dodawania udziału opcja utworzenia udziału lokalnego na potrzeby funkcji obliczeniowej Edge nie jest włączona.


## <a name="set-up-compute-role"></a>Konfigurowanie roli obliczeniowej

Po skonfigurowaniu roli funkcji obliczeniowej Edge na urządzeniu Edge tworzone są dwa urządzenia: urządzenie IoT i urządzenie IoT Edge. Oba urządzenia można wyświetlić w zasobie usługi IoT Hub.

Aby skonfigurować rolę obliczeniową na urządzeniu, wykonaj następujące czynności:

1. Przejdź do zasobu usługi Data Box Edge, wybierz pozycję **Przegląd**, a następnie wybierz pozycję **Skonfiguruj rolę obliczeniową**. 

    ![Link Przegląd w okienku po lewej stronie](./media/data-box-edge-deploy-configure-compute/setup-compute-1.png)
   
    Opcjonalnie możesz przejść do obszaru **Moduły** i wybrać pozycję **Konfigurowanie obliczeń**.

    ![Linki „Moduły” i „Konfigurowanie obliczeń”](./media/data-box-edge-deploy-configure-compute/setup-compute-2.png)
 
1. Na liście rozwijanej wybierz **zasób usługi IoT Hub** utworzony w poprzednim kroku.  
    W chwili obecnej dla urządzenia IoT Edge jest dostępna tylko platforma Linux. 
    
1. Kliknij pozycję **Utwórz**.

    ![Przycisk Utwórz](./media/data-box-edge-deploy-configure-compute/setup-compute-3.png)
 
    Utworzenie roli obliczeniowej trwa kilka minut. Z powodu błędu w tej wersji, nawet gdy rola obliczeniowa jest już utworzona, ekran nie jest odświeżany. Aby upewnić się, czy rola funkcji obliczeniowej Edge została skonfigurowana, przejdź do obszaru **Moduły**.  

    ![Lista urządzeń „Konfigurowanie funkcji obliczeniowej Edge”](./media/data-box-edge-deploy-configure-compute/setup-compute-4.png)

1. Ponownie przejdź do obszaru **Przegląd**.  
    Teraz ekran jest zaktualizowany, aby wskazać, że skonfigurowano rolę obliczeniową.

    ![Konfigurowanie roli obliczeniowej](./media/data-box-edge-deploy-configure-compute/setup-compute-5.png)
 
1. W usłudze IoT Hub używanej podczas tworzenia roli funkcji obliczeniowej Edge przejdź na stronę **Urządzenia IoT**.  
    Urządzenie IoT jest teraz włączone. 

    ![Strona „Urządzenia IoT”](./media/data-box-edge-deploy-configure-compute/setup-compute-6.png)

1. W lewym okienku wybierz pozycję **IoT Edge**.  
    Urządzenie IoT Edge także jest włączone.

    ![Konfigurowanie roli obliczeniowej](./media/data-box-edge-deploy-configure-compute/setup-compute-7.png)
 
1. Wybierz i kliknij urządzenie usługi IoT Edge.  
    Na tym urządzeniu usługi IoT Edge jest uruchomiony agent usługi Edge. 

    ![Strona Szczegóły urządzenia](./media/data-box-edge-deploy-configure-compute/setup-compute-8.png) 

    Na tym urządzeniu Edge nie ma żadnych modułów niestandardowych, jednak teraz możesz taki moduł dodać. Aby dowiedzieć się, jak utworzyć moduł niestandardowy, przejdź do artykułu [Tworzenie modułu C# dla urządzenia Data Box Edge](data-box-edge-create-iot-edge-module.md).


## <a name="add-a-custom-module"></a>Dodawanie modułu niestandardowego

W tej sekcji dodasz do urządzenia usługi IoT Edge moduł niestandardowy utworzony w temacie [Tworzenie modułu C# dla urządzenia Data Box Edge](data-box-edge-create-iot-edge-module.md). 

W poniższej procedurze jest używany przykład, w którym moduł niestandardowy pobiera pliki z udziału lokalnego na urządzeniu Edge i przenosi je do udziału chmurowego na tym urządzeniu. Następnie udział chmurowy wypycha pliki na konto magazynu platformy Azure skojarzone z tym udziałem chmurowym. 

1. Dodaj udział lokalny na urządzeniu Edge, wykonując następujące czynności:

    a. W swoim zasobie usługi Data Box Edge przejdź do sekcji **Shares** (Udziały). 
    
    b. Wybierz pozycję **Dodaj udział**, a następnie podaj nazwę udziału i wybierz typ udziału. 
    
    d. Aby utworzyć udział lokalny, zaznacz pole wyboru **Skonfiguruj jako lokalny udział funkcji Edge**. 
    
    d. Wybierz pozycję **Utwórz nowy** lub **Użyj istniejącego**, a następnie wybierz pozycję **Utwórz**.

    ![Dodawanie modułu niestandardowego](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-1.png) 

    Jeśli utworzono lokalny udział NFS, użyj następującej opcji synchronizacji zdalnej (rsync), aby skopiować pliki do udziału:

    `rsync --inplace <source file path> < destination file path>`

    Aby uzyskać więcej informacji na temat polecenia rsync, przejdź do [dokumentacji narzędzia Rsync](https://www.computerhope.com/unix/rsync.htm). 

    Udział lokalny zostanie utworzony i otrzymasz powiadomienie o pomyślnym utworzeniu. Lista udziałów może zostać zaktualizowana, ale musisz poczekać na ukończenie tworzenia udziału.
    
1. Przejdź do listy udziałów. 

    ![Dodawanie modułu niestandardowego](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-2.png) 
 
1. Aby wyświetlić właściwości nowo utworzonego udziału lokalnego, wybierz go. 

1. W polu **Lokalny punkt instalacji dla modułów funkcji obliczeniowej Edge** skopiuj wartość odpowiadającą temu udziałowi.  
    Użyjesz tego lokalnego punktu instalacji podczas wdrażania modułu.

    ![Pole „Lokalny punkt instalacji dla modułów funkcji obliczeniowej Edge”](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-3.png) 
 
1. W istniejącym udziale chmurowym, który został utworzony na urządzeniu Data Box Edge, w polu **Lokalny punkt instalacji dla modułów funkcji obliczeniowej Edge** skopiuj lokalny punkt instalacji dla modułów funkcji obliczeniowej Edge dla tego udziału chmurowego.  
    Użyjesz tego lokalnego punktu instalacji podczas wdrażania modułu.

    ![Dodawanie modułu niestandardowego](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-4.png)  

1. Aby dodać moduł niestandardowy do urządzenia usługi IoT Edge, przejdź do zasobu usługi IoT Hub, a następnie przejdź do **urządzenia usługi IoT Edge**. 

1. Wybierz urządzenie, a następnie w obszarze **Szczegóły urządzenia** wybierz pozycję **Ustawianie modułów**. 

    ![Link Ustawianie modułów](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-5.png) 

1. W obszarze **Dodawanie modułów** wykonaj następujące czynności:

    a. Wprowadź nazwę, adres, nazwę użytkownika i hasło dla ustawień rejestru kontenerów dla modułu niestandardowego.  
    Nazwa, adres i wymienione poświadczenia są używane do pobierania modułów za pomocą zgodnego adresu URL. Aby wdrożyć ten moduł, w obszarze **Deployment modules** (Moduły wdrażania) wybierz opcję **IoT Edge module** (Moduł usługi IoT Edge). Ten moduł usługi IoT Edge to kontener platformy Docker, który można wdrożyć na urządzeniu usługi IoT Edge skojarzonym z Twoim urządzeniem Data Box Edge.

    ![Strona Ustawianie modułów](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-6.png) 
 
    b. Określ ustawienia dla niestandardowego modułu usługi IoT Edge, wprowadzając nazwę swojego modułu oraz identyfikator URI obrazu dla odpowiedniego obrazu kontenera. 
    
    ![Strona modułów niestandardowych w usłudze IoT Edge](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-7.png) 

    d. W polu **Opcje tworzenia kontenera** wprowadź lokalne punkty instalacji dla modułów usługi Edge, które zostały skopiowane w poprzednich krokach dla udziału lokalnego i chmurowego.
    > [!IMPORTANT]
    > Użyj skopiowanych ścieżek — nie twórz nowych. Lokalne punkty instalacji są mapowane na odpowiednie foldery **InputFolderPath** i **OutputFolderPath**, które zostały określone w module podczas [aktualizacji modułu przy użyciu kodu niestandardowego](data-box-edge-create-iot-edge-module.md#update-the-module-with-custom-code). 
    
    W polu **Opcje tworzenia kontenera** możesz wkleić poniższy przykład: 
    
    ```
    {
        "HostConfig": {
        "Binds": [
        "/home/hcsshares/mysmblocalshare:/home/LocalShare",
        "/home/hcsshares/mysmbshare1:/home/CloudShare"
        ]
        }
    }
    ```

    Podaj tu także wszelkie zmienne środowiskowe dla Twojego modułu.

    ![Pole Opcje tworzenia kontenera](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-8.png) 
 
    d. Jeśli to konieczne, skonfiguruj zaawansowane ustawienia środowiska uruchomieniowego Edge, a następnie kliknij pozycję **Dalej**.

    ![Dodawanie modułu niestandardowego](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-9.png) 
 
1.  W obszarze **Określanie tras** ustaw trasy między modułami.  
    W tym przykładzie podaj nazwę udziału lokalnego, który wypchnie dane do udziału chmurowego.

    *Trasę* można zastąpić następującym ciągiem trasy:       `"route": "FROM /* WHERE topic = 'mysmblocalshare' INTO BrokeredEndpoint(\"/modules/filemovemodule/inputs/input1\")"`

    ![Sekcja Określanie tras](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-10.png) 

1. Wybierz opcję **Dalej**. 

1.  W obszarze **Przejrzyj wdrożenie** sprawdź wszystkie ustawienia, a następnie wybierz pozycję **Prześlij**, aby przesłać moduł do wdrożenia.

    ![Strona Ustawianie modułów](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-11.png) 
 
    Ta akcja rozpoczyna wdrażanie modułu, jak pokazano na poniższej ilustracji:

    ![Dodawanie modułu niestandardowego](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-12.png) 

### <a name="verify-data-transform-and-transfer"></a>Weryfikowanie przekształcania danych i transferu

Ostatnim krokiem jest upewnienie się, że moduł jest połączony i działa zgodnie z oczekiwaniami. Stan środowiska uruchomieniowego modułu powinien pokazywać uruchomienie dla urządzenia usługi IoT Edge w zasobie usługi IoT Hub.

![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute/verify-data-transform-1.png) 
 
Wykonaj poniższe kroki, aby sprawdzić, czy moduł jest uruchomiony:

1. Wybierz moduł, a następnie wyświetl bliźniaczą reprezentację tożsamości modułu.  
    Stan klienta dla urządzenia Edge i modułu powinien być wyświetlany jako *Połączony*.

    ![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute/verify-data-transform-2.png) 
 
    Po uruchomieniu moduł powinien być także wyświetlany na liście modułów usługi Edge w zasobie usługi Data Box Edge. Stan środowiska uruchomieniowego dodanego modułu ma wartość *uruchomione*.

    ![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute/verify-data-transform-3.png) 
 
1.  W Eksploratorze plików połącz się z utworzonymi wcześniej udziałami: lokalnym i chmurowym.

    ![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute/verify-data-transform-4.png) 
 
1.  Dodaj dane do udziału lokalnego.

    ![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute/verify-data-transform-5.png) 
 
    Dane zostaną przeniesione do udziału chmurowego.

    ![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute/verify-data-transform-6.png)  

    Następnie dane zostaną wypchnięte z udziału chmurowego na konto magazynu. Aby wyświetlić dane, przejdź do Eksploratora usługi Storage.

    ![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute/verify-data-transform-7.png) 
 
Ukończono proces walidacji.


## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zasobu usługi IoT Hub
> * Konfigurowanie roli obliczeniowej
> * Dodawanie modułu obliczeniowego
> * Weryfikowanie przekształcania danych i transferu

Aby dowiedzieć się, jak administrować urządzeniem Data Box Edge, zobacz:

> [!div class="nextstepaction"]
> [Administrowanie usługą Data Box Edge za pomocą lokalnego internetowego interfejsu użytkownika](https://aka.ms/dbg-docs)


