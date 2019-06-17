---
title: Użyj własnego klucza dla platformy Apache Kafka w usłudze Azure HDInsight
description: W tym artykule opisano sposób używania własnego klucza z usługi Azure Key Vault do szyfrowania danych przechowywanych na platformie Apache Kafka w usłudze Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 6108bfd9e39b37507ec7e113bf2c489e890f0ca0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233567"
---
# <a name="bring-your-own-key-for-apache-kafka-on-azure-hdinsight"></a>Użyj własnego klucza dla platformy Apache Kafka w usłudze Azure HDInsight

Usługa Azure HDInsight obsługuje Bring Your Own Key (BYOK) platformy Apache Kafka. Ta funkcja umożliwia należą do Ciebie i zarządzanie kluczami, używany do szyfrowania danych magazynowanych.

Wszystkie dyski zarządzane w HDInsight są chronione przy użyciu szyfrowania usługi Azure Storage (SSE). Domyślnie dane na tych dyskach są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Po włączeniu funkcji BYOK, możesz podać klucz szyfrowania na potrzeby HDInsight i zarządzać za pomocą usługi Azure Key Vault.

Szyfrowanie BYOK jest jednoetapowy proces obsługi podczas tworzenia klastra bez ponoszenia dodatkowych kosztów. To wszystko, co należy zrobić, zarejestrować HDInsight jako tożsamość zarządzaną w usłudze Azure Key Vault i dodać klucz szyfrowania, podczas tworzenia klastra.

Wszystkie komunikaty z klastrem Kafka (w tym replik obsługiwane przez platformę Kafka) są zaszyfrowane za pomocą symetrycznego danych szyfrowania Key (klucz szyfrowania danych). Klucz szyfrowania danych jest chroniona przy użyciu klucza szyfrowania klucza (KEK) z magazynu kluczy. Procesy szyfrowania i odszyfrowywania są obsługiwane wyłącznie przez usługi Azure HDInsight. 

Bezpiecznie wymiany kluczy w magazynie kluczy, można użyć witryny Azure portal lub interfejsu wiersza polecenia platformy Azure. Obraca się klucz, klaster HDInsight Kafka zostanie uruchomiony w ciągu kilku minut przy użyciu nowego klucza. Włącz funkcje ochrony klucza "Usuwanie nietrwałe" zapewnić ochronę przed oprogramowaniem wymuszającym okup scenariuszy i przypadkowym usunięciem. Magazyny kluczy bez tej funkcji ochrony nie są obsługiwane.

## <a name="get-started-with-byok"></a>Rozpoczynanie pracy z funkcją BYOK
Aby utworzyć BYOK włączone klastra platformy Kafka, przejdziemy przez następujące kroki:
1. Tworzenie zarządzanych tożsamości dla zasobów platformy Azure
2. Konfigurowanie usługi Azure Key Vault i klucze
3. Tworzenie klastra HDInsight Kafka z włączoną funkcją BYOK
4. Wymiana klucza szyfrowania

## <a name="create-managed-identities-for-azure-resources"></a>Tworzenie zarządzanych tożsamości dla zasobów platformy Azure

   Aby uwierzytelniać się do usługi Key Vault, Utwórz przypisanych przez użytkownika z tożsamości zarządzanej przy użyciu [witryny Azure portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), [programu Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md), [usługi Azure Resource Manager](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md), lub [ Interfejs wiersza polecenia Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). Aby uzyskać więcej informacji na temat zarządzanych pracy tożsamości w usłudze Azure HDInsight, zobacz [zarządzanych tożsamości w usłudze Azure HDInsight](../hdinsight-managed-identities.md). Usługi Azure Active directory jest wymagana dla zarządzanych tożsamości i scenariusza BYOK do platformy Kafka, pakiet zabezpieczeń przedsiębiorstwa (ESP) nie jest to wymagane. Koniecznie Zapisz identyfikator zasobu tożsamości zarządzanej, podczas dodawania zasad dostępu magazynu kluczy.

   ![Utwórz tożsamość zarządzaną przypisanych przez użytkownika w witrynie Azure portal](./media/apache-kafka-byok/user-managed-identity-portal.png)

