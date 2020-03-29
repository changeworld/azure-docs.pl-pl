---
title: Mój pierwszy system runbook języka Python w usłudze Azure Automation
description: Samouczek, który przeprowadzi Cię przez tworzenie, testowanie i publikowanie prostego elementu runbook języka Python.
services: automation
ms.subservice: process-automation
ms.date: 03/19/2019
ms.topic: conceptual
ms.openlocfilehash: b17a0403a3b2a3ff8c3586ed26a4b833db54922d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75365941"
---
# <a name="my-first-python-runbook"></a>Mój pierwszy element Runbook (Python)

> [!div class="op_single_selector"]
> - [Element graficzny](automation-first-runbook-graphical.md)
> - [Powershell](automation-first-runbook-textual-powershell.md)
> - [Przepływ pracy programu PowerShell](automation-first-runbook-textual.md)
> - [Python](automation-first-runbook-textual-python2.md)

W tym samouczku przeprowadzi Cię tworzenie [elementu runbook języka Python](automation-runbook-types.md#python-runbooks) w usłudze Azure Automation. Zaczynasz od prostego śmiętu, który testujesz i publikujesz. Następnie zmodyfikujesz element runbook, aby faktycznie zarządzał zasobami platformy Azure — w tym przypadku uruchamiając maszynę wirtualną platformy Azure. Na koniec należy uczynić system runbook bardziej niezawodne przez dodanie parametrów uruchomieniu. ś.00.

> [!NOTE]
> Używanie elementu webhook do uruchamiania elementu runbook języka Python nie jest obsługiwane.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Konto usługi Automation](automation-offering-get-started.md) do przechowywania elementu Runbook i uwierzytelniania w zasobach platformy Azure. To konto musi mieć uprawnienia do uruchamiania i zatrzymywania maszyny wirtualnej.
- Maszyna wirtualna platformy Azure. Będziesz uruchamiać i zatrzymywać tę maszynę, dlatego należy użyć maszyny innej niż produkcyjna.

## <a name="create-a-new-runbook"></a>Tworzenie nowego śmiętu

Zacznij od utworzenia prostego podstawowego podstawowego podstawowego tekstu *Hello World*.

