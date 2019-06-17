---
title: Ręczne instalowanie lub aktualizowanie rozszerzeń powiązania usługi Azure Functions
description: Dowiedz się, jak instalowanie lub aktualizowanie rozszerzeń powiązania usługi Azure Functions dla aplikacji wdrożonych funkcji.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Usługa Azure functions, funkcje i aktualizacje rozszerzeń NuGet, powiązań
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/26/2018
ms.author: glenga
ms.openlocfilehash: cda977ba59070c3ddaac05784277d6c0b5109f0f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61035750"
---
# <a name="manually-install-or-update-azure-functions-binding-extensions-from-the-portal"></a>Ręczne instalowanie lub aktualizowanie rozszerzeń powiązania usługi Azure Functions z poziomu portalu

Środowisko uruchomieniowe usługi Azure Functions w wersji 2.x używa rozszerzeń powiązania do implementacji kodu dla wyzwalaczy i powiązań. Rozszerzeń powiązania znajdują się w pakietach NuGet. Aby zarejestrować rozszerzenie, zasadniczo zainstalować pakiet. Podczas tworzenia funkcji, sposobem instalowania rozszerzeń powiązania zależy od środowiska projektowego. Aby uzyskać więcej informacji, zobacz [zarejestrować rozszerzeń powiązania](./functions-bindings-register.md) wyzwalaczy i powiązań artykułu.

Czasami zachodzi potrzeba ręcznego zainstalowania lub zaktualizowania rozszerzenia powiązania w witrynie Azure portal. Może na przykład może być konieczne zaktualizowanie zarejestrowanych powiązania do nowszej wersji. Może trzeba będzie również zarejestrować obsługiwane powiązanie, której nie można zainstalować w **integracja** karty w portalu.

## <a name="install-a-binding-extension"></a>Zainstaluj rozszerzenie powiązania

Wykonaj następujące kroki, aby ręcznie zainstalować lub zaktualizować rozszerzenia z portalu.

1. W [witryny Azure portal](https://portal.azure.com), Wyszukaj aplikację funkcji i wybierz ją. Wybierz **Przegląd** kartę, a następnie wybierz pozycję **zatrzymać**.  Zatrzymywanie aplikacji funkcji odblokowuje plików, tak, aby wprowadzić zmiany.

1. Wybierz **funkcje platformy** kartę i w obszarze **narzędzia programistyczne** wybierz **Narzędzia zaawansowane (Kudu)** . Punkt końcowy aparat Kudu (`https://<APP_NAME>.scm.azurewebsites.net/`) jest otwarty w nowym oknie.

1. W oknie narzędzia Kudu wybierz **konsoli debugowania** > **CMD**.  

1. W oknie wiersza polecenia przejdź do `D:\home\site\wwwroot` i wybierz ikonę Usuń `bin` można usunąć folderu. Wybierz **OK** o potwierdzenie usunięcia.

1. Wybierz ikonę edycji obok `extensions.csproj` pliku, który definiuje rozszerzeń powiązania dla aplikacji funkcji. Plik projektu jest otwarty w edytorze online.

1. Upewnij się, wymagane dodatki i aktualizacje **PackageReference** elementy w **ItemGroup**, a następnie wybierz **Zapisz**. Bieżącą listę pakietów obsługiwanych wersji można znaleźć w [pakietów, które są potrzebne?](https://github.com/Azure/azure-functions-host/wiki/Updating-your-function-app-extensions#what-nuget-packages-do-i-need) artykule o wiki. Wszystkie trzy powiązania magazynu platformy Azure wymagają pakietu Microsoft.Azure.WebJobs.Extensions.Storage.

1. Z `wwwroot` folder, uruchom następujące polecenie, aby odbudować zestawy występujące w odwołaniach w `bin` folderu.

    ```cmd
    dotnet build extensions.csproj -o bin --no-incremental --packages D:\home\.nuget
    ```

1. Ponownie **Przegląd** karta w portalu, wybierz **Start** ponowne uruchomienie aplikacji funkcji.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat usługi Azure functions, wyzwalaczami i powiązaniami](functions-triggers-bindings.md)
