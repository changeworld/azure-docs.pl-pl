---
title: Usługa Azure Service Fabric — konfigurowanie poświadczeń repozytorium kontenerów
description: Konfigurowanie poświadczeń repozytorium do pobierania obrazów z rejestru kontenerów
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 9bd6e6a0a22f7568760f014897fd28ff47e9450b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934993"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>Konfigurowanie poświadczeń repozytorium dla aplikacji w celu pobierania obrazów kontenerów

Skonfiguruj uwierzytelnianie rejestru kontenerów, dodając `RepositoryCredentials` do `ContainerHostPolicies` sekcji manifestu aplikacji. Dodaj konto i hasło do rejestru kontenerów *(myregistry.azurecr.io* w poniższym przykładzie), co umożliwia usłudze pobranie obrazu kontenera z repozytorium.

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

Zaleca się szyfrowanie hasła repozytorium przy użyciu certyfikatu szyfrowania, który jest wdrażany we wszystkich węzłach klastra. Gdy usługa Service Fabric wdroży pakiet usług w klastrze, zaszyfrowany tekst zostanie odszyfrowany za pomocą certyfikatu szyfrowania. Polecenie cmdlet Invoke-ServiceFabricEncryptText jest używane do utworzenia zaszyfrowanego tekstu dla hasła dodawanego do pliku ApplicationManifest.xml.
Zobacz [Tajne zarządzanie, aby](service-fabric-application-secret-management.md) uzyskać więcej informacji na temat certyfikatów i semantyki szyfrowania.

## <a name="configure-cluster-wide-credentials"></a>Konfigurowanie poświadczeń dla całego klastra

Sieć szkieletowa usług umożliwia skonfigurowanie poświadczeń dla całego klastra, które mogą być używane jako domyślne poświadczenia repozytorium przez aplikacje.

Tę funkcję można włączyć lub wyłączyć, `UseDefaultRepositoryCredentials` dodając `ContainerHostPolicies` atrybut do pliku ApplicationManifest.xml z wartością `true` lub. `false`

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

Usługa Sieci szkieletowej następnie używa domyślnych poświadczeń repozytorium, `Hosting` które można określić w ClusterManifest w sekcji.  Jeśli `UseDefaultRepositoryCredentials` `true`tak jest, sieci szkieletowej usług odczytuje następujące wartości z ClusterManifest:

* DefaultContainerRepositoryAccountName (ciąg)
* DefaultContainerRepositoryPassword (ciąg)
* IsDefaultContainerRepositoryPasswordEncrypted (bool)
* DefaultContainerRepositoryPasswordType (ciąg)

Oto przykład tego, co można `Hosting` dodać wewnątrz sekcji w pliku ClusterManifestTemplate.json. Sekcję `Hosting` można dodać podczas tworzenia klastra lub później w uaktualnieniu konfiguracji. Aby uzyskać więcej informacji, zobacz [Zmienianie ustawień klastra sieci szkieletowej usług Azure](service-fabric-cluster-fabric-settings.md) i [zarządzanie wpisami tajnymi aplikacji usługi Azure Service Fabric](service-fabric-application-secret-management.md)

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

## <a name="use-tokens-as-registry-credentials"></a>Używanie tokenów jako poświadczeń rejestru

Usługa Sieci szkieletowej obsługuje przy użyciu tokenów jako poświadczenia do pobierania obrazów dla kontenerów.  Ta funkcja wykorzystuje *tożsamość zarządzaną* podstawowej skali maszyny wirtualnej ustawionej do uwierzytelniania w rejestrze, eliminując potrzebę zarządzania poświadczeniami użytkownika.  Aby uzyskać więcej informacji, zobacz [Tożsamości zarządzane dla zasobów platformy Azure.](../active-directory/managed-identities-azure-resources/overview.md)  Korzystanie z tej funkcji wymaga następujących kroków:

1. Upewnij się, że *przypisana do systemu tożsamość zarządzana* jest włączona dla maszyny Wirtualnej.

    ![Portal Azure: tworzenie opcji tożsamości zestawu skalowania maszyny wirtualnej](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

2. Udziel uprawnień do skalowania maszyny wirtualnej ustawionej do ściągania/odczytywania obrazów z rejestru. Z bloku kontroli dostępu (IAM) rejestru kontenerów platformy Azure w witrynie Azure portal dodaj *przypisanie roli* dla maszyny wirtualnej:

    ![Dodawanie podmiotu zabezpieczeń maszyny Wirtualnej do usługi ACR](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3. Następnie zmodyfikuj manifest aplikacji. W `ContainerHostPolicies` sekcji dodaj atrybut `‘UseTokenAuthenticationCredentials=”true”`.

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
    > Flaga `UseDefaultRepositoryCredentials` ustawiona `UseTokenAuthenticationCredentials` na true while is true spowoduje błąd podczas wdrażania.

## <a name="next-steps"></a>Następne kroki

* Zobacz więcej informacji o [uwierzytelnianiu rejestru kontenerów](../container-registry/container-registry-authentication.md).
