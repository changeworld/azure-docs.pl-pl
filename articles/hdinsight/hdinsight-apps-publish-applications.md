---
title: Publikowanie aplikacji usługi Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć aplikację usługi HDInsight, a następnie opublikować go w portalu Azure Marketplace.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 14aef891-7a37-4cf1-8f7d-ca923565c783
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: 9c538be4948a8e67ee9b94fcd6ff6bcea04438ef
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201710"
---
# <a name="publish-an-hdinsight-application-in-the-azure-marketplace"></a>Publikowanie aplikacji usługi HDInsight w portalu Azure Marketplace
Aplikacja Azure HDInsight można zainstalować w klastrze usługi HDInsight opartej na systemie Linux. W tym artykule Dowiedz się, jak opublikować aplikację usługi HDInsight w portalu Azure Marketplace. Aby uzyskać ogólne informacje o publikowaniu w portalu Azure Marketplace, zobacz [publikowanie oferty w portalu Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md).

Aplikacje usługi HDInsight korzystają *Bring Your Own License (BYOL)* modelu. W scenariuszu BYOL dostawcę aplikacji jest odpowiedzialny za Licencjonowanie aplikacji dla użytkowników aplikacji. Użytkownicy aplikacji są naliczane tylko dla zasobów platformy Azure, utworzonego przez siebie, takich jak klaster usługi HDInsight i maszyn wirtualnych klastra oraz węzłów. Obecnie rozliczenia dotyczące samej aplikacji nie występuje na platformie Azure.

Aby uzyskać więcej informacji zobacz następujące artykuły dotyczące aplikacji usługi HDInsight:

* [Instalowanie aplikacji usługi HDInsight](hdinsight-apps-install-applications.md). Dowiedz się, jak instalowanie aplikacji usługi HDInsight w klastrach.
* [Instalowanie niestandardowych aplikacji usługi HDInsight](hdinsight-apps-install-custom-applications.md). Dowiedz się, jak zainstalować i przetestować niestandardowych aplikacji usługi HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne
Aby przesłać niestandardową aplikację w witrynie Marketplace, najpierw [tworzenie i testowanie aplikacji niestandardowych](hdinsight-apps-install-custom-applications.md).

Należy również zarejestrować swoje konto dewelopera. Aby uzyskać więcej informacji, zobacz [publikowanie oferty w portalu Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md) i [Utwórz konto Microsoft Developer](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).

## <a name="define-the-application"></a>Definiowanie aplikacji
Dwa kroki są zaangażowane w publikowania aplikacji w witrynie Marketplace. Najpierw należy zdefiniować *createUiDef.json* pliku. Plik createUiDef.json wskazuje, które klastrów aplikacji jest zgodny z. Następnie należy opublikować szablon z portalu Azure. Oto przykładowy plik createUiDef.json:

```json
{
    "handler": "Microsoft.HDInsight",
    "version": "0.0.1-preview",
    "clusterFilters": {
        "types": ["Hadoop", "HBase", "Storm", "Spark"],
        "versions": ["3.6"]
    }
}
```

| Pole | Opis | Możliwe wartości |
| --- | --- | --- |
| types (typy) |Typy klastrów, z którymi dana aplikacja jest zgodna. |Hadoop, HBase, Storm, Spark (lub dowolna ich kombinacja) |
| versions (wersje) |Typy klastrów usługi HDInsight, z którymi dana aplikacja jest zgodna. |3.4 |

## <a name="application-installation-script"></a>Skrypt instalacji aplikacji
Gdy aplikacja jest zainstalowana w klastrze (lub istniejącego klastra, na nowy), jest tworzony węzeł krawędzi. Uruchamia skrypt instalacji aplikacji na węzła krawędzi.

  > [!IMPORTANT]
  > Nazwa skryptu instalacyjnego aplikacji musi być unikatowa dla określonego klastra. Nazwa skryptu musi mieć następujący format:
  > 
  > "Nazwa": "[concat ('hue-install-v0 ','-', uniquestring('applicationName')]"
  > 
  > Nazwa skryptu ma trzy części:
  > 
  > * Prefiks nazwy skryptu, który musi zawierać nazwę aplikacji lub nazwę związaną z aplikacją.
  > * Łącznik, aby można było czytać.
  > * Funkcję unikatowego ciągu z nazwą aplikacji podaną jako parametr.
  > 
  > Na liście akcji utrwalonych skryptów poprzednim przykładzie jest wyświetlany jako **hue-install-v0-4wkahss55hlas**. Zobacz [przykładowy ładunek JSON](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).
  > 

