---
title: Przekształcanie danych za pomocą usługi Azure Data Box Edge | Microsoft Docs
description: Dowiedz się, jak skonfigurować rolę obliczeniową na urządzeniu Data Box Edge i przy jej użyciu przekształcić dane przed wysłaniem ich na platformę Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 31911c124aeafecb8ee37d14e58d3a0bdc0d4955
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62112613"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge"></a>Samouczek: Przekształcanie danych za pomocą usługi Azure Data Box Edge

W tym samouczku opisano sposób konfigurowania roli obliczeniowej na urządzeniu Azure Data Box Edge. Po skonfigurowaniu roli obliczeniowej urządzenie Data Box Edge może przekształcać dane przed wysłaniem ich na platformę Azure.

Ta procedura może zająć około 10 – 15 minut.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie obliczeń
> * Dodaj udziały
> * Dodawanie modułu obliczeniowego
> * Weryfikowanie przekształcania danych i transferu

 
## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem roli obliczeniowej na urządzeniu Data Box Edge upewnij się, że zostały spełnione następujące warunki:

- Urządzenie Data Box Edge zostało aktywowane zgodnie z opisem w temacie [Podłączanie, konfigurowanie i aktywowanie urządzenia Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Konfigurowanie obliczeń

Aby skonfigurować obliczeń na krawędzi sieci pole danych, należy utworzyć zasób usługi IoT Hub.

1. W witrynie Azure portal zasobu krawędź pola danych przejdź do przeglądu. W okienku po prawej stronie na **obliczenia** kafelka, wybierz opcję **wprowadzenie**.

    ![Rozpoczynanie pracy z mocą obliczeniową](./media/data-box-edge-deploy-configure-compute/configure-compute-1.png)

2. Na **obliczeń skonfigurować krawędzi** kafelka, wybierz opcję **Konfigurowanie obliczeń**.
3. Na **obliczeń skonfigurować krawędzi** bloku wprowadzić następujące:

   
    |Pole  |Wartość  |
    |---------|---------|
    |Usługa IoT Hub     | Wybierz z **nowe** lub **istniejących**. <br> Domyślnie w warstwie standardowa (S1) służy do tworzenia zasobu usługi IoT. Aby korzystać z zasobów IoT w warstwie bezpłatna, utwórz ją, a następnie wybierz istniejący zasób. <br> W każdym przypadku zasobu usługi IoT Hub korzysta z tej samej subskrypcji i grupie zasobów, który jest używany przez zasób krawędź pola danych.     |
    |Name (Nazwa)     |Wprowadź nazwę zasobu usługi IoT Hub.         |

    ![Rozpoczynanie pracy z mocą obliczeniową](./media/data-box-edge-deploy-configure-compute/configure-compute-2.png)

4. Wybierz pozycję **Utwórz**. Tworzenie zasobów usługi IoT Hub zajmuje kilka minut. Po utworzeniu zasobu usługi IoT Hub **Konfigurowanie obliczeń** Kafelek aktualizacji, aby wyświetlić konfigurację obliczeniową. Aby potwierdzić, czy skonfigurowano roli obliczeń brzegowych, zaznacz **obliczenia widoku** na **Konfigurowanie obliczeń** kafelka.
    
    ![Rozpoczynanie pracy z mocą obliczeniową](./media/data-box-edge-deploy-configure-compute/configure-compute-3.png)

    Po skonfigurowaniu roli funkcji obliczeniowej Edge na urządzeniu Edge tworzone są dwa urządzenia: urządzenie IoT i urządzenie IoT Edge. Oba urządzenia można wyświetlić w zasobie usługi IoT Hub. Środowisko uruchomieniowe IoT Edge jest także uruchomiona na tym urządzeniu usługi IoT Edge. W chwili obecnej dla urządzenia IoT Edge jest dostępna tylko platforma Linux.


## <a name="add-shares"></a>Dodaj udziały

W przypadku prostego wdrożenia w ramach tego samouczka będą potrzebne dwa udziały: jeden udział krawędzi i innego udziału lokalnego krawędzi.

1. Dodaj udział Edge na urządzeniu, wykonując następujące czynności:

    1. W swoim zasobie krawędź pola danych, przejdź do **obliczeń brzegowych > Rozpocznij**.
    2. Na **Dodawanie udziałów** kafelka, wybierz opcję **Dodaj**.
    3. Na **Dodaj udział** bloku, podaj nazwę udziału, a następnie wybierz typ udziału.
    4. Aby zainstalować udział krawędzi, zaznacz pole wyboru dla **udziału za pomocą obliczeń brzegowych**.
    5. Wybierz **konta magazynu**, **usługi Storage**, istniejącego użytkownika, a następnie wybierz **Utwórz**.

        ![Dodaj udział do krawędzi](./media/data-box-edge-deploy-configure-compute/add-edge-share-1.png) 

    Jeśli utworzono lokalny udział NFS, użyj następującej opcji synchronizacji zdalnej (rsync), aby skopiować pliki do udziału:

    `rsync <source file path> < destination file path>`

    Aby uzyskać więcej informacji na temat polecenia rsync, przejdź do [dokumentacji narzędzia Rsync](https://www.computerhope.com/unix/rsync.htm).

    Utworzeniu udziału krawędzi, a otrzymasz powiadomienie o pomyślnym utworzeniu. Lista udziałów może zostać zaktualizowana, ale musisz poczekać na ukończenie tworzenia udziału.

2. Dodawanie udziału lokalnego usługi Edge na urządzeniu usługi Edge powtarzające się wszystkie kroki opisane w poprzednim kroku, a następnie zaznaczając pole wyboru **Konfigurowanie udziału lokalnego krawędzi**. Dane w udziale lokalnym pozostaje na urządzeniu.

    ![Dodawanie udziału lokalnego usługi Edge](./media/data-box-edge-deploy-configure-compute/add-edge-share-2.png)

  
3. Wybierz **Dodawanie udziałów** Aby wyświetlić zaktualizowaną listę udziałów.

    ![Zaktualizowana lista udziałów](./media/data-box-edge-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Dodaj moduł

Można dodać niestandardowy lub wstępnie skompilowanych modułu. Nie istnieją żadne moduły niestandardowe na tym urządzeniu usługi Edge. Aby dowiedzieć się, jak utworzyć moduł niestandardowy, przejdź do artykułu [Tworzenie modułu C# dla urządzenia Data Box Edge](data-box-edge-create-iot-edge-module.md).

W tej sekcji dodasz do urządzenia usługi IoT Edge moduł niestandardowy utworzony w temacie [Tworzenie modułu C# dla urządzenia Data Box Edge](data-box-edge-create-iot-edge-module.md). Ten moduł niestandardowego pobiera pliki z udziałem lokalnym Edge na urządzeniu usługi Edge i przenosi je do udziału krawędzi (w chmurze) na urządzeniu. Następnie udział chmurowy wypycha pliki na konto magazynu platformy Azure skojarzone z tym udziałem chmurowym.

1. Przejdź do **obliczeń brzegowych > Rozpocznij**. Na **Dodaj moduły** kafelka, wybierz typ scenariusza jako **proste**. Wybierz pozycję **Dodaj**.
2. W **Konfiguruj i Dodaj moduł** bloku, wprowadź następujące wartości:

    
    |Pole  |Wartość  |
    |---------|---------|
    |Name (Nazwa)     | Unikatowa nazwa modułu. Ten moduł jest narzędziem, którą można wdrożyć na urządzeniu usługi IoT Edge, skojarzony z krawędzi pola sieci danych.        |
    |Identyfikator URI obrazu     | Identyfikator URI obrazu dla odpowiadających im obrazów kontenera dla modułu.        |
    |Poświadczenia są wymagane     | Jeśli zaznaczone, nazwę użytkownika i hasło są używane do pobierania modułów za pomocą dopasowywania adresu URL.        |
    |Udział w danych wejściowych     | Wybierz udział danych wejściowych. Krawędź lokalny udział w tym przypadku jest udział w danych wejściowych. Moduł, który został użyty tutaj przenosi pliki z udziału lokalnego krawędź z udziałem krawędzi gdzie są przekazywane do chmury.        |
    |Udostępnij dane wyjściowe     | Wybierz udział danych wyjściowych. Udostępnij krawędzi w tym przypadku jest udział w danych wyjściowych.        |
    |Typ wyzwalacza     | Wybierz jedną z **pliku** lub **harmonogram**. Plik wyzwalacza przy każdym wystąpieniu zdarzenia pliku, np. plik jest zapisywany do udziału danych wejściowych. Zaplanowane wyzwalanie uruchamia się zgodnie z harmonogramem, zdefiniowane przez użytkownika.         |
    |Nazwa wyzwalacza     | Unikatowa nazwa wyzwalacza.         |
    |Zmienne środowiskowe| Informacje opcjonalne, która pomoże określić środowisko, w którym będzie uruchamiany modułu.   |

    ![Dodawanie i Konfigurowanie modułu](./media/data-box-edge-deploy-configure-compute/add-module-1.png)

3. Wybierz pozycję **Dodaj**. Moduł pobiera dodane. **Dodaj moduł** Kafelek aktualizacji, aby wskazać, że moduł został wdrożony. 

    ![Wdrożony moduł](./media/data-box-edge-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Weryfikowanie przekształcania danych i transferu

Ostatnim krokiem jest upewnienie się, że moduł jest połączony i działa zgodnie z oczekiwaniami. Stan środowiska uruchomieniowego modułu powinien pokazywać uruchomienie dla urządzenia usługi IoT Edge w zasobie usługi IoT Hub.

Wykonaj poniższe kroki, aby sprawdzić, czy moduł jest uruchomiony:

1. Wybierz **Dodaj moduł** kafelka. Spowoduje to przejście do **modułów** bloku. Na liście modułów identyfikują moduł, który został wdrożony. Stan czasu wykonywania modułu dodano powinny być *systemem*.

    ![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute/verify-data-1.png)
 
1.  W Eksploratorze plików połączyć do krawędzi lokalnych i udziałów Edge, która została utworzona wcześniej.

    ![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute/verify-data-2.png) 
 
1.  Dodaj dane do udziału lokalnego.

    ![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute/verify-data-3.png) 
 
    Dane zostaną przeniesione do udziału chmurowego.

    ![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute/verify-data-4.png)  

    Następnie dane zostaną wypchnięte z udziału chmurowego na konto magazynu. Aby wyświetlić dane, przejdź do Eksploratora usługi Storage.

    ![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute/verify-data-5.png) 
 
Ukończono proces walidacji.


## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie obliczeń
> * Dodaj udziały
> * Dodawanie modułu obliczeniowego
> * Weryfikowanie przekształcania danych i transferu

Aby dowiedzieć się, jak administrować urządzeniem Data Box Edge, zobacz:

> [!div class="nextstepaction"]
> [Administrowanie usługą Data Box Edge za pomocą lokalnego internetowego interfejsu użytkownika](data-box-edge-manage-access-power-connectivity-mode.md)
