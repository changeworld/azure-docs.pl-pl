---
title: Wskazówki dotyczące wdrażania aplikacji za pomocą szablonów — w usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Zalecenia dotyczące tworzenia szablonów usługi Azure Resource Manager do wdrażania aplikacji sieci web.
services: app-service
documentationcenter: app-service
author: tfitzmac
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: d8fa8b216ca6044adefe1398b58f5d14630540e0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66137158"
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>Wskazówki dotyczące wdrażania aplikacji sieci web przy użyciu szablonów usługi Azure Resource Manager

Ten artykuł zawiera zalecenia dotyczące tworzenia szablonów usługi Azure Resource Manager, aby wdrożyć rozwiązania w usłudze Azure App Service. Te zalecenia mogą pomóc w uniknięciu typowych problemów.

## <a name="define-dependencies"></a>Definiowanie zależności

Definiowanie zależności dla aplikacji sieci web wymaga zrozumienia sposobu interakcji dla zasobów w ramach aplikacji sieci web. Jeśli określisz zależności nieprawidłowa kolejność, może spowodować błędy związane z wdrażaniem, lub Utwórz wyścigu, która wpłynie wdrożenia.

> [!WARNING]
> Jeśli dołączysz rozszerzenie witryny MSDeploy w szablonie wszystkie zasoby konfiguracji należy ustawić jako zależny od zasobu MSDeploy. Zmiany konfiguracji powodują witryny ponownie uruchomić asynchronicznie. Zasoby konfiguracji wprowadzania zależne od MSDeploy, gwarantuje, że MSDeploy zakończy się przed ponownym uruchomieniem lokacji. Bez tych zależności witryna może zostać ponownie uruchomiony w procesie wdrażania elementu MSDeploy. Aby uzyskać przykładowy szablon, zobacz [szablonu WordPress w sieci Web wdrażanie zależności](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json).

Na poniższej ilustracji przedstawiono kolejność zależności dla różnych zasobów usługi App Service:

![Zależności aplikacji sieci Web](media/web-sites-rm-template-guidance/web-dependencies.png)

Możesz wdrożyć zasoby w następującej kolejności:

**Warstwa 1**
* Plan usługi App Service.
* Wszystkie inne powiązane zasoby, takie jak bazy danych lub konta magazynu.

**Tier 2**
* Web app — zależy od planu usługi App Service.
* Wystąpienie usługi Application Insights platformy Azure, który jest przeznaczony dla farmy serwerów — zależy od planu usługi App Service.

**Warstwa 3**
* Kontrola — źródła zależy od aplikacji sieci web.
* Rozszerzenie witryny MSDeploy — zależy od aplikacji sieci web.
* Wystąpienie usługi Application Insights przeznaczonego dla farmy serwerów — zależy od aplikacji sieci web.

**Warstwy 4**
* Certyfikat usługi App Service — zależy od kontroli źródła lub MSDeploy, jeśli jest obecny. W przeciwnym razie to zależy od aplikacji sieci web.
* Ustawienia konfiguracji (parametry połączenia, wartości w pliku web.config, ustawienia aplikacji) — zależy od kontroli źródła lub MSDeploy Jeśli albo jest obecny. W przeciwnym razie to zależy od aplikacji sieci web.

**Tier 5**
* Hosta powiązania nazwy — zależy od certyfikatu, jeśli jest obecny. W przeciwnym razie to zależy od zasobu wyższego poziomu.
* Rozszerzenia — lokacji zależy od ustawień konfiguracji, jeśli jest obecny. W przeciwnym razie to zależy od zasobu wyższego poziomu.

Zazwyczaj Twoje rozwiązanie zawiera tylko niektóre z tych zasobów i warstw. W przypadku warstw brakuje zasobów niższy są mapowane na dalej wyższej warstwy.

Poniższy przykład przedstawia szablon. Wartość konfiguracji ciąg połączenia jest zależna od rozszerzenia MSDeploy. Rozszerzenie MSDeploy zależy od aplikacji sieci web i bazy danych. 

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

Przykładowy gotowe do uruchomienia, który używa powyższy kod, [szablonu: Tworzenie prostej aplikacji sieci Web Umbraco](https://github.com/Azure/azure-quickstart-templates/tree/master/umbraco-webapp-simple).

## <a name="find-information-about-msdeploy-errors"></a>Informacje o błędach MSDeploy

Jeśli szablon usługi Resource Manager korzysta z narzędzia MSDeploy, komunikaty o błędach wdrożenia może być trudne do zrozumienia. Aby uzyskać więcej informacji po wdrożenie nie powiodło się, spróbuj wykonać następujące kroki:

1. Przejdź do witryny [konsoli Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console).
2. Przejdź do folderu na D:\home\LogFiles\SiteExtensions\MSDeploy.
3. Wyszukaj pliki appManagerStatus.xml i appManagerLog.xml. Pierwszy plik rejestruje stan. Drugi plik rejestruje informacje o tym błędzie. Jeśli ten błąd nie jest jasne do Ciebie, można dołączyć podczas masz pytania, aby uzyskać pomoc na forum.

## <a name="choose-a-unique-web-app-name"></a>Wybierz nazwę aplikacji sieci web unikatowy

Nazwa aplikacji sieci web musi być globalnie unikatowa. Można użyć konwencji nazewnictwa może być unikatowy, lub możesz użyć [funkcja uniqueString](../azure-resource-manager/resource-group-template-functions-string.md#uniquestring) na potrzeby generuje unikatową nazwę.

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="deploy-web-app-certificate-from-key-vault"></a>Wdróż certyfikat aplikacji internetowej z usługi Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Jeśli szablon zawiera [Microsoft.Web/certificates](/azure/templates/microsoft.web/certificates) zasobu na potrzeby powiązania SSL i certyfikat są przechowywane w usłudze Key Vault, należy upewnić się, że tożsamość usługi App Service mogą uzyskiwać dostęp do certyfikatu.

Na platformie Azure globalne nazwy głównej usługi App Service ma identyfikator **abfa0a7c-a6b6-4736-8310-5855508787cd**. Aby udzielić dostępu do usługi Key Vault dla jednostki usługi App Service, należy użyć:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
  -VaultName KEY_VAULT_NAME `
  -ServicePrincipalName abfa0a7c-a6b6-4736-8310-5855508787cd `
  -PermissionsToSecrets get `
  -PermissionsToCertificates get
```

Na platformie Azure Government, jednostka usługi App Service ma identyfikator **6a02c803-dafd-4136-b4c3-5a6f318b4714**. W poprzednim przykładzie, należy użyć tego Identyfikatora.

W usłudze Key Vault wybierz **certyfikaty** i **Generuj/Import** można przekazać certyfikatu.

![Importowanie certyfikatu](media/web-sites-rm-template-guidance/import-certificate.png)

W szablonie, podaj nazwę certyfikatu na potrzeby `keyVaultSecretName`.

Aby uzyskać przykładowy szablon, zobacz [Wdróż certyfikat aplikacji internetowej z wpisu tajnego usługi Key Vault i użyć jej do tworzenia powiązania SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault).

## <a name="next-steps"></a>Kolejne kroki

* Samouczek dotyczący wdrażania aplikacji sieci web za pomocą szablonu, zobacz [Aprowizowanie i wdrażanie mikrousług przewidywalny na platformie Azure](deploy-complex-application-predictably.md).
* Aby dowiedzieć się więcej na temat składni JSON i właściwości dla typów zasobów w szablonach, zobacz [odwołanie do szablonu usługi Azure Resource Manager](/azure/templates/).
