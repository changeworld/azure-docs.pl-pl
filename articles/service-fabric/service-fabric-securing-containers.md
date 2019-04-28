---
title: Zaimportuj certyfikaty w kontenerze uruchomiona w usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się teraz zaimportować plik certyfikatu do kontenera usługi Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: aljo, subramar
ms.openlocfilehash: ac461c0d512a5a58ce687623a53e99eeeca399e5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837695"
---
# <a name="import-a-certificate-file-into-a-container-running-on-service-fabric"></a>Zaimportuj plik certyfikatu w kontenerze uruchomiona w usłudze Service Fabric

Można zabezpieczyć usługi kontenera, określając certyfikatu. Usługa Service Fabric udostępnia mechanizm dla usług w kontenerze na dostęp do certyfikatu, który jest zainstalowany na węzłach w klastrze Windows lub Linux (w wersji 5.7 lub nowszej). Certyfikat musi być zainstalowany w magazynie certyfikatów w obszarze LocalMachine we wszystkich węzłach klastra. Klucza prywatnego odpowiadająca certyfikatu musi być dostępny, dostępny i - Windows - eksportowalny. Informacje o certyfikacie znajduje się w manifeście aplikacji, w obszarze `ContainerHostPolicies` tag, co ilustruje poniższy fragment kodu:

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

W przypadku klastrów Windows, podczas uruchamiania aplikacji środowisko uruchomieniowe eksportuje każdego odwołania certyfikatu i odpowiedniego klucza prywatnego do pliku PFX zabezpieczony hasłem generowany losowo. Pliki PFX oraz hasła, odpowiednio, są dostępne w kontenerze za pomocą następujące zmienne środowiskowe: 

* Certificates_ServicePackageName_CodePackageName_CertName_PFX
* Certificates_ServicePackageName_CodePackageName_CertName_Password

W przypadku klastrów systemu Linux certyfikaty (PEM) są kopiowane z magazynu określonego przez X509StoreName do kontenera. To odpowiednie zmienne środowiskowe w systemie Linux:

* Certificates_ServicePackageName_CodePackageName_CertName_PEM
* Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey

Alternatywnie, jeśli już mają certyfikaty w formularzu wymaganych, aby uzyskać do niego dostęp w kontenerze można tworzyć pakiet danych wewnątrz pakietu aplikacji i podaj następujące informacje w manifeście aplikacji:

```xml
<ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
  <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

Usługa kontenera lub proces jest odpowiedzialny za importowanie plików certyfikatów do kontenera. Aby zaimportować certyfikat, możesz użyć `setupentrypoint.sh` skrypty lub wykonania niestandardowego kodu w ramach procesu kontenera. Poniżej przedstawiono przykładowy kod w C# importowania pliku PFX:

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
Ten certyfikat PFX może służyć do uwierzytelniania aplikacji lub usługi albo bezpiecznej komunikacji z innymi usługami. Domyślnie pliki są ACLed tylko do systemu. Możesz listy ACL je do innych kont, zgodnie z wymaganiami przez usługę.

Kolejnym krokiem przeczytaj następujące artykuły:

* [Wdrażanie kontenera Windows w usłudze Service Fabric w systemie Windows Server 2016](service-fabric-get-started-containers.md)
* [Wdrażanie kontenera platformy Docker w usłudze Service Fabric w systemie Linux](service-fabric-get-started-containers-linux.md)
