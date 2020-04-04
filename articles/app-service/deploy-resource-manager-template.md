---
title: Wdrażanie aplikacji z szablonami
description: Znajdź wskazówki dotyczące tworzenia szablonów usługi Azure Resource Manager w celu aprowizowania i wdrażania aplikacji usługi App Service.
author: tfitzmac
ms.topic: article
ms.date: 01/03/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: 0a282a412823207e5f662441158000e8c6121796
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637927"
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>Wskazówki dotyczące wdrażania aplikacji sieci Web przy użyciu szablonów usługi Azure Resource Manager

Ten artykuł zawiera zalecenia dotyczące tworzenia szablonów usługi Azure Resource Manager w celu wdrożenia rozwiązań usługi Azure App Service. Zalecenia te mogą pomóc uniknąć typowych problemów.

## <a name="define-dependencies"></a>Definiowanie zależności

Definiowanie zależności dla aplikacji sieci web wymaga zrozumienia sposobu interakcji zasobów w aplikacji sieci web. Jeśli określisz zależności w niepoprawnej kolejności, może spowodować błędy wdrażania lub utworzyć warunek wyścigu, który zatrzymuje wdrożenia.

> [!WARNING]
> Jeśli do szablonu zostanie uwzględnione rozszerzenie witryny MSDeploy, należy ustawić wszystkie zasoby konfiguracji jako zależne od zasobu MSDeploy. Zmiany konfiguracji powodują ponowne uruchomienie witryny asynchronicznie. Uzależniając zasoby konfiguracyjne od usługi MSDeploy, upewnij się, że msdeploy kończy się przed ponownym uruchomieniem lokacji. Bez tych zależności lokacja może zostać ponownie uruchomiona podczas procesu wdrażania usługi MSDeploy. Przykładowy szablon można znaleźć w szablonie [WordPress z zależnością wdrażania w sieci Web](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json).

Na poniższej ilustracji przedstawiono kolejność zależności dla różnych zasobów usługi App Service:

![Zależności aplikacji sieci Web](media/web-sites-rm-template-guidance/web-dependencies.png)

Zasoby można wdrożyć w następującej kolejności:

**Warstwa 1**
* Plan usługi aplikacji.
* Wszelkie inne powiązane zasoby, takie jak bazy danych lub konta magazynu.

**Warstwa 2**
* Aplikacja sieci Web — zależy od planu usługi aplikacji.
* Wystąpienie usługi Azure Application Insights, które jest przeznaczone dla farmy serwerów — zależy od planu usługi app service.

**Poziom 3**
* Kontrola źródła — zależy od aplikacji sieci web.
* ROZSZERZENIE WITRYNY MSDeploy — zależy od aplikacji sieci web.
* Wystąpienie usługi Azure Application Insights, które jest przeznaczone dla aplikacji sieci web — zależy od aplikacji sieci web.

**Poziom 4**
* Certyfikat usługi App Service — zależy od kontroli źródła lub MSDeploy, jeśli którykolwiek z nich jest obecny. W przeciwnym razie zależy od aplikacji sieci web.
* Ustawienia konfiguracji (parametry połączenia, wartości web.config, ustawienia aplikacji) — zależą od kontroli źródła lub MSDeploy, jeśli którykolwiek z nich jest obecny. W przeciwnym razie zależy od aplikacji sieci web.

**Poziom 5**
* Powiązania nazwy hosta — zależy od certyfikatu, jeśli jest obecny. W przeciwnym razie zależy od zasobu wyższego poziomu.
* Rozszerzenia lokacji — zależy od ustawień konfiguracji, jeśli są obecne. W przeciwnym razie zależy od zasobu wyższego poziomu.

Zazwyczaj rozwiązanie zawiera tylko niektóre z tych zasobów i warstw. W przypadku brakujących poziomów należy mapować niższe zasoby do następnego wyższego poziomu.

