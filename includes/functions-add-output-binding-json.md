---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: b118da6b751bc7a1e29ceef10c91dc5e9e3659c2
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77474210"
---
Atrybuty powiązań są definiowane bezpośrednio w pliku Function. JSON. W zależności od typu powiązania mogą być wymagane dodatkowe właściwości. [Konfiguracja wyjściowa kolejki](../articles/azure-functions/functions-bindings-storage-queue-output.md#configuration) opisuje pola wymagane dla powiązania kolejki usługi Azure Storage. Rozszerzenie ułatwia dodawanie powiązań do pliku Function. JSON. 

Aby utworzyć powiązanie, kliknij prawym przyciskiem myszy (Ctrl + kliknięcie na macOS) plik `function.json` w folderze HttpTrigger i wybierz polecenie **Dodaj powiązanie...** . Postępuj zgodnie z monitami, aby zdefiniować następujące właściwości powiązań dla nowego powiązania:

| Monit | Wartość | Opis |
| -------- | ----- | ----------- |
| **Wybierz kierunek powiązania** | `out` | Powiązanie jest powiązaniem wyjściowym. |
| **Wybierz powiązanie z kierunkiem...** | `Azure Queue Storage` | Powiązanie to powiązanie kolejki usługi Azure Storage. |
| **Nazwa używana do identyfikowania tego powiązania w kodzie** | `msg` | Nazwa, która identyfikuje parametr powiązania przywoływany w kodzie. |
| **Kolejka, do której zostanie wysłany komunikat** | `outqueue` | Nazwa kolejki, w której ma zostać zapisywany powiązania. Gdy *Kolejka* nie istnieje, powiązanie tworzy je przy pierwszym użyciu. |
| **Wybierz ustawienie z pliku "Local. Setting. JSON"** | `AzureWebJobsStorage` | Nazwa ustawienia aplikacji, które zawiera parametry połączenia dla konta magazynu. Ustawienie `AzureWebJobsStorage` zawiera parametry połączenia dla konta magazynu utworzonego za pomocą aplikacji funkcji. |

Powiązanie jest dodawane do tablicy `bindings` w pliku Function. JSON, który powinien teraz wyglądać podobnie do poniższego przykładu:

```json
{
   ...

  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```