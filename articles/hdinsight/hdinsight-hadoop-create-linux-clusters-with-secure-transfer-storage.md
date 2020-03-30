---
title: Apache Hadoop & bezpiecznej pamięci masowej transferu — Azure HDInsight
description: Dowiedz się, jak tworzyć klastry usługi HDInsight z kontami magazynu platformy Azure z bezpiecznym transferem.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: c1e5ca8b0bb828e5e8ce896bba6a5278266b118e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560086"
---
# <a name="apache-hadoop-clusters-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Klastry Apache Hadoop z kontami bezpiecznego magazynu transferu w usłudze Azure HDInsight

Funkcja [Wymagany bezpieczny transfer](../storage/common/storage-require-secure-transfer.md) poprawia bezpieczeństwo konta usługi Azure Storage poprzez wymuszanie kierowania wszystkich zapytań do konta przez zabezpieczone połączenie. Funkcja ta oraz schemat wasbs są obsługiwane tylko w klastrze usługi HDInsight w wersji 3.6 lub nowszym.

> [!IMPORTANT]
> Włączenie bezpiecznego transferu magazynu po utworzeniu klastra może spowodować błędy przy użyciu konta magazynu i nie jest zalecane. Lepiej jest utworzyć nowy klaster przy użyciu konta magazynu z bezpiecznym transferem już włączonym.

## <a name="storage-accounts"></a>Konta magazynu

### <a name="azure-portal"></a>Portal Azure

Domyślnie właściwość wymaganego bezpiecznego transferu jest włączona podczas tworzenia konta magazynu w witrynie Azure portal.

Aby zaktualizować istniejące konto magazynu w witrynie Azure portal, zobacz [Wymaganie bezpiecznego transferu w witrynie Azure portal](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-for-an-existing-storage-account).

### <a name="powershell"></a>PowerShell

W przypadku polecenia cmdlet Programu PowerShell [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount)upewnij się, że parametr `-EnableHttpsTrafficOnly` jest ustawiony na `1`.

Aby zaktualizować istniejące konto magazynu za pomocą programu PowerShell, zobacz [Wymaganie bezpiecznego transferu za pomocą programu PowerShell](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-powershell).

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

W przypadku polecenia Azure CLI polecenie [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create), upewnij się, że parametr `--https-only` jest ustawiony na `true`.

Aby zaktualizować istniejące konto magazynu za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [Wymaganie bezpiecznego transferu za pomocą interfejsu wiersza polecenia platformy Azure](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-azure-cli).

## <a name="add-additional-storage-accounts"></a>Dodawanie kolejnych kont magazynu

Dostępnych jest kilka opcji dodawania kolejnych kont magazynu z bezpiecznym transferem:

* Zmodyfikuj szablon usługi Azure Resource Manager w ostatniej sekcji.
* Utwórz klaster przy użyciu witryny [Azure Portal](https://portal.azure.com) i określ połączone konto magazynu.
* Użyj akcji skryptu, aby dodać kolejne konta magazynu z bezpiecznym transferem do istniejącego klastra usługi HDInsight. Aby uzyskać więcej informacji, zobacz [Dodawanie kolejnych kont magazynu do usługi HDInsight](hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Następne kroki

* Korzystanie z usługi Azure Storage (WASB) zamiast [apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) jako domyślnego magazynu danych
* Aby uzyskać informacje o sposobie używania usługi Azure Storage przez usługę HDInsight, zobacz [Używanie usługi Azure Storage z usługą HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Aby uzyskać informacje na temat przekazywania danych do usługi HDInsight, zobacz [Przekazywanie danych do usługi HDInsight](hdinsight-upload-data.md).
