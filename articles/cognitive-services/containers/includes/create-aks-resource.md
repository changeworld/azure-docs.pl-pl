---
title: Tworzenie zasobu klastra usługi Azure Kubernetes Service
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak utworzyć zasób usługi Azure Kubernetes Service (AKS).
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 4e3102912e88ef904fed3e680f8cdd23242b1f17
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383408"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Tworzenie zasobu klastra usługi Azure Kubernetes Service

1. Przejdź do [usługi Azure Kubernetes](https://ms.portal.azure.com/#create/microsoft.aks)i wybierz pozycję **Utwórz**.

1. Na karcie **podstawowe** wprowadź następujące informacje:

    |Ustawienie|Wartość|
    |--|--|
    |Subskrypcja|Wybierz odpowiednią subskrypcję.|
    |Grupa zasobów|Wybierz dostępną grupę zasobów.|
    |Nazwa klastra Kubernetes|Wprowadź nazwę (małe litery).|
    |Region|Wybierz lokalizację znajdującą się w pobliżu.|
    |Wersja Kubernetes|Każda wartość jest oznaczona jako **(domyślnie)** .|
    |Prefiks nazwy DNS|Tworzony automatycznie, ale można przesłonić.|
    |Rozmiar węzła|Standardowa DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |Liczba węzłów|Pozostaw suwak pod wartością domyślną.|

1. Na karcie **Skala** pozostaw wartości domyślne **węzłów wirtualnych** i **zestawów skalowania maszyn** wirtualnych.
1. Na karcie **uwierzytelnianie** pozostaw pozycję Nazwa **główna usługi** i **Włącz opcję RBAC** ustawioną jako wartości domyślne.
1. Na karcie **Sieć** wprowadź następujące opcje:

    |Ustawienie|Wartość|
    |--|--|
    |Routing aplikacji protokołu HTTP|Nie|
    |Konfiguracja sieci|Podstawowa|

1. Na karcie **monitorowanie** upewnij się, że ustawienie **Włącz monitorowanie kontenerów** ma wartość **tak**, i pozostaw **log Analyticsą przestrzeń roboczą** .
1. Na karcie **Tagi** pozostaw teraz puste pary nazwa/wartość.
1. Wybierz pozycję **Przejrzyj i Utwórz**.
1. Po zakończeniu walidacji wybierz pozycję **Utwórz**.

> [!NOTE]
> Jeśli walidacja nie powiedzie się, może to być spowodowane błędem "Nazwa główna usługi". Wróć do karty **uwierzytelnianie** , a następnie wróć do pozycji **Przegląd + Utwórz**, gdzie Walidacja powinna zostać uruchomiona, a następnie zastąp.
