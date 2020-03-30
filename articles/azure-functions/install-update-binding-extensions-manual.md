---
title: Ręczne instalowanie lub aktualizowanie rozszerzeń powiązań usługi Azure Functions
description: Dowiedz się, jak zainstalować lub zaktualizować rozszerzenia powiązania usługi Azure Functions dla wdrożonych aplikacji funkcji.
ms.topic: reference
ms.date: 09/26/2018
ms.openlocfilehash: e8716f691a5d19ddac7fece47c423e1f7787b9db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75768864"
---
# <a name="manually-install-or-update-azure-functions-binding-extensions-from-the-portal"></a>Ręczne instalowanie lub aktualizowanie rozszerzeń powiązania usługi Azure Functions z portalu

Począwszy od wersji 2.x środowisko wykonawcze usługi Azure Functions używa rozszerzeń powiązania do implementacji kodu dla wyzwalaczy i powiązań. Rozszerzenia wiązania są dostarczane w pakietach NuGet. Aby zarejestrować rozszerzenie, zasadniczo zainstalować pakiet. Podczas tworzenia funkcji sposób instalowania rozszerzeń wiązania zależy od środowiska programistycznego. Aby uzyskać więcej informacji, zobacz [Rejestrowanie rozszerzeń powiązania](./functions-bindings-register.md) w artykule wyzwalacze i powiązania.

Czasami należy ręcznie zainstalować lub zaktualizować rozszerzenia powiązania w witrynie Azure portal. Na przykład może być konieczne zaktualizowanie zarejestrowanego powiązania do nowszej wersji. Może być również konieczne zarejestrowanie obsługiwanego powiązania, którego nie można zainstalować na karcie **Integruj** w portalu.

## <a name="install-a-binding-extension"></a>Instalowanie rozszerzenia wiązania

Poniższe kroki można wykonać, aby ręcznie zainstalować lub zaktualizować rozszerzenia z portalu.

1. W [witrynie Azure portal](https://portal.azure.com)znajdź aplikację funkcji i wybierz ją. Wybierz kartę **Przegląd** i wybierz pozycję **Zatrzymaj**.  Zatrzymanie aplikacji funkcji odblokowuje pliki, dzięki czemu można wywdziałć zmiany.

1. Wybierz kartę **Funkcje platformy** i w obszarze **Narzędzia programistyczne** wybierz pozycję **Zaawansowane narzędzia (Kudu).** Punkt końcowy Kudu`https://<APP_NAME>.scm.azurewebsites.net/`( ) jest otwierany w nowym oknie.

1. W oknie Kudu wybierz polecenie **Debug console** > **CMD**.  

1. W oknie polecenia przejdź `D:\home\site\wwwroot` do ikony usuwania i wybierz ją obok, `bin` aby usunąć folder. Wybierz **przycisk OK,** aby potwierdzić usunięcie.

1. Wybierz ikonę edycji `extensions.csproj` obok pliku, która definiuje rozszerzenia powiązania dla aplikacji funkcyjnej. Plik projektu jest otwierany w edytorze online.

1. W grupie **elementów**należy dokonać wymaganych dodatków i aktualizacji elementów **PackageReference,** a następnie wybierz pozycję **Zapisz**. Aktualną listę obsługiwanych wersji pakietów można znaleźć w artykule [Jakie pakiety potrzebuję?](https://github.com/Azure/azure-functions-host/wiki/Updating-your-function-app-extensions#what-nuget-packages-do-i-need) Wszystkie trzy powiązania usługi Azure Storage wymagają pakietu Microsoft.Azure.WebJobs.Extensions.Storage.

1. Z `wwwroot` folderu uruchom następujące polecenie, aby odbudować `bin` zestawy, do których istnieje odwołanie, w folderze.

    ```cmd
    dotnet build extensions.csproj -o bin --no-incremental --packages D:\home\.nuget
    ```

1. Na karcie **Przegląd** w portalu wybierz pozycję **Start,** aby ponownie uruchomić aplikację funkcyjną.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o wyzwalaczach i powiązaniach funkcji platformy Azure](functions-triggers-bindings.md)
