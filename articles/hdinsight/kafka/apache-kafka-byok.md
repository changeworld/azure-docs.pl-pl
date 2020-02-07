---
title: Przenoszenie własnego klucza do Apache Kafka w usłudze Azure HDInsight
description: W tym artykule opisano sposób używania własnego klucza z Azure Key Vault do szyfrowania danych przechowywanych w Apache Kafka w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: cba8a4fd64b948d7a3e443426ca1f779af68a3fe
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049022"
---
# <a name="bring-your-own-key-for-apache-kafka-on-azure-hdinsight"></a>Przenoszenie własnego klucza do Apache Kafka w usłudze Azure HDInsight

Usługa Azure HDInsight obejmuje obsługę Bring Your Own Key (BYOK) dla Apache Kafka. Ta funkcja umożliwia posiadanie kluczy używanych do szyfrowania danych przechowywanych w usłudze REST i zarządzanie nimi.

Wszystkie dyski zarządzane w usłudze HDInsight są chronione za pomocą usługi Azure szyfrowanie usługi Storage (SSE). Domyślnie dane na tych dyskach są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. W przypadku włączenia BYOK należy podać klucz szyfrowania dla usługi HDInsight, aby używać go i zarządzać nim przy użyciu Azure Key Vault.

Szyfrowanie BYOK to proces jednoetapowy obsługiwany podczas tworzenia klastra bez dodatkowych kosztów. Wszystko, co musisz zrobić, to Rejestracja usługi HDInsight jako tożsamości zarządzanej przy użyciu Azure Key Vault i dodanie klucza szyfrowania podczas tworzenia klastra.

Wszystkie komunikaty do klastra Kafka (w tym repliki obsługiwane przez Kafka) są zaszyfrowane za pomocą klucza szyfrowania danych symetrycznych. Klucz szyfrowania danych jest chroniony przy użyciu klucza szyfrowanie klucza (KEK) z magazynu kluczy. Procesy szyfrowania i odszyfrowywania są obsługiwane całkowicie przez usługę Azure HDInsight.

Możesz użyć Azure Portal lub interfejsu wiersza polecenia platformy Azure, aby bezpiecznie obrócić klucze w magazynie kluczy. Gdy klucz zostanie obrócony, klaster usługi HDInsight Kafka rozpocznie korzystanie z nowego klucza w ciągu kilku minut. Włącz funkcje ochrony kluczy "nietrwałego usuwania", aby chronić je przed scenariuszami oprogramowania wymuszającego okup i przypadkowym usunięciem. Magazyny kluczy bez tej funkcji ochrony nie są obsługiwane.

## <a name="get-started-with-byok"></a>Wprowadzenie do BYOK

Aby utworzyć BYOK włączony klaster Kafka, przejdziemy przez następujące kroki:

1. Tworzenie tożsamości zarządzanych dla zasobów platformy Azure
2. Azure Key Vault i klucze Instalatora
3. Tworzenie klastra Kafka usługi HDInsight z włączonym BYOK
4. Obracanie klucza szyfrowania

## <a name="create-managed-identities-for-azure-resources"></a>Tworzenie tożsamości zarządzanych dla zasobów platformy Azure

Aby przeprowadzić uwierzytelnianie w Key Vault, Utwórz tożsamość zarządzaną przez użytkownika za pomocą [Azure Portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), [Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md), [Azure Resource Manager](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)lub [interfejsu wiersza polecenia platformy Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). Aby uzyskać więcej informacji na temat działania tożsamości zarządzanych w usłudze Azure HDInsight, zobacz [zarządzane tożsamości w usłudze Azure HDInsight](../hdinsight-managed-identities.md). Gdy usługa Azure Active Directory jest wymagana do zarządzania tożsamościami i BYOK do Kafka, pakiet Enterprise Security (ESP) nie jest wymagana. Należy pamiętać o zapisaniu identyfikatora zasobu tożsamości zarządzanej dla programu podczas dodawania go do zasad dostępu Key Vault.

![Tworzenie tożsamości zarządzanej przypisanej przez użytkownika w Azure Portal](./media/apache-kafka-byok/azure-portal-create-managed-identity.png)

## <a name="set-up-the-key-vault-and-keys"></a>Konfigurowanie Key Vault i kluczy

Usługa HDInsight obsługuje tylko Azure Key Vault. Jeśli masz własny magazyn kluczy, możesz zaimportować klucze do Azure Key Vault. Należy pamiętać, że klucze muszą mieć wartość "usuwanie nietrwałe". Funkcja usuwania nietrwałego jest dostępna za pomocą interfejsów REST, .NET/C#, PowerShell i interfejsu wiersza polecenia platformy Azure.

