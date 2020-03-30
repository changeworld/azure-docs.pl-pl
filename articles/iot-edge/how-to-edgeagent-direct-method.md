---
title: Wbudowane krawędzieWergentowe metody bezpośrednie — usługa Azure IoT Edge
description: Monitorowanie wdrożenia usługi IoT Edge i zarządzanie nim przy użyciu wbudowanych metod bezpośrednich w module środowiska uruchomieniowego agenta usługi IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 57b9d46918414cef9e8cbcffb941b98c98f985ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240341"
---
# <a name="communicate-with-edgeagent-using-built-in-direct-methods"></a>Komunikacja z krawędziąAgent przy użyciu wbudowanych metod bezpośrednich

Monitorowanie wdrożeń usługi IoT Edge i zarządzanie nimi przy użyciu metod bezpośrednich zawartych w module agenta usługi IoT Edge. Metody bezpośrednie są implementowane na urządzeniu, a następnie mogą być wywoływane z chmury. Agent usługi IoT Edge zawiera bezpośrednie metody, które ułatwiają zdalne monitorowanie urządzeń usługi IoT Edge i zarządzanie nimi.

Aby uzyskać więcej informacji na temat metod bezpośrednich, jak ich używać i jak je zaimplementować we własnych modułach, zobacz [Rozumienie i wywoływanie metod bezpośrednich z Usługi IoT Hub](../iot-hub/iot-hub-devguide-direct-methods.md).

Nazwy tych metod bezpośrednich są obsługiwane bez uwzględniania wielkości liter.

## <a name="ping"></a>Ping

Metoda **ping** jest przydatna do sprawdzania, czy funkcja IoT Edge jest uruchomiona na urządzeniu lub czy urządzenie ma otwarte połączenie z centrum IoT Hub. Ta metoda bezpośrednia służy do pingowania agenta IoT Edge i uzyskania jego stanu. Pomyślny ping zwraca pusty ładunek i **"status": 200**.

Przykład:

```azurecli
az iot hub invoke-module-method --method-name 'ping' -n <hub name> -d <device name> -m '$edgeAgent'
```

W witrynie Azure portal wywołać metodę `ping` z nazwą metody `{}`i pusty ładunek JSON .

![Wywoływanie bezpośredniego polecenia ping metody w witrynie Azure portal](./media/how-to-edgeagent-direct-method/ping-direct-method.png)

## <a name="restart-module"></a>Moduł ponownego uruchamiania

Metoda **RestartModule** umożliwia zdalne zarządzanie modułami działającymi na urządzeniu Usługi IoT Edge. Jeśli moduł zgłasza stan awarii lub inne nieprawidłowe zachowanie, można wyzwolić agenta IoT Edge, aby go ponownie uruchomić. Pomyślne polecenie ponownego uruchomienia zwraca pusty ładunek i **"stan": 200**.

Metoda RestartModule jest dostępna w wersji 1.0.9 i nowszej w uliczce IoT Edge. 

Metody direct RestartModule można użyć na dowolnym module uruchomionym na urządzeniu IoT Edge, w tym na samym module edgeAgent. Jednak jeśli używasz tej bezpośredniej metody, aby zamknąć edgeAgent, nie otrzymasz wynik sukcesu, ponieważ połączenie zostanie przerwane podczas ponownego uruchamiania modułu.

Przykład:

```azurecli
az iot hub invoke-module-method --method-name 'RestartModule' -n <hub name> -d <device name> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "id": "<module name>"
    }
'
```

W witrynie Azure portal wywołać metodę `RestartModule` o nazwie metody i następujące ładunku JSON:

```json
{
    "schemaVersion": "1.0",
    "id": "<module name>"
}
```

![Wywoływanie metody bezpośredniej "RestartModule" w witrynie Azure portal](./media/how-to-edgeagent-direct-method/restartmodule-direct-method.png)

## <a name="experimental-methods"></a>Metody eksperymentalne

Nowe opcje metody bezpośredniej są dostępne jako funkcje eksperymentalne do testowania, w tym:

* [UploadLogs](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md): Pobieranie dzienników modułów i przekazywanie ich do usługi Azure Blob Storage.
* [GetTaskStatus](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#gettaskstatus): Sprawdź stan żądania dziennika przekazywania.
* [GetLogs](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#getlogs): Pobieranie modułu loguje wbudowane w odpowiedzi metody bezpośredniej.

## <a name="next-steps"></a>Następne kroki

[Właściwości agenta usługi IoT Edge i bliźniaczy modułów koncentratora usługi IoT Edge](module-edgeagent-edgehub.md)
