---
title: Wskazówki dotyczące wdrażania aplikacji sieci web platformy Azure przy użyciu szablonów | Dokumentacja firmy Microsoft
description: Zalecenia dotyczące tworzenia szablonów usługi Azure Resource Manager do wdrażania aplikacji sieci web.
services: app-service
documentationcenter: app-service
author: tfitzmac
manager: timlt
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: tomfitz
ms.openlocfilehash: 8c29cf5a65e9587b281a6000b5b4eff47f11da91
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34807326"
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>Wskazówki dotyczące wdrażania aplikacji sieci web przy użyciu szablonów usługi Azure Resource Manager

Ten artykuł zawiera zalecenia dotyczące tworzenia szablonów usługi Azure Resource Manager, aby wdrożyć rozwiązania Azure App Service. Zalecenia te mogą pomóc w uniknięciu typowych problemów.

## <a name="define-dependencies"></a>Definiowanie zależności

Definiowanie zależności dla aplikacji sieci web wymaga zrozumienia interakcje między zasobami w aplikacji sieci web. Jeśli określisz zależności w niepoprawna kolejność może powodować błędy wdrażania lub Utwórz wyścigu zatrzymuje wdrożenia.

> [!WARNING]
> Jeśli rozszerzenie lokacji MSDeploy w szablonie, wszystkie zasoby konfiguracji musi być ustawiona jako zależy od zasobu MSDeploy. Zmiany konfiguracji powodują lokacji ponownie uruchomić asynchronicznie. Dzięki konfiguracji zasoby zależne MSDeploy, upewnieniu się, że MSDeploy kończy się przed ponownym uruchomieniem lokacji. Bez tych zależności lokacji może być ponownie podczas procesu wdrażania MSDeploy. Przykład szablonu, zobacz [szablonu WordPress z zależnością wdrażania Web](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json).

Na poniższej ilustracji przedstawiono kolejność zależności dla różnych zasobów usługi aplikacji:

![Zależności aplikacji sieci Web](media/web-sites-rm-template-guidance/web-dependencies.png)

Wdrażania zasobów w następującej kolejności:

**Warstwy 1**
* Plan usługi aplikacji.
* Innych powiązanych zasobów, takich jak bazy danych lub kont magazynu.

**Warstwy 2**
* Web app — zależy od planu usługi aplikacji.
* Azure wystąpienia usługi Application Insights, przeznaczonego dla farmy serwerów — zależy od planu usługi aplikacji.

**Warstwy 3**
* Formant--źródła zależy od aplikacji sieci web.
* Rozszerzenie lokacji MSDeploy--zależy od aplikacji sieci web.
* Wystąpienie usługi Insights aplikacji, przeznaczonego dla farmy serwerów — zależy od aplikacji sieci web.

**Warstwy 4**
* Certyfikat usługi aplikacji — zależy od kontroli źródła lub MSDeploy, jeśli jest obecny. W przeciwnym razie to zależy od aplikacji sieci web.
* Ustawienia konfiguracji (parametry połączenia, wartości pliku web.config, ustawienia aplikacji)--zależy od kontroli źródła lub MSDeploy jeśli spełniony jest obecny. W przeciwnym razie to zależy od aplikacji sieci web.

**Warstwa 5**
* Host name powiązania — zależy od certyfikatu, jeśli jest obecny. W przeciwnym razie zależy zasób wyższego poziomu.
* Rozszerzenia — lokacji zależy od ustawień konfiguracji, jeśli jest obecny. W przeciwnym razie zależy zasób wyższego poziomu.

Zazwyczaj rozwiązania zawiera tylko niektóre z tych zasobów i warstw. Brak warstw mapy niższe zasobów do następnego wyższej warstwy.

W poniższym przykładzie przedstawiono częścią szablonu. Wartość konfiguracji ciągu połączenia jest zależna od rozszerzenia MSDeploy. Rozszerzenie MSDeploy zależy od aplikacji sieci web i bazy danych. 

```json
{
    "name": "[parameters('appName')]",
    "type": "Microsoft.Web/Sites",
    ...
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'))]",
            "[concat('Microsoft.Sql/servers/', parameters('dbServerName'), '/databases/', parameters('dbName'))]",
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

Dla przykładu gotowości do uruchomienia korzystającego z powyższym kodzie, zobacz [szablonu: Tworzenie prostej aplikacji sieci Web Umbraco](https://github.com/Azure/azure-quickstart-templates/tree/master/umbraco-webapp-simple).

## <a name="find-information-about-msdeploy-errors"></a>Informacje o błędach MSDeploy

Jeśli szablon usługi Resource Manager używa MSDeploy, komunikaty o błędach wdrożenia może być trudne do zrozumienia. Aby uzyskać więcej informacji, po wdrożeniu nie powiodło się, spróbuj wykonać następujące kroki:

1. Przejdź do witryny [konsoli Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console).
2. Przejdź do folderu na D:\home\LogFiles\SiteExtensions\MSDeploy.
3. Wyszukaj pliki appManagerStatus.xml i appManagerLog.xml. Pierwszy plik rejestruje stan. Drugi plik rejestruje informacje o tym błędzie. Jeśli ten błąd nie jest jasne, do Ciebie, może zawierać podczas jest pytania, aby uzyskać pomoc na forum.

## <a name="choose-a-unique-web-app-name"></a>Wybierz nazwę aplikacji sieci web unikatowy

Nazwa aplikacji sieci web musi być globalnie unikatowa. Można użyć konwencji nazewnictwa może być unikatowy, lub można użyć [funkcja uniqueString](../azure-resource-manager/resource-group-template-functions-string.md#uniquestring) pomagające generowania unikatową nazwę.

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="next-steps"></a>Kolejne kroki

* Samouczek dotyczący wdrażania aplikacji sieci web za pomocą szablonu, zobacz [udostępniania i wdrażanie mikrousług przewidywalnego na platformie Azure](app-service-deploy-complex-application-predictably.md).