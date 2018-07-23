---
title: Desired State Configuration rozszerzenia z szablonami usługi Azure Resource Manager
description: Więcej informacji na temat definicji szablonu usługi Resource Manager dla rozszerzenia Desired State Configuration (DSC) na platformie Azure.
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
ms.openlocfilehash: d007869bb8bad1a2f0775a1ab2c1bf5d27c1cb8f
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37866228"
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>Desired State Configuration rozszerzenia z szablonami usługi Azure Resource Manager

W tym artykule opisano szablon usługi Azure Resource Manager dla [Desired State Configuration (DSC) rozszerzenia obsługi](dsc-overview.md).

> [!NOTE]
> Przykłady schematu nieco inne, mogą wystąpić. Zmiany w schemacie wystąpił w wersji z października 2016 r. Aby uzyskać więcej informacji, zobacz [aktualizacji z poprzedniego formatu](#update-from-a-previous-format).

## <a name="template-example-for-a-windows-vm"></a>Przykład szablonu dla maszyny Wirtualnej z systemem Windows

Poniższy fragment kodu znajduje się w **zasobów** części szablonu.
Rozszerzenie DSC dziedziczy właściwości rozszerzenia domyślne.
Aby uzyskać więcej informacji, zobacz [klasy VirtualMachineExtension](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension?view=azure-dotnet.).

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
                        "RegistrationUrl": "registrationUrl"
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

## <a name="template-example-for-windows-virtual-machine-scale-sets"></a>Ustawia szablonu przykład skalowania maszyn wirtualnych Windows

Węzeł zestaw skali maszyny wirtualnej ma **właściwości** sekcji, która ma **VirtualMachineProfile, extensionProfile** atrybutu.
W obszarze **rozszerzenia**, dodać szczegóły dotyczące rozszerzenia DSC.

Rozszerzenie DSC dziedziczy właściwości rozszerzenia domyślne.
Aby uzyskać więcej informacji, zobacz [klasy VirtualMachineScaleSetExtension](/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension?view=azure-dotnet).

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
                            "RegistrationUrl": "registrationUrl"
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
| settings.wmfVersion |ciąg |Określa wersję programu Windows Management Framework (WMF), ma być zainstalowany na maszynie Wirtualnej. Ustawienie tej właściwości na **najnowsze** instaluje najnowszą wersję programu WMF. Obecnie jedyną możliwą wartości dla tej właściwości to **4.0**, **5.0**, **5.0PP**, i **najnowsze**. Te wartości możliwe jest zależna od aktualizacji. Wartość domyślna to **najnowsze**. |
| settings.configuration.url |ciąg |Określa lokalizację adresu URL, z której można pobrać plik zip konfiguracji DSC. Jeśli adres URL podany wymaga tokenu sygnatury dostępu Współdzielonego dla dostępu, ustaw **protectedSettings.configurationUrlSasToken** właściwości na wartość tokenu sygnatury dostępu Współdzielonego. Ta właściwość jest wymagana, jeśli **settings.configuration.script** lub **settings.configuration.function** są zdefiniowane. Jeśli wartość nie zostanie podany dla tych właściwości, rozszerzenie wywołuje skrypt konfiguracji domyślnej można ustawić metadanych lokalizacji Configuration Manager (LCM), a powinien być dostarczony argumentów. |
| settings.configuration.script |ciąg |Określa nazwę pliku skryptu, który zawiera definicję konfiguracji DSC. Ten skrypt musi być w folderze głównym pliku zip, który jest pobierany z adresu URL określonego przez **configuration.url** właściwości. Ta właściwość jest wymagana, jeśli **settings.configuration.url** lub **settings.configuration.script** są zdefiniowane. Jeśli wartość nie zostanie podany dla tych właściwości, rozszerzenie wywołuje skrypt konfiguracji domyślnej można ustawić metadanych LCM i powinien być dostarczony argumentów. |
| settings.configuration.function |ciąg |Określa nazwę konfiguracji DSC. Konfiguracja, który nosi nazwę muszą być zawarte w skrypcie, **configuration.script** definiuje. Ta właściwość jest wymagana, jeśli **settings.configuration.url** lub **settings.configuration.function** są zdefiniowane. Jeśli wartość nie zostanie podany dla tych właściwości, rozszerzenie wywołuje skrypt konfiguracji domyślnej można ustawić metadanych LCM i powinien być dostarczony argumentów. |
| settings.configurationArguments |Collection |Określa wszelkie parametry, które mają być przekazane do konfiguracji DSC. Ta właściwość nie jest zaszyfrowany. |
| settings.configurationData.url |ciąg |Określa adres URL do pobrania pliku (psd1) danych konfiguracji do użycia jako dane wejściowe dla danej konfiguracji DSC. Jeśli adres URL podany wymaga tokenu sygnatury dostępu Współdzielonego dla dostępu, ustaw **protectedSettings.configurationDataUrlSasToken** właściwości na wartość tokenu sygnatury dostępu Współdzielonego. |
| settings.privacy.dataEnabled |ciąg |Włącza lub wyłącza zbieranie danych telemetrycznych. Tylko wtedy możliwe wartości dla tej właściwości to **Włącz**, **wyłączyć**, **''**, lub **$null**. Pozostawienie tej właściwości, puste ani mieć wartości null umożliwia danych telemetrycznych. Wartość domyślna to **''**. Aby uzyskać więcej informacji, zobacz [zbierania danych rozszerzenia DSC usługi Azure](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/). |
| settings.advancedOptions.downloadMappings |Collection |Definiuje alternatywnych lokalizacji, z którego będą pobierane programu WMF. Aby uzyskać więcej informacji, zobacz [rozszerzenie DSC usługi Azure 2.8 i sposób mapowania plików do pobrania zależności rozszerzenia na własnej lokalizacji](http://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx). |
| protectedSettings.configurationArguments |Collection |Określa wszelkie parametry, które mają być przekazane do konfiguracji DSC. Ta właściwość jest szyfrowana. |
| protectedSettings.configurationUrlSasToken |ciąg |Określa tokenu sygnatury dostępu Współdzielonego na potrzeby dostępu do adresu URL, **configuration.url** definiuje. Ta właściwość jest szyfrowana. |
| protectedSettings.configurationDataUrlSasToken |ciąg |Określa tokenu sygnatury dostępu Współdzielonego na potrzeby dostępu do adresu URL, **configurationData.url** definiuje. Ta właściwość jest szyfrowana. |

## <a name="default-configuration-script"></a>Skrypt konfiguracji domyślnej

Aby uzyskać więcej informacji na temat następujących wartości zobacz [Local Configuration Manager podstawowych ustawień](/powershell/dsc/metaconfig#basic-settings).
Skrypt konfiguracji domyślnej rozszerzenia DSC służy do konfigurowania właściwości LCM, które są wymienione w poniższej tabeli.

| Nazwa właściwości | Typ | Opis |
| --- | --- | --- |
| settings.configurationArguments.RegistrationKey |SecureString |Wymagana właściwość. Określa klucz, który jest używany dla węzła do rejestracji w usłudze Azure Automation jako hasło obiekt poświadczeń PowerShell. Ta wartość może automatycznie odnalezione za pomocą **listkeys** metoda względem konta usługi Automation. Wartości powinny być zabezpieczone w ustawieniach chronionego. |
| settings.configurationArguments.RegistrationUrl |ciąg |Wymagana właściwość. Określa adres URL punktu końcowego usługi Automation, gdzie węzeł, próbuje zarejestrować. Ta wartość może automatycznie odnalezione za pomocą **odwołania** metoda względem konta usługi Automation. |
| settings.configurationArguments.NodeConfigurationName |ciąg |Wymagana właściwość. Określa konfigurację węzła na koncie usługi Automation, które można przypisać do węzła. |
| settings.configurationArguments.ConfigurationMode |ciąg |Określa tryb LCM. Prawidłowe opcje to **ApplyOnly**, **ApplyandMonitor**, i **ApplyandAutoCorrect**.  Wartość domyślna to **ApplyandMonitor**. |
| settings.configurationArguments.RefreshFrequencyMins | uint32 | Określa, jak często LCM próbuje skontaktować się z konta usługi Automation, do aktualizacji.  Wartość domyślna to **30**.  Minimalna wartość to **15**. |
| settings.configurationArguments.ConfigurationModeFrequencyMins | uint32 | Określa, jak często LCM sprawdza bieżącą konfigurację. Wartość domyślna to **15**. Minimalna wartość to **15**. |
| settings.configurationArguments.RebootNodeIfNeeded | wartość logiczna | Określa, czy węzeł zostanie automatycznie ponownie uruchomiony, jeśli operacja DSC żąda ona. Wartość domyślna to **false**. |
| settings.configurationArguments.ActionAfterReboot | ciąg | Określa, co się stanie po ponownym uruchomieniu, gdy trwa operacja zastosowania konfiguracji. Prawidłowe opcje to **ContinueConfiguration** i **StopConfiguration**. Wartość domyślna to **ContinueConfiguration**. |
| settings.configurationArguments.AllowModuleOverwrite | wartość logiczna | Określa, czy LCM zastępuje istniejące moduły w węźle. Wartość domyślna to **false**. |

## <a name="settings-vs-protectedsettings"></a>Ustawienia programu vs. ProtectedSettings

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

Poniższy przykład pochodzi z [Przegląd procedury obsługi rozszerzenia DSC](dsc-overview.md).
W tym przykładzie używa szablonów usługi Resource Manager zamiast polecenia cmdlet, aby wdrożyć rozszerzenie.
Zapisz konfigurację IisInstall.ps1, umieść go w formie pliku .zip, a następnie przekaż plik dostępny adres URL.
W tym przykładzie użyto usługi Azure Blob storage, ale możesz pobrać pliki zip z dowolnego miejsca i dowolnego.

W szablonie usługi Resource Manager poniższy kod powoduje, że maszynę Wirtualną, aby pobrać prawidłowego pliku, a następnie uruchom odpowiednią funkcję programu PowerShell:

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

Wszystkie ustawienia w poprzednim formacie rozszerzenia (, których właściwości publiczne **ModulesUrl**, **ConfigurationFunction**, **SasToken**, lub  **Właściwości**) automatycznie dostosowywać się do bieżącego formatu rozszerzenia.
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

| Nazwa właściwości | Równoważne poprzedniego schematu |
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

Kod błędu: 1100 wskazuje na problem z danymi wejściowymi użytkownika rozszerzenia DSC.
Tekst te błędy różni się i mogą ulec zmianie.
Poniżej przedstawiono niektóre błędy, które możesz napotkać, i jak można je naprawić.

### <a name="invalid-values"></a>Nieprawidłowe wartości

"Jest Privacy.dataCollection"{0}".
Tylko wtedy możliwe wartości to ","Włącz"i"Disable"".
"Jest WmfVersion"{0}".
Tylko wtedy możliwe wartości to... i "najnowszy" ".

**Problem**: podana wartość jest niedozwolona.

**Rozwiązanie**: Zmień nieprawidłową wartość na prawidłową wartość.
Aby uzyskać więcej informacji, zobacz tabelę w [szczegóły](#details).

### <a name="invalid-url"></a>Nieprawidłowy adres URL

"Jest ConfigurationData.url"{0}". Nie jest prawidłowym adresem URL"" jest DataBlobUri "{0}". Nie jest prawidłowym adresem URL"" jest Configuration.url "{0}". Nie jest prawidłowym adresem URL"

**Problem**: A podany adres URL jest nieprawidłowy.

**Rozwiązanie**: Sprawdź wszystkie podane adresami URL.
Należy się upewnić, że wszystkie adresy URL rozpoznać prawidłowych lokalizacji czy rozszerzenia dostęp do komputera zdalnego.

### <a name="invalid-configurationargument-type"></a>Nieprawidłowy typ ConfigurationArgument

"Nieprawidłowa configurationArguments typu {0}"

**Problem**: *ConfigurationArguments* właściwości nie można rozpoznać **Hashtable** obiektu.

**Rozwiązanie**: wprowadzić swoje *ConfigurationArguments* właściwość **Hashtable**.
Postępuj zgodnie z formatu podana w powyższym przykładzie. Poszukaj ofert, przecinki oraz nawiasy klamrowe.

### <a name="duplicate-configurationarguments"></a>Zduplikowane ConfigurationArguments

"Znaleziono zduplikowane argumenty{0}" w configurationArguments zarówno publiczne i chronione "

**Problem**: *ConfigurationArguments* w ustawieniach publicznych i *ConfigurationArguments* w ustawieniach chronionego ma właściwości o takiej samej nazwie.

**Rozwiązanie**: Usuń jeden z tych właściwości.

### <a name="missing-properties"></a>Brak właściwości

"Configuration.function wymaga, że określono configuration.url lub configuration.module"

"Configuration.url wymaga tego configuration.script został określony."

"Configuration.script wymaga tego configuration.url został określony."

"Configuration.url wymaga tego configuration.function został określony."

"ConfigurationUrlSasToken wymaga tego configuration.url został określony."

"ConfigurationDataUrlSasToken wymaga tego configurationData.url został określony."

**Problem**: zdefiniowana właściwość musi inną właściwość, która nie istnieje.

**Rozwiązania**:

- Podaj brakuje właściwości.
- Usuń właściwość, która wymaga brakuje właściwości.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [zestawów za pomocą skalowania maszyn wirtualnych za pomocą rozszerzenia DSC usługi Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md).
- Poznaj więcej szczegółów dotyczących [zarządzania poświadczeniami bezpiecznego firmy DSC](dsc-credentials.md).
- Pobierz [wprowadzenie do procedury obsługi rozszerzenia DSC usługi Azure](dsc-overview.md).
- Aby uzyskać więcej informacji na temat programu PowerShell DSC, przejdź do [Centrum dokumentacji programu PowerShell](/powershell/dsc/overview).