1. Aby utworzyć nowy magazyn kluczy, postępuj zgodnie z [Azure Key Vault](../../key-vault/quick-create-cli.md) przewodnika Szybki Start. Aby uzyskać więcej informacji o importowaniu istniejących kluczy, odwiedź stronę [dotyczącą kluczy, wpisów tajnych i certyfikatów](../../key-vault/about-keys-secrets-and-certificates.md).

1. Włącz opcję "nietrwałe usuwanie" w magazynie kluczy za pomocą polecenia [AZ Key magazynu Update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) CLI.

    ```azurecli
    az keyvault update --name <Key Vault Name> --enable-soft-delete
    ```

1. Utwórz klucze.

    a. Aby utworzyć nowy klucz, wybierz pozycję **Generuj/Importuj** z menu **klucze** w obszarze **Ustawienia**.

    ![Generuj nowy klucz w Azure Key Vault](./media/apache-kafka-byok/kafka-create-new-key.png "Generuj nowy klucz w Azure Key Vault")

    b. Ustaw **Opcje** w celu **wygenerowania** klucza i nadaj mu nazwę.

    ![Apache Kafka generuje nazwę klucza](./media/apache-kafka-byok/apache-kafka-create-key.png "Generuj nazwę klucza")

    c. Wybierz utworzony klucz z listy kluczy.

    ![Lista kluczy magazynu kluczy Apache Kafka](./media/apache-kafka-byok/kafka-key-vault-key-list.png)

    d. W przypadku używania własnego klucza do szyfrowania klastra Kafka należy podać identyfikator URI klucza. Skopiuj **Identyfikator klucza** i Zapisz go w dowolnym miejscu do momentu, aż wszystko będzie gotowe do utworzenia klastra.

    ![Apache Kafka — uzyskiwanie identyfikatora klucza](./media/apache-kafka-byok/kafka-get-key-identifier.png)

1. Dodaj zarządzaną tożsamość do zasad dostępu magazynu kluczy.

    a. Utwórz nowe zasady dostępu Azure Key Vault.

    ![Utwórz nowe zasady dostępu Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy.png)

    b. W obszarze **Wybierz podmiot zabezpieczeń**Wybierz utworzoną tożsamość zarządzaną przez użytkownika.

    ![Ustaw pozycję Wybierz podmiot zabezpieczeń dla zasad dostępu Azure Key Vault](./media/apache-kafka-byok/azure-portal-add-access-policy.png)

    c. Ustawianie **uprawnień klucza** do **pobierania**, **odwinięcia klucza**i **zawijania klucza**.

    ![Ustawianie uprawnień klucza dla Azure Key Vault dostępu policy1](./media/apache-kafka-byok/add-key-vault-access-policy-keys.png "Ustawianie uprawnień klucza dla Azure Key Vault dostępu policy1")

    d. Ustaw **uprawnienia klucza tajnego** na **pobieranie**, **Ustawianie**i **usuwanie**.

    ![Ustawianie uprawnień klucza dla Azure Key Vault dostępu policy2](./media/apache-kafka-byok/add-key-vault-access-policy-secrets.png "Ustawianie uprawnień klucza dla Azure Key Vault dostępu policy2")

    e. Wybierz pozycję **Zapisz**.

    ![Zapisz zasady dostępu Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy-save.png)

## <a name="create-hdinsight-cluster"></a>Tworzenie klastra usługi HDInsight

Teraz możesz utworzyć nowy klaster usługi HDInsight. Na karcie **podstawy** wybierz pozycję **Kafka** dla **typu klastra**.

![Azure Portal wybierz typ Kafka](./media/apache-kafka-byok/azure-portal-cluster-basics-type-kafka.png)

BYOK można stosować tylko do nowych klastrów podczas tworzenia klastra. Nie można usunąć szyfrowania z klastrów BYOK i nie można dodać BYOK do istniejących klastrów.

![Kafka szyfrowanie dysków w Azure Portal](./media/apache-kafka-byok/azure-portal-cluster-security-networking-kafka-byok.png)

