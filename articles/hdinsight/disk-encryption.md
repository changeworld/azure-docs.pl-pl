---
title: Szyfrowanie dysku klucza zarządzanego przez klienta dla usługi Azure HDInsight
description: W tym artykule opisano, jak używać własnego klucza szyfrowania z Azure Key Vault do szyfrowania danych przechowywanych na dyskach zarządzanych w klastrach usługi Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/06/2019
ms.openlocfilehash: 2c015db828bcbfa8b26f519b3a4707b5ec69b8f3
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982506"
---
# <a name="customer-managed-key-disk-encryption"></a>Szyfrowanie dysków klucza zarządzanego przez klienta

Usługa Azure HDInsight obsługuje klucze zarządzane przez klienta, znane także jako Bring Your Own Key (BYOK) szyfrowanie danych na dyskach zarządzanych i dyskach zasobów podłączonych do maszyn wirtualnych klastrów usługi HDInsight. Ta funkcja umożliwia używanie Azure Key Vault do zarządzania kluczami szyfrowania, które zabezpieczają dane przechowywane w klastrach usługi HDInsight. Klastry mogą mieć co najmniej jedno dołączone konto usługi Azure Storage, na których klucze szyfrowania mogą być również zarządzane przez firmę Microsoft lub zarządzane przez klienta, ale usługa szyfrowania jest inna.

Ten dokument nie zawiera adresów danych przechowywanych na koncie usługi Azure Storage. Aby uzyskać więcej informacji na temat środowiska ASE, zobacz [szyfrowanie usługi Azure Storage w przypadku przechowywanych danych](../storage/common/storage-service-encryption.md).

Wszystkie dyski zarządzane w usłudze HDInsight są chronione za pomocą usługi Azure szyfrowanie usługi Storage (SSE). Domyślnie dane na tych dyskach są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. W przypadku włączenia kluczy zarządzanych przez klienta usługi HDInsight należy udostępnić klucze szyfrowania dla usługi HDInsight, aby używać tych kluczy i zarządzać nimi za pomocą Azure Key Vault.

Szyfrowanie klucza zarządzanego przez klienta to proces jednoetapowy obsługiwany podczas tworzenia klastra bez dodatkowych kosztów. Wszystko, co musisz zrobić, to Rejestracja usługi HDInsight jako tożsamości zarządzanej przy użyciu Azure Key Vault i dodanie klucza szyfrowania podczas tworzenia klastra.

Zarówno dysk zasobów, jak i dyski zarządzane w każdym węźle klastra są szyfrowane za pomocą klucza szyfrowania danych symetrycznych. Klucz szyfrowania danych jest chroniony przy użyciu klucza szyfrowanie klucza (KEK) z magazynu kluczy. Procesy szyfrowania i odszyfrowywania są obsługiwane całkowicie przez usługę Azure HDInsight.

Możesz użyć Azure Portal lub interfejsu wiersza polecenia platformy Azure, aby bezpiecznie obrócić klucze w magazynie kluczy. Gdy klucz zostanie obrócony, klaster usługi HDInsight zaczyna używać nowego klucza w ciągu kilku minut. Włącz funkcje ochrony kluczy "nietrwałego usuwania", aby chronić je przed scenariuszami oprogramowania wymuszającego okup i przypadkowym usunięciem. Magazyny kluczy bez tej funkcji ochrony nie są obsługiwane.

## <a name="get-started-with-customer-managed-keys"></a>Wprowadzenie do kluczy zarządzanych przez klienta

Aby utworzyć klaster usługi HDInsight z włączonym kluczem zarządzanym przez klienta, wykonaj następujące czynności:

1. Tworzenie tożsamości zarządzanych dla zasobów platformy Azure
2. Azure Key Vault i klucze Instalatora
3. Tworzenie klastra usługi HDInsight z włączonym kluczem zarządzanym przez klienta
4. Obracanie klucza szyfrowania

## <a name="create-managed-identities-for-azure-resources"></a>Tworzenie tożsamości zarządzanych dla zasobów platformy Azure

Aby przeprowadzić uwierzytelnianie w Key Vault, Utwórz tożsamość zarządzaną przez użytkownika za pomocą [Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md), [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)lub [interfejsu wiersza polecenia platformy Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). Aby uzyskać więcej informacji na temat działania tożsamości zarządzanych w usłudze Azure HDInsight, zobacz [zarządzane tożsamości w usłudze Azure HDInsight](hdinsight-managed-identities.md). Należy pamiętać o zapisaniu identyfikatora zasobu tożsamości zarządzanej dla programu podczas dodawania go do zasad dostępu Key Vault.

## <a name="set-up-the-key-vault-and-keys"></a>Konfigurowanie Key Vault i kluczy