1. W witrynie Azure Portal otwórz konto usługi Automation.

    Strona konta usługi Automation umożliwia szybki przegląd zasobów na tym koncie. Konto powinno mieć już pewne elementy zawartości. Większość z nich to moduły, które są automatycznie uwzględniane na nowym koncie usługi Automation. Powinien istnieć również element zawartości Poświadczenie wymieniony w części dotyczącej [wymagań wstępnych](#prerequisites).<br>

1. Wybierz pozycję **Elementy runbook** w obszarze **ZARZĄDZANIE PROCESAMI**, aby otworzyć listę elementów runbook.
1. Wybierz **+ Dodaj projekt runbook,** aby utworzyć nowy projekt runbook.
1. Nadaj projektowi uszytemu nazwę *MyFirstRunbook-Python*.
1. W takim przypadku zamierzasz utworzyć [projekt owy Pythona,](automation-runbook-types.md#python-runbooks) więc wybierz **Python 2** dla **typu runbook**.
1. Kliknij pozycję **Utwórz**, aby utworzyć element Runbook i otworzyć edytor tekstów.

## <a name="add-code-to-the-runbook"></a>Dodawanie kodu do podręcznika runbooka

Teraz dodajesz proste polecenie, aby wydrukować tekst "Hello World":

```python
print("Hello World!")
```

Kliknij **przycisk Zapisz,** aby zapisać projekt runbook.

## <a name="test-the-runbook"></a>Testowanie elementu runbook

Przed opublikowaniem elementu runbook w celu udostępnienia go w środowisku produkcyjnym warto go przetestować, aby upewnić się, że działa prawidłowo. Testowanie elementu Runbook polega na uruchomieniu jego **wersji roboczej** i interaktywnym przejrzeniu danych wyjściowych.

1. Kliknij pozycję **Okienko testowania**, aby otworzyć okienko testowania.
1. Kliknij opcję **Uruchom**, aby rozpocząć test. Powinna to być jedyna włączona opcja.
1. Zostanie utworzone [zadanie elementu Runbook](automation-runbook-execution.md) i pojawi się jego stan.
   Zadanie będzie miało początkowy stan *W kolejce*, wskazujący, że trwa oczekiwanie na udostępnienie procesu roboczego elementu runbook w chmurze. Przenosi się do *Uruchamianie,* gdy pracownik żąda zadania, a następnie *uruchomiony,* gdy runbook faktycznie rozpoczyna się uruchomiony.
1. Po zakończeniu zadania elementu Runbook zostaną wyświetlone jego dane wyjściowe. W takim przypadku powinieneś zobaczyć *Hello World*.
1. Zamknij okienko testowania, aby wrócić do kanwy.

## <a name="publish-and-start-the-runbook"></a>Publikowanie i uruchamianie podręcznika runbook

Utworzony projekt runbook jest nadal w trybie roboczym. Należy go opublikować, zanim będzie można uruchomić go w produkcji.
Podczas publikowania likrządu należy zastąpić istniejącą opublikowaną wersję wersją wersji roboczej.
W takim przypadku nie masz jeszcze opublikowanej wersji, ponieważ właśnie utworzono projekt runbook.

1. Kliknij przycisk **Opublikuj**, aby opublikować element Runbook, a następnie kliknij opcję **Tak** po wyświetleniu monitu.
1. Jeśli przewiniesz w lewo, aby wyświetlić element runbook w okienku **Runbooks** teraz, pokazuje **stan tworzenia** **opublikowanych**.
1. Przewiń z powrotem w prawo, aby wyświetlić okienko **myfirstRunbook-Python**.
   Opcje w górnej części pozwalają nam uruchomić runbook, wyświetlić runbook, lub zaplanować jego uruchomienie w pewnym momencie w przyszłości.
2. Chcesz uruchomić projekt runbook, więc kliknij przycisk **Start,** a następnie kliknij przycisk **Ok** po otwarciu bloku Rozpocznij program Runbook.
3. Okienko zadania jest otwierane dla utworzonego zadania ego. Możesz zamknąć to okienko, ale w tym przypadku pozostawiasz je otwarte, aby można było obserwować postęp zadania.
1. Stan zadania jest wyświetlany w **podsumowaniu zadania** i odpowiada stanom, które zostały wyświetlone podczas testowania śmiwoja.
2. Gdy stanem elementu Runbook będzie *Ukończono*, kliknij pozycję **Dane wyjściowe**. Okienko Dane wyjściowe zostanie otwarte i będzie można zobaczyć swój *Hello World*.
3. Zamknij okienko danych wyjściowych.
4. Kliknij pozycję **Wszystkie dzienniki**, aby otworzyć okienko strumieni dla zadania elementu Runbook. W strumieniu danych wyjściowych powinien być widoczny tylko ciąg *Witaj, świecie*, ale mogą zostać wyświetlone inne strumienie zadania elementu runbook, takie jak Pełne informacje i Błąd, jeśli element runbook wykonuje w nich operacje zapisywania.
5. Zamknij okienko Strumienie i okienko zadania, aby powrócić do okienka MyFirstRunbook-Python.
6. Kliknij pozycję **Zadania**, aby otworzyć okienko zadań dla tego elementu Runbook. Zawiera ono listę wszystkich zadań utworzonych przez dany element Runbook. Ponieważ uruchomiono zadanie tylko raz, powinno być widoczne tylko jedno zadanie.
7. Możesz kliknąć to zadanie, aby otworzyć okienko Zadania wyświetlone wcześniej po uruchomieniu elementu runbook. Dzięki temu możesz cofnąć się w czasie i wyświetlić szczegóły dowolnego zadania, które zostało utworzone dla określonego elementu Runbook.

## <a name="add-authentication-to-manage-azure-resources"></a>Dodawanie uwierzytelniania w celu zarządzania zasobami platformy Azure

Element runbook został przetestowany i opublikowany, ale jak do tej pory nie wykonuje on żadnych użytecznych czynności. Powinien zarządzać zasobami platformy Azure.
Aby zarządzać zasobami platformy Azure, skrypt musi uwierzytelnić się przy użyciu poświadczeń z konta automatyzacji. Aby ci pomóc, możesz użyć [pakietu narzędzi usługi Azure Automation,](https://github.com/azureautomation/azure_automation_utility) aby ułatwić uwierzytelnianie zasobów platformy Azure i interakcję z nimi.

> [!NOTE]
> Konto automatyzacji musi zostać utworzone przy za pomocą funkcji głównej usługi, aby można było uruchomić jako certyfikat.
> Jeśli konto automatyzacji nie zostało utworzone z jednostką usługi, można uwierzytelnić się przy użyciu metody opisanej w [uwierzytelniij za pomocą bibliotek zarządzania platformy Azure dla języka Python.](/azure/python/python-sdk-azure-authenticate)

1. Otwórz edytor tekstu, klikając przycisk **Edytuj** w okienku MyFirstRunbook-Python.

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

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Dodaj kod, aby utworzyć klienta obliczeniowego języka Python i uruchomić maszynę wirtualną

Aby pracować z maszynami wirtualnymi platformy Azure, utwórz [wystąpienie klienta obliczeń platformy Azure dla języka Python](https://docs.microsoft.com/python/api/azure-mgmt-compute/azure.mgmt.compute.computemanagementclient).

Użyj klienta obliczeniowego, aby uruchomić maszynę wirtualną. Dodaj następujący kod do elementów runbook:

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

Gdzie _MyResourceGroup_ jest nazwą grupy zasobów, która zawiera maszynę wirtualną, a _TestVM_ jest nazwą maszyny Wirtualnej, którą chcesz uruchomić.

Przetestuj i uruchom runbook ponownie, aby zobaczyć, że uruchamia maszynę wirtualną.

## <a name="use-input-parameters"></a>Użyj parametrów wejściowych

Księga runbooka używa obecnie zakodowanych wartości dla nazw grupy zasobów i maszyny wirtualnej.
Teraz dodajmy kod, który pobiera te wartości z parametrów wejściowych.

Zmienna `sys.argv` służy do uzyskania wartości parametrów.
Dodaj następujący kod do elementów runbook natychmiast po innych `import` instrukcji:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Spowoduje to `sys` zaimportowanie modułu i utworzenie dwóch zmiennych do przechowywania nazw grupy zasobów i maszyn wirtualnych.
Należy zauważyć, że element `sys.argv[0]`listy argumentów, , jest nazwą skryptu i nie jest wprowadzany przez użytkownika.

Teraz możesz zmodyfikować dwa ostatnie wiersze żytnika, aby używać wartości parametrów wejściowych zamiast wartości zakodowanych na twardo:

```python
async_vm_start = compute_client.virtual_machines.start(
    resource_group_name, vm_name)
async_vm_start.wait()
```

Po uruchomieniu ślikmy python (na stronie **testowej** lub jako opublikowany projekt runbook) można wprowadzić wartości parametrów na stronie **Rozpocznij runbook** w obszarze **Parametry**.

Po rozpoczęciu wprowadzania wartości w pierwszym polu pojawi się druga i tak dalej, dzięki czemu można wprowadzić dowolną liczbę wartości parametrów.

Wartości są dostępne dla skryptu jako tablicy, `sys.argv` jak w kodzie, który właśnie dodano.

Wprowadź nazwę grupy zasobów jako wartość pierwszego parametru i nazwę maszyny Wirtualnej, aby rozpocząć jako wartość drugiego parametru.

![Wprowadzanie wartości parametrów](media/automation-first-runbook-textual-python/runbook-python-params.png)

Kliknij **przycisk OK,** aby uruchomić program runbook. Projekt runbook uruchamia i uruchamia określoną maszynę wirtualną.

## <a name="error-handling-in-python"></a>Obsługa błędów w pythonie

Można również użyć następujących konwencji do pobierania różnych strumieni z elementów runbook języka Python, w tym **OSTRZEŻENIE**, **BŁĄD**i **debug** strumieni.

```python
print("Hello World output") 
print("ERROR: - Hello world error")
print("WARNING: - Hello world warning")
print("DEBUG: - Hello world debug")
print("VERBOSE: - Hello world verbose")
```

Poniższy przykład pokazuje tę `try...except` konwencję używaną w bloku.

```python
try:
    raise Exception('one', 'two')
except Exception as detail:
    print 'ERROR: Handling run-time error:', detail
```

> [!NOTE]
> **sys.stderr** nie jest obsługiwany w usłudze Azure Automation.

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć pracę z podręcznikami runbook programu PowerShell, zobacz [Mój pierwszy program PowerShell runbook](automation-first-runbook-textual-powershell.md)
- Aby rozpocząć pracę z graficznymi elementami Runbook, zobacz artykuł [My first graphical runbook](automation-first-runbook-graphical.md) (Mój pierwszy graficzny element Runbook).
- Aby rozpocząć pracę z uruchomieniu przepływem pracy programu PowerShell, zobacz [Mój pierwszy program PowerShell workflow runbook](automation-first-runbook-textual.md)
- Aby dowiedzieć się więcej na temat typów elementów Runbook, ich zalet i ograniczeń, zobacz [Azure Automation runbook types](automation-runbook-types.md) (Typy elementów Runbook usługi Azure Automation).
- Aby dowiedzieć się więcej o tworzeniu platformy Azure za pomocą języka Python, zobacz [Azure for Python developers](/azure/python/)
- Aby wyświetlić przykładowe elementy runbook języka Python 2, zobacz [usługę Azure Automation GitHub](https://github.com/azureautomation/runbooks/tree/master/Utility/Python)
