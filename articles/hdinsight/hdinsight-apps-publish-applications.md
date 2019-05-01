---
title: Publikowanie aplikacji usługi Azure HDInsight
description: Dowiedz się, jak utworzyć aplikację HDInsight, a następnie opublikuj go w witrynie Azure Marketplace.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: e64bf253a73df3a2f8170109dc1dfb9a59613733
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64685325"
---
# <a name="publish-an-hdinsight-application-in-the-azure-marketplace"></a>Publikowanie aplikacji HDInsight w witrynie Azure Marketplace
Aplikacja usługi Azure HDInsight można zainstalować w klastrze HDInsight opartych na systemie Linux. Ten artykuł zawiera informacje o sposobie publikowania aplikacji HDInsight w portalu Azure Marketplace. Aby uzyskać ogólne informacje o funkcji publikowania w witrynie Azure Marketplace, zobacz [publikowanie oferty w portalu Azure Marketplace](../marketplace/marketplace-publishers-guide.md).

Użyj aplikacji HDInsight *Bring Your Own License (BYOL)* modelu. W przypadku scenariusza BYOL dostawcę aplikacji jest odpowiedzialny za Licencjonowanie aplikacji dla użytkowników aplikacji. Użytkownicy aplikacji jest naliczana tylko dla zasobów platformy Azure, utworzonego przez siebie, takie jak klaster HDInsight i maszyny wirtualne klastra oraz węzłów. Obecnie rozliczenia dla samej aplikacji nie występuje na platformie Azure.

Aby uzyskać więcej informacji zobacz następujące artykuły dotyczące aplikacji HDInsight:

* [Instalowanie aplikacji HDInsight](hdinsight-apps-install-applications.md). Dowiedz się, jak instalowanie aplikacji usługi HDInsight w klastrach.
* [Instalowanie niestandardowych aplikacji HDInsight](hdinsight-apps-install-custom-applications.md). Dowiedz się, jak zainstalować i przetestować niestandardowych aplikacji HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne
Aby przesłać niestandardową aplikację w witrynie Marketplace, najpierw [utworzenia i przetestowania aplikacji niestandardowej](hdinsight-apps-install-custom-applications.md).

Należy również zarejestrować konta dewelopera. Aby uzyskać więcej informacji, zobacz [publikowanie oferty w portalu Azure Marketplace](../marketplace/marketplace-publishers-guide.md) i [Utwórz konto Microsoft Developer](../marketplace/marketplace-publishers-guide.md).

## <a name="define-the-application"></a>Definiowanie aplikacji
Dwa kroki są zaangażowane w publikowania aplikacji w portalu Marketplace. Najpierw należy zdefiniować *createUiDef.json* pliku. Plik createUiDef.json wskazuje, które klastrów aplikacji jest zgodny z. Następnie należy opublikować szablon z witryny Azure portal. Poniżej przedstawiono przykładowy plik createUiDef.json:

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
Gdy aplikacja jest zainstalowana w klastrze (albo w istniejącym klastrze lub na nowy), zostanie utworzony węzeł krawędzi. Skrypt instalacji aplikacji działa w węźle brzegowym.

  > [!IMPORTANT]  
  > Nazwa skryptu instalacyjnego aplikacji musi być unikatowa dla określonego klastra. Nazwa skryptu musi mieć następujący format:
  > 
  > "name": "[concat ('hue-install-v0 ','-', uniquestring('applicationName')]"
  > 
  > Nazwa skryptu ma trzy części:
  > 
  > * Prefiks nazwy skryptu, który musi zawierać nazwę aplikacji lub nazwę związaną z aplikacją.
  > * Łącznik, aby zwiększyć czytelność.
  > * Funkcję unikatowego ciągu z nazwą aplikacji jako parametr.
  > 
  > Na liście akcji utrwalonych skryptów poprzedni przykład jest wyświetlany jako **hue-install-v0-4wkahss55hlas**. Zobacz [przykładowy ładunek JSON](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).
  > 

