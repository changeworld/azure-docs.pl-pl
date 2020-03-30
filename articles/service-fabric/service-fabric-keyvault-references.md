---
title: Sieci szkieletowej usługi Azure — przy użyciu aplikacji sieci szkieletowej usług KeyVault odwołania
description: W tym artykule wyjaśniono, jak używać usługi sieci szkieletowej KeyVaultReference obsługi wpisów tajnych aplikacji.
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: f7d8a083ea5ec4b66c29d392ee98927915465875
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76545487"
---
#  <a name="keyvaultreference-support-for-service-fabric-applications-preview"></a>Obsługa keyvaultreference dla aplikacji sieci szkieletowej usług (wersja zapoznawcza)

Typowym wyzwaniem podczas tworzenia aplikacji w chmurze jest sposób bezpiecznego przechowywania wpisów tajnych wymaganych przez aplikację. Na przykład można zapisać poświadczenia repozytorium kontenera w keyvault i odwołać się do niego w manifeście aplikacji. Usługa Sieci szkieletowej KeyVaultReference używa tożsamości zarządzanej sieci szkieletowej usług i ułatwia odwoływanie się do wpisów tajnych kluczy. Poniżej dalsza część artykułu Szczegółowe informacje na temat używania usługi Fabric KeyVaultReference i zawiera niektóre typowe użycie.

## <a name="prerequisites"></a>Wymagania wstępne

- Tożsamość zarządzana dla aplikacji (MIT)
    
    Obsługa keyvaultreference sieci szkieletowej usług używa tożsamości zarządzanej aplikacji i dlatego aplikacje planujące użycie Funkcji KeyVaultReferences powinny używać tożsamości zarządzanej. Postępuj zgodnie z tym [dokumentem,](concepts-managed-identity.md) aby włączyć tożsamość zarządzana dla aplikacji.

- Centralny Magazyn Tajemnic (CSS).

    Central Secrets Store (CSS) to zaszyfrowana lokalna pamięć podręczna wpisów tajnych sieci szkieletowej. CSS to lokalna pamięć podręczna magazynu tajne, która przechowuje poufne dane, takie jak hasło, tokeny i klucze, zaszyfrowane w pamięci. KeyVaultReference, po pobraniu, są buforowane w CSS.

    Dodaj poniżej do konfiguracji `fabricSettings` klastra w ramach, aby włączyć wszystkie funkcje wymagane dla obsługi KeyVaultReference.

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
Aby zmiany zostały wprowadzone, należy również zmienić zasady uaktualniania, aby określić silne ponowne uruchomienie środowiska uruchomieniowego sieci szkieletowej usług w każdym węźle w miarę postępu uaktualniania za pośrednictwem klastra. To ponowne uruchomienie gwarantuje, że nowo włączona usługa systemowa jest uruchomiona i uruchomiona w każdym węźle. We we wtorkowym urywku forceRestart jest podstawowym ustawieniem; użyj istniejących wartości dla pozostałych ustawień.
```json
"upgradeDescription": {
    "forceRestart": true,
    "healthCheckRetryTimeout": "00:45:00",
    "healthCheckStableDuration": "00:05:00",
    "healthCheckWaitDuration": "00:05:00",
    "upgradeDomainTimeout": "02:00:00",
    "upgradeReplicaSetCheckTimeout": "1.00:00:00",
    "upgradeTimeout": "12:00:00"
}
```
- Udzielanie aplikacji uprawnień dostępu do tożsamości zarządzanej do keyvault

    Odwołaj się do tego [dokumentu,](how-to-grant-access-other-resources.md) aby zobaczyć, jak udzielić dostępu do tożsamości zarządzanej do keyvault. Należy również zauważyć, że jeśli używasz tożsamości zarządzanej przypisanej do systemu, tożsamość zarządzana jest tworzona dopiero po wdrożeniu aplikacji.

