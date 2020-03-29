---
title: Konfigurowanie obsługi tożsamości zarządzanych w istniejącym klastrze sieci szkieletowej usług
description: Poniżej opisano, jak włączyć obsługę tożsamości zarządzanych w istniejącym klastrze sieci szkieletowej usług Azure
ms.topic: article
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: cb6e4ab00afd80cba41881e46296f7046a905919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934947"
---
# <a name="configure-managed-identity-support-in-an-existing-service-fabric-cluster-preview"></a>Konfigurowanie obsługi tożsamości zarządzanych w istniejącym klastrze sieci szkieletowej usług (wersja zapoznawcza)

Aby użyć [tożsamości zarządzanych dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) w aplikacjach sieci szkieletowej usług, należy najpierw włączyć usługę *tokenu tożsamości zarządzanej* w klastrze. Ta usługa jest odpowiedzialna za uwierzytelnianie aplikacji sieci szkieletowej usług przy użyciu ich tożsamości zarządzanych i uzyskiwanie tokenów dostępu w ich imieniu. Po włączeniu usługi można ją wyświetlić w Eksploratorze sieci szkieletowej usług w sekcji **System** w lewym okienku, działającej pod nazwą **sieci szkieletowej:/System/ManagedIdentityTokenService**.

> [!NOTE]
> Aby włączyć **usługę Token Tokenu Tożsamości Zarządzanej,** wymagana jest wersja wykonawcza sieci szkieletowej usług w wersji 6.5.658.9590 lub nowszej.  
>
> Wersję sieci szkieletowej usług klastra można znaleźć w portalu Azure, otwierając zasób klastra i sprawdzając właściwość **wersji sieci szkieletowej usług** w sekcji **Essentials.**
>
> Jeśli klaster jest w trybie **ręcznego** uaktualniania, należy najpierw uaktualnić go do 6.5.658.9590 lub nowszego.

## <a name="enable-managed-identity-token-service-in-an-existing-cluster"></a>Włączanie *usługi tokenu tożsamości zarządzanej* w istniejącym klastrze

Aby włączyć usługę tokenu tożsamości zarządzanej w istniejącym klastrze, należy zainicjować uaktualnienie klastra określające dwie zmiany: (1) Włączanie usługi tokenu tożsamości zarządzanej i (2) żądanie ponownego uruchomienia każdego węzła. Najpierw dodaj następujący fragment kodu szablonu usługi Azure Resource Manager klastra:

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

Aby zmiany zostały wprowadzone, należy również zmienić zasady uaktualniania, aby określić silne ponowne uruchomienie środowiska uruchomieniowego sieci szkieletowej usług w każdym węźle w miarę postępu uaktualniania za pośrednictwem klastra. To ponowne uruchomienie gwarantuje, że nowo włączona usługa systemowa jest uruchomiona i uruchomiona w każdym węźle. We poniższym urywek `forceRestart` jest podstawowym ustawieniem; użyj istniejących wartości dla pozostałych ustawień.  

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
> Po pomyślnym zakończeniu uaktualnienia, nie zapomnij `forceRestart` wycofać ustawienie, aby zminimalizować wpływ kolejnych uaktualnień. 

## <a name="errors-and-troubleshooting"></a>Błędy i rozwiązywanie problemów

Jeśli wdrożenie nie powiedzie się z następującym komunikatem, oznacza to, że klaster nie jest uruchomiony w wersji sieci szkieletowej o wystarczającej wysokości:

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>Następne kroki
* [Wdrażanie aplikacji sieci szkieletowej usług Azure z tożsamością zarządzaną przypisaną do systemu](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Wdrażanie aplikacji sieci szkieletowej usług Azure z tożsamością zarządzaną przypisaną przez użytkownika](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Wykorzystaj zarządzaną tożsamość aplikacji sieci szkieletowej usług z kodu usługi](./how-to-managed-identity-service-fabric-app-code.md)
* [Udzielanie dostępu aplikacji sieci szkieletowej usługi Azure do innych zasobów platformy Azure](./how-to-grant-access-other-resources.md)