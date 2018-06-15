---
title: Żądany stan konfiguracji rozszerzenia przy użyciu szablonów usługi Azure Resource Manager
description: Więcej informacji na temat definicji szablonu usługi Resource Manager dla rozszerzenia konfiguracji żądanego stanu (DSC) na platformie Azure.
services: virtual-machines-windows
documentationcenter: ''
author: DCtheGeek
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: DSC
ms.assetid: b5402e5a-1768-4075-8c19-b7f7402687af
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: dacoulte
ms.openlocfilehash: d19d33d7d8d19923e814b8685c3e56553f85850d
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012482"
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>Żądany stan konfiguracji rozszerzenia przy użyciu szablonów usługi Azure Resource Manager

W tym artykule opisano szablonu usługi Azure Resource Manager dla [konfiguracji żądanego stanu (DSC) rozszerzenia obsługi](dsc-overview.md).

> [!NOTE]
> Może wystąpić schematu nieco inne przykłady. Zmiany w schemacie wystąpił w wersji z października 2016. Aby uzyskać więcej informacji, zobacz [aktualizacji z poprzedniego formatu](#update-from-the-previous-format).

## <a name="template-example-for-a-windows-vm"></a>Przykład szablonu maszyny wirtualnej systemu Windows

Poniższy fragment kodu zawiera **zasobów** sekcji szablonu.
Rozszerzenia DSC dziedziczy właściwości domyślne rozszerzenie.
Aby uzyskać więcej informacji, zobacz [VirtualMachineExtension klasy](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension?view=azure-dotnet.).

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat(parameters('VMName'),'/Microsoft.Powershell.DSC')]",
    "apiVersion": "2017-12-01",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Powershell",
        "type": "DSC",
        "typeHandlerVersion": "2.76",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "protectedSettings": {
                "Items": {
                    "registrationKeyPrivate": "registrationKey"
                }
            },
            "publicSettings": {
                "configurationArguments": [{
                        "Name": "RegistrationKey",
                        "Value": {
                            "UserName": "PLACEHOLDER_DONOTUSE",
                            "Password": "PrivateSettingsRef:registrationKeyPrivate"
                        }
                    },
                    {
                        "RegistrationUrl": "registrationUrl",
                    },
                    {
                        "NodeConfigurationName": "nodeConfigurationName"
                    }
                ]
            }
        }
    }
}
```

## <a name="template-example-for-windows-virtual-machine-scale-sets"></a>Ustawia szablonu przykład skalowania maszyny wirtualnej systemu Windows

Węzeł zestaw skali maszyny wirtualnej ma **właściwości** sekcja, która ma **VirtualMachineProfile, extensionProfile** atrybutu.
W obszarze **rozszerzenia**, dodać szczegóły dla rozszerzenia usługi Konfiguracja DSC.

Rozszerzenia DSC dziedziczy właściwości domyślne rozszerzenie.
Aby uzyskać więcej informacji, zobacz [VirtualMachineScaleSetExtension klasy](/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension?view=azure-dotnet).

```json
"extensionProfile": {
    "extensions": [{
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(parameters('VMName'),'/Microsoft.Powershell.DSC')]",
        "apiVersion": "2017-12-01",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
        ],
        "properties": {
            "publisher": "Microsoft.Powershell",
            "type": "DSC",
            "typeHandlerVersion": "2.76",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "protectedSettings": {
                    "Items": {
                        "registrationKeyPrivate": "registrationKey"
                    }
                },
                "publicSettings": {
                    "configurationArguments": [{
                            "Name": "RegistrationKey",
                            "Value": {
                                "UserName": "PLACEHOLDER_DONOTUSE",
                                "Password": "PrivateSettingsRef:registrationKeyPrivate"
                            },
                        },
                        {
                            "RegistrationUrl": "registrationUrl",
                        },
                        {
                            "NodeConfigurationName": "nodeConfigurationName"
                        }
                    ]
                }
            },
        }
    }]
}
```

## <a name="detailed-settings-information"></a>Informacje szczegółowe ustawienia

Użyj następującego schematu w **ustawienia** sekcję rozszerzenia usługi Konfiguracja DSC Azure w szablonie usługi Resource Manager.

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
| settings.wmfVersion |ciąg |Określa wersję programu Windows Management Framework (WMF), który powinien być zainstalowany na maszynie Wirtualnej. Ustawienie tej właściwości na **najnowsze** instaluje najnowszą wersję programu WMF. Obecnie są to tylko możliwe wartości dla tej właściwości **4.0**, **5.0**, **5.0PP**, i **najnowsze**. Te wartości można podlegają aktualizacji. Wartość domyślna to **najnowsze**. |
| settings.configuration.url |ciąg |Określa lokalizację adres URL, z której można pobrać pliku zip konfiguracji DSC. Jeśli podany adres URL wymaga tokenu sygnatury dostępu Współdzielonego w celu uzyskania dostępu, ustaw **protectedSettings.configurationUrlSasToken** na wartość token sygnatury dostępu Współdzielonego. Ta właściwość jest wymagana, jeśli **settings.configuration.script** lub **settings.configuration.function** są zdefiniowane. Jeśli wartość nie podano tych właściwości, rozszerzenie wywołuje skrypt konfiguracji domyślnej można ustawić metadane lokalizacji Configuration Manager (LCM), a argumenty powinny być dostarczane. |
| settings.configuration.script |ciąg |Określa nazwę pliku skryptu, który zawiera definicję konfiguracji DSC. Ten skrypt musi być w folderze głównym pliku zip, który jest pobierana z określony przez adres URL **configuration.url** właściwości. Ta właściwość jest wymagana, jeśli **settings.configuration.url** lub **settings.configuration.script** są zdefiniowane. Jeśli wartość nie podano tych właściwości, rozszerzenie wywołuje skrypt konfiguracji domyślnej można ustawić LCM metadanych i argumenty powinny być dostarczane. |
| settings.configuration.function |ciąg |Określa nazwę konfiguracji DSC. Konfiguracja o nazwie muszą być zawarte w skrypcie który **configuration.script** definiuje. Ta właściwość jest wymagana, jeśli **settings.configuration.url** lub **settings.configuration.function** są zdefiniowane. Jeśli wartość nie podano tych właściwości, rozszerzenie wywołuje skrypt konfiguracji domyślnej można ustawić LCM metadanych i argumenty powinny być dostarczane. |
| settings.configurationArguments |Collection |Definiuje parametry, które mają być przekazywane do konfiguracji DSC. Ta właściwość nie jest zaszyfrowany. |
| settings.configurationData.url |ciąg |Określa adres URL do pobrania pliku (psd1) danych konfiguracji do użycia jako dane wejściowe dla danej konfiguracji DSC. Jeśli podany adres URL wymaga tokenu sygnatury dostępu Współdzielonego w celu uzyskania dostępu, ustaw **protectedSettings.configurationDataUrlSasToken** na wartość token sygnatury dostępu Współdzielonego. |
| settings.privacy.dataEnabled |ciąg |Włącza lub wyłącza gromadzenia danych telemetrii. Są to tylko możliwe wartości dla tej właściwości **włączyć**, **wyłączyć**, **''**, lub **$null**. Puste ani mieć wartości null, pozostawiając ta właściwość umożliwia telemetrii. Wartość domyślna to **''**. Aby uzyskać więcej informacji, zobacz [zbierania danych rozszerzenia usługi Konfiguracja DSC Azure](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/). |
| settings.advancedOptions.downloadMappings |Collection |Definiuje alternatywnej lokalizacji, z których można pobrać WMF. Aby uzyskać więcej informacji, zobacz [rozszerzenia usługi Konfiguracja DSC Azure 2.8 i sposób odwzorowywania pobieranie zależności rozszerzenia własnej lokalizacji](http://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx). |
| protectedSettings.configurationArguments |Collection |Definiuje parametry, które mają być przekazywane do konfiguracji DSC. Ta właściwość jest zaszyfrowany. |
| protectedSettings.configurationUrlSasToken |ciąg |Określa tokenu sygnatury dostępu Współdzielonego na potrzeby dostępu do adresu URL który **configuration.url** definiuje. Ta właściwość jest zaszyfrowany. |
| protectedSettings.configurationDataUrlSasToken |ciąg |Określa tokenu sygnatury dostępu Współdzielonego na potrzeby dostępu do adresu URL który **configurationData.url** definiuje. Ta właściwość jest zaszyfrowany. |

## <a name="default-configuration-script"></a>Skrypt konfiguracji domyślnej

Aby uzyskać więcej informacji na temat następujące wartości, zobacz [lokalny program Configuration Manager podstawowe ustawienia](/powershell/dsc/metaconfig#basic-settings).
Skrypt konfiguracji DSC rozszerzenia domyślna służy do konfigurowania właściwości LCM, które są wymienione w poniższej tabeli.

| Nazwa właściwości | Typ | Opis |
| --- | --- | --- |
| settings.configurationArguments.RegistrationKey |SecureString |Wymagana właściwość. Określa klucz używany do rejestrowania w usłudze Automatyzacja Azure jako hasło obiekt poświadczeń PowerShell dla węzła. Tę wartość można automatycznie odnaleźć za pomocą **listkeys** metody względem konta automatyzacji. Wartość powinien być zabezpieczony jako ustawienie chronionych. |
| settings.configurationArguments.RegistrationUrl |ciąg |Wymagana właściwość. Określa adres URL punktu końcowego automatyzacji, gdzie węzeł próbuje zarejestrować. Tę wartość można automatycznie odnaleźć za pomocą **odwołania** metody względem konta automatyzacji. |
| settings.configurationArguments.NodeConfigurationName |ciąg |Wymagana właściwość. Określa konfigurację węzła w ramach konta automatyzacji można przypisać do tego węzła. |
| settings.configurationArguments.ConfigurationMode |ciąg |Określa tryb LCM. Prawidłowe opcje to **ApplyOnly**, **ApplyandMonitor**, i **ApplyandAutoCorrect**.  Wartość domyślna to **ApplyandMonitor**. |
| settings.configurationArguments.RefreshFrequencyMins | uint32 | Określa, jak często LCM próbuje skontaktować się z konta automatyzacji aktualizacji.  Wartość domyślna to **30**.  Minimalna wartość to **15**. |
| settings.configurationArguments.ConfigurationModeFrequencyMins | uint32 | Określa, jak często LCM sprawdza bieżącą konfigurację. Wartość domyślna to **15**. Minimalna wartość to **15**. |
| settings.configurationArguments.RebootNodeIfNeeded | wartość logiczna | Określa, czy węzeł może być automatycznie ponownie uruchomiony, jeśli żądanie operacji DSC. Wartość domyślna to **false**. |
| settings.configurationArguments.ActionAfterReboot | ciąg | Określa, co się stanie po ponownym uruchomieniu podczas stosowania konfiguracji. Prawidłowe opcje to **ContinueConfiguration** i **StopConfiguration**. Wartość domyślna to **ContinueConfiguration**. |
| settings.configurationArguments.AllowModuleOverwrite | wartość logiczna | Określa, czy LCM spowoduje zastąpienie istniejących modułów w węźle. Wartość domyślna to **false**. |

## <a name="settings-vs-protectedsettings"></a>Ustawienia programu vs. ProtectedSettings

Wszystkie ustawienia są zapisywane w pliku tekstowym ustawień na maszynie Wirtualnej.
Właściwości wyświetlane w obszarze **ustawienia** nie ma właściwości publicznej.
Właściwości publiczne nie są szyfrowane w pliku tekstowym ustawienia.
Właściwości wyświetlane w obszarze **protectedSettings** są szyfrowane przy użyciu certyfikatu i nie są wyświetlane w postaci zwykłego tekstu w pliku ustawień na maszynie Wirtualnej.

Konfiguracja wymaga poświadczeń, można używać poświadczeń w **protectedSettings**:

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

W poniższym przykładzie przedstawiono domyślne zachowanie dla rozszerzenia usługi Konfiguracja DSC jest zapewnienie ustawienia metadanych LCM i zarejestrować w usłudze Konfiguracja DSC automatyzacji.
Argumenty konfiguracji są wymagane.
Argumenty konfiguracji są przekazywane do skryptu konfiguracji domyślnej można ustawić LCM metadanych.

```json
"settings": {
    "configurationArguments": {
        {
            "Name": "RegistrationKey",
            "Value": {
                "UserName": "PLACEHOLDER_DONOTUSE",
                "Password": "PrivateSettingsRef:registrationKeyPrivate"
            },
        },
        "RegistrationUrl": "[parameters('registrationUrl1')]",
        "NodeConfigurationName": "nodeConfigurationNameValue1"
    }
},
"protectedSettings": {
    "Items": {
        "registrationKeyPrivate": "[parameters('registrationKey1']"
    }
}
```

## <a name="example-using-the-configuration-script-in-azure-storage"></a>Przykład za pomocą skryptu konfiguracji w usłudze Azure Storage

Poniższy przykład pochodzi [Omówienie obsługi rozszerzenia DSC](dsc-overview.md).
W tym przykładzie używane szablony Menedżera zasobów zamiast polecenia cmdlet, aby wdrożyć rozszerzenie.
Zapisz konfigurację IisInstall.ps1, umieść go w formie pliku .zip i następnie przekazać plik w dostępny adres URL.
W tym przykładzie użyto magazynu obiektów Blob platformy Azure, ale możesz pobrać z dowolnego miejsca i dowolnego pliki z rozszerzeniem .zip.

W szablonie usługi Resource Manager następujący kod powoduje, że maszyna wirtualna może pobrać właściwy plik, a następnie uruchom odpowiednią funkcję programu PowerShell:

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## <a name="update-from-a-previous-format"></a>Aktualizacja z poprzedniego formatu

Wszystkie ustawienia w poprzednim formacie rozszerzenia (, których właściwości publiczne **ModulesUrl**, **ConfigurationFunction**, **SasToken**, lub  **Właściwości**) automatycznie dostosowania do bieżącego formatu rozszerzenia.
Działają tak samo jak przed.

Następującego schematu pokazuje jakie poprzednie ustawienia schemat wyszukiwanego, takich jak:

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

Oto, jak poprzedni format dostosowuje się do bieżącego formatu:

| Nazwa właściwości | Poprzednie schematu równoważne |
| --- | --- |
| settings.wmfVersion |Ustawienia. WMFVersion |
| settings.configuration.url |settings.ModulesUrl |
| settings.configuration.script |Pierwsza część ustawień. ConfigurationFunction (przed \\ \\) |
| settings.configuration.function |Druga część ustawień. ConfigurationFunction (po \\ \\) |
| settings.configurationArguments |settings.Properties |
| settings.configurationData.url |protectedSettings.DataBlobUri (bez tokenu sygnatury dostępu Współdzielonego) |
| settings.privacy.dataEnabled |settings.Privacy.DataEnabled |
| settings.advancedOptions.downloadMappings |settings.AdvancedOptions.DownloadMappings |
| protectedSettings.configurationArguments |protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken |settings.SasToken |
| protectedSettings.configurationDataUrlSasToken |Token sygnatury dostępu Współdzielonego z protectedSettings.DataBlobUri |

## <a name="troubleshooting---error-code-1100"></a>Rozwiązywanie problemów — kod błędu: 1100

Kod błędu: 1100 wskazuje na problem z danych wejściowych użytkownika do rozszerzenia usługi Konfiguracja DSC.
Tekst tych błędów zmienia się i mogą ulec zmianie.
Oto kilka błędów, które możesz napotkać i jak można je naprawić.

### <a name="invalid-values"></a>Nieprawidłowe wartości

"Jest Privacy.dataCollection"{0}".
Tylko możliwe wartości to ","Włącz"i"Wyłącz"".
"Jest WmfVersion"{0}".
Tylko możliwe wartości to... i "najnowsze" ".

**Problem**: podana wartość nie jest dozwolona.

**Rozwiązanie**: Zmień nieprawidłową wartość na prawidłową wartość.
Aby uzyskać więcej informacji, zobacz tabelę w [szczegóły](#details).

### <a name="invalid-url"></a>Nieprawidłowy adres URL

"Jest ConfigurationData.url"{0}". Nie jest prawidłowym adresem URL"" jest DataBlobUri "{0}". Nie jest prawidłowym adresem URL"" jest Configuration.url "{0}". Nie jest prawidłowym adresem URL"

**Problem**: A podany adres URL jest nieprawidłowy.

**Rozwiązanie**: Sprawdź wszystkie podane adresami URL.
Upewnij się, że wszystkie adresy URL rozpoznać jako prawidłowych lokalizacji czy rozszerzenia dostęp do komputera zdalnego.

### <a name="invalid-configurationargument-type"></a>Nieprawidłowy typ ConfigurationArgument

"Nieprawidłowa configurationArguments typu {0}"

**Problem**: *ConfigurationArguments* nie można rozpoznać właściwości **Hashtable** obiektu.

**Rozwiązanie**: Wprowadź Twojej *ConfigurationArguments* właściwości **Hashtable**.
Postępuj zgodnie z formatu podany w poprzednim przykładzie. Obserwowanie oferty, przecinki i nawiasy.

### <a name="duplicate-configurationarguments"></a>Zduplikowana ConfigurationArguments

"Znaleziono zduplikowane argumenty{0}" w publicznych i chronionych configurationArguments "

**Problem**: *ConfigurationArguments* w ustawieniach publicznego i *ConfigurationArguments* w ustawieniach chronionego ma właściwości o tej samej nazwie.

**Rozwiązanie**: Usuń jeden z tych właściwości.

### <a name="missing-properties"></a>Brak właściwości

"Configuration.function wymaga configuration.url lub configuration.module określono"

"Configuration.url wymaga się, że określono tego configuration.script"

"Configuration.script wymaga się, że określono tego configuration.url"

"Configuration.url wymaga się, że określono tego configuration.function"

"ConfigurationUrlSasToken wymaga się, że określono tego configuration.url"

"ConfigurationDataUrlSasToken wymaga się, że określono tego configurationData.url"

**Problem**: zdefiniowanych właściwości wymaga innej właściwości, których brakuje.

**Rozwiązania**:

- Podaj brakuje właściwości.
- Usuń właściwość, która wymaga brakuje właściwości.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [zestawów za pomocą skalowania maszyny wirtualnej z rozszerzeniem Azure DSC](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md).
- Uzyskać więcej informacji na temat [zarządzania poświadczeniami bezpiecznego DSC](dsc-credentials.md).
- Pobierz [wprowadzenie do obsługi rozszerzenia usługi Konfiguracja DSC Azure](dsc-overview.md).
- Aby uzyskać więcej informacji o konfiguracji DSC środowiska PowerShell, przejdź do [Centrum dokumentacji programu PowerShell](/powershell/dsc/overview).
