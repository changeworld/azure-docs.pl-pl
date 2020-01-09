---
title: Service Fabric platformy Azure — korzystanie z Service Fabric odwołań do magazynu kluczy aplikacji
description: W tym artykule wyjaśniono, jak używać obsługi KeyVaultReference usługi Service-Fabric dla wpisów tajnych aplikacji.
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: b0e882c2b39c06a3040d22fc6694599966ceeb39
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75463035"
---
#  <a name="keyvaultreference-support-for-service-fabric-applications-preview"></a>Obsługa KeyVaultReference dla aplikacji Service Fabric (wersja zapoznawcza)

Typowym wyzwaniem podczas kompilowania aplikacji w chmurze jest sposób bezpiecznego przechowywania wpisów tajnych wymaganych przez aplikację. Na przykład możesz chcieć zapisać poświadczenia repozytorium kontenera w magazynie kluczy i odwołać się do niego w manifeście aplikacji. Service Fabric KeyVaultReference korzysta z tożsamości zarządzanej Service Fabric i ułatwia odwoływanie się do wpisów tajnych magazynu kluczy. W pozostałej części tego artykułu szczegółowo przedstawiono sposób korzystania z Service Fabric KeyVaultReference i zawiera on typowe użycie.

## <a name="prerequisites"></a>Wymagania wstępne

- Tożsamość zarządzana dla aplikacji (MIT)
    
    Service Fabric obsługa KeyVaultReference korzysta z tożsamości zarządzanej aplikacji, w związku z czym aplikacje planujące użycie KeyVaultReferences powinny używać tożsamości zarządzanej. Postępuj zgodnie z tym [dokumentem](concepts-managed-identity.md) , aby włączyć zarządzaną tożsamość aplikacji.

- Magazyn centralnych wpisów tajnych (CSS).

    Magazyn centralnych wpisów tajnych (CSS) to pamięć podręczna szyfrowanych lokalnych kluczy tajnych usługi Service Fabric, KeyVaultReference po pobraniu są buforowane w CSS.

    Dodaj poniżej do konfiguracji klastra w obszarze `fabricSettings`, aby włączyć wszystkie funkcje wymagane do obsługi KeyVaultReference.

    ```json
    "fabricSettings": 
    [
        ...
    {
                "name":  "CentralSecretService",
                "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                },
                {
                    "name":  "MinReplicaSetSize",
                    "value":  "3"
                },
                {
                    "name":  "TargetReplicaSetSize",
                    "value":  "3"
                }
                ]
            },
            {
                "name":  "ManagedIdentityTokenService",
                "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                }
                ]
            }
            ]
    ```

    > [!NOTE] 
    > Zaleca się użycie oddzielnego certyfikatu szyfrowania dla CSS. Można go dodać w sekcji "CentralSecretService".

    ```json
        {
            "name": "EncryptionCertificateThumbprint",
            "value": "<EncryptionCertificateThumbprint for CSS>"
        }
    ```

- Przyznawanie uprawnień dostępu do tożsamości zarządzanej aplikacji do magazynu kluczy

    Odwołuje się do tego [dokumentu](how-to-grant-access-other-resources.md) , aby dowiedzieć się, jak udzielić zarządzanej tożsamości dostępu do magazynu kluczy. Należy również pamiętać, że jeśli używasz tożsamości zarządzanej przypisanej przez system, tożsamość zarządzana jest tworzona dopiero po wdrożeniu aplikacji.

## <a name="keyvault-secret-as-application-parameter"></a>Wpis tajny magazynu kluczy jako parametr aplikacji
Załóżmy, że aplikacja musi odczytać hasło bazy danych zaplecza przechowywane w magazynie kluczy, Service Fabric pomoc techniczną KeyVaultReference. Poniżej przykład odczytuje `DBPassword` klucz tajny z magazynu kluczy przy użyciu Service Fabric obsługi KeyVaultReference.

