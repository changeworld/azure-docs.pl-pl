---
title: Używanie brokera identyfikatorów (wersja zapoznawcza) do zarządzania poświadczeniami — usługa Azure HDInsight
description: Dowiedz się więcej o brokerze HDInsight ID, aby uprościć uwierzytelnianie klastrów Apache Hadoop przyłączonych do domeny.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: f14cbef2ab568962601b3a407fa979e8f982598d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483013"
---
# <a name="use-id-broker-preview-for-credential-management"></a>Używanie brokera identyfikatorów (wersja zapoznawcza) do zarządzania poświadczeniami

W tym artykule opisano sposób konfigurowania i używania funkcji brokera identyfikatorów w usłudze Azure HDInsight. Za pomocą tej funkcji można zalogować się do apache Ambari za pośrednictwem usługi Azure Multi-Factor Authentication i uzyskać wymagane bilety Protokołu Kerberos bez konieczności tworzenia skrótów haseł w usługach domenowych Usługi azure Active Directory Domain Services (Azure AD DS).

## <a name="overview"></a>Omówienie

ID Broker upraszcza złożone konfiguracje uwierzytelniania w następujących scenariuszach:

* Organizacja polega na federacji, aby uwierzytelnić użytkowników w celu uzyskania dostępu do zasobów w chmurze. Wcześniej, aby używać klastrów pakietu zabezpieczeń usługi HDInsight Enterprise Security Package (ESP), trzeba było włączyć synchronizację skrótów haseł ze środowiska lokalnego do usługi Azure Active Directory. To wymaganie może być trudne lub niepożądane dla niektórych organizacji.

* Budujesz rozwiązania, które wykorzystują technologie, które opierają się na różnych mechanizmach uwierzytelniania. Na przykład Apache Hadoop i Apache Ranger polegają na kerberos, podczas gdy usługa Azure Data Lake Storage opiera się na OAuth.

Broker identyfikatorów zapewnia ujednoliconą infrastrukturę uwierzytelniania i usuwa wymóg synchronizowania skrótów haseł z usługą Azure AD DS. Broker identyfikatorów składa się ze składników uruchomionych w węźle maszyny Wirtualnej systemu Windows Server (węzeł ID Broker) wraz z węzłami bramy klastra. 

Na poniższym diagramie przedstawiono przepływ uwierzytelniania dla wszystkich użytkowników, w tym użytkowników federowanych, po włączeniu ID Broker:

![Przepływ uwierzytelniania za pomocą brokera identyfikatorów](./media/identity-broker/identity-broker-architecture.png)

ID Broker umożliwia logowanie się do klastrów ESP przy użyciu uwierzytelniania wieloskładnikowego, bez podawania żadnych haseł. Jeśli zalogowano się już do innych usług platformy Azure, takich jak witryna Azure portal, możesz zalogować się do klastra USŁUGI HDInsight za pomocą środowiska logowania jednokrotnego.

## <a name="enable-hdinsight-id-broker"></a>Włącz brokera identyfikatorów HDInsight

Aby utworzyć klaster ESP z włączoną funkcją ID Broker, należy wykonać następujące kroki:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wykonaj podstawowe kroki tworzenia klastra ESP. Aby uzyskać więcej informacji, zobacz [Tworzenie klastra HDInsight za pomocą esp](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).
1. Wybierz **włącz brokera identyfikatorów HDInsight**.

Funkcja brokera identyfikatorów doda jedną dodatkową maszynę wirtualną do klastra. Ta maszyna wirtualna jest węzłem Broker identyfikatora i zawiera składniki serwera do obsługi uwierzytelniania. Węzeł Broker identyfikatorów jest domeną przyłączona do domeny usług Azure AD DS.

![Opcja włączenia brokera identyfikatorów](./media/identity-broker/identity-broker-enable.png)

## <a name="tool-integration"></a>Integracja narzędzi

Wtyczka HDInsight [IntelliJ](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib) została zaktualizowana w celu obsługi oauth. Za pomocą tej wtyczki można połączyć się z klastrem i przesłać zadania.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Dostęp SSH bez skrótu hasła w usługach Azure AD DS

Po włączeniu brokera identyfikatorów nadal będziesz potrzebować skrótu hasła przechowywanego w usługach AD DS dla scenariuszy SSH z kontami domeny. Aby SSH do maszyny wirtualnej przyłączone `kinit` do domeny lub uruchomić polecenie, należy podać hasło. 

Uwierzytelnianie SSH wymaga skrótu, który ma być dostępny w usługach Azure AD DS. Jeśli chcesz używać SSH tylko dla scenariuszy administracyjnych, można utworzyć jedno konto tylko w chmurze i użyć go do SSH do klastra. Inni użytkownicy mogą nadal korzystać z narzędzi Ambari lub HDInsight (takich jak wtyczka IntelliJ) bez konieczności używania skrótu hasła w usługach Azure AD DS.

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie klastra usługi HDInsight przy użyciu pakietu zabezpieczeń przedsiębiorstwa przy użyciu usług domenowych Active Directory azure](apache-domain-joined-configure-using-azure-adds.md)
* [Synchronizowanie użytkowników usługi Azure Active Directory z klastrem usługi HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
* [Monitorowanie wydajności klastra](../hdinsight-key-scenarios-to-monitor.md)
