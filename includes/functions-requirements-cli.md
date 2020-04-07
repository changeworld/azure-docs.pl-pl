---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: a84f0a92703d1b626710cfc4dcfa2820bc58bda6
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673197"
---
## <a name="configure-your-local-environment"></a>Konfigurowanie środowiska lokalnego

Przed rozpoczęciem należy mieć następujące właściwości:

+ Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell"  
+ [Podstawowe narzędzia azure functions](../articles/azure-functions/functions-run-local.md#v2) w wersji 2.7.1846 lub nowszej wersji 2.x.
::: zone-end  
::: zone pivot="programming-language-python"
+ Język Python 3.6 i 3.7 wymagają [narzędzia Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) w wersji 2.7.1846 lub nowszej wersji 2.x. Python 3.8 wymaga [wersji 3.x](../articles/azure-functions/functions-run-local.md#v2) podstawowych narzędzi.
::: zone-end

+ Interfejsu [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w wersji 2.0.76 lub nowszej. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/), Active LTS i konserwacja lts wersje (8.11.1 i 10.14.1 zalecane).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3.8 (64-bit)](https://www.python.org/downloads/release/python-382/), [Python 3.7 (64-bit)](https://www.python.org/downloads/release/python-375/), [Python 3.6 (64-bit)](https://www.python.org/downloads/release/python-368/), które są obsługiwane przez usługi Azure Functions. 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [Program PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ .NET [Core SDK 2.2+](https://www.microsoft.com/net/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ [Zestaw Java Developer Kit](https://aka.ms/azure-jdks), wersja 8.

+ [Apache Maven](https://maven.apache.org), wersja 3.0 lub wyższa.

> [!IMPORTANT]
> Aby wykonać wszystkie czynności opisane w tym przewodniku Szybki start, dla zmiennej środowiskowej JAVA_HOME należy ustawić lokalizację instalacji zestawu JDK.
::: zone-end