---
title: Jak działa zdalne debugowanie kodu za pomocą usługi Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Zawiera opis procesów zdalnego debugowania w usłudze Azure Kubernetes za pomocą usługi Azure Dev Spaces
keywords: Miejsca deweloperów platformy Azure, przestrzenie deweloperów, platforma do dokowania, sieci Kubernetes, platforma Azure, usługa AKS, usługa Azure Kubernetes, kontenery
ms.openlocfilehash: a9f303ea6f954285a319be137a594f7ce127f740
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241402"
---
# <a name="how-remote-debugging-your-code-with-azure-dev-spaces-works"></a>Jak działa zdalne debugowanie kodu za pomocą usługi Azure Dev Spaces

Usługa Azure Dev Spaces oferuje wiele sposobów szybkiego iteracji i debugowania aplikacji Kubernetes oraz współpracy z zespołem nad klastrem usługi Azure Kubernetes (AKS). Gdy projekt jest uruchomiony w obszarze deweloperskim, usługa Azure Dev Spaces umożliwia dołączanie i debugowanie uruchomionej aplikacji w usłudze AKS.

W tym artykule opisano, jak działa zdalne debugowanie za pomocą miejsc deweloperskich.

## <a name="debug-your-code"></a>Debugowanie kodu

W przypadku aplikacji Java, .NET Core i Node.js można debugować aplikację uruchamiaącą bezpośrednio w przestrzeni deweloperskiej przy użyciu programu Visual Studio Code lub Visual Studio. Visual Studio Code i Visual Studio zapewniają narzędzia do łączenia się z przestrzenią deweloperów, uruchamiania aplikacji i dołączania debugera. Po `azds prep`uruchomieniu można otworzyć projekt w programie Visual Studio Code lub Visual Studio. Visual Studio Code lub Visual Studio wygeneruje własne `azds prep`pliki konfiguracyjne do łączenia, który jest oddzielony od uruchomionego . Z poziomu programu Visual Studio Code lub Visual Studio można ustawić punkty przerwania i uruchomić aplikację w przestrzeni deweloperów.

![Debugowanie kodu](media/get-started-node/debug-configuration-nodejs2.png)

Po uruchomieniu aplikacji przy użyciu programu Visual Studio Code lub Visual Studio do debugowania obsługują `azds up`one uruchamianie i łączenie się z przestrzenią deweloperów w taki sam sposób, jak uruchamianie. Ponadto narzędzia po stronie klienta w programie Visual Studio Code i Visual Studio zapewniają dodatkowy parametr z określonymi informacjami do debugowania. Parametr zawiera nazwę obrazu debugera, lokalizację debugera w obrazie debugera oraz lokalizację docelową w kontenerze aplikacji w celu zainstalowania folderu debugera.

Obraz debugera jest automatycznie określany przez narzędzia po stronie klienta. Używa metody podobnej do tej używanej podczas generowania wykresu `azds prep`Dockerfile i Helm podczas uruchamiania. Po zamontowaniu debugera na obrazie aplikacji `azds exec`jest on uruchamiany przy użyciu programu .

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć korzystanie z usługi Azure Dev Spaces do zdalnego debugowania projektu, zobacz następujące przewodniki Szybki start:

* [Szybka iteracji i debugowania z Visual Studio Code i Java][quickstart-java]
* [Szybko iterować i debugować za pomocą programu Visual Studio Code i .NET][quickstart-netcore]
* [Szybka iteracji i debugowania z Visual Studio Code i Node.js][quickstart-node]
* [Szybkie iterowanie i debugowanie za pomocą programów Visual Studio i .NET Core][quickstart-vs]


[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md
