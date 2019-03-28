---
title: Zabezpieczanie klastra z systemem Windows przy użyciu zabezpieczeń Windows | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować zabezpieczenia węzła do węzła i klient węzeł klastra autonomicznego w systemie Windows przy użyciu zabezpieczeń Windows.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ce3bf686-ffc4-452f-b15a-3c812aa9e672
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/24/2017
ms.author: dekapur
ms.openlocfilehash: d599414978c44407acc1a449f853607d6a40c495
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541013"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-windows-security"></a>Zabezpieczanie klastra autonomicznego w Windows przy użyciu zabezpieczeń Windows
Aby zapobiec nieautoryzowanemu dostępowi do klastra usługi Service Fabric, należy zabezpieczyć klaster. Bezpieczeństwo jest szczególnie ważne, gdy klaster działa obciążeń produkcyjnych. W tym artykule opisano sposób konfigurowania zabezpieczeń między węzłami i węzeł klienta przy użyciu zabezpieczeń Windows w *ClusterConfig.JSON* pliku.  Proces odnosi się do kroku zabezpieczeń Konfiguruj [Tworzenie klastra autonomicznego w systemie Windows](service-fabric-cluster-creation-for-windows-server.md). Aby uzyskać więcej informacji o używaniu Windows zabezpieczeń w usłudze Service Fabric, zobacz [scenariusze zabezpieczeń klastra](service-fabric-cluster-security.md).

> [!NOTE]
> Należy rozważyć wybór zabezpieczeń między węzłami ponieważ nie istnieje żadne uaktualniania klastra z wybraną zabezpieczeniami do innego. Aby zmienić wybór zabezpieczeń, musisz odbudować pełną klastra.
>
>

