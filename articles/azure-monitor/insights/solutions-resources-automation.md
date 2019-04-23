---
title: Zasoby automatyzacji platformy Azure w rozwiązaniach do zarządzania | Dokumentacja firmy Microsoft
description: Rozwiązania do zarządzania zwykle będzie zawierać elementy runbook w usłudze Azure Automation do automatyzacji procesów, takich jak zbieranie i przetwarzanie danych monitorowania.  W tym artykule opisano jak dołączać elementy runbook i ich powiązane zasoby w ramach rozwiązania.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 5281462e-f480-4e5e-9c19-022f36dce76d
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1c9b13f44dae068597cb82a0aa803283ad5e67bc
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2019
ms.locfileid: "57763610"
---
# <a name="adding-azure-automation-resources-to-a-management-solution-preview"></a>Dodawanie zasobów usługi Azure Automation do rozwiązania do zarządzania (wersja zapoznawcza)
> [!NOTE]
> Jest to wstępne dokumentację dotyczącą tworzenia rozwiązań do zarządzania, które są obecnie dostępne w wersji zapoznawczej. Żadnego schematu opisanych poniżej ulec zmianie.   


[Rozwiązania do zarządzania]( solutions.md) zwykle zawierają elementy runbook w usłudze Azure Automation do automatyzacji procesów, takich jak zbieranie i przetwarzanie danych monitorowania.  Oprócz elementów runbook konta usługi Automation obejmuje zasoby, takie jak zmienne i harmonogramów, obsługujących elementy runbook, używane w rozwiązaniu.  W tym artykule opisano jak dołączać elementy runbook i ich powiązane zasoby w ramach rozwiązania.

> [!NOTE]
> Przykłady w tym artykule użyć parametrów i zmiennych, które są wymagane lub wspólne dla rozwiązania do zarządzania i opisem w artykule [projektowanie i tworzenie rozwiązania do zarządzania na platformie Azure]( solutions-creating.md) 


## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że znasz już następujące informacje.

- Jak [utworzyć to rozwiązanie do zarządzania]( solutions-creating.md).
- Struktura [plik rozwiązania]( solutions-solution-file.md).
- Jak [Tworzenie szablonów usługi Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md)

