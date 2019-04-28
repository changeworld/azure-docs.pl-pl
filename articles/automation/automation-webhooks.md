---
title: Uruchamianie elementu runbook usługi Azure Automation za pomocą elementu webhook
description: Element webhook, który umożliwia klientom uruchamianie elementu runbook w usłudze Azure Automation z wywołania HTTP.  W tym artykule opisano sposób tworzenia elementu webhook oraz wywołać jedną do uruchamiania elementu runbook.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 153bb0304102906f7be64ae55dd0e0f6bb8d7146
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61305024"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Uruchamianie elementu runbook usługi Azure Automation za pomocą elementu webhook

A *elementu webhook* umożliwia uruchomienie określonego elementu runbook w usłudze Azure Automation za pośrednictwem pojedynczego żądania HTTP. Dzięki temu usług zewnętrznych, takich jak usługom DevOps platformy Azure, GitHub, dzienników usługi Azure Monitor lub niestandardowe aplikacje do uruchamiania elementów runbook bez implementacji pełnego rozwiązania przy użyciu interfejsu API usługi Azure Automation.  
![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

Można porównać elementów webhook, uruchamianie elementu runbook inne sposoby [uruchamianie elementu runbook w usłudze Azure Automation](automation-starting-a-runbook.md)

> [!NOTE]
> Aby uruchomić element runbook Python przy użyciu elementu webhook nie jest obsługiwana.

## <a name="details-of-a-webhook"></a>Szczegóły elementu webhook

W poniższej tabeli opisano właściwości, które należy skonfigurować dla elementu webhook.

| Właściwość | Opis |
|:--- |:--- |
| Name (Nazwa) |Możesz podać dowolną nazwę, wymagany dla elementu webhook, ponieważ to nie jest widoczne dla klienta. Jest ono używane wyłącznie dla Ciebie do identyfikowania elementów runbook w usłudze Azure Automation. <br> Najlepszym rozwiązaniem należy nadać elementu webhook nazwę związane z klienta, który korzysta z niego. |
| Adres URL |Adres URL elementu webhook jest unikatowy adres, który klient wywołań za pomocą metody POST protokołu HTTP, aby uruchomić element runbook połączone z elementu webhook. Są one generowane automatycznie podczas tworzenia elementu webhook. Nie można określić niestandardowy adres URL. <br> <br> Adres URL zawiera token zabezpieczający, który umożliwia elementu runbook do wywołania przez system innej firmy, bez dalszego uwierzytelniania. Z tego powodu powinny być traktowane jak hasło. Ze względów bezpieczeństwa możesz tylko wyświetlić adres URL w witrynie Azure portal w czasie, zostanie utworzony element webhook. Zanotuj adres URL w bezpiecznej lokalizacji, do użytku w przyszłości. |
| Data ważności |Podobnie jak certyfikat każdy element webhook ma datę wygaśnięcia, co może już służyć. Ta data wygaśnięcia można zmodyfikować po utworzeniu elementu webhook, tak długo, jak element webhook nie wygasł. |
| Enabled (Włączony) |Element webhook jest domyślnie włączona, podczas jego tworzenia. Jeśli zostanie ustawiona na wyłączone, a następnie klienta nie będzie można jej używać. Możesz ustawić **włączone** podczas tworzenia elementu webhook lub dowolnym momencie po jej utworzeniu. |

### <a name="parameters"></a>Parametry

Element webhook można określić wartości parametrów elementu runbook, które są używane, gdy element runbook zostanie uruchomiony przez ten element webhook. Element webhook musi zawierać wartości parametrów obowiązkowych elementu runbook i może zawierać wartości dla parametrów opcjonalnych. Wartość parametru skonfigurowany tak, aby element webhook można zmodyfikować po utworzeniu elementu webhook. Każdy z wielu elementów webhook, połączone z jednego elementu runbook można użyć wartości różnych parametrów.

Po uruchomieniu elementu runbook za pomocą elementu webhook klient go nie można zastąpić wartości parametrów zdefiniowanych w elementu webhook. Na odbieranie danych z klienta, element runbook może zaakceptować pojedynczy parametr o nazwie **$WebhookData**. Ten parametr jest typu [object], która zawiera dane, które klient dołącza do żądania POST.

![Parametr Webhookdata właściwości](media/automation-webhooks/webhook-data-properties.png)

**$WebhookData** obiekt ma następujące właściwości:

| Właściwość | Opis |
|:--- |:--- |
| WebhookName |Nazwa elementu webhook. |
| RequestHeader |Tabela skrótu zawierający nagłówki przychodzące żądanie POST. |
| RequestBody |Treść żądania POST przychodzącego. To zachowa formatowania, takiego jak ciąg JSON, XML, lub formowanie danych zakodowanych jako formularz. Element runbook musi być przystosowana do pracy z formatem danych, który jest oczekiwany. |

Brak konfiguracji elementu webhook, wymagane do obsługi **$WebhookData** parametru i elementu runbook nie jest wymagane go zaakceptować. Jeśli element runbook nie zdefiniowano parametru, żadnych szczegółów żądania wysłanych z klienta zostaną zignorowane.

Jeśli określisz wartości dla $WebhookData podczas tworzenia elementu webhook, że wartość zostanie zastąpiona podczas elementu webhook uruchamiania elementu runbook przy użyciu danych z żądania POST klienta, nawet wtedy, gdy klient nie ma żadnych danych w treści żądania. W przypadku uruchomienia elementu runbook, który ma $WebhookData przy użyciu innej metody niż element webhook, możesz podać wartość $Webhookdata rozpoznawany przez element runbook. Ta wartość powinna być obiekt o takiej samej [właściwości](#details-of-a-webhook) jako $Webhookdata elementu runbook może prawidłowo pracować z nim, tak, jakby była pracę rzeczywisty parametr WebhookData przekazywane przez element webhook.

Na przykład Jeśli rozpoczynasz następujący element runbook w portalu Azure i mają być przekazane przykładowymi parametr WebhookData do testowania, ponieważ parametr WebhookData jest obiektem, powinien zostać przekazany jako dane JSON w interfejsie użytkownika.

![Parametr WebhookData z poziomu interfejsu użytkownika](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Dla następującego elementu runbook, jeśli mają następujące właściwości dla parametru WebhookData:

* WebhookName: *MyWebhook*
* RequestBody: *[{"ResourceGroup": "myResourceGroup", "Name": "vm01"}, {"ResourceGroup": "myResourceGroup", "Name": "vm02"}]*

Następnie należy wprowadzić następujące wartości JSON w Interfejsie użytkownika dla parametru WebhookData. W poniższym przykładzie, za pomocą transportu zwracany i znaki nowego wiersza dopasowuje formatu, który jest przekazywany z poziomu elementu webhook.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Parametr WebhookData rozpoczęcia z poziomu interfejsu użytkownika](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Wartości wszystkie parametry wejściowe są rejestrowane przy użyciu zadania elementu runbook. Oznacza to, że dowolny danych wejściowych dostarczonych przez klienta podczas żądania elementu webhook będą rejestrowane i dostępne dla każdej osoby z uprawnieniami do zadania automatyzacji.  Z tego powodu należy zachować ostrożność w tym informacji poufnych w wywołaniach elementu webhook.

## <a name="security"></a>Bezpieczeństwo

Zabezpieczenia elementu webhook opiera się na prywatność jej adresu URL, który zawiera token zabezpieczający, który umożliwia wywoływanie. Usługa Azure Automation nie wykonuje uwierzytelniania na żądanie tak długo, jak staje się do prawidłowego adresu URL. Z tego powodu nie można używać elementów webhook dla elementów runbook, które wykonują funkcje wysoce poufne bez używania alternatywnej metody sprawdzania poprawności żądania.

Może zawierać logiki w ramach elementu runbook w celu określenia, czy została wywołana przez element webhook, sprawdzając **WebhookName** właściwość parametru $WebhookData. Element runbook mógł wykonać dalsze weryfikacji przez wyszukiwanie szczegółowych informacji w **RequestHeader** lub **RequestBody** właściwości.

Kolejną strategią jest runbook weryfikowania niektórych warunku zewnętrznych podczas otrzymał żądanie elementu webhook. Rozważmy na przykład element runbook, który jest wywoływany przez usługę GitHub, gdy ma nowego zatwierdzenia z repozytorium GitHub. Element runbook może nawiązywać połączenie z usługi GitHub, aby sprawdzić, czy wystąpiły nowe zatwierdzenie, przed kontynuowaniem.

## <a name="creating-a-webhook"></a>Utworzenie elementu webhook

Poniższa procedura umożliwia utworzenie nowego elementu webhook, połączone z elementem runbook w witrynie Azure portal.

1. Z **strony elementów Runbook** w witrynie Azure portal, kliknij element runbook, który uruchamia elementu webhook, aby wyświetlić jego stronę szczegółów. Upewnij się, element runbook **stan** jest **opublikowano**.
2. Kliknij przycisk **elementu Webhook** w górnej części strony Aby otworzyć **Dodaj element Webhook** strony.
3. Kliknij przycisk **Utwórz nowy element webhook** otworzyć **tworzenia elementu webhook strony**.
4. Określ **nazwa**, **datę wygaśnięcia** dla elementu webhook i czy powinno ono zostać włączone. Zobacz [szczegóły elementu webhook](#details-of-a-webhook) Aby uzyskać więcej informacji tych właściwości.
5. Kliknij ikonę kopiowania, a następnie naciśnij klawisze Ctrl + C, aby skopiować adres URL elementu webhook. Następnie zapisz go w bezpiecznym miejscu. **Po utworzeniu elementu webhook, adres URL nie może pobrać ponownie.**

   ![Adres URL elementu webhook](media/automation-webhooks/copy-webhook-url.png)

1. Kliknij przycisk **parametry** podać wartości parametrów elementu runbook. Jeśli element runbook ma parametry obowiązkowe, następnie nie jest możliwe do utworzenia elementu webhook, chyba że wartości są podane.
1. Kliknij przycisk **Utwórz** tworzenia elementu webhook.

## <a name="using-a-webhook"></a>Za pomocą elementu webhook

Aby po jego utworzeniu, należy użyć elementu webhook, Twoja aplikacja kliencka musi wysłać metodę POST protokołu HTTP przy użyciu adresu URL elementu webhook. Składnia elementu webhook jest w następującym formacie:

```http
http://<Webhook Server>/token?=<Token Value>
```

Klient odbiera jedną z następujących kody powrotne z żądania POST.

| Kod | Text | Opis |
|:--- |:--- |:--- |
| 202 |Zaakceptowany |Żądanie zostało zaakceptowane, a element runbook został pomyślnie w kolejce. |
| 400 |Nieprawidłowe żądanie |Żądanie nie zostało zaakceptowane dla jednego z następujących powodów: <ul> <li>Element webhook wygasł.</li> <li>Element webhook jest wyłączona.</li> <li>Token w adresie URL jest nieprawidłowy.</li>  </ul> |
| 404 |Nie znaleziono |Żądanie nie zostało zaakceptowane dla jednego z następujących powodów: <ul> <li>Nie znaleziono elementu webhook.</li> <li>Nie znaleziono elementu runbook.</li> <li>Nie znaleziono konta.</li>  </ul> |
| 500 |Wewnętrzny błąd serwera |Adres URL był prawidłowy, ale wystąpił błąd. Prześlij żądanie. |

Przy założeniu, że żądanie zakończy się pomyślnie, odpowiedź elementu webhook zawiera identyfikator zadania w formacie JSON w następujący sposób. Będzie ona zawierać identyfikator pojedynczego zadania, ale umożliwia potencjalne przyszłe rozszerzenia będą miały JSON format.

```json
{"JobIds":["<JobId>"]}
```

Klient nie może określić, po zakończeniu zadania elementu runbook lub jego stanu ukończenia od elementu webhook. Można określić, te informacje przy użyciu Identyfikatora zadania przy użyciu innej metody takie jak [programu Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjob) lub [interfejsu API usługi Azure Automation](/rest/api/automation/job).

## <a name="renew-webhook"></a>Odnów elementu webhook

Po utworzeniu elementu webhook ma okres ważności jednego roku. Wygasa po roku czasie automatycznie elementu webhook. Gdy element webhook wygasł. nie można ponownie uaktywnić, należy usunąć i ponownie utworzyć. Element webhook nie dotarła do czasu jego wygaśnięcia, można rozszerzyć.

Aby rozszerzyć elementu webhook, przejdź do elementu runbook, który zawiera element webhook. Wybierz **elementów Webhook** w obszarze **zasobów**. Kliknij element webhook, który ma zostać rozszerzony, ta akcja powoduje otwarcie **elementu Webhook** strony.  Wybierz nową datę wygaśnięcia i godzina, a następnie kliknij przycisk **Zapisz**.

## <a name="sample-runbook"></a>Przykładowy element runbook

Następujący przykładowy element runbook przyjmuje dane elementu webhook i uruchamia maszyny wirtualne, określona w treści żądania. Aby przetestować ten element runbook na Twoim koncie usługi Automation w ramach **elementów Runbook**, kliknij przycisk **+ Dodaj element runbook**. Jeśli nie wiesz, jak utworzyć element runbook, zobacz [Tworzenie elementu runbook](automation-quickstart-create-runbook.md).

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

W poniższym przykładzie użyto programu Windows PowerShell do uruchamiania elementu runbook za pomocą elementu webhook. Dowolny język, który może zgłaszać żądania HTTP można użyć elementu webhook; Program Windows PowerShell jest używany tutaj jako przykładu.

Element runbook oczekuje listy maszyn wirtualnych, zapisany w formacie JSON w treści żądania. Element runbook sprawdza także, czy nagłówki zawierają zdefiniowanych wiadomości do sprawdzania poprawności wywołujący element webhook jest prawidłowy.

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

W poniższym przykładzie pokazano treści żądania, które są dostępne w elemencie runbook w programie **RequestBody** właściwość **WebhookData**. Ta wartość jest w formacie JSON, ponieważ format, który został uwzględniony w treści żądania.

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

Na poniższej ilustracji przedstawiono żądania wysyłane z programu Windows PowerShell i wynikowe odpowiedzi. Identyfikator zadania jest wyodrębniany z odpowiedzi i konwertowana na ciąg.

![Przycisk elementów Webhook](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się, jak usługa Azure Automation umożliwia podejmowanie akcji na alerty platformy Azure, zobacz [umożliwia alertu można wyzwolić elementu runbook usługi Azure Automation](automation-create-alert-triggered-runbook.md).

