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
ms.openlocfilehash: d44f33b0e9f71c1d8d6e2c9878b08f9fa0e1f8a1
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938176"
---
## <a name="preparing-code-for-docker-and-kubernetes-development"></a>Przygotowywanie kodu dla rozwoju Docker i Kubernetes
Do tej pory masz aplikacji sieci web podstawowego, który można uruchomić lokalnie. Będzie teraz containerize tworząc zasoby definiujące kontenera i jak będą wdrażać do Kubernetes aplikacji. Zadanie to łatwo zrobić spacjami deweloperów platformy Azure: 

1. Uruchom kodzie VS i otworzyć `webfrontend` folderu. (Możesz zignorować żadnych monitów domyślne, aby dodać zasoby debugowania lub przywrócić projekt).
1. Otwórz Terminal zintegrowane w kodzie VS (przy użyciu **Widok > zintegrowane Terminal** menu).
1. Uruchom to polecenie (Upewnij się, że **webfrontend** jest bieżący folder):

    ```cmd
    azds prep --public
    ```

Azure CLI `azds prep` polecenie generuje Docker i Kubernetes zasoby z ustawieniami domyślnymi:
* `./Dockerfile` Zawiera opis aplikacji kontenera obrazu oraz sposób kod źródłowy jest wbudowana i jest uruchamiany w ramach kontenera.
* A [wykresu Helm](https://docs.helm.sh) w obszarze `./charts/webfrontend` opisano, jak wdrożyć Kubernetes kontenera.

Obecnie nie trzeba zrozumieć pełną treść tych plików. Zaleca się wskazanie, jednak, że **te same Kubernetes i Docker zasobów konfiguracji jako kodu pozwala od projektowania do produkcji, zapewniając lepsze spójności w różnych środowiskach.**
 
Plik o nazwie `./azds.yaml` również jest generowany przez `prep` polecenia, a ta jest pliku konfiguracyjnego spacje deweloperów platformy Azure. Uzupełnia on Docker i Kubernetes artefaktów z dodatkowych konfiguracji, zapewniającym środowisko programistyczne iteracyjne na platformie Azure.
