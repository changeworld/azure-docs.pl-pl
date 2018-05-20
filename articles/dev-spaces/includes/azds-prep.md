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
ms.openlocfilehash: e9f97f804985f948e5442c64a31d95e7931b03cd
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
## <a name="preparing-code-for-docker-and-kubernetes-development"></a>Przygotowywanie kodu dla rozwoju Docker i Kubernetes
Do tej pory masz aplikacji sieci web podstawowego, który można uruchomić lokalnie. Będzie teraz containerize tworząc zasoby definiujące kontenera i jak będą wdrażać do Kubernetes aplikacji. Jest to łatwo zrobić spacjami deweloperów platformy Azure: 

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
 
Plik o nazwie `./azds.yaml` również jest generowany przez `prep` polecenia, a ta jest pliku konfiguracyjnego spacje deweloperów platformy Azure. Uzupełnia on Docker i Kubernetes artefaktów z dodatkowych konfiguracji, zapewniającym środowisko programistyczne iteracyjne na platformie Azure. Na przykład wykres Helm domyślny nie ujawnia żadnych publicznych punktów końcowych. Czasami jednak warto tymczasowo otwarcie publicznego punktu końcowego podczas tworzenia, aby można było sprawdzić kodu, powiedz, z urządzeniem przenośnym lub adres URL elementu webhook. Utworzone przy użyciu pliku azds.yaml `azds prep --public` zastępuje Helm domyślne parametry do udostępnienia publiczny punkt końcowy dla rozwoju tylko raz.
