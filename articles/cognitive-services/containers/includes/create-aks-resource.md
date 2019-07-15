---
title: Utwórz zasób klastra usługi Azure Kubernetes Service
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak utworzyć zasób usługi Azure Kubernetes Service (AKS).
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: ab7ce8b4a538e6a286a00285069a22878c5d88d9
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "67877460"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Utwórz zasób klastra usługi Azure Kubernetes Service

1. Przejdź do [usługi Azure Kubernetes Service](https://ms.portal.azure.com/#create/microsoft.aks) i wybierz **Utwórz**.

1. Na **podstawy** wprowadź następujące informacje:

    |Ustawienie|Wartość|
    |--|--|
    |Subscription|Wybierz odpowiednią subskrypcję|
    |Resource group|Wybierz grupę dostępnych zasobów|
    |Nazwa klastra Kubernetes|Wprowadź nazwę (małe litery)|
    |Region|Wybierz lokalizację w pobliżu|
    |Wersja programu Kubernetes|1.12.8 (ustawienie domyślne)|
    |Prefiks nazwy DNS|Tworzone automatycznie, ale możesz zastąpić|
    |Rozmiar węzła|Standardowa DS2, wersja 2:<br>`2 vCPUs`, `7 GB`|
    |Liczba węzłów|Pozostaw suwak na wartość domyślną|

1. Na **skalowania** kartę, pozostaw **węzłów wirtualnej** i **zestawów skalowania maszyn wirtualnych (wersja zapoznawcza)** ustawione wartości domyślne.
1. Na **uwierzytelniania** kartę, pozostaw **nazwy głównej usługi** i **Włącz RBAC** ustawione wartości domyślne.
1. Na **sieć** wprowadź następujące ustawienia:

    |Ustawienie|Value|
    |--|--|
    |Routing aplikacji protokołu HTTP|Nie|
    |Konfiguracja sieci|Podstawowa|

1. Na **monitorowanie** kartę, upewnij się, że **Włącz monitorowanie kontenera** ustawiono **tak**i pozostawić **obszaru roboczego usługi Log Analytics** jako Wartość domyślna.
1. Na **tagi** pozycję puste teraz pary nazwa/wartość.
1. Wybierz **Przejrzyj i Utwórz**.
1. Po pomyślnej weryfikacji, wybierz **Utwórz**.

> [!NOTE]
> Jeśli weryfikacja zakończy się niepowodzeniem, może to być ze względu na błąd "Usługa podmiotu zabezpieczeń". Wróć do **uwierzytelniania** kartę, a następnie z powrotem do **przeglądu + Utwórz**, gdy sprawdzanie poprawności i uruchamiania należy przekazać.
