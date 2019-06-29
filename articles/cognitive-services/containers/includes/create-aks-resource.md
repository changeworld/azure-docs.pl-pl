---
title: Obsługa kontenerów
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak utworzyć kubernetes usługi azure resource (AKS).
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 1d68c08f6dfca74c38973af1686d614f3f10cc28
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455095"
---
## <a name="create-an-azure-kubernetes-service-aks-cluster-resource"></a>Utwórz zasób klastra usługi Azure Kubernetes Service (AKS)

1. Przejdź do [tworzenie](https://ms.portal.azure.com/#create/microsoft.aks) usługi Kubernetes.

1. Na **podstawy** wprowadź następujące informacje:

    |Ustawienie|Wartość|
    |--|--|
    |Subskrypcja|Wybierz odpowiednią subskrypcję.|
    |Grupa zasobów|Wybierz grupę dostępnych zasobów|
    |Nazwa klastra Kubernetes|Nazwy żądanej (małe litery)|
    |Region|Wybierz lokalizację w pobliżu|
    |Wersja programu Kubernetes|1.12.8 (ustawienie domyślne)|
    |Prefiks nazwy DNS|Tworzone automatycznie, ale Opcjonalnie można przesłonić|
    |Rozmiar węzła|Standardowa DS2, wersja 2:<br>`2 vCPUs`, `7 GB`|
    |Liczba węzłów|Pozostaw suwak na wartość domyślną|

1. Na **skalowania** kartę, pozostaw *wirtualnych węzłów* i *zestawy skalowania maszyn wirtualnych* wartości domyślne.
1. Na **uwierzytelniania** kartę, pozostaw *nazwy głównej usługi* i *Włącz RBAC* wartości domyślne.
1. Na **sieć** wprowadź następujące ustawienia:

    |Ustawienie|Wartość|
    |--|--|
    |Routing aplikacji protokołu HTTP|Nie|
    |Konfiguracja sieci|Podstawowa|

1. Na **monitorowanie** kartę, upewnij się, że *Włącz monitorowanie kontenera* ustawiono **tak** i pozostawić *obszaru roboczego usługi Log Analytics* jako jego Wartość domyślna
1. Na **tagi** pozycję puste teraz pary nazwa/wartość.
1. Kliknij przycisk **Przejrzyj i Utwórz**.
1. Po pomyślnej weryfikacji kliknij przycisk **Utwórz**.

> [!NOTE]
> Jeśli weryfikacja zakończy się niepowodzeniem, może być termin *nazwy głównej usługi* błędu. Przejdź z powrotem do *uwierzytelniania* kartę, a następnie z powrotem do *przeglądu + Utwórz* gdzie weryfikacja powinna wykonać, a następnie przekaż.
