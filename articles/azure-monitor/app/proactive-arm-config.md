---
title: Konfigurowanie ustawień reguły wykrywania inteligentnego Application Insights platformy Azure przy użyciu szablonów Azure Resource Manager | Microsoft Docs
description: Automatyzowanie zarządzania i konfiguracji Application Insights reguł wykrywania inteligentnego na platformie Azure przy użyciu szablonów Azure Resource Manager
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 06/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 8b55271b39bf2a65dababbef58f7389ca07d57d8
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72818835"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Zarządzanie Application Insights regułami wykrywania inteligentnego przy użyciu szablonów Azure Resource Manager

Reguły inteligentnego wykrywania w Application Insights mogą być zarządzane i konfigurowane przy użyciu [szablonów Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md).
Tej metody można użyć podczas wdrażania nowych zasobów Application Insights z automatyzacją Azure Resource Manager lub do modyfikowania ustawień istniejących zasobów.

## <a name="smart-detection-rule-configuration"></a>Konfiguracja reguły wykrywania inteligentnego

Dla reguły wykrywania inteligentnego można skonfigurować następujące ustawienia:
- Jeśli reguła jest włączona (wartość domyślna to **true**).
- Jeśli wiadomości e-mail powinny być wysyłane do użytkowników skojarzonych z [czytnikiem monitorowania](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) subskrypcji i [monitorować role współautor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) po znalezieniu wykrywania (wartość domyślna to **true**).
- Wszyscy dodatkowi Adresaci poczty e-mail, którzy powinni otrzymać powiadomienie po znalezieniu wykrywania.
    -  Konfiguracja poczty e-mail nie jest dostępna dla reguł wykrywania inteligentnego oznaczonych jako _wersja zapoznawcza_.

Aby umożliwić Konfigurowanie ustawień reguły za pośrednictwem Azure Resource Manager, Konfiguracja reguły wykrywania inteligentnego jest teraz dostępna jako zasób wewnętrzny w ramach zasobu Application Insights o nazwie **ProactiveDetectionConfigs**.
W celu uzyskania maksymalnej elastyczności Każda reguła wykrywania inteligentnego może być skonfigurowana przy użyciu unikatowych ustawień powiadomień.

## 

## <a name="examples"></a>Przykłady

Poniżej przedstawiono kilka przykładów, które pokazują, jak skonfigurować ustawienia reguł wykrywania inteligentnego za pomocą szablonów Azure Resource Manager.
Wszystkie przykłady odnoszą się do zasobu Application Insights o nazwie _"moja aplikacja"_ i do "reguły wykrywania" długiego czasu trwania zależności ", która jest wewnętrznie nazywana _" longdependencyduration "_ .
Pamiętaj, aby zastąpić nazwę zasobu Application Insights i określić odpowiednią nazwę wewnętrzną reguły wykrywania inteligentnego. Zapoznaj się z poniższą tabelą, aby wyświetlić listę odpowiednich wewnętrznych nazw Azure Resource Manager dla każdej reguły wykrywania inteligentnego.

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

### <a name="disable-sending-email-notifications-for-a-smart-detection-rule"></a>Wyłącz wysyłanie powiadomień e-mail dla reguły wykrywania inteligentnego

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

### <a name="add-additional-email-recipients-for-a-smart-detection-rule"></a>Dodawanie dodatkowych odbiorców poczty e-mail dla reguły wykrywania inteligentnego

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

### <a name="failure-anomalies-v2-non-classic-alert-rule"></a>Reguła alertu o niepowodzeniu w wersji 2 (nieklasycznej)

Ten szablon Azure Resource Manager demonstruje konfigurację reguły alertu o anomalii niepowodzeń v2 o ważności 2. Ta nowa wersja reguły alertu dotyczącego anomalii awarii jest częścią nowej platformy alertów platformy Azure i zastępuje wersję klasyczną, która jest wycofywana w ramach procesu wycofywania [alertów klasycznych](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "microsoft.alertsmanagement/smartdetectoralertrules",
            "apiVersion": "2019-03-01",
            "name": "Failure Anomalies - my-app",
            "location": "global", 
            "properties": {
                  "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls.",
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
> Ten szablon Azure Resource Manager jest unikatowy dla reguły alertu niepowodzenia anomalii v2 i różni się od innych klasycznych reguł wykrywania inteligentnego opisanych w tym artykule.   

## <a name="smart-detection-rule-names"></a>Nazwy reguł wykrywania inteligentnego

Poniżej znajduje się tabela nazw reguł wykrywania inteligentnego, która pojawia się w portalu wraz z nazwami wewnętrznymi, które powinny być używane w szablonie Azure Resource Manager.

> [!NOTE]
> Reguły inteligentnego wykrywania oznaczone jako _wersja zapoznawcza_ nie obsługują powiadomień e-mail. W związku z tym można ustawić tylko właściwość _Enabled_ dla tych reguł. 

| Nazwa reguły Azure Portal | Nazwa wewnętrzna
|:---|:---|
| Długi czas ładowania strony | slowpageloadtime |
| Wolny czas odpowiedzi serwera | slowserverresponsetime |
| Czas trwania długiej zależności | longdependencyduration |
| Spadek czasu odpowiedzi serwera | degradationinserverresponsetime |
| Pogorszenie czasu trwania zależności | degradationindependencyduration |
| Spadek współczynnika ważności śledzenia (wersja zapoznawcza) | extension_traceseveritydetector |
| Nietypowy wzrost w woluminie wyjątku (wersja zapoznawcza) | extension_exceptionchangeextension |
| Wykryto Potencjalny przeciek pamięci (wersja zapoznawcza) | extension_memoryleakextension |
| Wykryto potencjalny problem z zabezpieczeniami (wersja zapoznawcza) | extension_securityextensionspackage |
| Nietypowy wzrost ilości danych dziennych (wersja zapoznawcza) | extension_billingdatavolumedailyspikeextension |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat automatycznego wykrywania:

- [Anomalie błędów](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Przecieki pamięci](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Anomalie wydajności](../../azure-monitor/app/proactive-performance-diagnostics.md)
