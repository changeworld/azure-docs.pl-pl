---
title: Samouczek do filtrowania, analizowania danych za pomocą obliczeń na platformie Azure Data Box Edge | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować rolę obliczeniową na urządzeniu Data Box Edge i przy jej użyciu przekształcić dane przed wysłaniem ich na platformę Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: b641ae62ba6e0cdacaeb46b1ffee2f02c7544763
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239021"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge"></a>Samouczek: Przekształcanie danych za pomocą usługi Azure Data Box Edge

W tym samouczku opisano sposób konfigurowania roli obliczeniowej na urządzeniu Azure Data Box Edge. Po skonfigurowaniu roli obliczeniowej urządzenie Data Box Edge może przekształcać dane przed wysłaniem ich na platformę Azure.

Ta procedura może potrwać około 10 do 15 minut.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie obliczeń
> * Dodawanie udziałów
> * Dodawanie modułu obliczeniowego
> * Weryfikowanie przekształcania danych i transferu

 
## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem roli obliczeniowej na urządzeniu Data Box Edge upewnij się, że zostały spełnione następujące warunki:

- Urządzenie Data Box Edge zostało aktywowane zgodnie z opisem w temacie [Podłączanie, konfigurowanie i aktywowanie urządzenia Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Konfigurowanie obliczeń

Aby skonfigurować obliczenia na krawędzi pola danych, utworzysz zasób Usługi IoT Hub.

1. W witrynie Azure portal zasobu usługi Data Box Edge przejdź do przeglądu. W prawym okienku na kafelku **Obliczanie** wybierz pozycję **Wprowadzenie**.

    ![Wprowadzenie do obliczeń](./media/data-box-edge-deploy-configure-compute/configure-compute-1.png)

2. Na kafelku **Konfiguruj obliczenia krawędzi** wybierz pozycję **Konfiguruj obliczenia**.
3. W bloku **obliczeń Konfiguruj krawędź** wprowadź następujące dane:

   
    |Pole  |Wartość  |
    |---------|---------|
    |Usługa IoT Hub     | Wybierz z **nowego** lub **istniejącego**. <br> Domyślnie warstwa standardowa (S1) jest używana do tworzenia zasobu IoT. Aby użyć zasobu IoT warstwy bezpłatnej, utwórz jeden, a następnie wybierz istniejący zasób. <br> W każdym przypadku zasób Usługi IoT Hub używa tej samej subskrypcji i grupy zasobów, która jest używana przez zasób Data Box Edge.     |
    |Nazwa     |Wprowadź nazwę zasobu usługi IoT Hub.         |

    ![Wprowadzenie do obliczeń](./media/data-box-edge-deploy-configure-compute/configure-compute-2.png)

4. Wybierz **pozycję Utwórz**. Tworzenie zasobów usługi IoT Hub trwa kilka minut. Po utworzeniu zasobu Usługi IoT Hub aktualizacje kafelków **Konfiguruj obliczenia,** aby wyświetlić konfigurację obliczeniową. Aby potwierdzić, że rola obliczeniowa krawędzi została skonfigurowana, wybierz **polecenie Wyświetl obliczenia** na kafelku **Konfiguruj obliczenia.**
    
    ![Wprowadzenie do obliczeń](./media/data-box-edge-deploy-configure-compute/configure-compute-3.png)

    > [!NOTE]
    > Jeśli okno dialogowe **Konfiguruj obliczenia** zostanie zamknięte, zanim centrum IoT Hub jest skojarzone z urządzeniem Data Box Edge, centrum IoT zostanie utworzone, ale nie jest wyświetlane w konfiguracji obliczeniowej. 
    
    Po skonfigurowaniu roli funkcji obliczeniowej Edge na urządzeniu Edge tworzone są dwa urządzenia: urządzenie IoT i urządzenie IoT Edge. Oba urządzenia można wyświetlić w zasobie usługi IoT Hub. Środowisko uruchomieniowe IoT Edge jest również uruchomione na tym urządzeniu IoT Edge. W chwili obecnej dla urządzenia IoT Edge jest dostępna tylko platforma Linux.


## <a name="add-shares"></a>Dodawanie udziałów

Do prostego wdrożenia w tym samouczku potrzebne są dwa udziały: jeden udział edge i inny udział lokalny edge.

1. Dodaj udział edge'a na urządzeniu, wykonując następujące czynności:

    1. W zasobie Data Box Edge przejdź do aplikacji **Edge compute > Wprowadzenie**.
    2. Na kafelku **Dodawanie udziałów** wybierz pozycję **Dodaj**.
    3. Na **Dodaj udział** bloku bloku, podaj nazwę udziału i wybierz typ udziału.
    4. Aby zainstalować udział krawędzi, zaznacz pole wyboru **Użyj udziału z obliczeń krawędzi**.
    5. Wybierz **konto Magazyn,** **Usługę magazynowania**, istniejącego użytkownika, a następnie wybierz pozycję **Utwórz**.

        ![Dodawanie udziału edge'a](./media/data-box-edge-deploy-configure-compute/add-edge-share-1.png) 

    Jeśli utworzono lokalny udział NFS, użyj następującej opcji synchronizacji zdalnej (rsync), aby skopiować pliki do udziału:

    `rsync <source file path> < destination file path>`

    Aby uzyskać więcej informacji na temat polecenia rsync, przejdź do [dokumentacji narzędzia Rsync](https://www.computerhope.com/unix/rsync.htm).

    Zostanie utworzony udział Edge, a otrzymasz powiadomienie o pomyślnym utworzeniu. Lista udziałów może zostać zaktualizowana, ale musisz poczekać na ukończenie tworzenia udziału.

2. Dodaj udział lokalny edge na urządzeniu Edge, powtarzając wszystkie kroki w poprzednim kroku i zaznaczając pole wyboru **Konfiguruj jako udział lokalny edge**. Dane w udziale lokalnym pozostają na urządzeniu.

    ![Dodawanie udziału lokalnego brzegowego](./media/data-box-edge-deploy-configure-compute/add-edge-share-2.png)

  
3. Wybierz **dodaj udziały,** aby wyświetlić zaktualizowaną listę udziałów.

    ![Zaktualizowana lista udziałów](./media/data-box-edge-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Dodawanie modułu

Można dodać niestandardowy lub wstępnie utworzony moduł. Na tym urządzeniu Edge nie ma żadnych modułów niestandardowych. Aby dowiedzieć się, jak utworzyć moduł niestandardowy, przejdź do artykułu [Tworzenie modułu C# dla urządzenia Data Box Edge](data-box-edge-create-iot-edge-module.md).

W tej sekcji dodasz do urządzenia usługi IoT Edge moduł niestandardowy utworzony w temacie [Tworzenie modułu C# dla urządzenia Data Box Edge](data-box-edge-create-iot-edge-module.md). Ten moduł niestandardowy pobiera pliki z udziału lokalnego usługi Edge na urządzeniu Edge i przenosi je do udziału edge (chmury) na urządzeniu. Następnie udział chmurowy wypycha pliki na konto magazynu platformy Azure skojarzone z tym udziałem chmurowym.

1. Przejdź do **urządzenia Edge > Wprowadzenie .** Na kafelku **Dodaj moduły** wybierz typ scenariusza jako **prosty**. Wybierz pozycję **Dodaj**.
2. W bloku **Konfiguruj i dodaj moduł** wprowadź następujące wartości:

    
    |Pole  |Wartość  |
    |---------|---------|
    |Nazwa     | Unikatowa nazwa modułu. Ten moduł jest kontenerem docker, który można wdrożyć na urządzeniu usługi IoT Edge, który jest skojarzony z krawędzią pola danych.        |
    |Identyfikator URI obrazu     | Identyfikator URI obrazu dla odpowiedniego obrazu kontenera dla modułu.        |
    |Wymagane poświadczenia     | Jeśli ta jest zaznaczona, nazwa użytkownika i hasło są używane do pobierania modułów z pasującym adresem URL.        |
    |Udział wejściowy     | Wybierz udział wejściowy. Udział lokalny krawędzi jest udział wejściowy w tym przypadku. Moduł używany w tym miejscu przenosi pliki z udziału lokalnego krawędzi do udziału edge, gdzie są one przekazywane do chmury.        |
    |Udział wyjściowy     | Wybierz udział wyjściowy. Udział Edge jest udziałem wyjściowym w tym przypadku.        |
    |Typ wyzwalacza     | Wybierz z **pliku** lub **harmonogramu**. Wyzwalacz pliku jest uruchamiany za każdym razem, gdy wystąpi zdarzenie pliku, takie jak plik jest zapisywany w udziale wejściowym. Zaplanowany wyzwalacz uruchamia się na podstawie harmonogramu zdefiniowanego przez użytkownika.         |
    |Nazwa wyzwalacza     | Unikatowa nazwa wyzwalacza.         |
    |Zmienne środowiskowe| Opcjonalne informacje, które pomogą zdefiniować środowisko, w którym moduł będzie działać.   |

    ![Dodawanie i konfigurowanie modułu](./media/data-box-edge-deploy-configure-compute/add-module-1.png)

3. Wybierz pozycję **Dodaj**. Moduł zostanie dodany. Aktualizacja kafelka **dodaj moduł,** aby wskazać, że moduł jest wdrożony. 

    ![Wdrożony moduł](./media/data-box-edge-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Weryfikowanie przekształcania danych i transferu

Ostatnim krokiem jest upewnienie się, że moduł jest połączony i działa zgodnie z oczekiwaniami. Stan środowiska uruchomieniowego modułu powinien pokazywać uruchomienie dla urządzenia usługi IoT Edge w zasobie usługi IoT Hub.

Wykonaj poniższe kroki, aby sprawdzić, czy moduł jest uruchomiony:

1. Wybierz kafelek **Dodaj moduł.** Spowoduje to przejście do **modułów** bloku. Na liście modułów zidentyfikuj wdrożony moduł. Stan środowiska wykonawczego dodanego modułu powinien być *uruchomiony*.

    ![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute/verify-data-1.png)
 
1.  W Eksploratorze plików połącz się zarówno z wcześniej utworzonymi udziałami lokalnymi krawędzi, jak i z udziałami Edge.

    ![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute/verify-data-2.png) 
 
1.  Dodaj dane do udziału lokalnego.

    ![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute/verify-data-3.png) 
 
    Dane zostaną przeniesione do udziału chmurowego.

    ![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute/verify-data-4.png)  

    Następnie dane zostaną wypchnięte z udziału chmurowego na konto magazynu. Aby wyświetlić dane, przejdź do Eksploratora usługi Storage.

    ![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute/verify-data-5.png) 
 
Ukończono proces walidacji.


## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie obliczeń
> * Dodawanie udziałów
> * Dodawanie modułu obliczeniowego
> * Weryfikowanie przekształcania danych i transferu

Aby dowiedzieć się, jak administrować urządzeniem Data Box Edge, zobacz:

> [!div class="nextstepaction"]
> [Administrowanie usługą Data Box Edge za pomocą lokalnego internetowego interfejsu użytkownika](data-box-edge-manage-access-power-connectivity-mode.md)
