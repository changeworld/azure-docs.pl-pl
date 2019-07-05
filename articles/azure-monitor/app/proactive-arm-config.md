---
title: Skonfiguruj ustawienia reguły wykrywania inteligentnego usługi Azure Application Insights przy użyciu szablonów usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: Automatyzowanie zarządzania i konfiguracji reguły wykrywania inteligentnego usługi Azure Application Insights przy użyciu szablonów usługi Azure Resource Manager
services: application-insights
documentationcenter: ''
author: harelbr
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/26/2019
ms.reviewer: mbullwin
ms.author: harelbr
ms.openlocfilehash: 6bb89eec0b4905e101bed87d3d3fc617dec589e0
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477859"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Zarządzanie regułami wykrywania inteligentnego usługi Application Insights przy użyciu szablonów usługi Azure Resource Manager

Reguły wykrywania inteligentnego w usłudze Application Insights można zarządzać, a także skonfigurować przy użyciu [szablonów usługi Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md).
Ta metoda może służyć w przypadku wdrażania nowych zasobów usługi Application Insights z usługą automation usługi Azure Resource Manager lub modyfikując ustawienia istniejących zasobów.

## <a name="smart-detection-rule-configuration"></a>Konfiguracja reguły wykrywania inteligentnego

Można skonfigurować następujące ustawienia dla reguły wykrywania inteligentnego:
- Jeśli zasada jest włączona (wartość domyślna to **true**.)
- Jeśli wiadomości e-mail powinny być przesyłane do użytkowników skojarzonych z subskrypcją [Czytelnik monitorowania](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) i [Współautor monitorowania](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) role po znalezieniu wykrycie (wartość domyślna to **true**.)
- Znajduje wszystkie dodatkowi adresaci wiadomości e-mail, które powinien otrzymywać powiadomienie, gdy wykrycie.
    -  Konfiguracja poczty e-mail jest niedostępna dla reguły wykrywania inteligentnego oznaczone jako _Podgląd_.

Aby zezwolić na konfigurowanie ustawień reguły za pomocą usługi Azure Resource Manager, konfiguracji reguły wykrywania inteligentnego jest teraz dostępny jako wewnętrzny zasobów w ramach zasobu usługi Application Insights, o nazwie **ProactiveDetectionConfigs**.
Maksymalny elastyczności każdej reguły wykrywania inteligentnego można skonfigurować ustawienia powiadomień unikatowy.

## 

## <a name="examples"></a>Przykłady

Poniżej przedstawiono kilka przykładów, w którym pokazano, jak skonfigurować ustawienia reguły wykrywania inteligentnego, za pomocą szablonów usługi Azure Resource Manager.
Wszystkie przykłady odnoszą się do zasobu usługi Application Insights o nazwie _"MojaAplikacja"_ , i "zależności długi czas trwania wykrywania inteligentnego reguły", która wewnętrznie nosi nazwę _"longdependencyduration"_ .
Upewnij się, zastąp nazwę zasobu usługi Application Insights i określ nazwę wewnętrznego reguły wykrywania inteligentnego odpowiednie. Sprawdź w tabeli poniżej listy odpowiednie wewnętrzne nazwy usługi Azure Resource Manager dla każdej reguły wykrywania inteligentnego.

### <a name="disable-a-smart-detection-rule"></a>Wyłącz regułę wykrywania inteligentnego

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": [],
            "enabled": false
          }
        }
      ]
    }
```

### <a name="disable-sending-email-notifications-for-a-smart-detection-rule"></a>Wyłączanie wysyłania powiadomień e-mail dla reguły wykrywania inteligentnego

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": false,
            "customEmails": [],
            "enabled": true
          }
        }
      ]
    }
```

### <a name="add-additional-email-recipients-for-a-smart-detection-rule"></a>Dodaj dodatkowi adresaci wiadomości e-mail dla reguły wykrywania inteligentnego

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": ['alice@contoso.com', 'bob@contoso.com'],
            "enabled": true
          }
        }
      ]
    }

```

### <a name="failure-anomalies-v2-non-classic-alert-rule"></a>Anomalie w wersji 2 (inne niż wersja klasyczna) reguła alertu o niepowodzeniu

Ten szablon usługi Azure Resource Manager pokazuje konfigurowanie reguły alertu anomalie w wersji 2 przy użyciu ważność 2. Nowa wersja anomalie reguły alertu jest częścią nowej platformy Azure, zgłaszania alertów platformy i zastępuje klasycznej wersji, która zostanie wycofana w ramach [classic alerty procesu wycofywania](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "microsoft.alertsmanagement/smartdetectoralertrules",
            "apiVersion": "2019-03-01",
            "name": "Failure Anomalies - my-app",
            "properties": {
                  "description": "Detects a spike in the failure rate of requests or dependencies",
                  "state": "Enabled",
                  "severity": "2",
                  "frequency": "PT1M",
                  "detector": {
                  "id": "FailureAnomaliesDetector"
                  },
                  "scope": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/MyResourceGroup/providers/microsoft.insights/components/my-app"],
                  "actionGroups": {
                        "groupIds": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourcegroups/MyResourceGroup/providers/microsoft.insights/actiongroups/MyActionGroup"]
                  }
            }
        }
    ]
}
```

> [!NOTE]
> Ten szablon usługi Azure Resource Manager jest unikatowy dla reguły alertu v2 anomalie i różni się od innych klasyczne reguły wykrywania inteligentnego opisanego w tym artykule.   

## <a name="smart-detection-rule-names"></a>Nazwy reguł wykrywania inteligentnego

Poniżej znajduje się tabela nazwy reguł wykrywania inteligentnego, w jakiej występują w portalu wraz z ich nazwy wewnętrzne, które powinny być używane w szablonie usługi Azure Resource Manager.

> [!NOTE]
> Reguły wykrywania inteligentnego oznaczone jako _Podgląd_ nie obsługują powiadomienia e-mail. W związku z tym, można ustawić tylko _włączone_ właściwość dla tych zasad. 

| Nazwa reguły portalu Azure | Nazwa wewnętrzna
|:---|:---|
| Długi czas ładowania strony | slowpageloadtime |
| Długi czas odpowiedzi serwera | slowserverresponsetime |
| Długi czas trwania zależności | longdependencyduration |
| Pogorszenie czasu odpowiedzi serwera | degradationinserverresponsetime |
| Pogorszenie czasu trwania zależności | degradationindependencyduration |
| Obniżenie wydajności stopień ważności śledzenia (wersja zapoznawcza) | extension_traceseveritydetector |
| Nietypowy wzrost liczby wyjątków (wersja zapoznawcza) | extension_exceptionchangeextension |
| Potencjalny przeciek pamięci wykryto (wersja zapoznawcza) | extension_memoryleakextension |
| Potencjalny problem z zabezpieczeniami wykryto (wersja zapoznawcza) | extension_securityextensionspackage |
| Nietypowy wzrost liczby dzienna ilość danych (wersja zapoznawcza) | extension_billingdatavolumedailyspikeextension |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat automatycznego wykrywania:

- [Anomalie błędów](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Przecieki pamięci](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Anomalie wydajności](../../azure-monitor/app/proactive-performance-diagnostics.md)
