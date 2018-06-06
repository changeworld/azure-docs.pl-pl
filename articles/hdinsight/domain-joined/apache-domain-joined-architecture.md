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
ms.date: 05/30/2018
ms.author: omidm
ms.openlocfilehash: f4380f5d6ec379d5807f697294623a672bd270ae
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34715245"
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Planowanie klastrów Hadoop przyłączonych do domeny platformy Azure w usłudze HDInsight

Standardowa klastra usługi HDInsight to klaster jednego użytkownika. Odpowiada to większości firm, które mają mniejsze zespoły ds. aplikacji tworzące obciążenia składające się z dużych ilości danych. Każdy użytkownik ma innego klastra dedykowane do siebie na żądanie i zniszcz go, gdy nie są już potrzebne. Jednak w wielu przedsiębiorstwach uruchomić przenoszenie kierunku modelu, w którym klastry są zarządzane przez zespoły IT i wiele aplikacji zespoły udziału klastrów. W związku z tym wielodostępnym dostęp do klastra jest wymagany w usłudze Azure HDInsight dla tych większych przedsiębiorstw.

HDInsight polega na najbardziej popularnych dostawcy tożsamości — Active Directory (AD) w sposób zarządzanych. Dzięki integracji usługi HDInsight za pomocą [Azure Active Directory Domain Services (usługi AAD DS)](../../active-directory-domain-services/active-directory-ds-overview.md), dostęp do klastrów przy użyciu poświadczeń domeny. Maszyny wirtualne w usłudze HDInsight są przyłączonych do domeny podana, więc cała usługi działające w usłudze HDInsight (Ambari, Hive serwera, zakres, Spark thrift serwera i inne) pracy bezproblemowo dla tego uwierzytelnionego użytkownika. Administratorzy mogą tworzyć zasady autoryzacji silne używanie Apache zakres w celu zapewnienia kontroli dostępu opartej na rolach dla zasobów w klastrze.


## <a name="integrate-hdinsight-with-active-directory"></a>Integracja usługi HDInsight z usługą Active Directory

Hadoop open source zależy od protokołu Kerberos do uwierzytelniania i zabezpieczeń. W związku z tym HDInsight węzły klastra są przyłączone do domeny do domeny zarządzanej przez usługi AAD DS. Zabezpieczeń protokołu Kerberos jest skonfigurowany do składników platformy Hadoop w klastrze. Dla poszczególnych składników Hadoop nazwy głównej usługi jest tworzone automatycznie. Tworzona jest również odpowiednią maszynę, główną dla każdego komputera, który jest przyłączony do domeny. Aby przechowywać wystawcy usługi i dla maszyn, jest wymagany do Podaj jednostki organizacyjnej (OU) w ramach kontrolera domeny (AAD DS), rozmieszczenia wystawcy. 

Podsumowując, należy skonfigurować środowisko z:

- Domeny usługi Active Directory (zarządzane przez usługę AAD DS)
- Zabezpieczenia protokołu LDAP (LDAPS) włączone w usługach Domenowych AAD w.
- Właściwe sieci łączności z usługi HDInsight w sieci Wirtualnej do sieci Wirtualnej AAD DS, w przypadku oddzielne sieci wirtualnych dla nich. Maszyny Wirtualnej w sieci Wirtualnej HDI powinien mieć procesów w wierszu, AAD usług domenowych w usłudze przy użyciu sieci Wirtualnej komunikacji równorzędnej. Zarówno HDI, jak i AAD DS są wdrażane w tej samej sieci Wirtualnej, połączenie jest teraz udostępniana automatycznie i nie dalsze akcje nie jest wymagane.
- Jednostki organizacyjnej (OU) [utworzone w usłudze Katalogowej usługi AAD](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)
- Konto usługi ma uprawnienia do:
    - Tworzenie nazwy główne usług, w jednostce Organizacyjnej.
    - Przyłączanie komputerów do domeny i utworzyć podmiotów maszyny w jednostce Organizacyjnej.

Poniższy zrzut ekranu przedstawia jednostki Organizacyjnej utworzone w domenie contoso.com. Niektóre nazwy główne usług i podmiotów zabezpieczeń komputera są wyświetlane również na zrzucie ekranu.

![Jednostki organizacyjnej klastrów HDInsight przyłączone do domeny](./media/apache-domain-joined-architecture/hdinsight-domain-joined-ou.png).

### <a name="different-domain-controllers-setup"></a>Instalator kontrolery domeny inne:
HDInsight aktualnie obsługuje tylko AAD DS jako kontroler domeny głównej, że klaster będzie się komunikował z Kerberise klastra. Jednak innych złożonych konfiguracji AD są również możliwe tak długo, jak prowadzi do włączania dostępu HDI AAD DS.

- **[Azure Active Directory Domain Services (usługi AAD DS)](../../active-directory-domain-services/active-directory-ds-overview.md)**: ta usługa udostępnia domeny zarządzanej, które są całkowicie zgodne z usługi Active Directory systemu Windows Server. Microsoft odpowiada on za zarządzanie, poprawki i domeny w konfiguracji wysokiej Available(HA) monitorowania. Można wdrożyć klaster bez obaw o zachowaniu kontrolerów domeny. Użytkownicy, grupy i hasła są synchronizowane z Azure Active Directory(AAD) [jednokierunkowe Synchronizuj z usługi AAD AAD usług domenowych w usłudze], włączanie użytkownikom zalogować się do klastra przy użyciu tych samych poświadczeń firmowych. Aby uzyskać więcej informacji, zobacz [sposób konfigurowania domeny w usłudze HDInsight clusters przy użyciu usługi AAD DS](./apache-domain-joined-configure-using-azure-adds.md).
- **Lokalnej usługi AD lub AD na maszynach wirtualnych IaaS**: Jeśli masz usługi AD z lokalnymi lub innych AD bardziej złożonych konfiguracji dla domeny, można zsynchronizować te tożsamości usługi AAD przy użyciu AD Connect, a następnie włącz dzierżawy usługi AAD DS na danej reklamy. Ponieważ protokół Kerberos opiera się na skrótów haseł, konieczne będzie [Włączanie synchronizacji skrótów haseł w usłudze Katalogowej AAD](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md). Jeśli używasz federacji z AD federation Services (AD FS), można opcjonalnie Konfiguracja synchronizacji skrótów haseł jako zapasowy na wypadek awarii infrastruktury usług AD FS. Aby uzyskać więcej informacji, zobacz [Włączanie synchronizacji skrótów haseł z synchronizacją AAD Connect](../../active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md). Przy użyciu lokalnej usługi AD lub AD w samych maszyn wirtualnych IaaS, bez usługi AAD i usługa AAD DS nie jest obsługiwana konfiguracja za dołączenie do domeny klastra HDI.

## <a name="next-steps"></a>Kolejne kroki
* [Konfigurowanie klastrów HDInsight przyłączonych do domeny](apache-domain-joined-configure-using-azure-adds.md).
* [Konfigurowanie zasad gałęzi dla klastrów usługi HDInsight przyłączonych do domeny](apache-domain-joined-run-hive.md).
* [Zarządzanie przyłączonych do domeny w usłudze hdinsight](apache-domain-joined-manage.md). 
