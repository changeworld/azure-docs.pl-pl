---
title: Zabezpieczanie klastra z systemem Windows przy użyciu zabezpieczeń systemu Windows | Dokumentacja firmy Microsoft
description: Informacje o sposobie konfigurowania zabezpieczeń między węzłami i węzeł klient autonomiczny klastra z systemem Windows przy użyciu zabezpieczeń systemu Windows.
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
ms.openlocfilehash: 0f0df7883b25344560514491c08af3eadf872ffb
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34209141"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-windows-security"></a>Zabezpieczanie klastra autonomicznego w systemie Windows przy użyciu zabezpieczeń systemu Windows
Aby uniemożliwić nieautoryzowany dostęp do klastra usługi sieć szkieletowa, należy zabezpieczyć klastra. Zabezpieczeń jest szczególnie ważne, jeśli klaster uruchamia obciążeń produkcyjnych. W tym artykule opisano sposób konfigurowania zabezpieczeń między węzłami i węzeł klienta przy użyciu zabezpieczeń systemu Windows w *pliku ClusterConfig.JSON* pliku.  Proces odpowiada Konfiguruj kroku zabezpieczeń [tworzenia autonomicznych klastra z systemem Windows](service-fabric-cluster-creation-for-windows-server.md). Aby uzyskać więcej informacji o używaniu sieci szkieletowej usług zabezpieczeń systemu Windows, temacie [klastra scenariusze zabezpieczeń](service-fabric-cluster-security.md).

> [!NOTE]
> Należy zastanów zaznaczenie zabezpieczeń węzła do węzła, ponieważ nie istnieje uaktualnienie nie klastra z wybór jednego zabezpieczeń do innego. Aby zmienić wybór zabezpieczeń, należy odbudować pełne klastra.
>
>

