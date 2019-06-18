---
title: Desired State Configuration rozszerzenia z szablonami usługi Azure Resource Manager
description: Więcej informacji na temat definicji szablonu usługi Resource Manager dla rozszerzenia Desired State Configuration (DSC) na platformie Azure.
services: virtual-machines-windows
author: bobbytreed
manager: carmonm
tags: azure-resource-manager
keywords: dsc
ms.assetid: b5402e5a-1768-4075-8c19-b7f7402687af
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 10/05/2018
ms.author: robreed
ms.openlocfilehash: 1bcec37e7642ae0cb5bd68de1426c8cc62085d38
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61475528"
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>Desired State Configuration rozszerzenia z szablonami usługi Azure Resource Manager

W tym artykule opisano szablon usługi Azure Resource Manager dla [Desired State Configuration (DSC) rozszerzenia obsługi](dsc-overview.md). Wiele przykładów użycia **RegistrationURL** (podana jako ciąg) i **RegistrationKey** (udostępniane jako [PSCredential](/dotnet/api/system.management.automation.pscredential)) dołączyć za pomocą usługi Azure Automation. Aby uzyskać szczegółowe informacje na temat uzyskiwania tych wartości, zobacz [dołączanie maszyn w celu zarządzania przez Azure Automation stanu konfiguracji — bezpiecznego rejestracji](/azure/automation/automation-dsc-onboarding#secure-registration).

> [!NOTE]
> Przykłady schematu nieco inne, mogą wystąpić. Zmiany w schemacie wystąpił w wersji z października 2016 r. Aby uzyskać więcej informacji, zobacz [aktualizacji z poprzedniego formatu](#update-from-a-previous-format).

## <a name="template-example-for-a-windows-vm"></a>Przykład szablonu dla maszyny Wirtualnej z systemem Windows

Poniższy fragment kodu znajduje się w **zasobów** części szablonu.
Rozszerzenie DSC dziedziczy właściwości rozszerzenia domyślne.
Aby uzyskać więcej informacji, zobacz [klasy VirtualMachineExtension](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension?view=azure-dotnet).

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "Microsoft.Powershell.DSC",
  "apiVersion": "2018-06-30",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Powershell",
    "type": "DSC",
    "typeHandlerVersion": "2.77",
    "autoUpgradeMinorVersion": true,
    "protectedSettings": {
      "Items": {
        "registrationKeyPrivate": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-06-30').Keys[0].value]"
      }
    },
    "settings": {
      "Properties": [
        {
          "Name": "RegistrationKey",
          "Value": {
            "UserName": "PLACEHOLDER_DONOTUSE",
            "Password": "PrivateSettingsRef:registrationKeyPrivate"
          },
          "TypeName": "System.Management.Automation.PSCredential"
        },
        {
          "Name": "RegistrationUrl",
          "Value": "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
          "TypeName": "System.String"
        },
        {
          "Name": "NodeConfigurationName",
          "Value": "[parameters('nodeConfigurationName')]",
          "TypeName": "System.String"
        }
      ]
    }
  }
}
```

## <a name="template-example-for-windows-virtual-machine-scale-sets"></a>Ustawia szablonu przykład skalowania maszyn wirtualnych Windows

Węzeł zestaw skali maszyny wirtualnej ma **właściwości** sekcji, która ma **VirtualMachineProfile, extensionProfile** atrybutu.
W obszarze **rozszerzenia**, dodać szczegóły dotyczące rozszerzenia DSC.

Rozszerzenie DSC dziedziczy właściwości rozszerzenia domyślne.
Aby uzyskać więcej informacji, zobacz [klasy VirtualMachineScaleSetExtension](/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension?view=azure-dotnet).

```json
"extensionProfile": {
    "extensions": [
      {
        "name": "Microsoft.Powershell.DSC",
        "properties": {
          "publisher": "Microsoft.Powershell",
          "type": "DSC",
          "typeHandlerVersion": "2.77",
          "autoUpgradeMinorVersion": true,
          "protectedSettings": {
            "Items": {
              "registrationKeyPrivate": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-06-30').Keys[0].value]"
            }
          },
          "settings": {
            "Properties": [
              {
                "Name": "RegistrationKey",
                "Value": {
                  "UserName": "PLACEHOLDER_DONOTUSE",
                  "Password": "PrivateSettingsRef:registrationKeyPrivate"
                },
                "TypeName": "System.Management.Automation.PSCredential"
              },
              {
                "Name": "RegistrationUrl",
                "Value": "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
                "TypeName": "System.String"
              },
              {
                "Name": "NodeConfigurationName",
                "Value": "[parameters('nodeConfigurationName')]",
                "TypeName": "System.String"
              }
            ]
          }
        }
      }
    ]
  }
