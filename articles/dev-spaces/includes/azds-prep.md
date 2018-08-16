---
title: Plik dyrektywy include
description: Plik dyrektywy include
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 5ff41442d32739763332fa9bab59543ea3c7804a
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2018
ms.locfileid: "40129359"
---
## <a name="preparing-code-for-docker-and-kubernetes-development"></a>Przygotowywanie kodu do opracowywania na platformie Docker i Kubernetes
W tej chwili masz podstawową aplikację internetową, która działa lokalnie. Teraz konteneryzujesz ją, tworząc zasoby, które definiują kontener aplikacji i sposób jego wdrażania na platformie Kubernetes. To zadanie łatwo wykonać za pomocą usługi Azure Dev Spaces: 

1. Uruchom program VS Code i otwórz folder `webfrontend`. (Możesz zignorować wszystkie domyślne monity o dodanie zasobów debugowania lub przywrócenie projektu).
1. Otwórz zintegrowany terminal w programie VS Code (przy użyciu menu **Widok > Zintegrowany terminal**).
1. Uruchom następujące polecenie (upewnij się, że bieżący folder to **webfrontend**):

    ```cmd
    azds prep --public
    ```

Polecenie `azds prep` interfejsu wiersza polecenia platformy Azure generuje zasoby platformy Docker i Kubernetes z ustawieniami domyślnymi:
* Plik `./Dockerfile` opisuje obraz kontenera aplikacji oraz sposób kompilowania kodu źródłowego i uruchamiania go w kontenerze.
* [Pakiet Helm](https://docs.helm.sh) w folderze `./charts/webfrontend` opisuje, jak wdrożyć kontener na platformie Kubernetes.

Na razie nie trzeba rozumieć pełnej zawartości tych plików. Warto jednak wspomnieć, że **te same zasoby konfiguracji jako kodu platformy Kubernetes i Docker mogą być używane od etapu opracowywania po etap produkcyjny, co zapewnia większą spójność w różnych środowiskach.**
 
Polecenie `prep` generuje również plik o nazwie `./azds.yaml`. Jest to plik konfiguracji usługi Azure Dev Spaces. Uzupełnia on artefakty platformy Docker i Kubernetes o dodatkową konfigurację zapewniającą środowisko programowania przyrostowego na platformie Azure.
