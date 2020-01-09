---
title: Korzystanie z usługi ID brokera (wersja zapoznawcza) na potrzeby zarządzania poświadczeniami — Azure HDInsight
description: Dowiedz się więcej o usłudze HDInsight ID Broker, aby uprościć uwierzytelnianie dla przyłączonych do domeny Apache Hadoop klastrów.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: f14cbef2ab568962601b3a407fa979e8f982598d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483013"
---
# <a name="use-id-broker-preview-for-credential-management"></a>Użyj usługi ID brokera (wersja zapoznawcza) do zarządzania poświadczeniami

W tym artykule opisano sposób konfigurowania funkcji brokera identyfikatorów w usłudze Azure HDInsight i korzystania z niej. Korzystając z tej funkcji, możesz zalogować się do usługi Apache Ambari za pomocą platformy Azure Multi-Factor Authentication i uzyskać wymaganych biletów Kerberos bez konieczności używania skrótów haseł w Azure Active Directory Domain Services (Azure AD DS).

## <a name="overview"></a>Przegląd

Broker identyfikatorów upraszcza konfiguracje uwierzytelniania złożonego w następujących scenariuszach:

* Organizacja korzysta z Federacji, aby uwierzytelniać użytkowników w celu uzyskiwania dostępu do zasobów w chmurze. Wcześniej aby można było korzystać z klastrów usługi HDInsight pakiet Enterprise Security (ESP), należy włączyć synchronizację skrótów haseł w środowisku lokalnym, aby Azure Active Directory. To wymaganie może być trudne lub niepożądane w niektórych organizacjach.

* Tworzysz rozwiązania korzystające z technologii, które korzystają z różnych mechanizmów uwierzytelniania. Na przykład Apache Hadoop i Apache Ranger polegają na protokole Kerberos, natomiast Azure Data Lake Storage korzysta z protokołu OAuth.

Broker identyfikatora zapewnia ujednoliconą infrastrukturę uwierzytelniania i eliminuje wymaganie synchronizacji skrótów haseł do usługi Azure AD DS. Identyfikator brokera składa się z składników uruchomionych na maszynie wirtualnej z systemem Windows Server (węzłem brokera identyfikatorów), wraz z węzłami bramy klastra. 

Na poniższym diagramie przedstawiono przepływ uwierzytelniania dla wszystkich użytkowników, w tym użytkowników federacyjnych, po włączeniu brokera identyfikatorów:

![Przepływ uwierzytelniania z brokerem identyfikatorów](./media/identity-broker/identity-broker-architecture.png)

Identyfikator brokera umożliwia logowanie do klastrów ESP przy użyciu Multi-Factor Authentication bez podawania żadnych haseł. Jeśli zalogowano się już do innych usług platformy Azure, takich jak Azure Portal, możesz zalogować się do klastra usługi HDInsight przy użyciu logowania jednokrotnego (SSO).

## <a name="enable-hdinsight-id-broker"></a>Włącz brokera identyfikatorów usługi HDInsight

Aby utworzyć klaster ESP z włączonym brokerem identyfikatorów, wykonaj następujące czynności:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Postępuj zgodnie z podstawowymi procedurami tworzenia klastra ESP. Aby uzyskać więcej informacji, zobacz [Tworzenie klastra usługi HDInsight przy użyciu protokołu ESP](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).
1. Wybierz pozycję **Włącz brokera identyfikatorów HDInsight**.

Funkcja brokera identyfikatorów doda do klastra jedną dodatkową maszynę wirtualną. Ta maszyna wirtualna jest węzłem brokera identyfikatorów i zawiera składniki serwera do obsługi uwierzytelniania. Węzeł brokera identyfikatorów jest przyłączony do domeny AD DS platformy Azure.

![Opcja włączenia brokera identyfikatorów](./media/identity-broker/identity-broker-enable.png)

## <a name="tool-integration"></a>Integracja narzędzi

[Wtyczka IntelliJ](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib) usługi HDInsight została zaktualizowana w celu obsługi protokołu OAuth. Ta wtyczka służy do nawiązywania połączenia z klastrem i przesyłania zadań.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Dostęp SSH bez skrótu hasła na platformie Azure AD DS

Po włączeniu brokera identyfikatora nadal będzie potrzebny skrót hasła przechowywany w usłudze Azure AD DS dla scenariuszy SSH z kontami domeny. Aby zapewnić połączenie SSH z przyłączoną do domeny maszyną wirtualną lub uruchomić polecenie `kinit`, należy podać hasło. 

Uwierzytelnianie SSH wymaga, aby skrót był dostępny na platformie Azure AD DS. Jeśli chcesz używać protokołu SSH tylko w przypadku scenariuszy administracyjnych, możesz utworzyć jedno konto tylko w chmurze i używać go do protokołu SSH do klastra. Inni użytkownicy nadal mogą używać narzędzi Ambari lub HDInsight (takich jak wtyczka IntelliJ) bez potrzeby używania skrótu hasła na platformie Azure AD DS.

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie klastra usługi HDInsight z pakiet Enterprise Security przy użyciu Azure Active Directory Domain Services](apache-domain-joined-configure-using-azure-adds.md)
* [Synchronizowanie użytkowników Azure Active Directory z klastrem usługi HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
* [Monitorowanie wydajności klastra](../hdinsight-key-scenarios-to-monitor.md)
