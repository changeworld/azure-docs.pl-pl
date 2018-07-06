---
title: Zapisywanie komunikatów Centrum IoT w magazynie danych platformy Azure | Dokumentacja firmy Microsoft
description: Umożliwia routing komunikatów usługi IoT Hub zapisywania wiadomości Centrum IoT do usługi Azure blob storage. Wiadomości Centrum IoT zawierają informacje, takie jak dane czujników, które są wysyłane z urządzenia IoT.
author: rangv
manager: ''
keywords: Magazyn danych iot, Magazyn danych czujników iot
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 84355453a5cb8d8f42abdcbde5432651c9c035b0
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2018
ms.locfileid: "37856294"
---
# <a name="save-iot-hub-messages-that-contain-sensor-data-to-your-azure-blob-storage"></a>Zapisz komunikaty do Centrum IoT, które zawierają danych czujnika do usługi Azure blob storage

![Diagram end-to-end](./media/iot-hub-store-data-in-azure-table-storage/1_route-to-storage.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Omawiane zagadnienia

Dowiesz się, jak utworzyć konto usługi Azure storage i aplikację funkcji platformy Azure do przechowywania komunikatów Centrum IoT w usłudze Azure Blob storage.

## <a name="what-you-do"></a>Co należy zrobić

- Utworzenie konta magazynu platformy Azure.
- Konfigurowanie usługi IoT hub do przesyłania wiadomości w magazynie.

## <a name="what-you-need"></a>Co jest potrzebne

- [Konfigurowanie urządzenia](iot-hub-raspberry-pi-kit-node-get-started.md) do obejmują następujące wymagania:
  - Aktywna subskrypcja platformy Azure
  - Centrum IoT hub w ramach Twojej subskrypcji 
  - Uruchomionej aplikacji, która wysyła komunikaty do Centrum IoT hub

## <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage

1. W [witryny Azure portal](https://portal.azure.com/), kliknij przycisk **Utwórz zasób** > **magazynu** > **konta magazynu**.

2. Wprowadź wymagane informacje dla konta magazynu:

   ![Tworzenie konta magazynu w witrynie Azure portal](./media/iot-hub-store-data-in-azure-table-storage/1_azure-portal-create-storage-account.png)

   * **Nazwa**: Nazwa konta magazynu. Nazwa musi być unikatowa w skali globalnej.

   * **Rodzaj konta**: Wybierz `Storage (general purpose v1)`.

   * **Lokalizacja**: Wybierz z tej samej lokalizacji, która korzysta z usługi IoT hub.

   * **Replikacja**: Wybierz `Locally-redundant storage (LRS)`.

   * **Wydajność**: Wybierz `Standard`.

   * **Wymagany bezpieczny transfer**: Wybierz `Disabled`.

   * **Subskrypcja**: wybierz subskrypcję platformy Azure.

   * **Grupa zasobów**: Użyj tej samej grupie zasobów, która korzysta z usługi IoT hub.

   * **Przypnij do pulpitu nawigacyjnego**: wybierz tę opcję, aby mieć łatwy dostęp do centrum IoT Hub z pulpitu nawigacyjnego.

3. Kliknij przycisk **Utwórz**.

## <a name="prepare-your-iot-hub-to-route-messages-to-storage"></a>Przygotowanie do przesyłania wiadomości do magazynu w Centrum IoT hub

Usługa IoT Hub natywnie obsługuje routing komunikatów do usługi Azure storage jako obiekty BLOB. Aby dowiedzieć się więcej o niestandardowych punktach końcowych usługi Azure IoT Hub, mogą odwoływać się do [lista wbudowanej punktów końcowych usługi IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-endpoints#custom-endpoints).

### <a name="add-storage-as-a-custom-endpoint"></a>Dodawanie magazynu jako niestandardowy punkt końcowy

1. Przejdź do Centrum IoT hub w witrynie Azure portal. 

2. Kliknij przycisk **punktów końcowych** > **Dodaj**. 

3. Nazwa punktu końcowego, a następnie wybierz pozycję **kontenera magazynu Azure** jako typ punktu końcowego. 

4. Użyj selektora, aby wybrać konto magazynu, do którego został utworzony w poprzedniej sekcji. Utwórz kontener magazynu i wybierz ją, a następnie kliknij przycisk **OK**.

   ![Tworzenie niestandardowego punktu końcowego w usłudze IoT Hub](./media/iot-hub-store-data-in-azure-table-storage/2_custom-storage-endpoint.png)

### <a name="add-a-route-to-route-data-to-storage"></a>Dodawanie danych trasy, trasa do magazynu

1. Kliknij przycisk **trasy** > **Dodaj** i wprowadź nazwę dla danej trasy. 

2. Wybierz **komunikaty z urządzenia** jako źródła danych, a następnie wybierz punkt końcowy magazynu został właśnie utworzony jako punktu końcowego dla trasy. 

3. Wprowadź `true` jako ciąg zapytania, następnie kliknij przycisk **Zapisz**.

   ![Utwórz trasę w usłudze IoT Hub](./media/iot-hub-store-data-in-azure-table-storage/3_create-route.png)
  
### <a name="add-a-route-for-hot-path-telemetry-optional"></a>Dodaj trasę dla ścieżki aktywnej telemetrii (opcjonalnie)

Domyślnie usługi IoT Hub kieruje wszystkie komunikaty, które nie są zgodne z jakichkolwiek innych tras do wbudowany punkt końcowy. Ponieważ wszystkie komunikaty telemetryczne są teraz zgodne reguły, które kieruje komunikaty do usługi storage, musisz dodać innej trasy wiadomości do zapisania wbudowany punkt końcowy. Nie ma dodatkowych opłat do przesyłania wiadomości do wielu punktów końcowych.

> [!NOTE]
> Jeśli nie wykonujesz dodatkowe przetwarzanie danych telemetrycznych wiadomości, można pominąć ten krok.

1. Kliknij przycisk **Dodaj** z poziomu okienka tras i wprowadź nazwę dla danej trasy. 

2. Wybierz **komunikaty z urządzenia** jako źródło danych i **zdarzenia** jako punktu końcowego. 

3. Wprowadź `true` jako ciąg zapytania, następnie kliknij przycisk **Zapisz**.

  ![Tworzona jest trasa aktywną ścieżkę w usłudze IoT Hub](./media/iot-hub-store-data-in-azure-table-storage/4_hot-path-route.png)

## <a name="verify-your-message-in-your-storage-container"></a>Sprawdź wiadomość w kontenerze magazynu

1. Uruchamianie przykładowej aplikacji na urządzeniu, aby wysyłać komunikaty do Centrum IoT hub.

2. [Pobieranie i instalowanie Eksploratora usługi Azure Storage](http://storageexplorer.com/).

3. Otwórz Eksploratora usługi Storage, kliknij przycisk **Dodaj konto platformy Azure** > **Zaloguj**, a następnie zaloguj się do konta platformy Azure.

4. Kliknij subskrypcję platformy Azure > **kont magazynu** > konta magazynu > **kontenery obiektów Blob** > kontenera.

   Powinien zostać wyświetlony komunikatów wysyłanych z urządzenia do usługi IoT hub w kontenerze obiektów blob.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 

W tym samouczku dodany na koncie magazynu, a następnie dodany routingu dla komunikatów z usługi IoT Hub do zapisania się do konta magazynu. Aby wyczyścić zasoby utworzone, możesz usunąć informacji o routingu, a następnie usuń konto magazynu. 

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

2. Kliknij przycisk **grup zasobów** i wybierz grupę zasobów, możesz użyć. Zostanie wyświetlona lista zasobów w grupie. 

   > [!NOTE]
   > Jeśli chcesz usunąć wszystkie zasoby w grupie zasobów, kliknij przycisk **Usuń** Aby usunąć grupę zasobów, a następnie wykonaj instrukcje. Spowoduje to usunięcie wszystko, co w danej grupie zasobów co zakończeniu czyszczenia zasobów i od razu przejść do następnej sekcji.

3. Kliknij Centrum IoT hub, używane na potrzeby tego samouczka. 

4. W okienku usługi IoT Hub kliknij **trasy**. Kliknij pole wyboru obok reguły routingu, możesz dodać, a następnie kliknij przycisk **Usuń**. Po wyświetleniu monitu, jeśli, na pewno chcesz usunąć tej trasy, kliknij przycisk **tak**.

   ![Usuń regułę routingu](./media/iot-hub-store-data-in-azure-table-storage/cleanup-remove-routing.png)

   Zamknij okienko routingu. Nastąpi powrót do okienka grupy zasobów.

5. Kliknij ponownie w Centrum IoT hub. 

6. W okienku usługi IoT Hub kliknij **punktów końcowych**. Kliknij pole wyboru obok punkt końcowy został dodany do kontenera magazynu, a następnie kliknij przycisk **Usuń**. Po wyświetleniu monitu, jeśli, na pewno chcesz usunąć wybrany punkt końcowy, kliknij przycisk **tak**.

    ![Usuwanie punktu końcowego](./media/iot-hub-store-data-in-azure-table-storage/cleanup-remove-endpoint.png)

    Zamknij okienko punkty końcowe. Nastąpi powrót do okienka grupy zasobów. 

7.  Kliknij konto magazynu, zdefiniowanych na potrzeby tego samouczka. 

8.  W okienku konta magazynu, kliknij polecenie **Usuń** można usunąć konta magazynu. Nastąpi przekierowanie do **Usuń konto magazynu** okienka.

   ![Usuń konto magazynu](./media/iot-hub-store-data-in-azure-table-storage/cleanup-remove-storageaccount.png)

8.  Wpisz nazwę konta magazynu, a następnie kliknij przycisk **Usuń** w dolnej części okienka. 

## <a name="next-steps"></a>Kolejne kroki

Utworzono pomyślnie Twoje konto usługi Azure storage i routingu wiadomości z usługi IoT Hub na kontener obiektów blob w ramach tego konta magazynu.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
