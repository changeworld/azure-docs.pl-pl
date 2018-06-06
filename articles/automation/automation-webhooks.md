---
title: Uruchamianie elementu runbook usługi Automatyzacja Azure z elementu webhook
description: Element webhook, która umożliwia klientowi uruchomienia elementu runbook automatyzacji Azure z wywołania HTTP.  W tym artykule opisano sposób tworzenia elementu webhook i wywoływanie jednego uruchomienia elementu runbook.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 06/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e047dffa86915b0cd6e8829ea27e0335e7f88cb2
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757160"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Uruchamianie elementu runbook usługi Automatyzacja Azure z elementu webhook

A *webhook* umożliwia uruchomienie określonego elementu runbook automatyzacji Azure za pomocą pojedynczego żądania HTTP. Dzięki temu usług zewnętrznych, takich jak Visual Studio Team Services, GitHub, usługi Analiza dzienników Azure lub niestandardowych aplikacji do uruchamiania elementów runbook bez wdrażania pełnego rozwiązania przy użyciu interfejsu API usługi Automatyzacja Azure.  
![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

Można porównać elementów webhook do innych metod uruchamianie elementu runbook [uruchamianie elementu runbook automatyzacji Azure](automation-starting-a-runbook.md)

## <a name="details-of-a-webhook"></a>Szczegóły elementu webhook

W poniższej tabeli opisano właściwości, które należy skonfigurować dla elementu webhook.

| Właściwość | Opis |
|:--- |:--- |
| Name (Nazwa) |Możesz podać dowolną nazwę wybranego dla elementu webhook, ponieważ to nie jest widoczne dla klientów. Tylko służy do należy do identyfikacji elementu runbook automatyzacji Azure. <br> Najlepszym rozwiązaniem należy nadać elementu webhook nazwę związane z klienta, który korzysta z niego. |
| Adres URL |Adres URL elementu webhook jest unikatowy adres, który klient wywołuje z POST protokołu HTTP, aby uruchomić element runbook połączone z elementu webhook. Jest ona generowana automatycznie podczas tworzenia elementu webhook. Nie można określić niestandardowy adres URL. <br> <br> Adres URL zawiera token zabezpieczający, który umożliwia wywołanie przez system innej firmy, bez dalszego uwierzytelniania elementu runbook. Z tego powodu powinny być traktowane jak hasło. Ze względów bezpieczeństwa możesz jedynie wyświetlić adres URL w portalu Azure w czasie tworzenia elementu webhook. Zanotuj adres URL w bezpiecznej lokalizacji do użytku w przyszłości. |
| Data ważności |Takie jak certyfikat każdy element webhook ma datę wygaśnięcia, które go można już używać. Ta data wygaśnięcia można zmodyfikować po utworzeniu elementu webhook. |
| Enabled (Włączony) |Elementu webhook jest domyślnie włączona po jego utworzeniu. Jeśli zostanie ustawiona na wyłączone, klienta nie będzie mógł jej użyć. Można ustawić **włączone** właściwości po utworzeniu elementu webhook lub w każdej chwili po jego utworzeniu. |

### <a name="parameters"></a>Parametry

Elementu webhook można zdefiniować wartości parametrów elementu runbook, które będą używane po uruchomieniu elementu runbook przez ten element webhook. Element webhook muszą zawierać wartości parametrów obowiązkowych elementu runbook i może zawierać wartości parametrów opcjonalnych. Wartość parametru skonfigurowany do elementu webhook można modyfikować po utworzeniu elementu webhook. Każdy z wielu elementów webhook połączone z jednego elementu runbook można użyć wartości innym parametrem.

Po uruchomieniu elementu runbook za pomocą elementu webhook klient go nie można zastąpić wartości parametrów zdefiniowanych w elementu webhook. Na odbieranie danych z klientem, element runbook może akceptować jeden parametr o nazwie **$WebhookData** typu [object], który zawiera dane, które klient dołącza w żądaniu POST.

![Właściwości Webhookdata](media/automation-webhooks/webhook-data-properties.png)

**$WebhookData** obiekt ma następujące właściwości:

| Właściwość | Opis |
|:--- |:--- |
| WebhookName |Nazwa elementu webhook. |
| RequestHeader |Tabela skrótów zawierająca nagłówki przychodzącego żądania POST. |
| requestBody |Treść żądania POST przychodzącego. To zachowuje formatowania, takiego jak ciąg formatu JSON, XML, lub postać zakodowanych danych. Element runbook musi być przystosowana do pracy z formatem danych, która oczekuje. |

Nie jest żadna konfiguracja elementu webhook wymagany do obsługi **$WebhookData** parametr, a element runbook nie jest wymagane go zaakceptować. Jeśli element runbook nie definiuje parametru, żadnych szczegółów żądania wysłanych z klienta zostanie zignorowany.

Jeśli musisz określić wartość dla $WebhookData podczas tworzenia elementu webhook, że wartość zostanie zastąpiona podczas elementu webhook uruchamiania elementu runbook przy użyciu danych z żądania POST klienta, nawet jeśli klient nie ma żadnych danych w treści żądania. Po uruchomieniu elementu runbook, który ma $WebhookData przy użyciu innej metody niż elementu webhook należy podać wartość dla $Webhookdata rozpoznawany przez element runbook. Ta wartość powinna być obiekt o takim samym [właściwości](#details-of-a-webhook) jako $Webhookdata tak, aby element runbook właściwie pracę z nią tak, jakby jego pracy z rzeczywistego WebhookData przekazany przez elementu webhook.

Na przykład Jeśli rozpoczynasz następujący element runbook w portalu Azure i przekazać niektóre przykładowe WebhookData do testowania, ponieważ obiekt jest WebhookData, powinien zostać przekazany jako dane JSON w Interfejsie użytkownika.

![Parametr WebhookData z poziomu interfejsu użytkownika](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Dla następującego elementu runbook, jeśli masz następujące właściwości dla parametru WebhookData:

* WebhookName: *MyWebhook*
* RequestBody: *[{"W grupie zasobów": "myResourceGroup", "Nazwa": "vm01"}, {"W grupie zasobów": "myResourceGroup", "Nazwa": "vm02"}]*

Następnie przejdzie następującą wartość JSON w Interfejsie użytkownika dla parametru WebhookData. Zwraca następujący przykład karetki, znaków nowego wiersza odpowiada formacie, który jest przekazywany w z elementu webhook.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Uruchom parametr WebhookData z interfejsu użytkownika](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Wartości wszystkich parametrów wejściowych są rejestrowane za pomocą zadania elementu runbook. Oznacza to, że wszelkie danych wejściowych dostarczonych przez klienta w żądaniu elementu webhook jest rejestrowane i dostępną dla wszystkich użytkowników z dostępem do zadanie usługi Automatyzacja.  Z tego powodu należy zachować ostrożność w wywołaniach elementu webhook w tym poufne informacje.

## <a name="security"></a>Bezpieczeństwo

Zabezpieczenia elementu webhook polega na prywatność jej adres URL, który zawiera token zabezpieczający, który umożliwi można wywołać. Automatyzacja Azure nie wykonuje żadnego uwierzytelniania na żądanie tak długo, jak staje się poprawny adres URL. Z tego powodu nie można używać elementów webhook dla elementów runbook, które bardzo ważne zmiany funkcji bez używania alternatywnej metody sprawdzania poprawności żądania.

Można uwzględnić logiki w ramach elementu runbook w celu określenia, czy została wywołana przez elementu webhook, sprawdzając **WebhookName** właściwość parametru $WebhookData. Element runbook można wykonać dalsze weryfikacji przez wyszukiwanie szczegółowych informacji w **RequestHeader** lub **RequestBody** właściwości.

Kolejną strategią jest runbook weryfikowania niektórych warunków zewnętrznych podczas odebrała żądanie elementu webhook. Rozważmy na przykład element runbook, który jest wywoływany przez GitHub, gdy istnieje nowy zatwierdzeń do repozytorium GitHub. Element runbook może się połączyć GitHub, aby sprawdzić, czy nastąpiło nowe zatwierdzenia przed kontynuowaniem.

## <a name="creating-a-webhook"></a>Tworzenie elementu webhook

Użyj poniższej procedury, aby utworzyć nowy element webhook powiązany element runbook w portalu Azure.

1. Z **strony elementów Runbook** w portalu Azure kliknij element runbook, który uruchamia elementu webhook, aby wyświetlić jego stronę szczegółów.
1. Kliknij przycisk **Webhook** w górnej części strony, aby otworzyć **Dodawanie elementu Webhook** strony.
1. Kliknij przycisk **tworzenia nowego elementu webhook** otworzyć **tworzenia elementu webhook strony**.
1. Określ **nazwa**, **Data wygaśnięcia** dla elementu webhook i określa, czy powinno być włączone. Zobacz [szczegóły elementu webhook](#details-of-a-webhook) Aby uzyskać więcej informacji tych właściwości.
1. Kliknij ikonę kopiowania, a następnie naciśnij klawisze Ctrl + C, aby skopiować adres URL elementu webhook. Następnie zapisz go w bezpiecznym miejscu. **Po utworzeniu elementu webhook nie można ponownie pobrać adresu URL.**

   ![Adres URL elementu webhook](media/automation-webhooks/copy-webhook-url.png)

1. Kliknij przycisk **parametry** podać wartości parametrów elementu runbook. Jeśli element runbook ma parametry obowiązkowe, następnie nie jest możliwe do tworzenia elementu webhook, chyba że znajdują się wartości.
1. Kliknij przycisk **Utwórz** można utworzyć elementu webhook.

## <a name="using-a-webhook"></a>Przy użyciu elementu webhook

Aby użyć elementu webhook po jego utworzeniu, aplikacja kliencka należy wygenerować HTTP POST z adresem URL dla elementu webhook. Składnia elementu webhook jest w następującym formacie:

```http
http://<Webhook Server>/token?=<Token Value>
```

Klient odbiera jedną z następujących kody powrotu z żądania POST.

| Kod | Tekst | Opis |
|:--- |:--- |:--- |
| 202 |Zaakceptowany |Żądanie zostało zaakceptowane, a element runbook został pomyślnie w kolejce. |
| 400 |Nieprawidłowe żądanie |Nie zaakceptowano żądanie dla jednego z następujących powodów: <ul> <li>Element webhook wygasł.</li> <li>Elementu webhook jest wyłączona.</li> <li>Token w adresie URL jest nieprawidłowy.</li>  </ul> |
| 404 |Nie znaleziono |Nie zaakceptowano żądanie dla jednego z następujących powodów: <ul> <li>Nie można odnaleźć elementu webhook.</li> <li>Nie znaleziono elementu runbook.</li> <li>Nie można odnaleźć konta.</li>  </ul> |
| 500 |Wewnętrzny błąd serwera |Adres URL jest prawidłowy, ale wystąpił błąd. Prześlij żądanie. |

Zakładając, że żądanie zakończy się pomyślnie, odpowiedzi elementu webhook zawiera identyfikator zadania w formacie JSON w następujący sposób. Będzie zawierać identyfikator pojedyncze zadanie, ale umożliwia potencjalne przyszłe ulepszenia formatu JSON.

```json
{"JobIds":["<JobId>"]}
```

Klient nie może określić po zakończeniu zadania elementu runbook lub jego stanie ukończenia z elementu webhook. Można określić te informacje przy użyciu identyfikatora zadania przy użyciu innej metody takie jak [programu Windows PowerShell](http://msdn.microsoft.com/library/azure/dn690263.aspx) lub [interfejsu API usługi Automatyzacja Azure](/rest/api/automation/job).

## <a name="sample-runbook"></a>Przykładowy element runbook

Następujący przykładowy element runbook akceptuje akceptuje dane elementu webhook i uruchomienie maszyny wirtualnej określona w treści żądania. Aby przetestować ten element runbook, na Twoim koncie automatyzacji w obszarze **elementów Runbook**, kliknij przycisk **+ Dodaj element runbook**. Jeśli nie wiesz, jak utworzyć element runbook, zobacz [Tworzenie elementu runbook](automation-quickstart-create-runbook.md).

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData
)

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

    # Retrieve VM's from Webhook request body
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
            Add-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Output

        # Start each virtual machine
        foreach ($vm in $vms)
        {
            $vmName = $vm.Name
            Write-Output "Starting $vmName"
            Start-AzureRMVM -Name $vm.Name -ResourceGroup $vm.ResourceGroup
        }
}
else {
    # Error
    write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="test-the-example"></a>Przykład testu

W poniższym przykładzie użyto programu Windows PowerShell do uruchamiania elementu runbook z elementu webhook. Dowolnego języka, który może zgłaszać żądania HTTP można użyć elementu webhook; Środowisko Windows PowerShell jest używane jako przykład.

Element runbook jest Oczekiwano listy maszyn wirtualnych zapisany w formacie JSON w treści żądania.

```azurepowershell-interactive
$uri = "<webHook Uri>"

$vms  = @(
            @{ Name="vm01";ResourceGroup="vm01"},
            @{ Name="vm02";ResourceGroup="vm02"}
        )
$body = ConvertTo-Json -InputObject $vms

$response = Invoke-RestMethod -Method Post -Uri $uri -Body $body
$jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
```

W poniższym przykładzie przedstawiono treści żądania, która jest dostępna dla elementu runbook w **RequestBody** właściwość **WebhookData**. To jest w formacie JSON powodu formatu, który został uwzględniony w treści żądania.

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

Na poniższej ilustracji przedstawiono żądania wysyłane z programu Windows PowerShell i wynikowy odpowiedzi. Identyfikator zadania jest wyodrębniana z odpowiedzi i konwertowana na ciąg.

![Przycisk elementów Webhook](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się, jak używać usługi Automatyzacja Azure do wykonania akcji na alerty Azure, zobacz [Użyj alertu można wyzwolić elementu runbook usługi Automatyzacja Azure](automation-create-alert-triggered-runbook.md).
