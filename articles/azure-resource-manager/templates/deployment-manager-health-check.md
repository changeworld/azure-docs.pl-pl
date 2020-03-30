---
title: Wdrażanie integracji kondycji — Usługa Azure Deployment Manager
description: W tym artykule opisano sposób wdrażania usługi w wielu regionach za pomocą usługi Azure Deployment Manager. Pokazuje bezpieczne praktyki wdrażania, aby zweryfikować stabilność wdrożenia przed wdrożeniem we wszystkich regionach.
author: mumian
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: jgao
ms.openlocfilehash: aa99bdfcbc2f42ae81bdd55c266bcd7d87808031
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273802"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>Wprowadzenie wdrożenia integracji kondycji do usługi Azure Deployment Manager (publiczna wersja zapoznawcza)

[Usługa Azure Deployment Manager](./deployment-manager-overview.md) umożliwia wykonywanie etapowych wdrożeń zasobów usługi Azure Resource Manager. Zasoby są wdrażane region po regionie w uporządkowany sposób. Zintegrowane sprawdzanie kondycji usługi Azure Deployment Manager może monitorować wdrożenia i automatycznie zatrzymywać problematyczne wdrożenia, dzięki czemu można rozwiązywać problemy i zmniejszać skalę wpływu. Ta funkcja może zmniejszyć dostępność usługi spowodowane przez regresji w aktualizacjach.

## <a name="health-monitoring-providers"></a>Dostawcy monitorowania zdrowia

Aby integracja kondycji była tak łatwa, jak to możliwe, firma Microsoft współpracuje z niektórymi z najlepszych firm monitorujących kondycję usług, aby zapewnić proste rozwiązanie do kopiowania/wklejania w celu zintegrowania kontroli kondycji z wdrożeniami. Jeśli nie używasz jeszcze monitora kondycji, są to świetne rozwiązania na początek:

| ![usługa azure deployment manager health monitor provider datadog](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![usługa azure deployment manager health monitor provider site24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![azure deployment manager health monitor provider wavefront](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|------|------|
|Datadog, wiodąca platforma monitorowania i analizy dla nowoczesnych środowisk w chmurze. Zobacz, [jak program Datadog integruje się z usługą Azure Deployment Manager.](https://www.datadoghq.com/azure-deployment-manager/)|Site24x7, rozwiązanie do monitorowania usług w chmurze prywatnej i publicznej typu "wszystko w jednym". Zobacz, [jak usługa Site24x7 integruje się z usługą Azure Deployment Manager.](https://www.site24x7.com/azure/adm.html)| Wavefront, platforma monitorowania i analizy dla środowisk aplikacji w wielu chmurach. Zobacz, [jak wavefront integruje się z usługą Azure Deployment Manager.](https://go.wavefront.com/wavefront-adm/)|

## <a name="how-service-health-is-determined"></a>Jak określa się kondycję usługi

[Dostawcy monitorowania kondycji](#health-monitoring-providers) oferują kilka mechanizmów monitorowania usług i ostrzegania o wszelkich problemach ze zdrowiem usługi. [Usługa Azure Monitor](../../azure-monitor/overview.md) jest przykładem jednej z takich ofert. Usługa Azure Monitor może służyć do tworzenia alertów po przekroczeniu niektórych progów. Na przykład wzrost wykorzystania pamięci i procesora CPU poza oczekiwanych poziomów podczas wdrażania nowej aktualizacji do usługi. Po powiadomieniu można podjąć działania naprawcze.

Te dostawców kondycji zazwyczaj oferują interfejsy API REST, dzięki czemu stan monitorów usługi mogą być badane programowo. Interfejsy API REST można wrócić z prostym zdrowy/niezdrowy sygnał (określone przez kod odpowiedzi HTTP) i/lub ze szczegółowymi informacjami o sygnałach, które otrzymuje.

Nowy krok *healthCheck* w usłudze Azure Deployment Manager umożliwia deklarowanie kodów HTTP, które wskazują na zdrową usługę, lub, w przypadku bardziej złożonych wyników REST, można nawet określić wyrażenia regularne, które, jeśli są zgodne, wskazują na odpowiedź w dobrej kondycji.

Przepływ do instalacji za pomocą kontroli kondycji usługi Azure Deployment Manager:

1. Stwórz swoje monitory zdrowia za pośrednictwem wybranego dostawcy usług zdrowotnych.
1. Utwórz co najmniej jeden healthCheck kroki w ramach wdrożenia usługi Azure Deployment Manager. Wypełnij healthCheck kroki z następujących informacji:

    1. Identyfikator URI dla interfejsu API REST dla monitorów kondycji (zgodnie z definicją dostawcy usług zdrowotnych).
    1. Informacje o uwierzytelnianiu. Obecnie obsługiwane jest tylko uwierzytelnianie w stylu klucza interfejsu API.
    1. [Kody stanu HTTP](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes) lub wyrażenia regularne definiujące odpowiedź w dobrej kondycji. Należy zauważyć, że można podać wyrażenia regularne, które wszystkie muszą być zgodne dla odpowiedzi, które mają być uznane za zdrowe, lub można podać wyrażenia, które any musi odpowiadać dla odpowiedzi, które mają być uważane za zdrowe. Obie metody są obsługiwane.

    Przykładem jest następujący Json:

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

1. Wywołaj healthCheck kroki w odpowiednim czasie we wdrażaniu usługi Azure Deployment Manager. W poniższym przykładzie krok sprawdzania kondycji jest wywoływany w **postDeploymentSteps** **stepGroup2**.

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

Aby przejść przez przykład, zobacz [Samouczek: Użyj sprawdzania kondycji w usłudze Azure Deployment Manager](./deployment-manager-health-check.md).

## <a name="phases-of-a-health-check"></a>Fazy kontroli stanu zdrowia

W tym momencie Usługa Azure Deployment Manager wie, jak kwerendy dla kondycji usługi i na jakich etapach wdrażania, aby to zrobić. Jednak usługa Azure Deployment Manager umożliwia również głęboką konfigurację czasu tych kontroli. HealthCheck krok jest wykonywany w 3 kolejnych fazach, z których wszystkie mają konfigurowalne czasy trwania: 

1. Oczekiwanie

    1. Po zakończeniu operacji wdrażania maszyny wirtualne mogą być ponowne uruchomienie, ponowne konfigurowanie na podstawie nowych danych, a nawet uruchamiane po raz pierwszy. Zajmuje również trochę czasu, aby usługi zaczęły emitować sygnały zdrowotne, które mają być agregowane przez dostawcę monitorowania kondycji w coś przydatnego. Podczas tego burzliwego procesu może nie ma sensu sprawdzanie kondycji usługi, ponieważ aktualizacja nie osiągnęła jeszcze stanu ustalonego. Rzeczywiście, usługa może być oscylujące między stanami zdrowymi i niezdrowymi, jak zasoby rozliczenia. 
    1. Podczas fazy oczekiwania kondycja usługi nie jest monitorowana. Służy do umożliwienia wdrożonych zasobów czas do pieczenia przed rozpoczęciem procesu sprawdzania kondycji. 
1. Elastyczne

    1. Ponieważ nie można wiedzieć we wszystkich przypadkach, jak długo zasoby będą trwać do pieczenia, zanim staną się stabilne, faza elastyczna pozwala na elastyczny okres między kiedy zasoby są potencjalnie niestabilne i kiedy są one wymagane do utrzymania zdrowego stały Państwa.
    1. Po rozpoczęciu fazy elastycznej usługa Azure Deployment Manager rozpoczyna okresowe sondowanie podanego punktu końcowego REST dla kondycji usługi. Interwał sondowania jest konfigurowalny. 
    1. Jeśli monitor kondycji powraca z sygnałami wskazującymi, że usługa jest w złej kondycji, sygnały te są ignorowane, faza elastyczna jest kontynuowana i sondowanie jest kontynuowane. 
    1. Jak tylko monitor kondycji powraca z sygnałami wskazującymi, że usługa jest w dobrej kondycji, faza elastyczna kończy się i rozpoczyna się faza HealthyState. 
    1. W związku z tym czas trwania określony dla fazy elastycznej jest maksymalną ilość czasu, który można spędzić sondowania kondycji usługi, zanim odpowiedź w dobrej kondycji jest uważany za obowiązkowe. 
1. ZdrowyStan

    1. Podczas fazy HealthyState kondycji usługi jest stale sondowane w tym samym przedziale co faza elastyczna. 
    1. Usługa ma zachować zdrowe sygnały od dostawcy monitorowania kondycji przez cały określony czas trwania. 
    1. Jeśli w dowolnym momencie zostanie wykryta odpowiedź w złej kondycji, usługa Azure Deployment Manager zatrzyma całe wdrożenie i zwróci odpowiedź REST niosącą sygnały usługi w złej kondycji.
    1. Po zakończeniu trwania HealthyState healthCheck jest zakończona, a wdrażanie jest kontynuowane do następnego kroku.

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak zintegrować monitorowanie kondycji w usłudze Azure Deployment Manager. Przejdź do następnego artykułu, aby dowiedzieć się, jak wdrożyć za pomocą Menedżera wdrażania.

> [!div class="nextstepaction"]
> [Samouczek: integrowanie sprawdzania kondycji w usłudze Azure Deployment Manager](./deployment-manager-tutorial-health-check.md)