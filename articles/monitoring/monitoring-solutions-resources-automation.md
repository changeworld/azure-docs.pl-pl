---
title: Zasobów usługi Automatyzacja Azure w rozwiązaniach do zarządzania | Dokumentacja firmy Microsoft
description: Rozwiązania do zarządzania zwykle zawiera elementy runbook automatyzacji Azure można zautomatyzować procesy, takie jak zbierania i przetwarzania danych monitorowania.  W tym artykule opisano, jak dołączyć elementy runbook i ich powiązane zasoby w rozwiązaniu.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 5281462e-f480-4e5e-9c19-022f36dce76d
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a7f32cc47c96f6fc8adaedef8f8ea3156ee70115
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
ms.locfileid: "33887895"
---
# <a name="adding-azure-automation-resources-to-a-management-solution-preview"></a>Dodawanie zasobów usługi Automatyzacja Azure do rozwiązania do zarządzania (wersja zapoznawcza)
> [!NOTE]
> To jest wstępna dokumentacji do tworzenia rozwiązań do zarządzania, które są obecnie w wersji zapoznawczej. Żadnego schematu opisanych poniżej może ulec zmianie.   


[Rozwiązania do zarządzania]( monitoring-solutions.md) zazwyczaj uwzględnia elementy runbook automatyzacji Azure można zautomatyzować procesy, takie jak zbierania i przetwarzania danych monitorowania.  Oprócz elementów runbook kont automatyzacji zawiera zasoby, takie jak zmienne i harmonogramy, obsługujących elementy runbook używane w rozwiązaniu.  W tym artykule opisano, jak dołączyć elementy runbook i ich powiązane zasoby w rozwiązaniu.

> [!NOTE]
> Przykłady w tym artykule, użyj parametrów i zmiennych, które są wymagane ani wspólne dla rozwiązań do zarządzania i opisano w [projektowanie i tworzenie rozwiązania do zarządzania na platformie Azure ]( monitoring-solutions-creating.md) 


## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule przyjęto założenie, że znasz już następujące informacje.

- Jak [tworzenie rozwiązania do zarządzania]( monitoring-solutions-creating.md).
- Struktura [plik rozwiązania]( monitoring-solutions-solution-file.md).
- Jak [Tworzenie szablonów usługi Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)

## <a name="automation-account"></a>Konto usługi Automation
Wszystkie zasoby w automatyzacji Azure są zawarte w [konto automatyzacji](../automation/automation-security-overview.md#automation-account-overview).  Zgodnie z opisem w [obszaru roboczego analizy dzienników i konto automatyzacji]( monitoring-solutions.md#log-analytics-workspace-and-automation-account) konta automatyzacji nie jest zawarty w rozwiązaniu do zarządzania, ale musi istnieć przed zainstalowaniem rozwiązania.  Jeśli nie jest dostępny, nie będą instalacji rozwiązania.

Nazwa każdego zasobu automatyzacji zawiera nazwę swojego konta automatyzacji.  Jest to rozwiązanie z **accountName** parametru, jak w poniższym przykładzie zasobu elementu runbook.

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Elementy Runbook
Należy uwzględnić wszystkie elementy runbook, dzięki czemu są tworzone po zainstalowaniu rozwiązania już używana przez rozwiązania w pliku rozwiązania.  Treść elementu runbook w szablonie nie mogą zawierać jednak, należy opublikować element runbook do lokalizacji publicznej, gdzie jest dostępny przez dowolnego użytkownika instalowanie rozwiązania.

[Element runbook automatyzacji Azure](../automation/automation-runbook-types.md) zasoby mają typ **Microsoft.Automation/automationAccounts/runbooks** i ma następującą strukturę. W tym wspólnych zmiennych i parametrów, aby mogli skopiuj i wklej następujący fragment kodu w pliku rozwiązania i Zmień nazwy parametrów. 

    {
        "name": "[concat(parameters('accountName'), '/', variables('Runbook').Name)]",
        "type": "Microsoft.Automation/automationAccounts/runbooks",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "dependsOn": [
        ],
        "location": "[parameters('regionId')]",
        "tags": { },
        "properties": {
            "runbookType": "[variables('Runbook').Type]",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('Runbook').Description]",
            "publishContentLink": {
                "uri": "[variables('Runbook').Uri]",
                "version": [variables('Runbook').Version]"
            }
        }
    }