Skrypt instalacji musi mieć następujące właściwości:
* Skrypt jest idempotentności. Wiele wywołań skrypt tworzy ten sam rezultat.
* Skrypt jest prawidłowo numerów wersji. Użyj innej lokalizacji dla skryptu podczas uaktualniania i testowanie zmian. Dzięki temu, że klienci, którzy są instalowania aplikacji nie są wykorzystywanych przez aktualizacje lub testowania. 
* Skrypt ma odpowiednie rejestrowania w każdym punkcie. Zazwyczaj skryptu dzienniki są jedynym sposobem debugowania problemów z instalacją aplikacji.
* Wywołania usług zewnętrznych lub zasoby mają odpowiednie ponownych prób, więc instalacji nie ma wpływu na przejściowe problemy z siecią.
* Jeśli skrypt zostanie uruchomiony usługi na węzłach, usługi są monitorowane i skonfigurowane do automatycznego uruchamiania, jeśli występuje ponowny rozruch węzła.

## <a name="package-the-application"></a>Pakiet aplikacji
Utwórz plik zip, który zawiera wszystkie pliki, które są wymagane do zainstalowania aplikacji usługi HDInsight. Użyj pliku zip do [opublikować aplikację](#publish-application). Plik zip zawiera następujące pliki:

* [createUiDefinition.json](#define-application)
* mainTemplate.json (na przykład, zobacz [instalowanie niestandardowych aplikacji usługi HDInsight](hdinsight-apps-install-custom-applications.md).)
* Wszystkie wymagane skrypty

> [!NOTE]
> Pliki aplikacji (w tym pliki aplikacji sieci web) mogą być hostowane w dowolnym publicznie dostępnym punkcie końcowym.
> 

## <a name="publish-the-application"></a>Publikowanie aplikacji
Aby opublikować aplikację usługi HDInsight:

1. Zaloguj się do [Azure publikowanie](https://publish.windowsazure.com/).
2. W menu po lewej stronie wybierz **szablony rozwiązań**.
3. Wprowadź tytuł, a następnie wybierz **Utwórz nowy szablon rozwiązania**.
4. Jeśli nie zostało już zarejestrowane organizacji, wybierz **konto Centrum deweloperów Utwórz i Dołącz do programu Azure**.  Aby uzyskać więcej informacji, zobacz [Utwórz konto Microsoft Developer](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).
5. Wybierz **zdefiniować niektóre topologiami aby rozpocząć**. Szablon rozwiązania jest "nadrzędnej" do wszystkich jego topologii. Można zdefiniować wiele topologii w jednym szablonie oferty lub rozwiązania. Gdy oferta zostanie przeniesiona do etapu przemieszczania, spoczywa z jego topologii. 
6. Wprowadź nazwę topologii, a następnie wybierz **+**.
7. Wprowadź nową wersję, a następnie wybierz **+**.
8. Przekaż plik zip został utworzony, gdy użytkownik [spakowanej aplikacji](#package-application).  
9. Wybierz **żądanie certyfikacji**. Zespół certyfikacji Microsoft sprawdza pliki i oświadcza topologii.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [instalowanie aplikacji usługi HDInsight](hdinsight-apps-install-applications.md) w klastrach.
* Dowiedz się, jak [instalowanie niestandardowych aplikacji usługi HDInsight](hdinsight-apps-install-custom-applications.md) i wdrażania aplikacji usługi HDInsight nieopublikowane do usługi HDInsight.
* Dowiedz się, jak [umożliwia dostosowywanie klastrów usługi HDInsight opartych na systemie Linux akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md) i dodać więcej aplikacji. 
* Dowiedz się, jak [utworzyć klastry z opartą na systemie Linux platformą Hadoop w usłudze HDInsight przy użyciu szablonów usługi Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).
* Dowiedz się, jak [w usłudze HDInsight za pomocą węzła krawędzi pusty](hdinsight-apps-use-edge-node.md) dostępu w usłudze hdinsight, do testowania aplikacji usługi HDInsight i hostowania aplikacji usługi HDInsight.

