---
title: Konfigurowanie aprowizowania urządzeń w witrynie Azure Portal | Microsoft Docs
description: Przewodnik Szybki start platformy Azure — Konfigurowanie usługi Azure IoT Hub Device Provisioning Service w witrynie Azure Portal
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2017
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 276bd33f5724db4d67da0cc31b16297915c9a417
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629443"
---
# <a name="set-up-the-iot-hub-device-provisioning-service-with-the-azure-portal"></a>Konfigurowanie usługi IoT Hub Device Provisioning (wersja zapoznawcza) w witrynie Azure Portal

W tych krokach pokazano, jak w portalu skonfigurować zasoby platformy Azure w chmurze na potrzeby aprowizowania posiadanych urządzeń. Obejmuje to utworzenie centrum IoT, utworzenie nowej usługi IoT Hub Device Provisioning Service i powiązanie obu usług ze sobą. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).


## <a name="log-in-to-the-azure-portal"></a>Logowanie do witryny Azure Portal

Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT

1. Kliknij przycisk **Utwórz zasób** (+) znajdujący się w lewym górnym rogu witryny Azure Portal.

2. Wybierz pozycję **Internet rzeczy**, wybierz pozycję **Centrum IoT** i kliknij przycisk **Utwórz**. 

3. Nadaj **nazwę** swojemu centrum IoT. Wybierz jedną z dostępnych opcji cen, wprowadź [jednostki usługi IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/), wybierz liczbę partycji dla komunikatów wysyłanych między urządzeniem a chmurą i subskrypcję, która będzie używana dla tego zasobu. Wprowadź nazwę nowej lub istniejącej grupy zasobów i wybierz lokalizację. Na koniec kliknij pozycję **Utwórz**.

    ![Wprowadzanie podstawowych informacji o centrum IoT w bloku portalu](./media/quick-setup-auto-provision/create-iot-hub-portal.png)  

4. Po pomyślnym wdrożeniu centrum IoT automatycznie zostanie otwarty blok podsumowania tego centrum.


## <a name="create-a-new-instance-for-the-iot-hub-device-provisioning-service"></a>Tworzenie nowego wystąpienia dla usługi IoT Hub Device Provisioning Service

1. Kliknij przycisk **Utwórz zasób** (+) znajdujący się w lewym górnym rogu witryny Azure Portal.

2. *Wyszukaj w witrynie Marketplace* **usługę Device Provisioning Service**. Wybierz pozycję **Usługa IoT Hub Device Provisioning** i kliknij przycisk **Utwórz**. 

3. Nadaj **nazwę** swojemu wystąpieniu usługi Device Provisioning Service. Wybierz subskrypcję, która będzie używana dla tego wystąpienia, i nazwę dla nowej lub istniejącej grupy zasobów. Wybierz lokalizację. Na koniec kliknij pozycję **Utwórz**.

    ![Wprowadzanie podstawowych informacji o wystąpieniu usługi DPS w bloku portalu](./media/quick-setup-auto-provision/create-iot-dps-portal.png)  

4. Po pomyślnym wdrożeniu usługi automatycznie zostanie otwarty blok podsumowania tej usługi.


## <a name="link-the-iot-hub-and-your-device-provisioning-service"></a>Połącz centrum IoT ze swoją usługą Device Provisioning Service

1. Kliknij przycisk **Wszystkie zasoby** w menu po lewej stronie witryny Azure Portal. Wybierz wystąpienie usługi Device Provisioning Service utworzone we wcześniejszej sekcji.  

2. W bloku podsumowania usługi Device Provisioning Service wybierz pozycję **Połączone centra IoT**. Kliknij przycisk **+ Dodaj** widoczny u góry. 

3. W bloku **Dodaj link do centrum IoT** portalu wybierz bieżącą subskrypcję albo wprowadź nazwę i parametry połączenia dla innej subskrypcji. Wybierz nazwę centrum z listy rozwijanej. Po zakończeniu kliknij przycisk **Zapisz**. 

    ![Powiązywanie nazwy centrum z wystąpieniem usługi DPS w bloku portalu](./media/quick-setup-auto-provision/link-iot-hub-to-dps-portal.png)  

3. Teraz wybrane centrum powinno być widoczne w bloku **Powiązane centra IoT**. Aby wyświetlić blok **Połączone centra IoT Hub**, może być konieczne kliknięcie przycisku **Odśwież**.



## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Inne przewodniki Szybki start w tej kolekcji bazują na tym przewodniku. Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki start lub samouczkami, nie usuwaj zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie planujesz kontynuować pracy, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone w witrynie Azure Portal w ramach tego przewodnika Szybki start.

1. W witrynie Azure Portal w menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie wybierz swoją usługę Device Provisioning Service. U góry bloku **Wszystkie zasoby** kliknij pozycję **Usuń**.  
2. W witrynie Azure Portal w menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie wybierz swoje centrum IoT. U góry bloku **Wszystkie zasoby** kliknij pozycję **Usuń**.  

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start zostały wdrożone centrum IoT i wystąpienie usługi Device Provisioning Service, po czym te dwa zasoby zostały połączone ze sobą. Aby dowiedzieć się, jak za pomocą tej konfiguracji zaaprowizować symulowane urządzenie, przejdź do przewodnika Szybki start omawiającego tworzenie symulowanego urządzenia.

> [!div class="nextstepaction"]
> [Przewodnik Szybki start z tworzeniem symulowanego urządzenia](./quick-create-simulated-device.md)
