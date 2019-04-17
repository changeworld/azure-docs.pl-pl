---
title: Zmiany klastra dostęp do konfiguracji — Azure HDInsight
description: Dowiedz się więcej o zmianach wprowadzonych do uzyskiwania dostępu do pola konfiguracji klastra poufnych.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: 85a6737ee111975d4c7ecf078673280127bfd0fd
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2019
ms.locfileid: "59610245"
---
# <a name="changes-to-cluster-configuration-access"></a>Zmiany dostępu do konfiguracji klastra

Najnowsza wersja zestawu SDK usługi Azure HDInsight zapewnia pewne ważne zmiany do obsługi więcej szczegółową kontrolę dostępu opartej na rolach do uzyskania informacji poufnych. Jako część tych zmian, niektóre **akcji mogą być wymagane** korzystając z jednej z metod, których to dotyczy.

## <a name="sdk-for-net-500"></a>Zestaw SDK dla platformy .NET 5.0.0

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) będzie **już nie zwracać parametry poufnych** , np. klucze magazynu (lokacja) lub poświadczeń HTTP (bramy).
    - Aby pobrać wszystkie konfiguracje, w tym liter parametrów, należy użyć `ConfigurationOperationsExtensions.List` przyszłości.  Należy pamiętać, że użytkownicy z rolą "Reader" nie będzie można użyć tej metody. To zapewnia precyzyjną kontrolę nad tym, którzy użytkownicy mają dostęp do poufnych informacji do klastra. 
    - Aby pobrać tylko poświadczeń bramy protokołu HTTP, użyj `ClusterOperationsExtensions.GetGatewaySettings`. 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) jest już przestarzały i został zastąpiony przez `ClusterOperationsExtensions.UpdateGatewaySettings`. 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) i [ `DisableHttp` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) obecnie są przestarzałe. HTTP są teraz zawsze włączone, więc te metody nie są już potrzebne.