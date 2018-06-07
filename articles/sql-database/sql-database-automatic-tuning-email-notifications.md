---
title: Przewodnik powiadomienia - automatycznego dostrajania e-mail bazy danych SQL Azure | Dokumentacja firmy Microsoft
description: Baza danych SQL Azure analizuje zapytania SQL i automatycznie dostosowuje się do użytkownika obciążenia.
services: sql-database
author: danimir
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 02/05/2018
ms.author: v-daljep
ms.openlocfilehash: 643740ea76769f857e8c99ebaa6d27eceed99067
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34644335"
---
# <a name="email-notifications-for-automatic-tuning"></a>Powiadomienia e-mail dotyczące automatycznego dostrajania

Baza danych SQL dostrajania zalecenia są generowane przez bazę danych SQL Azure [automatycznego dostrajania](sql-database-automatic-tuning.md). To rozwiązanie stale monitoruje i analizuje obciążeń udostępniania bazy danych SQL, dostosowane dostrajanie zalecenia dotyczące każdej poszczególne bazy danych związanych z Tworzenie indeksu i usuwania indeksu oraz optymalizacji planów wykonania zapytania.

Dostrajanie zalecenia SQL bazy danych automatycznego można wyświetlić w [portalu Azure](sql-database-advisor-portal.md), pobrane z [interfejsu API REST](https://docs.microsoft.com/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) wywołuje lub przy użyciu [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) i [ PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqldatabaserecommendedaction) poleceń. W tym artykule jest oparta na użyciu skryptu programu PowerShell do pobrania automatycznego dostrajania zalecenia.

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>Powiadomienia e-mail dotyczące automatycznego dostrajania zalecenia dotyczące automatyzacji

Następujące rozwiązania automatyzuje wysyłania powiadomień e-mail zawierające automatycznego dostrajania zalecenia. Opisanego rozwiązania składa się z automatyzacji wykonywania skryptu programu PowerShell do pobierania dostrajania zalecenia za pomocą [usługi Automatyzacja Azure](https://docs.microsoft.com/azure/automation/automation-intro), i automatyzacji planowania poczty e-mail przy użyciu zadania dostarczania [Flow firmy Microsoft ](https://flow.microsoft.com).

## <a name="create-azure-automation-account"></a>Utwórz konto usługi Automatyzacja Azure

Aby korzystać z usługi Automatyzacja Azure, pierwszym krokiem jest utworzyć konto usługi Automatyzacja i skonfigurować go z zasobów platformy Azure na potrzeby wykonywania skryptu środowiska PowerShell. Aby dowiedzieć się więcej na temat usługi Automatyzacja Azure i jej możliwości, zobacz [wprowadzenie do korzystania z usługi Automatyzacja Azure](https://docs.microsoft.com/azure/automation/automation-offering-get-started).

Wykonaj następujące kroki, aby utworzyć konto usługi Automatyzacja Azure za pomocą metody wybranie i skonfigurowanie automatyzacja aplikacji z portalu Marketplace:

- Zaloguj się do portalu Azure
- Kliknij pozycję "**+ Utwórz zasób**" w lewym górnym rogu
- Wyszukaj "**automatyzacji**" (naciśnij klawisz enter)
- Kliknij aplikację automatyzacji w wynikach wyszukiwania

![Dodawanie usługi Automatyzacja Azure](./media/sql-database-automatic-tuning-email-notifications/howto-email-01.png)

- Raz kliknij w okienku "Utwórz konto automatyzacji" na "**Utwórz**"
- Wypełnij wymagane informacje: Wprowadź nazwę dla tego konta automatyzacji, wybierz zasobów identyfikator i Azure subskrypcji platformy Azure do zastosowania w przypadku wykonywania skryptów programu PowerShell
- Dla "**tworzenia konta Uruchom jako Azure**" Wybierz **tak** skonfigurować typ konta, w których PowerShell skrypt jest uruchamiany za pomocą usługi Automatyzacja Azure. Aby dowiedzieć się więcej na temat typów kont, zobacz [konta Uruchom jako](https://docs.microsoft.com/azure/automation/automation-create-runas-account)
- Kończenie tworzenia konta automatyzacji przez kliknięcie **Utwórz**

> [!TIP]
> Zarejestruj w formie wprowadzonej podczas tworzenia aplikacji automatyzacji z nazwą konta usługi Automatyzacja Azure, identyfikator subskrypcji i zasobów (takich jak kopiowania i wklejania do Notatnika). Należy później tych informacji.
>

Jeśli masz wiele subskrypcji platformy Azure, dla których chcesz tworzenie automatyzacji tej samej należy powtórzyć ten proces dla innych subskrypcji.

## <a name="update-azure-automation-modules"></a>Aktualizuj moduły usługi Automatyzacja Azure

Skrypt programu PowerShell do pobrania automatycznego dostrajania zalecenie używa [Get-AzureRmResource](https://docs.microsoft.com/powershell/module/AzureRM.Resources/Get-AzureRmResource) i [Get AzureRmSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Get-AzureRmSqlDatabaseRecommendedAction) poleceń, dla których aktualizacji modułów Azure w wersji 4 lub nowszy jest wymagany.

Wykonaj następujące kroki, aby zaktualizować modułów programu Azure PowerShell:

- Dostępu do automatyzacji okienka aplikacji, a następnie wybierz pozycję "**modułów**" w menu po lewej stronie (Przewiń w dół tego elementu menu podlega udostępnionych zasobów).
- W okienku modułów, kliknij polecenie "**aktualizacji modułów Azure**" u góry i zaczekaj, aż zostanie wyświetlony komunikat "zostały zaktualizowane moduły Azure". Ten proces może potrwać kilka minut.

![Aktualizuj moduły usługi Automatyzacja Azure](./media/sql-database-automatic-tuning-email-notifications/howto-email-02.png)

Wymagana wersja AzureRM.Resources i AzureRM.Sql modułów wymaga wersji 4 lub nowszym.

## <a name="create-azure-automation-runbook"></a>Tworzenie elementu Runbook usługi Automatyzacja Azure

Następnym krokiem jest utworzyć element Runbook automatyzacji Azure, w którym znajduje się skrypt programu PowerShell do pobrania dostrajanie zalecenia.

Wykonaj następujące kroki, aby utworzyć nowy element runbook usługi Automatyzacja Azure:

- Dostęp do utworzonego w poprzednim kroku konta usługi Automatyzacja Azure
- Raz w okienku konta automatyzacji, kliknij na "**elementów Runbook**" z menu po lewej stronie, aby utworzyć nowy element runbook usługi Automatyzacja Azure za pomocą skryptu programu PowerShell. Aby dowiedzieć się więcej na temat tworzenia elementów runbook automatyzacji, zobacz [tworzenia nowego elementu runbook](../automation/automation-creating-importing-runbook.md).
- Aby dodać nowy element runbook, kliknij na "**+ Dodaj element runbook**" opcji menu, a następnie kliknij na "**szybkie tworzenie — Utwórz nowy element runbook**".
- W okienku elementu Runbook, wpisz nazwę elementu runbook (na potrzeby tego przykładu "**AutomaticTuningEmailAutomation**" jest używany), wybierz typ elementu runbook jako **PowerShell** i podaj opis Ten element runbook do opisywania jego przeznaczenia.
- Polecenie **Utwórz** przycisk, aby utworzyć nowy element runbook

![Dodaj element runbook usługi Automatyzacja Azure](./media/sql-database-automatic-tuning-email-notifications/howto-email-03.png)

Wykonaj następujące kroki, które można załadować skryptu programu PowerShell wewnątrz elementu runbook utworzone:

- Wewnątrz "**edytować element Runbook programu PowerShell**"okienku, wybierz opcję"**RUNBOOK**" w menu drzewa, a następnie rozwiń węzeł widoku, aż zostanie wyświetlony nazwa elementu runbook (w tym przykładzie " **AutomaticTuningEmailAutomation**"). Wybierz ten element runbook.
- W pierwszym wierszu "Edytuj Runbook programu PowerShell" (rozpoczyna się o numerze 1) kopiowania i wklejania w poniższym kodzie skryptu programu PowerShell. Ten skrypt programu PowerShell jest dostarczany jako — jest ułatwiających rozpoczęcie pracy. Zmodyfikuj skrypt do zestawu z potrzebami.

W nagłówku udostępnionego skryptu programu PowerShell, należy zastąpić `<SUBSCRIPTION_ID_WITH_DATABASES>` z identyfikatorem subskrypcji platformy Azure Aby dowiedzieć się, jak pobrać identyfikator subskrypcji platformy Azure, zobacz [pobierania Twojego identyfikatora GUID subskrypcji Azure](https://blogs.msdn.microsoft.com/mschray/2016/03/18/getting-your-azure-subscription-guid-new-portal/).

W przypadku kilku subskrypcji można dodać je jako rozdzielana przecinkami z właściwością "$subscriptions" w nagłówku pliku skryptu.

```PowerShell
# PowerShell script to retrieve Azure SQL Database Automatic tuning recommendations.
#
# Provided “as-is” with no implied warranties or support.
# The script is released to the public domain.
#
# Replace <SUBSCRIPTION_ID_WITH_DATABASES> in the header with your Azure subscription ID.
#
# Microsoft Azure SQL Database team, 2018-01-22.

# Set subscriptions : IMPORTANT – REPLACE <SUBSCRIPTION_ID_WITH_DATABASES> WITH YOUR SUBSCRIPTION ID 
$subscriptions = ("<SUBSCRIPTION_ID_WITH_DATABASES>", "<SECOND_SUBSCRIPTION_ID_WITH_DATABASES>", "<THIRD_SUBSCRIPTION_ID_WITH_DATABASES>")

# Get credentials
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Define the resource types
$resourceTypes = ("Microsoft.Sql/servers/databases")
$advisors = ("CreateIndex", "DropIndex");
$results = @()

# Loop through all subscriptions
foreach($subscriptionId in $subscriptions) {    
    Select-AzureRmSubscription -SubscriptionId $subscriptionId    
    $rgs = Get-AzureRmResourceGroup

    # Loop through all resource groups
    foreach($rg in $rgs) {
        $rgname = $rg.ResourceGroupName;

        # Loop through all resource types
        foreach($resourceType in $resourceTypes) {
            $resources = Get-AzureRmResource -ResourceGroupName $rgname -ResourceType $resourceType    

            # Loop through all databases
            # Extract resource groups, servers and databases
            foreach ($resource in $resources) {
                $resourceId = $resource.ResourceId
                if ($resourceId -match ".*RESOURCEGROUPS/(?<content>.*)/PROVIDERS.*") {
                    $ResourceGroupName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*SERVERS/(?<content>.*)/DATABASES.*") {
                    $ServerName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*/DATABASES/(?<content>.*)") {
                    $DatabaseName = $matches['content']
                } else {
                    continue 
                }

                # Skip if master
                if ($DatabaseName -eq "master") {
                    continue
                }

                # Loop through all Automatic tuning recommendation types
                foreach ($advisor in $advisors) {
                    $recs = Get-AzureRmSqlDatabaseRecommendedAction -ResourceGroupName $ResourceGroupName -ServerName $ServerName  -DatabaseName $DatabaseName -AdvisorName $advisor
                    foreach ($r in $recs) {
                        if ($r.State.CurrentValue -eq "Active") {
                            $object = New-Object -TypeName PSObject
                            $object | Add-Member -Name 'SubscriptionId' -MemberType Noteproperty -Value $subscriptionId
                            $object | Add-Member -Name 'ResourceGroupName' -MemberType Noteproperty -Value $r.ResourceGroupName
                            $object | Add-Member -Name 'ServerName' -MemberType Noteproperty -Value $r.ServerName
                            $object | Add-Member -Name 'DatabaseName' -MemberType Noteproperty -Value $r.DatabaseName
                            $object | Add-Member -Name 'Script' -MemberType Noteproperty -Value $r.ImplementationDetails.Script
                            $results += $object
                        }
                    }
                }                
            }
        }
    }
}

# Format and output results for the email
$table = $results | Format-List
Write-Output $table
```

Kliknij przycisk "**zapisać**" przycisk w prawym górnym rogu, aby zapisać skrypt. Po zakończeniu ze skryptem, kliknij przycisk "**publikowania**" przycisk, aby opublikować ten element runbook. 

W okienku głównym elementu runbook można kliknij pozycję "**Start**" znajdujący się **test** skryptu. Kliknij pozycję "**dane wyjściowe**" Aby wyświetlić wyniki skrypt wykonywany. Te dane wyjściowe ma być zawartości wiadomości e-mail. Przykładowe dane wyjściowe skryptu widać na poniższym zrzucie ekranu.

![Uruchom widok automatycznego dostrajania zalecenia w usłudze Automatyzacja Azure](./media/sql-database-automatic-tuning-email-notifications/howto-email-04.png)

Upewnij się, aby dostosować zawartość dostosowując skrypt programu PowerShell do własnych potrzeb.

Z powyższych kroków skrypt programu PowerShell, aby pobrać automatycznego dostrajania zalecenia jest ładowany w automatyzacji Azure. Następnym krokiem jest zautomatyzować i zaplanować zadanie dostarczania poczty e-mail.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>Automatyzowanie zadań wiadomości e-mail z Flow firmy Microsoft

Do ukończenia rozwiązanie, co stanowi ostatni krok, należy utworzyć przepływ automatyzacji w Flow Microsoft składające się z trzech akcji (zadania): 

1. "**Usługi Automatyzacja azure — Tworzenie zadania**" — używane do uruchomienia skryptu programu PowerShell do pobrania automatycznego dostrajania zalecenia wewnątrz elementu runbook usługi Automatyzacja Azure
2. "**Usługi Automatyzacja azure - dane wyjściowe zadania Get**" — używane do pobierania danych wyjściowych z wykonanie skryptu PowerShell
3. "**Office 365 Outlook — Wyślij usługi poczty e-mail**" — używane do wysyłania wiadomości e-mail. Wiadomości e-mail są wysyłane przy użyciu konta usługi Office 365, osoby, której Tworzenie przepływu.

Aby dowiedzieć się więcej o możliwościach Flow firmy Microsoft, zobacz [wprowadzenie Microsoft Flow](https://docs.microsoft.com/flow/getting-started).

Warunkiem wstępnym tego kroku jest do zarejestrowania się w [Microsoft Flow](https://flow.microsoft.com) konta i zalogowania. Raz wewnątrz rozwiązania, wykonaj następujące kroki, aby skonfigurować **nowy przepływ**:

- Dostęp "**Moje przepływów**" elementu menu
- Wewnątrz Moje przepływów, wybierz "**+ Utwórz z puste**" znajdujący się w górnej części strony
- Kliknij łącze "**Wyszukaj setki łączników i wyzwalaczy**" u dołu strony
- W polu wyszukiwania wpisz "**cyklu**" i wybierz pozycję "**harmonogram - cyklu**" z wyników wyszukiwania, aby zaplanować uruchomienie zadania dostarczania poczty e-mail.
- W okienku cyklu w polu Częstotliwość wybierz częstotliwość harmonogramu dla tego przepływu do wykonania, takie jak automatyczne wysyłanie wiadomości e-mail każdego minuty, godziny, dnia, tygodnia, itp.

Następnym krokiem jest można dodać trzy zadania (Tworzenie, dane wyjściowe get i wysyłania wiadomości e-mail) do nowo utworzony przepływ cyklicznego. Aby osiągnąć Dodawanie wymaganych zadań z przepływem, wykonaj następujące kroki:

1. Akcja do wykonania skryptu PowerShell do pobrania dostrajania zalecenia dotyczące tworzenia
- Wybierz opcję "**+ nowy krok**", a następnie"**Dodaj akcję**" wewnątrz okienka przepływu cyklu
- W polu wyszukiwania wpisz "**automatyzacji**"i wybierz"**usługi Automatyzacja Azure — Tworzenie zadania**" w wynikach wyszukiwania
- W okienku zadania tworzenia Skonfiguruj właściwości zadania. W przypadku tej konfiguracji należy Szczegóły subskrypcji platformy Azure identyfikator, grupy zasobów i konto automatyzacji **uprzednio zarejestrowane** w **okienko konta automatyzacji**. Aby dowiedzieć się więcej na temat opcji dostępnych w tej sekcji, zobacz [usługi Automatyzacja Azure - Utwórz zadanie](https://docs.microsoft.com/connectors/azureautomation/#Create_job).
- Ukończyć tworzenia tej akcji, klikając "**zapisać przepływ**"

2. Utworzenie pobrać dane wyjściowe skryptu programu PowerShell wykonanego
- Wybierz opcję "**+ nowy krok**", a następnie"**Dodaj akcję**" wewnątrz okienka przepływu cyklu
- W wyszukiwaniu zachowano typu "**automatyzacji**"i wybierz"**usługi Automatyzacja Azure — dane wyjściowe zadania Get**" w wynikach wyszukiwania. Aby dowiedzieć się więcej na temat opcji dostępnych w tej sekcji, zobacz [usługi Automatyzacja Azure — dane wyjściowe zadania Get](https://docs.microsoft.com/connectors/azureautomation/#Get_job_output).
- Wypełnij pola wymagane (podobnie jak poprzednie zadanie tworzenia) — wypełnić Twojej subskrypcji Azure identyfikator, grupy zasobów oraz konto automatyzacji (jak został wprowadzony w okienku konta automatyzacji)
- Kliknij wewnątrz pola "**zadania o identyfikatorze**" dla "**zawartości dynamicznej**" menu wyświetlone. W tym menu, zaznacz opcję "**zadania o identyfikatorze**".
- Ukończyć tworzenia tej akcji, klikając "**zapisać przepływ**"

3. Utworzenie wysyłanie poczty e-mail przy użyciu integracji z usługą Office 365
- Wybierz opcję "**+ nowy krok**", a następnie"**Dodaj akcję**" wewnątrz okienka przepływu cyklu
- W wyszukiwaniu zachowano typu "**wysłać wiadomość e-mail**"i wybierz"**Office 365 Outlook — Wyślij usługi poczty e-mail**" w wynikach wyszukiwania
- W "**do**" Typ w adresie e-mail, do którego należy wysłać powiadomienie e-mail pola
- W "**podmiotu**" w temacie wiadomości e-mail, na przykład "automatycznego dostrajania zalecenia powiadomienia e-mail" typ pola
- Kliknij wewnątrz pola "**treści**" dla "**zawartości dynamicznej**" menu wyświetlone. Z wewnątrz tego menu, w obszarze "**uzyskać dane wyjściowe zadania**", wybierz opcję"**zawartości**" 
- Ukończyć tworzenia tej akcji, klikając "**zapisać przepływ**"

> [!TIP]
> Aby wysłać zautomatyzowane wiadomości e-mail do odbiorców, Utwórz oddzielne przepływów. Te dodatkowe przepływów zmiany adresów e-mail adresatów w polu "Do", a wiersz tematu wiadomości e-mail w polu "Temat". Tworzenie nowych elementów runbook w automatyzacji Azure z dostosowane skrypty programu PowerShell (takie jak zmiana identyfikatora subskrypcji platformy Azure) umożliwia dalsze dostosowanie automatycznych scenariusze, takie jest na przykład wysyłanie oddzielne adresatów automatycznego dostrajania zalecenia dotyczące oddzielne subskrypcji.
>

Powyższe zawiera kroki wymagane do skonfigurowania przepływie pracy zadania dostarczania poczty e-mail. Cały przepływ, składające się z trzech akcje wbudowane pokazano na poniższej ilustracji.

![Widok automatycznego dostrajania przepływu powiadomienia e-mail](./media/sql-database-automatic-tuning-email-notifications/howto-email-05.png)

Aby przetestować przepływ, wybierz polecenie "**Uruchom teraz**" w górnym okienku przepływu.

Statystyki uruchomionych zadań automatycznych, przedstawiający Powodzenie powiadomień e-mail wysłanych, może być widoczny w okienku analytics przepływu.

![Uruchomiony przepływ automatycznego dostrajania powiadomień e-mail](./media/sql-database-automatic-tuning-email-notifications/howto-email-06.png)

Analiza przepływu jest przydatne w przypadku powodzenia wykonania zadań monitorowania i w razie potrzeby w celu rozwiązania problemu.  W przypadku rozwiązywania problemów, można również sprawdzać dostępne za pośrednictwem aplikacji usługi Automatyzacja Azure w dzienniku wykonywania skryptu programu PowerShell.

Ostateczne dane wyjściowe zautomatyzowane wiadomości e-mail wygląda podobnie do następującą wiadomość e-mail po tworzenia i uruchamiania tego rozwiązania:

![Przykładowe dane wyjściowe wiadomości e-mail z automatycznego dostrajania powiadomienia e-mail](./media/sql-database-automatic-tuning-email-notifications/howto-email-07.png)

Dostosowując skrypt programu PowerShell, można dostosować dane wyjściowe i formatowanie automatyczne wiadomości e-mail do własnych potrzeb.

Rozwiązania do tworzenia powiadomienia e-mail, na podstawie określonego zdarzenia dostrajania i do wielu adresatów dla wielu subskrypcji lub baz danych, w zależności od niestandardowych scenariuszy może dostosować. 

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej na dostrajanie jak automatyczne może pomóc zwiększyć wydajność bazy danych, zobacz [automatycznego dostrajania w bazie danych SQL Azure](sql-database-automatic-tuning.md).
- Aby włączyć automatyczne strojenia w bazie danych SQL Azure do zarządzania obciążenie, zobacz [Włączanie automatycznego dostrajania](sql-database-automatic-tuning-enable.md).
- Aby ręcznie Przejrzyj i Zastosuj automatycznego dostrajania zalecenia, zobacz [Znajdowanie i stosować zalecenia wydajności](sql-database-advisor-portal.md).
