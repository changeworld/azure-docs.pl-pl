---
title: Service Fabric platformy Azure — konfigurowanie poświadczeń repozytorium kontenerów | Microsoft Docs
description: Skonfiguruj poświadczenia repozytorium, aby pobierać obrazy z rejestru kontenerów
services: service-fabric
documentationcenter: .net
author: arya
manager: gkhanna
ms.assetid: b93d31e5-9e4c-4405-b266-c0efa4643d97
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 8/1/2019
ms.author: arya
ms.openlocfilehash: c415739934e2318ea5287d5eed9f8235029b666f
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74405617"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>Skonfiguruj poświadczenia repozytorium dla aplikacji, aby pobierać obrazy kontenerów

Skonfiguruj uwierzytelnianie rejestru kontenerów przez dodanie elementu `RepositoryCredentials` do elementu `ContainerHostPolicies` pliku ApplicationManifest.xml. Dodaj nazwę konta i hasło dla rejestru kontenerów myregistry.azurecr.io, dzięki czemu usługa będzie mogła pobrać obraz kontenera z repozytorium.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

Zaleca się zaszyfrowanie hasła do repozytorium przy użyciu certyfikatu szyfrowania, który został wdrożony na wszystkich węzłach klastra. Gdy usługa Service Fabric wdroży pakiet usług w klastrze, zaszyfrowany tekst zostanie odszyfrowany za pomocą certyfikatu szyfrowania. Polecenie cmdlet Invoke-ServiceFabricEncryptText jest używane do utworzenia zaszyfrowanego tekstu dla hasła dodawanego do pliku ApplicationManifest.xml.
Aby uzyskać więcej informacji na temat certyfikatów i semantyki szyfrowania, zobacz [Zarządzanie kluczami tajnymi](service-fabric-application-secret-management.md) .

## <a name="configure-cluster-wide-credentials"></a>Konfigurowanie poświadczeń dla całego klastra

Service Fabric umożliwia skonfigurowanie poświadczeń dla całego klastra, które mogą być używane jako domyślne poświadczenia repozytorium przez aplikacje.

Tę funkcję można włączać lub wyłączać, dodając atrybut `UseDefaultRepositoryCredentials` do `ContainerHostPolicies` w ApplicationManifest. XML z wartością `true` lub `false`.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code" UseDefaultRepositoryCredentials="true">
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

Service Fabric następnie używa domyślnych poświadczeń repozytorium, które można określić w ClusterManifest w sekcji `Hosting`.  Jeśli `UseDefaultRepositoryCredentials` jest `true`, Service Fabric odczytuje następujące wartości z ClusterManifest:

* DefaultContainerRepositoryAccountName (ciąg)
* DefaultContainerRepositoryPassword (ciąg)
* IsDefaultContainerRepositoryPasswordEncrypted (bool)
* DefaultContainerRepositoryPasswordType (String)---obsługiwane począwszy od środowiska uruchomieniowego 6,4

Poniżej znajduje się przykład, co można dodać do sekcji `Hosting` w pliku ClusterManifestTemplate. JSON. Sekcję `Hosting` można dodać podczas tworzenia klastra lub później w ramach uaktualnienia konfiguracji. Aby uzyskać więcej informacji, zobacz [Zmienianie ustawień klastra platformy azure Service Fabric](service-fabric-cluster-fabric-settings.md) i zarządzanie wpisami [tajnymi aplikacji platformy Azure Service Fabric](service-fabric-application-secret-management.md)

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
            "name": "EndpointProviderEnabled",
            "value": "true"
          },
          {
            "name": "DefaultContainerRepositoryAccountName",
            "value": "someusername"
          },
          {
            "name": "DefaultContainerRepositoryPassword",
            "value": "somepassword"
          },
          {
            "name": "IsDefaultContainerRepositoryPasswordEncrypted",
            "value": "false"
          },
          {
            "name": "DefaultContainerRepositoryPasswordType",
            "value": "PlainText"
          }
        ]
      },
]
```

## <a name="leveraging-the-managed-identity-of-the-virtual-machine-scale-set-by-using-managed-identity-service-msi"></a>Korzystanie z zarządzanej tożsamości zestawu skalowania maszyn wirtualnych przy użyciu usługi tożsamości zarządzanej (MSI)

Service Fabric obsługuje używanie tokenów jako poświadczeń do pobierania obrazów dla kontenerów.  Ta funkcja wykorzystuje zarządzaną tożsamość zestawu skalowania maszyn wirtualnych do uwierzytelniania w rejestrze, eliminując konieczność zarządzania poświadczeniami użytkowników.  Zobacz [tożsamość usługi zarządzanej](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) , aby uzyskać więcej informacji na temat pliku msi.  Korzystanie z tej funkcji wymaga wykonania następujących czynności:

1.  Upewnij się, że dla maszyny wirtualnej jest włączona tożsamość zarządzana przypisana przez system (Zobacz zrzut ekranu poniżej)

    ![Tworzenie tożsamości zestawu skalowania maszyn wirtualnych](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

2.  Następnie Udziel uprawnień do maszyny wirtualnej (SS), aby pobierać/odczytywać obrazy z rejestru.  Przejdź do Access Control (IAM) ACR za pomocą bloku Azure i nadaj maszynie wirtualnej (SS) odpowiednie uprawnienia, jak pokazano poniżej:

    ![Dodawanie podmiotu maszyny wirtualnej do ACR](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3.  Po wykonaniu powyższych kroków zmodyfikuj plik ApplicationManifest. XML.  Znajdź tag o nazwie "ContainerHostPolicies" i Dodaj atrybut `‘UseTokenAuthenticationCredentials=”true”`.

    ```xml
      <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
      <Policies>
        <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" UseTokenAuthenticationCredentials="true">
          <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        </ContainerHostPolicies>
        <ResourceGovernancePolicy CodePackageRef="NodeService.Code" MemoryInMB="256"/>
      </Policies>
      </ServiceManifestImport>
    ```

    > [!NOTE]
    > Flaga `UseDefaultRepositoryCredentials` ustawiona na wartość true, gdy `UseTokenAuthenticationCredentials` ma wartość true, spowoduje wystąpienie błędu podczas wdrażania.

## <a name="next-steps"></a>Następne kroki

* Zobacz więcej informacji o [uwierzytelnianiu rejestru kontenerów](/azure/container-registry/container-registry-authentication).
