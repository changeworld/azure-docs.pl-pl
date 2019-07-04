---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/27/2018
ms.author: glenga
ms.openlocfilehash: f9b853f0e86fc298ab35421928492ba813d85827
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444622"
---
## <a name="create-an-azure-functions-project"></a>Tworzenie projektu usługi Functions przy użyciu funkcji 

Szablon projektu usługi Azure Functions w programie Visual Studio Code umożliwia utworzenie projektu, który można opublikować w aplikacji funkcji na platformie Azure. Aplikacja funkcji umożliwia grupowanie funkcji w jednostki logiczne, co ułatwia wdrażanie i udostępnianie zasobów oraz zarządzanie nimi.

1. W programie Visual Studio Code naciśnij klawisz F1, aby otworzyć paletę poleceń. W palecie poleceń, wyszukiwanie i wybieranie `Azure Functions: Create new project...`.

1. Wybierz lokalizację katalogu dla obszaru roboczego projektu, a następnie wybierz **wybierz**.

    > [!NOTE]
    > Te kroki są przeznaczone do można wykonać poza obszarem roboczym. W takim przypadku nie wybieraj folderu projektu, który jest częścią obszaru roboczego.

1. Postępuj zgodnie z monitami Podaj następujące informacje:

    | Monit | Wartość | Opis |
    | ------ | ----- | ----------- |
    | Wybierz język, projekt aplikacji funkcji | C# lub JavaScript | W tym artykule obsługuje C# i JavaScript. Dla języka Python, zobacz [w tym artykule Python](https://code.visualstudio.com/docs/python/tutorial-azure-functions)i dla programu PowerShell, zobacz [w tym artykule PowerShell](../articles/azure-functions/functions-create-first-function-powershell.md).  |
    | Wybierz szablon projektu pierwszej funkcji | Wyzwalacz HTTP | Tworzenie funkcji wyzwalanej przez protokół HTTP w nowej aplikacji funkcji. |
    | Podaj nazwę funkcji | HttpTrigger | Naciśnij klawisz Enter, aby użyć nazwy domyślnej. |
    | Podaj przestrzeń nazw | My.Functions | (C# tylko) C# bibliotek klas musi mieć przestrzeni nazw.  |
    | Poziom autoryzacji | Funkcja | Wymaga [funkcyjne](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) wywołać punkt końcowy HTTP przez funkcję. |
    | Wybierz, jak można otworzyć projektu | Dodaj do obszaru roboczego | Tworzy aplikację funkcji w bieżącym obszarze roboczym. |

Program Visual Studio Code utworzy projekt aplikacji funkcji w nowym obszarze roboczym. Ten projekt zawiera pliki konfiguracyjne [host.json](../articles/azure-functions/functions-host-json.md) i [local.settings.json](../articles/azure-functions/functions-run-local.md#local-settings-file), a także wszystkie specyficzne dla języka pliki projektu. 

Nową funkcję wyzwalaną przez protokół HTTP jest tworzona w folderze HttpTrigger projektu aplikacji funkcji.