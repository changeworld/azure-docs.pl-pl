---
title: Publikowanie aplikacji usługi Azure HDInsight
description: Dowiedz się, jak utworzyć aplikację HDInsight, a następnie opublikować ją w portalu Azure Marketplace.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: e64bf253a73df3a2f8170109dc1dfb9a59613733
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "64685325"
---
# <a name="publish-an-hdinsight-application-in-the-azure-marketplace"></a>Publikowanie aplikacji USŁUGI HDInsight w portalu Azure Marketplace
Aplikację usługi Azure HDInsight można zainstalować w klastrze HDInsight opartym na systemie Linux. W tym artykule dowiesz się, jak opublikować aplikację HDInsight w portalu Azure Marketplace. Aby uzyskać ogólne informacje dotyczące publikowania w portalu Azure Marketplace, zobacz [Publikowanie oferty w portalu Azure Marketplace.](../marketplace/marketplace-publishers-guide.md)

Aplikacje HDInsight korzystają z modelu *Bring Your Own License (BYOL).* W scenariuszu BYOL dostawca aplikacji jest odpowiedzialny za licencjonowanie aplikacji do użytkowników aplikacji. Użytkownicy aplikacji są naliczane tylko za zasoby platformy Azure, które tworzą, takie jak klaster HDInsight oraz maszyny wirtualne i węzły klastra. Obecnie rozliczenia za samą aplikację nie występuje na platformie Azure.

Aby uzyskać więcej informacji, zobacz te artykuły związane z aplikacjami HDInsight:

* [Zainstaluj aplikacje HDInsight](hdinsight-apps-install-applications.md). Dowiedz się, jak zainstalować aplikację HDInsight w klastrach.
* [Zainstaluj niestandardowe aplikacje HDInsight](hdinsight-apps-install-custom-applications.md). Dowiedz się, jak zainstalować i przetestować niestandardowe aplikacje HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne
Aby przesłać aplikację niestandardową w portalu Marketplace, najpierw [utwórz i przetestuj aplikację niestandardową](hdinsight-apps-install-custom-applications.md).

Musisz również zarejestrować swoje konto dewelopera. Aby uzyskać więcej informacji, zobacz [Publikowanie oferty w portalu Azure Marketplace](../marketplace/marketplace-publishers-guide.md) i tworzenie konta microsoft [developer](../marketplace/marketplace-publishers-guide.md).

## <a name="define-the-application"></a>Zdefiniuj aplikację
Dwa kroki są zaangażowane w publikowanie aplikacji w portalu Marketplace. Najpierw zdefiniuj plik *createUiDef.json.* Plik createUiDef.json wskazuje, z którymi klastrami aplikacja jest zgodna. Następnie opublikuj szablon z witryny Azure portal. Oto przykładowy plik createUiDef.json:

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
Gdy aplikacja jest zainstalowana w klastrze (w istniejącym klastrze lub na nowym) tworzony jest węzeł brzegowy. Skrypt instalacji aplikacji jest uruchamiany w węźle brzegu.

  > [!IMPORTANT]  
  > Nazwa skryptu instalacji aplikacji musi być unikatowa dla określonego klastra. Nazwa skryptu musi mieć następujący format:
  > 
  > "name": "[concat('hue-install-v0','-' ,uniquestring('applicationName')]"
  > 
  > Nazwa skryptu ma trzy części:
  > 
  > * Prefiks nazwy skryptu, który musi zawierać nazwę aplikacji lub nazwę odpowiednią dla aplikacji.
  > * Łącznik, dla czytelności.
  > * Unikatowa funkcja ciągu, z nazwą aplikacji jako parametrem.
  > 
  > Na liście akcji utrwalonych skryptów poprzedni przykład jest wyświetlany jako **hue-install-v0-4wkahss55hlas**. Zobacz [przykładowy ładunek JSON](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).
  > 

