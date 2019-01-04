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
ms.date: 07/19/2018
ms.reviewer: mbullwin
ms.author: harelbr
ms.openlocfilehash: b1f4d278079b81b4a224dc4712426d1f078de110
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54020408"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Zarządzanie regułami wykrywania inteligentnego usługi Application Insights przy użyciu szablonów usługi Azure Resource Manager

Reguły wykrywania inteligentnego w usłudze Application Insights można zarządzać, a także skonfigurować przy użyciu [szablonów usługi Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md).
Ta metoda może służyć w przypadku wdrażania nowych zasobów usługi Application Insights z usługą automation usługi Azure Resource Manager lub modyfikując ustawienia istniejących zasobów.

## <a name="smart-detection-rule-configuration"></a>Konfiguracja reguły wykrywania inteligentnego

Można skonfigurować następujące ustawienia dla reguły wykrywania inteligentnego:
- Jeśli zasada jest włączona (wartość domyślna to **true**.)
- Jeśli mają być wysyłane wiadomości e-mail do właścicieli subskrypcji, współautorzy i czytelnicy podczas wykrywania znajduje się (wartość domyślna to **true**.)
- Znajduje wszystkie dodatkowi adresaci wiadomości e-mail, które powinien otrzymywać powiadomienie, gdy wykrycie.

Aby zezwolić na konfigurowanie ustawień reguły za pomocą usługi Azure Resource Manager, konfiguracji reguły wykrywania inteligentnego jest teraz dostępny jako wewnętrzny zasobów w ramach zasobu usługi Application Insights, o nazwie **ProactiveDetectionConfigs**.
Maksymalny elastyczności każdej reguły wykrywania inteligentnego można skonfigurować ustawienia powiadomień unikatowy.

## <a name="examples"></a>Przykłady

Poniżej przedstawiono kilka przykładów, w którym pokazano, jak skonfigurować ustawienia reguły wykrywania inteligentnego, za pomocą szablonów usługi Azure Resource Manager.
Wszystkie przykłady odnoszą się do zasobu usługi Application Insights o nazwie _"MojaAplikacja"_, i "zależności długi czas trwania wykrywania inteligentnego reguły", która wewnętrznie nosi nazwę _"longdependencyduration"_.
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

## <a name="smart-detection-rule-names"></a>Nazwy reguł wykrywania inteligentnego

Poniżej znajduje się tabela nazwy reguł wykrywania inteligentnego, w jakiej występują w portalu wraz z ich nazwy wewnętrzne, które powinny być używane w szablonie usługi Azure Resource Manager.

> [!NOTE]
> Reguły wykrywania inteligentnego oznaczone jako wersji zapoznawczej nie obsługują powiadomienia e-mail. W związku z tym można ustawić tylko dla właściwości włączone dla tych reguł. 

| Nazwa reguły portalu Azure | Nazwa wewnętrzna
|:---|:---|
| Długi czas ładowania strony | slowpageloadtime |
| Długi czas odpowiedzi serwera | slowserverresponsetime |
| Długi czas trwania zależności | longdependencyduration |
| Wydłużenie czasu odpowiedzi serwera | degradationinserverresponsetime |
| Obniżenie w czasie trwania zależności | degradationindependencyduration |
| Obniżenie wydajności stopień ważności śledzenia (wersja zapoznawcza) | extension_traceseveritydetector |
| Nietypowy wzrost liczby wyjątków (wersja zapoznawcza) | extension_exceptionchangeextension |
| Potencjalny przeciek pamięci wykryto (wersja zapoznawcza) | extension_memoryleakextension |
| Potencjalny problem z zabezpieczeniami wykryto (wersja zapoznawcza) | extension_securityextensionspackage |
| Problem z wykorzystania zasobów wykryto (wersja zapoznawcza) | extension_resourceutilizationextensionspackage |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat automatycznego wykrywania:

- [Anomalie błędów](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Przecieki pamięci](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Anomalie wydajności](../../azure-monitor/app/proactive-performance-diagnostics.md)