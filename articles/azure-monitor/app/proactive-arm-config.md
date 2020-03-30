---
title: Ustawienia reguł wykrywania inteligentnego — usługa Azure Application Insights
description: Automatyzacja zarządzania i konfiguracji reguł inteligentnego wykrywania usługi Azure Application Insights za pomocą szablonów usługi Azure Resource Manager
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 06/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 7ca4df620739b2ab55b8ba986031cc48fe87f1fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294917"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Zarządzanie regułami inteligentnego wykrywania usługi Application Insights przy użyciu szablonów usługi Azure Resource Manager

Reguły wykrywania inteligentnego w usłudze Application Insights można zarządzać i konfigurować za pomocą [szablonów usługi Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md).
Tej metody można użyć podczas wdrażania nowych zasobów usługi Application Insights za pomocą automatyzacji usługi Azure Resource Manager lub do modyfikowania ustawień istniejących zasobów.

## <a name="smart-detection-rule-configuration"></a>Konfiguracja reguł wykrywania inteligentnego

Dla reguły wykrywania inteligentnego można skonfigurować następujące ustawienia:
- Jeśli reguła jest włączona (wartość domyślna to **prawda).**
- Jeśli wiadomości e-mail powinny być wysyłane do użytkowników skojarzonych z [programem monitorowania](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) subskrypcji i [monitorowanie roli współautora](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) po znalezieniu wykrywania (wartość domyślna jest **true**.)
- Wszelkie dodatkowe adresaci wiadomości e-mail, którzy powinni otrzymać powiadomienie po wykryciu.
    -  Konfiguracja poczty e-mail nie jest dostępna w przypadku reguł inteligentnego wykrywania _oznaczonych_jako podgląd .

Aby zezwolić na konfigurowanie ustawień reguł za pośrednictwem usługi Azure Resource Manager, konfiguracja reguły wykrywania inteligentnego jest teraz dostępna jako zasób wewnętrzny w zasobie usługi Application Insights o nazwie **ProactiveDetectionConfigs**.
Aby zapewnić maksymalną elastyczność, każdą regułę inteligentnego wykrywania można skonfigurować za pomocą unikatowych ustawień powiadomień.

## <a name="examples"></a>Przykłady

Poniżej znajduje się kilka przykładów pokazujących, jak skonfigurować ustawienia reguł wykrywania inteligentnego przy użyciu szablonów usługi Azure Resource Manager.
Wszystkie przykłady odnoszą się do zasobu usługi Application Insights o nazwie _"myApplication"_ oraz do "reguły inteligentnego wykrywania długiego czasu trwania zależności", która nosi nazwę _"longdependuration"._
Upewnij się, aby zastąpić nazwę zasobu usługi Application Insights i określić odpowiednią nazwę wewnętrzną reguły inteligentnego wykrywania. Sprawdź w poniższej tabeli listę odpowiednich wewnętrznych nazw usługi Azure Resource Manager dla każdej reguły wykrywania inteligentnego.

### <a name="disable-a-smart-detection-rule"></a>Wyłączanie reguły wykrywania inteligentnego

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

### <a name="disable-sending-email-notifications-for-a-smart-detection-rule"></a>Wyłączanie wysyłania powiadomień e-mail dla reguły inteligentnego wykrywania

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

### <a name="add-additional-email-recipients-for-a-smart-detection-rule"></a>Dodawanie dodatkowych adresatów wiadomości e-mail dla reguły inteligentnego wykrywania

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


## <a name="smart-detection-rule-names"></a>Nazwy reguł wykrywania inteligentnego

Poniżej znajduje się tabela nazw reguł wykrywania inteligentnego, które pojawiają się w portalu, wraz z ich nazwami wewnętrznymi, które powinny być używane w szablonie usługi Azure Resource Manager.

> [!NOTE]
> Reguły wykrywania _inteligentnego_ oznaczone jako podgląd nie obsługują powiadomień e-mail. W związku z tym można ustawić tylko _włączoną_ właściwość dla tych reguł. 

| Nazwa reguły portalu azure | Nazwa wewnętrzna
|:---|:---|
| Powolny czas ładowania strony | slowpageloadtime (czas ładowania stron) |
| Powolny czas odpowiedzi serwera | slowserverresponsetime |
| Długi czas trwania zależności | długozależność |
| Degradacja czasu reakcji serwera | degradacja w czasie reakcji |
| Degradacja czasu trwania zależności | degradacji w zależnościoduduration |
| Degradacja współczynnika ważności śledzenia (podgląd) | extension_traceseveritydetector |
| Nieprawidłowy wzrost liczby wyjątków (podgląd) | extension_exceptionchangeextension |
| Wykryto potencjalny wyciek pamięci (podgląd) | extension_memoryleakextension |
| Wykryto potencjalny problem z zabezpieczeniami (wersja zapoznawcza) | extension_securityextensionspackage |
| Nieprawidłowy wzrost dziennej ilości danych (podgląd) | extension_billingdatavolumedailyspikeextension |

### <a name="failure-anomalies-alert-rule"></a>Reguła alertu Anomalie błędów

Ten szablon usługi Azure Resource Manager demonstruje konfigurowanie reguły alertu anomalie awarii o ważności 2. Ta nowa wersja reguły alertu Anomalie awarii jest częścią nowej platformy alertów platformy Azure i zastępuje klasyczną wersję, która jest wycofywana w ramach [klasycznego procesu wycofywania alertów.](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/)

> [!NOTE]
> Anomalie błędów to usługa globalna, dlatego lokalizacja reguły jest tworzona w lokalizacji globalnej.

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
> Ten szablon usługi Azure Resource Manager jest unikatowy dla reguły alertu anomalie awarii i różni się od innych klasycznych reguł inteligentnego wykrywania opisanych w tym artykule. Jeśli chcesz ręcznie zarządzać anomaliami awarii, odbywa się to w alertach usługi Azure Monitor, podczas gdy wszystkie inne reguły wykrywania inteligentnego są zarządzane w okienku inteligentne wykrywanie interfejsu użytkownika.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o automatycznym wykrywaniu:

- [Anomalie błędów](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Przecieki pamięci](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Anomalie wydajności](../../azure-monitor/app/proactive-performance-diagnostics.md)
