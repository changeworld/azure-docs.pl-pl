---
title: Wizualizacja danych w czasie rzeczywistym danych czujnika z Centrum Azure IoT — aplikacji sieci Web | Dokumentacja firmy Microsoft
description: Funkcja aplikacji sieci Web usługi Microsoft Azure App Service, do wizualizacji danych temperatury i wilgotności, który został zebrany z czujnika i wysyłany do Centrum Iot.
services: iot-hub
documentationcenter: ''
author: rangv
manager: timlt
tags: ''
keywords: wizualizację danych czasu rzeczywistego, wizualizacji danych na żywo czujnik wizualizacji danych
ms.assetid: e42b07a8-ddd4-476e-9bfb-903d6b033e91
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: 0edf72595f67dd9f6b33f9bfeaf4cd98405dc3ca
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-by-using-the-web-apps-feature-of-azure-app-service"></a>Wizualizacja danych czujnika w czasie rzeczywistym z Centrum Azure IoT za pomocą funkcji aplikacji sieci Web usługi aplikacji Azure

![Diagram end-to-end](media/iot-hub-get-started-e2e-diagram/5.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Omawiane zagadnienia

W tym samouczku możesz dowiedzieć się, jak do wizualizacji danych czujnika w czasie rzeczywistym Centrum IoT odbieranych przez uruchomienie aplikacji sieci web, w której znajduje się w aplikacji sieci web. Jeśli chcesz spróbować wizualizacji danych w Centrum IoT przy użyciu usługi Power BI, zobacz [usługi Power BI do wizualizacji danych czujnika w czasie rzeczywistym z Centrum IoT Azure](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>Co zrobić

- Tworzenie aplikacji sieci web w portalu Azure.
- Przygotuj się Centrum IoT na dostęp do danych przez dodanie grupy odbiorców.
- Konfigurowanie aplikacji sieci web można odczytać danych czujnika z Centrum IoT.
- Przekaż aplikację sieci web ma być obsługiwana przez aplikację sieci web.
- Otwórz aplikację sieci web, aby wyświetlić dane w czasie rzeczywistym temperatury i wilgotności z Centrum IoT.

## <a name="what-you-need"></a>Co jest potrzebne

- [Urządzenie jest skonfigurowane](iot-hub-raspberry-pi-kit-node-get-started.md), która obejmuje następujące wymagania:
  - Aktywną subskrypcją platformy Azure
  - Centrum Iot w ramach Twojej subskrypcji
  - Aplikacji klienckiej, która wysyła komunikaty do Centrum Iot
- [Pobierz narzędzia Git](https://www.git-scm.com/downloads)

## <a name="create-a-web-app"></a>Tworzenie aplikacji sieci Web

1. W [portalu Azure](https://portal.azure.com/), kliknij przycisk **Utwórz zasób** > **sieci Web i mobilność** > **aplikacji sieci Web**.
2. Wprowadź nazwę unikatową zadania upewnij się, subskrypcji, określ grupę zasobów i lokalizację, wybierz opcję **Przypnij do pulpitu nawigacyjnego**, a następnie kliknij przycisk **Utwórz**.

   Firma Microsoft zaleca, wybierz lokalizację, jak te grupy zasobów. W ten sposób mogą korzystać z szybkość przetwarzania i zmniejsza koszty transferu danych.

   ![Tworzenie aplikacji sieci Web](media/iot-hub-live-data-visualization-in-web-apps/2_create-web-app-azure.png)

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="configure-the-web-app-to-read-data-from-your-iot-hub"></a>Konfigurowanie aplikacji sieci web można odczytać danych z Centrum IoT

1. Otwórz aplikację sieci web, wystarczy aprowizowanej.
2. Kliknij przycisk **ustawienia aplikacji**, a następnie w obszarze **ustawień aplikacji**, Dodaj następujące pary klucz wartość:

   | Klucz                                   | Wartość                                                        |
   |---------------------------------------|--------------------------------------------------------------|
   | Azure.IoT.IoTHub.ConnectionString     | Uzyskane z Centrum iothub explorer                                |
   | Azure.IoT.IoTHub.ConsumerGroup        | Nazwa grupy odbiorców, które można dodać do Centrum IoT  |

   ![Dodawanie ustawień aplikacji sieci web z pary klucz wartość](media/iot-hub-live-data-visualization-in-web-apps/4_web-app-settings-key-value-azure.png)

3. Kliknij przycisk **ustawienia aplikacji**w obszarze **ustawienia ogólne**, Przełącz **sieci Web sockets** , a następnie kliknij przycisk **zapisać**.

   ![Przełącz opcję gniazda sieci Web](media/iot-hub-live-data-visualization-in-web-apps/10_toggle_web_sockets.png)

## <a name="upload-a-web-application-to-be-hosted-by-the-web-app"></a>Przekaż aplikację sieci web ma być obsługiwana przez aplikację sieci web

W witrynie GitHub wprowadziliśmy dostępności aplikacji sieci web, który wyświetla danych czujnika w czasie rzeczywistym z Centrum IoT. To wszystko, co należy zrobić, konfigurowanie aplikacji sieci web do pracy z repozytorium Git, pobierania aplikacji sieci web z usługi GitHub i przekaż go do platformy Azure dla aplikacji sieci web do hosta.

1. W aplikacji sieci web, kliknij przycisk **opcje wdrażania** > **wybierz źródło** > **lokalnego repozytorium Git**, a następnie kliknij przycisk **OK**.

   ![Skonfiguruj wdrożenia aplikacji sieci web do użycia lokalnego repozytorium Git](media/iot-hub-live-data-visualization-in-web-apps/5_configure-web-app-deployment-local-git-repository-azure.png)

2. Kliknij przycisk **poświadczenia wdrażania**, Utwórz nazwę użytkownika i hasło używane do nawiązania połączenia repozytorium Git w usłudze Azure, a następnie kliknij przycisk **zapisać**.

3. Kliknij przycisk **omówienie**i zanotuj wartość **adres url klonowania Git**.

   ![Pobierz adres URL klonowania Git aplikacji sieci web](media/iot-hub-live-data-visualization-in-web-apps/7_web-app-git-clone-url-azure.png)

4. Otwórz okno terminalu na komputerze lokalnym lub polecenie.

5. Pobierz aplikację sieci web z usługi GitHub i przekaż go do platformy Azure dla aplikacji sieci web do hosta. Aby to zrobić, uruchom następujące polecenia:

   ```bash
   git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
   cd web-apps-node-iot-hub-data-visualization
   git remote add webapp <Git clone URL>
   git push webapp master:master
   ```

   > [!NOTE]
   > \<Adres URL klonowania Git\> jest adres URL repozytorium Git na **omówienie** strony aplikacji sieci web.

## <a name="open-the-web-app-to-see-real-time-temperature-and-humidity-data-from-your-iot-hub"></a>Otwórz aplikację sieci web, aby wyświetlić dane w czasie rzeczywistym temperatury i wilgotności z Centrum IoT

Na **omówienie** strony aplikacji sieci web, kliknij adres URL do otwierania aplikacji sieci web.

![Pobierz adres URL aplikacji sieci web](media/iot-hub-live-data-visualization-in-web-apps/8_web-app-url-azure.png)

Powinny pojawić się w czasie rzeczywistym danych temperatury i wilgotności z Centrum IoT.

![Wyświetlanie w czasie rzeczywistym temperatury i wilgotności strony aplikacji sieci Web](media/iot-hub-live-data-visualization-in-web-apps/9_web-app-page-show-real-time-temperature-humidity-azure.png)

> [!NOTE]
> Upewnij się, że przykładowa aplikacja jest uruchomiona na twoim urządzeniu. Jeśli nie, otrzymasz pusty wykres, może się odwoływać do samouczków w obszarze [skonfigurować na twoim urządzeniu](iot-hub-raspberry-pi-kit-node-get-started.md).

## <a name="next-steps"></a>Kolejne kroki
W celu wizualizacji danych czujnika w czasie rzeczywistym z Centrum IoT pomyślnie zastosowano aplikację sieci web.

Aby alternatywny sposób wizualizuj dane z Centrum IoT Azure, zobacz [usługi Power BI do wizualizacji danych czujnika w czasie rzeczywistym z Centrum IoT](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
