---
title: Uruchamianie Azure Automation elementu Runbook za pomocą elementu webhook
description: Element webhook, który umożliwia klientowi uruchomienie elementu Runbook w Azure Automation z wywołania HTTP.  W tym artykule opisano sposób tworzenia elementu webhook oraz wywoływania go w celu uruchomienia elementu Runbook.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/19/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: bc03425a64486e449b4df93ea187435a1e893dda
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849602"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Uruchamianie Azure Automation elementu Runbook za pomocą elementu webhook

Element *webhook* umożliwia uruchomienie określonego elementu runbook w Azure Automation za pośrednictwem pojedynczego żądania HTTP. Umożliwia to usługom zewnętrznym, takim jak Azure DevOps Services, GitHub, dzienniki Azure Monitor lub aplikacje niestandardowe do uruchamiania elementów Runbook bez implementowania pełnego rozwiązania przy użyciu interfejsu API Azure Automation.
![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

Elementy webhook można porównać z innymi metodami uruchamiania elementu Runbook w [rozpoczęciu wykonywania elementu Runbook w Azure Automation](automation-starting-a-runbook.md)

> [!NOTE]
> Korzystanie z elementu webhook do uruchamiania elementów Runbook języka Python nie jest obsługiwane.

## <a name="details-of-a-webhook"></a>Szczegóły elementu webhook

W poniższej tabeli opisano właściwości, które należy skonfigurować dla elementu webhook.

| Właściwość | Opis |
|:--- |:--- |
| Nazwa |Możesz podać dowolną nazwę dla elementu webhook, ponieważ nie jest on narażony na klienta. Jest on używany tylko do identyfikowania elementu Runbook w Azure Automation. <br> Najlepszym rozwiązaniem jest nadanie elementu webhook nazwy powiązanej z klientem, który go używa. |
| Adres URL |Adres URL elementu webhook jest unikatowym adresem, który klient wywołuje przy użyciu wpisu HTTP w celu uruchomienia elementu Runbook połączonego z elementem webhook. Jest on generowany automatycznie podczas tworzenia elementu webhook. Nie można określić niestandardowego adresu URL. <br> <br> Adres URL zawiera token zabezpieczający, który umożliwia wywoływanie elementu Runbook przez system innej firmy bez żadnego dodatkowego uwierzytelniania. Z tego powodu powinna być traktowana jak hasło. Ze względów bezpieczeństwa można tylko wyświetlić adres URL w Azure Portal w momencie utworzenia elementu webhook. Zanotuj adres URL w bezpiecznej lokalizacji do użytku w przyszłości. |
| Data wygaśnięcia |Podobnie jak w przypadku certyfikatu, każdy element webhook ma datę wygaśnięcia, w której nie można już jej używać. Tę datę wygaśnięcia można zmodyfikować po utworzeniu elementu webhook, o ile element webhook nie wygasł. |
| Enabled (Włączony) |Element webhook jest domyślnie włączony podczas jego tworzenia. Jeśli ustawisz ją jako wyłączoną, nie będzie można jej używać. Właściwość **Enabled** można ustawić podczas tworzenia elementu webhook lub w dowolnym momencie po jego utworzeniu. |

### <a name="parameters"></a>Parametry

Element webhook może definiować wartości parametrów elementu Runbook, które są używane, gdy element Runbook jest uruchamiany przez ten element webhook. Element webhook musi zawierać wartości parametrów obowiązkowych elementu Runbook i może zawierać wartości parametrów opcjonalnych. Wartość parametru skonfigurowana dla elementu webhook można modyfikować nawet po utworzeniu elementu webhook. Wiele elementów webhook połączonych z pojedynczym elementem Runbook może używać różnych wartości parametrów.

Gdy klient uruchamia element Runbook przy użyciu elementu webhook, nie może zastąpić wartości parametrów zdefiniowanych w elemencie webhook. Aby odebrać dane od klienta, element Runbook może zaakceptować jeden parametr o nazwie **$WebhookData**. Ten parametr jest typu [Object], który zawiera dane zawarte przez klienta w żądaniu POST.

![Właściwości Webhookdata](media/automation-webhooks/webhook-data-properties.png)

Obiekt **$WebhookData** ma następujące właściwości:

| Właściwość | Opis |
|:--- |:--- |
| WebhookName |Nazwa elementu webhook. |
| RequestHeader |Tablica skrótów zawierająca nagłówki przychodzącego żądania POST. |
| Elemencie requestbody |Treść przychodzącego żądania POST. Spowoduje to zachowanie dowolnego formatowania, takiego jak ciąg, JSON, XML lub zakodowane dane formularza. Element Runbook musi być zapisany, aby można było korzystać z oczekiwanego formatu danych. |

Nie istnieje konfiguracja elementu webhook wymaganego do obsługi parametru **$WebhookData** , a element Runbook nie jest wymagany do jego zaakceptowania. Jeśli element Runbook nie definiuje parametru, wszystkie szczegóły żądania wysyłanego z klienta zostaną zignorowane.

> [!NOTE]
> Podczas wywoływania elementu webhook należy zawsze przechowywać wszystkie wartości parametrów na wypadek niepowodzenia wywołania. W przypadku awarii sieci lub problemu z połączeniem nie można pobrać zakończonych niepowodzeniem wywołań elementu webhook.

Jeśli określisz wartość dla $WebhookData podczas tworzenia elementu webhook, ta wartość zostanie zastąpiona, gdy element webhook rozpocznie działanie elementu Runbook przy użyciu danych z żądania POST klienta, nawet jeśli klient nie zawiera żadnych danych w treści żądania. W przypadku uruchomienia elementu Runbook, który ma $WebhookData przy użyciu metody innej niż element webhook, można podać wartość $Webhookdata, która jest rozpoznawana przez element Runbook. Ta wartość powinna być obiektem z tymi samymi [właściwościami](#details-of-a-webhook) co $Webhookdata tak, aby element Runbook mógł prawidłowo pracować z nim tak, jakby działał z rzeczywistym Webhookdata przekazaną przez element webhook.

Na przykład, jeśli uruchamiasz następujący element Runbook z Azure Portal i chcesz przekazać niektóre przykładowe WebhookData do testowania, ponieważ WebhookData jest obiektem, powinien on zostać przekazany jako kod JSON w interfejsie użytkownika.

![WebhookData parametr z interfejsu użytkownika](media/automation-webhooks/WebhookData-parameter-from-UI.png)

W przypadku następującego elementu Runbook, jeśli masz następujące właściwości dla parametru WebhookData:

* Element webhookname: *MyWebhook*
* Elemencie requestbody: *[{"resourceName": "Moja resourceName", "name": "vm01"}, {"resourceName": "Moja resourceName", "name": "vm02"}]*

Następnie można przekazać następującą wartość JSON w interfejsie użytkownika dla parametru WebhookData. Poniższy przykład z znakami powrotu karetki i znaków nowego wiersza pasuje do formatu, który jest przesyłany z elementu webhook.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Uruchom parametr WebhookData z interfejsu użytkownika](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Wartości wszystkich parametrów wejściowych są rejestrowane przy użyciu zadania elementu Runbook. Oznacza to, że wszelkie dane wejściowe dostarczone przez klienta w żądaniu elementu webhook będą rejestrowane i dostępne dla wszystkich użytkowników mających dostęp do zadania automatyzacji.  Z tego powodu należy zachować ostrożność wraz z uwzględnieniem poufnych informacji w wywołaniach elementu webhook.

## <a name="security"></a>Zabezpieczenia

Bezpieczeństwo elementu webhook polega na poufności jego adresu URL, który zawiera token zabezpieczający, który umożliwia wywoływanie. Azure Automation nie wykonuje żadnych uwierzytelnień w żądaniu, o ile zostanie wprowadzony prawidłowy adres URL. Z tego powodu elementy webhook nie powinny być używane dla elementów Runbook, które wykonują wysoce poufne funkcje bez użycia alternatywnego sposobu weryfikacji żądania.

Można uwzględnić logikę w elemencie Runbook, aby określić, że została wywołana przez element webhook, sprawdzając Właściwość **webhookname** parametru $WebhookData. Element Runbook może przeprowadzić dalsze sprawdzanie poprawności, szukając określonych informacji we właściwościach **RequestHeader** lub **elemencie requestbody** .

Inna strategia polega na tym, że element Runbook wykonuje weryfikację zewnętrznego stanu po odebraniu żądania elementu webhook. Rozważmy na przykład element Runbook, który jest wywoływany przez witrynę GitHub, gdy istnieje nowe zatwierdzenie do repozytorium GitHub. Element Runbook może nawiązać połączenie z usługą GitHub, aby zweryfikować, że nastąpiło nowe zatwierdzenie przed kontynuowaniem.

## <a name="creating-a-webhook"></a>Tworzenie elementu webhook

Poniższa procedura umożliwia utworzenie nowego elementu webhook połączonego z elementem Runbook w Azure Portal.

1. Na **stronie elementy Runbook** w Azure Portal kliknij element Runbook, który zostanie uruchomiony przez element webhook, aby wyświetlić jego stronę szczegółów. Upewnij się, że **stan** elementu Runbook jest **opublikowany**.
2. Kliknij pozycję **element webhook** w górnej części strony, aby otworzyć stronę **Dodawanie elementu webhook** .
3. Kliknij pozycję **Utwórz nowy element webhook** , aby otworzyć **stronę Tworzenie elementu webhook**.
4. Określ **nazwę**, **datę wygaśnięcia** elementu webhook i czy powinien on być włączony. Aby uzyskać więcej informacji o tych właściwościach [, zobacz szczegóły elementu webhook](#details-of-a-webhook) .
5. Kliknij ikonę kopiowania i naciśnij klawisze CTRL + C, aby skopiować adres URL elementu webhook. Następnie zapisz go w bezpiecznym miejscu. **Po utworzeniu elementu webhook nie można ponownie pobrać adresu URL.**

   ![Adres URL elementu webhook](media/automation-webhooks/copy-webhook-url.png)

1. Kliknij pozycję **Parametry** , aby podać wartości parametrów elementu Runbook. Jeśli element runbook ma parametry obowiązkowe, nie można utworzyć elementu webhook, chyba że zostaną podane wartości.
1. Kliknij pozycję **Utwórz**, aby utworzyć element webhook.

## <a name="using-a-webhook"></a>Używanie elementu webhook

Aby można było użyć elementu webhook po jego utworzeniu, aplikacja kliencka musi wydać wpis HTTP przy użyciu adresu URL elementu webhook. Składnia elementu webhook ma następujący format:

```http
http://<Webhook Server>/token?=<Token Value>
```

Klient otrzymuje jeden z następujących kodów powrotnych z żądania POST.

| Kod | Tekst | Opis |
|:--- |:--- |:--- |
| 202 |Zaakceptowano |Żądanie zostało zaakceptowane, a element Runbook został pomyślnie umieszczony w kolejce. |
| 400 |Nieprawidłowe żądanie |Żądanie nie zostało zaakceptowane z jednego z następujących powodów: <ul> <li>Element webhook wygasł.</li> <li>Element webhook jest wyłączony.</li> <li>Token w adresie URL jest nieprawidłowy.</li>  </ul> |
| 404 |Nie znaleziono |Żądanie nie zostało zaakceptowane z jednego z następujących powodów: <ul> <li>Nie znaleziono elementu webhook.</li> <li>Nie znaleziono elementu Runbook.</li> <li>Nie znaleziono konta.</li>  </ul> |
| 500 |Wewnętrzny błąd serwera |Adres URL był prawidłowy, ale wystąpił błąd. Prześlij ponownie żądanie. |

Przy założeniu, że żądanie zakończy się pomyślnie, odpowiedź elementu webhook zawiera identyfikator zadania w formacie JSON w następujący sposób. Będzie on zawierał pojedynczy identyfikator zadania, ale format JSON pozwala na potencjalne udoskonalenia w przyszłości.

```json
{"JobIds":["<JobId>"]}
```

Klient nie może określić momentu zakończenia zadania elementu Runbook lub jego stanu ukończenia z elementu webhook. Informacje te można określić przy użyciu identyfikatora zadania innej metody, takiej jak program [Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjob) lub [interfejs API Azure Automation](/rest/api/automation/job).

## <a name="renew-webhook"></a>Odnawianie elementu webhook

Po utworzeniu elementu webhook obowiązuje okres ważności wynoszący jeden rok. Po upływie tego czasu element webhook automatycznie wygaśnie. Po wygaśnięciu elementu webhook nie można go ponownie uaktywnić, musi zostać usunięty i utworzony ponownie. Jeśli element webhook nie osiągnął czasu wygaśnięcia, można go rozszerzyć.

Aby rozwinąć element webhook, przejdź do elementu Runbook, który zawiera element webhook. Wybierz pozycję elementy **webhook** w obszarze **zasoby**. Kliknij element webhook, który chcesz zwiększyć, ta akcja spowoduje otwarcie strony **elementu webhook** .  Wybierz nową datę i godzinę wygaśnięcia, a następnie kliknij przycisk **Zapisz**.

## <a name="sample-runbook"></a>Przykładowy element Runbook

Następujący przykładowy element Runbook akceptuje dane elementu webhook i uruchamia maszyny wirtualne określone w treści żądania. Aby przetestować ten element Runbook, na koncie usługi Automation w obszarze **elementy Runbook**kliknij pozycję **+ Dodaj element Runbook**. Jeśli nie wiesz, jak utworzyć element Runbook, zobacz [Tworzenie elementu Runbook](automation-quickstart-create-runbook.md).

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

## <a name="test-the-example"></a>Testowanie przykładu

Poniższy przykład używa środowiska Windows PowerShell, aby uruchomić element Runbook za pomocą elementu webhook. Dowolny język, który może wykonywać żądanie HTTP, może używać elementu webhook; W przykładzie użyto programu Windows PowerShell.

Element Runbook oczekuje listy maszyn wirtualnych sformatowanych w formacie JSON w treści żądania. Element Runbook sprawdza poprawność, a nagłówki zawierają zdefiniowany komunikat, aby sprawdzić, czy obiekt wywołujący elementu webhook jest prawidłowy.

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

Poniższy przykład przedstawia treść żądania, które jest dostępne dla elementu Runbook we właściwości **elemencie requestbody** **WebhookData**. Ta wartość jest formatowana jako plik JSON, ponieważ był to format, który został uwzględniony w treści żądania.

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

Na poniższej ilustracji przedstawiono żądanie wysyłane z programu Windows PowerShell i otrzymanej odpowiedzi. Identyfikator zadania jest wyodrębniany z odpowiedzi i konwertowany na ciąg.

![Przycisk elementów webhook](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak za pomocą Azure Automation podejmować działania dotyczące alertów platformy Azure, zobacz temat [Używanie alertu do wyzwalania Azure Automation elementu Runbook](automation-create-alert-triggered-runbook.md).