Usługa HDInsight obsługuje tylko Azure Key Vault. Jeśli masz własny magazyn kluczy, możesz zaimportować klucze do Azure Key Vault. Należy pamiętać, że klucze muszą mieć wartość "usuwanie nietrwałe". Funkcja usuwania nietrwałego jest dostępna za pomocą interfejsów REST, .NET/C#, PowerShell i interfejsu wiersza polecenia platformy Azure.

1. Aby utworzyć nowy magazyn kluczy, postępuj zgodnie z [Azure Key Vault](../key-vault/key-vault-overview.md) przewodnika Szybki Start. Aby uzyskać więcej informacji o importowaniu istniejących kluczy, odwiedź stronę [dotyczącą kluczy, wpisów tajnych i certyfikatów](../key-vault/about-keys-secrets-and-certificates.md).

1. Włącz opcję "nietrwałe usuwanie" w magazynie kluczy za pomocą polecenia [AZ Key magazynu Update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) CLI.

    ```azurecli
    az keyvault update --name <Key Vault Name> --enable-soft-delete
    ```

1. Utwórz klucze.

    a. Aby utworzyć nowy klucz, wybierz pozycję **Generuj/Importuj** z menu **klucze** w obszarze **Ustawienia**.

    ![Generuj nowy klucz w Azure Key Vault](./media/disk-encryption/create-new-key.png "Generuj nowy klucz w Azure Key Vault")

    b. Ustaw **Opcje** w celu **wygenerowania** klucza i nadaj mu nazwę.

    ![generuje nazwę klucza](./media/disk-encryption/create-key.png "Generuj nazwę klucza")

    d. Wybierz utworzony klucz z listy kluczy.

    ![Lista kluczy magazynu kluczy](./media/disk-encryption/key-vault-key-list.png)

    d. Jeśli używasz własnego klucza do szyfrowania klastra usługi HDInsight, musisz podać identyfikator URI klucza. Skopiuj **Identyfikator klucza** i Zapisz go w dowolnym miejscu do momentu, aż wszystko będzie gotowe do utworzenia klastra.

    ![Pobierz identyfikator klucza](./media/disk-encryption/get-key-identifier.png)

1. Dodaj zarządzaną tożsamość do zasad dostępu magazynu kluczy.

    a. Utwórz nowe zasady dostępu Azure Key Vault.

    ![Utwórz nowe zasady dostępu Azure Key Vault](./media/disk-encryption/add-key-vault-access-policy.png)

    b. W obszarze **Wybierz podmiot zabezpieczeń**Wybierz utworzoną tożsamość zarządzaną przez użytkownika.

    ![Ustaw pozycję Wybierz podmiot zabezpieczeń dla zasad dostępu Azure Key Vault](./media/disk-encryption/add-key-vault-access-policy-select-principal.png)

    d. Ustawianie **uprawnień klucza** do **pobierania**, **odwinięcia klucza**i **zawijania klucza**.

    ![Ustawianie uprawnień klucza dla Azure Key Vault dostępu policy1](./media/disk-encryption/add-key-vault-access-policy-keys.png "Ustawianie uprawnień klucza dla Azure Key Vault dostępu policy1")

    d. Ustaw **uprawnienia klucza tajnego** na **pobieranie**, **Ustawianie**i **usuwanie**.

    ![Ustawianie uprawnień klucza dla Azure Key Vault dostępu policy2](./media/disk-encryption/add-key-vault-access-policy-secrets.png "Ustawianie uprawnień klucza dla Azure Key Vault dostępu policy2")

    e. Wybierz pozycję **Zapisz**.

    ![Zapisz zasady dostępu Azure Key Vault](./media/disk-encryption/add-key-vault-access-policy-save.png)

## <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>Tworzenie klastra przy użyciu szyfrowania dysku klucza zarządzanego przez klienta

