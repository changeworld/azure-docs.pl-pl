---
title: Zarządzane tożsamości w usłudze Azure HDInsight
description: Zawiera omówienie implementacji zarządzanych tożsamości w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/20/2019
ms.openlocfilehash: 236f79c9060a0d6fdcb0f558373d02f32eba7abb
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905608"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Zarządzane tożsamości w usłudze Azure HDInsight

Tożsamość zarządzana to tożsamość zarejestrowana w Azure Active Directory (Azure AD), której poświadczenia są zarządzane przez platformę Azure. Przy użyciu tożsamości zarządzanych nie trzeba rejestrować nazw głównych usług w usłudze Azure AD ani zachować poświadczeń, takich jak certyfikaty.

Tożsamości zarządzane są używane w usłudze Azure HDInsight w celu uzyskiwania dostępu do usług domenowych Azure AD lub dostępu do plików w Azure Data Lake Storage Gen2 w razie potrzeby.

Istnieją dwa typy tożsamości zarządzanych: przypisane przez użytkownika i przypisane do systemu. Usługa Azure HDInsight używa tożsamości zarządzanych przypisanych przez użytkownika. Tożsamość zarządzana przypisana przez użytkownika jest tworzona jako autonomiczny zasób platformy Azure, który można następnie przypisać do co najmniej jednego wystąpienia usługi platformy Azure. W przeciwieństwie do usługi Azure AD jest tworzona tożsamość zarządzana przypisana przez system, a następnie automatycznie włączana bezpośrednio w konkretnej usłudze platformy Azure. Okres istnienia tożsamości zarządzanej przypisanej przez system jest następnie związany z cyklem życia wystąpienia usługi, na którym jest włączona.

## <a name="hdinsight-managed-identity-implementation"></a>Implementacja tożsamości zarządzanej usługi HDInsight

W usłudze Azure HDInsight zarządzane tożsamości są obsługiwane w każdym węźle klastra. Te składniki tożsamości są jednak używane tylko przez usługę HDInsight. Obecnie nie jest obsługiwana metoda generowania tokenów dostępu przy użyciu tożsamości zarządzanych zainstalowanych w węzłach klastra usługi HDInsight. W przypadku niektórych usług platformy Azure tożsamości zarządzane są implementowane za pomocą punktu końcowego, którego można użyć do uzyskania tokenów dostępu do samodzielnej współpracy z innymi usługami platformy Azure.

## <a name="create-a-managed-identity"></a>Tworzenie tożsamości zarządzanej

Tożsamości zarządzane można tworzyć przy użyciu dowolnej z następujących metod:

* [Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Interfejs wiersza polecenia platformy Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

Pozostałe kroki konfigurowania tożsamości zarządzanej zależą od scenariusza, w którym będą używane.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Scenariusze zarządzanej tożsamości w usłudze Azure HDInsight

Tożsamości zarządzane są używane w usłudze Azure HDInsight w wielu scenariuszach. Szczegółowe instrukcje dotyczące instalacji i konfiguracji można znaleźć w odpowiednich dokumentach:

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity)
* [pakiet Enterprise Security](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Kafka Bring Your Own Key (BYOK)](kafka/apache-kafka-byok.md#get-started-with-byok)

## <a name="faq"></a>Często zadawane pytania
### <a name="what-happens-if-i-delete-the-managed-identity-after-the-cluster-creation"></a>Co się stanie, jeśli usuniesz tożsamość zarządzaną po utworzeniu klastra?
Gdy wymagana jest tożsamość zarządzana, klaster będzie uruchamiał problemy. Obecnie nie ma możliwości aktualizacji lub zmiany zarządzania tożsamości po utworzeniu klastra. Zalecamy, aby upewnić się, że zarządzana tożsamość nie została usunięta podczas środowiska uruchomieniowego klastra. Alternatywnie można utworzyć ponownie klaster i przypisać nową tożsamość zarządzaną.

## <a name="next-steps"></a>Następne kroki

* [Czym są tożsamości zarządzane dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md)
