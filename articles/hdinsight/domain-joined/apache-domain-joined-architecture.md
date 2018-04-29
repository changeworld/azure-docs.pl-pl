---
title: Architektura przyłączonych do domeny usługi Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zaplanować usługę HDInsight przyłączoną do domeny.
services: hdinsight
documentationcenter: ''
author: omidm1
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: omidm
ms.openlocfilehash: e366a9b73ee678c78063240838b399c88ae633cc
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Planowanie klastrów Hadoop przyłączonych do domeny platformy Azure w usłudze HDInsight

Standardowa klastra usługi HDInsight to klaster jednego użytkownika. Odpowiada to większości firm, które mają mniejsze zespoły ds. aplikacji tworzące obciążenia składające się z dużych ilości danych. Jak Hadoop uzyskane popularne, wiele przedsiębiorstw uruchomiona przenoszenie kierunku modelu, w którym klastry są zarządzane przez zespoły IT i wiele aplikacji zespoły udziału klastrów. W związku z tym użytkownicy usługi Azure HDInsight często domagają się wprowadzenia obsługi klastrów przeznaczonych dla wielu użytkowników.

Zamiast tworzenia własnego wielodostępnym uwierzytelniania i autoryzacji, HDInsight polega na najbardziej popularnych dostawcy tożsamości — Active Directory (AD). Funkcji zaawansowanych zabezpieczeń w usłudze AD może służyć do zarządzania dostępem wielu użytkowników uwierzytelniania w usłudze HDInsight. Dzięki integracji usługi HDInsight z usługą Active Directory, może komunikować się z klastrów przy użyciu poświadczeń usługi AD. Maszyny wirtualne w usłudze HDInsight są przyłączone do domeny do usługi AD i jest sposób HDInsight mapowania użytkownika AD lokalnego użytkownika Hadoop, dlatego wszystkie usługi działające w usłudze HDInsight (Ambari, Hive serwera, zakres, Spark thrift serwera i inne) pracy bezproblemowo dla tego uwierzytelnionego użytkownika. Administratorzy mogą tworzyć zasady autoryzacji silne używanie Apache zakres w celu zapewnienia kontroli dostępu opartej na rolach dla zasobów w usłudze HDInsight.


## <a name="integrate-hdinsight-with-active-directory"></a>Integracja usługi HDInsight z usługą Active Directory

Podczas integracji usługi HDInsight z usługą Active Directory, węzły klastra usługi HDInsight są przyłączonych do domeny usługi AD. Zabezpieczeń protokołu Kerberos jest skonfigurowany do składników platformy Hadoop w klastrze. Dla poszczególnych składników Hadoop nazwy głównej usługi jest tworzony na usłudze Active Directory. Tworzona jest również odpowiednią maszynę, główną dla każdego komputera, który jest przyłączony do domeny. Te nazwy główne usług i podmiotów maszyny można zajmowały usługi Active Directory. W związku z tym jest wymagane do jednostki organizacyjnej (OU) w usłudze Active Directory, rozmieszczenia wystawcy. 

Podsumowując, należy skonfigurować środowisko z:

- Kontroler domeny usługi Active Directory z LDAPS skonfigurowane.
- Łączność z siecią wirtualną w usłudze HDInsight do kontrolera domeny usługi Active Directory.
- Jednostka organizacyjna utworzone w usłudze Active Directory.
- Konto usługi ma uprawnienia do:

    - Tworzenie nazwy główne usług, w jednostce Organizacyjnej.
    - Przyłączanie komputerów do domeny i utworzyć podmiotów maszyny w jednostce Organizacyjnej.

Poniższy zrzut ekranu przedstawia jednostki Organizacyjnej utworzone w domenie contoso.com. Niektóre nazwy główne usług i podmiotów zabezpieczeń komputera są wyświetlane również na zrzucie ekranu.

![Jednostki organizacyjnej klastrów HDInsight przyłączone do domeny](./media/apache-domain-joined-architecture/hdinsight-domain-joined-ou.png).

### <a name="the-way-of-bringing-your-own-active-directory-domain-controllers"></a>Sposób zapewniania własnych kontrolerów domeny usługi Active Directory

- **Azure Active Directory Domain Services**: ta usługa udostępnia zarządzanej domeny usługi Active Directory, która jest w pełni zgodny z usługi Active Directory systemu Windows Server. Microsoft odpowiada on za zarządzanie, poprawki i monitorowania domeny usługi AD. Można wdrożyć klaster bez obaw o zachowaniu kontrolerów domeny. Użytkowników, grup i haseł są synchronizowane z usługi Azure Active Directory, umożliwiając użytkownikom na logowanie się do klastra przy użyciu swoich poświadczeń firmowych w. Aby uzyskać więcej informacji, zobacz [przyłączonych do domeny skonfiguruj HDInsight clusters przy użyciu usługi Azure Active Directory Domain Services](./apache-domain-joined-configure-using-azure-adds.md).

> [!NOTE]
> Usługi Active Directory na maszynach wirtualnych Azure IaaS nie jest już obsługiwana.

## <a name="next-steps"></a>Kolejne kroki
* Aby zarządzać przyłączonymi do domeny klastrami usługi HDInsight, zobacz [Manage Domain-joined HDInsight clusters](apache-domain-joined-manage.md) (Zarządzanie przyłączonymi do domeny klastrami usługi HDInsight).
* Aby znaleźć informacje na temat konfigurowania zasad usługi Hive i uruchamiania zapytań usługi Hive, zobacz [Konfigurowanie zasad usługi Hive dla przyłączonych do domeny klastrów usługi HDInsight](apache-domain-joined-run-hive.md).
* Uruchamianie zapytań Hive przy użyciu protokołu SSH w klastrach HDInsight przyłączonych do domeny, zobacz [używanie SSH z usługą HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