W poniższej tabeli opisano właściwości dla elementów runbook.

| Właściwość | Opis |
|:--- |:--- |
| runbookType |Określa typy elementu runbook. <br><br> Skrypt — skrypt programu PowerShell <br>PowerShell — przepływ pracy programu PowerShell <br> GraphPowerShell - runbook skryptu PowerShell graficznego <br> GraphPowerShellWorkflow - PowerShell graficzny element runbook przepływu pracy |
| logProgress |Określa, czy [rekordy postępu](../automation/automation-runbook-output-and-messages.md) ma być generowany dla elementu runbook. |
| logVerbose |Określa, czy [rekordów pełnych](../automation/automation-runbook-output-and-messages.md) ma być generowany dla elementu runbook. |
| description |Opcjonalny opis dla elementu runbook. |
| publishContentLink |Określa zawartość elementu runbook. <br><br>Identyfikator URI - identyfikator Uri zawartości elementu runbook.  Są to plik .ps1 dla elementów runbook programu PowerShell i skryptów, a plik wyeksportowany element runbook graficzny element runbook wykresu.  <br> Wersja — wersji elementu runbook do własnych śledzenia. |


## <a name="automation-jobs"></a>Automatyzacja zadań
Po uruchomieniu elementu runbook w automatyzacji Azure tworzy zadanie usługi Automatyzacja.  Można dodać zasobu Zadanie usługi Automatyzacja do rozwiązania, aby automatycznie uruchomić element runbook po zainstalowaniu rozwiązania do zarządzania.  Ta metoda jest zwykle używana do uruchamiania elementów runbook, które są używane do początkowej konfiguracji rozwiązania.  Aby uruchomić element runbook w regularnych odstępach czasu, należy utworzyć [harmonogram](#schedules) i [harmonogram zadań](#job-schedules)

Zasoby zadanie ma typ **Microsoft.Automation/automationAccounts/jobs** i ma następującą strukturę.  W tym wspólnych zmiennych i parametrów, aby mogli skopiuj i wklej następujący fragment kodu w pliku rozwiązania i Zmień nazwy parametrów. 

    {
      "name": "[concat(parameters('accountName'), '/', parameters('Runbook').JobGuid)]",
      "type": "Microsoft.Automation/automationAccounts/jobs",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('Runbook').Name)]"
      ],
      "tags": { },
      "properties": {
        "runbook": {
          "name": "[variables('Runbook').Name]"
        },
        "parameters": {
          "Parameter1": "[[variables('Runbook').Parameter1]",
          "Parameter2": "[[variables('Runbook').Parameter2]"
        }
      }
    }

Właściwości automatyzacji zadań są opisane w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| runbook |Jedna nazwa jednostki o nazwie elementu runbook, aby rozpocząć. |
| parameters |Jednostki dla każdej wartości parametru wymagane przez element runbook. |

