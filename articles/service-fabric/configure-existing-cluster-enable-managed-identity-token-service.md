---
title: Azure Service Fabric — Skonfiguruj istniejący klaster Service Fabric platformy Azure, aby umożliwić obsługę tożsamości zarządzanych
description: W tym artykule opisano sposób konfigurowania istniejącego klastra Service Fabric platformy Azure w celu włączenia obsługi tożsamości zarządzanych
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 13b8b38a206b0dae0877263a5cda56a134d4788d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75351607"
---
# <a name="configure-an-existing-azure-service-fabric-cluster-to-enable-managed-identity-support-preview"></a>Skonfiguruj istniejący klaster Service Fabric platformy Azure, aby włączyć obsługę tożsamości zarządzanych (wersja zapoznawcza)
Aby uzyskać dostęp do funkcji tożsamości zarządzanej dla aplikacji Service Fabric platformy Azure, należy najpierw włączyć **usługę tokenu tożsamości zarządzanej** w klastrze. Ta usługa jest odpowiedzialna za uwierzytelnianie aplikacji Service Fabric przy użyciu ich tożsamości zarządzanych i uzyskiwania tokenów dostępu w ich imieniu. Po włączeniu usługi zobaczysz ją w Service Fabric Explorer w sekcji **system** w okienku po lewej stronie, działając w obszarze Nazwa **sieci szkieletowej:/system/ManagedIdentityTokenService**.

> [!NOTE]
> Aby włączyć **usługę zarządzanego tokenu tożsamości**, wymagana jest Service Fabric środowiska uruchomieniowego w wersji 6.5.658.9590 lub nowszej.  
> 
> Service Fabric wersję klastra można znaleźć z poziomu Azure Portal, otwierając zasób klastra i sprawdzając Właściwość **wersja Service Fabric** w sekcji **podstawowe** informacje.
> 
> Jeśli klaster działa w trybie uaktualniania **ręcznego** , należy najpierw uaktualnić go do 6.5.658.9590 lub nowszego.


## <a name="enable-the-managed-identity-token-service-in-an-existing-cluster"></a>Włączanie usługi Managed Identity Tokens w istniejącym klastrze
Aby włączyć usługę tokenu tożsamości zarządzanej w istniejącym klastrze, należy zainicjować uaktualnienie klastra, określając dwie zmiany: włączenie usługi Managed Identity Tokens i zażądanie ponownego uruchomienia każdego węzła. Aby to zrobić, Dodaj następujące dwa fragmenty kodu w szablonie Azure Resource Manager:

```json
"fabricSettings": [
    {
        "name": "ManagedIdentityTokenService",
        "parameters": [
            {
                "name": "IsEnabled",
                "value": "true"
            }
        ]
    }
]
```

Aby zmiany zaczęły obowiązywać, należy również zmienić zasady uaktualniania, aby określić wymuszenie ponownego uruchomienia Service Fabric środowiska uruchomieniowego na każdym węźle w miarę postępu uaktualniania przez klaster. To ponowne uruchomienie zapewnia, że nowo włączona usługa systemu zostanie uruchomiona i uruchomiona w każdym węźle. W poniższym fragmencie kodu `forceRestart` jest to podstawowe ustawienie; Użyj istniejących wartości dla pozostałej części ustawień.  

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

> [!NOTE]
> Po pomyślnym zakończeniu uaktualniania nie zapomnij wycofać ustawienia `forceRestart`, aby zminimalizować wpływ kolejnych uaktualnień. 

## <a name="errors-and-troubleshooting"></a>Błędy i rozwiązywanie problemów

Jeśli wdrożenie zakończy się niepowodzeniem z następującym komunikatem, oznacza to, że klaster nie jest uruchomiony na wystarczająco wysokiej wersji Service Fabric:

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>Następne kroki
* [Wdrażanie aplikacji Service Fabric platformy Azure przy użyciu tożsamości zarządzanej przypisanej do systemu](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Wdrażanie aplikacji Service Fabric platformy Azure przy użyciu tożsamości zarządzanej przypisanej przez użytkownika](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Korzystanie z zarządzanej tożsamości aplikacji Service Fabric z poziomu kodu usługi](./how-to-managed-identity-service-fabric-app-code.md)
* [Przyznaj aplikacji Service Fabric platformy Azure dostęp do innych zasobów platformy Azure](./how-to-grant-access-other-resources.md)