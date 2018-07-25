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
ms.openlocfilehash: 657006360105ac26091c54fe2e0deb523d1e6dea
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008505"
---
Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie.

W przeciwnym razie możesz usunąć konfigurację lokalną i zasoby platformy Azure utworzone podczas pracy z tym artykułem, aby uniknąć naliczania opłat. 

> [!IMPORTANT]
> Usunięcie zasobów i grup zasobów platformy Azure jest nieodwracalne. Po usunięciu tych elementów grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub niewłaściwej grupy zasobów. Jeśli centrum IoT Hub zostało utworzone w istniejącej grupie zasobów zawierającej zasoby, które chcesz zachować, zamiast usuwać całą grupę zasobów, usuń tylko zasób centrum IoT Hub.
>

Aby usunąć tylko centrum IoT Hub, wykonaj następujące polecenie. Zastąp ciąg \<YourIoTHub> nazwą swojego centrum IoT Hub, a ciąg \<TestResources> nazwą swojej grupy zasobów:

```azurecli-interactive
az iot hub delete --name <YourIoTHub> --resource-group <TestResources>
```


Aby usunąć całą grupę zasobów na podstawie nazwy:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i wybierz pozycję **Grupy zasobów**.

2. W polu tekstowym **Filtruj według nazwy** wpisz nazwę grupy zasobów zawierającej centrum IoT Hub. 

3. Po prawej stronie grupy zasobów na liście wyników wybierz wielokropek (**...**), a następnie wybierz pozycję **Usuń grupę zasobów**.

    ![Usuwanie grupy zasobów](./media/iot-edge-quickstarts-clean-up-resources/iot-edge-delete-resource-group.png)

4. Zobaczysz prośbę o potwierdzenie usunięcia grupy zasobów. Ponownie wprowadź nazwę grupy zasobów w celu potwierdzenia, a następnie wybierz pozycję **Usuń**. Po krótkim czasie grupa zasobów i wszystkie zawarte w niej zasoby zostaną usunięte.