## <a name="setup-the-key-vault-and-keys"></a>Konfigurowanie usługi Key Vault i klucze

   HDInsight obsługuje tylko usługi Azure Key Vault. Jeśli masz magazynu kluczy, kluczy można zaimportować do usługi Azure Key Vault. Należy pamiętać, że klucze muszą mieć "Usuwanie nietrwałe". Funkcja "Usuwanie nietrwałe" jest dostępna za pośrednictwem interfejsu REST, .NET /C#, programu PowerShell i wiersza polecenia platformy Azure interfejsów.

   1. Aby utworzyć nowy magazyn kluczy, wykonaj [usługi Azure Key Vault](../../key-vault/key-vault-overview.md) Szybki Start. Aby uzyskać więcej informacji na temat importowania istniejących kluczy, odwiedź stronę [o kluczy, wpisów tajnych i certyfikatów](../../key-vault/about-keys-secrets-and-certificates.md).

   2. Włącz za pomocą usługi key vault "opcji soft-delete" [az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) interfejsu wiersza polecenia.
        ```Azure CLI az keyvault update --name <Key Vault Name> --enable-soft-delete
        ```

   3. Create keys

        a. To create a new key, select **Generate/Import** from the **Keys** menu under **Settings**.

        ![Generate a new key in Azure Key Vault](./media/apache-kafka-byok/kafka-create-new-key.png)

        b. Set **Options** to **Generate** and give the key a name.

        ![Generate a new key in Azure Key Vault](./media/apache-kafka-byok/kafka-create-a-key.png)

        c. Select the key you created from the list of keys.

        ![Azure Key Vault key list](./media/apache-kafka-byok/kafka-key-vault-key-list.png)

        d. When you use your own key for Kafka cluster encryption, you need to provide the key URI. Copy the **Key identifier** and save it somewhere until you're ready to create your cluster.

        ![Copy key identifier](./media/apache-kafka-byok/kafka-get-key-identifier.png)
   
    4. Add managed identity to the key vault access policy.

        a. Create a new Azure Key Vault access policy.

        ![Create new Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy.png)

        b. Under **Select Principal**, choose the user-assigned managed identity you created.

        ![Set Select Principal for Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-select-principal.png)

        c. Set **Key Permissions** to **Get**, **Unwrap Key**, and **Wrap Key**.

        ![Set Key Permissions for Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-keys.png)

        d. Set **Secret Permissions** to **Get**, **Set**, and **Delete**.

        ![Set Key Permissions for Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-secrets.png)

        e. Click on **Save**. 

        ![Save Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-save.png)

## Create HDInsight cluster

   You're now ready to create a new HDInsight cluster. BYOK can only be applied to new clusters during cluster creation. Encryption can't be removed from BYOK clusters, and BYOK can't be added to existing clusters.

   ![Kafka disk encryption in Azure portal](./media/apache-kafka-byok/apache-kafka-byok-portal.png)

   During cluster creation, provide the full key URL, including the key version. For example, `https://contoso-kv.vault.azure.net/keys/kafkaClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. You also need to assign the managed identity to the cluster and provide the key URI.

## Rotating the Encryption key
   There might be scenarios where you might want to change the encryption keys used by the Kafka cluster after it has been created. This can be easily via the portal. For this operation, the cluster must have access to both the current key and the intended new key, otherwise the rotate key operation will fail.

   To rotate the key, you must have the full url of the new key (See Step 3 of [Setup the Key Vault and Keys](#setup-the-key-vault-and-keys)). Once you have that, go to the Kafka cluster properties section in the portal and click on **Change Key** under **Disk Encryption Key URL**. Enter in the new key url and submit to rotate the key.

   ![Kafka rotate disk encryption key](./media/apache-kafka-byok/kafka-change-key.png)

## FAQ for BYOK to Apache Kafka

**How does the Kafka cluster access my key vault?**

   Associate a managed identity with the HDInsight Kafka cluster during cluster creation. This managed identity can be created before or during cluster creation. You also need to grant the managed identity access to the key vault where the key is stored.

**Is this feature available for all Kafka clusters on HDInsight?**

   BYOK encryption is only possible for Kafka 1.1 and above clusters.

**Can I have different keys for different topics/partitions?**

   No, all managed disks in the cluster are encrypted by the same key.

**What happens if the cluster loses access to the key vault or the key?**
   If the cluster loses access to the key, warnings will be shown in the Apache Ambari portal. In this state, the **Change Key** operation will fail. Once key access is restored, Ambari warnings will go away and operations such as key rotation can be successfully performed.

   ![Kafka key access Ambari alert](./media/apache-kafka-byok/kafka-byok-ambari-alert.png)

**How can I recover the cluster if the keys are deleted?**

   Since only “Soft Delete” enabled keys are supported, if the keys are recovered in the key vault, the cluster should regain access to the keys. To recover an Azure Key Vault key, see [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) or [az-keyvault-key-recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).

**Can I have producer/consumer applications working with a BYOK cluster and a non-BYOK cluster simultaneously?**

   Yes. The use of BYOK is transparent to producer/consumer applications. Encryption happens at the OS layer. No changes need to be made to existing producer/consumer Kafka applications.

**Are OS disks/Resource disks also encrypted?**

   No. OS disks and Resource disks are not encrypted.

**If a cluster is scaled up, will the new brokers support BYOK seamlessly?**

   Yes. The cluster needs access to the key in the key vault during scale up. The same key is used to encrypt all managed disks in the cluster.

**Is BYOK available in my location?**

   Kafka BYOK is available in all public clouds.

## Next steps

* For more information about Azure Key Vault, see [What is Azure Key Vault](../../key-vault/key-vault-whatis.md)?
* To get started with Azure Key Vault, see [Getting Started with Azure Key Vault](../../key-vault/key-vault-overview.md).