## <a name="keyvault-secret-as-application-parameter"></a>Klucz tajny keyvault jako parametr aplikacji
Załóżmy, że aplikacja musi odczytać hasło bazy danych wewnętrznej bazy danych przechowywane w keyvault, obsługa keyvaultreference sieci szkieletowej usługi ułatwia. Poniższy `DBPassword` przykład odczytuje klucz tajny z keyvault przy użyciu obsługi usługi Fabric KeyVaultReference.

- Dodawanie sekcji do pliku settings.xml

    Definiowanie parametru `DBPassword` za pomocą typu `KeyVaultReference` i wartości`<KeyVaultURL>`

    ```xml
    <Section Name="dbsecrets">
        <Parameter Name="DBPassword" Type="KeyVaultReference" Value="https://vault200.vault.azure.net/secrets/dbpassword/8ec042bbe0ea4356b9b171588a8a1f32"/>
    </Section>
    ```
- Odwoływać się do nowej sekcji w ApplicationManifest.xml w`<ConfigPackagePolicies>`

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

- Korzystanie z funkcji KeyVaultReference w aplikacji

    Usługa Sieci szkieletowej w wystąpieniu usługi rozwiąże Parametr KeyVaultReference przy użyciu tożsamości zarządzanej aplikacji. Każdy parametr `<Section  Name=dbsecrets>` wymieniony w obszarze będzie plik w folderze wskazanym przez EnvironmentVariable SecretPath. Poniżej fragment kodu C# pokaż, jak odczytać DBPassword w aplikacji.

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "DBPassword"))) 
    {
        string dbPassword =  sr.ReadToEnd();
        // dbPassword to connect to DB
    }
    ```
    > [!NOTE] 
    > W scenariuszu kontenera można użyć MountPoint `secrets` do kontrolowania, gdzie zostanie zainstalowany.

## <a name="keyvault-secret-as-environment-variable"></a>Klucz tajny keyvault jako zmienna środowiskowa

Zmienne środowiskowe sieci szkieletowej usług obsługują teraz typ KeyVaultReference, w poniższym przykładzie pokazano, jak powiązać zmienną środowiskową z kluczem tajnym przechowywanym w keyvault.

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="EventStorePassword" Type="KeyVaultReference" Value="https://ttkvault.vault.azure.net/secrets/clustercert/e225bd97e203430d809740b47736b9b8"/>
</EnvironmentVariables>
```

```C#
string eventStorePassword =  Environment.GetEnvironmentVariable("EventStorePassword");
```
## <a name="keyvault-secret-as-container-repository-password"></a>Keyvault tajny jako hasło repozytorium kontenerów
KeyVaultReference jest obsługiwanym typem dla repozytorium kontenerówCredentials, poniżej pokazano, jak używać odwołania keyvault jako hasło repozytorium kontenera.
```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="user1" Type="KeyVaultReference" Password="https://ttkvault.vault.azure.net/secrets/containerpwd/e225bd97e203430d809740b47736b9b8"/>
      </ContainerHostPolicies>
```
## <a name="faq"></a>Najczęściej zadawane pytania
- Tożsamość zarządzana musi być włączona dla obsługi KeyVaultReference, aktywacja aplikacji zakończy się niepowodzeniem, jeśli KeyVaultReference jest używany bez włączania tożsamości zarządzanej.

- Jeśli używasz tożsamości przypisanej do systemu, jest ona tworzona tylko po wdrożeniu aplikacji, co powoduje utworzenie zależności cyklicznej. Po wdrożeniu aplikacji można udzielić systemowi uprawnienia dostępu do tożsamości przypisanemu do usługi keyvault. Tożsamość przypisaną do systemu można znaleźć według nazwy {cluster}/{nazwa aplikacji}/{nazwa usługi}

- Keyvault musi być w tej samej subskrypcji co klaster sieci szkieletowej usług. 

## <a name="next-steps"></a>Następne kroki

* [Dokumentacja usługi Azure KeyVault](https://docs.microsoft.com/azure/key-vault/)
