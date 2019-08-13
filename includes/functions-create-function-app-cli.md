---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/05/2019
ms.author: glenga
ms.openlocfilehash: c90587ebd7b0a608e41f19ab600f82dc484bad0a
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "68949933"
---
## <a name="create-the-local-function-app-project"></a>Tworzenie lokalnego projektu aplikacji funkcji

Uruchom następujące polecenie w wierszu polecenia, aby utworzyć projekt aplikacji funkcji w folderze `MyFunctionProj` bieżącego katalogu lokalnego. Ponadto w folderze `MyFunctionProj` jest tworzone repozytorium GitHub.

```command
func init MyFunctionProj
```

Po wyświetleniu monitu wybierz środowisko uruchomieniowe procesu roboczego z następujących opcji języka:

+ `dotnet`: tworzy projekt biblioteki klas platformy .NET (csproj).
+ `node`: tworzy projekt oparty na języku Node. js. `javascript` Wybierz lub .`typescript` 
+ `python`: w przypadku projektu w języku Python należy zamiast tego ukończyć opcję [Utwórz funkcję wyzwalaną przez protokół http na platformie Azure](../articles/azure-functions/functions-create-first-function-python.md).
+ `powershell`: w przypadku projektu programu PowerShell należy [utworzyć pierwszą funkcję programu PowerShell na platformie Azure](../articles/azure-functions/functions-create-first-function-powershell.md). 


Po utworzeniu projektu Użyj następującego polecenia, aby przejść do folderu nowego `MyFunctionProj` projektu.

```command
cd MyFunctionProj
```
