---
title: Tworzenie zasobu klastra usługi Azure Kubernetes Service
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak utworzyć zasób usługi Azure Kubernetes Service (AKS).
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: 5e442f38a8e81170eeeac2f31f00a7d0eeb08d2b
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377442"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Tworzenie zasobu klastra usługi Azure Kubernetes Service

1. Przejdź do [usługi Azure Kubernetes](https://ms.portal.azure.com/#create/microsoft.aks)i wybierz pozycję **Utwórz**.

1. Na karcie **podstawowe** wprowadź następujące informacje:

    |Ustawienie|Value|
    |--|--|
    |Subscription|Wybierz odpowiednią subskrypcję.|
    |Resource group|Wybierz dostępną grupę zasobów.|
    |Nazwa klastra Kubernetes|Wprowadź nazwę (małe litery).|
    |Region|Wybierz lokalizację znajdującą się w pobliżu.|
    |Wersja programu Kubernetes|1.12.8 (domyślnie).|
    |Prefiks nazwy DNS|Tworzony automatycznie, ale można przesłonić.|
    |Rozmiar węzła|Standardowa DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |Liczba węzłów|Pozostaw suwak pod wartością domyślną.|

1. Na karcie **Skala** pozostaw wartości domyślne dla **węzłów wirtualnych** i **zestawów skalowania maszyn wirtualnych (wersja zapoznawcza)** .
1. Na karcie **uwierzytelnianie** pozostaw **nazwę główną usługi** i Włącz wartość domyślną **RBAC** .
1. Na karcie **Sieć** wprowadź następujące opcje:

    |Ustawienie|Value|
    |--|--|
    |Routing aplikacji protokołu HTTP|Nie|
    |Konfiguracja sieci|Podstawowa|

1. Na karcie **monitorowanie** upewnij się, że ustawienie **Włącz monitorowanie kontenerów** ma wartość **tak**, i pozostaw **log Analyticsą przestrzeń roboczą** .
1. Na karcie **Tagi** pozostaw teraz puste pary nazwa/wartość.
1. Wybierz pozycję **Przejrzyj i Utwórz**.
1. Po zakończeniu walidacji wybierz pozycję **Utwórz**.

> [!NOTE]
> Jeśli walidacja nie powiedzie się, może to być spowodowane błędem "Nazwa główna usługi". Wróć do karty **uwierzytelnianie** , a następnie wróć do pozycji **Przegląd + Utwórz**, gdzie Walidacja powinna zostać uruchomiona, a następnie zastąp.
