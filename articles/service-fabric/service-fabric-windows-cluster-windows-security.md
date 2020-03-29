---
title: Zabezpieczanie klastra działającego w systemie Windows przy użyciu zabezpieczeń systemu Windows
description: Dowiedz się, jak skonfigurować zabezpieczenia węzła do węzła i klient-węzeł w autonomicznym klastrze uruchomionym w systemie Windows przy użyciu zabezpieczeń systemu Windows.
author: dkkapur
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: dekapur
ms.openlocfilehash: 46be6acc1ef08770826a2e020c8930eba0787791
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774444"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-windows-security"></a>Zabezpieczanie autonomicznego klastra w systemie Windows przy użyciu zabezpieczeń systemu Windows
Aby zapobiec nieautoryzowanemu dostępowi do klastra sieci szkieletowej usług, należy zabezpieczyć klaster. Zabezpieczenia są szczególnie ważne, gdy klaster uruchamia obciążenia produkcyjne. W tym artykule opisano sposób konfigurowania zabezpieczeń węzeł-węzeł i klient-węzeł przy użyciu zabezpieczeń systemu Windows w pliku *ClusterConfig.JSON.*  Proces odpowiada kroku zabezpieczeń konfigurowania [Tworzenie autonomicznego klastra działającego w systemie Windows](service-fabric-cluster-creation-for-windows-server.md). Aby uzyskać więcej informacji o tym, jak sieci szkieletowej usług używa zabezpieczeń systemu Windows, zobacz [Scenariusze zabezpieczeń klastra](service-fabric-cluster-security.md).

> [!NOTE]
> Należy rozważyć wybór zabezpieczeń węzeł do węzła ostrożnie, ponieważ nie ma uaktualnienia klastra z jednego wyboru zabezpieczeń do drugiego. Aby zmienić wybór zabezpieczeń, należy odbudować pełny klaster.
>
>

