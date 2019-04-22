---
title: Wizualizacja danych w czasie rzeczywistym danych z czujników z usługi Azure IoT hub — aplikacje sieci Web | Dokumentacja firmy Microsoft
description: Funkcja Web Apps, Microsoft Azure App Service umożliwia wizualizowanie temperatury i wilgotności danych zebranych z czujników i wysyłane do usługi Iot hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: robinsh
ms.openlocfilehash: 070f37a969411cfc4caf5f2d2b089ccfae759ca2
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59683202"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-by-using-the-web-apps-feature-of-azure-app-service"></a>Wizualizowanie danych z czujników w czasie rzeczywistym z usługi Azure IoT hub przy użyciu funkcji Web Apps w usłudze Azure App Service

![Diagram end-to-end](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Omawiane zagadnienia

W tym samouczku dowiesz się, jak wizualizować dane czujników w czasie rzeczywistym usługi IoT hub odbierze, uruchamiając aplikację internetową, która jest hostowana w usłudze web app. Jeśli chcesz wypróbować jej możliwości do wizualizacji danych w usłudze IoT hub za pomocą usługi Power BI, zobacz [usługa Power BI umożliwia wizualizowanie danych z czujników w czasie rzeczywistym z usługi Azure IoT Hub](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>Co należy zrobić

* Tworzenie aplikacji internetowej w witrynie Azure portal.
* Przygotuj usługi IoT hub na dostęp do danych, dodając grupy odbiorców.
* Konfigurowanie aplikacji sieci web, które można odczytać danych z czujników z Centrum IoT hub.
* Przekaż aplikację sieci web hostowane przez aplikację internetową.
* Otwórz aplikację sieci web, aby wyświetlić dane w czasie rzeczywistym temperatury i wilgotności z usługi IoT hub.

## <a name="what-you-need"></a>Co jest potrzebne

* Wykonaj [symulatora w trybie online urządzenia Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) samouczka lub jednym z samouczków urządzenia; na przykład [Raspberry Pi w środowisku node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Obejmują one następujące wymagania:

  * Aktywna subskrypcja platformy Azure
  * Centrum Iot hub w ramach Twojej subskrypcji
  * Aplikacja kliencka, która wysyła komunikaty do Centrum Iot hub

* [Pobierz narzędzia Git](https://www.git-scm.com/downloads)

## <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

1. W [witryny Azure portal](https://portal.azure.com/), kliknij przycisk **Utwórz zasób** > **sieci Web i mobilność** > **aplikacji sieci Web**.

2. Wprowadź unikatową nazwę zadania, sprawdź subskrypcję, określ grupę zasobów i lokalizację, wybierz **Przypnij do pulpitu nawigacyjnego**, a następnie kliknij przycisk **Utwórz**.

   Zaleca się wybrać tej samej lokalizacji co grupa zasobów. 
   
   ![Tworzenie aplikacji internetowej](./media/iot-hub-live-data-visualization-in-web-apps/2_create-web-app-azure.png)

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="configure-the-web-app-to-read-data-from-your-iot-hub"></a>Konfigurowanie aplikacji sieci web, które można odczytać danych z usługi IoT hub

1. Otwórz aplikację sieci web, po prostu aprowizowanej.

2. Kliknij przycisk **ustawienia aplikacji**, a następnie w obszarze **ustawienia aplikacji**, Dodaj następujące pary klucz/wartość:

   | Klucz                                   | Wartość                                                        |
   |---------------------------------------|--------------------------------------------------------------|
   | Azure.IoT.IoTHub.ConnectionString     | Uzyskany z wiersza polecenia platformy Azure                                      |
   | Azure.IoT.IoTHub.ConsumerGroup        | Nazwa grupy konsumentów, dodanego do usługi IoT hub  |
   | WEBSITE_NODE_DEFAULT_VERSION          | 8.9.4                                                        |

   ![Dodawanie ustawień aplikacji sieci web za pomocą pary klucz/wartość](./media/iot-hub-live-data-visualization-in-web-apps/3_web-app-settings-key-value-azure.png)

3. Kliknij przycisk **ustawienia aplikacji**w obszarze **ustawienia ogólne**, Przełącz **Web sockets** opcji, a następnie kliknij przycisk **Zapisz**.

   ![Przełącz opcję gniazda sieci Web](./media/iot-hub-live-data-visualization-in-web-apps/4_toggle_web_sockets.png)

## <a name="upload-a-web-application-to-be-hosted-by-the-web-app"></a>Przekaż aplikację sieci web hostowane przez aplikację sieci web

W witrynie GitHub wprowadziliśmy dostępności aplikacji sieci web, która wyświetla dane czujników w czasie rzeczywistym z Centrum IoT hub. To wszystko, co należy zrobić, konfigurowanie aplikacji sieci web, aby pracować z repozytorium Git, pobrać aplikację sieci web z witryny GitHub i przekaż go na platformę Azure dla aplikacji sieci web do hosta.

1. W aplikacji sieci web kliknij **opcje wdrażania** > **wybierz źródło** > **lokalnego repozytorium Git**, a następnie kliknij przycisk **OK**.

   ![Konfigurowanie wdrożenia aplikacji sieci web do użycia lokalnego repozytorium Git](./media/iot-hub-live-data-visualization-in-web-apps/5_configure-web-app-deployment-local-git-repository-azure.png)

2. Kliknij przycisk **poświadczenia wdrożenia**, Utwórz nazwę użytkownika i hasło do użycia, aby nawiązać połączenie z repozytorium Git na platformie Azure, a następnie kliknij przycisk **Zapisz**.

3. Kliknij przycisk **Przegląd**i zanotuj wartość ustawienia **adres url klonowania Git**.

   ![Pobierz adres URL klonowania Git aplikacji sieci web](./media/iot-hub-live-data-visualization-in-web-apps/6_web-app-git-clone-url-azure.png)

4. Otwórz polecenie lub okno terminalu na komputerze lokalnym.

5. Pobieranie aplikacji internetowej z usługi GitHub, a następnie przekaż go na platformę Azure dla aplikacji sieci web do hosta. Aby to zrobić, uruchom następujące polecenia:

   ```bash
   git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
   cd web-apps-node-iot-hub-data-visualization
   git remote add webapp <Git clone URL>
   git push webapp master:master
   ```

   > [!NOTE]
   > \<Adres URL klonowania Git\> jest adres URL repozytorium Git w **Przegląd** stronie aplikacji sieci web.

## <a name="open-the-web-app-to-see-real-time-temperature-and-humidity-data-from-your-iot-hub"></a>Otwórz aplikację sieci web, aby wyświetlić dane w czasie rzeczywistym temperatury i wilgotności z usługi IoT hub

Na **Przegląd** strony aplikacji sieci web, kliknij adres URL, aby otworzyć aplikację sieci web.

![Pobierz adres URL aplikacji sieci web](./media/iot-hub-live-data-visualization-in-web-apps/7_web-app-url-azure.png)

Powinien zostać wyświetlony w czasie rzeczywistym dane temperatury i wilgotności z usługi IoT hub.

![Wyświetlanie w czasie rzeczywistym temperatury i wilgotności strona aplikacji internetowej](./media/iot-hub-live-data-visualization-in-web-apps/8_web-app-page-show-real-time-temperature-humidity-azure.png)

> [!NOTE]
> Upewnij się, że przykładowa aplikacja jest uruchomiona na urządzeniu. Jeśli nie, zostanie wyświetlony pusty wykres, możesz zapoznać się z samouczkami, w obszarze [skonfigurować na twoim urządzeniu](iot-hub-raspberry-pi-kit-node-get-started.md).

## <a name="next-steps"></a>Kolejne kroki

Aplikacji sieci web została pomyślnie użyta, wizualizowanie danych z czujników w czasie rzeczywistym z usługi IoT hub.

Aby uzyskać alternatywny sposób wizualizować dane z usługi Azure IoT Hub, zobacz [usługa Power BI umożliwia wizualizowanie danych z czujników w czasie rzeczywistym z usługi IoT hub](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]