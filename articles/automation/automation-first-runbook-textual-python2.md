---
title: Mój pierwszy element runbook Python w usłudze Azure Automation
description: Samouczek, w którym opisano proces tworzenia, testowania i publikowania prostego elementu runbook języka Python.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 03/19/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a030005d73df61c31c5aa1c2e1b56e52b03a56e3
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478506"
---
# <a name="my-first-python-runbook"></a>Mój pierwszy element runbook Python

> [!div class="op_single_selector"]
> - [Element graficzny](automation-first-runbook-graphical.md)
> - [Program PowerShell](automation-first-runbook-textual-powershell.md)
> - [Przepływ pracy programu PowerShell](automation-first-runbook-textual.md)
> - [Python](automation-first-runbook-textual-python2.md)

Ten samouczek przeprowadzi Cię przez tworzenie [element runbook Python](automation-runbook-types.md#python-runbooks) w usłudze Azure Automation. Możesz rozpocząć od prostego elementu runbook, testowanie i publikowanie. Następnie zmodyfikujesz element runbook, aby faktycznie zarządzał zasobami platformy Azure — w tym przypadku uruchamiając maszynę wirtualną platformy Azure. Na koniec należy elementu runbook bardziej niezawodne, dodając parametry elementu runbook.

> [!NOTE]
> Aby uruchomić element runbook Python przy użyciu elementu webhook nie jest obsługiwana.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Konto usługi Automation](automation-offering-get-started.md) do przechowywania elementu Runbook i uwierzytelniania w zasobach platformy Azure. To konto musi mieć uprawnienia do uruchamiania i zatrzymywania maszyny wirtualnej.
- Maszyna wirtualna platformy Azure. Będziesz uruchamiać i zatrzymywać tę maszynę, dlatego należy użyć maszyny innej niż produkcyjna.

## <a name="create-a-new-runbook"></a>Tworzenie nowego elementu runbook

Rozpocznij od utworzenia prostego elementu runbook służącego do wyświetlania tekstu *Witaj, świecie*.