## <a name="configure-windows-security-using-gmsa"></a>Konfigurowanie zabezpieczeń systemu Windows przy użyciu gMSA  
Przykładowy plik *konfiguracyjny ClusterConfig.gMSA.Windows.MultiMachine.JSON* pobrany za pomocą serwera [Microsoft.Azure.ServiceFabric.WindowsServer.\< wersja>.zip](https://go.microsoft.com/fwlink/?LinkId=730690) samodzielny pakiet klastra zawiera szablon do konfigurowania zabezpieczeń systemu Windows przy użyciu [konta usługi zarządzanej grupy (gMSA)](https://technet.microsoft.com/library/hh831782.aspx):  

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {  
        "ClustergMSAIdentity": "[gMSA Identity]",
        "ClusterSPN": "[Registered SPN for the gMSA account]",
        "ClientIdentities": [
            {
                "Identity": "domain\\username",
                "IsAdmin": true
            }
        ]
    }
}
```

| **Ustawienie konfiguracji** | **Opis** |
| --- | --- |
| Typ klastra z klasterem |Ustaw *system Windows,* aby włączyć zabezpieczenia systemu Windows dla komunikacji węzeł-węzeł.  | 
| Typ serwera z programem |Ustaw *system Windows,* aby włączyć zabezpieczenia systemu Windows dla komunikacji klient-węzeł. |
| Nieidentyfikowalności systemu Windows |Zawiera tożsamości klastra i klienta. |
| ClustergMSAIdentity (Grupa GsA Nieadekwikowość) |Konfiguruje zabezpieczenia między węzłami. Konto usługi zarządzanej przez grupę. |
| ClusterSPN |Zarejestrowana nazwa SPN dla konta gMSA|
| Tożsamość klienta |Konfiguruje zabezpieczenia między klientami. Tablica kont użytkowników klienta. |
| Tożsamość |Dodaj użytkownika domeny, domenę\nazwę użytkownika, dla tożsamości klienta. |
| IsAdmin ( IsAdmin ) |Ustaw wartość true, aby określić, że użytkownik domeny ma dostęp do klienta administratora lub false dla dostępu klienta użytkownika. |

> [!NOTE]
> Wartość ClustergMSAIdentity musi byćmysfgmsa@mydomainw formacie " ".

[Zabezpieczenia węzła do węzła](service-fabric-cluster-security.md#node-to-node-security) są konfigurowane przez ustawienie **klastraGMSAIdentity,** gdy sieci szkieletowej usług musi działać w ramach gMSA. Aby budować relacje zaufania między węzłami, muszą być świadomi siebie nawzajem. Można to osiągnąć na dwa różne sposoby: określ konto usługi zarządzanej grupy, które zawiera wszystkie węzły w klastrze lub Określ grupę maszyn domeny, która zawiera wszystkie węzły w klastrze. Zdecydowanie zaleca się stosowanie metody [gMSA (Group Managed Service Account),](https://technet.microsoft.com/library/hh831782.aspx) szczególnie w przypadku większych klastrów (więcej niż 10 węzłów) lub klastrów, które mogą się powiększać lub zmniejszać.  
Takie podejście nie wymaga utworzenia grupy domen, dla której administratorom klastrów przyznano prawa dostępu do dodawania i usuwania elementów członkowskich. Konta te są również przydatne do automatycznego zarządzania hasłami. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do grupowych kont usług zarządzanych](https://technet.microsoft.com/library/jj128431.aspx).  
 
[Zabezpieczenia klienta do węzła](service-fabric-cluster-security.md#client-to-node-security) są konfigurowane przy użyciu **clientidentities**. Aby ustanowić zaufanie między klientem a klastrem, należy skonfigurować klaster, aby wiedział, którym tożsamościom klienta może ufać. Można to zrobić na dwa różne sposoby: określ użytkowników grupy domen, którzy mogą łączyć się lub określić użytkowników węzła domeny, którzy mogą się łączyć. Sieci szkieletowej usług obsługuje dwa różne typy kontroli dostępu dla klientów, którzy są podłączone do klastra sieci szkieletowej usług: administrator i użytkownik. Kontrola dostępu umożliwia administratorowi klastra ograniczenie dostępu do niektórych typów operacji klastra dla różnych grup użytkowników, dzięki czemu klaster jest bezpieczniejszy.  Administratorzy mają pełny dostęp do funkcji zarządzania (w tym funkcji odczytu/zapisu). Użytkownicy domyślnie mają tylko dostęp do odczytu do funkcji zarządzania (na przykład możliwości zapytań) i możliwość rozpoznawania aplikacji i usług. Aby uzyskać więcej informacji na temat kontroli dostępu, zobacz [Kontrola dostępu oparta na rolach dla klientów sieci szkieletowej usług](service-fabric-cluster-security-roles.md).  
 
Poniższa przykładowa sekcja **zabezpieczeń** konfiguruje zabezpieczenia systemu Windows przy użyciu gMSA i określa, że maszyny w *ServiceFabric.clusterA.contoso.com* gMSA są częścią klastra i że *contoso\usera* ma dostęp do klienta administratora:  
  
```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClustergMSAIdentity" : "ServiceFabric.clusterA.contoso.com",
        "ClusterSPN" : "http/servicefabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
            "IsAdmin": true
        }]
    }
}
```
  
## <a name="configure-windows-security-using-a-machine-group"></a>Konfigurowanie zabezpieczeń systemu Windows przy użyciu grupy maszyn  
Ten model jest przestarzały. Zaleca się stosowanie gMSA, jak opisano powyżej. Przykładowy plik *konfiguracyjny ClusterConfig.Windows.MultiMachine.JSON* pobrany za pomocą pliku [Microsoft.Azure.ServiceFabric.WindowsServer.\< wersja>.zip](https://go.microsoft.com/fwlink/?LinkId=730690) samodzielny pakiet klastra zawiera szablon do konfigurowania zabezpieczeń systemu Windows.  Zabezpieczenia systemu Windows są konfigurowane w sekcji **Właściwości:** 

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "[domain\machinegroup]",
        "ClientIdentities": [{
            "Identity": "[domain\username]",
            "IsAdmin": true
        }]
    }
}
```

