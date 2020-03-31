---
title: Uruchamianie elementu runbook usługi Azure Automation za pomocą elementu webhook
description: Element webhook, który umożliwia klientowi uruchomienie elementu runbook w usłudze Azure Automation z wywołania HTTP.  W tym artykule opisano sposób tworzenia elementu webhook i jak wywołać jeden, aby uruchomić element runbook.
services: automation
ms.subservice: process-automation
ms.date: 01/16/2020
ms.topic: conceptual
ms.openlocfilehash: 8cb641f95e7327e80f42df86a56eba8c34e7e598
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367027"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Uruchamianie elementu runbook usługi Azure Automation za pomocą elementu webhook

Element webhook umożliwia usługi zewnętrznej, aby uruchomić określonego elementu runbook w usłudze Azure Automation za pomocą jednego żądania HTTP. Usługi zewnętrzne obejmują usługi Azure DevOps Services, GitHub, dzienniki usługi Azure Monitor i aplikacje niestandardowe. Taka usługa może użyć elementu webhook do uruchomienia elementu runbook bez implementowania pełnego rozwiązania przy użyciu interfejsu API usługi Azure Automation. Elementy webhook można porównać z innymi metodami uruchamiania elementu runbook w [obszarze Uruchamianie elementu runbook w usłudze Azure Automation.](automation-starting-a-runbook.md)

> [!NOTE]
> Używanie elementu webhook do uruchamiania elementu runbook języka Python nie jest obsługiwane.

![WebhooksWygląd](media/automation-webhooks/webhook-overview-image.png)

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](automation-update-azure-modules.md)

## <a name="webhook-properties"></a>Właściwości elementu webhook

W poniższej tabeli opisano właściwości, które należy skonfigurować dla elementu webhook.

| Właściwość | Opis |
|:--- |:--- |
| Nazwa |Nazwa elementu webhook. Można podać dowolną nazwę, która ma, ponieważ nie jest narażony na klienta. Jest używany tylko do identyfikowania uruchomieniu w usłudze Azure Automation. Najlepszym rozwiązaniem należy nadać elementowi webhook nazwę powiązaną z klientem, który go używa. |
| Adres URL |Adres URL elementu webhook. Jest to unikatowy adres, który klient wywołuje z adresem HTTP POST, aby uruchomić element runbook połączony z elementem webhook. Jest generowany automatycznie podczas tworzenia elementu webhook. Nie można określić niestandardowego adresu URL. <br> <br> Adres URL zawiera token zabezpieczający, który umożliwia systemowi innej firmy wywoływanie żyła bez dalszego uwierzytelniania. Z tego powodu należy traktować adres URL jak hasło. Ze względów bezpieczeństwa można wyświetlić tylko adres URL w witrynie Azure portal podczas tworzenia elementu webhook. Zanotuj adres URL w bezpiecznej lokalizacji do wykorzystania w przyszłości. |
| Data wygaśnięcia | Data wygaśnięcia elementu webhook, po którym nie można go już używać. Datę wygaśnięcia można zmodyfikować po utworzeniu elementu webhook, o ile element webhook nie wygasł. |
| Enabled (Włączony) | Ustawienie wskazujące, czy element webhook jest domyślnie włączony podczas jego tworzenia. Jeśli ta właściwość jest ustawiona na Wyłączone, żaden klient nie może używać elementu webhook. Tę właściwość można ustawić podczas tworzenia elementu webhook lub innego czasu po jego utworzeniu. |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>Parametry używane podczas uruchamiania elementu runbook elementu webhook

Element webhook można zdefiniować wartości dla parametrów elementu runbook, które są używane podczas uruchamiania elementu runbook. Element webhook musi zawierać wartości dla wszystkich parametrów elementu runbook obowiązkowe i może zawierać wartości dla parametrów opcjonalnych. Wartość parametru skonfigurowana do elementu webhook można modyfikować nawet po utworzeniu elementu webhook. Wiele zestawów webhook połączonych z jednym elementem runbook może używać różnych wartości parametrów elementu runbook. Gdy klient uruchamia element runbook przy użyciu elementu webhook, nie można zastąpić wartości parametrów zdefiniowanych w elementów webhook.

