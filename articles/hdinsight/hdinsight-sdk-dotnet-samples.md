---
title: 'Azure HDInsight: przykłady dla platformy .NET'
description: Znajdź C# przykłady dla platformy .NET na potrzeby typowych zadań przy użyciu zestawu HDInsight SDK dla platformy .NET.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: sample
ms.date: 04/15/2019
ms.openlocfilehash: c1365b39385a3dfb684628214765269ea3a225e9
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076882"
---
# <a name="azure-hdinsight-net-samples"></a>Azure HDInsight: przykłady dla platformy .NET

> [!div class="op_single_selector"]
> * [Przykłady dla platformy .NET](hdinsight-sdk-dotnet-samples.md)
> * [Przykłady kodu Python](hdinsight-sdk-python-samples.md)
> * [Przykłady kodu Java](hdinsight-sdk-java-samples.md)
<!-- * [Go Examples](hdinsight-sdk-go-samples.md)-->

Ten artykuł zawiera:

* Linki do przykładów dla zadań tworzenia klastra.
* Linki do zawartości referencyjnej dla innych zadań zarządzania.

Możesz [aktywować korzyści subskrybenta programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): W ramach subskrypcji programu Visual Studio co miesiąc otrzymasz środki, które możesz przeznaczyć na płatne usługi platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [Zestaw SDK usługi Azure HDInsight dla platformy .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight#sdk-installation)

## <a name="cluster-management---creation"></a>Zarządzanie klastrem — tworzenie

* [Tworzenie klastra Kafka](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateKafkaClusterSample.cs)
* [Tworzenie klastra Spark](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateSparkClusterSample.cs)
* [Tworzenie klastra Spark przy użyciu Azure Data Lake Storage Gen2](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateHadoopClusterWithAdlsGen2Sample.cs)
* [Tworzenie klastra Spark z pakiet Enterprise Security (ESP)](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateEspClusterSample.cs)

Te przykłady dla platformy .NET można uzyskać, klonowanie repozytorium usługi [HDInsight-dotnet-SDK-Samples](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples) w witrynie GitHub.

[!INCLUDE [hdinsight-sdk-additional-functionality](../../includes/hdinsight-sdk-additional-functionality.md)]

Fragmenty kodu dla tej dodatkowej funkcji zestawu SDK znajdują się w [dokumentacji referencyjnej usługi HDINSIGHT SDK dla platformy .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet).