## <a name="configure-windows-security-using-gmsa"></a>Konfigurowanie zabezpieczeń Windows przy użyciu gMSA  
Przykład *ClusterConfig.gMSA.Windows.MultiMachine.JSON* pliku konfiguracji pobranej z [Microsoft.Azure.ServiceFabric.WindowsServer.\< w wersji > zip](https://go.microsoft.com/fwlink/?LinkId=730690) autonomicznego klastra pakiet zawiera szablon służący do konfigurowania zabezpieczeń Windows przy użyciu [konta usługi zarządzanego przez grupę (gMSA)](https://technet.microsoft.com/library/hh831782.aspx):  

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

| **Ustawienia konfiguracji** | **Opis** |
| --- | --- |
| ClusterCredentialType |Ustaw *Windows* umożliwiające Windows zabezpieczenia komunikacji między węzłami.  | 
| ServerCredentialType |Ustaw *Windows* umożliwiające zabezpieczeń Windows komunikacji z klientem i węzłem. |
| WindowsIdentities |Zawiera tożsamość klastra i klienta. |
| ClustergMSAIdentity |Służy do konfigurowania zabezpieczeń między węzłami. Konto usługi zarządzane przez grupę. |
| ClusterSPN |Zarejestrowane nazwy SPN dla konta gMSA|
| ClientIdentities |Służy do konfigurowania zabezpieczeń węzeł klienta. Tablica konta użytkownika klienta. |
| Tożsamość |Dodaj użytkownika domeny, domena\nazwa_użytkownika, tożsamość klienta. |
| IsAdmin |Ustaw wartość true, aby określić, czy użytkownik domeny ma administrator dostępu klienta lub wartość false dla dostępu klientów użytkownika. |

> [!NOTE]
> Wartość ClustergMSAIdentity nie może zawierać nazwy domeny i może być tylko nazwa konta grupy usługi zarządzanej. I.E. "mysfgmsa" jest poprawny, a oba "moja_domena / / mysfgmsa" lub "mysfgmsa@mydomain" są nieprawidłowe; ponieważ domena jest implikowany przez komputer hosta.

[Węzeł, aby węzeł zabezpieczeń](service-fabric-cluster-security.md#node-to-node-security) jest skonfigurowany, ustawiając **ClustergMSAIdentity** po usługi Service fabric musi zostać uruchomiony w ramach gMSA. Aby można było utworzyć relacje zaufania między węzłami, muszą być umieszczane pamiętać od siebie. Można to zrobić na dwa sposoby: Określ grupy zarządzane konta usługi, która zawiera wszystkie węzły w klastrze lub określ grupy maszyn domeny, która zawiera wszystkie węzły w klastrze. Zdecydowanie zalecamy używanie [konta usługi zarządzanego przez grupę (gMSA)](https://technet.microsoft.com/library/hh831782.aspx) podejście, szczególnie w przypadku większych klastrów (ponad 10 węzłów) lub w przypadku klastrów, które mogą mieć możliwość zwiększania i zmniejszania.  
To podejście nie jest wymagane tworzenie grupy domeny, dla którego administratorzy klastra przyznano prawa dostępu do dodawania i usuwania elementów członkowskich. Te konta są również przydatne w przypadku automatyczne zarządzanie hasłami. Aby uzyskać więcej informacji, zobacz [rozpoczęcie korzystania z kont usług zarządzanych przez grupę](https://technet.microsoft.com/library/jj128431.aspx).  
 
[Klienta zabezpieczeń węzła](service-fabric-cluster-security.md#client-to-node-security) została skonfigurowana przy użyciu **ClientIdentities**. Aby ustanowić zaufanie między klientem a klastrem, należy skonfigurować klaster, aby wiedzieć, którego komputera klienckiego tożsamości, w którym można zaufać. Można to zrobić na dwa sposoby: Określ użytkowników grupy domeny, które mogą łączyć i określ użytkowników węzeł domeny, którymi mogą nawiązywać połączenie. Usługa Service Fabric obsługuje dwa typy kontroli dostępu w różnych klientów, które są podłączone do klastra usługi Service Fabric: administratora i użytkownika. Kontrola dostępu umożliwia administrator klastra ograniczyć dostęp do niektórych typów operacji klastra dla różnych grup użytkowników, dzięki czemu klaster jest bardziej bezpieczne.  Administratorzy mają pełny dostęp do możliwości zarządzania (w tym możliwości odczytu/zapisu). Użytkownicy, domyślnie mają tylko dostęp do odczytu do możliwości zarządzania (na przykład, zapytanie możliwości) i możliwość usuwania aplikacji i usług. Aby uzyskać więcej informacji na temat kontroli dostępu, zobacz [kontrola dostępu oparta na rolach dla klientów usługi Service Fabric](service-fabric-cluster-security-roles.md).  
 
Poniższy przykład **zabezpieczeń** sekcji konfiguruje zabezpieczeń Windows przy użyciu gMSA i określa, że na komputerach należących do *ServiceFabric.clusterA.contoso.com* gMSA są częścią klastra i że  *CONTOSO\usera* ma dostęp klient administratora:  
  
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
  
## <a name="configure-windows-security-using-a-machine-group"></a>Konfigurowanie zabezpieczeń Windows przy użyciu grupy maszyn  
Ten model jest wycofywany. Zaleca się używania konta usługi zarządzanego zgodnie z powyższym opisem. Przykład *ClusterConfig.Windows.MultiMachine.JSON* pliku konfiguracji pobranej z [Microsoft.Azure.ServiceFabric.WindowsServer.\< w wersji > zip](https://go.microsoft.com/fwlink/?LinkId=730690) autonomicznego klastra pakiet zawiera szablon służący do konfigurowania zabezpieczeń Windows.  Zabezpieczenia Windows jest skonfigurowany w **właściwości** sekcji: 

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

| **Ustawienia konfiguracji** | **Opis** |
| --- | --- |
| ClusterCredentialType |Ustaw *Windows* umożliwiające Windows zabezpieczenia komunikacji między węzłami.  |
| ServerCredentialType |Ustaw *Windows* umożliwiające zabezpieczeń Windows komunikacji z klientem i węzłem. |
| WindowsIdentities |Zawiera tożsamość klastra i klienta. |
| ClusterIdentity |Nazwa grupy maszyn, domain\machinegroup, umożliwia konfigurowanie zabezpieczeń między węzłami. |
| ClientIdentities |Służy do konfigurowania zabezpieczeń węzeł klienta. Tablica konta użytkownika klienta. |  
| Tożsamość |Dodaj użytkownika domeny, domena\nazwa_użytkownika, tożsamość klienta. |  
| IsAdmin |Ustaw wartość true, aby określić, czy użytkownik domeny ma administrator dostępu klienta lub wartość false dla dostępu klientów użytkownika. |  

[Węzeł, aby węzeł zabezpieczeń](service-fabric-cluster-security.md#node-to-node-security) jest konfigurowana przy użyciu ustawienia **ClusterIdentity** Jeśli chcesz użyć grupy na komputerze w domenie usługi Active Directory. Aby uzyskać więcej informacji, zobacz [Utwórz grupę maszyny w usłudze Active Directory](https://msdn.microsoft.com/library/aa545347(v=cs.70).aspx).

[Węzeł klienta zabezpieczeń](service-fabric-cluster-security.md#client-to-node-security) jest konfigurowana przy użyciu **ClientIdentities**. Aby ustanowić zaufanie między klientem a klastrem, należy skonfigurować klaster, aby znać klienta tożsamości, które mogą ufać klastra. Można ustanowić relacji zaufania na dwa sposoby:

- Określ użytkowników grupy domeny, którymi mogą nawiązywać połączenie.
- Określ użytkowników węzeł domeny, którymi mogą nawiązywać połączenie.

Usługa Service Fabric obsługuje dwa typy kontroli dostępu w różnych klientów, które są podłączone do klastra usługi Service Fabric: administratora i użytkownika. Access control umożliwia administrator klastra ograniczyć dostęp do niektórych typów operacji klastra dla różnych grup użytkowników, co sprawia, że klaster jest bardziej bezpieczne.  Administratorzy mają pełny dostęp do możliwości zarządzania (w tym możliwości odczytu/zapisu). Użytkownicy, domyślnie mają tylko dostęp do odczytu do możliwości zarządzania (na przykład, zapytanie możliwości) i możliwość usuwania aplikacji i usług.  

Poniższy przykład **zabezpieczeń** sekcji konfiguruje zabezpieczeń Windows, określa, że na komputerach należących do *ServiceFabric/clusterA.contoso.com* są częścią klastra i określa, że *CONTOSO\usera* ma dostęp klient administratora:

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
> Usługa Service Fabric nie powinny być wdrażane na kontrolerze domeny. Upewnij się, że ClusterConfig.json nie obejmują adres IP kontrolera domeny, korzystając z grupy na komputerze lub konta grupy usługi zarządzanej (gMSA).
>
>

## <a name="next-steps"></a>Kolejne kroki
Po skonfigurowaniu zabezpieczeń Windows w *ClusterConfig.JSON* pliku, wznowienie procesu tworzenia klastra w [Tworzenie klastra autonomicznego w systemie Windows](service-fabric-cluster-creation-for-windows-server.md).

Aby uzyskać więcej informacji na temat sposobu węzeł węzeł zabezpieczeń, węzeł klienta zabezpieczeń i kontroli dostępu opartej na rolach, zobacz [scenariusze zabezpieczeń klastra](service-fabric-cluster-security.md).

Zobacz [nawiązywanie połączenia z zabezpieczonym klastrem](service-fabric-connect-to-secure-cluster.md) przykłady łączenia się przy użyciu programu PowerShell lub FabricClient.