Zadanie zawiera nazwę elementu runbook i wartości parametrów do wysłania do elementu runbook.  Zadanie powinno [są zależne od]( monitoring-solutions-solution-file.md#resources) elementu runbook, który jest uruchamiany od elementu runbook musi zostać utworzone przed zadania.  Jeśli masz wiele elementów runbook, który ma być uruchamiany przez zadanie, które są zależne od innych zadań, które powinny być uruchamiane w pierwszy można zdefiniować ich kolejność.

Nazwa zasobu zadania musi zawierać identyfikator GUID, który zazwyczaj jest przypisywany przez parametr.  Więcej o parametrach identyfikatora GUID w [utworzenia pliku rozwiązania zarządzania na platformie Azure]( monitoring-solutions-solution-file.md#parameters).  


## <a name="certificates"></a>Certyfikaty
[Certyfikaty automatyzacji Azure](../automation/automation-certificates.md) typu **Microsoft.Automation/automationAccounts/certificates** i ma następującą strukturę. W tym wspólnych zmiennych i parametrów, aby mogli skopiuj i wklej następujący fragment kodu w pliku rozwiązania i Zmień nazwy parametrów. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Certificate').Name)]",
      "type": "Microsoft.Automation/automationAccounts/certificates",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "base64Value": "[variables('Certificate').Base64Value]",
        "thumbprint": "[variables('Certificate').Thumbprint]"
      }
    }



W poniższej tabeli opisano właściwości zasobów certyfikatów.

| Właściwość | Opis |
|:--- |:--- |
| base64Value |Wartość Base-64 dla certyfikatu. |
| Odcisk palca |Odcisk palca certyfikatu. |



## <a name="credentials"></a>Poświadczenia
[Poświadczenia usługi Automatyzacja Azure](../automation/automation-credentials.md) typu **Microsoft.Automation/automationAccounts/credentials** i ma następującą strukturę.  W tym wspólnych zmiennych i parametrów, aby mogli skopiuj i wklej następujący fragment kodu w pliku rozwiązania i Zmień nazwy parametrów. 


    {
      "name": "[concat(parameters('accountName'), '/', variables('Credential').Name)]",
      "type": "Microsoft.Automation/automationAccounts/credentials",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "userName": "[parameters('credentialUsername')]",
        "password": "[parameters('credentialPassword')]"
      }
    }

W poniższej tabeli opisano właściwości zasobów poświadczeń.

| Właściwość | Opis |
|:--- |:--- |
| userName |Nazwa użytkownika dla poświadczenia. |
| hasło |Hasło dla poświadczeń. |


## <a name="schedules"></a>Harmonogramy
[Harmonogramy automatyzacji Azure](../automation/automation-schedules.md) typu **Microsoft.Automation/automationAccounts/schedules** i ma następującą strukturę. W tym wspólnych zmiennych i parametrów, aby mogli skopiuj i wklej następujący fragment kodu w pliku rozwiązania i Zmień nazwy parametrów. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Schedule').Name)]",
      "type": "microsoft.automation/automationAccounts/schedules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "description": "[variables('Schedule').Description]",
        "startTime": "[parameters('scheduleStartTime')]",
        "timeZone": "[parameters('scheduleTimeZone')]",
        "isEnabled": "[variables('Schedule').IsEnabled]",
        "interval": "[variables('Schedule').Interval]",
        "frequency": "[variables('Schedule').Frequency]"
      }
    }

W poniższej tabeli opisano właściwości planowania zasobów.

| Właściwość | Opis |
|:--- |:--- |
| description |Opcjonalny opis dla harmonogramu. |
| startTime |Określa czas rozpoczęcia harmonogramu jako obiekt typu DateTime. Jeśli można przekonwertować na prawidłowy element DateTime można podać ciąg. |
| IsEnabled |Określa, czy jest włączony harmonogram. |
| interval |Typ interwału harmonogramu.<br><br>dzień<br>godz. |
| frequency |Częstotliwość, z jaką harmonogram powinny wyzwalać w liczbie dni lub godzin. |

Harmonogramy musi mieć godzinę rozpoczęcia o wartości większej niż bieżący czas.  Nie można podać tę wartość za pomocą zmiennych, ponieważ czy nie ma możliwości wiedzy, kiedy przechodzi do zainstalowania.

Za pomocą harmonogramu zasobów w rozwiązaniu, należy użyć jednej z następujących dwóch strategii.

