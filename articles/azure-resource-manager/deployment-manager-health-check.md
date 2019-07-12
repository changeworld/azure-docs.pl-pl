---
title: Wprowadzenie do usługi Azure Deployment Manager wdrożenie integracji kondycji
description: W tym artykule opisano, jak wdrożyć usługę w wielu regionach za pomocą usługi Azure Deployment Manager. Przedstawia on praktyki bezpiecznego wdrażania, aby sprawdzić stabilność wdrożenie, zanim zetknie się we wszystkich regionach.
services: azure-resource-manager
documentationcenter: na
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: jgao
ms.openlocfilehash: 0c6d32f06e30bd85c12967ce4b15a053bb505ab7
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594306"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>Wprowadzenie do usługi Azure Deployment Manager (publiczna wersja zapoznawcza) wdrożenia integracji kondycji

[Azure Deployment Manager](./deployment-manager-overview.md) umożliwia wykonywanie wprowadzanie przygotowanych zasobów usługi Azure Resource Manager. Zasoby są wdrażane przez regionu, w sposób uporządkowany. Sprawdzenie kondycji zintegrowane usługi Azure Deployment Manager można monitorować wprowadzanie i automatycznie wprowadzanie problematyczne stop, dzięki czemu mogą Rozwiązywanie problemów i Zmniejsz skalę wpływ. Ta funkcja może ograniczyć niedostępność usługi spowodowane wystąpieniu regresji w aktualizacji.

## <a name="health-monitoring-providers"></a>Dostawcy monitorowania kondycji

W celu integracji kondycji tak proste, jak to możliwe, Microsoft pracował nad z niektórymi monitorowania firmy mogą udostępniać prostego kopiowania/wklejania rozwiązania do integracji kontroli kondycji z wdrożeniami kondycji najważniejszych usług. Jeśli jeszcze nie używasz monitor kondycji, są to wspaniałe rozwiązania zacząć:

