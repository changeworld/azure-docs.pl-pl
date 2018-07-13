---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-edge
author: wesmc7777
ms.service: iot-edge
ms.topic: include
ms.date: 06/26/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: c0b9f9e9808de90df84edf2d3c409a921629baee
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38728309"
---
Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować już utworzone zasoby oraz konfiguracje i użyć ich ponownie.

W przeciwnym razie możesz usunąć konfigurację lokalną i zasoby platformy Azure utworzone podczas pracy z tym artykułem, aby uniknąć naliczania opłat. 

> [!IMPORTANT]
> Usunięcie zasobów i grupy zasobów platformy Azure jest nieodwracalne. Grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. Jeśli usługa IoT Hub została utworzona wewnątrz istniejącej grupy zasobów zawierającej zasoby, które chcesz zachować, zamiast usuwać całą grupę zasobów, usuń tylko zasób usługi IoT Hub.
>

Aby usunąć tylko centrum IoT Hub, uruchom poniższe polecenie po zastąpieniu elementu `<YourIoTHub>` nazwą centrum i elementu `<TestResources>` nazwą grupy zasobów:

```azurecli-interactive
az iot hub delete --name <YourIoTHub> --resource-group <TestResources>
```


Aby usunąć całą grupę zasobów na podstawie nazwy:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i kliknij pozycję **Grupy zasobów**.

2. W polu tekstowym **Filtruj według nazwy** wpisz nazwę grupy zasobów zawierającej usługę IoT Hub. 

3. Z prawej strony grupy zasobów na liście wyników kliknij pozycję **...**, a następnie kliknij pozycję **Usuń grupę zasobów**.

    ![Usuwanie](./media/iot-edge-quickstarts-clean-up-resources/iot-edge-delete-resource-group.png)

4. Zobaczysz prośbę o potwierdzenie usunięcia grupy zasobów. Ponownie wpisz nazwę grupy zasobów w celu potwierdzenia, a następnie kliknij pozycję **Usuń**. Po krótkim czasie grupa zasobów i wszystkie zawarte w niej zasoby zostaną usunięte.






