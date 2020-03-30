---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8530f4469a0c25f3c32e652e2b0752c51c28ff3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78191064"
---
Atrybuty powiązania są definiowane bezpośrednio w pliku function.json. W zależności od typu wiązania mogą być wymagane dodatkowe właściwości. [Konfiguracja danych wyjściowych kolejki](../articles/azure-functions/functions-bindings-storage-queue-output.md#configuration) opisuje pola wymagane dla powiązania kolejki usługi Azure Storage. Rozszerzenie ułatwia dodawanie powiązań do pliku function.json. 

Aby utworzyć powiązanie, kliknij prawym przyciskiem myszy (Ctrl+click on macOS) `function.json` plik w folderze HttpTrigger i wybierz polecenie Dodaj **powiązanie...**. Postępuj zgodnie z monitami, aby zdefiniować następujące właściwości powiązania dla nowego powiązania:

| Monit | Wartość | Opis |
| -------- | ----- | ----------- |
| **Wybierz kierunek wiązania** | `out` | Powiązanie jest powiązanie danych wyjściowych. |
| **Wybierz oprawę z kierunkiem...** | `Azure Queue Storage` | Powiązanie jest powiązaniem kolejki usługi Azure Storage. |
| **Nazwa używana do identyfikowania tego powiązania w kodzie** | `msg` | Nazwa identyfikująca parametr wiązania, do którego odwołuje się kod. |
| **Kolejka, do której zostanie wysłana wiadomość** | `outqueue` | Nazwa kolejki, do których pisze powiązanie. Gdy *queueName* nie istnieje, powiązanie tworzy go przy pierwszym użyciu. |
| **Wybierz ustawienie z "local.setting.json"** | `AzureWebJobsStorage` | Nazwa ustawienia aplikacji zawierającego parametry połączenia dla konta magazynu. Ustawienie `AzureWebJobsStorage` zawiera parametry połączenia dla konta Magazynu utworzonego za pomocą aplikacji funkcji. |

Powiązanie jest dodawane do `bindings` tablicy w function.json, który powinien wyglądać następująco:

:::code language="son" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="18-24":::