Teraz możesz utworzyć nowy klaster usługi HDInsight. Klucz zarządzany przez klienta można stosować tylko do nowych klastrów podczas tworzenia klastra. Nie można usunąć szyfrowania z klastrów kluczy zarządzanych przez klienta i nie można dodać klucza zarządzanego przez klienta do istniejących klastrów.

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Podczas tworzenia klastra Podaj pełny adres URL klucza, w tym wersję klucza. Na przykład `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. Należy również przypisać zarządzaną tożsamość do klastra i podać identyfikator URI klucza.

### <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

W poniższym przykładzie pokazano, jak za pomocą interfejsu wiersza polecenia platformy Azure utworzyć nowy klaster Apache Spark z włączonym szyfrowaniem dysków. Aby uzyskać więcej informacji, zobacz [interfejs wiersza polecenia platformy Azure AZ HDInsight Create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) documentation.

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

## <a name="rotating-the-encryption-key"></a>Obracanie klucza szyfrowania

Mogą wystąpić sytuacje, w których warto zmienić klucze szyfrowania używane przez klaster usługi HDInsight po jego utworzeniu. Można to łatwo zrobić za pośrednictwem portalu. W przypadku tej operacji klaster musi mieć dostęp zarówno do bieżącego klucza, jak i do zamierzonego nowego klucza. w przeciwnym razie operacja zamiany klucza zakończy się niepowodzeniem.

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Aby obrócić klucz, musisz mieć pełny adres URL nowego klucza (zobacz krok 3 [instalacji Key Vault i kluczy](#set-up-the-key-vault-and-keys)). Po wykonaniu tej czynności przejdź do sekcji Właściwości klastra usługi HDInsight w portalu, a następnie kliknij pozycję **Zmień klucz** w obszarze **adres URL klucza szyfrowania dysku**. Wprowadź adres URL nowego klucza i prześlij go, aby obrócić klucz.

![Obróć klucz szyfrowania dysku](./media/disk-encryption/change-key.png)

### <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

Poniższy przykład pokazuje, jak obrócić klucz szyfrowania dysku dla istniejącego klastra usługi HDInsight. Aby uzyskać więcej informacji, zobacz [interfejs wiersza polecenia platformy Azure AZ HDInsight Obróć-Disk-Encryption-Key](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-rotate-disk-encryption-key) .

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="faq-for-customer-managed-key-encryption"></a>Często zadawane pytania dotyczące szyfrowania kluczy zarządzanych przez klienta

**Jak klaster usługi HDInsight uzyskuje dostęp do mojego magazynu kluczy?**

Usługa HDInsight uzyskuje dostęp do wystąpienia Azure Key Vault przy użyciu tożsamości zarządzanej skojarzonej z klastrem usługi HDInsight. Tę tożsamość zarządzaną można utworzyć przed lub podczas tworzenia klastra. Musisz również udzielić zarządzanej tożsamości dostępu do magazynu kluczy, w którym jest przechowywany klucz.

**Czy ta funkcja jest dostępna dla wszystkich klastrów w usłudze HDInsight?**

Szyfrowanie klucza zarządzanego przez klienta jest dostępne dla wszystkich typów klastrów z wyjątkiem Spark 2,1 i 2,2.

**Czy można użyć wielu kluczy do szyfrowania różnych dysków lub folderów?**

Nie, wszystkie dyski zarządzane i dyski zasobów są szyfrowane przy użyciu tego samego klucza.

**Co się stanie, Jeśli klaster utraci dostęp do magazynu kluczy lub klucza?**

Jeśli klaster utraci dostęp do klucza, ostrzeżenia będą wyświetlane w portalu Apache Ambari. W tym stanie operacja **zmiany klucza** zakończy się niepowodzeniem. Po przywróceniu dostępu do klucza Ambari ostrzeżenia i operacje, takie jak rotacja kluczy, mogą zostać wykonane pomyślnie.

![alert dotyczący Ambari dostępu do klucza](./media/disk-encryption/ambari-alert.png)

**Jak odzyskać klaster, jeśli klucze są usuwane?**

Ponieważ obsługiwane są tylko klucze z włączonym usuwaniem nietrwałego, jeśli klucze zostaną odzyskane w magazynie kluczy, klaster powinien odzyskać dostęp do kluczy. Aby odzyskać klucz Azure Key Vault, zobacz [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) lub [AZ-Key magazyn-klucz-Recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).

**Które typy dysków są szyfrowane? Czy dyski systemu operacyjnego/dyski zasobów są również szyfrowane?**

Dyski zasobów i dane/dyski zarządzane są szyfrowane. Dyski systemu operacyjnego nie są zaszyfrowane.

**Jeśli klaster jest skalowany w górę, nowe węzły będą bezproblemowo obsługiwać klucze zarządzane przez klienta?**

Tak. Klaster wymaga dostępu do klucza w magazynie kluczy podczas skalowania w górę. Ten sam klucz służy do szyfrowania zarówno dysków zarządzanych, jak i dysków zasobów w klastrze.

**Czy klucze zarządzane przez klienta są dostępne w mojej lokalizacji?**

Klucze zarządzane przez klienta usługi HDInsight są dostępne we wszystkich chmurach publicznych i w chmurach narodowych.

## <a name="next-steps"></a>Następne kroki

* [Omówienie zabezpieczeń przedsiębiorstwa w usłudze Azure HDInsight](./domain-joined/hdinsight-security-overview.md)