- Czas rozpoczęcia harmonogramu, należy użyć parametru.  To pojawi się monit podanie wartości podczas instalacji rozwiązania.  Jeśli masz wiele harmonogramów można używać wartości jeden parametr dla więcej niż jeden z nich.
- Tworzenie harmonogramów przy użyciu elementu runbook, który rozpoczyna się, gdy rozwiązanie jest zainstalowane.  Spowoduje to usunięcie wymaganie użytkownika, aby określić godzinę, ale nie może zawierać harmonogram w rozwiązaniu, dlatego zostanie usunięta po usunięciu rozwiązania.


### <a name="job-schedules"></a>Harmonogramy zadań
Zadania planowania zasobów powiązania elementu runbook z harmonogramem.  Mają one typu **Microsoft.Automation/automationAccounts/jobSchedules** i ma następującą strukturę.  W tym wspólnych zmiennych i parametrów, aby mogli skopiuj i wklej następujący fragment kodu w pliku rozwiązania i Zmień nazwy parametrów. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Schedule').LinkGuid)]",
      "type": "microsoft.automation/automationAccounts/jobSchedules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "dependsOn": [
        "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
        "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]"
      ],
      "tags": {
      },
      "properties": {
        "schedule": {
          "name": "[variables('Schedule').Name]"
        },
        "runbook": {
          "name": "[variables('Runbook').Name]"
        }
      }
    }


W poniższej tabeli opisano właściwości harmonogramy zadań.

| Właściwość | Opis |
|:--- |:--- |
| Nazwa harmonogramu |Pojedynczy **nazwa** jednostki o nazwę harmonogramu. |
| Nazwa elementu Runbook  |Pojedynczy **nazwa** jednostki o nazwie elementu runbook.  |



## <a name="variables"></a>Zmienne
[Zmienne automatyzacji Azure](../automation/automation-variables.md) typu **Microsoft.Automation/automationAccounts/variables** i ma następującą strukturę.  W tym wspólnych zmiennych i parametrów, aby mogli skopiuj i wklej następujący fragment kodu w pliku rozwiązania i Zmień nazwy parametrów.

    {
      "name": "[concat(parameters('accountName'), '/', variables('Variable').Name)]",
      "type": "microsoft.automation/automationAccounts/variables",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "description": "[variables('Variable').Description]",
        "isEncrypted": "[variables('Variable').Encrypted]",
        "type": "[variables('Variable').Type]",
        "value": "[variables('Variable').Value]"
      }
    }

W poniższej tabeli opisano właściwości dla zmiennej zasobów.

| Właściwość | Opis |
|:--- |:--- |
| description | Opcjonalny opis dla zmiennej. |
| isEncrypted | Określa, czy ma być szyfrowana zmienna. |
| type | Ta właściwość obecnie nie ma znaczenia.  Typ danych zmiennej będzie określana przez wartość początkowa. |
| wartość | Wartość zmiennej. |

> [!NOTE]
> **Typu** właściwości obecnie nie ma wpływu na zmiennej tworzona.  Typ danych zmiennej będzie określana przez wartość.  

Jeśli ustawisz wartość początkowa zmiennej musi być skonfigurowany jako prawidłowy typ danych.  Poniższa tabela zawiera różne typy danych dopuszczalny i ich składni.  Należy pamiętać, że wartości w formacie JSON powinny zawsze być ujęte w cudzysłów z znaków specjalnych w cudzysłowy.  Na przykład wartość ciągu zostałaby określona przez stawiać cudzysłów wokół ciąg (przy użyciu znaku ucieczki (\\)) podczas zostałaby określona wartość liczbową z jednego zestawu znaków cudzysłowu.