## <a name="configure-windows-security-using-gmsa"></a>Konfigurowanie zabezpieczeń systemu Windows przy użyciu usługi zarządzane przez grupę  
Przykład *ClusterConfig.gMSA.Windows.MultiMachine.JSON* pobrane za pomocą pliku konfiguracji [Microsoft.Azure.ServiceFabric.WindowsServer.<version>. ZIP](http://go.microsoft.com/fwlink/?LinkId=730690) pakiet klastra autonomiczny zawiera szablon do konfigurowania systemu Windows zabezpieczeń za pomocą [konta usług zarządzanych grupy (gMSA)](https://technet.microsoft.com/library/hh831782.aspx):  

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
| ClusterCredentialType |Ustaw *Windows* włączenie zabezpieczeń systemu Windows dla węzła node komunikacji.  | 
| ServerCredentialType |Ustaw *Windows* włączenie zabezpieczeń systemu Windows dla komunikacji klient węzeł. |  
| WindowsIdentities |Zawiera tożsamość klastra i klienta. |  
| ClustergMSAIdentity |Konfiguruje zabezpieczeń węzła do węzła. Konto usługi zarządzane przez grupę. |  
| ClusterSPN |SPN zarejestrowanych dla konta gMSA|  
| ClientIdentities |Konfiguruje zabezpieczeń węzeł klienta. Tablica konta użytkownika klienta. | 
| Tożsamość |Dodaj użytkownika domeny, a domena azwa_użytkownika tożsamości klienta. |  
| IsAdmin |Ustaw wartość true, aby określić, czy użytkownik domeny ma dostęp administratora klienta lub false dla dostępu klientów użytkownika. |  

[Węzła do węzła zabezpieczeń](service-fabric-cluster-security.md#node-to-node-security) skonfigurowano ustawienie **ClustergMSAIdentity** podczas usługi sieć szkieletowa musi działać w ramach usługi zarządzane przez grupę. Aby można było utworzyć relację zaufania między węzłami, ich należy pamiętać o sobie nawzajem. Można to zrobić na dwa sposoby: można określić grupy zarządzane konto usługi, który zawiera wszystkie węzły w klastrze lub grupy maszyny domeny, która zawiera wszystkie węzły w klastrze. Zdecydowanie zaleca się używanie [konta usług zarządzanych grupy (gMSA)](https://technet.microsoft.com/library/hh831782.aspx) podejście, szczególnie w przypadku dużych klastrów (więcej niż 10 węzłów) lub w przypadku klastrów, które mogą zwiększać i zmniejszać.  
To rozwiązanie nie wymaga utworzenia grupy domeny, dla którego klastra Administratorzy przyznano uprawnienia do dodawania i usuwania członków. Te konta są również przydatne w przypadku automatyczne zarządzanie hasłami. Aby uzyskać więcej informacji, zobacz [wprowadzenie do kont usług zarządzanych grupy](http://technet.microsoft.com/library/jj128431.aspx).  
 
[Klientowi zabezpieczeń węzła](service-fabric-cluster-security.md#client-to-node-security) jest konfigurowana przy użyciu **ClientIdentities**. Aby ustanowić zaufanie między klientem a klastrem, należy skonfigurować klaster, aby wiedzieć, który klient tożsamości, które on zaufany. Można to zrobić na dwa sposoby: Określ użytkowników grupy domeny, które mogą połączyć lub określ użytkowników węzeł domeny, które mogą nawiązywać połączenia. Sieć szkieletowa usług obsługuje dwa typy kontroli różny dostęp dla klientów, które są podłączone do klastra usługi sieć szkieletowa: administratora i użytkownika. Kontrola dostępu umożliwia określenie dla administratora klastrów, aby ograniczyć dostęp dla określonych typów działania klastra dla różnych grup użytkowników, co klaster bardziej bezpieczne.  Administratorzy mają pełny dostęp do funkcji zarządzania (w tym możliwości odczytu/zapisu). Użytkownicy, domyślnie mają tylko do odczytu możliwości zarządzania (na przykład możliwość wykonywania kwerend) i możliwość usuwania aplikacji i usług. Aby uzyskać więcej informacji dotyczących kontroli dostępu, zobacz [kontrola dostępu oparta na rolach dla klientów usługi sieć szkieletowa](service-fabric-cluster-security-roles.md).  
 
Poniższy przykład **zabezpieczeń** sekcji konfiguruje zabezpieczenia systemu Windows przy użyciu usługi zarządzane przez grupę i określa, że na komputerach należących do *ServiceFabric.clusterA.contoso.com* gMSA są częścią klastra i że  *CONTOSO\usera* ma dostęp klient administratora:  
  
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
  
## <a name="configure-windows-security-using-a-machine-group"></a>Konfigurowanie zabezpieczeń systemu Windows przy użyciu grupy na komputerze  
Ten model jest przestarzałe. Zaleca się używania konta usługi zarządzanego zgodnie z opisem powyżej. Przykład *ClusterConfig.Windows.MultiMachine.JSON* pobrane za pomocą pliku konfiguracji [Microsoft.Azure.ServiceFabric.WindowsServer.<version>. ZIP](http://go.microsoft.com/fwlink/?LinkId=730690) pakiet klastra autonomiczny zawiera szablon do konfigurowania zabezpieczeń systemu Windows.  Zabezpieczenia systemu Windows jest skonfigurowany w **właściwości** sekcji: 

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
| ClusterCredentialType |Ustaw *Windows* włączenie zabezpieczeń systemu Windows dla węzła node komunikacji.  | 
| ServerCredentialType |Ustaw *Windows* włączenie zabezpieczeń systemu Windows dla komunikacji klient węzeł. |  
| WindowsIdentities |Zawiera tożsamość klastra i klienta. |  
| ClusterIdentity |Nazwa grupy maszyn, domain\machinegroup, umożliwia skonfigurowanie zabezpieczeń węzła do węzła. |  
| ClientIdentities |Konfiguruje zabezpieczeń węzeł klienta. Tablica konta użytkownika klienta. |  
| Tożsamość |Dodaj użytkownika domeny, a domena azwa_użytkownika tożsamości klienta. |  
| IsAdmin |Ustaw wartość true, aby określić, czy użytkownik domeny ma dostęp administratora klienta lub false dla dostępu klientów użytkownika. |  

[Węzła do węzła zabezpieczeń](service-fabric-cluster-security.md#node-to-node-security) jest konfigurowana przy użyciu ustawienia **ClusterIdentity** Jeśli chcesz użyć grupy na komputerze w ramach domeny usługi Active Directory. Aby uzyskać więcej informacji, zobacz [Utwórz grupę maszyny w usłudze Active Directory](https://msdn.microsoft.com/library/aa545347(v=cs.70).aspx).

[Węzeł Klient zabezpieczeń](service-fabric-cluster-security.md#client-to-node-security) jest konfigurowana przy użyciu **ClientIdentities**. Aby ustanowić zaufanie między klientem a klastrem, należy skonfigurować klaster, aby wiedzieć, klient tożsamości, które można ufać klastra. Można ustanowić relacji zaufania na dwa sposoby:

- Określ użytkowników grupy domeny, które mogą nawiązywać połączenia.
- Określ użytkowników węzeł domeny, które mogą nawiązywać połączenia.

Sieć szkieletowa usług obsługuje dwa typy kontroli różny dostęp dla klientów, które są podłączone do klastra usługi sieć szkieletowa: administratora i użytkownika. Kontrola dostępu umożliwia administratora klastrów, aby ograniczyć dostęp dla określonych typów działania klastra dla różnych grup użytkowników, co sprawia, że klaster jest bardziej bezpieczne.  Administratorzy mają pełny dostęp do funkcji zarządzania (w tym możliwości odczytu/zapisu). Użytkownicy, domyślnie mają tylko do odczytu możliwości zarządzania (na przykład możliwość wykonywania kwerend) i możliwość usuwania aplikacji i usług.  

Poniższy przykład **zabezpieczeń** sekcji konfiguruje zabezpieczenia systemu Windows, określa, że na komputerach należących do *ServiceFabric/clusterA.contoso.com* są częścią klastra i określa, że *CONTOSO\usera* ma dostęp klient administratora:

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
> Sieć szkieletowa usług nie powinny zostać wdrożone na kontrolerze domeny. Upewnij się, że pliku ClusterConfig.json nie obejmują adres IP kontrolera domeny, korzystając z grupy komputera lub konta grupy usługi zarządzanej (gMSA).
>
>

## <a name="next-steps"></a>Kolejne kroki
Po skonfigurowaniu zabezpieczeń systemu Windows w *pliku ClusterConfig.JSON* plików, wznowić procesu tworzenia klastra w [tworzenia autonomicznych klastra z systemem Windows](service-fabric-cluster-creation-for-windows-server.md).

Aby uzyskać więcej informacji na temat sposobu węzeł węzeł zabezpieczeń, węzeł klient zabezpieczeń i kontroli dostępu opartej na rolach, zobacz [klastra scenariusze zabezpieczeń](service-fabric-cluster-security.md).

Zobacz [Połącz z klastrem bezpiecznego](service-fabric-connect-to-secure-cluster.md) przykłady połączenie przy użyciu programu PowerShell lub klienta fabricclient z rolą.
