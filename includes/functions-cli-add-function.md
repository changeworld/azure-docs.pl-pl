---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: a33cc604d45d51a63a73ea6ed58abc67269437d7
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673146"
---
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
Dodaj funkcję do projektu za pomocą następującego `--name` polecenia, gdzie argument jest unikatową nazwą funkcji `--template` (HttpExample), a argument określa wyzwalacz funkcji (HTTP). 

```
func new --name HttpExample --template "HTTP trigger"
```  
::: zone-end  
::: zone pivot="programming-language-csharp"
`func new`tworzy plik kodu HttpExample.cs.
::: zone-end
::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
`func new`tworzy podfolder pasujący do nazwy funkcji, która zawiera plik kodu odpowiedni do wybranego języka projektu i plik konfiguracyjny o nazwie *function.json*.
::: zone-end