Aby odbierać dane od klienta, projekt runbook obsługuje pojedynczy parametr o nazwie `WebhookData`. Ten parametr definiuje obiekt zawierający dane, które klient zawiera w żądaniu POST.

![Właściwości webhookData](media/automation-webhooks/webhook-data-properties.png)

Parametr `WebhookData` ma następujące właściwości:

| Właściwość | Opis |
|:--- |:--- |
| `WebhookName` | Nazwa elementu webhook. |
| `RequestHeader` | Tabela skrótu zawierająca nagłówki przychodzącego żądania POST. |
| `RequestBody` | Treść przychodzącego żądania POST. Ta treść zachowuje wszelkie formatowanie danych, takie jak ciąg, JSON, XML lub zakodowane w formularzu. Księga runbook musi być zapisana do pracy z formatem danych, który jest oczekiwany. |

Nie ma konfiguracji elementu webhook wymagane do `WebhookData` obsługi parametru, a element runbook nie jest wymagane, aby go zaakceptować. Jeśli projekt runbook nie definiuje parametru, wszelkie szczegóły żądania wysłanego z klienta są ignorowane.

> [!NOTE]
> Podczas wywoływania elementu webhook klient powinien zawsze przechowywać wartości parametrów w przypadku niepowodzenia wywołania. Jeśli występuje awaria sieci lub problem z połączeniem, aplikacja nie może pobrać nieudanych wywołań elementu webhook.

Jeśli określisz wartość `WebhookData` dla tworzenia elementu webhook, jest zastępowane, gdy element webhook uruchamia element runbook z danymi z żądania post klienta. Dzieje się tak, nawet jeśli aplikacja nie zawiera żadnych danych w treści żądania. 

