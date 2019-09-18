---
title: Zarządzane tożsamości w usłudze Azure HDInsight
description: Zawiera omówienie implementacji zarządzanych tożsamości w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 02ea164a1fa29b494801623d418be73fc47d069c
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077080"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Zarządzane tożsamości w usłudze Azure HDInsight

Tożsamość zarządzana to tożsamość zarejestrowana w Azure Active Directory (Azure AD), której poświadczenia są zarządzane przez platformę Azure. Przy użyciu tożsamości zarządzanych nie trzeba rejestrować nazw głównych usług w usłudze Azure AD ani zachować poświadczeń, takich jak certyfikaty.

Tożsamości zarządzane mogą być używane w usłudze Azure HDInsight, aby umożliwić Klastrom dostęp do usług domenowych Azure AD, dostęp Azure Key Vault lub dostęp do plików w Azure Data Lake Storage Gen2.

Istnieją dwa typy tożsamości zarządzanych: przypisane przez użytkownika i przypisane do systemu. Usługa Azure HDInsight używa tożsamości zarządzanych przypisanych przez użytkownika. Tożsamość zarządzana przypisana przez użytkownika jest tworzona jako autonomiczny zasób platformy Azure, który można następnie przypisać do jednego lub większej liczby wystąpień usługi platformy Azure. W przeciwieństwie do usługi Azure AD jest tworzona tożsamość zarządzana przypisana przez system, a następnie automatycznie włączana bezpośrednio w konkretnej usłudze platformy Azure. Okres istnienia tożsamości zarządzanej przypisanej przez system jest następnie związany z cyklem życia wystąpienia usługi, na którym jest włączona.

## <a name="hdinsight-managed-identity-implementation"></a>Implementacja tożsamości zarządzanej usługi HDInsight

W usłudze Azure HDInsight zarządzane tożsamości są obsługiwane w każdym węźle klastra. Te składniki tożsamości są jednak używane tylko przez usługę HDInsight. Obecnie nie ma obsługiwanej metody generowania tokenów dostępu przy użyciu zarządzanych tożsamości zainstalowanych w węzłach klastra usługi HDInsight. W przypadku niektórych usług platformy Azure tożsamości zarządzane są implementowane za pomocą punktu końcowego, którego można użyć do uzyskania tokenów dostępu do samodzielnej współpracy z innymi usługami platformy Azure.

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

## <a name="next-steps"></a>Następne kroki

* [Czym są tożsamości zarządzane dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md)
