---
title: Importowanie certyfikatów do kontenera
description: Dowiedz się teraz, aby zaimportować pliki certyfikatów do usługi kontenera sieci szkieletowej usług.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: da4babd8f9d1a25a8514d0c6f1526b43a9723854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614115"
---
# <a name="import-a-certificate-file-into-a-container-running-on-service-fabric"></a>Importowanie pliku certyfikatu do kontenera działającego w sieci szkieletowej usług

Usługi kontenera można zabezpieczyć, określając certyfikat. Sieci szkieletowej usług udostępnia mechanizm dla usług wewnątrz kontenera, aby uzyskać dostęp do certyfikatu, który jest zainstalowany w węzłach w klastrze systemu Windows lub Linux (wersja 5.7 lub nowsza). Certyfikat musi być zainstalowany w magazynie certyfikatów w obszarze LocalMachine we wszystkich węzłach klastra. Klucz prywatny odpowiadający certyfikatowi musi być dostępny, dostępny i - w systemie Windows - eksportowany. Informacje o certyfikacie są `ContainerHostPolicies` dostarczane w manifeście aplikacji pod znacznikiem, jak pokazuje następujący fragment kodu:

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

W przypadku klastrów systemu Windows podczas uruchamiania aplikacji środowisko wykonawcze eksportuje każdy certyfikat, do którego istnieje odwołanie, i jego odpowiedni klucz prywatny do pliku PFX, zabezpieczone losowo wygenerowanym hasłem. Pliki PFX i hasła są dostępne w kontenerze przy użyciu następujących zmiennych środowiskowych: 

* Certificates_ServicePackageName_CodePackageName_CertName_PFX
* Certificates_ServicePackageName_CodePackageName_CertName_Password

W przypadku klastrów systemu Linux certyfikaty (PEM) są kopiowane ze sklepu określonego przez X509StoreName do kontenera. Odpowiednie zmienne środowiskowe w systemie Linux to:

* Certificates_ServicePackageName_CodePackageName_CertName_PEM
* Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey

Alternatywnie, jeśli masz już certyfikaty w wymaganym formularzu i chcesz uzyskać do niego dostęp wewnątrz kontenera, możesz utworzyć pakiet danych wewnątrz pakietu aplikacji i określić następujące elementy w manifeście aplikacji:

```xml
<ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
  <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

Usługa lub proces kontenera jest odpowiedzialny za importowanie plików certyfikatów do kontenera. Aby zaimportować certyfikat, można użyć `setupentrypoint.sh` skryptów lub wykonać kod niestandardowy w procesie kontenera. Oto przykładowy kod w języku C# do importowania pliku PFX:

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
Ten certyfikat PFX może służyć do uwierzytelniania aplikacji lub usługi lub bezpiecznej komunikacji z innymi usługami. Domyślnie pliki są acled tylko do systemu. Możesz acl go do innych kont, zgodnie z wymaganiami usługi.

W następnym kroku przeczytaj następujące artykuły:

* [Wdrażanie kontenera systemu Windows w sieci szkieletowej usług w systemie Windows Server 2016](service-fabric-get-started-containers.md)
* [Wdrażanie kontenera platformy Docker w sieci szkieletowej usług w systemie Linux](service-fabric-get-started-containers-linux.md)
