---
title: Mój pierwszy element Runbook w języku Python w Azure Automation
description: Samouczek, który przeprowadzi Cię przez proces tworzenia, testowania i publikowania prostego elementu Runbook języka Python.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/19/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 54b008939b3d083769756b9ac3c3c9e3f7aebea5
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850282"
---
# <a name="my-first-python-runbook"></a>Mój pierwszy element Runbook w języku Python

> [!div class="op_single_selector"]
> - [Element graficzny](automation-first-runbook-graphical.md)
> - [Program PowerShell](automation-first-runbook-textual-powershell.md)
> - [Przepływ pracy programu PowerShell](automation-first-runbook-textual.md)
> - [Python](automation-first-runbook-textual-python2.md)

Ten samouczek przeprowadzi Cię przez proces tworzenia [elementu Runbook](automation-runbook-types.md#python-runbooks) w języku Python w Azure Automation. Zacznij od prostego elementu Runbook, który można testować i publikować. Następnie zmodyfikujesz element runbook, aby faktycznie zarządzał zasobami platformy Azure — w tym przypadku uruchamiając maszynę wirtualną platformy Azure. W końcu element Runbook jest bardziej niezawodny przez dodanie parametrów elementu Runbook.

> [!NOTE]
> Korzystanie z elementu webhook do uruchamiania elementów Runbook języka Python nie jest obsługiwane.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Konto usługi Automation](automation-offering-get-started.md) do przechowywania elementu Runbook i uwierzytelniania w zasobach platformy Azure. To konto musi mieć uprawnienia do uruchamiania i zatrzymywania maszyny wirtualnej.
- Maszyna wirtualna platformy Azure. Będziesz uruchamiać i zatrzymywać tę maszynę, dlatego należy użyć maszyny innej niż produkcyjna.

## <a name="create-a-new-runbook"></a>Utwórz nowy element Runbook

Zacznij od utworzenia prostego elementu Runbook, który będzie wyprowadzał tekst *Hello World*.

