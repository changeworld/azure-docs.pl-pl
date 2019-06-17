---
title: Zarządzanych tożsamości w usłudze Azure HDInsight
description: Zawiera omówienie wdrożenia zarządzanych tożsamości w usłudze Azure HDInsight.
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: hrasheed
ms.openlocfilehash: 30631c4b71d1e8f3b0380a39bab49b900df32621
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427633"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Zarządzanych tożsamości w usłudze Azure HDInsight

Tożsamość zarządzana jest tożsamością zarejestrowane w usłudze Azure Active Directory (Azure AD) którego poświadczenia są zarządzane przez platformę Azure. Za pomocą tożsamości zarządzanej nie trzeba zarejestrować nazwy główne usług w usłudze Azure AD lub Obsługa poświadczeń, takich jak certyfikaty.

Zarządzanych tożsamości może służyć w usłudze Azure HDInsight, aby zezwolić na dostęp do usług domenowych w usłudze Azure AD, dostęp do usługi Azure Key Vault lub uzyskać dostęp do plików w usłudze Azure Data Lake magazynu Gen2 klastrów.

Istnieją dwa typy zarządzanych tożsamości: użytkownik przypisany i przypisana przez system. Usługa Azure HDInsight korzysta z zarządzanych tożsamości przypisanych przez użytkownika. Użytkownik przypisany tożsamości zarządzanej jest tworzony jako autonomiczny zasobów platformy Azure, której następnie można przypisać do co najmniej jedno wystąpienie usługi platformy Azure. Z kolei przypisany systemowo tożsamości zarządzanej jest tworzone w usłudze Azure AD i włączone bezpośrednio w wystąpieniu określonej usługi platformy Azure automatycznie. Czas życia tej tożsamości zarządzanej przypisana przez system jest następnie wiązane cyklu życia wystąpienia usługi, który został włączony na.

## <a name="hdinsight-managed-identity-implementation"></a>HDInsight zarządzana implementacja tożsamości

W usłudze Azure HDInsight zarządzanych tożsamości są aprowizowane w każdym węźle klastra. Te składniki tożsamości, jednak są tylko może być używany przez usługę HDInsight. Nie istnieje obecnie obsługiwana metoda służących do generowania tokenów dostępu przy użyciu zarządzanych tożsamości instalowane w węzłach klastra HDInsight. W przypadku niektórych usług platformy Azure zarządzanych tożsamości są implementowane przy użyciu punktu końcowego, który można użyć w celu uzyskania tokenów dostępu do interakcji z innymi usługami platformy Azure na własną rękę.

## <a name="create-a-managed-identity"></a>Utwórz tożsamość zarządzaną

Zarządzanych tożsamości mogą być tworzone przy użyciu dowolnej z następujących metod:

* [Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Interfejs wiersza polecenia platformy Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

Pozostałe kroki konfigurowania tożsamości zarządzanej zależą od scenariusza, w którym będzie używany.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Scenariusze tożsamość zarządzaną w usłudze Azure HDInsight

Zarządzanych tożsamości są używane w usłudze Azure HDInsight w wielu scenariuszach. Zobacz powiązane dokumenty konfiguracji szczegółowe i instrukcje dotyczące konfiguracji:

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity)
* [Pakiet Enterprise Security](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Platforma Kafka Bring Your Own Key (BYOK)](kafka/apache-kafka-byok.md#get-started-with-byok)

## <a name="next-steps"></a>Kolejne kroki

* [Czym są tożsamości zarządzane dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md)