```

## <a name="detailed-settings-information"></a>Informacje szczegółowe ustawienia

Użyj następującego schematu w **ustawienia** sekcję rozszerzenia DSC usługi Azure w szablonie usługi Resource Manager.

Aby uzyskać listę argumentów, które są dostępne dla skryptu konfiguracji domyślnej, zobacz [domyślne skryptu konfiguracji](#default-configuration-script).

```json
"settings": {
    "wmfVersion": "latest",
    "configuration": {
        "url": "http://validURLToConfigLocation",
        "script": "ConfigurationScript.ps1",
        "function": "ConfigurationFunction"
    },
    "configurationArguments": {
        "argument1": "Value1",
        "argument2": "Value2"
    },
    "configurationData": {
        "url": "https://foo.psd1"
    },
    "privacy": {
        "dataCollection": "enable"
    },
    "advancedOptions": {
        "downloadMappings": {
            "customWmfLocation": "http://myWMFlocation"
        }
    }
},
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
            "userName": "UsernameValue1",
            "password": "PasswordValue1"
        },
        "parameterOfTypePSCredential2": {
            "userName": "UsernameValue2",
            "password": "PasswordValue2"
        }
    },
    "configurationUrlSasToken": "?g!bber1sht0k3n",
    "configurationDataUrlSasToken": "?dataAcC355T0k3N"
}
```

## <a name="details"></a>Szczegóły

| Nazwa właściwości | Typ | Opis |
| --- | --- | --- |
| settings.wmfVersion |string |Określa wersję programu Windows Management Framework (WMF), ma być zainstalowany na maszynie Wirtualnej. Ustawienie tej właściwości na **najnowsze** instaluje najnowszą wersję programu WMF. Obecnie jedyną możliwą wartości dla tej właściwości to **4.0**, **5.0**, **5.1**, i **najnowsze**. Te wartości możliwe jest zależna od aktualizacji. Wartość domyślna to **najnowsze**. |
| settings.configuration.url |string |Określa lokalizację adresu URL, z której można pobrać plik zip konfiguracji DSC. Jeśli adres URL podany wymaga tokenu sygnatury dostępu Współdzielonego dla dostępu, ustaw **protectedSettings.configurationUrlSasToken** właściwości na wartość tokenu sygnatury dostępu Współdzielonego. Ta właściwość jest wymagana, jeśli **settings.configuration.script** lub **settings.configuration.function** są zdefiniowane. Jeśli wartość nie zostanie podany dla tych właściwości, rozszerzenie wywołuje skrypt konfiguracji domyślnej można ustawić metadanych lokalizacji Configuration Manager (LCM), a powinien być dostarczony argumentów. |
| settings.configuration.script |string |Określa nazwę pliku skryptu, który zawiera definicję konfiguracji DSC. Ten skrypt musi być w folderze głównym pliku zip, który jest pobierany z adresu URL określonego przez **settings.configuration.url** właściwości. Ta właściwość jest wymagana, jeśli **settings.configuration.url** lub **settings.configuration.script** są zdefiniowane. Jeśli wartość nie zostanie podany dla tych właściwości, rozszerzenie wywołuje skrypt konfiguracji domyślnej można ustawić metadanych LCM i powinien być dostarczony argumentów. |
| settings.configuration.function |string |Określa nazwę konfiguracji DSC. Konfiguracja, który nosi nazwę muszą być zawarte w skrypcie, **settings.configuration.script** definiuje. Ta właściwość jest wymagana, jeśli **settings.configuration.url** lub **settings.configuration.function** są zdefiniowane. Jeśli wartość nie zostanie podany dla tych właściwości, rozszerzenie wywołuje skrypt konfiguracji domyślnej można ustawić metadanych LCM i powinien być dostarczony argumentów. |
| settings.configurationArguments |Collection |Określa wszelkie parametry, które mają być przekazane do konfiguracji DSC. Ta właściwość nie jest zaszyfrowany. |
| settings.configurationData.url |string |Określa adres URL do pobrania pliku (psd1) danych konfiguracji do użycia jako dane wejściowe dla danej konfiguracji DSC. Jeśli adres URL podany wymaga tokenu sygnatury dostępu Współdzielonego dla dostępu, ustaw **protectedSettings.configurationDataUrlSasToken** właściwości na wartość tokenu sygnatury dostępu Współdzielonego. |
| settings.privacy.dataCollection |string |Włącza lub wyłącza zbieranie danych telemetrycznych. Tylko wtedy możliwe wartości dla tej właściwości to **Włącz**, **wyłączyć**, **''** , lub **$null**. Pozostawienie tej właściwości, puste ani mieć wartości null umożliwia danych telemetrycznych. Wartość domyślna to **''** . Aby uzyskać więcej informacji, zobacz [zbierania danych rozszerzenia DSC usługi Azure](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/). |
| settings.advancedOptions.downloadMappings |Collection |Definiuje alternatywnych lokalizacji, z którego będą pobierane programu WMF. Aby uzyskać więcej informacji, zobacz [rozszerzenie DSC usługi Azure 2.8 i sposób mapowania plików do pobrania zależności rozszerzenia na własnej lokalizacji](https://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx). |
| protectedSettings.configurationArguments |Collection |Określa wszelkie parametry, które mają być przekazane do konfiguracji DSC. Ta właściwość jest szyfrowana. |
| protectedSettings.configurationUrlSasToken |string |Określa tokenu sygnatury dostępu Współdzielonego na potrzeby dostępu do adresu URL, **settings.configuration.url** definiuje. Ta właściwość jest szyfrowana. |
| protectedSettings.configurationDataUrlSasToken |string |Określa tokenu sygnatury dostępu Współdzielonego na potrzeby dostępu do adresu URL, **settings.configurationData.url** definiuje. Ta właściwość jest szyfrowana. |

## <a name="default-configuration-script"></a>Skrypt konfiguracji domyślnej

Aby uzyskać więcej informacji na temat następujących wartości zobacz [Local Configuration Manager podstawowych ustawień](/powershell/dsc/metaconfig#basic-settings).
Skrypt konfiguracji domyślnej rozszerzenia DSC służy do konfigurowania właściwości LCM, które są wymienione w poniższej tabeli.

| Nazwa właściwości | Typ | Opis |
| --- | --- | --- |
| protectedSettings.configurationArguments.RegistrationKey |PSCredential |Wymagana właściwość. Określa klucz, który jest używany dla węzła do rejestracji w usłudze Azure Automation jako hasło obiekt poświadczeń PowerShell. Ta wartość może automatycznie odnalezione za pomocą **listkeys** metoda względem konta usługi Automation.  Zobacz [przykład](#example-using-referenced-azure-automation-registration-values). |
| settings.configurationArguments.RegistrationUrl |string |Wymagana właściwość. Określa adres URL punktu końcowego usługi Automation, gdzie węzeł, próbuje zarejestrować. Ta wartość może automatycznie odnalezione za pomocą **odwołania** metoda względem konta usługi Automation. |
| settings.configurationArguments.NodeConfigurationName |string |Wymagana właściwość. Określa konfigurację węzła na koncie usługi Automation, które można przypisać do węzła. |
| settings.configurationArguments.ConfigurationMode |string |Określa tryb LCM. Prawidłowe opcje to **ApplyOnly**, **ApplyandMonitor**, i **ApplyandAutoCorrect**.  Wartość domyślna to **ApplyandMonitor**. |
| settings.configurationArguments.RefreshFrequencyMins | uint32 | Określa, jak często LCM próbuje skontaktować się z konta usługi Automation, do aktualizacji.  Wartość domyślna to **30**.  Minimalna wartość to **15**. |
| settings.configurationArguments.ConfigurationModeFrequencyMins | uint32 | Określa, jak często LCM sprawdza bieżącą konfigurację. Wartość domyślna to **15**. Minimalna wartość to **15**. |
| settings.configurationArguments.RebootNodeIfNeeded | boolean | Określa, czy węzeł zostanie automatycznie ponownie uruchomiony, jeśli operacja DSC żąda ona. Wartość domyślna to **false**. |
| settings.configurationArguments.ActionAfterReboot | string | Określa, co się stanie po ponownym uruchomieniu, gdy trwa operacja zastosowania konfiguracji. Prawidłowe opcje to **ContinueConfiguration** i **StopConfiguration**. Wartość domyślna to **ContinueConfiguration**. |
| settings.configurationArguments.AllowModuleOverwrite | boolean | Określa, czy LCM zastępuje istniejące moduły w węźle. Wartość domyślna to **false**. |

## <a name="settings-vs-protectedsettings"></a>ustawienia, a protectedSettings

Wszystkie ustawienia są zapisywane w pliku tekstowym ustawień na maszynie Wirtualnej.
Właściwości wyświetlane w obszarze **ustawienia** są właściwości publiczne.
Właściwości publiczne nie są szyfrowane w pliku tekstowym ustawienia.
Właściwości wyświetlane w obszarze **protectedSettings** są szyfrowane przy użyciu certyfikatu, a nie są wyświetlane w postaci zwykłego tekstu w pliku ustawień na maszynie Wirtualnej.

Jeśli konfiguracja wymaga poświadczeń, może zawierać poświadczenia w **protectedSettings**:

```json
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
               "userName": "UsernameValue1",
               "password": "PasswordValue1"
        }
    }
}
```

## <a name="example-configuration-script"></a>Przykładowy skrypt konfiguracji

Poniższy przykład przedstawia domyślne zachowanie w przypadku rozszerzenia DSC jest zapewnienie ustawień metadanych LCM i zarejestrować w usłudze Automation DSC.
Argumenty konfiguracji są wymagane.
Konfiguracja argumenty są przekazywane do skryptu konfiguracji domyślnej można ustawić LCM metadanych.

```json
"settings": {
    "RegistrationUrl" : "[parameters('registrationUrl1')]",
    "NodeConfigurationName" : "nodeConfigurationNameValue1"
},
"protectedSettings": {
    "configurationArguments": {
        "RegistrationKey": {
            "userName": "NOT_USED",
            "Password": "registrationKey"
        }
    }
}
```

## <a name="example-using-the-configuration-script-in-azure-storage"></a>Przykład za pomocą skryptu konfiguracji w usłudze Azure Storage

Poniższy przykład pochodzi z [Przegląd procedury obsługi rozszerzenia DSC](dsc-overview.md).
W tym przykładzie używa szablonów usługi Resource Manager zamiast polecenia cmdlet, aby wdrożyć rozszerzenie.
Zapisz konfigurację IisInstall.ps1, umieść go w pliku zip (przykład: `iisinstall.zip`), a następnie przekaż plik dostępny adres URL.
W tym przykładzie użyto usługi Azure Blob storage, ale możesz pobrać pliki zip z dowolnego miejsca i dowolnego.

W szablonie usługi Resource Manager poniższy kod powoduje, że maszynę Wirtualną, aby pobrać prawidłowego pliku, a następnie uruchom odpowiednią funkcję programu PowerShell:

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/iisinstall.zip",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## <a name="example-using-referenced-azure-automation-registration-values"></a>Przykład za pomocą odwołanie do wartości uzyskanych przy rejestracji usługi Azure Automation

Poniższy przykład pobiera **RegistrationUrl** i **RegistrationKey** odwołuje się do właściwości konta usługi Azure Automation i używając **listkeys** metody Pobierz klucz podstawowy (0).  W tym przykładzie parametry **automationAccountName** i **NodeConfigName** zostały dostarczone z szablonem.

```json
"settings": {
    "RegistrationUrl" : "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
    "NodeConfigurationName" : "[parameters('NodeConfigName')]"
},
"protectedSettings": {
    "configurationArguments": {
        "RegistrationKey": {
            "userName": "NOT_USED",
            "Password": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-01-15').Keys[0].value]"
        }
    }
}
```

## <a name="update-from-a-previous-format"></a>Aktualizacja z poprzedniego formatu

Wszystkie ustawienia w poprzednim formacie rozszerzenia (, których właściwości publiczne **ModulesUrl**, **ModuleSource**, **ModuleVersion**,  **ConfigurationFunction**, **SasToken**, lub **właściwości**) automatycznie dostosowywać się do bieżącego formatu rozszerzenia.
Działają one tak samo jak przed rozpoczęciem.

Zgodny z następującym schematem pokazuje, jakie poprzednie ustawienia schematu wyglądał jak:

```json
"settings": {
    "WMFVersion": "latest",
    "ModulesUrl": "https://UrlToZipContainingConfigurationScript.ps1.zip",
    "SasToken": "SAS Token if ModulesUrl points to private Azure Blob Storage",
    "ConfigurationFunction": "ConfigurationScript.ps1\\ConfigurationFunction",
    "Properties": {
        "ParameterToConfigurationFunction1": "Value1",
        "ParameterToConfigurationFunction2": "Value2",
        "ParameterOfTypePSCredential1": {
            "UserName": "UsernameValue1",
            "Password": "PrivateSettingsRef:Key1"
        },
        "ParameterOfTypePSCredential2": {
            "UserName": "UsernameValue2",
            "Password": "PrivateSettingsRef:Key2"
        }
    }
},
"protectedSettings": {
    "Items": {
        "Key1": "PasswordValue1",
        "Key2": "PasswordValue2"
    },
    "DataBlobUri": "https://UrlToConfigurationDataWithOptionalSasToken.psd1"
}
```

Poniżej przedstawiono, jak poprzedniego formatu dostosowuje się do bieżącego formatu:

| Bieżąca nazwa właściwości | Równoważne poprzedniego schematu |
| --- | --- |
| settings.wmfVersion |settings.WMFVersion |
| settings.configuration.url |settings.ModulesUrl |
| settings.configuration.script |Pierwsza część ustawień. ConfigurationFunction (przed \\ \\) |
| settings.configuration.function |Druga część ustawień. ConfigurationFunction (po \\ \\) |
| settings.configuration.module.name | settings.ModuleSource |
| settings.configuration.module.version | settings.ModuleVersion |
| settings.configurationArguments |settings.Properties |
| settings.configurationData.url |protectedSettings.DataBlobUri (bez tokenu sygnatury dostępu Współdzielonego) |
| settings.privacy.dataCollection |settings.Privacy.dataCollection |
| settings.advancedOptions.downloadMappings |settings.AdvancedOptions.DownloadMappings |
| protectedSettings.configurationArguments |protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken |settings.SasToken |
| protectedSettings.configurationDataUrlSasToken |Token sygnatury dostępu Współdzielonego z protectedSettings.DataBlobUri |

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Poniżej przedstawiono niektóre błędy, które możesz napotkać i jak można je naprawić.

### <a name="invalid-values"></a>Nieprawidłowe wartości

"Jest Privacy.dataCollection"{0}".
Tylko wtedy możliwe wartości to ","Włącz"i"Disable"".
"Jest WmfVersion"{0}".
Tylko wtedy możliwe wartości to... i "najnowszy" ".

**Problem**: Podana wartość nie jest dozwolone.

**Rozwiązanie**: Zmień nieprawidłową wartość na prawidłową wartość.
Aby uzyskać więcej informacji, zobacz tabelę w [szczegóły](#details).

### <a name="invalid-url"></a>Nieprawidłowy adres URL

"Jest ConfigurationData.url"{0}". Nie jest prawidłowym adresem URL"" jest DataBlobUri "{0}". Nie jest prawidłowym adresem URL"" jest Configuration.url "{0}". Nie jest prawidłowym adresem URL"

**Problem**: Podany adres URL jest nieprawidłowy.

**Rozwiązanie**: Sprawdź wszystkie podane adresami URL.
Należy się upewnić, że wszystkie adresy URL rozpoznać prawidłowych lokalizacji czy rozszerzenia dostęp do komputera zdalnego.

### <a name="invalid-registrationkey-type"></a>Nieprawidłowy typ RegistrationKey

"Nieprawidłowy typ parametru RegistrationKey typu PSCredential."

**Problem**: *RegistrationKey* w protectedSettings.configurationArguments nie można podać wartość dowolnego typu innego niż obiektu PSCredential.

**Rozwiązanie**: Zmień wpis protectedSettings.configurationArguments dla RegistrationKey typ PSCredential w następującym formacie:

```json
"configurationArguments": {
    "RegistrationKey": {
        "userName": "NOT_USED",
        "Password": "RegistrationKey"
    }
}
```

### <a name="invalid-configurationargument-type"></a>Nieprawidłowy typ ConfigurationArgument

"Nieprawidłowa configurationArguments typu {0}"

**Problem**: *ConfigurationArguments* właściwości nie można rozpoznać **tabeli mieszania** obiektu.

**Rozwiązanie**: Wprowadź swoje *ConfigurationArguments* właściwości **tabeli mieszania**.
Postępuj zgodnie z formatu podane w powyższych przykładach. Poszukaj ofert, przecinki oraz nawiasy klamrowe.

### <a name="duplicate-configurationarguments"></a>Duplicate ConfigurationArguments

"Znaleziono zduplikowane argumenty{0}" w configurationArguments zarówno publiczne i chronione "

**Problem**: *ConfigurationArguments* w ustawieniach publicznych i *ConfigurationArguments* w ustawieniach chronionego ma właściwości o takiej samej nazwie.

**Rozwiązanie**: Usuń jeden z tych właściwości.

### <a name="missing-properties"></a>Brak właściwości

"ustawienia. Configuration.Function wymaga, że określono settings.configuration.url lub settings.configuration.module".

"ustawienia. Configuration.URL wymaga tego settings.configuration.script został określony".

"ustawienia. Configuration.Script wymaga tego settings.configuration.url został określony".

"ustawienia. Configuration.URL wymaga tego settings.configuration.function został określony".

"protectedSettings.ConfigurationUrlSasToken wymaga tego settings.configuration.url został określony."

"protectedSettings.ConfigurationDataUrlSasToken wymaga tego settings.configurationData.url został określony."

**Problem**: Zdefiniowana właściwość musi inną właściwość, która nie istnieje.

**Rozwiązania**:

- Podaj brakuje właściwości.
- Usuń właściwość, która wymaga brakuje właściwości.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [zestawów za pomocą skalowania maszyn wirtualnych za pomocą rozszerzenia DSC usługi Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md).
- Poznaj więcej szczegółów dotyczących [zarządzania poświadczeniami bezpiecznego firmy DSC](dsc-credentials.md).
- Pobierz [wprowadzenie do procedury obsługi rozszerzenia DSC usługi Azure](dsc-overview.md).
- Aby uzyskać więcej informacji na temat programu PowerShell DSC, przejdź do [Centrum dokumentacji programu PowerShell](/powershell/dsc/overview).