1. W witrynie Azure Portal otwórz konto usługi Automation.

    Strona konta usługi Automation umożliwia szybki przegląd zasobów na tym koncie. Konto powinno mieć już pewne elementy zawartości. Większość z nich to moduły, które są automatycznie uwzględniane na nowym koncie usługi Automation. Powinien istnieć również element zawartości Poświadczenie wymieniony w części dotyczącej [wymagań wstępnych](#prerequisites).<br>

1. Wybierz pozycję **Elementy runbook** w obszarze **ZARZĄDZANIE PROCESAMI**, aby otworzyć listę elementów runbook.
1. Wybierz pozycję **+ Dodaj element Runbook** , aby utworzyć nowy element Runbook.
1. Nadaj elementowi Runbook nazwę *MyFirstRunbook-Python*.
1. W takim przypadku należy utworzyć [element Runbook](automation-runbook-types.md#python-runbooks) w języku Python, aby wybrać opcję **Python 2** dla **typu elementu Runbook**.
1. Kliknij pozycję **Utwórz**, aby utworzyć element Runbook i otworzyć edytor tekstów.

## <a name="add-code-to-the-runbook"></a>Dodawanie kodu do elementu Runbook

Teraz można dodać proste polecenie do drukowania tekstu "Hello world":

```python
print("Hello World!")
```

Kliknij przycisk **Zapisz** , aby zapisać element Runbook.

## <a name="test-the-runbook"></a>Testowanie elementu runbook

Przed opublikowaniem elementu runbook w celu udostępnienia go w środowisku produkcyjnym warto go przetestować, aby upewnić się, że działa prawidłowo. Testowanie elementu Runbook polega na uruchomieniu jego **wersji roboczej** i interaktywnym przejrzeniu danych wyjściowych.

1. Kliknij pozycję **Okienko testowania**, aby otworzyć okienko testowania.
1. Kliknij opcję **Uruchom**, aby rozpocząć test. Powinna to być jedyna włączona opcja.
1. Zostanie utworzone [zadanie elementu Runbook](automation-runbook-execution.md) i pojawi się jego stan.
   Zadanie będzie miało początkowy stan *W kolejce*, wskazujący, że trwa oczekiwanie na udostępnienie procesu roboczego elementu runbook w chmurze. Przenosi się do *początku* , gdy proces roboczy pozyskuje zadanie, a następnie *uruchamia* się, gdy element Runbook faktycznie zacznie działać.
1. Po zakończeniu zadania elementu Runbook zostaną wyświetlone jego dane wyjściowe. W takim przypadku powinna zostać wyświetlona *Hello World*.
1. Zamknij okienko testowania, aby wrócić do kanwy.

## <a name="publish-and-start-the-runbook"></a>Publikowanie i Uruchamianie elementu Runbook

Utworzony element Runbook nadal działa w trybie roboczym. Należy ją opublikować przed uruchomieniem jej w środowisku produkcyjnym.
Po opublikowaniu elementu Runbook Zastąp istniejącą opublikowaną wersję wersją roboczą.
W takim przypadku nie masz jeszcze opublikowanej wersji, ponieważ element Runbook został właśnie utworzony.

1. Kliknij przycisk **Opublikuj**, aby opublikować element Runbook, a następnie kliknij opcję **Tak** po wyświetleniu monitu.
1. Jeśli przewiniesz w lewo, aby wyświetlić element Runbook w okienku **elementy Runbook** , zostanie wyświetlony **Stan tworzenia** **opublikowanych**.
1. Przewiń z powrotem w prawo, aby wyświetlić okienko dla **MyFirstRunbook-Python**.
   Opcje w górnej części umożliwiają nam Uruchamianie elementu Runbook, wyświetlanie elementu Runbook lub planowanie jego uruchomienia w przyszłości.
2. Chcesz uruchomić element Runbook, a następnie kliknij przycisk **Start** , a następnie kliknij przycisk **OK** , gdy zostanie otwarty blok Uruchamianie elementu Runbook.
3. Zostanie otwarte okienko zadań dla utworzonego zadania elementu Runbook. można zamknąć to okienko, ale w tym przypadku pozostawić otwarte, aby można było obserwować postęp zadania.
1. Stan zadania jest pokazywany w **podsumowaniu zadania** i jest zgodny z Stanami wyświetlonymi podczas testowania elementu Runbook.
2. Gdy stan elementu Runbook zmieni się na *Ukończono*, kliknij pozycję **Dane wyjściowe**. Zostanie otwarte okienko danych wyjściowych i będzie można zobaczyć *Hello World*.
3. Zamknij okienko danych wyjściowych.
4. Kliknij pozycję **Wszystkie dzienniki**, aby otworzyć okienko strumieni dla zadania elementu Runbook. W strumieniu danych wyjściowych powinien być widoczny tylko ciąg *Witaj, świecie*, ale mogą zostać wyświetlone inne strumienie zadania elementu runbook, takie jak Pełne informacje i Błąd, jeśli element runbook wykonuje w nich operacje zapisywania.
5. Zamknij okienko strumieni i okienko zadania, aby wrócić do okienka MyFirstRunbook-Python.
6. Kliknij pozycję **Zadania**, aby otworzyć okienko zadań dla tego elementu Runbook. Zawiera ono listę wszystkich zadań utworzonych przez dany element Runbook. Ponieważ uruchomiono zadanie tylko raz, powinno być widoczne tylko jedno zadanie.
7. Możesz kliknąć to zadanie, aby otworzyć okienko Zadania wyświetlone wcześniej po uruchomieniu elementu runbook. Dzięki temu możesz cofnąć się w czasie i wyświetlić szczegóły dowolnego zadania, które zostało utworzone dla określonego elementu Runbook.

## <a name="add-authentication-to-manage-azure-resources"></a>Dodawanie uwierzytelniania w celu zarządzania zasobami platformy Azure

Element runbook został przetestowany i opublikowany, ale jak do tej pory nie wykonuje on żadnych użytecznych czynności. Powinien zarządzać zasobami platformy Azure.
Aby zarządzać zasobami platformy Azure, skrypt musi uwierzytelniać się przy użyciu poświadczeń z konta usługi Automation. Aby ułatwić Ci korzystanie z zasobów platformy Azure, można użyć [pakietu narzędzi Azure Automation](https://github.com/azureautomation/azure_automation_utility) .

> [!NOTE]
> Konto usługi Automation musi zostać utworzone za pomocą funkcji głównej usługi, aby można było używać certyfikatu Uruchom jako.
> Jeśli konto usługi Automation nie zostało utworzone za pomocą nazwy głównej, można przeprowadzić uwierzytelnianie przy użyciu metody opisanej w temacie [uwierzytelnianie przy użyciu bibliotek zarządzania platformy Azure dla języka Python](/azure/python/python-sdk-azure-authenticate).

1. Otwórz Edytor tekstów, klikając pozycję **Edytuj** w okienku MyFirstRunbook-Python.

2. Dodaj następujący kod w celu uwierzytelnienia na platformie Azure:

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

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Dodaj kod, aby utworzyć klienta obliczeniowego języka Python i uruchomić maszynę wirtualną

Aby współpracować z maszynami wirtualnymi platformy Azure, Utwórz wystąpienie [klienta obliczeniowego platformy Azure dla języka Python](https://docs.microsoft.com/python/api/azure-mgmt-compute/azure.mgmt.compute.computemanagementclient).

Użyj klienta obliczeniowego, aby uruchomić maszynę wirtualną. Dodaj następujący kod do elementu Runbook:

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

Gdzie moja _resourceName_ jest nazwą grupy zasobów ZAWIERAJĄCEj maszynę wirtualną, a _TestVM_ to nazwa maszyny wirtualnej, która ma zostać uruchomiona.

Przetestuj i uruchom ponownie element Runbook, aby zobaczyć, że uruchamia maszynę wirtualną.

## <a name="use-input-parameters"></a>Użyj parametrów wejściowych

Element Runbook obecnie używa trwale zakodowanych wartości nazw grupy zasobów i maszyny wirtualnej.
Teraz Dodajmy kod, który pobiera te wartości z parametrów wejściowych.

Użyj zmiennej `sys.argv`, aby pobrać wartości parametrów.
Dodaj następujący kod do elementu Runbook bezpośrednio po innych instrukcjach `import`:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Spowoduje to zaimportowanie modułu `sys` i utworzenie dwóch zmiennych w celu przechowywania nazw grup zasobów i maszyn wirtualnych.
Zauważ, że element listy argumentów, `sys.argv[0]`, to nazwa skryptu i nie jest wprowadzany przez użytkownika.

Teraz można zmodyfikować ostatnie dwa wiersze elementu Runbook, aby używały wartości parametrów wejściowych zamiast używać zakodowanych wartości:

```python
async_vm_start = compute_client.virtual_machines.start(
    resource_group_name, vm_name)
async_vm_start.wait()
```

Po uruchomieniu elementu Runbook w języku Python (na stronie **test** lub jako opublikowanego elementu Runbook) można wprowadzić wartości parametrów na stronie **Uruchom element Runbook** w obszarze **Parametry**.

Po rozpoczęciu wprowadzania wartości w pierwszym polu zostanie wyświetlona druga sekunda, tak aby można było wprowadzić tyle wartości parametrów, ile jest to konieczne.

Wartości są dostępne dla skryptu jako tablica `sys.argv`, jak w kodzie, który właśnie został dodany.

Wprowadź nazwę grupy zasobów jako wartość pierwszego parametru i nazwę maszyny wirtualnej, która ma być uruchamiana jako wartość drugiego parametru.

![Wprowadź wartości parametrów](media/automation-first-runbook-textual-python/runbook-python-params.png)

Kliknij przycisk **OK** , aby uruchomić element Runbook. Element Runbook działa i uruchamia określoną maszynę wirtualną.

## <a name="error-handling-in-python"></a>Obsługa błędów w języku Python

Można również użyć poniższych konwencji do pobrania różnych strumieni z elementów Runbook języka Python, w tym **ostrzeżeń**, **błędów**i **debugowania** strumieni.

```python
print("Hello World output") 
print("ERROR: - Hello world error")
print("WARNING: - Hello world warning")
print("DEBUG: - Hello world debug")
print("VERBOSE: - Hello world verbose")
```

W poniższym przykładzie przedstawiono Konwencję używaną w bloku `try...except`.

```python
try:
    raise Exception('one', 'two')
except Exception as detail:
    print 'ERROR: Handling run-time error:', detail
```

> [!NOTE]
> **sys. stderr** nie jest obsługiwany w Azure Automation.

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć pracę z elementami Runbook programu PowerShell, zobacz artykuł [My first PowerShell runbook](automation-first-runbook-textual-powershell.md) (Mój pierwszy element Runbook programu PowerShell).
- Aby rozpocząć pracę z graficznymi elementami Runbook, zobacz artykuł [My first graphical runbook](automation-first-runbook-graphical.md) (Mój pierwszy graficzny element Runbook).
- Aby rozpocząć pracę z elementami Runbook przepływu pracy programu PowerShell, zobacz artykuł [My first PowerShell workflow runbook](automation-first-runbook-textual.md) (Mój pierwszy element Runbook przepływu pracy programu PowerShell).
- Aby dowiedzieć się więcej na temat typów elementów Runbook, ich zalet i ograniczeń, zobacz [Azure Automation runbook types](automation-runbook-types.md) (Typy elementów Runbook usługi Azure Automation).
- Aby dowiedzieć się więcej na temat tworzenia aplikacji dla platformy Azure przy użyciu języka Python, zobacz [Azure for Python Developers](/azure/python/)
- Aby wyświetlić Przykładowe elementy Runbook języka Python 2, zobacz [Azure Automation GitHub](https://github.com/azureautomation/runbooks/tree/master/Utility/Python)