## <a name="automation-account"></a>Konto usługi Automation
Wszystkie zasoby w usłudze Azure Automation są zawarte w [konta usługi Automation](../../automation/automation-security-overview.md#automation-account-overview).  Zgodnie z opisem w [obszar roboczy usługi Log Analytics i konto usługi Automation]( solutions.md#log-analytics-workspace-and-automation-account) konta usługi Automation nie jest zawarty w rozwiązaniu do zarządzania, ale musi istnieć przed zainstalowaniem rozwiązania.  Jeśli nie jest dostępny, nie będą instalacji rozwiązania.

Nazwa każdego zasobu automatyzacji zawiera nazwę jego konta usługi Automation.  Jest to realizowane w rozwiązaniu za pomocą **accountName** parametru, jak w poniższym przykładzie zasobów elementu runbook.

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Elementy Runbook
Należy uwzględnić wszelkie elementy runbook, używane przez rozwiązanie w pliku rozwiązania, aby ich utworzenia po zainstalowaniu rozwiązania.  Treść elementu runbook w szablonie nie może zawierać Chociaż, więc należy opublikować element runbook w ogólnodostępnej lokalizacji, gdzie jest dostępny przez dowolnego użytkownika, instalowanie rozwiązania.

[Element runbook automatyzacji Azure](../../automation/automation-runbook-types.md) zasoby mają typ **Microsoft.Automation/automationAccounts/runbooks** i mają następującą strukturę. W tym wspólnych zmiennych i parametrów, aby można skopiuj i wklej następujący fragment kodu do pliku rozwiązania i Zmień nazwy parametrów. 

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


Właściwości dla elementów runbook są opisane w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| runbookType |Określa typy elementu runbook. <br><br> Skrypt — skrypt programu PowerShell <br>PowerShell — przepływ pracy programu PowerShell <br> GraphPowerShell - runbook skryptu PowerShell graficznego <br> GraphPowerShellWorkflow - elementu runbook programu PowerShell graficzny przepływ pracy |
| logProgress |Określa, czy [rekordy postępu](../../automation/automation-runbook-output-and-messages.md) powinny być generowane dla elementu runbook. |
| logVerbose |Określa, czy [rekordów pełnych](../../automation/automation-runbook-output-and-messages.md) powinny być generowane dla elementu runbook. |
| description |Opcjonalny opis dla elementu runbook. |
| publishContentLink |Określa zawartość elementu runbook. <br><br>Identyfikator URI — identyfikator Uri zawartości elementu runbook.  Są to plik .ps1 dla elementów runbook programu PowerShell i skryptów oraz pliku eksportowanego graficznego elementu runbook dla elementu runbook programu Graph.  <br> Wersja — wersji elementu runbook dla własnego śledzenia. |


## <a name="automation-jobs"></a>Automatyzacja zadań
Po uruchomieniu elementu runbook w usłudze Azure Automation, tworzy zadanie usługi automation.  Zasób zadań usługi automation można dodać do rozwiązania, aby automatycznie uruchomić element runbook po zainstalowaniu rozwiązania do zarządzania.  Ta metoda jest zwykle używana do uruchamiania elementów runbook, używane w przypadku konfiguracji początkowej rozwiązania.  Aby uruchomić element runbook w regularnych odstępach czasu, należy utworzyć [harmonogram](#schedules) i [harmonogramu zadań](#job-schedules)

Zasoby zadania mają typ **Microsoft.Automation/automationAccounts/jobs** i mają następującą strukturę.  W tym wspólnych zmiennych i parametrów, aby można skopiuj i wklej następujący fragment kodu do pliku rozwiązania i Zmień nazwy parametrów. 

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

W poniższej tabeli opisano właściwości dla zadań usługi automation.

| Właściwość | Opis |
|:--- |:--- |
| runbook |Jedna nazwa jednostki o nazwie uruchomienie elementu runbook. |
| parameters |Jednostki dla każdej wartości parametru wymagane przez element runbook. |

Zadanie zawiera nazwę elementu runbook i wartości parametrów do wysłania do elementu runbook.  To zadanie powinno [zależą od]( solutions-solution-file.md#resources) elementu runbook, który jest uruchamiany od elementu runbook musi zostać utworzone przed nim.  Jeśli masz wiele elementów runbook, który ma być uruchamiany przez zadanie, które są zależne od innych zadań, które powinien być uruchamiany w pierwszy można zdefiniować ich kolejność.

Nazwa zasobu zadania musi zawierać identyfikator GUID, który zazwyczaj jest przypisywany przez parametr.  Możesz dowiedzieć się więcej o parametrach identyfikator GUID w [tworzenia pliku rozwiązania zarządzania na platformie Azure]( solutions-solution-file.md#parameters).  


## <a name="certificates"></a>Certyfikaty
[Certyfikaty usługi Azure Automation](../../automation/automation-certificates.md) mają typ **Microsoft.Automation/automationAccounts/certificates** i mają następującą strukturę. W tym wspólnych zmiennych i parametrów, aby można skopiuj i wklej następujący fragment kodu do pliku rozwiązania i Zmień nazwy parametrów. 

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



Właściwości zasobów certyfikaty są opisane w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| base64Value |Wartość Base-64 dla certyfikatu. |
| thumbprint |Odcisk palca certyfikatu. |



## <a name="credentials"></a>Poświadczenia
[Poświadczenia usługi Azure Automation](../../automation/automation-credentials.md) mają typ **Microsoft.Automation/automationAccounts/credentials** i mają następującą strukturę.  W tym wspólnych zmiennych i parametrów, aby można skopiuj i wklej następujący fragment kodu do pliku rozwiązania i Zmień nazwy parametrów. 


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

Właściwości zasobów poświadczeń są opisane w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| userName |Nazwa użytkownika dla poświadczenia. |
| password |Hasło dla poświadczeń. |


## <a name="schedules"></a>Harmonogramy
[Usługa Azure Automation harmonogramy](../../automation/automation-schedules.md) mają typ **Microsoft.Automation/automationAccounts/schedules** i mają następującą strukturę. W tym wspólnych zmiennych i parametrów, aby można skopiuj i wklej następujący fragment kodu do pliku rozwiązania i Zmień nazwy parametrów. 

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

Właściwości harmonogramu zasoby są opisane w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| description |Opcjonalny opis dla harmonogramu. |
| startTime |Określa czas rozpoczęcia harmonogramu jako obiekt daty/godziny. Można podać ciąg, jeśli można przekonwertować prawidłową datę/godzinę. |
| isEnabled |Określa, czy jest włączony harmonogram. |
| interval |Typ interwału harmonogramu.<br><br>dzień<br>godz. |
| frequency |Częstotliwość, z którą harmonogram powinny wyzwalać w liczbie dni lub godzin. |

Harmonogramy muszą zawierać godzinę rozpoczęcia o wartości większej niż bieżący czas.  Ta wartość nie może dostarczyć za pomocą zmiennych, ponieważ miałoby żadnej możliwość określenia, kiedy przechodzi do zainstalowania.

Korzystanie z zasobów harmonogramu w ramach rozwiązania, należy użyć jednej z następujących dwóch strategii.

- Czas rozpoczęcia harmonogramu, należy użyć parametru.  To będzie monitować użytkownika o Podaj wartość podczas instalacjo rozwiązania.  Jeśli masz wiele harmonogramów, możesz użyć wartości pojedynczy parametr w więcej niż jeden z nich.
- Tworzenie harmonogramów przy użyciu elementu runbook, który rozpoczyna się, gdy rozwiązanie jest zainstalowane.  Spowoduje to usunięcie wymagań użytkownika, aby określić godzinę, ale nie może zawierać harmonogramu w rozwiązaniu, dlatego zostanie usunięta po usunięciu rozwiązania.


### <a name="job-schedules"></a>Harmonogramy zadań
Zasoby harmonogram zadania połączenie runbook z harmonogramem.  Mają one typu **Microsoft.Automation/automationAccounts/jobSchedules** i mają następującą strukturę.  W tym wspólnych zmiennych i parametrów, aby można skopiuj i wklej następujący fragment kodu do pliku rozwiązania i Zmień nazwy parametrów. 

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


Właściwości harmonogramy zadań są opisane w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| schedule name |Pojedynczy **nazwa** jednostce o nazwie harmonogramu. |
| runbook name  |Pojedynczy **nazwa** jednostce o nazwie elementu runbook.  |



## <a name="variables"></a>Zmienne
[Zmienne automatyzacji Azure](../../automation/automation-variables.md) mają typ **Microsoft.Automation/automationAccounts/variables** i mają następującą strukturę.  W tym wspólnych zmiennych i parametrów, aby można skopiuj i wklej następujący fragment kodu do pliku rozwiązania i Zmień nazwy parametrów.

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

Właściwości dla zmiennej zasoby są opisane w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| description | Opcjonalny opis dla zmiennej. |
| isEncrypted | Określa, czy ma być szyfrowana zmienna. |
| type | Ta właściwość aktualnie nie ma znaczenia.  Typ danych zmiennej będzie określana przez wartość początkową. |
| value | Wartość do zmiennej. |

> [!NOTE]
> **Typu** właściwość aktualnie nie ma wpływu na zmiennej tworzona.  Typ danych zmiennej będzie określana przez wartość.  

Jeśli ustawisz wartość początkową zmiennej, musi być skonfigurowany jako prawidłowy typ danych.  Poniższa tabela zawiera różne typy danych dopuszczalny rozmiar i ich składnię.  Należy pamiętać, że wartości w formacie JSON powinny zawsze być ujęte w cudzysłów, przy użyciu znaków specjalnych w cudzysłowie.  Na przykład wartość ciągu przekazywanej przez ciąg w cudzysłowie (przy użyciu znaku ucieczki (\\)), podczas gdy wartość liczbową, będzie można określić za pomocą jeden zestaw cudzysłowów.

| Typ danych | Opis | Przykład | Jest rozpoznawana jako |
|:--|:--|:--|:--|
| string   | Wartość należy ująć w cudzysłów.  | "\"Witaj, świecie\"" | "Hello world" |
| numeric  | Wartość liczbowa w apostrofy.| "64" | 64 |
| boolean  | **wartość true,** lub **false** w cudzysłowie.  Należy pamiętać, że ta wartość musi być litera. | "true" | true |
| datetime | Wartość daty serializacji.<br>Polecenia cmdlet ConvertTo Json w programie PowerShell służy do generowania tej wartości dla określonej daty.<br>Przykład: get-date "5/24/2017 13:14:57" \| ConvertTo Json | "\\/Date(1495656897378)\\/" | 2017-05-24 13:14:57 |

## <a name="modules"></a>Moduły
Rozwiązania do zarządzania nie trzeba zdefiniować [moduły globalne](../../automation/automation-integration-modules.md) używany przez elementy runbook, ponieważ zawsze będą dostępne na koncie usługi Automation.  Konieczne jest uwzględnienie zasobu na inny moduł, używany przez elementy runbook.

[Moduły integracji](../../automation/automation-integration-modules.md) mają typ **Microsoft.Automation/automationAccounts/modules** i mają następującą strukturę.  W tym wspólnych zmiennych i parametrów, aby można skopiuj i wklej następujący fragment kodu do pliku rozwiązania i Zmień nazwy parametrów.

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


W poniższej tabeli opisano właściwości dla zasobów modułu.

| Właściwość | Opis |
|:--- |:--- |
| contentLink |Określa zawartość modułu. <br><br>Identyfikator URI — identyfikator Uri zawartości modułu.  Są to plik .ps1 dla elementów runbook programu PowerShell i skryptów oraz pliku eksportowanego graficznego elementu runbook dla elementu runbook programu Graph.  <br> Wersja — wersja modułu dla własnego śledzenia. |

Element runbook powinna zależeć od zasobu modułu, aby upewnić się, czy został utworzony przed elementu runbook.

### <a name="updating-modules"></a>Aktualizowanie modułów
Jeśli rozwiązanie do zarządzania, które zawiera element runbook, który korzysta z harmonogramem, a następnie nowa wersja rozwiązania ma nowy moduł używane przez ten element runbook, element runbook może używać starej wersji modułu.  Należy obejmują następujące elementy runbook w Twoim rozwiązaniu, a następnie Utwórz zadanie, aby uruchamiać je przed wszystkie inne elementy runbook.  Pozwoli to zagwarantować, że wszystkie moduły są aktualizowane co wymagane przed elementy runbook są ładowane.

* [Aktualizacja ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/) będzie upewnij się, że wszystkie moduły używane przez elementy runbook w rozwiązaniu do najnowszej wersji.  
* [ReRegisterAutomationSchedule-MS-Mgmt](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/) zostanie ponownie zarejestrować wszystkie zasoby harmonogramu, aby upewnić się, że elementy runbook powiązane z nimi przy użyciu najnowszych modułów.




## <a name="sample"></a>Sample
Poniżej przedstawiono przykładowe rozwiązania, które obejmują zawierającą następujące zasoby:

- Element Runbook.  To jest przykładowy element runbook, przechowywane w publicznym repozytorium GitHub.
- Zadania automatyzacji, który uruchomi element runbook, gdy rozwiązanie jest zainstalowane.
- Harmonogram i harmonogramu zadań, aby uruchomić element runbook w regularnych odstępach czasu.
- certyfikat.
- Poświadczenie.
- Zmienna.
- Moduł.  Jest to [modułu OMSIngestionAPI](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) za zapisywanie danych w usłudze Log Analytics. 

W przykładzie użyto [parametry standardowe rozwiązanie]( solutions-solution-file.md#parameters) zmiennych, które często będzie używana w ramach rozwiązania, w przeciwieństwie do wartości hardcoding w definicji zasobu.


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
            "Description": "Start time for schedule."
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
* [Dodaj widok do rozwiązania]( solutions-resources-views.md) w celu wizualizacji zebranych danych.
