---
title: Mój pierwszy Python runbook automatyzacji Azure
description: Samouczek, który przeprowadzi Cię przez tworzenie, testowanie i publikowania elementu runbook prostego języka Python.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cfeef05df63cd08db48d555d7ca6f12969b533cb
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="my-first-python-runbook"></a>Moje pierwszego elementu runbook języka Python

> [!div class="op_single_selector"]
> * [Element graficzny](automation-first-runbook-graphical.md)
> * [Program PowerShell](automation-first-runbook-textual-powershell.md)
> * [Przepływ pracy programu PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 
> 

Ten samouczek przedstawia tworzenie [Python runbook](automation-runbook-types.md#python-runbooks) automatyzacji Azure. Rozpoczyna prosty element runbook, testowania i publikowania. Następnie zmodyfikujesz element runbook, aby faktycznie zarządzał zasobami platformy Azure — w tym przypadku uruchamiając maszynę wirtualną platformy Azure. Ponadto należy elementu runbook bardziej niezawodne, dodając parametry elementu runbook.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](automation-offering-get-started.md) do przechowywania elementu Runbook i uwierzytelniania w zasobach platformy Azure. To konto musi mieć uprawnienia do uruchamiania i zatrzymywania maszyny wirtualnej.
* Maszyna wirtualna platformy Azure. Będziesz uruchamiać i zatrzymywać tę maszynę, dlatego należy użyć maszyny innej niż produkcyjna.

## <a name="create-a-new-runbook"></a>Utwórz nowy element runbook

Rozpocznij od utworzenia prosty element runbook, która wyświetla tekst *Hello World*.

1. W witrynie Azure Portal otwórz konto usługi Automation.

    Strona konta usługi Automation umożliwia szybki przegląd zasobów na tym koncie. Konto powinno mieć już pewne elementy zawartości. Większość z nich to moduły, które są automatycznie uwzględniane na nowym koncie usługi Automation. Powinien istnieć również element zawartości Poświadczenie wymieniony w części dotyczącej [wymagań wstępnych](#prerequisites).<br>

1. Wybierz pozycję **Elementy runbook** w obszarze **ZARZĄDZANIE PROCESAMI**, aby otworzyć listę elementów runbook.
2. Wybierz **+ Dodaj element runbook** Aby utworzyć nowy element runbook.
3. Nadaj nazwę elementu runbook *MyFirstRunbook Python*.
4. W takim przypadku użytkownik chce utworzyć [Python runbook](automation-runbook-types.md#python-runbooks) tak wybierz **Python 2** dla **typ elementu Runbook**.
5. Kliknij pozycję **Utwórz**, aby utworzyć element Runbook i otworzyć edytor tekstów.

## <a name="add-code-to-the-runbook"></a>Dodawanie kodu do elementu runbook

Teraz możesz dodać prostego polecenia, aby wydrukować tekst "Hello World":

```python
print("Hello World!")
```

Kliknij przycisk **zapisać** można zapisać elementu runbook.

## <a name="test-the-runbook"></a>Testowanie elementu runbook

Przed opublikowaniem elementu runbook w celu udostępnienia go w środowisku produkcyjnym warto go przetestować, aby upewnić się, że działa prawidłowo. Testowanie elementu Runbook polega na uruchomieniu jego **wersji roboczej** i interaktywnym przejrzeniu danych wyjściowych.

1. Kliknij pozycję **Okienko testowania**, aby otworzyć okienko testowania.
2. Kliknij opcję **Uruchom**, aby rozpocząć test. Powinna to być jedyna włączona opcja.
3. Zostanie utworzone [zadanie elementu Runbook](automation-runbook-execution.md) i pojawi się jego stan.
   Zadanie będzie miało początkowy stan *W kolejce*, wskazujący, że trwa oczekiwanie na udostępnienie procesu roboczego elementu runbook w chmurze. Powoduje przeniesienie do *uruchamianie* gdy pracownik oświadczeń zadania, a następnie *systemem* , gdy element runbook faktycznie zacznie działać.
4. Po zakończeniu zadania elementu Runbook zostaną wyświetlone jego dane wyjściowe. W takim przypadku powinien zostać wyświetlony *Hello World*.
5. Zamknij okienko testowania, aby wrócić do kanwy.

## <a name="publish-and-start-the-runbook"></a>Publikowanie i uruchamiania elementu runbook

Utworzony element runbook jest nadal w trybie wersji roboczej. Należy opublikować przed uruchomieniem jej w środowisku produkcyjnym.
Podczas publikowania elementu runbook można zastąpienie istniejącej opublikowanej wersji wersję roboczą.
W takim przypadku nie masz opublikowanej wersji jeszcze ponieważ właśnie utworzony element runbook.

1. Kliknij pozycję **Opublikuj**, aby opublikować element Runbook, a następnie kliknij pozycję **Tak** po wyświetleniu monitu.
2. Podczas przewijania po lewej, aby wyświetlić element runbook w programie **elementów Runbook** okienko teraz, będzie wyświetlana **stan pisania przyp** z **opublikowano**.
1. Prawo do wyświetlania w okienku przewiń **MyFirstRunbook Python**.
   Opcje w górnej części umożliwiają uruchamianie elementu Runbook, wyświetlanie elementu Runbook, planowanie jego uruchomienia w przyszłości lub utworzenie [elementu webhook](automation-webhooks.md) w celu umożliwienia uruchamiania za pośrednictwem wywołania HTTP.
2. Aby uruchomić element runbook, więc klikamy **Start** , a następnie kliknij przycisk **Ok** po otwarciu bloku Uruchom element Runbook.
3. Okienko zadania jest otwarty dla zadania elementu runbook, który został utworzony. Możesz zamknąć to okienko, ale w takim przypadku można zostawić otwarty, możesz obserwować postęp zadania.
4. Stan zadania jest wyświetlany w **Podsumowanie zadania** i odpowiada stany pokazaliśmy, gdy przetestować element runbook.
5. Gdy stanem elementu Runbook będzie *Ukończono*, kliknij pozycję **Dane wyjściowe**. W okienku danych wyjściowych jest otwarty, aby zobaczyć Twojej *Hello World*.
6. Zamknij okienko danych wyjściowych.
7. Kliknij pozycję **Wszystkie dzienniki**, aby otworzyć okienko strumieni dla zadania elementu Runbook. W strumieniu danych wyjściowych powinien być widoczny tylko ciąg *Witaj, świecie*, ale mogą zostać wyświetlone inne strumienie zadania elementu runbook, takie jak Pełne informacje i Błąd, jeśli element runbook wykonuje w nich operacje zapisywania.
8. Zamknij okienko strumieni i w okienku zadania, aby wrócić do okienka MyFirstRunbook Python.
9. Kliknij pozycję **Zadania**, aby otworzyć okienko zadań dla tego elementu Runbook. Zawiera ono listę wszystkich zadań utworzonych przez dany element Runbook. Ponieważ uruchomiono zadanie tylko raz, powinno być widoczne tylko jedno zadanie.
10. Możesz kliknąć to zadanie, aby otworzyć okienko Zadania wyświetlone wcześniej po uruchomieniu elementu runbook. Dzięki temu możesz cofnąć się w czasie i wyświetlić szczegóły dowolnego zadania, które zostało utworzone dla określonego elementu Runbook.

## <a name="add-authentication-to-manage-azure-resources"></a>Dodawanie uwierzytelniania do zarządzania zasobami Azure

Element runbook został przetestowany i opublikowany, ale jak do tej pory nie wykonuje on żadnych użytecznych czynności. Powinien zarządzać zasobami platformy Azure.
Do zarządzania zasobami Azure, skrypt musi przeprowadzić uwierzytelnianie przy użyciu poświadczeń z sieci [konto automatyzacji](automation-offering-get-started.md).

> [!NOTE]
> Konto automatyzacji musi być utworzony przy użyciu funkcji główną usługi dla niego, aby był certyfikat runas.
> Jeśli nie utworzono konto automatyzacji z nazwy głównej usługi, można uwierzytelniać za pomocą metody w sposób opisany w [Uwierzytelnij z bibliotekami zarządzania platformy Azure dla języka Python](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate).

1. Otwórz edytor tekstowy, klikając **Edytuj** w okienku MyFirstRunbook Python.
1. Dodaj następujący kod, aby uwierzytelniać na platformie Azure:
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

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Dodaj kod, aby utworzyć obliczeniowe Python klienta i uruchomić maszyny Wirtualnej

Aby pracować z maszyn wirtualnych platformy Azure, Utwórz wystąpienie [rozwiązań usługi obliczenia Azure klienta dla języka Python](https://docs.microsoft.com/python/api/azure.mgmt.compute.computemanagementclient?view=azure-python).

Korzystając z klienta obliczeniowe można uruchomić maszyny Wirtualnej. Dodaj następujący kod do elementu runbook:

```python
# Initialize the compute management client with the RunAs credential and specify the subscription to work against.
compute_client = ComputeManagementClient(
azure_credential,
  str(runas_connection["SubscriptionId"])
)


print('\nStart VM')
async_vm_start = compute_client.virtual_machines.start("MyResourceGroup", "TestVM")
async_vm_start.wait()
```

Gdzie _MyResourceGroup_ to nazwa grupy zasobów, która zawiera maszynę Wirtualną, a _TestVM_ jest nazwa maszyny wirtualnej chcesz uruchomić. 

Testowanie i uruchomić element runbook ponownie, aby zobaczyć, że rozpoczyna się maszyny Wirtualnej.

## <a name="use-input-parameters"></a>Użyj parametrów wejściowych

Element runbook używa obecnie zakodowanych wartości dla nazwy grupy zasobów i maszyny Wirtualnej.
Teraz możemy dodać kod, który pobiera te wartości z parametrów wejściowych.

Możesz użyć `sys.argv` zmienną, aby uzyskać wartości parametrów.
Dodaj następujący kod do elementu runbook natychmiast po drugim `import` instrukcji:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Zostaną zaimportowane `sys` modułu i tworzy dwie zmienne do przechowywania nazwy grupy zasobów i maszyny Wirtualnej.
Zwróć uwagę, że element listy argumentów `sys.argv[0]`, to nazwa skryptu i nie jest wejściem przez użytkownika.

Teraz można zmodyfikować elementu runbook, aby użyć wartości parametru wejściowego zamiast zakodowanych wartości ostatnie dwa wiersze:

```python
async_vm_start = compute_client.virtual_machines.start(resource_group_name, vm_name)
async_vm_start.wait()
```

Po uruchomieniu elementu runbook języka Python (na **testu** strony lub jako opublikowanego elementu runbook), można wprowadzić wartości dla parametrów w **Uruchom element Runbook** w obszarze **parametry** .

Po uruchomieniu wprowadzenie wartości w pierwszym polu, drugi wyświetlane i tak dalej, dzięki czemu w razie potrzeby można wprowadzić dowolną liczbę wartości parametrów.

Wartości są dostępne do skryptu jako `sys.argv` tablicy, tak jak kod, który właśnie został dodany.

Wprowadź nazwę grupy zasobów jako wartość parametru pierwszy i Nazwa maszyny Wirtualnej do uruchamiania jako wartość drugiego parametru.

![Wprowadź wartości parametrów](media/automation-first-runbook-textual-python/runbook-python-params.png)

Kliknij przycisk **OK** można uruchomić elementu runbook. Element runbook uruchamia i uruchamia wybraną maszynę Wirtualną.

## <a name="next-steps"></a>Kolejne kroki

* Aby rozpocząć pracę z elementami Runbook programu PowerShell, zobacz artykuł [My first PowerShell runbook](automation-first-runbook-textual-powershell.md) (Mój pierwszy element Runbook programu PowerShell).
* Aby rozpocząć pracę z graficznymi elementami Runbook, zobacz artykuł [My first graphical runbook](automation-first-runbook-graphical.md) (Mój pierwszy graficzny element Runbook).
* Aby rozpocząć pracę z elementami Runbook przepływu pracy programu PowerShell, zobacz artykuł [My first PowerShell workflow runbook](automation-first-runbook-textual.md) (Mój pierwszy element Runbook przepływu pracy programu PowerShell).
* Aby dowiedzieć się więcej na temat typów elementów Runbook, ich zalet i ograniczeń, zobacz [Azure Automation runbook types](automation-runbook-types.md) (Typy elementów Runbook usługi Azure Automation).
* Aby dowiedzieć się więcej o tworzeniu aplikacji Azure z języka Python, zobacz [Azure dla deweloperów języka Python](https://docs.microsoft.com/python/azure/?view=azure-python).
* Aby wyświetlić przykładowe elementy runbook 2 języka Python, zobacz [GitHub automatyzacji Azure](https://docs.microsoft.com/python/azure/?view=azure-python).
