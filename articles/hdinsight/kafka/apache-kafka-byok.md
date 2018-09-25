---
title: Użyj własnego klucza dla platformy Apache Kafka w usłudze Azure HDInsight (wersja zapoznawcza)
description: W tym artykule opisano sposób używania własnego klucza z usługi Azure Key Vault do szyfrowania danych przechowywanych na platformie Apache Kafka w usłudze Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 46105ee92a5c98cb8180b2499d0ad295702aac43
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953378"
---
# <a name="bring-your-own-key-for-apache-kafka-on-azure-hdinsight-preview"></a>Użyj własnego klucza dla platformy Apache Kafka w usłudze Azure HDInsight (wersja zapoznawcza)

Usługa Azure HDInsight obsługuje Bring Your Own Key (BYOK) platformy Apache Kafka. Ta funkcja umożliwia należą do Ciebie i zarządzanie kluczami, używany do szyfrowania danych magazynowanych. 

Wszystkie dyski zarządzane w HDInsight są chronione przy użyciu szyfrowania usługi Azure Storage (SSE). Domyślnie dane na tych dyskach są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Po włączeniu funkcji BYOK, możesz podać klucz szyfrowania na potrzeby HDInsight i zarządzać za pomocą usługi Azure Key Vault. 

Szyfrowanie BYOK jest jednoetapowy proces obsługi podczas tworzenia klastra bez ponoszenia dodatkowych kosztów. To wszystko, co należy zrobić, zarejestrować HDInsight jako tożsamość zarządzaną w usłudze Azure Key Vault i dodać klucz szyfrowania, podczas tworzenia klastra.

Wszystkie komunikaty z klastrem Kafka (w tym replik obsługiwane przez platformę Kafka) są zaszyfrowane za pomocą symetrycznego danych szyfrowania Key (klucz szyfrowania danych). Klucz szyfrowania danych jest chroniona przy użyciu klucza szyfrowania klucza (KEK) z magazynu kluczy. Procesy szyfrowania i odszyfrowywania są obsługiwane wyłącznie przez usługi Azure HDInsight. 

Bezpiecznie wymiany kluczy w magazynie kluczy, można użyć witryny Azure portal lub interfejsu wiersza polecenia platformy Azure. Obraca się klucz, klaster HDInsight Kafka zostanie uruchomiony w ciągu kilku minut przy użyciu nowego klucza. Włącz funkcje ochrony klucza "Przeczyszczanie" i "Usuwanie nietrwałe" zapewnić ochronę przed oprogramowaniem wymuszającym okup scenariuszy i przypadkowym usunięciem. Klucze bez funkcji ochrony nie są obsługiwane.

## <a name="get-started-with-byok"></a>Rozpoczynanie pracy z funkcją BYOK

1. Tworzenie zarządzanych tożsamości dla zasobów platformy Azure.

   Aby uwierzytelniać się do usługi Key Vault, Utwórz przypisanych przez użytkownika z tożsamości zarządzanej przy użyciu [witryny Azure Portal](../../active-directory/managed-service-identity/how-to-manage-ua-identity-portal.md), [programu Azure PowerShell](../../active-directory/managed-service-identity/how-to-manage-ua-identity-powershell.md), [usługi Azure Resource Manager](../../active-directory/managed-service-identity/how-to-manage-ua-identity-arm.md), lub [ Interfejs wiersza polecenia Azure](../../active-directory/managed-service-identity/how-to-manage-ua-identity-cli.md). Usługi Azure Active directory jest wymagana dla zarządzanych tożsamości i scenariusza BYOK do platformy Kafka, pakiet zabezpieczeń przedsiębiorstwa (ESP) nie jest to wymagane. Koniecznie Zapisz identyfikator zasobu tożsamości zarządzanej, podczas dodawania zasad dostępu magazynu kluczy.

   ![Utwórz tożsamość zarządzaną przypisanych przez użytkownika w witrynie Azure portal](./media/apache-kafka-byok/user-managed-identity-portal.png)

2. Utwórz lub zaimportuj usługi Azure Key Vault.

   HDInsight obsługuje tylko usługi Azure Key Vault. Jeśli masz magazynu kluczy, kluczy można zaimportować do usługi Azure Key Vault. Należy pamiętać, że klucze muszą mieć "Usuwanie nietrwałe" i "Przeczyszczanie" włączone. Funkcje "Usuwanie nietrwałe" i "Przeczyszczanie" są dostępne za pośrednictwem interfejsu REST, .NET / C#, programu PowerShell i wiersza polecenia platformy Azure interfejsów.

   Aby utworzyć nowy magazyn kluczy, wykonaj [usługi Azure Key Vault](../../key-vault/key-vault-get-started.md) Szybki Start. Aby uzyskać więcej informacji na temat importowania istniejących kluczy, odwiedź stronę [o kluczy, wpisów tajnych i certyfikatów](../../key-vault/about-keys-secrets-and-certificates.md).

