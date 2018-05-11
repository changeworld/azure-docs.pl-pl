---
title: Uruchamianie elementu runbook usługi Automatyzacja Azure z elementu webhook
description: Element webhook, która umożliwia klientowi uruchomienia elementu runbook automatyzacji Azure z wywołania HTTP.  W tym artykule opisano sposób tworzenia elementu webhook i wywoływanie jednego uruchomienia elementu runbook.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: bb64d0c5d94bb198b6ece2ea50a7fc248b93c7dd
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Uruchamianie elementu runbook usługi Automatyzacja Azure z elementu webhook
A *webhook* umożliwia uruchomienie określonego elementu runbook automatyzacji Azure za pomocą pojedynczego żądania HTTP. Dzięki temu usług zewnętrznych, takich jak Visual Studio Team Services, GitHub, usługi Analiza dzienników Azure lub niestandardowych aplikacji do uruchamiania elementów runbook bez wdrażania pełnego rozwiązania przy użyciu interfejsu API usługi Automatyzacja Azure.  
![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

Można porównać elementów webhook do innych metod uruchamianie elementu runbook [uruchamianie elementu runbook automatyzacji Azure](automation-starting-a-runbook.md)

## <a name="details-of-a-webhook"></a>Szczegóły elementu webhook
W poniższej tabeli opisano właściwości, które należy skonfigurować dla elementu webhook.

| Właściwość | Opis |
|:--- |:--- |
| Name (Nazwa) |Możesz podać dowolną nazwę wybranego dla elementu webhook, ponieważ to nie jest widoczne dla klientów.  Tylko służy do należy do identyfikacji elementu runbook automatyzacji Azure. <br>  Najlepszym rozwiązaniem należy nadać elementu webhook nazwę związane z klienta, który zostanie użyty. |
| Adres URL |Adres URL elementu webhook jest unikatowy adres, który klient wywołuje z POST protokołu HTTP, aby uruchomić element runbook połączone z elementu webhook.  Jest ona generowana automatycznie podczas tworzenia elementu webhook.  Nie można określić niestandardowy adres URL. <br> <br>  Adres URL zawiera token zabezpieczający, który umożliwia elementu runbook do wywołania przez system innej firmy bez dalszego uwierzytelniania. Z tego powodu powinny być traktowane jak hasło.  Ze względów bezpieczeństwa możesz jedynie wyświetlić adres URL w portalu Azure w czasie tworzenia elementu webhook. Należy zauważyć, adres URL w bezpiecznej lokalizacji do użytku w przyszłości. |
| Data ważności |Takie jak certyfikat każdy element webhook ma datę wygaśnięcia, które go można już używać.  Ta data wygaśnięcia można zmodyfikować po utworzeniu elementu webhook. |
| Enabled (Włączony) |Elementu webhook jest domyślnie włączona po jego utworzeniu.  Jeśli zostanie ustawiona na wyłączone, a następnie klienta nie będzie można go użyć.  Można ustawić **włączone** właściwości po utworzeniu elementu webhook lub w każdej chwili po jego utworzeniu. |

### <a name="parameters"></a>Parametry
Elementu webhook można zdefiniować wartości parametrów elementu runbook, które będą używane po uruchomieniu elementu runbook przez ten element webhook. Element webhook muszą zawierać wartości parametrów obowiązkowych elementu runbook i może zawierać wartości parametrów opcjonalnych. Wartość parametru skonfigurowany do elementu webhook można modyfikować po utworzeniu elementu webhook. Każdy z wielu elementów webhook połączone z jednego elementu runbook można użyć wartości innym parametrem.

Po uruchomieniu elementu runbook za pomocą elementu webhook klient go nie można zastąpić wartości parametrów zdefiniowanych w elementu webhook.  Na odbieranie danych z klientem, element runbook może akceptować jeden parametr o nazwie **$WebhookData** typu [object], który będzie zawierać dane, które klient dołącza w żądaniu POST.

![Właściwości Webhookdata](media/automation-webhooks/webhook-data-properties.png)

**$WebhookData** obiektu będzie mieć następujące właściwości:

| Właściwość | Opis |
|:--- |:--- |
| WebhookName |Nazwa elementu webhook. |
| RequestHeader |Tabela skrótów zawierająca nagłówki przychodzącego żądania POST. |
| requestBody |Treść żądania POST przychodzącego.  To zachowują formatowania, takiego jak ciąg formatu JSON, XML, lub postać zakodowanych danych. Element runbook musi być przystosowana do pracy z formatem danych, która oczekuje. |

Nie jest żadna konfiguracja elementu webhook wymagany do obsługi **$WebhookData** parametr, a element runbook nie jest wymagane go zaakceptować.  Jeśli element runbook nie definiuje parametru, żadnych szczegółów żądania wysłanych z klienta zostanie zignorowany.

Jeśli musisz określić wartość dla $WebhookData podczas tworzenia elementu webhook, że wartości zostaną zastąpione podczas elementu webhook uruchamiania elementu runbook przy użyciu danych z żądania POST klienta, nawet jeśli klient nie ma żadnych danych w treści żądania.  Po uruchomieniu elementu runbook, który ma $WebhookData przy użyciu innej metody niż elementu webhook należy podać wartość dla $Webhookdata rozpoznane zostaną przez element runbook.  Ta wartość powinna być obiekt o takim samym [właściwości](#details-of-a-webhook) jako $Webhookdata tak, aby element runbook właściwie pracę z nią tak, jakby jego pracy z rzeczywistego WebhookData przekazany przez elementu webhook.

Na przykład Jeśli rozpoczynasz następujący element runbook w portalu Azure i przekazać niektóre przykładowe WebhookData do testowania, ponieważ obiekt jest WebhookData, powinien zostać przekazany jako dane JSON w Interfejsie użytkownika.

![Parametr WebhookData z poziomu interfejsu użytkownika](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Powyżej elementu runbook, jeśli masz następujące właściwości dla parametru WebhookData:

1. WebhookName: *MyWebhook*
2. RequestHeader: *z = użytkownika testowego*
3. RequestBody: *["VM1", "Maszyny VM2"]*

Następnie przejdzie następującą wartość JSON w Interfejsie użytkownika dla parametru WebhookData:  

* {"WebhookName": "MyWebhook", "RequestHeader": {"Od": "Użytkownik testowy"}, "RequestBody": "[\"VM1\",\"maszyny VM2\"]"}

![Uruchom parametr WebhookData z interfejsu użytkownika](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Wartości wszystkich parametrów wejściowych są rejestrowane za pomocą zadania elementu runbook.  Oznacza to, że wszelkie danych wejściowych dostarczonych przez klienta w żądaniu elementu webhook jest rejestrowane i dostępną dla wszystkich użytkowników z dostępem do zadanie usługi Automatyzacja.  Z tego powodu należy zachować ostrożność w wywołaniach elementu webhook w tym poufne informacje.
>

## <a name="security"></a>Bezpieczeństwo
Zabezpieczenia elementu webhook polega na prywatność adresu URL, który zawiera token zabezpieczający, który umożliwi można wywołać. Automatyzacja Azure nie wykonuje żadnego uwierzytelniania na żądanie tak długo, jak staje się poprawny adres URL. Z tego powodu nie można używać elementów webhook dla elementów runbook, które bardzo ważne zmiany funkcji bez używania alternatywnej metody sprawdzania poprawności żądania.

Można uwzględnić logiki w ramach elementu runbook w celu określenia, czy została wywołana przez elementu webhook, sprawdzając **WebhookName** właściwość parametru $WebhookData. Element runbook można wykonać dalsze weryfikacji przez wyszukiwanie szczegółowych informacji w **RequestHeader** lub **RequestBody** właściwości.

Kolejną strategią jest runbook weryfikowania niektórych warunków zewnętrznych podczas odebrała żądanie elementu webhook.  Rozważmy na przykład element runbook, który jest wywoływany przez GitHub, gdy istnieje nowy zatwierdzeń do repozytorium GitHub.  Element runbook może się połączyć GitHub, aby sprawdzić, czy nowe zatwierdzenia rzeczywista właśnie nastąpiło przed kontynuowaniem.

## <a name="creating-a-webhook"></a>Tworzenie elementu webhook
Użyj poniższej procedury, aby utworzyć nowy element webhook powiązany element runbook w portalu Azure.

1. Z **strony elementów Runbook** w portalu Azure kliknij element runbook, aby wyświetlić jego stronę szczegółów uruchomienia elementu webhook.
2. Kliknij przycisk **Webhook** w górnej części strony, aby otworzyć **Dodawanie elementu Webhook** strony. <br>
   ![Przycisk elementów Webhook](media/automation-webhooks/webhooks-button.png)
3. Kliknij przycisk **tworzenia nowego elementu webhook** otworzyć **tworzenia elementu webhook strony**.
4. Określ **nazwa**, **Data wygaśnięcia** dla elementu webhook i określa, czy powinno być włączone. Zobacz [szczegóły elementu webhook](#details-of-a-webhook) Aby uzyskać więcej informacji tych właściwości.
5. Kliknij ikonę kopiowania, a następnie naciśnij klawisze Ctrl + C, aby skopiować adres URL elementu webhook.  Następnie zapisz go w bezpiecznym miejscu.  **Po utworzeniu elementu webhook nie można ponownie pobrać adresu URL.** <br>
   ![Adres URL elementu Webhook](media/automation-webhooks/copy-webhook-url.png)
6. Kliknij przycisk **parametry** podać wartości parametrów elementu runbook.  Jeśli element runbook ma parametry obowiązkowe, następnie nie można utworzyć elementu webhook, chyba że znajdują się wartości.
7. Kliknij przycisk **Utwórz** można utworzyć elementu webhook.

## <a name="using-a-webhook"></a>Przy użyciu elementu webhook
Aby użyć elementu webhook po jego utworzeniu, aplikacja kliencka należy wygenerować HTTP POST z adresem URL dla elementu webhook.  Składnia elementu webhook będzie w następującym formacie.

    http://<Webhook Server>/token?=<Token Value>

Klient zostanie wyświetlony jeden z następujących kody powrotu z żądania POST.  

| Kod | Tekst | Opis |
|:--- |:--- |:--- |
| 202 |Zaakceptowany |Żądanie zostało zaakceptowane, a element runbook został pomyślnie w kolejce. |
| 400 |Nieprawidłowe żądanie |Nie zaakceptowano żądanie dla jednego z następujących powodów. <ul> <li>Element webhook wygasł.</li> <li>Elementu webhook jest wyłączona.</li> <li>Token w adresie URL jest nieprawidłowy.</li>  </ul> |
| 404 |Nie znaleziono |Nie zaakceptowano żądanie dla jednego z następujących powodów. <ul> <li>Nie można odnaleźć elementu webhook.</li> <li>Nie znaleziono elementu runbook.</li> <li>Nie można odnaleźć konta.</li>  </ul> |
| 500 |Wewnętrzny błąd serwera |Adres URL jest prawidłowy, ale wystąpił błąd.  Prześlij żądanie. |

Zakładając, że żądanie zakończy się pomyślnie, odpowiedzi elementu webhook zawiera identyfikator zadania w formacie JSON w następujący sposób. Będzie zawierać identyfikator pojedyncze zadanie, ale umożliwia potencjalne przyszłe ulepszenia formatu JSON.

    {"JobIds":["<JobId>"]}  

Klient nie może określić po zakończeniu zadania elementu runbook lub jego stanie ukończenia z elementu webhook.  Można określić te informacje przy użyciu identyfikatora zadania przy użyciu innej metody takie jak [programu Windows PowerShell](http://msdn.microsoft.com/library/azure/dn690263.aspx) lub [interfejsu API usługi Automatyzacja Azure](https://msdn.microsoft.com/library/azure/mt163826.aspx).

### <a name="example"></a>Przykład
W poniższym przykładzie użyto programu Windows PowerShell do uruchamiania elementu runbook z elementu webhook.  Można użyć dowolnego języka, który może zgłaszać żądania HTTP elementu webhook; Środowisko Windows PowerShell jest używany po prostu poniższym przykładzie.

Element runbook jest Oczekiwano listy maszyn wirtualnych zapisany w formacie JSON w treści żądania. Możemy także są w tym informacje o który uruchamia element runbook oraz datę i godzinę, które jest uruchamiana w nagłówku żądania.      

    $uri = "https://s1events.azure-automation.net/webhooks?token=8ud0dSrSo%2fvHWpYbklW%3c8s0GrOKJZ9Nr7zqcS%2bIQr4c%3d"
    $headers = @{"From"="user@contoso.com";"Date"="05/28/2015 15:47:00"}

    $vms  = @(
                @{ Name="vm01";ServiceName="vm01"},
                @{ Name="vm02";ServiceName="vm02"}
            )
    $body = ConvertTo-Json -InputObject $vms

    $response = Invoke-RestMethod -Method Post -Uri $uri -Headers $headers -Body $body
    $jobid = ConvertFrom-Json $response


Na poniższej ilustracji przedstawiono informacje o nagłówku (przy użyciu [Fiddler](http://www.telerik.com/fiddler) śledzenia) z tego żądania. W tym standardowych nagłówków żądania HTTP, oprócz niestandardowa data i z nagłówków, które dodano.  Każdy z tych wartości jest dostępny dla elementu runbook w **RequestHeaders** właściwość **WebhookData**.

![Przycisk elementów Webhook](media/automation-webhooks/webhook-request-headers.png)

Na poniższej ilustracji przedstawiono treść żądania (przy użyciu [Fiddler](http://www.telerik.com/fiddler) śledzenia) dostępnej do elementu runbook w **RequestBody** właściwość **WebhookData**. To jest w formacie JSON powodu formatu, który został uwzględniony w treści żądania.     

![Przycisk elementów Webhook](media/automation-webhooks/webhook-request-body.png)

Na poniższej ilustracji przedstawiono żądania wysyłane z programu Windows PowerShell i wynikowy odpowiedzi.  Identyfikator zadania jest wyodrębniana z odpowiedzi i konwertowana na ciąg.

![Przycisk elementów Webhook](media/automation-webhooks/webhook-request-response.png)

Następujący przykładowy element runbook akceptuje poprzednie przykładowe żądanie i uruchamiania maszyn wirtualnych, określona w treści żądania.

    workflow Test-StartVirtualMachinesFromWebhook
    {
        param (
            [object]$WebhookData
        )

        # If runbook was called from Webhook, WebhookData will not be null.
        if ($WebhookData -ne $null) {

            # Collect properties of WebhookData
            $WebhookName     =     $WebhookData.WebhookName
            $WebhookHeaders =     $WebhookData.RequestHeader
            $WebhookBody     =     $WebhookData.RequestBody

            # Collect individual headers. VMList converted from JSON.
            $From = $WebhookHeaders.From
            $VMList = ConvertFrom-Json -InputObject $WebhookBody
            Write-Output "Runbook started from webhook $WebhookName by $From."

            # Authenticate to Azure resources
            $Cred = Get-AutomationPSCredential -Name 'MyAzureCredential'
            Add-AzureAccount -Credential $Cred

            # Start each virtual machine
            foreach ($VM in $VMList)
            {
                $VMName = $VM.Name
                Write-Output "Starting $VMName"
                Start-AzureVM -Name $VM.Name -ServiceName $VM.ServiceName
            }
        }
        else {
            Write-Error "Runbook mean to be started only from webhook."
        }
    }


## <a name="starting-runbooks-in-response-to-azure-alerts"></a>Uruchamianie elementów runbook w odpowiedzi na alerty Azure
Włączone elementu Webhook elementów runbook może służyć do reagowania na [Azure alerty](../monitoring-and-diagnostics/insights-receive-alert-notifications.md). Zasobami na platformie Azure mogą być monitorowane przez zbieranie statystyk, takich jak wydajności, dostępności i użyciu za pomocą usługi Azure alerty. Otrzymasz alert w oparciu metryki monitorowania lub zdarzenia dla zasobów platformy Azure, obecnie kont automatyzacji obsługuje tylko metryki. Jeśli wartość określonej metryki przekracza próg przypisane lub skonfigurowany zdarzenie jest wyzwalane, a następnie powiadomienie jest wysyłane do administratora usługi lub współadministratorzy Aby rozwiązać alert, aby uzyskać więcej informacji na temat metryki i zdarzenia zapoznaj się [ Alerty Azure](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

Oprócz za pomocą usługi Azure alerty jako system powiadomień, należy również rozpocząć się poza elementów runbook w odpowiedzi na alerty. Automatyzacja Azure oferuje możliwość uruchamiania włączone elementu webhook elementów runbook z alertami platformy Azure. Jeśli Metryka przekracza wartość skonfigurowany próg następnie reguły alertu staje się aktywny i wyzwala webhook usługi Automatyzacja, który z kolei wykonuje element runbook.

![elementów webhook](media/automation-webhooks/webhook-alert.jpg)

### <a name="alert-context"></a>Kontekst alertu
Należy rozważyć zasobów platformy Azure, takich jak maszyny wirtualnej, użycie procesora CPU tego komputera jest jednym z metryki wydajności. Jeśli wykorzystanie procesora CPU jest 100% lub więcej niż określonym przez długi czas, możesz uruchomić ponownie maszynę wirtualną, aby rozwiązać ten problem. To będzie możliwe przez skonfigurowanie reguły alertu do maszyny wirtualnej i procent użycia procesora CPU, jako jego metryka ma ta reguła. Procent użycia procesora CPU w tym miejscu jest po prostu wykonywanej na przykład, ale istnieje wiele innych metryk, które można skonfigurować do zasobów platformy Azure i ponownego uruchamiania maszyny wirtualnej jest akcja, która jest wykonywana, aby rozwiązać ten problem, można skonfigurować element runbook, aby wykonywać inne czynności.

Gdy to reguły alertu staje się aktywny i wyzwala runbook włączone elementu webhook wysyła kontekst alertu w elemencie runbook. [Kontekst alertu](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) zawiera szczegółowe informacje, łącznie z **SubscriptionID**, **ResourceGroupName**, **ResourceName**, **ResourceType**, **ResourceId** i **sygnatury czasowej** jest wymagane dla elementu runbook do identyfikacji zasobu, na którym będzie biorąc pod akcji. Alert kontekstu jest osadzony w części treści **WebhookData** obiektu wysyłanego do elementu runbook i jest dostępny z **Webhook.RequestBody** właściwości

### <a name="example"></a>Przykład
Utwórz maszynę wirtualną platformy Azure w subskrypcji i powiąż [alert, aby monitorować metryki procent procesora CPU](../monitoring-and-diagnostics/insights-receive-alert-notifications.md). Podczas tworzenia alertu upewnij się, że wypełnić pole elementu webhook z adresem URL elementu webhook, który został wygenerowany podczas tworzenia elementu webhook.

Następujący przykładowy element runbook jest wyzwalane, gdy reguła alertu staje się aktywny i zbiera parametrów kontekstu alertu, które są wymagane dla elementu runbook do identyfikacji zasobu, na którym będzie biorąc pod akcji.

    workflow Invoke-RunbookUsingAlerts
    {
        param (      
            [object]$WebhookData
        )

        # If runbook was called from Webhook, WebhookData will not be null.
        if ($WebhookData -ne $null) {   
            # Collect properties of WebhookData.
            $WebhookName    =   $WebhookData.WebhookName
            $WebhookBody    =   $WebhookData.RequestBody
            $WebhookHeaders =   $WebhookData.RequestHeader

            # Outputs information on the webhook name that called This
            Write-Output "This runbook was started from webhook $WebhookName."


            # Obtain the WebhookBody containing the AlertContext
            $WebhookBody = (ConvertFrom-Json -InputObject $WebhookBody)
            Write-Output "`nWEBHOOK BODY"
            Write-Output "============="
            Write-Output $WebhookBody

            # Obtain the AlertContext     
            $AlertContext = [object]$WebhookBody.context

            # Some selected AlertContext information
            Write-Output "`nALERT CONTEXT DATA"
            Write-Output "==================="
            Write-Output $AlertContext.name
            Write-Output $AlertContext.subscriptionId
            Write-Output $AlertContext.resourceGroupName
            Write-Output $AlertContext.resourceName
            Write-Output $AlertContext.resourceType
            Write-Output $AlertContext.resourceId
            Write-Output $AlertContext.timestamp

            # Act on the AlertContext data, in our case restarting the VM.
            # Authenticate to your Azure subscription using Organization ID to be able to restart that Virtual Machine.
            $cred = Get-AutomationPSCredential -Name "MyAzureCredential"
            Add-AzureAccount -Credential $cred
            Select-AzureSubscription -subscriptionName "Visual Studio Ultimate with MSDN"

            #Check the status property of the VM
            Write-Output "Status of VM before taking action"
            Get-AzureVM -Name $AlertContext.resourceName -ServiceName $AlertContext.resourceName
            Write-Output "Restarting VM"

            # Restart the VM by passing VM name and Service name which are same in this case
            Restart-AzureVM -ServiceName $AlertContext.resourceName -Name $AlertContext.resourceName
            Write-Output "Status of VM after alert is active and takes action"
            Get-AzureVM -Name $AlertContext.resourceName -ServiceName $AlertContext.resourceName
        }
        else  
        {
            Write-Error "This runbook is meant to only be started from a webhook."  
        }  
    }



## <a name="next-steps"></a>Następne kroki
* Aby uzyskać więcej informacji na różne sposoby uruchamiania elementu runbook, zobacz [uruchamianie elementu Runbook](automation-starting-a-runbook.md).
* Aby uzyskać informacje o wyświetlaniu stanu zadania elementu Runbook, zapoznaj się [wykonanie elementu Runbook automatyzacji Azure](automation-runbook-execution.md).
* Aby dowiedzieć się, jak używać usługi Automatyzacja Azure do wykonania akcji na alerty Azure, zobacz [skorygować alerty maszyny Wirtualnej Azure z elementu Runbook usługi automatyzacja](automation-azure-vm-alert-integration.md).