1. W witrynie Azure Portal otwórz konto usługi Automation.

    Strona konta usługi Automation umożliwia szybki przegląd zasobów na tym koncie. Konto powinno mieć już pewne elementy zawartości. Większość z nich to moduły, które są automatycznie uwzględniane na nowym koncie usługi Automation. Powinien istnieć również element zawartości Poświadczenie wymieniony w części dotyczącej [wymagań wstępnych](#prerequisites).<br>

1. Wybierz pozycję **Elementy runbook** w obszarze **ZARZĄDZANIE PROCESAMI**, aby otworzyć listę elementów runbook.
1. Wybierz **+ Dodaj element runbook** do utworzenia nowego elementu runbook.
1. Nadaj elementowi runbook nazwę *MyFirstRunbook-Python*.
1. W takim przypadku możesz zacząć tworzyć [element runbook Python](automation-runbook-types.md#python-runbooks) dlatego wybierz **języka Python 2** dla **typ elementu Runbook**.
1. Kliknij pozycję **Utwórz**, aby utworzyć element Runbook i otworzyć edytor tekstów.

## <a name="add-code-to-the-runbook"></a>Dodaj kod do elementu runbook

Teraz możesz dodać prostego polecenia do drukowania tekstu "Hello World":

```python
print("Hello World!")
```

Kliknij przycisk **Zapisz** można zapisać elementu runbook.

## <a name="test-the-runbook"></a>Testowanie elementu runbook

Przed opublikowaniem elementu runbook w celu udostępnienia go w środowisku produkcyjnym warto go przetestować, aby upewnić się, że działa prawidłowo. Testowanie elementu Runbook polega na uruchomieniu jego **wersji roboczej** i interaktywnym przejrzeniu danych wyjściowych.

1. Kliknij pozycję **Okienko testowania**, aby otworzyć okienko testowania.
1. Kliknij opcję **Uruchom**, aby rozpocząć test. Powinna to być jedyna włączona opcja.
1. Zostanie utworzone [zadanie elementu Runbook](automation-runbook-execution.md) i pojawi się jego stan.
   Zadanie będzie miało początkowy stan *W kolejce*, wskazujący, że trwa oczekiwanie na udostępnienie procesu roboczego elementu runbook w chmurze. Przenosi do *od* gdy proces roboczy pobierze zadanie, a następnie *systemem* , gdy element runbook faktycznie zacznie działać.
1. Po zakończeniu zadania elementu Runbook zostaną wyświetlone jego dane wyjściowe. W tym przypadku powinna zostać wyświetlona *Witaj, świecie*.
1. Zamknij okienko testowania, aby wrócić do kanwy.

## <a name="publish-and-start-the-runbook"></a>Publikowanie i uruchamianie elementu runbook

Utworzony element runbook nadal działa w trybie roboczym. należy ją opublikować, zanim będzie można uruchomić w środowisku produkcyjnym.
Podczas publikowania elementu runbook, możesz zastąpić istniejącą wersję opublikowanej wersją roboczą.
W takim przypadku nie ma opublikowanej wersji jeszcze został właśnie utworzony element runbook.

1. Kliknij pozycję **Opublikuj**, aby opublikować element Runbook, a następnie kliknij pozycję **Tak** po wyświetleniu monitu.
1. Jeśli przewiniesz w lewo, aby wyświetlić element runbook w programie **elementów Runbook** okienko teraz pokazuje **stan pisania przyp** z **opublikowano**.
1. Przewiń w prawo, aby wyświetlić okienko **MyFirstRunbook-Python**.
   Opcje w górnej części umożliwiają nam uruchamianie elementu runbook, wyświetlanie elementu runbook lub Planowanie jego uruchomienia w przyszłości.
2. Aby uruchomić element runbook, więc klikamy **Start** a następnie kliknij przycisk **Ok** po otwarciu bloku.
3. Zostanie otwarte okienko zadania zadanie elementu runbook, który został utworzony. Możesz zamknąć to okienko, ale w takim przypadku użytkownik pozostanie on otwarty, aby było obserwować postęp zadania.
1. Stan zadania jest wyświetlany w **Podsumowanie zadania** i odpowiada stanom widoczny podczas testowania elementu runbook.
2. Gdy stanem elementu Runbook będzie *Ukończono*, kliknij pozycję **Dane wyjściowe**. Zostanie otwarte okienko danych wyjściowych i można zobaczyć swoje *Witaj, świecie*.
3. Zamknij okienko danych wyjściowych.
4. Kliknij pozycję **Wszystkie dzienniki**, aby otworzyć okienko strumieni dla zadania elementu Runbook. W strumieniu danych wyjściowych powinien być widoczny tylko ciąg *Witaj, świecie*, ale mogą zostać wyświetlone inne strumienie zadania elementu runbook, takie jak Pełne informacje i Błąd, jeśli element runbook wykonuje w nich operacje zapisywania.
5. Zamknij okienko strumieni i okienko zadania, aby wrócić do okienka MyFirstRunbook-Python.
6. Kliknij pozycję **Zadania**, aby otworzyć okienko zadań dla tego elementu Runbook. Zawiera ono listę wszystkich zadań utworzonych przez dany element Runbook. Ponieważ uruchomiono zadanie tylko raz, powinno być widoczne tylko jedno zadanie.
7. Możesz kliknąć to zadanie, aby otworzyć okienko Zadania wyświetlone wcześniej po uruchomieniu elementu runbook. Dzięki temu możesz cofnąć się w czasie i wyświetlić szczegóły dowolnego zadania, które zostało utworzone dla określonego elementu Runbook.

## <a name="add-authentication-to-manage-azure-resources"></a>Dodawanie uwierzytelniania w celu zarządzania zasobami platformy Azure

Element runbook został przetestowany i opublikowany, ale jak do tej pory nie wykonuje on żadnych użytecznych czynności. Powinien zarządzać zasobami platformy Azure.
Aby zarządzać zasobami platformy Azure, skrypt musi dokonać uwierzytelnienia przy użyciu poświadczeń z konta usługi Automation. Aby ułatwić, można użyć [pakiet narzędzia usługi Azure Automation](https://github.com/azureautomation/azure_automation_utility) się ułatwić do uwierzytelniania i wchodzić w interakcje z zasobami platformy Azure.

> [!NOTE]
> Konto usługi Automation utworzonych za pomocą funkcji nazwy głównej usługi dla miejsca do uruchamiania jako certyfikat.
> Jeśli nie utworzono konta usługi automation przy użyciu jednostki usługi, można uwierzytelniać za pomocą metody opisanej w [uwierzytelnienia przy użyciu bibliotek zarządzania platformy Azure dla języka Python](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate).

1. Otwórz Edytor tekstów, klikając **Edytuj** w okienku MyFirstRunbook-Python.

2. Dodaj następujący kod do uwierzytelniania na platformie Azure:

   ```python
   import os
   from azure.mgmt.compute import ComputeManagementClient
   import azure.mgmt.resource
   import automationassets

   def get_automation_runas_credential(runas_connection):
       from OpenSSL import crypto
       import binascii
       from msrestazure import azure_active_directory
       import adal

       # Get the Azure Automation RunAs service principal certificate
       cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
       pks12_cert = crypto.load_pkcs12(cert)
       pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())

       # Get run as connection information for the Azure Automation service principal
       application_id = runas_connection["ApplicationId"]
       thumbprint = runas_connection["CertificateThumbprint"]
       tenant_id = runas_connection["TenantId"]

       # Authenticate with service principal certificate
       resource ="https://management.core.windows.net/"
       authority_url = ("https://login.microsoftonline.com/"+tenant_id)
       context = adal.AuthenticationContext(authority_url)
       return azure_active_directory.AdalAuthentication(
       lambda: context.acquire_token_with_client_certificate(
               resource,
               application_id,
               pem_pkey,
               thumbprint)
       )

   # Authenticate to Azure using the Azure Automation RunAs service principal
   runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
   azure_credential = get_automation_runas_credential(runas_connection)
   ```

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Dodaj kod, aby utworzyć klienta Python obliczeń i uruchom maszynę Wirtualną

Aby pracować z maszyn wirtualnych platformy Azure, Utwórz wystąpienie obiektu [klienta usługi Azure Compute dla języka Python](https://docs.microsoft.com/python/api/azure.mgmt.compute.computemanagementclient?view=azure-python).

Uruchom maszynę Wirtualną za pomocą klienta obliczeń. Dodaj następujący kod do elementu runbook:

```python
# Initialize the compute management client with the RunAs credential and specify the subscription to work against.
compute_client = ComputeManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"])
)


print('\nStart VM')
async_vm_start = compute_client.virtual_machines.start(
    "MyResourceGroup", "TestVM")
async_vm_start.wait()
```

Gdzie _MyResourceGroup_ to nazwa grupy zasobów, która zawiera tę maszynę Wirtualną, a _TestVM_ to nazwa maszyny Wirtualnej, aby rozpocząć.

Przetestuj i uruchomić element runbook ponownie, aby zobaczyć, że uruchomieniu maszyny Wirtualnej.

## <a name="use-input-parameters"></a>Użyj parametrów wejściowych

Element runbook aktualnie używa zakodowanych wartości dla nazwy grupy zasobów i maszynę Wirtualną.
Teraz możemy dodać kod, który pobiera te wartości z parametrów wejściowych.

Możesz użyć `sys.argv` zmienną, aby uzyskać wartości parametrów.
Dodaj następujący kod do elementu runbook bezpośrednio po sobie `import` instrukcji:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Zaimportowane `sys` moduł i tworzy dwie zmienne do przechowywania nazwy grupy zasobów i maszynę Wirtualną.
Należy zauważyć, że element listy argumentów `sys.argv[0]`, jest nazwa skryptu i nie jest wprowadzane przez użytkownika.

Teraz można zmodyfikować ostatnie dwa wiersze elementu runbook, aby użyć wartości parametru wejściowego zamiast zakodowanych wartości:

```python
async_vm_start = compute_client.virtual_machines.start(
    resource_group_name, vm_name)
async_vm_start.wait()
```

Po uruchomieniu elementu runbook języka Python (albo na **testu** strony lub jako opublikowanego elementu runbook), można wprowadzić wartości parametrów w **uruchamianie elementu Runbook** strony w obszarze **parametry** .

Po rozpoczęciu wpisywania wartości w pierwszym polu, sekundy są wyświetlane i tak dalej, aby wprowadzić dowolną liczbę wartości parametrów, zgodnie z potrzebami.

Wartości są dostępne do skryptu jako `sys.argv` tablicy, jak w poniższym kodzie, który właśnie został dodany.

Wprowadź nazwę grupy zasobów jako wartość jako pierwszy parametr i nazwę maszyny Wirtualnej, aby uruchomić jako wartość drugiego parametru.

![Wprowadź wartości parametrów](media/automation-first-runbook-textual-python/runbook-python-params.png)

Kliknij przycisk **OK** uruchomienia elementu runbook. Element runbook uruchamia i uruchomieniu maszyny Wirtualnej, który określiłeś.

## <a name="next-steps"></a>Kolejne kroki

- Aby rozpocząć pracę z elementami Runbook programu PowerShell, zobacz artykuł [My first PowerShell runbook](automation-first-runbook-textual-powershell.md) (Mój pierwszy element Runbook programu PowerShell).
- Aby rozpocząć pracę z graficznymi elementami Runbook, zobacz artykuł [My first graphical runbook](automation-first-runbook-graphical.md) (Mój pierwszy graficzny element Runbook).
- Aby rozpocząć pracę z elementami Runbook przepływu pracy programu PowerShell, zobacz artykuł [My first PowerShell workflow runbook](automation-first-runbook-textual.md) (Mój pierwszy element Runbook przepływu pracy programu PowerShell).
- Aby dowiedzieć się więcej na temat typów elementów Runbook, ich zalet i ograniczeń, zobacz [Azure Automation runbook types](automation-runbook-types.md) (Typy elementów Runbook usługi Azure Automation).
- Aby dowiedzieć się więcej o tworzeniu aplikacji na platformie Azure przy użyciu języka Python, zobacz [platformy Azure dla deweloperów języka Python](https://docs.microsoft.com/python/azure/?view=azure-python)
- Aby wyświetlić przykładowe elementy runbook programu Python 2, zobacz [Azure Automation w witrynie GitHub](https://github.com/azureautomation/runbooks/tree/master/Utility/Python)