| Typ danych | Opis | Przykład | Jest rozpoznawana jako |
|:--|:--|:--|:--|
| ciąg   | Wartość należy ująć w cudzysłów.  | "\"Hello world\"" | "Hello world" |
| numeryczne  | Wartość liczbowa z apostrofy.| "64" | 64 |
| wartość logiczna  | **wartość true,** lub **false** w cudzysłowy.  Należy pamiętać, że ta wartość musi być litera. | wartość "prawda" | true |
| datetime | Serializacji wartości typu date.<br>Polecenia cmdlet ConvertTo-Json w programie PowerShell służy do generowania wartości dla określonej daty.<br>Przykład: get data "2017-5/24 13:14:57" \| ConvertTo-Json | "\\/Date(1495656897378)\\/" | 2017-05-24 13:14:57 |

## <a name="modules"></a>Moduły
Rozwiązania do zarządzania, nie trzeba definiować [modułów globalnych](../automation/automation-integration-modules.md) używany przez elementy runbook, ponieważ zawsze będzie dostępna na Twoim koncie automatyzacji.  Należy uwzględnić zasobów dla innych modułu używany przez elementy runbook.

[Moduły integracji](../automation/automation-integration-modules.md) typu **Microsoft.Automation/automationAccounts/modules** i ma następującą strukturę.  W tym wspólnych zmiennych i parametrów, aby mogli skopiuj i wklej następujący fragment kodu w pliku rozwiązania i Zmień nazwy parametrów.

    {
      "name": "[concat(parameters('accountName'), '/', variables('Module').Name)]",
      "type": "Microsoft.Automation/automationAccounts/modules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "dependsOn": [
      ],
      "properties": {
        "contentLink": {
          "uri": "[variables('Module').Uri]"
        }
      }
    }


W poniższej tabeli opisano właściwości zasobów modułu.

| Właściwość | Opis |
|:--- |:--- |
| contentLink |Określa zawartość modułu. <br><br>Identyfikator URI - identyfikator Uri zawartości modułu.  Są to plik .ps1 dla elementów runbook programu PowerShell i skryptów, a plik wyeksportowany element runbook graficzny element runbook wykresu.  <br> Wersja - modułu dla własnego śledzenia. |

Element runbook należy są zależne od zasobów modułu, aby upewnić się, że został utworzony przed elementu runbook.

### <a name="updating-modules"></a>Aktualizowanie modułów
Aktualizacji rozwiązanie do zarządzania, które zawiera element runbook, który używa harmonogram, a nowa wersja rozwiązań ma nowy moduł używany przez ten element runbook, element runbook może używać starej wersji modułu.  Powinny obejmować następujące elementy runbook w rozwiązaniu i utworzyć zadanie, aby uruchomić je przed wszystkie inne elementy runbook.  Daje to pewność, że wszystkie moduły są aktualizowane, kiedy wymagana przed elementy runbook zostały załadowane.

* [Aktualizacja ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/DisplayScript) sprawdzi wszystkie moduły używane przez elementy runbook w rozwiązaniu do najnowszej wersji.  
* [ReRegisterAutomationSchedule-MS-Mgmt](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/DisplayScript) zostanie ponownie zarejestrować wszystkie zasoby harmonogram, aby upewnić się, że elementy runbook powiązane z nimi przy użyciu najnowszych modułów.




## <a name="sample"></a>Sample
Poniżej przedstawiono przykładowe rozwiązanie obejmujące obejmuje następujące zasoby:

- Element Runbook.  To jest przykładowy element runbook, przechowywane w publicznych repozytorium GitHub.
- Zadanie usługi Automatyzacja, który uruchomi element runbook, gdy rozwiązanie jest zainstalowane.
- Harmonogram i harmonogram uruchamiania elementu runbook w regularnych odstępach czasu.
- Certyfikat.
- Poświadczenie.
- Zmienna.
- Moduł.  Jest to [modułu OMSIngestionAPI](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) do zapisywania danych do analizy dzienników. 