W poniższym przykładzie przedstawiono część szablonu. Wartość konfiguracji ciągu połączenia zależy od rozszerzenia MSDeploy. Rozszerzenie MSDeploy zależy od aplikacji sieci web i bazy danych. 

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

Aby uzyskać gotowy do uruchomienia przykład, który używa kodu powyżej, zobacz [Szablon: Tworzenie prostej aplikacji sieci Web Umbraco](https://github.com/Azure/azure-quickstart-templates/tree/master/umbraco-webapp-simple).

## <a name="find-information-about-msdeploy-errors"></a>Znajdowanie informacji o błędach MSDeploy

Jeśli szablon Menedżera zasobów używa msdeploy, komunikaty o błędach wdrożenia mogą być trudne do zrozumienia. Aby uzyskać więcej informacji po nieudanym wdrożeniu, spróbuj wykonać następujące czynności:

1. Przejdź do konsoli [Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console)witryny .
2. Przejdź do folderu w witrynie D:\home\LogFiles\SiteExtensions\MSDeploy.
3. Poszukaj plików appManagerStatus.xml i appManagerLog.xml. Pierwszy plik rejestruje stan. Drugi plik rejestruje informacje o błędzie. Jeśli błąd nie jest dla Ciebie jasny, możesz go dołączyć, gdy prosisz o pomoc na [forum](https://docs.microsoft.com/answers/topics/azure-webapps.html).

## <a name="choose-a-unique-web-app-name"></a>Wybieranie unikatowej nazwy aplikacji sieci Web

Nazwa aplikacji sieci web musi być unikatowa globalnie. Można użyć konwencji nazewnictwa, która może być unikatowa, lub można użyć [uniqueString funkcji,](../azure-resource-manager/templates/template-functions-string.md#uniquestring) aby pomóc w generowaniu unikatową nazwę.

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="deploy-web-app-certificate-from-key-vault"></a>Wdrażanie certyfikatu aplikacji sieci Web z usługi Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Jeśli szablon zawiera zasób [Microsoft.Web/certificates](/azure/templates/microsoft.web/certificates) dla powiązania TLS/SSL, a certyfikat jest przechowywany w magazynie kluczy, należy upewnić się, że tożsamość usługi App Service może uzyskać dostęp do certyfikatu.

Na globalnej platformie Azure podmiot usługi App Service ma identyfikator **abfa0a7c-a6b6-4736-8310-585508787cd**. Aby udzielić dostępu do usługi Key Vault dla jednostki usługi App Service, należy użyć:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
  -VaultName KEY_VAULT_NAME `
  -ServicePrincipalName abfa0a7c-a6b6-4736-8310-5855508787cd `
  -PermissionsToSecrets get `
  -PermissionsToCertificates get
```

W usłudze Azure Government podmiotu zabezpieczeń usługi App Service ma identyfikator **6a02c803-dafd-4136-b4c3-5a6f318b4714**. Użyj tego identyfikatora w poprzednim przykładzie.

W magazynie kluczy wybierz **pozycję Certyfikaty** i **Generuj/Importuj,** aby przekazać certyfikat.

![Importowanie certyfikatu](media/web-sites-rm-template-guidance/import-certificate.png)

W szablonie podaj nazwę certyfikatu dla pliku `keyVaultSecretName`.

Przykładowy szablon umożliwia wyświetlanie [programu Do wdrażania certyfikatu aplikacji sieci Web z klucza tajnego usługi Key Vault i do tworzenia powiązania SSL.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z samouczkiem na temat wdrażania aplikacji sieci web z szablonem, zobacz [Udostępnianie i wdrażanie mikrousług w sposób przewidywalny na platformie Azure.](deploy-complex-application-predictably.md)
* Aby dowiedzieć się więcej o składni JSON i właściwościach typów zasobów w szablonach, zobacz [Odwołanie do szablonu usługi Azure Resource Manager](/azure/templates/).
