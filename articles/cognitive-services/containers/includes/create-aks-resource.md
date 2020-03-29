---
title: Tworzenie zasobu klastra usługi Azure Kubernetes
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak utworzyć zasób usługi Azure Kubernetes (AKS).
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 4e3102912e88ef904fed3e680f8cdd23242b1f17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74383408"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Tworzenie zasobu klastra usługi Azure Kubernetes

1. Przejdź do [usługi Azure Kubernetes](https://ms.portal.azure.com/#create/microsoft.aks)i wybierz pozycję **Utwórz**.

1. Na karcie **Podstawy** wprowadź następujące informacje:

    |Ustawienie|Wartość|
    |--|--|
    |Subskrypcja|Wybierz odpowiednią subskrypcję.|
    |Grupa zasobów|Wybierz dostępną grupę zasobów.|
    |Nazwa klastra Kubernetes|Wprowadź nazwę (małe litery).|
    |Region|Wybierz pobliską lokalizację.|
    |Wersja Kubernetes|Niezależnie od wartości jest oznaczony jako **(domyślny)**.|
    |Prefiks nazwy DNS|Utworzony automatycznie, ale można zastąpić.|
    |Rozmiar węzła|Standard DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |Liczba węzłów|Pozostaw suwak przy wartości domyślnej.|

1. Na karcie **Skalowanie** pozostaw **wirtualne węzły** i **zestawy skalowania maszyny Wirtualnej** ustawione na ich wartości domyślne.
1. Na karcie **Uwierzytelnianie** pozostaw **jednostkę usługi** i **włącz rbac** ustawioną na ich wartości domyślne.
1. Na karcie **Sieć** wprowadź następujące opcje:

    |Ustawienie|Wartość|
    |--|--|
    |Routing aplikacji protokołu HTTP|Nie|
    |Konfiguracja sieci|Podstawowa (Basic)|

1. Na karcie **Monitorowanie** upewnij się, że **włącz monitorowanie kontenera** jest **ustawiona**na Tak i pozostaw **obszar roboczy usługi Log Analytics** jako wartość domyślną.
1. Na karcie **Znaczniki** pozostaw na razie puste pary nazw/wartości.
1. Wybierz **pozycję Recenzja i utwórz**.
1. Po przejściu weryfikacji wybierz pozycję **Utwórz**.

> [!NOTE]
> Jeśli sprawdzanie poprawności nie powiedzie się, może to być spowodowane błędem "Jednostki usługi". Wróć do karty **Uwierzytelnianie,** a następnie wróć do **pola Recenzja + utwórz**, gdzie sprawdzanie poprawności powinno zostać uruchomione, a następnie przekazać.