3. Dodaj tożsamość zarządzaną do zasad dostępu magazynu kluczy.

   Utwórz nowe zasady dostępu do usługi Azure Key Vault.

   ![Utwórz nowe zasady dostępu do usługi Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy.png)

   Ustaw **uprawnienia klucza** do **uzyskać**, **Odpakuj klucz**, i **Opakuj klucz**.

   ![Ustaw uprawnienia klucza pod kątem zasad dostępu usługi Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy-keys.png)

   Ustaw **uprawnienia klucza tajnego** do **uzyskać**, **ustaw**, i **Usuń**.

   ![Ustaw uprawnienia klucza pod kątem zasad dostępu usługi Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy-secrets.png)

   W obszarze **Wybierz podmiot zabezpieczeń**, wybierz przypisanych przez użytkownika tożsamości zarządzanej został utworzony.

   ![Ustaw Wybierz podmiot zabezpieczeń dla zasad dostępu w usłudze Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy-select-principal.png)

4. Tworzenie klastra HDInsight

   Teraz możesz przystąpić do tworzenia nowego klastra HDInsight. BYOK będzie stosowany tylko do nowych klastrów podczas tworzenia klastra. Szyfrowanie nie można usunąć z klastrami funkcji BYOK i BYOK nie można dodać do istniejących klastrów.

   ![Szyfrowanie dysków platformy Kafka w witrynie Azure portal](./media/apache-kafka-byok/apache-kafka-byok-portal.png)

   Podczas tworzenia klastra, podaj pełny adres URL, w tym klucza wersji klucza. Na przykład `myakv.azure.com/KEK1/v1`. Należy również przypisać tożsamość zarządzaną do klastra i podaj identyfikator URI klucza.

## <a name="faq-for-byok-to-kafka"></a>Często zadawane pytania dotyczące funkcji BYOK do platformy Kafka

**Jak klastra platformy Kafka dostęp mojej usługi key vault?**

   Skojarz tożsamości zarządzanej z klastrem HDInsight Kafka podczas tworzenia klastra. Ta tożsamość zarządzaną można utworzyć przed lub podczas tworzenia klastra. Należy również przyznać dostęp tożsamość zarządzaną usługą key vault, miejsce przechowywania klucza.

**Ta funkcja jest dostępna dla wszystkich klastrów platformy Kafka na HDInsight?**

   Szyfrowanie BYOK jest możliwe tylko dla platformy Kafka 1.1 lub nowszej klastrów.

**Czy można mieć różne klucze dla różnych tematów/partycji?**

   Nie wszystkie dyski zarządzane w klastrze są szyfrowane za pomocą tego samego klucza.

**Jak można odzyskać klastra, jeśli kluczy zostaną usunięte?**

   Ponieważ tylko "Usuwanie nietrwałe" włączone klucze są obsługiwane w przypadku kluczy zostaną przywrócone w usłudze key vault, klastra należy odzyskać dostęp do kluczy. Aby przywrócić klucz usługi Azure Key Vault, zobacz [Restore-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey).

**Czy można mieć producenta i odbiorcy aplikacji przy użyciu funkcji BYOK i klastrze bez BYOK jednocześnie?**

   Tak. Korzystanie z funkcji BYOK jest niewidoczny dla aplikacji, producenta i odbiorcy. Szyfrowanie odbywa się w warstwie systemu operacyjnego. Żadne zmiany nie muszą nawiązać istniejących producenta i odbiorcy platformy Kafka aplikacji.

**Dyski dysków lub zasobu systemu operacyjnego również są szyfrowane?**

   Nie. Dyski systemu operacyjnego i dyski zasobów nie są szyfrowane.

**Jeśli klaster będzie skalowany w górę, nowych brokerów obejmie BYOK bezproblemowo?**

   Tak. Klaster musi mieć dostęp do klucza w magazynie kluczy podczas skalowania się. Ten sam klucz służy do szyfrowania wszystkich dysków zarządzanych w klastrze.

**Jest BYOK w mojej lokalizacji?**

   Platforma Kafka BYOK jest dostępny w wszystkich chmur publicznych.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać więcej informacji na temat usługi Azure Key Vault, zobacz [co to jest usługa Azure Key Vault](../../key-vault/key-vault-whatis.md)?
* Aby rozpocząć pracę z usługą Azure Key Vault, zobacz [wprowadzenie do usługi Azure Key Vault](../../key-vault/key-vault-get-started.md).
