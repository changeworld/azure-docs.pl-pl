---
title: Zarejestrować nowe urządzenie Azure IoT krawędzi (w kodzie VS) | Dokumentacja firmy Microsoft
description: Użyj programu Visual Studio Code, aby utworzyć nowe urządzenie brzegowe IoT w Centrum Azure IoT
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/14/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7902461f58df1b4fe0c3ed3b577f668fe8be4cc2
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036532"
---
# <a name="register-a-new-azure-iot-edge-device-from-visual-studio-code"></a>Zarejestrować nowe urządzenie brzegowe IoT Azure w programie Visual Studio Code

Przed użyciem urządzenia IoT Azure IoT krawędzi, musisz zarejestrować ich przy użyciu Centrum IoT. Po zarejestrowaniu urządzenia, pojawi się ciąg połączenia, który może służyć do konfigurowania Twojego urządzenia pod kątem obciążeń krawędzi. 

W tym artykule pokazano, jak zarejestrować nowe urządzenie brzegowe IoT przy użyciu programu Visual Studio (kod VS). Istnieje wiele sposobów wykonywania większości operacji w kodzie VS. W tym artykule wykorzystano Eksploratora, ale umożliwia także palety polecenia do uruchomienia większość czynności. 

## <a name="prerequisites"></a>Wymagania wstępne

* [Centrum IoT](../iot-hub/iot-hub-create-through-portal.md) w Twojej subskrypcji platformy Azure
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Rozszerzenie Azure IoT krawędzi](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) dla programu Visual Studio Code

## <a name="sign-in-to-access-your-iot-hub"></a>Zaloguj się do Centrum IoT

Rozszerzenia Azure IoT dla programu Visual Studio Code służy do wykonywania operacji z Centrum IoT. Do tych operacji do pracy należy zalogować się do konta platformy Azure i wybierz Centrum IoT, z którym pracujesz na.

1. Otwórz w programie Visual Studio Code, **Explorer** widoku.

2. W dolnej części Eksploratora, rozwiń węzeł **urządzeń Centrum IoT Azure** sekcji. 

   ![Rozwiń węzeł urządzenia Centrum IoT Azure](./media/how-to-register-device-vscode/azure-iot-hub-devices.png)

3. Polecenie **...**  w **urządzeń Centrum IoT Azure** nagłówek sekcji. Jeśli nie widzisz wielokropka, umieść kursor nad nagłówka. 

4. Wybierz **wybierz Centrum IoT**.

5. Jeśli użytkownik nie jest zalogowany do konta platformy Azure, postępuj zgodnie z monitami, aby to zrobić. 

6. Wybierz swoją subskrypcję platformy Azure. 

7. Wybierz Centrum IoT. 

## <a name="create-a-device"></a>Utwórz urządzenie

1. W Eksploratorze kodu programu VS, rozwiń węzeł **urządzeń Centrum IoT Azure** sekcji. 

2. Polecenie **...**  w **urządzeń Centrum IoT Azure** nagłówek sekcji. Jeśli nie widzisz wielokropka, umieść kursor nad nagłówka. 

3. Wybierz **tworzenia urządzenia IoT**. 

4. W polu tekstowym zapewniają urządzenia identyfikatora. 

Na ekranie danych wyjściowych widać wynik polecenia. Podane informacje o urządzeniu, w tym **deviceId** podane i **connectionString** można połączyć urządzenie fizyczne do Centrum IoT. 

## <a name="view-all-devices"></a>Wyświetl wszystkie urządzenia

Wszystkie urządzenia, podłączane do Centrum IoT są wymienione w **urządzeń Centrum IoT Azure** część programu Visual Studio Explorer kodu. Urządzenia brzegowe IoT różnią się od urządzeń z systemem innym niż krawędzi z inną ikonę i faktów, może być rozwijany pokazanie modułów wdrożyć do każdego urządzenia. 

   ![Wyświetl urządzenia w kodzie VS](./media/how-to-register-device-vscode/view-devices.png)

## <a name="retrieve-the-connection-string"></a>Parametry połączenia

Gdy wszystko jest gotowe skonfigurować urządzenie, należy parametry połączenia, która łączy urządzenie fizyczne z swoją tożsamość w Centrum IoT.

1. Kliknij prawym przyciskiem myszy na identyfikator urządzenia w **urządzeń Centrum IoT Azure** sekcji. 
2. Wybierz **skopiuj parametry połączenia urządzenia**.

   Ciąg połączenia jest kopiowany do Schowka. 

Możesz też wybrać **uzyskać informacje o urządzeniu** w menu kontekstowym, aby wyświetlić wszystkie informacje o urządzeniu, w tym ciągu połączenia w oknie danych wyjściowych. 


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [wdrożenie modułów na urządzeniu z kodem Visual Studio](how-to-deploy-modules-vscode.md).