Podczas tworzenia klastra Podaj pełny adres URL klucza, w tym wersję klucza. Na przykład `https://contoso-kv.vault.azure.net/keys/kafkaClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. Należy również przypisać zarządzaną tożsamość do klastra i podać identyfikator URI klucza. Aby uzyskać szczegółowe informacje dotyczące tworzenia klastra, zobacz [Tworzenie klastrów Apache Hadoop przy użyciu Azure Portal](./apache-kafka-get-started.md)

## <a name="rotating-the-encryption-key"></a>Obracanie klucza szyfrowania

Mogą wystąpić sytuacje, w których warto zmienić klucze szyfrowania używane przez klaster Kafka po jego utworzeniu. Można to łatwo zrobić za pośrednictwem portalu. W przypadku tej operacji klaster musi mieć dostęp zarówno do bieżącego klucza, jak i do zamierzonego nowego klucza. w przeciwnym razie operacja zamiany klucza zakończy się niepowodzeniem.

Aby obrócić klucz, musisz mieć pełny adres URL nowego klucza (zobacz krok 3 [instalacji Key Vault i kluczy](#set-up-the-key-vault-and-keys)). Po wykonaniu tej opcji przejdź do sekcji Właściwości klastra Kafka w portalu, a następnie kliknij pozycję **Zmień klucz** w obszarze **adres URL klucza szyfrowania dysku**. Wprowadź adres URL nowego klucza i prześlij go, aby obrócić klucz.

![Kafka Obróć klucz szyfrowania dysku](./media/apache-kafka-byok/apache-kafka-change-key.png)

## <a name="faq-for-byok-to-apache-kafka"></a>Często zadawane pytania dotyczące BYOK do Apache Kafka

**Jak klaster Kafka uzyskuje dostęp do mojego magazynu kluczy?**

Skojarz zarządzaną tożsamość z klastrem usługi HDInsight Kafka podczas tworzenia klastra. Tę tożsamość zarządzaną można utworzyć przed lub podczas tworzenia klastra. Musisz również udzielić zarządzanej tożsamości dostępu do magazynu kluczy, w którym jest przechowywany klucz.

**Czy ta funkcja jest dostępna dla wszystkich klastrów Kafka w usłudze HDInsight?**

Szyfrowanie BYOK jest możliwe tylko dla klastrów Kafka 1,1 i nowszych.

**Czy mogę mieć różne klucze dla różnych tematów/partycji?**

Nie, wszystkie dyski zarządzane w klastrze są szyfrowane przy użyciu tego samego klucza.

**Co się stanie, Jeśli klaster utraci dostęp do magazynu kluczy lub klucza?**

Jeśli klaster utraci dostęp do klucza, ostrzeżenia będą wyświetlane w portalu Apache Ambari. W tym stanie operacja **zmiany klucza** zakończy się niepowodzeniem. Po przywróceniu dostępu do klucza Ambari ostrzeżenia i operacje, takie jak rotacja kluczy, mogą zostać wykonane pomyślnie.

![Apache Kafka alert Ambari dostępu do klucza](./media/apache-kafka-byok/kafka-byok-ambari-alert.png)

**Jak odzyskać klaster, jeśli klucze są usuwane?**

Ponieważ obsługiwane są tylko klucze z włączonym usuwaniem nietrwałego, jeśli klucze zostaną odzyskane w magazynie kluczy, klaster powinien odzyskać dostęp do kluczy. Aby odzyskać klucz Azure Key Vault, zobacz [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) lub [AZ-Key magazyn-klucz-Recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).

**Czy aplikacje producentów i konsumentów mogą pracować z klastrem BYOK i klastrem innym niż BYOK jednocześnie?**

Tak. Korzystanie z BYOK jest niewidoczne dla aplikacji producent/konsument. Szyfrowanie odbywa się w warstwie systemu operacyjnego. Nie trzeba wprowadzać żadnych zmian w istniejących aplikacjach producent/konsument Kafka.

**Czy dyski systemu operacyjnego/dyski zasobów są również szyfrowane?**

Nie. Dyski systemu operacyjnego i dyski zasobów nie są szyfrowane.

**Jeśli klaster jest skalowany w górę, czy nowi brokerzy będą wspierać BYOK bezproblemowo?**

Tak. Klaster wymaga dostępu do klucza w magazynie kluczy podczas skalowania w górę. Ten sam klucz służy do szyfrowania wszystkich dysków zarządzanych w klastrze.

**Czy BYOK jest dostępny w mojej lokalizacji?**

Kafka BYOK jest dostępny we wszystkich chmurach publicznych.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat Azure Key Vault, zobacz [co to jest Azure Key Vault](../../key-vault/key-vault-overview.md)?
* Aby rozpocząć pracę z Azure Key Vault, zobacz [wprowadzenie z Azure Key Vault](../../key-vault/key-vault-overview.md).
