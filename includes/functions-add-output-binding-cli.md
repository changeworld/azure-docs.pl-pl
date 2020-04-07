---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 121f6ffa5c1a7c903e59be8a5bc3e1e1db0834fc
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673344"
---
## <a name="add-an-output-binding-definition-to-the-function"></a>Dodawanie definicji wiązania wyjściowego do funkcji

Chociaż funkcja może mieć tylko jeden wyzwalacz, może mieć wiele powiązań danych wejściowych i wyjściowych, które umożliwiają łączenie się z innymi usługami i zasobami platformy Azure bez pisania niestandardowego kodu integracji. 

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Deklarujesz te powiązania w pliku *function.json* w folderze funkcji. Z poprzedniego przewodnika Szybki start plik *function.json* w folderze *HttpExample* zawiera dwa powiązania w `bindings` kolekcji:  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-python"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-powershell"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json" range="2-18":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript, programming-language-powershell, programming-language-typescript"  
Każde powiązanie ma co najmniej typ, kierunek i nazwę. W powyższym przykładzie pierwsze powiązanie jest typu `httpTrigger` z kierunkiem `in`. Dla `in` kierunku `name` określa nazwę parametru wejściowego, który jest wysyłany do funkcji, gdy wywoływane przez wyzwalacz.  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
Drugie powiązanie w kolekcji nosi nazwę `res`. To `http` powiązanie jest`out`powiązaniem danych wyjściowych ( ), który jest używany do zapisu odpowiedzi HTTP. 

Aby zapisać do kolejki usługi Azure Storage `out` z `queue` tej funkcji, należy dodać powiązanie typu o nazwie `msg`, jak pokazano w poniższym kodzie:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
Drugie powiązanie w kolekcji `http` jest `out`typu z direction `name` , `$return` w którym to przypadku specjalne wskazuje, że to powiązanie używa funkcji zwraca wartość, a nie dostarczanie parametru wejściowego.

Aby zapisać do kolejki usługi Azure Storage `out` z `queue` tej funkcji, należy dodać powiązanie typu o nazwie `msg`, jak pokazano w poniższym kodzie:

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
Drugie powiązanie w kolekcji nosi nazwę `res`. To `http` powiązanie jest`out`powiązaniem danych wyjściowych ( ), który jest używany do zapisu odpowiedzi HTTP. 

Aby zapisać do kolejki usługi Azure Storage `out` z `queue` tej funkcji, należy dodać powiązanie typu o nazwie `msg`, jak pokazano w poniższym kodzie:

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
W takim `msg` przypadku jest nadana funkcji jako argument wyjściowy. W `queue` przypadku typu należy również określić nazwę `queueName` kolejki i podać *nazwę* połączenia usługi Azure Storage (z `connection` *local.settings.json*) w pliku . 
::: zone-end  
