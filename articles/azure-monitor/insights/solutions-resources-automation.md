---
title: Zasoby usługi Azure Automation w rozwiązaniach do zarządzania | Dokumenty firmy Microsoft
description: Rozwiązania do zarządzania zazwyczaj obejmują elementy runbook w usłudze Azure Automation w celu automatyzacji procesów, takich jak zbieranie i przetwarzanie danych monitorowania.  W tym artykule opisano sposób dołączania śmiób runbook i powiązanych z nimi zasobów do rozwiązania.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/24/2017
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8ef9f27546e9db95d5a41769e1b5bc7bc0c2f851
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663066"
---
# <a name="adding-azure-automation-resources-to-a-management-solution-preview"></a>Dodawanie zasobów usługi Azure Automation do rozwiązania do zarządzania (Wersja zapoznawcza)
> [!NOTE]
> Jest to wstępna dokumentacja do tworzenia rozwiązań do zarządzania, które są obecnie w wersji zapoznawczej. Każdy schemat opisany poniżej może ulec zmianie.   


[Rozwiązania do zarządzania]( solutions.md) zazwyczaj obejmują elementy runbook w usłudze Azure Automation w celu automatyzacji procesów, takich jak zbieranie i przetwarzanie danych monitorowania.  Oprócz śmigieł kont automatyzacji zawiera zasoby, takie jak zmienne i harmonogramy, które obsługują elementy runbook używane w rozwiązaniu.  W tym artykule opisano sposób dołączania śmiób runbook i powiązanych z nimi zasobów do rozwiązania.

> [!NOTE]
> Przykłady w tym artykule używają parametrów i zmiennych, które są wymagane lub wspólne dla rozwiązań do zarządzania i opisane w [Projektowanie i tworzenie rozwiązania do zarządzania na platformie Azure]( solutions-creating.md) 


## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że znasz już następujące informacje.

- Jak utworzyć rozwiązanie do [zarządzania]( solutions-creating.md).
- Struktura pliku [rozwiązania]( solutions-solution-file.md).
- Jak [tworzyć szablony Menedżera zasobów](../../azure-resource-manager/templates/template-syntax.md)