| ![wdrożenie usługi Azure Menedżera kondycji monitora dostawcy pomocą usługi datadog](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![wdrożenie usługi Azure Menedżera kondycji monitora dostawcy site24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![wdrożenie usługi Azure Menedżera kondycji monitora dostawcy wavefront](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|------|------|
|Pomocą usługi Datadog, wiodący monitorowanie i platforma analiz dla środowisk nowoczesnym rozwiązaniom w chmurze. Zobacz [jak pomocą usługi Datadog integruje się z usługą Azure Deployment Manager](https://www.datadoghq.com/azure-deployment-manager/).|Site24x7, chmury prywatnej i publicznej w jednym usług rozwiązania do monitorowania. Zobacz [jak Site24x7 integruje się z usługą Azure Deployment Manager](https://www.site24x7.com/azure/adm.html).| Wavefront, analizowania i monitorowania platformy dla aplikacji w chmurze w wielu środowiskach. Zobacz [jak Wavefront integruje się z usługą Azure Deployment Manager](https://go.wavefront.com/wavefront-adm/).|

## <a name="how-service-health-is-determined"></a>Sposób ustalania kondycji usługi

[Dostawcy monitorowania kondycji](#health-monitoring-providers) oferuje kilka mechanizmów monitorowania usługi i wysyłać alerty o wszelkich problemów dotyczących kondycji usługi. [Usługa Azure Monitor](../azure-monitor/overview.md) znajduje się przykład jednego ofertą. Usługa Azure Monitor może służyć do tworzenia alertów, po przekroczeniu określonych wartości progowych. Na przykład Twoje pamięci i Procesora CPU Skacze powyżej oczekiwanego poziomu podczas wdrażania nowej aktualizacji do usługi. Po wyświetleniu powiadomienia, można wykonać akcje naprawcze.

Te opiekę zdrowotną oferują zazwyczaj interfejsów API REST, dzięki czemu można programowo zbadać stan monitorów z usługą. Interfejsy API REST można albo wróć za pomocą prostego sygnału dobrej kondycji/złej kondycji (według kodu odpowiedzi HTTP) i/lub szczegółowe informacje o sygnały, że odbiera.

Nowy *healthCheck* krok w usłudze Azure Deployment Manager pozwala zadeklarować kody HTTP, które wskazują usług w dobrej kondycji, ale dla bardziej złożonych wyników REST, można nawet określić wyrażeń regularnych, które są zgodne, wskazywać dobrej kondycji odpowiedź.

Przepływ do pobierania Instalatora z kontroli kondycji platformy Azure Deployment Manager:

1. Tworzenie usługi za pomocą wybranego dostawcy usługi kondycji z monitorów kondycji.
1. Utwórz co najmniej jednego kroku test kondycji w ramach wdrożenia usługi Azure Deployment Manager. Wypełnij test kondycji kroki z następującymi informacjami:

    1. Identyfikator URI dla interfejsu API REST dla usługi kondycji monitoruje (zdefiniowanej przez dostawcę usług kondycji).
    1. Informacje o uwierzytelnianiu. Obecnie obsługiwane jest tylko uwierzytelnianie kluczy interfejsu API w stylu.
    1. [Kody stanu HTTP](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes) lub wyrażeniami regularnymi definiującymi odpowiedzi dobrej kondycji. Należy pamiętać, że może zapewnić wyrażeń regularnych, które musi być wszystkie dopasowania dla odpowiedzi, aby zostały uznane za dobrej kondycji, lub może dostarczyć wyrażeń, których DOWOLNE musi odpowiadać odpowiedzi zostały uznane za dobrej kondycji. Obie metody są obsługiwane.

    Następujące dane Json znajduje się przykład:

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT0M",
          "maxElasticDuration": "PT0M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "ApiKey",
                    "name": "code",
                    "in": "Query",
                    "value": "[parameters('healthCheckAuthAPIKey')]"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "Status: healthy",
                      "Status: warning"
                    ],
                    "matchQuantifier": "Any"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

1. Wywołania funkcji healthCheck kroki w odpowiednim czasie w swoim wdrożeniu platformy Azure Deployment Manager. W poniższym przykładzie kroku sprawdzenia kondycji jest wywoływana w **postDeploymentSteps** z **stepGroup2**.

    ```json
    "stepGroups": [
        {
            "name": "stepGroup1",
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup2",
            "dependsOnStepGroups": ["stepGroup1"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit1.name)]",
            "postDeploymentSteps": [
                {
                    "stepId": "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
                }
            ]
        },
        {
            "name": "stepGroup3",
            "dependsOnStepGroups": ["stepGroup2"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup4",
            "dependsOnStepGroups": ["stepGroup3"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit1.name)]",
            "postDeploymentSteps": []
        }
    ]
    ```

Aby zapoznać się z przykładu, zobacz [samouczka: Użyj sprawdzania kondycji w usłudze Azure Deployment Manager](./deployment-manager-health-check.md).

## <a name="phases-of-a-health-check"></a>Fazy kontrola kondycji

W tym momencie Azure Deployment Manager wie, jak utworzyć zapytanie dotyczące kondycji usługi i na jakie etapy w swoim wdrożeniu, aby to zrobić. Jednak Azure Deployment Manager umożliwia również głębokiego konfiguracji w czasie tych sprawdzeń. Krok test kondycji jest wykonywane w 3 kolejne etapy, wszystkich z nich jest konfigurowalne czasów trwania: 

1. Oczekiwanie

    1. Po zakończeniu operacji wdrażania, może być ponowne uruchamianie maszyn wirtualnych, ponownej konfiguracji na podstawie nowych danych lub nawet uruchamiany po raz pierwszy. Ponadto czas na uruchomienie emitowanie sygnałów kondycji będą agregowane przez monitorowania dostawcy w coś, co jest przydatne kondycji usług. W trakcie tego procesu tumultuous go może nie mieć sensu pod kątem kondycji usługi, ponieważ aktualizacja nie została jeszcze osiągają stanu stabilnego. W rzeczywistości usługi może Densytometr między Stanami zdrowe i niezdrowe, jak zasoby rozliczenia. 
    1. W fazie oczekiwania usługi kondycji nie będzie monitorowana. To jest stosowane do umożliwienia wdrożonych zasobów czasu na tworzenie przed rozpoczęciem procesu sprawdzania kondycji. 
1. Elastyczność

    1. Ponieważ nie jest możliwe dowiedzieć się we wszystkich przypadkach, jak długo potrwa zasobów, wprowadzić, zanim staną się stabilne, elastyczne fazy umożliwia elastyczne okres między gdy wszystkie zasoby są potencjalnie niestabilny i kiedy są one wymagane do zachowania stabilności dobrej kondycji Stan.
    1. Po rozpoczęciu elastycznej fazy rozpoczyna się Azure Deployment Manager okresowo sondowanie podany punkt końcowy REST dla usługi kondycji. Interwał sondowania jest konfigurowalne. 
    1. Monitor kondycji wróci z sygnałów wskazująca, że usługa jest w złej kondycji, są ignorowane, te sygnały, nadal fazy elastycznej i kontynuuje sondowanie. 
    1. Zaraz po monitorowanie kondycji wróci z sygnałów wskazujący, że usługa jest w dobrej kondycji, elastyczne fazy kończy się i faza HealthyState rozpoczyna się. 
    1. W związku z tym czas trwania, określone w fazie elastycznej jest maksymalną ilość czasu, który można wydać sondowanie kondycji usługi przed dobrej kondycji odpowiedzi uważanego za obowiązkowy. 
1. HealthyState

    1. W fazie HealthyState usługi kondycji jest stale sondowania z takim samym okresem elastycznej fazy. 
    1. Usługa oczekuje się, utrzymanie dobrej kondycji sygnały od dostawcy monitorowania kondycji całego określonego czasu trwania. 
    1. Jeśli w dowolnym momencie odpowiedź złej kondycji zostanie wykryte, Azure Deployment Manager zatrzymać całego wdrożenia i zwraca odpowiedź REST przenosić sygnały usługi w złej kondycji.
    1. Po zakończeniu czasu trwania HealthyState test kondycji zostało zakończone, a wdrożenie nadal do następnego kroku.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano sposób integracji, monitorowanie kondycji w usłudze Azure Deployment Manager. Przejdź do następnego artykułu, aby dowiedzieć się, jak wdrożyć za pomocą programu Deployment Manager.

> [!div class="nextstepaction"]
> [Samouczek: integracja kontroli kondycji w usłudze Azure Deployment Manager](./deployment-manager-tutorial-health-check.md)