---
title: Zarządzanie pakietami języka Python 2 w usłudze Azure Automation
description: W tym artykule opisano sposób zarządzania pakietami języka Python 2 w usłudze Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 02/25/2019
ms.topic: conceptual
ms.openlocfilehash: 05d892edf20cda228bc566b30b0b693ea7c4a184
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75417646"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Zarządzanie pakietami języka Python 2 w usłudze Azure Automation

Usługa Azure Automation umożliwia uruchamianie 2 śmiękliń 2 języka Python na platformie Azure i w hybrydowych procesach ceł nych systemu Linux. Aby ułatwić uproszczenie śmięty śmięty, można użyć pakietów Pythona do importowania modułów, które są potrzebne. W tym artykule opisano sposób zarządzania pakietami języka Python i korzystania z niego w usłudze Azure Automation.

## <a name="import-packages"></a>Importowanie pakietów

Na koncie automatyzacji wybierz **pakiety Języka Python 2** w obszarze **Zasoby udostępnione**. Kliknij **+ Dodaj pakiet Python 2**.

![Dodawanie pakietu Języka Python](media/python-packages/add-python-package.png)

Na stronie **Dodaj pakiet Języka Python 2** wybierz pakiet lokalny do przekazania. Pakiet może być `.whl` plikiem lub `.tar.gz` plikiem. Gdy ta opcja jest zaznaczona, kliknij przycisk **OK,** aby przekazać pakiet.

![Dodawanie pakietu Języka Python](media/python-packages/upload-package.png)

Po zaimportowaniu pakietu jest on wyświetlany na stronie **pakietów języka Python 2** na koncie automatyzacji. Jeśli chcesz usunąć pakiet, wybierz pakiet i wybierz **pozycję Usuń** na stronie pakietu.

![Lista pakietów](media/python-packages/package-list.png)

## <a name="import-packages-with-dependencies"></a>Importowanie pakietów z zależnościami

Automatyzacja platformy Azure nie rozwiązuje zależności dla pakietów języka Python podczas procesu importowania. Istnieją dwa sposoby importowania pakietu ze wszystkimi jego zależnościami. Tylko jeden z następujących kroków musi być używany do importowania pakietów do konta automatyzacji.

### <a name="manually-download"></a>Pobieranie ręcznie

Na komputerze z systemem Windows 64-bitowym z [zainstalowanym python2.7](https://www.python.org/downloads/release/latest/python2) i [pip](https://pip.pypa.io/en/stable/) uruchom następujące polecenie, aby pobrać pakiet i wszystkie jego zależności:

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

Po pobraniu pakietów można zaimportować je do konta automatyzacji.

### <a name="runbook"></a>Element Runbook

Zaimportuj pakiety python runbook [Importuj 2 z pypi do konta usługi Azure Automation](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509) z galerii do konta automatyzacji. Upewnij się, że ustawienia uruchamiania są ustawione na **platformę Azure** i uruchom go z parametrami. System runbook wymaga uruchom jako konto dla konta automatyzacji do pracy. Dla każdego parametru upewnij się, że uruchamiasz go z przełącznikiem, jak widać na poniższej liście i obrazie:

* -s \<subscriptionId\>
* -g \<resourceGrupa\>
* -a \<automationKont\>
* -m \<modulePakiet\>

![Lista pakietów](media/python-packages/import-python-runbook.png)

Zestaw runbook pozwala określić, `Azure` jaki pakiet do pobrania, na przykład (czwarty parametr) pobierze wszystkie moduły platformy Azure i wszystkie jego zależności, co stanowi około 105.

Po zakończeniu uruchomieniu można sprawdzić python **2 pakiety** strony w obszarze **Zasoby udostępnione** na koncie automatyzacji, aby sprawdzić, czy pakiet został zaimportowany poprawnie.

## <a name="use-a-package-in-a-runbook"></a>Używanie pakietu w yjsce

Po zaimportowaniu pakietu można go teraz używać w bieście runbooka. W poniższym przykładzie użyto [pakietu narzędzi usługi Azure Automation](https://github.com/azureautomation/azure_automation_utility). Ten pakiet ułatwia korzystanie z języka Python z usługą Azure Automation. Aby użyć pakietu, postępuj zgodnie z instrukcjami w repozytorium GitHub `from azure_automation_utility import get_automation_runas_credential` i dodaj go do elementu runbook, używając na przykład do importowania funkcji pobierania konta RunAs.

```python
import azure.mgmt.resource
import automationassets
from azure_automation_utility import get_automation_runas_credential

# Authenticate to Azure using the Azure Automation RunAs service principal
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
azure_credential = get_automation_runas_credential()

# Intialize the resource management client with the RunAs credential and subscription
resource_client = azure.mgmt.resource.ResourceManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"]))

# Get list of resource groups and print them out
groups = resource_client.resource_groups.list()
for group in groups:
    print group.name
```

## <a name="develop-and-test-runbooks-offline"></a>Tworzenie i testowanie wiązek umanych w trybie offline

Aby opracować i przetestować swoje elementy runbook języka Python 2 w trybie offline, można użyć modułu [emulowanych zasobów python usługi Azure Automation](https://github.com/azureautomation/python_emulated_assets) w usłudze GitHub. Ten moduł umożliwia odwoływanie się do zasobów udostępnionych, takich jak poświadczenia, zmienne, połączenia i certyfikaty.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć korzystanie z śmięty python 2, zobacz [Mój pierwszy podręcznik Pythona 2](automation-first-runbook-textual-python2.md)