## <a name="automation-account"></a>Konto usługi Automation
Wszystkie zasoby w usłudze Azure Automation są zawarte w [koncie automatyzacji.](../../automation/automation-security-overview.md#automation-account-overview)  Zgodnie z opisem w [obszarze roboczym usługi Log Analytics i konto automatyzacji]( solutions.md#log-analytics-workspace-and-automation-account) konto automatyzacji nie jest zawarte w rozwiązaniu do zarządzania, ale musi istnieć przed zainstalowaniem rozwiązania.  Jeśli nie jest dostępna, instalacja rozwiązania zakończy się niepowodzeniem.

Nazwa każdego zasobu automatyzacji zawiera nazwę jego konta automatyzacji.  Odbywa się to w rozwiązaniu z **parametrem accountName,** jak w poniższym przykładzie zasobu runbook.

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Elementy Runbook
Należy dołączyć wszystkie pliki runbook używane przez rozwiązanie w pliku rozwiązania, tak aby były tworzone po zainstalowaniu rozwiązania.  Nie można jednak zawierać treści zestawu runbook w szablonie, dlatego należy opublikować program runbook w lokalizacji publicznej, do której może uzyskać dostęp każdy użytkownik instalujący rozwiązanie.

Zasoby [elementów runbook usługi Azure Automation](../../automation/automation-runbook-types.md) mają typ **Microsoft.Automation/automationAccounts/runbooks** i mają następującą strukturę. Obejmuje to typowe zmienne i parametry, dzięki czemu można skopiować i wkleić ten fragment kodu do pliku rozwiązania i zmienić nazwy parametrów. 

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


Właściwości elementów runbook są opisane w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| Typ ekwidu |Określa typy elementów runbook. <br><br> Skrypt — skrypt programu PowerShell <br>PowerShell — przepływ pracy programu PowerShell <br> GraphPowerShell — graficzny program powershell <br> GraphPowerShellWorkflow — graficzny program PowerShell |
| logProgress |Określa, czy [rekordy postępu](../../automation/automation-runbook-output-and-messages.md) mają być generowane dla ego księgi runbook. |
| logVerbose |Określa, czy dla ego księgi runbook mają być generowane [pełne rekordy.](../../automation/automation-runbook-output-and-messages.md) |
| description |Opcjonalny opis wiązki eksty. |
| publikowanieContentLink |Określa zawartość wiązki uruchomieniu. . <br><br>uri - Uri do zawartości wiązki uruchomieniu..  Będzie to plik ps1 dla śmięty programu PowerShell i skryptów oraz wyeksportowany graficzny plik runbook dla systemu runbook Graph.  <br> wersja - Wersja runbook dla własnego śledzenia. |


## <a name="automation-jobs"></a>Automatyzacja zadań
Po uruchomieniu systemu runbook w usłudze Azure Automation tworzy zadanie automatyzacji.  Można dodać zasób zadania automatyzacji do rozwiązania, aby automatycznie uruchomić element runbook po zainstalowaniu rozwiązania do zarządzania.  Ta metoda jest zwykle używana do uruchamiania śmięty, które są używane do początkowej konfiguracji rozwiązania.  Aby rozpocząć księgę runbook w regularnych odstępach czasu, utwórz [harmonogram](#schedules) i [harmonogram zadań](#job-schedules)

Zasoby zadań mają typ **Microsoft.Automation/automationAccounts/jobs** i mają następującą strukturę.  Obejmuje to typowe zmienne i parametry, dzięki czemu można skopiować i wkleić ten fragment kodu do pliku rozwiązania i zmienić nazwy parametrów. 

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

Właściwości zadań automatyzacji są opisane w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| element Runbook |Jednostka pojedynczej nazwy o nazwie elementu runbook do uruchomienia. |
| parameters |Jednostka dla każdej wartości parametru wymaganej przez element runbook. |

Zadanie zawiera nazwę uruchomieniu i wszelkie wartości parametrów, które mają być wysyłane do emujnika.  Zadanie powinno [zależeć od]( solutions-solution-file.md#resources) śmiwoja, który rozpoczyna się od czasu utworzenia łańczy ekscesu przed zadaniem.  Jeśli masz wiele kreśleń lecących, które powinny być uruchamiane, można zdefiniować ich kolejność, ponieważ zadanie zależy od innych zadań, które powinny być uruchamiane jako pierwsze.

Nazwa zasobu zadania musi zawierać identyfikator GUID, który jest zazwyczaj przypisywany przez parametr.  Więcej informacji na temat parametrów identyfikatora GUID można przeczytać w [temacie Tworzenie pliku rozwiązania do zarządzania na platformie Azure]( solutions-solution-file.md#parameters).  


## <a name="certificates"></a>Certyfikaty
[Certyfikaty usługi Azure Automation](../../automation/automation-certificates.md) mają typ **Microsoft.Automation/automationAccounts/certificates** i mają następującą strukturę. Obejmuje to typowe zmienne i parametry, dzięki czemu można skopiować i wkleić ten fragment kodu do pliku rozwiązania i zmienić nazwy parametrów. 

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



Właściwości zasobów certyfikatów są opisane w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| base64Value |Wartość podstawowa 64 dla certyfikatu. |
| Odcisk palca |Odcisk palca certyfikatu. |



## <a name="credentials"></a>Poświadczenia
[Poświadczenia usługi Azure Automation](../../automation/automation-credentials.md) mają typ **Microsoft.Automation/automationAccounts/credentials** i mają następującą strukturę.  Obejmuje to typowe zmienne i parametry, dzięki czemu można skopiować i wkleić ten fragment kodu do pliku rozwiązania i zmienić nazwy parametrów. 


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
| userName |Nazwa użytkownika poświadczeń. |
| hasło |Hasło do poświadczenia. |


## <a name="schedules"></a>Harmonogramy
[Harmonogramy usługi Azure Automation](../../automation/automation-schedules.md) mają typ **Microsoft.Automation/automationAccounts/schedules** i mają następującą strukturę. Obejmuje to typowe zmienne i parametry, dzięki czemu można skopiować i wkleić ten fragment kodu do pliku rozwiązania i zmienić nazwy parametrów. 

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

Właściwości zasobów harmonogramu są opisane w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| description |Opcjonalny opis harmonogramu. |
| startTime |Określa godzinę rozpoczęcia harmonogramu jako obiektu DateTime. Ciąg może być dostarczony, jeśli można go przekonwertować na prawidłową datetime. |
| Isenabled |Określa, czy harmonogram jest włączony. |
| interval |Typ interwału dla harmonogramu.<br><br>dzień<br>godzina |
| frequency |Częstotliwość, że harmonogram powinien strzelać w liczbie dni lub godzin. |

Harmonogramy muszą mieć czas rozpoczęcia o wartości większej niż bieżący czas.  Nie można podać tej wartości ze zmienną, ponieważ nie można wiedzieć, kiedy ma zostać zainstalowana.

Użyj jednej z następujących dwóch strategii podczas korzystania z zasobów harmonogramu w rozwiązaniu.

- Użyj parametru dla czasu rozpoczęcia harmonogramu.  Spowoduje to monit użytkownika o podanie wartości podczas instalowania rozwiązania.  Jeśli masz wiele harmonogramów, można użyć pojedynczej wartości parametru dla więcej niż jednego z nich.
- Tworzenie harmonogramów przy użyciu uruchomieniu, który rozpoczyna się po zainstalowaniu rozwiązania.  Spowoduje to usunięcie wymagań użytkownika, aby określić czas, ale nie można zawierać harmonogram w rozwiązaniu, więc zostanie usunięty po usunięciu rozwiązania.


### <a name="job-schedules"></a>Harmonogramy zadań
Zasoby harmonogramu zadań łączą runbook z harmonogramem.  Mają typ **Microsoft.Automation/automationAccounts/jobSchedules** i mają następującą strukturę.  Obejmuje to typowe zmienne i parametry, dzięki czemu można skopiować i wkleić ten fragment kodu do pliku rozwiązania i zmienić nazwy parametrów. 

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


Właściwości harmonogramów zadań są opisane w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| nazwa harmonogramu |Pojedyncza **encja nazwy** z nazwą harmonogramu. |
| nazwa runbooka  |Pojedyncza **encja nazwy** o nazwie elementu runbook.  |



## <a name="variables"></a>Zmienne
[Zmienne usługi Azure Automation](../../automation/automation-variables.md) mają typ **Microsoft.Automation/automationAccounts/variables** i mają następującą strukturę.  Obejmuje to typowe zmienne i parametry, dzięki czemu można skopiować i wkleić ten fragment kodu do pliku rozwiązania i zmienić nazwy parametrów.

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

Właściwości dla zasobów zmiennych są opisane w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| description | Opcjonalny opis zmiennej. |
| Isencrypted | Określa, czy zmienna ma być szyfrowana. |
| type | Ta właściwość obecnie nie ma wpływu.  Typ danych zmiennej zostanie określony przez wartość początkową. |
| value | Wartość zmiennej. |

> [!NOTE]
> Właściwość **typu** obecnie nie ma wpływu na zmienną, która jest tworzona.  Typ danych dla zmiennej zostanie określony przez wartość.  

Jeśli ustawisz wartość początkową zmiennej, musi ona być skonfigurowana jako poprawny typ danych.  Poniższa tabela zawiera różne typy danych dopuszczalne i ich składni.  Należy zauważyć, że wartości w JSON powinny być zawsze ujęte w cudzysłowie z dowolnymi znakami specjalnymi w cudzysłowach.  Na przykład wartość ciągu zostanie określona przez cudzysłowy\\wokół ciągu (przy użyciu znaku ucieczki ( )), podczas gdy wartość liczbowa zostanie określona z jednym zestawem cudzysłowów.

| Typ danych | Opis | Przykład | Rozwiązuje problem |
|:--|:--|:--|:--|
| ciąg   | Wartość należy ująć w cudzysłowy.  | "\"Hello\"world " | "Witaj świecie" |
| numeryczne  | Wartość liczbowa z pojedynczymi cudzysłowami.| "64" | 64 |
| wartość logiczna  | **prawda** lub **fałsz** w cudzysłowie.  Należy zauważyć, że ta wartość musi być małe litery. | "to prawda" | true |
| datetime | Seryjna wartość daty.<br>Polecenie cmdlet ConvertTo-Json w programie PowerShell służy do generowania tej wartości dla określonej daty.<br>Przykład: get-date "24/2017 13:14:57" \| ConvertTo-Json | "\\/Date(1495656897378)\\/" | 2017-05-24 13:14:57 |

## <a name="modules"></a>Moduły
Rozwiązanie do zarządzania nie musi definiować [modułów globalnych używanych](../../automation/automation-integration-modules.md) przez elementy runbook, ponieważ będą one zawsze dostępne na koncie automatyzacji.  Należy dołączyć zasób dla dowolnego innego modułu używanego przez runbooks.

[Moduły integracji](../../automation/automation-integration-modules.md) mają typ **Microsoft.Automation/automationAccounts/modules** i mają następującą strukturę.  Obejmuje to typowe zmienne i parametry, dzięki czemu można skopiować i wkleić ten fragment kodu do pliku rozwiązania i zmienić nazwy parametrów.

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


Właściwości zasobów modułu są opisane w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| Contentlink |Określa zawartość modułu. <br><br>uri - Uri do zawartości modułu.  Będzie to plik ps1 dla śmięty programu PowerShell i skryptów oraz wyeksportowany graficzny plik runbook dla systemu runbook Graph.  <br> wersja - Wersja modułu do własnego śledzenia. |

Grupa runbook powinna zależeć od zasobu modułu, aby upewnić się, że jest on tworzony przed runbook.

### <a name="updating-modules"></a>Aktualizowanie modułów
Jeśli zaktualizujesz rozwiązanie do zarządzania, które zawiera system runbook, który używa harmonogramu, a nowa wersja rozwiązania ma nowy moduł używany przez ten system runbook, a następnie runbook może użyć starej wersji modułu.  Należy dołączyć następujące elementy runbook w rozwiązaniu i utworzyć zadanie, aby uruchomić je przed innymi elementów runbook.  Zapewni to, że wszystkie moduły są aktualizowane zgodnie z wymaganiami przed załadowaniem śmięty.

* [Update-ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/) zapewni, że wszystkie moduły używane przez runbooks w rozwiązaniu są najnowszą wersją.  
* [ReRegisterAutomationSchedule-MS-Mgmt](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/) ponownie wyrejestruje wszystkie zasoby harmonogramu, aby upewnić się, że programy runbook połączone z nimi za pomocą najnowszych modułów.




## <a name="sample"></a>Sample
Poniżej przedstawiono przykład rozwiązania, które obejmują, które obejmuje następujące zasoby:

- Runbook.  Jest to przykładowy projekt runbook przechowywany w publicznym repozytorium GitHub.
- Zadanie automatyzacji, które uruchamia runbook po zainstalowaniu rozwiązania.
- Harmonogram i harmonogram zadań, aby rozpocząć runbook w regularnych odstępach czasu.
- Certyfikat.
- Poświadczeń.
- Zmiennej.
- Moduł.  Jest to [moduł OMSIngestionAPI](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) do zapisywania danych w usłudze Log Analytics. 

Przykład używa zmiennych [parametrów rozwiązania standardowego,]( solutions-solution-file.md#parameters) które często byłyby używane w rozwiązaniu, w przeciwieństwie do wartości hardcoding w definicjach zasobów.


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




## <a name="next-steps"></a>Następne kroki
* [Dodaj widok do rozwiązania,]( solutions-resources-views.md) aby wizualizować zebrane dane.
