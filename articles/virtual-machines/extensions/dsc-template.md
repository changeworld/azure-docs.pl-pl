---
title: Rozszerzenie konfiguracji żądanego stanu z szablonami usługi Azure Resource Manager
description: Dowiedz się więcej o definicji szablonu Menedżera zasobów dla rozszerzenia konfiguracji żądanego stanu (DSC) na platformie Azure.
services: virtual-machines-windows
author: bobbytreed
manager: carmonm
tags: azure-resource-manager
keywords: Dsc
ms.assetid: b5402e5a-1768-4075-8c19-b7f7402687af
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 10/05/2018
ms.author: robreed
ms.openlocfilehash: 89bae60c30cfe82d38c61f385dbaef574b4152d8
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547796"
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>Rozszerzenie konfiguracji żądanego stanu z szablonami usługi Azure Resource Manager

W tym artykule opisano szablon usługi Azure Resource Manager dla [programu obsługi rozszerzeń konfiguracji żądanego stanu (DSC).](dsc-overview.md) Wiele przykładów używa **RegistrationURL** (pod warunkiem, jako ciąg) i **RegistrationKey** (pod warunkiem, jako [PSCredential)](/dotnet/api/system.management.automation.pscredential)do wbudowanego z usługi Azure Automation. Aby uzyskać szczegółowe informacje na temat uzyskiwania tych wartości, zobacz [Maszyny dołączania do zarządzania przez konfigurację stanu automatyzacji platformy Azure — bezpieczna rejestracja](/azure/automation/automation-dsc-onboarding#onboarding-securely-using-registration).

> [!NOTE]
> Może wystąpić nieco inne przykłady schematu. Zmiana schematu nastąpiła w wydaniu z października 2016 r. Aby uzyskać szczegółowe informacje, zobacz [Aktualizowanie z poprzedniego formatu](#update-from-a-previous-format).

## <a name="template-example-for-a-windows-vm"></a>Przykład szablonu dla maszyny Wirtualnej systemu Windows

Poniższy fragment kodu jest w sekcji **Zasób** szablonu.
Rozszerzenie DSC dziedziczy domyślne właściwości rozszerzenia.
Aby uzyskać więcej informacji, zobacz [VirtualMachineExtension klasy](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension?view=azure-dotnet).

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

## <a name="template-example-for-windows-virtual-machine-scale-sets"></a>Przykład szablonu dla zestawów skalowania maszyny wirtualnej systemu Windows

Węzeł zestawu skalowania maszyny wirtualnej ma sekcję **właściwości,** która ma atrybut **VirtualMachineProfile, extensionProfile.**
W obszarze **rozszerzenia**dodaj szczegóły rozszerzenia DSC.

Rozszerzenie DSC dziedziczy domyślne właściwości rozszerzenia.
Aby uzyskać więcej informacji, zobacz [VirtualMachineScaleSetExtension klasy](/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension?view=azure-dotnet).

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

## <a name="detailed-settings-information"></a>Szczegółowe informacje o ustawieniach

Użyj następującego schematu w sekcji **ustawień** rozszerzenia usługi Azure DSC w szablonie Menedżera zasobów.

Aby uzyskać listę argumentów dostępnych dla domyślnego skryptu konfiguracji, zobacz [Domyślny skrypt konfiguracji](#default-configuration-script).

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
| settings.wmfVersion (wmfVersion) |ciąg |Określa wersję programu Windows Management Framework (WMF), która powinna być zainstalowana na maszynie wirtualnej. Ustawienie tej właściwości na **najnowszą** instaluje najnowszą wersję WMF. Obecnie jedynymi możliwymi wartościami dla tej właściwości są **4.0,** **5.0**, **5.1**i **najnowsze**. Te możliwe wartości podlegają aktualizacjom. Wartość domyślna jest **najnowsza**. |
| settings.configuration.url |ciąg |Określa lokalizację adresu URL, z której ma być pobierany plik zip konfiguracji DSC. Jeśli podany adres URL wymaga tokenu sygnatury dostępu współdzielonego dla dostępu, ustaw właściwość **protectedSettings.configurationUrlSasToken** na wartość tokenu sygnatury dostępu Współdzielonego. Ta właściwość jest wymagana, jeśli są zdefiniowane **ustawienia.configuration.script** lub **settings.configuration.function.** Jeśli dla tych właściwości nie zostanie podana żadna wartość, rozszerzenie wywołuje domyślny skrypt konfiguracji, aby ustawić metadane programu Lokalizacja Configuration Manager (LCM) i należy podać argumenty. |
| settings.configuration.script |ciąg |Określa nazwę pliku skryptu zawierającego definicję konfiguracji DSC. Ten skrypt musi znajdować się w folderze głównym pliku zip pobranego z adresu URL określonego przez właściwość **settings.configuration.url.** Ta właściwość jest wymagana, jeśli **zdefiniowano ustawienia.configuration.url** lub **settings.configuration.script.** Jeśli dla tych właściwości nie zostanie podana żadna wartość, rozszerzenie wywołuje domyślny skrypt konfiguracji, aby ustawić metadane LCM i należy podać argumenty. |
| settings.configuration.function |ciąg |Określa nazwę konfiguracji DSC. Konfiguracja o nazwie musi być uwzględniona w skrypcie zdefiniowanym przez **settings.configuration.script.** Ta właściwość jest wymagana, jeśli są zdefiniowane **ustawienia.configuration.url** lub **settings.configuration.function.** Jeśli dla tych właściwości nie zostanie podana żadna wartość, rozszerzenie wywołuje domyślny skrypt konfiguracji, aby ustawić metadane LCM i należy podać argumenty. |
| settings.configurationArguments |Collection |Definiuje wszystkie parametry, które mają zostać przedysze do konfiguracji DSC. Ta właściwość nie jest szyfrowana. |
| settings.configurationData.url |ciąg |Określa adres URL, z którego ma być pobierany plik danych konfiguracyjnych (psd1), który ma być używany jako dane wejściowe dla konfiguracji DSC. Jeśli podany adres URL wymaga tokenu sygnatury dostępu sygnatury dostępu, ustaw właściwość **protectedSettings.configurationDataUrlSasToken** na wartość tokenu sygnatury dostępu współdzielonego. |
| settings.privacy.dataKolejka |ciąg |Włącza lub wyłącza zbieranie danych telemetrycznych. Jedynymi możliwymi wartościami dla tej właściwości są **Enable**, **Disable**, **''** lub **$null**. Pozostawienie tej właściwości puste lub null umożliwia telemetrii. Wartością domyślną jest **''**. Aby uzyskać więcej informacji, zobacz [Zbieranie danych rozszerzenia usługi Azure DSC](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/). |
| settings.advancedOptions.downloadMapy |Collection |Definiuje alternatywne lokalizacje, z których mają być pobierane WMF. Aby uzyskać więcej informacji, zobacz [rozszerzenie usługi Azure DSC 2.8 i jak mapować pobieranie zależności rozszerzenia do własnej lokalizacji.](https://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx) |
| protectedSettings.configurationArgument |Collection |Definiuje wszystkie parametry, które mają zostać przedysze do konfiguracji DSC. Ta właściwość jest szyfrowana. |
| protectedSettings.configurationUrlSasToken |ciąg |Określa token sygnatury dostępu współdzielonego, który ma być używany do uzyskiwania dostępu do adresu URL zdefiniowanego przez **settings.configuration.url.** Ta właściwość jest szyfrowana. |
| protectedSettings.configurationDataUrlSasToken |ciąg |Określa token sygnatury dostępu współdzielonego, który ma być używany do uzyskiwania dostępu do adresu URL, który definiuje **plik settings.configurationData.url.** Ta właściwość jest szyfrowana. |

## <a name="default-configuration-script"></a>Domyślny skrypt konfiguracji

Aby uzyskać więcej informacji na temat następujących wartości, zobacz [Ustawienia podstawowe lokalnego programu Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig#basic-settings).
Domyślnego skryptu konfiguracji rozszerzenia DSC można użyć do skonfigurowania tylko właściwości LCM wymienionych w poniższej tabeli.

| Nazwa właściwości | Typ | Opis |
| --- | --- | --- |
| protectedSettings.configurationArguments.RegistrationKey |PsCredential (PsCredential) |Wymagana właściwość. Określa klucz, który jest używany dla węzła do rejestrowania w usłudze Azure Automation jako hasło obiektu poświadczeń programu PowerShell. Ta wartość może być automatycznie wykryte przy użyciu **metody klawiszy listkl dla** konta automatyzacji.  Zobacz [przykład](#example-using-referenced-azure-automation-registration-values). |
| settings.configurationArguments.RegistrationUrl |ciąg |Wymagana właściwość. Określa adres URL punktu końcowego automatyzacji, w którym węzeł próbuje się zarejestrować. Ta wartość może być automatycznie wykryte przy użyciu metody **odwołania** względem konta automatyzacji. |
| settings.configurationArguments.NodeConfigurationName |ciąg |Wymagana właściwość. Określa konfigurację węzła na koncie automatyzacji do przypisania do węzła. |
| settings.configurationArguments.ConfigurationMode |ciąg |Określa tryb dla LCM. Prawidłowe opcje to **ApplyOnly**, **ApplyandMonitor**i **ApplyandAutoCorrect**.  Wartością domyślną jest **ApplyandMonitor**. |
| settings.configurationArguments.RefreshFrequencyMins | uint32 | Określa, jak często LCM próbuje sprawdzić za pomocą konta automatyzacji dla aktualizacji.  Wartość domyślna to **30**.  Minimalna wartość to **15**. |
| settings.configurationArguments.ConfigurationModeFrequencyMins | uint32 | Określa, jak często LCM sprawdza poprawność bieżącej konfiguracji. Wartość domyślna to **15**. Minimalna wartość to **15**. |
| settings.configurationArguments.RebootNodeIfNeeded | wartość logiczna | Określa, czy węzeł może zostać automatycznie uruchomiony ponownie, jeśli wymaga tego operacja DSC. Wartość domyślna to **false**. |
| settings.configurationArguments.ActionAfterReboot | ciąg | Określa, co dzieje się po ponownym uruchomieniu komputera podczas stosowania konfiguracji. Prawidłowe opcje to **ContinueConfiguration** i **StopConfiguration**. Wartością domyślną jest **ContinueConfiguration**. |
| settings.configurationArguments.AllowModuleOverwrite | wartość logiczna | Określa, czy LCM zastępuje istniejące moduły w węźle. Wartość domyślna to **false**. |

## <a name="settings-vs-protectedsettings"></a>ustawienia a protectedSettings

Wszystkie ustawienia są zapisywane w pliku tekstowym ustawień na maszynie wirtualnej.
Właściwości wymienione w **ustawieniach** są właściwościami publicznymi.
Właściwości publiczne nie są szyfrowane w pliku tekstowym ustawień.
Właściwości wymienione w obszarze **protectedSettings** są szyfrowane za pomocą certyfikatu i nie są wyświetlane w postaci zwykłego tekstu w pliku ustawień na maszynie Wirtualnej.

Jeśli konfiguracja wymaga poświadczeń, można dołączyć poświadczenia **w protectedSettings:**

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

## <a name="example-configuration-script"></a>Przykładowy skrypt konfiguracyjny

W poniższym przykładzie przedstawiono domyślne zachowanie dla rozszerzenia DSC, które ma na celu zapewnienie ustawień metadanych do LCM i zarejestrować się w usłudze Automation DSC.
Wymagane są argumenty konfiguracji.
Argumenty konfiguracji są przekazywane do domyślnego skryptu konfiguracji, aby ustawić metadane LCM.

```json
"settings": {
    "configurationArguments": {
        "RegistrationUrl" : "[parameters('registrationUrl1')]",
        "NodeConfigurationName" : "nodeConfigurationNameValue1"
    }
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

## <a name="example-using-the-configuration-script-in-azure-storage"></a>Przykład przy użyciu skryptu konfiguracji w usłudze Azure Storage

Poniższy przykład pochodzi z [omówienia programu obsługi rozszerzeń DSC](dsc-overview.md).
W tym przykładzie użyto szablonów Menedżera zasobów zamiast poleceń cmdlet do wdrożenia rozszerzenia.
Zapisz konfigurację IisInstall.ps1, umieść ją w pliku `iisinstall.zip`zip (przykład: ), a następnie przekaż plik w dostępnym adresie URL.
W tym przykładzie użyto usługi Azure Blob storage, ale można pobrać pliki zip z dowolnej lokalizacji.

W szablonie Menedżera zasobów następujący kod nakazuje maszynie Wirtualnej pobranie właściwego pliku, a następnie uruchomienie odpowiedniej funkcji programu PowerShell:

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

## <a name="example-using-referenced-azure-automation-registration-values"></a>Przykład przy użyciu wartości rejestracji usługi Azure Automation, do których istnieją odwołania

W poniższym przykładzie pobiera **RegistrationUrl** i **RegistrationKey,** odwołując się do właściwości konta usługi Azure Automation i przy użyciu **metody klawiszy listkeys** do pobierania klucza podstawowego (0).  W tym przykładzie parametry **automationAccountName** i **NodeConfigName** zostały dostarczone do szablonu.

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

Wszystkie ustawienia w poprzednim formacie rozszerzenia (które mają właściwości publiczne **ModulesUrl**, **ModuleSource**, **ModuleVersion**, **ConfigurationFunction**, **SasToken**lub **Properties**) automatycznie dostosowują się do bieżącego formatu rozszerzenia.
Biegają tak samo jak wcześniej.

Poniższy schemat pokazuje, jak wyglądał poprzedni schemat ustawień:

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

Oto jak poprzedni format dostosowuje się do bieżącego formatu:

| Bieżąca nazwa właściwości | Poprzedni odpowiednik schematu |
| --- | --- |
| settings.wmfVersion (wmfVersion) |Ustawienia. WMFVersion ( WMFVersion ) |
| settings.configuration.url |Ustawienia. ModułyUrl |
| settings.configuration.script |Pierwsza część ustawień. Funkcja konfiguracji (przed \\ \\) |
| settings.configuration.function |Druga część ustawień. KonfiguracjaFunkcja \\ \\(po ) |
| settings.configuration.module.name | Ustawienia. ModuleSource (Źródło modułu) |
| settings.configuration.module.version | Ustawienia. Wersja modułu |
| settings.configurationArguments |Ustawienia. Właściwości |
| settings.configurationData.url |protectedSettings.DataBlobUri (bez tokenu Sygnatury dostępu Współdzielonego) |
| settings.privacy.dataKolejka |Ustawienia. Prywatność.dataKończenie |
| settings.advancedOptions.downloadMapy |Ustawienia. Zaawansowaneopcje.DownloadMappings |
| protectedSettings.configurationArgument |protectedSettings.Właściwości |
| protectedSettings.configurationUrlSasToken |Ustawienia. Okręg wyborczy SasToken |
| protectedSettings.configurationDataUrlSasToken |Token SAS z protectedSettings.DataBlobUri |

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Oto niektóre z błędów, które możesz napotkać i jak można je naprawić.

### <a name="invalid-values"></a>Nieprawidłowe wartości

"Privacy.dataCollection jest{0}' '.
Jedynymi możliwymi wartościami są '', 'Enable', 'Disable'".
"WmfVersion jest{0}' '.
Tylko możliwe wartości to ... i "ostatni".

**Problem:** Podana wartość jest niedozwolona.

**Rozwiązanie:** Zmień nieprawidłową wartość na prawidłową wartość.
Aby uzyskać więcej informacji, zobacz tabelę w [obszarze Szczegóły](#details).

### <a name="invalid-url"></a>Nieprawidłowy adres URL

"ConfigurationData.url jest{0}' '. Nie jest to prawidłowy adres URL"{0}"DataBlobUri is ' '. Nie jest to prawidłowy adres URL"{0}"Configuration.url is ' '. To nie jest prawidłowy adres URL"

**Problem:** Podany adres URL jest nieprawidłowy.

**Rozwiązanie:** Sprawdź wszystkie podane adresy URL.
Upewnij się, że wszystkie adresy URL są rozpoznawane w prawidłowych lokalizacjach, do których rozszerzenie może uzyskać dostęp na komputerze zdalnym.

### <a name="invalid-registrationkey-type"></a>Typ klucza nieprawidłowej rejestracji

"Nieprawidłowy typ parametru RegistrationKey typu PSCredential."

**Problem:** Wartość *RegistrationKey* w protectedSettings.configurationArguments nie może być podana jako dowolny typ inny niż PSCredential.

**Rozwiązanie:** Zmień protectedSettings.configurationArguments wpis dla RegistrationKey na typ PSCredential przy użyciu następującego formatu:

```json
"configurationArguments": {
    "RegistrationKey": {
        "userName": "NOT_USED",
        "Password": "RegistrationKey"
    }
}
```

### <a name="invalid-configurationargument-type"></a>Typ nieprawidłowego tytułu konfiguracji

"Nieprawidłowa konfiguracjaArgumenty typu {0}"

**Problem:** *Właściwość ConfigurationArguments* nie może rozwiązać problemu z obiektem **tabeli mieszania.**

**Rozwiązanie:** Stwórz właściwość *ConfigurationArguments* jako **tabelę skrótów**.
Postępuj zgodnie z formatem podanym w poprzednich przykładach. Uważaj na cytaty, przecinki i szelki.

### <a name="duplicate-configurationarguments"></a>Zduplikowane elementy konfiguracyjne

"Znaleziono zduplikowane argumenty '{0}zarówno w publicznej, jak i chronionej konfiguracjiArguments"

**Problem:** *ConfigurationArguments* w ustawieniach publicznych i *ConfigurationArguments* w ustawieniach chronionych mają właściwości o tej samej nazwie.

**Rozwiązanie:** Usuń jedną ze zduplikowanych właściwości.

### <a name="missing-properties"></a>Brakujące właściwości

"ustawienia. Configuration.function wymaga, aby ustawienia.configuration.url lub settings.configuration.module jest określony"

"ustawienia. Configuration.url wymaga, aby settings.configuration.script został określony"

"ustawienia. Configuration.script wymaga, aby settings.configuration.url jest określony"

"ustawienia. Configuration.url wymaga, aby settings.configuration.function został określony"

"protectedSettings.ConfigurationUrlSasToken wymaga, aby określono ustawienia.configuration.url"

"protectedSettings.ConfigurationDataUrlSasToken wymaga, aby określono ustawienia.configurationData.url jest określony"

**Problem:** Zdefiniowana właściwość potrzebuje innej właściwości, której brakuje.

**Rozwiązania:**

- Podaj brakującą właściwość.
- Usuń właściwość, która wymaga brakującej właściwości.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [używaniu zestawów skalowania maszyn wirtualnych z rozszerzeniem Usługi Azure DSC.](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md)
- Dowiedz się więcej o [bezpiecznym zarządzaniu poświadczeniami dsc.](dsc-credentials.md)
- Pobierz [wprowadzenie do programu obsługi rozszerzeń usługi Azure DSC.](dsc-overview.md)
- Aby uzyskać więcej informacji na temat programu PowerShell DSC, przejdź do centrum dokumentacji programu [PowerShell](/powershell/scripting/dsc/overview/overview).
