---
title: Tryb failover i skalowanie aplikacji kontenerów w usłudze Service Fabric na platformie Azure | Microsoft Docs
description: Korzystając z tego samouczka, dowiesz się, jak obsługiwany jest tryb failover w aplikacji kontenerów usługi Azure Service Fabric.  Dowiesz się również, jak skalować kontenery i usługi działające w klastrze.
services: service-fabric
documentationcenter: ''
author: suhuruli
manager: chackdan
editor: suhuruli
tags: servicefabric
keywords: Docker, Containers, Microservices, Service Fabric, Azure
ms.assetid: ''
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 0a3edac7638d8b63086482cdecf8d74bde426c35
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58665631"
---
# <a name="tutorial-demonstrate-fail-over-and-scaling-of-container-services-with-service-fabric"></a>Samouczek: Pokaz niepowodzenia ponad i skalowania usług kontenera za pomocą usługi Service Fabric

Ten samouczek jest trzecią częścią serii. Korzystając z tego samouczka, dowiesz się, jak obsługiwany jest tryb failover w aplikacjach kontenera usługi Service Fabric. Poznasz również sposób skalowania kontenerów. W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Omówienie trybu failover kontenera w klastrze usługi Service Fabric
> * Skalowanie kontenerów frontonu internetowego w aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

Aplikacja omówiona w [części 2](service-fabric-tutorial-package-containers.md) działa w aktywnym klastrze usługi Service Fabric.

## <a name="fail-over-a-container-in-a-cluster"></a>Przenoszenie kontenera do trybu failover w klastrze

Usługa Service Fabric zapewnia, że wystąpienia kontenera są automatycznie przenoszone do innych węzłów w klastrze, jeśli wystąpi awaria. Można również ręcznie opróżnić węzeł z kontenerów, a następnie bezproblemowo przenieść je do innych węzłów w klastrze. Istnieje wiele sposobów skalowania usług — w tym przykładzie użyto narzędzia Service Fabric Explorer.

Aby przenieść kontener frontonu do trybu failover, wykonaj następujące czynności:

1. Otwórz narzędzie Service Fabric Explorer w klastrze — na przykład `http://lin4hjim3l4.westus.cloudapp.azure.com:19080`.
2. Kliknij węzeł **fabric:/TestContainer/azurevotefront** w widoku drzewa i rozwiń węzeł partycji (reprezentowany przez identyfikator GUID). Zwróć uwagę na nazwę węzła w widoku drzewa przedstawiającym węzły, w których obecnie uruchomiono kontener — na przykład `_nodetype_1`
3. Rozwiń węzeł **Węzły** w widoku drzewa. Kliknij wielokropek (trzy kropki) obok węzła działającego w kontenerze.
4. Wybierz pozycję **Uruchom ponownie**, aby ponownie uruchomić ten węzeł, i potwierdź akcję ponownego uruchomienia. Ponowne uruchomienie powoduje przeniesienie kontenera do trybu failover w innym węźle klastra.

![noderestart][noderestart]

Zwróć uwagę, że nazwa węzła (wskazująca, gdzie zostały uruchomione kontenery frontonu) zmienia się na nazwę innego węzła w klastrze. Po kilku chwilach powinno być możliwe ponowne przeglądanie w poszukiwaniu aplikacji, a aplikacja powinna zostać uruchomiona w innym węźle.

## <a name="scale-containers-and-services-in-a-cluster"></a>Skalowanie kontenerów i usług w klastrze

Kontenery Service Fabric można skalować w klastrze w celu uwzględnienia obciążenia w usługach. Skalowanie kontenera odbywa się przez zmienianie liczby wystąpień uruchomionych w klastrze.

Aby skalować fronton internetowy, wykonaj następujące czynności:

1. Otwórz narzędzie Service Fabric Explorer w klastrze — na przykład `http://lin4hjim3l4.westus.cloudapp.azure.com:19080`.
2. Kliknij wielokropek (trzy kropki) obok węzła **fabric:/TestContainer/azurevotefront** w widoku drzewa i wybierz pozycję **Skaluj usługę**.

![sfxscale][sfxscale]

Teraz możesz skalować liczbę wystąpień frontonu internetowego.

1. Zmień liczbę na **2** i kliknij pozycję **Skaluj usługę**.
1. Kliknij węzeł **fabric:/TestContainer/azurevotefront** w widoku drzewa i rozwiń węzeł partycji (reprezentowany przez identyfikator GUID).

![sfxscaledone][sfxscaledone]

Teraz możesz zobaczyć, że usługa ma dwa wystąpienia. W widoku drzewa widać, w których węzłach uruchomiono wystąpienia.

Wykonując to proste zadanie zarządzania, podwoiliśmy zasoby dostępne dla naszej usługi frontonu w celu przetworzenia obciążenia użytkownika. Ważne jest, aby zrozumieć, że nie musisz mieć wielu wystąpień usługi, aby działała ona niezawodnie. W przypadku niepowodzenia usługi usługa Service Fabric zapewnia, że nowe wystąpienie usługi jest uruchamiane w klastrze.

## <a name="next-steps"></a>Następne kroki

W tym samouczku zademonstrowano tryb failover kontenera oraz skalowanie aplikacji. Wykonano następujące czynności:

> [!div class="checklist"]
> * Omówienie trybu failover kontenera w klastrze usługi Service Fabric
> * Skalowanie kontenerów frontonu internetowego w aplikacji

W tej serii samouczków zawarto informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie obrazów kontenerów
> * Wypychanie obrazów kontenera do usługi Azure Container Registry
> * Tworzenie pakietów kontenerów dla usługi Service Fabric przy użyciu narzędzia Yeoman
> * Kompilowanie i uruchamianie aplikacji usługi Service Fabric z kontenerami
> * Sposób obsługi trybu failover i skalowania w usłudze Service Fabric

[noderestart]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialnoderestart.png
[sfxscale]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialscale.png
[sfxscaledone]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialscaledone.png