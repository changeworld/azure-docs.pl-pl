---
title: 'Szybki Start: Konfigurowanie aprowizacji urządzeń w Azure Portal'
description: Przewodnik Szybki Start platformy Azure — Konfigurowanie IoT Hub Device Provisioning Service platformy Azure w Azure Portal
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 498ec758fe8ad5d596a62d3541b39d860336e726
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151269"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-the-azure-portal"></a>Szybki Start: Konfigurowanie IoT Hub Device Provisioning Service przy użyciu Azure Portal

W tych krokach pokazano, jak w portalu skonfigurować zasoby platformy Azure w chmurze na potrzeby aprowizowania posiadanych urządzeń. Ten artykuł zawiera instrukcje tworzenia centrum IoT Hub, tworzenia nowego wystąpienia usługi IoT Hub Device Provisioning Service oraz łączenia obu usług ze sobą. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).


## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]


## <a name="create-a-new-instance-for-the-iot-hub-device-provisioning-service"></a>Tworzenie nowego wystąpienia dla usługi IoT Hub Device Provisioning Service

1. Wybierz ponownie przycisk **+ Utwórz zasób** .

2. *Wyszukaj w portalu Marketplace* **usługę Device Provisioning**. Wybierz pozycję **IoT Hub Device Provisioning Service** i kliknij przycisk **Utwórz** . 

3. Podaj następujące informacje dotyczące nowego wystąpienia usługi Device Provisioning i kliknij przycisk **Utwórz**.

    * **Nazwa:** podaj unikatową nazwę nowego wystąpienia usługi Device Provisioning Service. Jeśli wprowadzona nazwa jest dostępna, zostanie wyświetlony zielony znacznik wyboru.
    * **Subskrypcja**: wybierz subskrypcję, która ma zostać użyta do utworzenia tego wystąpienia usługi Device Provisioning Service.
    * **Grupa zasobów:** w tym polu możesz utworzyć nową grupę zasobów lub wybrać istniejącą grupę, w której ma znajdować się nowe wystąpienie. Wybierz tę grupę zasobów, w której znajduje się utworzone wcześniej centrum IoT Hub, na przykład **TestResources**. Dzięki umieszczeniu wszystkich powiązanych zasobów w jednej grupie można nimi zarządzać jednocześnie. Na przykład usunięcie grupy zasobów powoduje usunięcie wszystkich zasobów w tej grupie. Aby uzyskać więcej informacji, zobacz [Zarządzanie grupami zasobów usługi Azure Resource Manager](../azure-resource-manager/manage-resource-groups-portal.md).
    * **Lokalizacja**: wybierz lokalizację znajdującą się najbliżej Twoich urządzeń.

      ![Wprowadź podstawowe informacje o wystąpieniu usługi Device Provisioning w bloku portalu](./media/quick-setup-auto-provision/create-iot-dps-portal.png)  

4. Wybierz przycisk powiadomień, aby monitorować Tworzenie wystąpienia zasobu. Po pomyślnym wdrożeniu usługi wybierz pozycję **Przypnij do pulpitu nawigacyjnego**, a następnie **Przejdź do pozycji zasób**.

    ![Monitorowanie powiadomień dotyczących wdrażania](./media/quick-setup-auto-provision/pin-to-dashboard.png)

## <a name="link-the-iot-hub-and-your-device-provisioning-service"></a>Łączenie Centrum IoT Hub i usługi Device Provisioning

W tej sekcji dodasz konfigurację do wystąpienia usługi Device Provisioning. Ta konfiguracja określa centrum IoT Hub, dla którego będą aprowizowane urządzenia.

1. Wybierz przycisk **wszystkie zasoby** w menu po lewej stronie Azure Portal. Wybierz wystąpienie usługi Device Provisioning Service utworzone we wcześniejszej sekcji.  

2. Z menu usługi Device Provisioning wybierz pozycję **połączone centra IoT**. Kliknij przycisk **+ Dodaj** widoczny w górnej części strony. 

3. Na stronie **Dodawanie linku do centrum IoT Hub** wprowadź następujące informacje, aby połączyć nowe wystąpienie usługi Device Provisioning Service z centrum IoT Hub. Następnie naciśnij przycisk **Zapisz**. 

    * **Subskrypcja:** wybierz subskrypcję zawierającą centrum IoT Hub, które chcesz połączyć z nowym wystąpieniem usługi Device Provisioning Service.
    * **Centrum IoT Hub:** wybierz centrum IoT Hub, które chcesz połączyć z nowym wystąpieniem usługi Device Provisioning Service.
    * **Zasady dostępu:** wybierz **iothubowner** jako poświadczenia do nawiązania połączenia z centrum IoT Hub.  

      ![Połącz nazwę centrum, aby połączyć się z wystąpieniem usługi Device Provisioning w bloku portalu](./media/quick-setup-auto-provision/link-iot-hub-to-dps-portal.png)  

3. Teraz wybrane centrum powinno być widoczne w bloku **Powiązane centra IoT**. Może być konieczne **odświeżenie** , aby można było je wyświetlić.


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Inne przewodniki Szybki start w tej kolekcji bazują na tym przewodniku. Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki start lub samouczkami, nie usuwaj zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie planujesz kontynuować pracy, wykonaj następujące czynności, aby usunąć wszystkie zasoby utworzone w witrynie Azure Portal w ramach tego przewodnika Szybki start.

1. Z menu po lewej stronie w Azure Portal wybierz pozycję **wszystkie zasoby** , a następnie wybierz usługę Device Provisioning. W górnej części okienka szczegółów urządzenia wybierz pozycję **Usuń**.  
2. Z menu po lewej stronie w Azure Portal wybierz pozycję **wszystkie zasoby** , a następnie wybierz swoje centrum IoT Hub. W górnej części okienka szczegółów centrum wybierz pozycję **Usuń**.  

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono Centrum IoT Hub i wystąpienie usługi Device Provisioning Service i połączono te dwa zasoby. Aby dowiedzieć się, jak używać tego Instalatora do aprowizacji symulowanego urządzenia, przejdź do przewodnika Szybki Start dotyczącego tworzenia symulowanego urządzenia.

> [!div class="nextstepaction"]
> [Przewodnik Szybki Start dotyczący tworzenia symulowanego urządzenia](./quick-create-simulated-device.md)
