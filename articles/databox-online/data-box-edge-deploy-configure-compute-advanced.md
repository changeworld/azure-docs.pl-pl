---
title: Samouczek do filtrowania, analizowania danych w celu zaawansowanego wdrożenia za pomocą obliczeń na platformie Azure Data Box Edge | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować rolę obliczeniową w usłudze Data Box Edge i wykorzystać ją do przekształcenia danych dla przepływu zaawansowanego wdrażania przed wysłaniem na platformę Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge for advanced deployment flow so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: b446a3ebf92f6240d3bc02a148fbb8296efec926
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239042"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge-for-advanced-deployment-flow"></a>Samouczek: Przekształcanie danych za pomocą usługi Azure Data Box Edge w celu uzyskania zaawansowanego przepływu wdrażania

W tym samouczku opisano sposób konfigurowania roli obliczeniowej dla zaawansowanego przepływu wdrażania na urządzeniu usługi Azure Data Box Edge. Po skonfigurowaniu roli obliczeniowej urządzenie Data Box Edge może przekształcać dane przed wysłaniem ich na platformę Azure.

Obliczenia można skonfigurować pod kątem prostego lub zaawansowanego przepływu wdrażania na urządzeniu.

|                  | Proste wdrażanie                                | Zaawansowane wdrażanie                   |
|------------------|--------------------------------------------------|---------------------------------------|
| Przeznaczone do     | Administratorzy IT                                | Deweloperzy                            |
| Typ             | Wdrażanie modułów za pomocą usługi Data Box Edge      | Wdrażanie modułów za pomocą usługi IoT Hub |
| Wdrożone moduły | Single                                           | Przykuty lub wiele modułów           |


Ta procedura może potrwać około 20 do 30 minut.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie obliczeń
> * Dodawanie udziałów
> * Dodawanie wyzwalacza
> * Dodawanie modułu obliczeniowego
> * Weryfikowanie przekształcania danych i transferu

 
## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem roli obliczeniowej na urządzeniu Data Box Edge upewnij się, że zostały spełnione następujące warunki:

