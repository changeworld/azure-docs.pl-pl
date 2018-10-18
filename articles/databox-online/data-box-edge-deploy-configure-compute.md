---
title: Przekształcanie danych za pomocą usługi Azure Data Box Edge | Microsoft Docs
description: Dowiedz się, jak skonfigurować rolę obliczeniową na urządzeniu Data Box Edge i przy jej użyciu przekształcić dane przed wysłaniem ich na platformę Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/08/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 4729e08399132243543c6f4e1cadd537d185e9e3
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166257"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge-preview"></a>Samouczek: przekształcanie danych za pomocą usługi Azure Data Box Edge (wersja zapoznawcza)

W tym samouczku opisano sposób konfigurowania roli obliczeniowej na urządzeniu Data Box Edge. Po skonfigurowaniu roli obliczeniowej urządzenie Data Box Edge może przekształcać dane przed wysłaniem ich na platformę Azure.

Wykonanie tej procedury może zająć około 30-45 minut. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zasobu usługi IoT Hub
> * Konfigurowanie roli obliczeniowej
> * Dodawanie modułu obliczeniowego
> * Weryfikowanie przekształcania danych i transferu

> [!IMPORTANT]
> Usługa Data Box Edge jest dostępna w wersji zapoznawczej. Przed zamówieniem i wdrożeniem tego rozwiązania zapoznaj się z [warunkami świadczenia usług Azure w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
 
## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem funkcji obliczeniowej na urządzeniu Data Box Edge upewnij się, że zostały spełnione następujące warunki:

* Urządzenie Data Box Edge zostało aktywowane zgodnie z opisem w temacie [Podłączanie i aktywowanie urządzenia Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="create-an-iot-hub-resource"></a>Tworzenie zasobu usługi IoT Hub

Zanim skonfigurujesz rolę obliczeniową na urządzeniu Data Box Edge musisz utworzyć zasób usługi IoT Hub.

Aby uzyskać szczegółowe instrukcje, przejdź do tematu [Tworzenie centrum IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#create-an-iot-hub). Użyj tej samej subskrypcji i grupy zasobów, które zostały użyte dla zasobu usługi Data Box Edge.

![Tworzenie zasobu usługi IoT Hub](./media/data-box-edge-deploy-configure-compute/create-iothub-resource-1.png)

Jeśli rola obliczeniowa usługi Edge nie została skonfigurowana, pamiętaj, że: 
- zasób usługi IoT Hub nie ma żadnych urządzeń IoT ani urządzeń IoT Edge,
- nie możesz tworzyć udziałów lokalnych usługi Edge, podczas dodawania udziału opcja utworzenia udziału lokalnego na potrzeby funkcji obliczeniowej Edge nie jest włączona.


## <a name="set-up-compute-role"></a>Konfigurowanie roli obliczeniowej

Po skonfigurowaniu roli obliczeniowej usługi Edge na urządzeniu Edge tworzone są dwa urządzenia — jedno z nich to urządzenie IoT, a drugie to urządzenie IoT Edge. Oba urządzenia można wyświetlić w zasobie usługi IoT Hub.

Aby skonfigurować rolę obliczeniową na urządzeniu, wykonaj następujące czynności.

1. Przejdź do zasobu usługi Data Box Edge, a następnie przejdź do pozycji **Overview** (Przegląd) i kliknij pozycję **Set up compute role** (Skonfiguruj rolę obliczeniową). 

    ![Konfigurowanie roli obliczeniowej](./media/data-box-edge-deploy-configure-compute/setup-compute-1.png)
   
    Możesz także przejść do pozycji **Modules** (Moduły) i kliknąć pozycję **Configure compute** (Konfigurowanie obliczeń).

    ![Konfigurowanie roli obliczeniowej](./media/data-box-edge-deploy-configure-compute/setup-compute-2.png)
 
2. Z listy rozwijanej wybierz **zasób usługi IoT Hub** utworzony w poprzednim kroku. W chwili obecnej dla urządzenia IoT Edge jest dostępna tylko platforma Linux. Kliknij pozycję **Utwórz**.

    ![Konfigurowanie roli obliczeniowej](./media/data-box-edge-deploy-configure-compute/setup-compute-3.png)
 
3. Utworzenie roli obliczeniowej trwa kilka minut. Z powodu błędu w tej wersji, nawet gdy rola obliczeniowa jest już utworzona, ekran nie jest odświeżany. Przejdź do sekcji **Modules** (Moduły), aby zobaczyć, że funkcja obliczeniowa Edge została skonfigurowana.  

    ![Konfigurowanie roli obliczeniowej](./media/data-box-edge-deploy-configure-compute/setup-compute-4.png)

4. Ponownie przejdź do sekcji **Overview** (Przegląd) — teraz ekran jest zaktualizowany, aby wskazać, że skonfigurowano rolę obliczeniową.

    ![Konfigurowanie roli obliczeniowej](./media/data-box-edge-deploy-configure-compute/setup-compute-5.png)
 
5. Przejdź do usługi IoT Hub używanej podczas tworzenia roli obliczeniowej usługi Edge. Przejdź do pozycji **IoT devices** (Urządzenia IoT). Możesz zobaczyć, że urządzenie IoT jest teraz włączone. 

    ![Konfigurowanie roli obliczeniowej](./media/data-box-edge-deploy-configure-compute/setup-compute-6.png)

6. Przejdź do pozycji **IoT Edge**, a zobaczysz, że urządzenie usługi IoT Edge także zostało włączone.

    ![Konfigurowanie roli obliczeniowej](./media/data-box-edge-deploy-configure-compute/setup-compute-7.png)
 
7. Wybierz i kliknij urządzenie usługi IoT Edge. Na tym urządzeniu usługi IoT Edge jest uruchomiony agent usługi Edge. 

    ![Konfigurowanie roli obliczeniowej](./media/data-box-edge-deploy-configure-compute/setup-compute-8.png) 

Jednak na tym urządzeniu Edge nie ma żadnych modułów niestandardowych. Możesz teraz dodać moduł niestandardowy do tego urządzenia.


## <a name="add-a-custom-module"></a>Dodawanie modułu niestandardowego

W tej sekcji dodasz moduł niestandardowy na urządzeniu usługi IoT Edge. 

W tej procedurze jest używany przykład, w którym moduł niestandardowy pobiera pliki z udziału lokalnego na urządzeniu Edge i przenosi je do udziału chmurowego na tym urządzeniu. Następnie udział chmurowy wypycha pliki na konto magazynu platformy Azure skojarzone z tym udziałem chmurowym. 

1. Pierwszym krokiem jest dodanie udziału lokalnego na urządzeniu Edge. W swoim zasobie usługi Data Box Edge przejdź do sekcji **Shares** (Udziały). Kliknij pozycję **+ Add share** (+ Dodaj udział). Podaj nazwę udziału, a następnie wybierz typ udziału. Aby utworzyć udział lokalny, zaznacz opcję **Configure as Edge local share** (Skonfiguruj jako lokalny udział usługi Edge). Wybierz **istniejącego użytkownika** lub **utwórz nowego**. Kliknij pozycję **Utwórz**.

    ![Dodawanie modułu niestandardowego](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-1.png) 

    Jeśli utworzono lokalny udział NFS, użyj następującej opcji polecenia rsync, aby skopiować pliki do udziału:

    `rsync --inplace <source file path> < destination file path>`

     Aby uzyskać więcej informacji na temat polecenia rsync, przejdź do [dokumentacji narzędzia Rsync](https://www.computerhope.com/unix/rsync.htm). 

 
2. Po utworzeniu udziału lokalnego i wyświetleniu powiadomienia o pomyślnym utworzeniu (lista udziałów może zostać zaktualizowana wcześniej, ale i tak trzeba poczekać na ukończenie tworzenia udziału) przejdź do listy udziałów. 

    ![Dodawanie modułu niestandardowego](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-2.png) 
 
3. Wybierz i kliknij nowo utworzony udział lokalny i wyświetl jego właściwości. Skopiuj i zapisz właściwość **local mount point for Edge modules** (lokalny punkt instalacji dla modułów usługi Edge) odpowiednią dla tego udziału.

    ![Dodawanie modułu niestandardowego](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-3.png) 
 
    Przejdź do istniejącego udziału chmurowego utworzonego na Twoim urządzeniu Data Box Edge. Ponownie skopiuj i zapisz lokalny punkt instalacji dla modułów obliczeniowych usługi Edge dla tego udziału chmurowego. Te lokalne punkty instalacji będą używane podczas wdrażania modułu.

    ![Dodawanie modułu niestandardowego](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-4.png)  

4. Aby dodać moduł niestandardowy do urządzenia usługi IoT Edge, przejdź do zasobu usługi IoT Hub, a następnie przejdź do **urządzenia usługi IoT Edge**. Wybierz i kliknij to urządzenie. W sekcji **Device details** (Szczegóły urządzenia) na pasku poleceń u góry kliknij pozycję **Set modules** (Ustaw moduły). 

    ![Dodawanie modułu niestandardowego](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-5.png) 

5. W obszarze **Add Modules** (Dodawanie modułów) wykonaj następujące czynności:

    1. Podaj **nazwę**, **adres**, **nazwę użytkownika** i **hasło** w obszarze **Container registry settings** (Ustawienia rejestru kontenerów) dla modułu niestandardowego. Nazwa, adres i wymienione poświadczenia są używane do pobierania modułów za pomocą zgodnego adresu URL. Aby wdrożyć ten moduł, w obszarze **Deployment modules** (Moduły wdrażania) wybierz opcję **IoT Edge module** (Moduł usługi IoT Edge). Ten moduł usługi IoT Edge to kontener platformy Docker, który można wdrożyć na urządzeniu usługi IoT Edge skojarzonym z Twoim urządzeniem Data Box Edge.

        ![Dodawanie modułu niestandardowego](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-6.png) 
 
    2. Określ ustawienia dla niestandardowego modułu usługi IoT Edge. Podaj **nazwę** modułu i **identyfikator URI obrazu**. 
    
        ![Dodawanie modułu niestandardowego](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-7.png) 

    3. W obszarze **Container create options** (Opcje tworzenia kontenera) podaj lokalne punkty instalacji dla modułów usługi Edge skopiowane w poprzednich krokach dla udziału lokalnego i chmurowego (koniecznie użyj tych ścieżek, zamiast tworzyć nowe). Te udziały są mapowane na odpowiednie punkty instalacji kontenera. Podaj tu także wszelkie zmienne środowiskowe dla Twojego modułu.

        ![Dodawanie modułu niestandardowego](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-8.png) 
 
    4. **Skonfiguruj zaawansowane ustawienia środowiska uruchomieniowego Edge**, jeśli są potrzebne, a następnie kliknij przycisk **Dalej**.

        ![Dodawanie modułu niestandardowego](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-9.png) 
 
6.  W obszarze **Specify routes** (Określanie tras) ustaw trasy między modułami. W tym przypadku podaj nazwę udziału lokalnego, który wypchnie dane do udziału chmurowego. Kliknij przycisk **Dalej**.

    ![Dodawanie modułu niestandardowego](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-10.png) 
 
7.  W obszarze **Review deployment** (Przejrzyj wdrożenie) sprawdź wszystkie ustawienia i, jeśli wszystko będzie w porządku, **prześlij** moduł dla wdrożenia.

    ![Dodawanie modułu niestandardowego](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-11.png) 
 
Spowoduje to rozpoczęcie wdrażania modułu, jak pokazuje pozycja **IoT Edge Custom module** (Moduł niestandardowy usługi IoT Edge) w obszarze **Modules** (Moduły).

![Dodawanie modułu niestandardowego](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-12.png) 

### <a name="verify-data-transform-and-transfer"></a>Weryfikowanie przekształcania danych i transferu

Ostatnim krokiem jest sprawdzenie, czy moduł jest połączony i działa zgodnie z oczekiwaniami. Wykonaj poniższe kroki, aby sprawdzić, czy moduł jest uruchomiony.

1. Stan środowiska uruchomieniowego modułu powinien pokazywać uruchomienie dla urządzenia usługi IoT Edge w zasobie usługi IoT Hub.

    ![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute/verify-data-transform-1.png) 
 
2. Wybierz i kliknij moduł i spójrz na pozycję **Module Identity Twin** (Bliźniacza reprezentacja tożsamości modułu). Stan klienta dla urządzenia Edge i modułu powinien być wyświetlany jako **Connected** (Połączony).

    ![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute/verify-data-transform-2.png) 
 
3.  Po uruchomieniu moduł powinien być także wyświetlany na liście modułów usługi Edge w zasobie usługi Data Box Edge. **Stan środowiska uruchomieniowego** dodanego modułu ma wartość **uruchomione**.

    ![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute/verify-data-transform-3.png) 
 
4.  Połącz się z utworzonym udziałem lokalnym i z udziałem chmurowym za pomocą Eksploratora plików.

    ![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute/verify-data-transform-4.png) 
 
5.  Dodaj dane do udziału lokalnego.

    ![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute/verify-data-transform-5.png) 
 
6.  Dane zostaną przeniesione do udziału chmurowego.

    ![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute/verify-data-transform-6.png)  

7.  Następnie dane zostaną wypchnięte z udziału chmurowego na konto magazynu. Przejdź do Eksploratora usługi Storage, aby wyświetlić dane.

    ![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute/verify-data-transform-7.png) 
 
To kończy proces weryfikacji.


## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące usługi Data Box Edge, takie jak:

> [!div class="checklist"]
> * Tworzenie zasobu usługi IoT Hub
> * Konfigurowanie roli obliczeniowej
> * Dodawanie modułu obliczeniowego
> * Weryfikowanie przekształcania danych i transferu

Przejdź do następnego samouczka, aby dowiedzieć się, jak administrować usługą Data Box Edge.

> [!div class="nextstepaction"]
> [Administrowanie usługą Data Box Edge za pomocą lokalnego internetowego interfejsu użytkownika](http://aka.ms/dbg-docs)