- Dodaj sekcję do pliku Settings. XML

    Zdefiniuj parametr `DBPassword` typu `KeyVaultReference` i wartości `<KeyVaultURL>`

    ```xml
    <Section Name="dbsecrets">
        <Parameter Name="DBPassword" Type="KeyVaultReference" Value="https://vault200.vault.azure.net/secrets/dbpassword/8ec042bbe0ea4356b9b171588a8a1f32"/>
    </Section>
    ```
- Odwołuje się do nowej sekcji w ApplicationManifest. XML w `<ConfigPackagePolicies>`

    ```xml
    <ServiceManifestImport>
        <Policies>
        <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="ttkappuser" />
        <ConfigPackagePolicies CodePackageRef="Code">
            <!--Linux container example-->
            <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="/var/secrets"/>
            <!--Windows container example-->
            <!-- <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="C:\secrets"/> -->
        </ConfigPackagePolicies>
        </Policies>
    </ServiceManifestImport>
    ```

- Korzystanie z KeyVaultReference w aplikacji

    Service Fabric przy tworzeniu wystąpienia usługi rozwiąże parametr KeyVaultReference za pomocą tożsamości zarządzanej aplikacji. Każdy parametr wyświetlany w obszarze `<Section  Name=dbsecrets>` będzie plikiem w folderze wskazanym przez zmiennych środowiskowych SecretPath. Poniższy C# fragment kodu przedstawia sposób odczytywania hasła DBW aplikacji.

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "DBPassword"))) 
    {
        string dbPassword =  sr.ReadToEnd();
        // dbPassword to connect to DB
    }
    ```
    > [!NOTE] 
    > W scenariuszu kontenera można użyć MountPoint do kontrolowania miejsca, w którym zostanie zainstalowana `secrets`.

## <a name="keyvault-secret-as-environment-variable"></a>Wpis tajny magazynu kluczy jako zmienna środowiskowa

Zmienne środowiskowe Service Fabric obsługują teraz typ KeyVaultReference, poniżej przedstawiono sposób powiązania zmiennej środowiskowej z wpisem tajnym przechowywanym w magazynie kluczy.

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="EventStorePassword" Type="KeyVaultReference" Value="https://ttkvault.vault.azure.net/secrets/clustercert/e225bd97e203430d809740b47736b9b8"/>
</EnvironmentVariables>
```

```C#
string eventStorePassword =  Environment.GetEnvironmentVariable("EventStorePassword");
```
## <a name="keyvault-secret-as-container-repository-password"></a>Wpis tajny magazynu kluczy jako hasło repozytorium kontenera
KeyVaultReference jest obsługiwanym typem kontenera RepositoryCredentials, poniżej pokazano, jak używać odwołania do magazynu kluczy jako hasła repozytorium kontenerów.
```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="user1" Type="KeyVaultReference" Password="https://ttkvault.vault.azure.net/secrets/containerpwd/e225bd97e203430d809740b47736b9b8"/>
      </ContainerHostPolicies>
```
## <a name="faq"></a>Często zadawane pytania
- Tożsamość zarządzana musi być włączona na potrzeby obsługi KeyVaultReference, jednak aktywacja aplikacji zakończy się niepowodzeniem, jeśli KeyVaultReference jest używany bez włączania tożsamości zarządzanej.

- Jeśli używasz tożsamości przypisanej do systemu, jest ona tworzona dopiero po wdrożeniu aplikacji i spowoduje to utworzenie zależności cyklicznej. Po wdrożeniu aplikacji można przyznać systemowi uprawnienia dostępu do tożsamości dla magazynu kluczy. Tożsamość przypisaną przez system można znaleźć według nazwy {Cluster}/{Application Name}/{ServiceName}

- Magazyn kluczy musi znajdować się w tej samej subskrypcji co klaster usługi Service Fabric. 

## <a name="next-steps"></a>Następne kroki

* [Dokumentacja usługi Azure — Magazyn kluczy](https://docs.microsoft.com/azure/key-vault/)