- Urządzenie Data Box Edge zostało aktywowane zgodnie z opisem w temacie [Podłączanie, konfigurowanie i aktywowanie urządzenia Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Konfigurowanie obliczeń

Aby skonfigurować obliczenia na krawędzi pola danych, utworzysz zasób Usługi IoT Hub.

1. W witrynie Azure portal zasobu usługi Data Box Edge przejdź do **przeglądu**. W prawym okienku na kafelku **Obliczanie** wybierz pozycję **Wprowadzenie**.

    ![Wprowadzenie do obliczeń](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-1.png)

2. Na kafelku **Konfiguruj obliczenia krawędzi** wybierz pozycję **Konfiguruj obliczenia**.

    ![Wprowadzenie do obliczeń](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-2.png)

3. W bloku **obliczeń Konfiguruj krawędź** wprowadź następujące dane:

   
    |Pole  |Wartość  |
    |---------|---------|
    |Usługa IoT Hub     | Wybierz z **nowego** lub **istniejącego**. <br> Domyślnie warstwa standardowa (S1) jest używana do tworzenia zasobu IoT. Aby użyć zasobu IoT warstwy bezpłatnej, utwórz jeden, a następnie wybierz istniejący zasób. <br> W każdym przypadku zasób Usługi IoT Hub używa tej samej subskrypcji i grupy zasobów, która jest używana przez zasób Data Box Edge.     |
    |Nazwa     |Wprowadź nazwę zasobu usługi IoT Hub.         |

    ![Wprowadzenie do obliczeń](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-3.png)

4. Wybierz **pozycję Utwórz**. Tworzenie zasobów usługi IoT Hub trwa kilka minut. Po utworzeniu zasobu Usługi IoT Hub aktualizacje kafelka **obliczeń konfiguruj krawędź,** aby wyświetlić konfigurację obliczeniową. Aby potwierdzić, że rola obliczeniowa krawędzi została skonfigurowana, wybierz **opcję Wyświetl konfigurację** na kafelku **Konfiguruj obliczenia.**
    
    ![Wprowadzenie do obliczeń](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-4.png)

    Po skonfigurowaniu roli funkcji obliczeniowej Edge na urządzeniu Edge tworzone są dwa urządzenia: urządzenie IoT i urządzenie IoT Edge. Oba urządzenia można wyświetlić w zasobie usługi IoT Hub. Środowisko uruchomieniowe IoT Edge jest również uruchomione na tym urządzeniu IoT Edge.

    W chwili obecnej dla urządzenia IoT Edge jest dostępna tylko platforma Linux.


## <a name="add-shares"></a>Dodawanie udziałów

W przypadku zaawansowanego wdrożenia w tym samouczku potrzebne będą dwa udziały: jeden udział edge i inny udział lokalny usługi Edge.

1. Dodaj udział edge'a na urządzeniu, wykonując następujące czynności:

    1. W zasobie Data Box Edge przejdź do aplikacji **Edge compute > Wprowadzenie**.
    2. Na kafelku **Dodawanie udziałów** wybierz pozycję **Dodaj**.
    3. Na **Dodaj udział** bloku bloku, podaj nazwę udziału i wybierz typ udziału.
    4. Aby zainstalować udział krawędzi, zaznacz pole wyboru **Użyj udziału z obliczeń krawędzi**.
    5. Wybierz **konto Magazyn,** **Usługę magazynowania**, istniejącego użytkownika, a następnie wybierz pozycję **Utwórz**.

        ![Dodawanie udziału edge'a](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-1.png)

    <!--If you created a local NFS share, use the following remote sync (rsync) command option to copy files onto the share:

    `rsync <source file path> < destination file path>`

    For more information about the rsync command, go to [Rsync documentation](https://www.computerhope.com/unix/rsync.htm).-->

    Po utworzeniu udziału Edge otrzymasz powiadomienie o pomyślnym utworzeniu. Lista akcji zostanie zaktualizowana w celu odzwierciedlenia nowego udziału.

2. Dodaj udział lokalny edge na urządzeniu Edge, powtarzając wszystkie kroki w poprzednim kroku i zaznaczając pole wyboru **Konfiguruj jako udział lokalny edge**. Dane w udziale lokalnym pozostają na urządzeniu.

    ![Dodawanie udziału lokalnego brzegowego](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-2.png)

3. Na **akcji** bloku bloku, zobaczysz zaktualizowaną listę udziałów.

    ![Zaktualizowana lista udziałów](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-3.png)

4. Aby wyświetlić właściwości nowo utworzonego udziału lokalnego, wybierz udział z listy. W polu **Lokalny punkt instalacji dla modułów funkcji obliczeniowej Edge** skopiuj wartość odpowiadającą temu udziałowi.

    Użyjesz tego lokalnego punktu instalacji podczas wdrażania modułu.

    ![Pole „Lokalny punkt instalacji dla modułów funkcji obliczeniowej Edge”](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-4.png)
 
5. Aby wyświetlić właściwości utworzonego udziału Edge, wybierz udział z listy. W polu **Lokalny punkt instalacji dla modułów funkcji obliczeniowej Edge** skopiuj wartość odpowiadającą temu udziałowi.

    Użyjesz tego lokalnego punktu instalacji podczas wdrażania modułu.

    ![Dodawanie modułu niestandardowego](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-5.png)


## <a name="add-a-trigger"></a>Dodawanie wyzwalacza

1. Przejdź do **urządzenia Edge compute > Wyzwalacze**. Wybierz **+ Dodaj wyzwalacz**.

    ![Dodawanie wyzwalacza](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-1.png)

2. W **bloku Dodaj wyzwalacz** wprowadź następujące wartości.

    |Pole  |Wartość  |
    |---------|---------|
    |Nazwa wyzwalacza     | Unikatowa nazwa wyzwalacza.         |
    |Typ wyzwalacza     | Wybierz **wyzwalacz pliku.** Wyzwalacz pliku jest uruchamiany za każdym razem, gdy wystąpi zdarzenie pliku, takie jak plik jest zapisywany w udziale wejściowym. Z drugiej strony zaplanowany wyzwalacz uruchamia się na podstawie harmonogramu zdefiniowanego przez Ciebie. W tym przykładzie potrzebujemy wyzwalacza pliku.    |
    |Udział wejściowy     | Wybierz udział wejściowy. Udział lokalny krawędzi jest udział wejściowy w tym przypadku. Moduł używany w tym miejscu przenosi pliki z udziału lokalnego krawędzi do udziału edge, gdzie są one przekazywane do chmury.        |

    ![Dodawanie wyzwalacza](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-2.png)

3. Użytkownik zostanie powiadomiony po utworzeniu wyzwalacza. Lista wyzwalaczy zostanie zaktualizowana w celu wyświetlenia nowo utworzonego wyzwalacza. Wybierz właśnie utworzony wyzwalacz.

    ![Dodawanie wyzwalacza](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-3.png)

4. Skopiuj i zapisz przykładową trasę. Zmodyfikujesz tę przykładową trasę i użyjesz jej później w centrum IoT Hub.

    `"sampleroute": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/modulename/inputs/input1\")"`

    ![Dodawanie wyzwalacza](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-4.png)

## <a name="add-a-module"></a>Dodawanie modułu

Na tym urządzeniu Edge nie ma żadnych modułów niestandardowych. Można dodać niestandardowy lub wstępnie utworzony moduł. Aby dowiedzieć się, jak utworzyć moduł niestandardowy, przejdź do artykułu [Tworzenie modułu C# dla urządzenia Data Box Edge](data-box-edge-create-iot-edge-module.md).

W tej sekcji dodasz do urządzenia usługi IoT Edge moduł niestandardowy utworzony w temacie [Tworzenie modułu C# dla urządzenia Data Box Edge](data-box-edge-create-iot-edge-module.md). Ten moduł niestandardowy pobiera pliki z udziału lokalnego usługi Edge na urządzeniu Edge i przenosi je do udziału edge (chmury) na urządzeniu. Następnie udział chmurowy wypycha pliki na konto magazynu platformy Azure skojarzone z tym udziałem chmurowym.

1. Przejdź do **urządzenia Edge > Wprowadzenie .** Na kafelku **Dodaj moduły** wybierz typ scenariusza jako **zaawansowany**. Wybierz **pozycję Przejdź do Centrum IoT**.

    ![Wybieranie zaawansowanego wdrożenia](./media/data-box-edge-deploy-configure-compute-advanced/add-module-1.png)

<!--2. In the **Configure and add module** blade, input the following values:  

    |Input share     | Select an input share. The Edge local share is the input share in this case. The module used here moves files from the Edge local share to an Edge share where they are uploaded into the cloud.        |
    |Output share     | Select an output share. The Edge share is the output share in this case.        |
-->

2. W zasobie Usługi IoT Hub przejdź do **urządzenia Usługi IoT Edge,** a następnie wybierz urządzenie Usługi IoT Edge.

    ![Przejdź do urządzenia Usługi IoT Edge w Uiszczajniku IoT Hub](./media/data-box-edge-deploy-configure-compute-advanced/add-module-2.png)

3. W **obszarze Szczegóły urządzenia**wybierz pozycję Ustaw **moduły**.

    ![Link Ustawianie modułów](./media/data-box-edge-deploy-configure-compute-advanced/add-module-3.png)

4. W obszarze **Dodawanie modułów** wykonaj następujące czynności:

    1. Wprowadź nazwę, adres, nazwę użytkownika i hasło dla ustawień rejestru kontenerów dla modułu niestandardowego.
    Nazwa, adres i wymienione poświadczenia są używane do pobierania modułów za pomocą zgodnego adresu URL. Aby wdrożyć ten moduł, w obszarze **Deployment modules** (Moduły wdrażania) wybierz opcję **IoT Edge module** (Moduł usługi IoT Edge). Ten moduł usługi IoT Edge to kontener platformy Docker, który można wdrożyć na urządzeniu usługi IoT Edge skojarzonym z Twoim urządzeniem Data Box Edge.

        ![Strona Ustawianie modułów](./media/data-box-edge-deploy-configure-compute-advanced/add-module-4.png) 
 
    2. Określ ustawienia dla niestandardowego modułu usługi IoT Edge. Wprowadź następujące wartości.
     
        |Pole  |Wartość  |
        |---------|---------|
        |Nazwa     | Unikatowa nazwa modułu. Ten moduł jest kontenerem platformy docker, który można wdrożyć na urządzeniu usługi IoT Edge skojarzonym z krawędzią pola danych.        |
        |Identyfikator URI obrazu     | Identyfikator URI obrazu dla odpowiedniego obrazu kontenera dla modułu.        |
        |Wymagane poświadczenia     | Jeśli ta jest zaznaczona, nazwa użytkownika i hasło są używane do pobierania modułów z pasującym adresem URL.        |
    
        W polu **Opcje tworzenia kontenera** wprowadź lokalne punkty instalacji dla modułów krawędzi skopiowanych w poprzednich krokach udziału lokalnego edge i edge.

        > [!IMPORTANT]
        > Ścieżki używane w tym miejscu są montowane w kontenerze, więc muszą odpowiadać oczekiwanej funkcjonalności w kontenerze. Jeśli obserwujesz [Tworzenie modułu niestandardowego,](data-box-edge-create-iot-edge-module.md#update-the-module-with-custom-code)kod określony w tym module oczekuje skopiowanych ścieżek. Nie należy modyfikować tych ścieżek.
    
        W polu **Opcje tworzenia kontenera** możesz wkleić poniższy przykład:
    
        ```
        {
          "HostConfig": 
          {
           "Binds": 
            [
             "/home/hcsshares/mydbesmbedgelocalshare1:/home/input",
             "/home/hcsshares/mydbesmbedgeshare1:/home/output"
            ]
           }
        }
        ```

        Podaj wszelkie zmienne środowiskowe używane dla twojego modułu. Zmienne środowiskowe zawierają opcjonalne informacje, które pomagają zdefiniować środowisko, w którym działa moduł.

        ![Pole Opcje tworzenia kontenera](./media/data-box-edge-deploy-configure-compute-advanced/add-module-5.png) 
 
    4. Jeśli to konieczne, skonfiguruj zaawansowane ustawienia środowiska uruchomieniowego Edge, a następnie kliknij pozycję **Dalej**.

        ![Dodawanie modułu niestandardowego](./media/data-box-edge-deploy-configure-compute-advanced/add-module-6.png)
 
5.  W obszarze **Określanie tras**ustaw trasy między modułami.  
    
    ![Określ trasy](./media/data-box-edge-deploy-configure-compute-advanced/add-module-7.png)

    *Trasę* można zastąpić następującym ciągiem trasy skopiowanym wcześniej. W tym przykładzie podaj nazwę udziału lokalnego, który wypchnie dane do udziału chmurowego. Wymień `modulename` nazwę modułu. Wybierz **pozycję Dalej**.
        
    ```
    "route": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/filemove/inputs/input1\")"
    ```

    ![Sekcja Określanie tras](./media/data-box-edge-deploy-configure-compute-advanced/add-module-8.png)

6.  W obszarze **Przejrzyj wdrożenie** sprawdź wszystkie ustawienia, a następnie wybierz pozycję **Prześlij**, aby przesłać moduł do wdrożenia.

    ![Strona Ustawianie modułów](./media/data-box-edge-deploy-configure-compute-advanced/add-module-9.png)
 
    Ta akcja rozpoczyna wdrażanie modułu. Po zakończeniu wdrażania jest **uruchomiony** **stan środowiska wykonawczego** modułu .

    ![Dodawanie modułu niestandardowego](./media/data-box-edge-deploy-configure-compute-advanced/add-module-10.png)

## <a name="verify-data-transform-transfer"></a>Weryfikowanie przekształcania danych, przesyłanie danych

Ostatnim krokiem jest upewnienie się, że moduł jest połączony i działa zgodnie z oczekiwaniami. Stan środowiska uruchomieniowego modułu powinien pokazywać uruchomienie dla urządzenia usługi IoT Edge w zasobie usługi IoT Hub.

Aby zweryfikować przekształcanie i przenoszenie danych na platformę Azure, należy wykonać następujące kroki.
 
1.  W Eksploratorze plików połącz się zarówno z wcześniej utworzonymi udziałami lokalnymi krawędzi, jak i z udziałami Edge.

    ![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-2.png)
 
1.  Dodaj dane do udziału lokalnego.

    ![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-3.png)
 
    Dane zostaną przeniesione do udziału chmurowego.

    ![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-4.png)  

    Następnie dane zostaną wypchnięte z udziału chmurowego na konto magazynu. Aby wyświetlić dane, przejdź do konta magazynu, a następnie wybierz **pozycję Eksplorator magazynu**. Przesłane dane można wyświetlić na koncie magazynu.

    ![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-5.png)
 
Ukończono proces walidacji.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie obliczeń
> * Dodawanie udziałów
> * Dodawanie wyzwalacza
> * Dodawanie modułu obliczeniowego
> * Weryfikowanie przekształcania danych i transferu

Aby dowiedzieć się, jak administrować urządzeniem Data Box Edge, zobacz:

> [!div class="nextstepaction"]
> [Administrowanie usługą Data Box Edge za pomocą lokalnego internetowego interfejsu użytkownika](data-box-edge-manage-access-power-connectivity-mode.md)