Skrypt instalacyjny musi mieć następujące właściwości:
* Skrypt jest idempotentny. Wiele wywołań do skryptu dają ten sam wynik.
* Skrypt jest poprawnie wersjona. Użyj innej lokalizacji dla skryptu podczas uaktualniania lub testowania zmian. Gwarantuje to, że klienci, którzy instalują aplikację, nie są na nie dotyczy aktualizacje lub testowanie. 
* Skrypt ma odpowiednie rejestrowanie w każdym punkcie. Zazwyczaj dzienniki skryptów są jedynym sposobem debugowania problemów z instalacją aplikacji.
* Wywołania do usług zewnętrznych lub zasobów mają odpowiednie ponownych prób, tak aby instalacja nie ma wpływu na przejściowe problemy z siecią.
* Jeśli skrypt uruchamia usługi w węzłach, usługi są monitorowane i skonfigurowane do automatycznego uruchamiania w przypadku ponownego uruchomienia węzła.

## <a name="package-the-application"></a>Zapakuj aplikację
Utwórz plik zip zawierający wszystkie pliki wymagane do zainstalowania aplikacji HDInsight. Do opublikowania aplikacji użyj pliku zip. Plik zip zawiera następujące pliki:

* tworzenieUiDedefdefition.json
* mainTemplate.json (Przykład można znaleźć w [2005 r.)](hdinsight-apps-install-custom-applications.md)
* Wszystkie wymagane skrypty

> [!NOTE]  
> Pliki aplikacji (w tym wszystkie pliki aplikacji sieci web) można hostować w dowolnym publicznie dostępnym punkcie końcowym.

## <a name="publish-the-application"></a>Publikowanie aplikacji
Aby opublikować aplikację HDInsight:

1. Zaloguj się do [publikowania na platformie Azure](https://publish.windowsazure.com/).
2. W menu po lewej stronie wybierz pozycję **Szablony rozwiązań**.
3. Wprowadź tytuł, a następnie wybierz pozycję **Utwórz nowy szablon rozwiązania**.
4. Jeśli organizacja nie została jeszcze zarejestrowana, wybierz pozycję **Utwórz konto Centrum deweloperów i dołącz do programu platformy Azure**.  Aby uzyskać więcej informacji, zobacz [Tworzenie konta microsoft developer](../marketplace/marketplace-publishers-guide.md).
5. Wybierz **definiuj niektóre topologie, aby rozpocząć**. Szablon rozwiązania jest "nadrzędnym" dla wszystkich jego topologii. Można zdefiniować wiele topologii w jednej ofercie lub szablonie rozwiązania. Gdy oferta jest wypychany do inscenizacji, jest wypychany ze wszystkimi jego topologie. 
6. Wprowadź nazwę topologii, a **+** następnie wybierz opcję .
7. Wprowadź nową wersję, a **+** następnie wybierz opcję .
8. Przekaż plik zip utworzony podczas pakowania aplikacji.  
9. Wybierz pozycję **Poproś o certyfikację**. Zespół certyfikacyjny firmy Microsoft przegląda pliki i certyfikuje topologię.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [zainstalować aplikacje HDInsight](hdinsight-apps-install-applications.md) w klastrach.
* Dowiedz się, jak [zainstalować niestandardowe aplikacje HDInsight](hdinsight-apps-install-custom-applications.md) i wdrożyć nieopublikowane aplikacje HDInsight w udojaszu HD.
* Dowiedz się, jak [używać akcji skryptów do dostosowywania klastrów HDInsight opartych na systemie Linux](hdinsight-hadoop-customize-cluster-linux.md) i dodawania większej liczby aplikacji. 
* Dowiedz się, jak [tworzyć klastry Apache Hadoop oparte na systemie Linux w usłudze HDInsight przy użyciu szablonów usługi Azure Resource Manager.](hdinsight-hadoop-create-linux-clusters-arm-templates.md)
* Dowiedz się, jak [używać pustego węzła krawędzi w programie HDInsight](hdinsight-apps-use-edge-node.md) do uzyskiwania dostępu do klastrów HDInsight, testowania aplikacji HDInsight i hostowania aplikacji HDInsight.

