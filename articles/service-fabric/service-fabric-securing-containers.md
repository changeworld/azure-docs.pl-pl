---
title: Importowanie certyfikatów do kontenera systemem Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się teraz zaimportować plik certyfikatu usługi kontenera platformy Service Fabric.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: f234a6f6ca56d1833aac53f490feb5f667a6bf1b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208220"
---
# <a name="import-a-certificate-file-into-a-container-running-on-service-fabric"></a>Zaimportuj plik certyfikatu do kontenera systemem sieci szkieletowej usług

Określając certyfikatu, można zabezpieczyć swoje usługi kontenera. Sieć szkieletowa usług udostępnia mechanizm dla usług wewnątrz kontenera można uzyskać dostępu do certyfikatu, który jest zainstalowany na węzłach w klastrze systemu Windows lub Linux (w wersji 5.7 lub nowszej). Certyfikat musi być zainstalowany w LocalMachine we wszystkich węzłach klastra. Informacje o certyfikacie znajduje się w manifeście aplikacji w obszarze `ContainerHostPolicies` tagu w formie poniższy fragment kodu przedstawia:

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

W przypadku klastrów systemu Windows, podczas uruchamiania aplikacji środowisko uruchomieniowe odczytuje certyfikaty i generuje plik PFX oraz hasła dla każdego certyfikatu. Ten plik PFX oraz hasła są dostępne w kontenerze, za pomocą następujących zmiennych środowiskowych: 

* Certificates_ServicePackageName_CodePackageName_CertName_PFX
* Certificates_ServicePackageName_CodePackageName_CertName_Password

W przypadku klastrów systemu Linux (PEM) certyfikaty są kopiowane z określonego przez X509StoreName na kontenerze magazynu. Odpowiednie zmienne środowiskowe w systemie Linux są:

* Certificates_ServicePackageName_CodePackageName_CertName_PEM
* Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey

Alternatywnie, jeśli już mają postać wymagane certyfikaty i mają dostęp do niego wewnątrz kontenera, można tworzyć pakiet danych wewnątrz pakietu aplikacji i podaj następujące informacje w manifeście aplikacji:

```xml
<ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
  <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

Usługa kontenera lub proces jest odpowiedzialny za importowanie plików certyfikatów do kontenera. Aby zaimportować certyfikat, można użyć `setupentrypoint.sh` skrypty lub wykonywanie kodu niestandardowego, w ramach procesu kontenera. Poniżej przedstawiono przykładowy kod w języku C# do importowania plików PFX:

```csharp
string certificateFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_PFX");
string passwordFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_Password");
X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
string password = File.ReadAllLines(passwordFilePath, Encoding.Default)[0];
password = password.Replace("\0", string.Empty);
X509Certificate2 cert = new X509Certificate2(certificateFilePath, password, X509KeyStorageFlags.MachineKeySet | X509KeyStorageFlags.PersistKeySet);
store.Open(OpenFlags.ReadWrite);
store.Add(cert);
store.Close();
```
Ten certyfikat PFX mogą służyć do uwierzytelniania aplikacji lub usługi albo bezpiecznej komunikacji z innymi usługami. Domyślnie pliki są ACLed tylko do systemu. Można listy kontroli dostępu do innych kont, co jest wymagane przez usługę.

Jako kolejny krok przeczytaj następujące artykuły:

* [Wdrażanie kontenera systemu Windows w sieci szkieletowej usług w systemie Windows Server 2016](service-fabric-get-started-containers.md)
* [Wdrażanie kontenera Docker sieci szkieletowej usług w systemie Linux](service-fabric-get-started-containers-linux.md)