| **Ustawienie konfiguracji** | **Opis** |
| --- | --- |
| Typ klastra z klasterem |Ustaw *system Windows,* aby włączyć zabezpieczenia systemu Windows dla komunikacji węzeł-węzeł.  |
| Typ serwera z programem |Ustaw *system Windows,* aby włączyć zabezpieczenia systemu Windows dla komunikacji klient-węzeł. |
| Nieidentyfikowalności systemu Windows |Zawiera tożsamości klastra i klienta. |
| Nieidentytet klastra |Użyj nazwy grupy maszyn, domain\machinegroup, aby skonfigurować zabezpieczenia węzła do węzła. |
| Tożsamość klienta |Konfiguruje zabezpieczenia między klientami. Tablica kont użytkowników klienta. |  
| Tożsamość |Dodaj użytkownika domeny, domenę\nazwę użytkownika, dla tożsamości klienta. |  
| IsAdmin ( IsAdmin ) |Ustaw wartość true, aby określić, że użytkownik domeny ma dostęp do klienta administratora lub false dla dostępu klienta użytkownika. |  

[Zabezpieczenia węzła do węzła](service-fabric-cluster-security.md#node-to-node-security) są konfigurowane przez ustawienie przy użyciu **funkcji ClusterIdentity,** jeśli chcesz użyć grupy maszyn w domenie usługi Active Directory. Aby uzyskać więcej informacji, zobacz [Tworzenie grupy maszyn w usłudze Active Directory](https://msdn.microsoft.com/library/aa545347(v=cs.70).aspx).

[Zabezpieczenia klient-węzeł](service-fabric-cluster-security.md#client-to-node-security) są konfigurowane przy użyciu **clientidentities**. Aby ustanowić zaufanie między klientem a klastrem, należy skonfigurować klaster tak, aby znał tożsamości klientów, którym może ufać klaster. Zaufanie można na dwa różne sposoby:

- Określ użytkowników grupy domen, którzy mogą się łączyć.
- Określ użytkowników węzła domeny, którzy mogą się łączyć.

Sieci szkieletowej usług obsługuje dwa różne typy kontroli dostępu dla klientów, którzy są podłączone do klastra sieci szkieletowej usług: administrator i użytkownik. Kontrola dostępu umożliwia administratorowi klastra ograniczenie dostępu do niektórych typów operacji klastra dla różnych grup użytkowników, co zwiększa bezpieczeństwo klastra.  Administratorzy mają pełny dostęp do funkcji zarządzania (w tym funkcji odczytu/zapisu). Użytkownicy domyślnie mają tylko dostęp do odczytu do funkcji zarządzania (na przykład możliwości zapytań) i możliwość rozpoznawania aplikacji i usług.  

Poniższa przykładowa sekcja **zabezpieczeń** konfiguruje zabezpieczenia systemu Windows, określa, że maszyny w *witrynie ServiceFabric/clusterA.contoso.com* są częścią klastra, oraz określa, że *funkcja CONTOSO\usera* ma dostęp do klienta administratora:

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "ServiceFabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
            "IsAdmin": true
        }]
    }
},
```

> [!NOTE]
> Sieci szkieletowej usług nie należy wdrażać na kontrolerze domeny. Upewnij się, że plik ClusterConfig.json nie zawiera adresu IP kontrolera domeny podczas korzystania z grupy maszynowej lub grupy zarządzanego konta usługi (gMSA).
>
>

## <a name="next-steps"></a>Następne kroki
Po skonfigurowaniu zabezpieczeń systemu Windows w pliku *ClusterConfig.JSON* wznowić proces tworzenia klastra w [programie Tworzenie autonomicznego klastra działającego w systemie Windows](service-fabric-cluster-creation-for-windows-server.md).

Aby uzyskać więcej informacji na temat zabezpieczeń między węzłami, zabezpieczeń między klientami i kontroli dostępu opartego na [rolach, zobacz Scenariusze zabezpieczeń klastra](service-fabric-cluster-security.md).

Zobacz [Łączenie z bezpiecznym klastrem, aby zapoznać](service-fabric-connect-to-secure-cluster.md) się z przykładami łączenia się przy użyciu programu PowerShell lub FabricClient.