W przykładzie użyto [parametry standardowego rozwiązania]( monitoring-solutions-solution-file.md#parameters) zmiennych, które służy zwykle do rozwiązania, w przeciwieństwie do wartości hardcoding w definicji zasobu.


    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceName": {
          "type": "string",
          "metadata": {
            "Description": "Name of Log Analytics workspace."
          }
        },
        "accountName": {
          "type": "string",
          "metadata": {
            "Description": "Name of Automation account."
          }
        },
        "workspaceregionId": {
          "type": "string",
          "metadata": {
            "Description": "Region of Log Analytics workspace."
          }
        },
        "regionId": {
          "type": "string",
          "metadata": {
            "Description": "Region of Automation account."
          }
        },
        "pricingTier": {
          "type": "string",
          "metadata": {
            "Description": "Pricing tier of both Log Analytics workspace and Azure Automation account."
          }
        },
        "certificateBase64Value": {
          "type": "string",
          "metadata": {
            "Description": "Base 64 value for certificate."
          }
        },
        "certificateThumbprint": {
          "type": "securestring",
          "metadata": {
            "Description": "Thumbprint for certificate."
          }
        },
        "credentialUsername": {
          "type": "string",
          "metadata": {
            "Description": "Username for credential."
          }
        },
        "credentialPassword": {
          "type": "securestring",
          "metadata": {
            "Description": "Password for credential."
          }
        },
        "scheduleStartTime": {
          "type": "string",
          "metadata": {
            "Description": "Start time for shedule."
          }
        },
        "scheduleTimeZone": {
          "type": "string",
          "metadata": {
            "Description": "Time zone for schedule."
          }
        },
        "scheduleLinkGuid": {
          "type": "string",
          "metadata": {
            "description": "GUID for the schedule link to runbook.",
            "control": "guid"
          }
        },
        "runbookJobGuid": {
          "type": "string",
          "metadata": {
            "description": "GUID for the runbook job.",
            "control": "guid"
          }
        }
      },
      "variables": {
        "SolutionName": "MySolution",
        "SolutionVersion": "1.0",
        "SolutionPublisher": "Contoso",
        "ProductName": "SampleSolution",
    
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31",
    
        "Runbook": {
          "Name": "MyRunbook",
          "Description": "Sample runbook",
          "Type": "PowerShell",
          "Uri": "https://raw.githubusercontent.com/user/myrepo/master/samples/MyRunbook.ps1",
          "JobGuid": "[parameters('runbookJobGuid')]"
        },
    
        "Certificate": {
          "Name": "MyCertificate",
          "Base64Value": "[parameters('certificateBase64Value')]",
          "Thumbprint": "[parameters('certificateThumbprint')]"
        },
    
        "Credential": {
          "Name": "MyCredential",
          "UserName": "[parameters('credentialUsername')]",
          "Password": "[parameters('credentialPassword')]"
        },
    
        "Schedule": {
          "Name": "MySchedule",
          "Description": "Sample schedule",
          "IsEnabled": "true",
          "Interval": "1",
          "Frequency": "hour",
          "StartTime": "[parameters('scheduleStartTime')]",
          "TimeZone": "[parameters('scheduleTimeZone')]",
          "LinkGuid": "[parameters('scheduleLinkGuid')]"
        },
    
        "Variable": {
          "Name": "MyVariable",
          "Description": "Sample variable",
          "Encrypted": 0,
          "Type": "string",
          "Value": "'This is my string value.'"
        },
    
        "Module": {
          "Name": "OMSIngestionAPI",
          "Uri": "https://devopsgallerystorage.blob.core.windows.net/packages/omsingestionapi.1.3.0.nupkg"
        }
      },
      "resources": [
        {
          "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
          "location": "[parameters('workspaceRegionId')]",
          "tags": { },
          "type": "Microsoft.OperationsManagement/solutions",
          "apiVersion": "[variables('LogAnalyticsApiVersion')]",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/jobs/', parameters('accountName'), variables('Runbook').JobGuid)]",
            "[resourceId('Microsoft.Automation/automationAccounts/certificates/', parameters('accountName'), variables('Certificate').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/credentials/', parameters('accountName'), variables('Credential').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/jobSchedules/', parameters('accountName'), variables('Schedule').LinkGuid)]",
            "[resourceId('Microsoft.Automation/automationAccounts/variables/', parameters('accountName'), variables('Variable').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/modules/', parameters('accountName'), variables('Module').Name)]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
            "referencedResources": [
              "[resourceId('Microsoft.Automation/automationAccounts/modules/', parameters('accountName'), variables('Module').Name)]"
            ],
            "containedResources": [
              "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/jobs/', parameters('accountName'), variables('Runbook').JobGuid)]",
              "[resourceId('Microsoft.Automation/automationAccounts/certificates/', parameters('accountName'), variables('Certificate').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/credentials/', parameters('accountName'), variables('Credential').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/jobSchedules/', parameters('accountName'), variables('Schedule').LinkGuid)]",
              "[resourceId('Microsoft.Automation/automationAccounts/variables/', parameters('accountName'), variables('Variable').Name)]"
            ]
          },
          "plan": {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
            "Version": "[variables('SolutionVersion')]",
            "product": "[variables('ProductName')]",
            "publisher": "[variables('SolutionPublisher')]",
            "promotionCode": ""
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Runbook').Name)]",
          "type": "Microsoft.Automation/automationAccounts/runbooks",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "location": "[parameters('regionId')]",
          "tags": { },
          "properties": {
            "runbookType": "[variables('Runbook').Type]",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('Runbook').Description]",
            "publishContentLink": {
              "uri": "[variables('Runbook').Uri]",
              "version": "1.0.0.0"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Runbook').JobGuid)]",
          "type": "Microsoft.Automation/automationAccounts/jobs",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('Runbook').Name)]"
          ],
          "tags": { },
          "properties": {
            "runbook": {
              "name": "[variables('Runbook').Name]"
            },
            "parameters": {
              "targetSubscriptionId": "[subscription().subscriptionId]",
              "resourcegroup": "[resourceGroup().name]",
              "automationaccount": "[parameters('accountName')]"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Certificate').Name)]",
          "type": "Microsoft.Automation/automationAccounts/certificates",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "Base64Value": "[variables('Certificate').Base64Value]",
            "Thumbprint": "[variables('Certificate').Thumbprint]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Credential').Name)]",
          "type": "Microsoft.Automation/automationAccounts/credentials",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "userName": "[variables('Credential').UserName]",
            "password": "[variables('Credential').Password]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Schedule').Name)]",
          "type": "microsoft.automation/automationAccounts/schedules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "description": "[variables('Schedule').Description]",
            "startTime": "[variables('Schedule').StartTime]",
            "timeZone": "[variables('Schedule').TimeZone]",
            "isEnabled": "[variables('Schedule').IsEnabled]",
            "interval": "[variables('Schedule').Interval]",
            "frequency": "[variables('Schedule').Frequency]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Schedule').LinkGuid)]",
          "type": "microsoft.automation/automationAccounts/jobSchedules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]"
          ],
          "tags": {
          },
          "properties": {
            "schedule": {
              "name": "[variables('Schedule').Name]"
            },
            "runbook": {
              "name": "[variables('Runbook').Name]"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Variable').Name)]",
          "type": "microsoft.automation/automationAccounts/variables",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "description": "[variables('Variable').Description]",
            "isEncrypted": "[variables('Variable').Encrypted]",
            "type": "[variables('Variable').Type]",
            "value": "[variables('Variable').Value]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Module').Name)]",
          "type": "Microsoft.Automation/automationAccounts/modules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "properties": {
            "contentLink": {
              "uri": "[variables('Module').Uri]"
            }
          }
        }
    
      ],
      "outputs": { }
    }




## <a name="next-steps"></a>Kolejne kroki
* [Dodaj widok do rozwiązania]( monitoring-solutions-resources-views.md) do wizualizacji zebranych danych.