Skrypt instalacji musi mieć następujące właściwości:
* Skrypt jest idempotentny. Wiele wywołań do skryptu generuje ten sam wynik.
* Skrypt jest prawidłowo numerów wersji. Na użytek inną lokalizację skryptu podczas uaktualniania lub testowanie zmian. Dzięki temu, że klienci, którzy aplikacja jest instalowana nie są objęte przez aktualizacje lub testowania. 
* Skrypt zawiera odpowiednie rejestrowania w każdym punkcie. Zazwyczaj dzienniki skryptu są jedynym sposobem, aby debugować problemy z instalacją aplikacji.
* Wywołania usług ani zasobów zewnętrznych mają odpowiednią liczbę ponownych prób, więc nie występuje instalacji przez przejściowe problemy z siecią.
* Jeśli skrypt zostanie uruchomiony usług w węzłach, usługi są monitorowane i skonfigurowane do automatycznego uruchamiania, jeśli występuje Akcja ponownego uruchomienia węzła.

## <a name="package-the-application"></a>Pakiet aplikacji
Utwórz plik zip, który zawiera wszystkie pliki, które są wymagane do zainstalowania aplikacji HDInsight. Plik zip umożliwia publikowanie aplikacji. Plik zip zawiera następujące pliki:

* createUiDefinition.json
* mainTemplate.json (przykład można wyświetlić [instalowanie niestandardowych aplikacji HDInsight](hdinsight-apps-install-custom-applications.md).)
* Wszystkie wymagane skrypty

> [!NOTE]  
> Możesz hostować pliki aplikacji (w tym wszystkie pliki aplikacji sieci web), w dowolnym publicznie dostępnym punkcie końcowym.

## <a name="publish-the-application"></a>Publikowanie aplikacji
Publikowanie aplikacji HDInsight:

1. Zaloguj się do [publikowania platformy Azure](https://publish.windowsazure.com/).
2. W menu po lewej stronie wybierz **szablony rozwiązań**.
3. Wprowadź tytuł, a następnie wybierz **Utwórz nowy szablon rozwiązania**.
4. Jeśli nie została jeszcze zarejestrowana Twojej organizacji, wybierz **konta Centrum deweloperów Utwórz i Dołącz do programu platformy Azure**.  Aby uzyskać więcej informacji, zobacz [Utwórz konto Microsoft Developer](../marketplace/marketplace-publishers-guide.md).
5. Wybierz **zdefiniować niektórych topologii, aby rozpocząć**. Szablon rozwiązania jest "nadrzędny" do wszystkich jego topologii. Można zdefiniować wiele topologii w jednym szablonie oferty lub rozwiązania. Wypchnięcie oferty do wdrażania przejściowego wypchnięciach przy użyciu jego topologii. 
6. Wprowadź nazwę topologii, a następnie wybierz **+**.
7. Wprowadź nową wersję, a następnie wybierz **+**.
8. Przekaż plik zip, który został utworzony podczas Spakowana aplikacja.  
9. Wybierz **żądanie certyfikacji**. Zespół certyfikacji Microsoft przeglądów pliki i certyfikuje topologii.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [instalowanie aplikacji HDInsight](hdinsight-apps-install-applications.md) w klastrach.
* Dowiedz się, jak [instalowanie niestandardowych aplikacji HDInsight](hdinsight-apps-install-custom-applications.md) i wdrażanie cofnięto publikowanie aplikacji HDInsight HDInsight.
* Dowiedz się, jak [użyć akcji skryptu, aby dostosować klastry HDInsight opartych na systemie Linux](hdinsight-hadoop-customize-cluster-linux.md) i dodawanie większej liczby aplikacji. 
* Dowiedz się, jak [Tworzenie klastrów opartych na systemie Linux Apache Hadoop w HDInsight przy użyciu szablonów usługi Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).
* Dowiedz się, jak [użyć pustego węzła krawędzi w HDInsight](hdinsight-apps-use-edge-node.md) dostępu klastrów HDInsight, do testowania aplikacji HDInsight i hostowanie aplikacji HDInsight.