Po uruchomieniu elementu runbook, `WebhookData` który definiuje przy użyciu mechanizmu innego niż `WebhookData` element webhook, można podać wartość, że element runbook rozpoznaje. Ta wartość powinna być obiektem o `WebhookData` takich [samych właściwościach](#webhook-properties) jak parametr, aby element `WebhookData` runbook mógł z nim współpracować, tak jak działa z rzeczywistymi obiektami przekazywanymi przez element webhook.

Na przykład jeśli uruchamiasz następujący element runbook z witryny Azure portal i chcesz przekazać niektóre przykładowe dane elementu webhook do testowania, należy przekazać dane w JSON w interfejsie użytkownika.

![Parametr WebhookData z interfejsu użytkownika](media/automation-webhooks/WebhookData-parameter-from-UI.png)

W następnym przykładzie elementu runbook zdefiniujmy następujące właściwości dla: `WebhookData`

* **Nazwa elementu Webhook :** MyWebhook
* **RequestBody**:`*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

Teraz przekazujemy następujący obiekt JSON w `WebhookData` interfejsie użytkownika dla parametru. W tym przykładzie z powrotami karetki i znaki nowego śunku, pasuje do formatu, który jest przekazywany z elementu webhook.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Uruchom parametr WebhookData z interfejsu użytkownika](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Usługa Azure Automation rejestruje wartości wszystkich parametrów wejściowych za pomocą zadania zestawu runbook. W związku z tym wszelkie dane wejściowe dostarczone przez klienta w żądaniu elementu webhook jest rejestrowany i dostępny dla każdego, kto ma dostęp do zadania automatyzacji. Z tego powodu należy zachować ostrożność w dołączaniu poufnych informacji w połączeniach webhook.

## <a name="webhook-security"></a>Zabezpieczenia elementu webhook

Zabezpieczenia elementu webhook opiera się na prywatność jego adres URL, który zawiera token zabezpieczający, który umożliwia webhook do wywołania. Usługa Azure Automation nie wykonuje żadnych uwierzytelniania na żądanie, tak długo, jak jest ono wykonane do poprawnego adresu URL. Z tego powodu klienci nie należy używać elementów webhook dla elementów runbook, które wykonują bardzo poufne operacje bez użycia alternatywnych środków sprawdzania poprawności żądania.

Można dołączyć logikę w liczebie, aby ustalić, czy jest wywoływana przez element webhook. Sprawdź `WebhookName` właściwość parametru. `WebhookData` Elementu runbook można wykonać dalsze sprawdzanie poprawności, szukając określonych informacji w `RequestHeader` i `RequestBody` właściwości.

Inną strategią jest, aby element runbook wykonać niektóre sprawdzania poprawności warunku zewnętrznego, gdy odbiera żądanie elementu webhook. Rozważmy na przykład runbook, który jest wywoływany przez GitHub za każdym razem, gdy istnieje nowe zatwierdzenie do repozytorium GitHub. Grupa runbook może połączyć się z gitHub, aby sprawdzić, czy nowe zatwierdzenie wystąpiło przed kontynuowaniem.

## <a name="creating-a-webhook"></a>Tworzenie elementu webhook

Poniższa procedura służy do tworzenia nowego elementu webhook połączonego z elementem runbook w witrynie Azure portal.

1. Na stronie Runbooks w witrynie Azure portal kliknij element runbook, który rozpoczyna element webhook, aby wyświetlić szczegóły elementu runbook. Upewnij się, że pole **stanowy** jest ustawione na **Opublikowane**.
2. Kliknij **pozycję Element webhook** u góry strony, aby otworzyć stronę Dodaj element webhook.
3. Kliknij **pozycję Utwórz nowy element webhook,** aby otworzyć stronę Tworzenie elementu Webhook.
4. Wypełnij pola **Nazwa** i **Data wygaśnięcia** elementu webhook i określ, czy ma być włączony. Zobacz [właściwości elementu webhook,](#webhook-properties) aby uzyskać więcej informacji na temat tych właściwości.
5. Kliknij ikonę kopiowania i naciśnij klawisze Ctrl+C, aby skopiować adres URL elementu webhook. Następnie nagraj go w bezpiecznym miejscu. 

    > [!NOTE]
    > Po utworzeniu elementu webhook nie można ponownie pobrać adresu URL.

   ![Webhook URL](media/automation-webhooks/copy-webhook-url.png)

1. Kliknij **przycisk Parametry,** aby podać wartości parametrów likmy. Jeśli element runbook ma parametry obowiązkowe, nie można utworzyć elementu webhook, chyba że podano wartości.
1. Kliknij pozycję **Utwórz**, aby utworzyć element webhook.

## <a name="using-a-webhook"></a>Korzystanie z elementu webhook

Aby użyć elementu webhook po jego utworzeniu, `POST` klient musi wydać żądanie HTTP z adresem URL elementu webhook. Składnia jest następująca:

```http
http://<Webhook Server>/token?=<Token Value>
```

Klient otrzymuje jeden z następujących kodów zwrotnych z `POST` żądania.

| Code | Tekst | Opis |
|:--- |:--- |:--- |
| 202 |Zaakceptowane |Żądanie zostało zaakceptowane, a projekt runbook został pomyślnie w kolejce. |
| 400 |Nieprawidłowe żądanie |Wniosek nie został przyjęty z jednego z następujących powodów: <ul> <li>Element webhook wygasł.</li> <li>Element webhook jest wyłączony.</li> <li>Token w adresie URL jest nieprawidłowy.</li>  </ul> |
| 404 |Nie znaleziono |Wniosek nie został przyjęty z jednego z następujących powodów: <ul> <li>Nie znaleziono elementu webhook.</li> <li>Nie znaleziono podręcznika runbooka.</li> <li>Nie znaleziono konta.</li>  </ul> |
| 500 |Błąd serwera wewnętrznego |Adres URL był prawidłowy, ale wystąpił błąd. Prosimy o ponowne przesłanie wniosku. |

Zakładając, że żądanie zakończy się pomyślnie, odpowiedź elementu webhook zawiera identyfikator zadania w formacie JSON, jak pokazano poniżej. Zawiera jeden identyfikator zadania, ale format JSON pozwala na potencjalne przyszłe ulepszenia.

```json
{"JobIds":["<JobId>"]}
```

Klient nie może określić, kiedy zadanie elementu runbook zostanie ukończone lub jego stan ukończenia z elementu webhook. Można znaleźć te informacje przy użyciu identyfikatora zadania z innym mechanizmem, takim jak [Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjob) lub [Azure Automation API](/rest/api/automation/job).

## <a name="renewing-a-webhook"></a><a name="renew-webhook"></a>Odnawianie elementu webhook

Po utworzeniu elementu webhook ma okres ważności dziesięciu lat, po którym automatycznie wygasa. Po wygaśnięciu elementu webhook nie można go ponownie aktywować. Można go tylko usunąć, a następnie ponownie utworzyć. 

Można rozszerzyć element webhook, który nie osiągnął czasu wygaśnięcia. Aby rozszerzyć element webhook:

1. Przejdź do elementu runbook zawierającego element webhook. 
2. Wybierz **pozycję Webhooks** w obszarze **Zasoby**. 
3. Kliknij element webhook, który chcesz rozszerzyć. 
4. Na stronie Webhook wybierz nową datę i godzinę wygaśnięcia i kliknij przycisk **Zapisz**.

## <a name="sample-runbook"></a>Przykładowy podręcznik runbook

Poniższy przykładowy element runbook akceptuje dane elementu webhook i uruchamia maszyny wirtualne określone w treści żądania. Aby przetestować ten projekt umie ,na koncie automatyzacji w obszarze **Elementy runbook**kliknij pozycję **Utwórz system runbook**. Jeśli nie wiesz, jak utworzyć system runbook, zobacz [Tworzenie uruchomieniu](automation-quickstart-create-runbook.md). .

> [!NOTE]
> W przypadku niegraficzowych wiązków czągo programu PowerShell `Add-AzAccount` i `Add-AzureRMAccount` aliasów [connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Można użyć tych poleceń cmdlet lub można [zaktualizować moduły](automation-update-azure-modules.md) na koncie automatyzacji do najnowszych wersji. Może być konieczne zaktualizowanie modułów, nawet jeśli właśnie utworzono nowe konto automatyzacji.

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData
)

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

    # Check header for message to validate request
    if ($WebhookData.RequestHeader.message -eq 'StartedbyContoso')
    {
        Write-Output "Header has required information"}
    else
    {
        Write-Output "Header missing required information";
        exit;
    }

    # Retrieve VMs from Webhook request body
    $vms = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.

    Write-Output "Authenticating to Azure with service principal and certificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    Write-Output "Get connection asset: $ConnectionAssetName"

    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null)
            {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Write-Output "Authenticating to Azure with service principal."
            Add-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Output

        # Start each virtual machine
        foreach ($vm in $vms)
        {
            $vmName = $vm.Name
            Write-Output "Starting $vmName"
            Start-AzVM -Name $vm.Name -ResourceGroup $vm.ResourceGroup
        }
}
else {
    # Error
    write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="testing-the-sample"></a>Badanie próbki

W poniższym przykładzie użyto programu Windows PowerShell do uruchomienia elementu runbook z elementem webhook. Każdy język, który może złożyć żądanie HTTP, może użyć elementu webhook. Program Windows PowerShell jest używany w tym miejscu jako przykład.

Projekt runbook oczekuje listy maszyn wirtualnych sformatowanych w JSON w treści żądania. Element runbook sprawdza również, że nagłówki zawierają zdefiniowany komunikat, aby sprawdzić, czy wywoływacz elementu webhook jest prawidłowy.

```azurepowershell-interactive
$uri = "<webHook Uri>"

$vms  = @(
            @{ Name="vm01";ResourceGroup="vm01"},
            @{ Name="vm02";ResourceGroup="vm02"}
        )
$body = ConvertTo-Json -InputObject $vms
$header = @{ message="StartedbyContoso"}
$response = Invoke-WebRequest -Method Post -Uri $uri -Body $body -Headers $header
$jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
```

W poniższym przykładzie przedstawiono treść żądania, które `RequestBody` jest `WebhookData`dostępne dla elementów runbook we właściwości . Ta wartość jest sformatowana w JSON, aby była zgodna z formatem zawartym w treści żądania.

```json
[
    {
        "Name":  "vm01",
        "ResourceGroup":  "myResourceGroup"
    },
    {
        "Name":  "vm02",
        "ResourceGroup":  "myResourceGroup"
    }
]
```

Na poniższej ilustracji przedstawiono żądanie wysyłane z programu Windows PowerShell i wynikowy odpowiedzi. Identyfikator zadania jest wyodrębniany z odpowiedzi i konwertowany na ciąg.

![Przycisk elementów webhook](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak korzystać z usługi Azure Automation do podejmowania działań w alertach platformy Azure, zobacz [Wyzwalanie uruchomieniu bieczku usługi Azure Automation za pomocą alertów platformy Azure.](automation-create-alert-triggered-runbook.md)
