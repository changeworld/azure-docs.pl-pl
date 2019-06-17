---
title: Dostrajanie automatyczne usługi poczty e-mail powiadomień przewodniku z instrukcjami — usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Włącz powiadomienia e-mail dostrajania automatycznego zapytań usługi Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 06/03/2019
ms.openlocfilehash: 0050745ea9d624adb6b7a28d5db91924d1c54b0a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66479449"
---
# <a name="email-notifications-for-automatic-tuning"></a>Powiadomienia e-mail dotyczące automatycznego dostrajania

Zalecenia dotyczące dostrajania są generowane przez usługę Azure SQL Database bazy danych SQL [dostrajania automatycznego](sql-database-automatic-tuning.md). To rozwiązanie stale monitoruje i analizuje obciążeń związanych z udostępnianiem baz danych SQL, dostosowane zaleceń dotyczących każdej pojedynczej bazy danych związanych z tworzenia indeksu, usunięcie indeksu i optymalizacji plany wykonywania zapytań dostosowywania.

SQL Database zalecenia dostrajania automatycznego usługi mogą być wyświetlane w [witryny Azure portal](sql-database-advisor-portal.md), pobrany z [interfejsu API REST](https://docs.microsoft.com/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) wywołuje lub przy użyciu [języka T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) i [ Program PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction) poleceń. Ten artykuł jest oparty na przy użyciu skryptu programu PowerShell, aby pobrać zalecenia dostrajania automatycznego.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł programu PowerShell usługi Azure Resource Manager jest nadal obsługiwane przez usługę Azure SQL Database, ale wszystkie przyszłego rozwoju jest Az.Sql modułu. Dla tych poleceń cmdlet, zobacz [elementu AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w Az module, a w modułach AzureRm są zasadniczo identyczne.

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>Automatyzowanie powiadomienia e-mail dotyczące automatycznego zalecenia dotyczące dostrajania

Następujące rozwiązania automatyzuje wysyłania powiadomienia e-mail zawierające zalecenia dostrajania automatycznego. Rozwiązanie przedstawione składa się z automatyzacji wykonywania skryptu programu PowerShell do pobierania zalecenia dotyczące dostrajania za pomocą [usługi Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro), i wyśle wiadomość e-mail automatyzacji planowania, dostarczania zadania za pomocą polecenia [Microsoft Flow ](https://flow.microsoft.com).

## <a name="create-azure-automation-account"></a>Tworzenie konta usługi Azure Automation

Aby korzystać z usługi Azure Automation, pierwszym krokiem jest utworzenie konta usługi automation i jest skonfigurowana z zasobami platformy Azure do użycia dla wykonania skryptu programu PowerShell. Aby dowiedzieć się więcej na temat usługi Azure Automation i jego możliwości, zobacz [wprowadzenie do usługi Azure automation](https://docs.microsoft.com/azure/automation/automation-offering-get-started).

Wykonaj następujące kroki, aby utworzyć konto usługi Azure Automation za pośrednictwem metody wyboru i konfigurowanie aplikacji usługi Automation z witryny Marketplace:

- Zaloguj się do witryny Azure portal
- Kliknij pozycję " **+ Utwórz zasób**" w lewym górnym rogu
- Wyszukaj "**automatyzacji**" (naciśnij klawisz enter)
- Kliknij aplikację automatyzacji w wynikach wyszukiwania

![Dodawanie usługi Azure automation](./media/sql-database-automatic-tuning-email-notifications/howto-email-01.png)

- Jeden raz w okienku "Utwórz konto usługi Automation" kliknij pozycję "**Utwórz**"
- Wypełnij wymagane informacje: Wprowadź nazwę dla tego konta usługi automation, wybierz subskrypcję platformy Azure identyfikator i Azure zasobami służący do wykonywania skryptów programu PowerShell
- Dla "**Tworzenie konta Uruchom jako platformy**" Wybierz **tak** skonfigurować typ konta, w którego programie PowerShell skrypt jest uruchamiany za pomocą usługi Azure Automation. Aby dowiedzieć się więcej na temat typów kont, zobacz [konta Uruchom jako](https://docs.microsoft.com/azure/automation/automation-create-runas-account)
- Kończenie tworzenia konta usługi automation przez kliknięcie **Create**

> [!TIP]
> Zapisz w formie wprowadzonej podczas tworzenia aplikacji automatyzacji swojej nazwy konta usługi Azure Automation, identyfikator subskrypcji i zasobów (takich jak kopiowanie i wklejanie do Notatnika). Należy później tych informacji.
>

Jeśli masz kilka subskrypcji platformy Azure, dla których chcesz się tego samego automatyzacji kompilacji, należy powtórzyć ten proces dla innych subskrypcji.

## <a name="update-azure-automation-modules"></a>Aktualizowanie modułów usługi Azure Automation

Skrypt programu PowerShell, aby pobrać zalecenia dostrajania automatycznego używa [Get AzResource](https://docs.microsoft.com/powershell/module/az.Resources/Get-azResource) i [Get AzSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlDatabaseRecommendedAction) polecenia dla modułu, którego platformy Azure w wersji 4 lub nowszy jest wymagany.

- W przypadku, gdy moduły platformy Azure wymagają aktualizacji, zobacz [Az Obsługa modułu w usłudze Azure Automation](../automation/az-modules.md).

## <a name="create-azure-automation-runbook"></a>Tworzenie elementu Runbook usługi Azure Automation

Następnym krokiem jest utworzenie elementu Runbook w usłudze Azure Automation, w którym znajduje się skrypt programu PowerShell do pobrania zalecenia dotyczące dostrajania.

Wykonaj następujące kroki, aby utworzyć nowy element runbook usługi Azure Automation:

- Dostęp do konta usługi Azure Automation, utworzonej w poprzednim kroku
- Jeden raz w okienku konta automatyzacji kliknij pozycję "**elementów Runbook**" z menu po lewej stronie, aby utworzyć nowy element runbook usługi Azure Automation za pomocą skryptu programu PowerShell. Aby dowiedzieć się więcej na temat tworzenia elementów runbook usługi automation, zobacz [Tworzenie nowego elementu runbook](../automation/manage-runbooks.md#create-a-runbook).
- Aby dodać nowy element runbook, kliknij " **+ Dodaj element runbook**" opcję menu, a następnie kliknij pozycję "**szybkie tworzenie — Utwórz nowy element runbook**".
- W okienku elementu Runbook, wpisz nazwę elementu runbook (na potrzeby tego przykładu "**AutomaticTuningEmailAutomation**" jest używany), wybierz typ elementu runbook jako **PowerShell** i podaj opis Ten element runbook do opisania jej przeznaczenie.
- Kliknij pozycję **Utwórz** przycisk, aby zakończyć tworzenie nowego elementu runbook

![Dodaj element runbook usługi Azure automation](./media/sql-database-automatic-tuning-email-notifications/howto-email-03.png)

Wykonaj następujące kroki, aby załadować skrypt programu PowerShell, wewnątrz elementu runbook tworzone:

- Wewnątrz "**edytować element Runbook programu PowerShell**"Wybierz opcję"**elementów RUNBOOK**" w menu drzewa i rozwiń widok, aż zobaczysz nazwę elementu runbook (w tym przykładzie " **AutomaticTuningEmailAutomation**"). Wybierz ten element runbook.
- W pierwszym wierszu "Edytuj element Runbook programu PowerShell" (począwszy od numeru 1) kopiowania i wklejania następujący kod skryptu programu PowerShell. Ten skrypt programu PowerShell jest dostarczany jako — jest ułatwią Ci rozpoczęcie pracy. Zmodyfikuj skrypt do zestawu Twoich potrzeb.

W nagłówku dostarczonego skryptu programu PowerShell, musisz zastąpić `<SUBSCRIPTION_ID_WITH_DATABASES>` przy użyciu swojego identyfikatora subskrypcji platformy Azure. Aby dowiedzieć się, jak pobrać swój identyfikator subskrypcji platformy Azure, zobacz [wprowadzenie swojej identyfikator GUID subskrypcji platformy Azure](https://blogs.msdn.microsoft.com/mschray/20../../getting-your-azure-subscription-guid-new-portal/).

W przypadku kilka subskrypcji możesz dodać ich jako rozdzielonych przecinkami do właściwości "$subscriptions" w nagłówku skryptu.

```powershell
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
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Define the resource types
$resourceTypes = ("Microsoft.Sql/servers/databases")
$advisors = ("CreateIndex", "DropIndex");
$results = @()

# Loop through all subscriptions
foreach($subscriptionId in $subscriptions) {
    Select-AzSubscription -SubscriptionId $subscriptionId
    $rgs = Get-AzResourceGroup

    # Loop through all resource groups
    foreach($rg in $rgs) {
        $rgname = $rg.ResourceGroupName;

        # Loop through all resource types
        foreach($resourceType in $resourceTypes) {
            $resources = Get-AzResource -ResourceGroupName $rgname -ResourceType $resourceType

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
                    $recs = Get-AzSqlDatabaseRecommendedAction -ResourceGroupName $ResourceGroupName -ServerName $ServerName  -DatabaseName $DatabaseName -AdvisorName $advisor
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

Kliknij przycisk "**Zapisz**" przycisk w prawym górnym rogu, aby zapisać skrypt. Gdy jesteś zadowolony z skrypt, kliknij przycisk "**Publikuj**" przycisk, aby opublikować ten element runbook.

W okienku głównym elementu runbook, użytkownik może kliknąć "**Start**" przycisk, aby **test** skryptu. Kliknij pozycję "**dane wyjściowe**" Aby wyświetlić wyniki skryptu, wykonywane. Te dane wyjściowe będzie zawartość wiadomości e-mail. Przykładowe dane wyjściowe skryptu są widoczne na następującym zrzucie ekranu.

![Widoku zalecenia za pomocą usługi Azure Automation dostrajania automatycznego uruchamiania](./media/sql-database-automatic-tuning-email-notifications/howto-email-04.png)

Upewnij się, aby dostosować zawartość, dostosowując skrypt programu PowerShell do Twoich potrzeb.

Za pomocą powyższych kroków skrypt programu PowerShell, aby pobrać zalecenia dostrajania automatycznego jest ładowany w usłudze Azure Automation. Następnym krokiem jest zautomatyzować i zaplanować zadanie dostarczania poczty e-mail.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>Automatyzuj zadania poczty e-mail przy użyciu Microsoft Flow

Aby kompletne rozwiązanie, co stanowi ostatni krok, należy utworzyć przepływ automatyzacji w Microsoft Flow składający się z trzech akcji (zadania):

1. "**Usługi azure Automation — Tworzenie zadania**" — używane do uruchomienia skryptu programu PowerShell można pobrać automatyczne dostrajanie zalecenia wewnątrz elementu runbook usługi Azure Automation
2. "**Usługi azure Automation — dane wyjściowe zadania Get**" — używane do pobierania danych wyjściowych z wykonany skrypt programu PowerShell
3. "**Usługi office 365 Outlook — Wyślij wiadomość e-mail**" — używane do wysyłania wiadomości e-mail. Wiadomości e-mail są wysyłane przy użyciu konta usługi Office 365, osoby, tworząc przepływ.

Aby dowiedzieć się więcej o możliwościach, Microsoft Flow, zobacz [Rozpoczynanie pracy z usługą Microsoft Flow](https://docs.microsoft.com/flow/getting-started).

Warunkiem wstępnym tego kroku jest do zarejestrowania się w [Microsoft Flow](https://flow.microsoft.com) konto i zaloguj się. Raz wewnątrz rozwiązanie, wykonaj następujące kroki, aby skonfigurować **nowy przepływ**:

- Dostęp "**moje przepływy**" elementu menu.
- Wewnątrz moje przepływy, wybierz pozycję " **+ Utwórz z pustego**" link u góry strony
- Kliknij link "**wyszukiwanie setki łączników i wyzwalaczy**" w dolnej części strony
- W polu wyszukiwania wpisz "**cyklu**" i wybierz pozycję "**harmonogram — cyklicznie**" z wyników wyszukiwania, aby zaplanować uruchomienie zadania dostarczania poczty e-mail.
- W okienku cyklu w polu Częstotliwość wybierz częstotliwość planowania dla tego przepływu do wykonania, takie jak automatyczne wysyłanie wiadomości e-mail każdej minuty, godziny, dnia, tygodnia, itp.

Następnym krokiem jest można dodać trzy zadania (Tworzenie, Pobierz dane wyjściowe i wysyłania poczty e-mail) do nowo utworzonego przepływu cyklicznego. Aby umożliwić dodawanie zadań wymaganych do przepływu, wykonaj następujące kroki:

1. Utwórz akcję można wykonać skrypt programu PowerShell, aby uzyskać zalecenia dotyczące dostrajania

   - Wybierz pozycję " **+ nowy krok**", a następnie"**Dodaj akcję**" wewnątrz okienka przepływu cykl
   - W polu wyszukiwania wpisz "**automatyzacji**"i wybierz pozycję"**usługi Azure Automation — Tworzenie zadania**" z wyników wyszukiwania
   - W okienku zadania Utwórz Skonfiguruj właściwości zadania. W przypadku tej konfiguracji należy szczegółowe informacje o Twojej subskrypcji platformy Azure identyfikator, grupy zasobów i konta usługi Automation **wcześniej zapisaną** na **okienku konta automatyzacji**. Aby dowiedzieć się więcej na temat opcji dostępnych w tej sekcji, zobacz [usługi Azure Automation — Utwórz zadanie](https://docs.microsoft.com/connectors/azureautomation/#create-job).
   - Ukończyć tworzenia tej akcji, klikając "**Zapisz przepływ**"

2. Utwórz akcję, aby pobrać dane wyjściowe z wykonany skrypt programu PowerShell

   - Wybierz pozycję " **+ nowy krok**", a następnie"**Dodaj akcję**" wewnątrz okienka przepływu cykl
   - W wyszukiwaniu pola typu "**automatyzacji**"i wybierz pozycję"**usługi Azure Automation — dane wyjściowe zadania Get**" z wyników wyszukiwania. Aby dowiedzieć się więcej na temat opcji dostępnych w tej sekcji, zobacz [usługi Azure Automation — dane wyjściowe zadania Get](https://docs.microsoft.com/connectors/azureautomation/#get-job-output).
   - Wypełnij pola wymagana (podobny do poprzedniego zadania tworzenia) — wypełnić Twojej subskrypcji platformy Azure identyfikator, grupy zasobów oraz konta usługi Automation (wprowadzoną w okienku konta automatyzacji)
   - Kliknij wewnątrz pola "**identyfikator zadania**" do "**zawartości dynamicznej**" menu do wyświetlenia. W ramach tego menu, zaznacz opcję "**identyfikator zadania**".
   - Ukończyć tworzenia tej akcji, klikając "**Zapisz przepływ**"

3. Utwórz akcję wysyłania wiadomości e-mail za pomocą integracji z usługą Office 365

   - Wybierz pozycję " **+ nowy krok**", a następnie"**Dodaj akcję**" wewnątrz okienka przepływu cykl
   - W wyszukiwaniu pola typu "**Wyślij wiadomość e-mail**"i wybierz pozycję"**usługi Office 365 Outlook — Wyślij wiadomość e-mail**" z wyników wyszukiwania
   - W "**do**" polu wpisz adres e-mail, do której należy wysłać wiadomość e-mail z powiadomieniem
   - W "**podmiotu**" w temacie wiadomości e-mail, na przykład "zalecenia dostrajania automatycznego email notification" typ pola
   - Kliknij wewnątrz pola "**treści**" do "**zawartości dynamicznej**" menu do wyświetlenia. Z w ramach tego menu, w obszarze "**pobierać dane wyjściowe zadania**", wybierz opcję"**zawartości**"
   - Ukończyć tworzenia tej akcji, klikając "**Zapisz przepływ**"

> [!TIP]
> Aby wysyłanie zautomatyzowanych wiadomości e-mail do adresatów różne, należy utworzyć oddzielne przepływy. W tych dodatkowych przepływów Zmień adres e-mail adresata, w polu "Do" i wiersz tematu wiadomości e-mail w polu "Temat". Tworzenie nowych elementów runbook w usłudze Azure Automation za pomocą dostosowane skrypty programu PowerShell (takie jak zmiana Identyfikatora subskrypcji platformy Azure) umożliwia dalsze dostosowywanie automatycznych scenariuszy takich jest na przykład wysyłanie wiadomości e-mail oddzielne adresatów w dostrajanie automatyczne zalecenia dotyczące użycie osobnych subskrypcji.
>

Powyższe kończy się kroki wymagane do skonfigurowania przepływu pracy zadania dostarczania poczty e-mail. Cały przepływ składający się z trzech akcji utworzone jest wyświetlany na poniższej ilustracji.

![Widok przepływu powiadomień e-mail dostrajania automatycznego](./media/sql-database-automatic-tuning-email-notifications/howto-email-05.png)

Aby przetestować przepływ, wybierz polecenie "**Uruchom teraz**" w prawym górnym rogu w okienku usługi flow.

Statystyki uruchamiania zautomatyzowanych zadań, przedstawiający Powodzenie powiadomień e-mail wysłanych, będą widoczne z poziomu okienka analizy przepływu.

![Uruchamianie przepływu automatycznego dostrajania powiadomień e-mail](./media/sql-database-automatic-tuning-email-notifications/howto-email-06.png)

Analizy przepływu jest przydatne do monitorowania Powodzenie wykonań zadań i w razie potrzeby do rozwiązywania problemów.  W przypadku rozwiązywania problemów, również można zbadać za pośrednictwem aplikacji usługi Azure Automation w dziennikach wykonywania skryptu środowiska PowerShell.

Ostatecznymi automatyczne wiadomości e-mail przypomina następującą wiadomość e-mail po kompilowanie i uruchamianie tego rozwiązania:

![Przykładowe dane wyjściowe wiadomości e-mail z automatycznego dostrajania powiadomienia e-mail](./media/sql-database-automatic-tuning-email-notifications/howto-email-07.png)

Dostosowując skrypt programu PowerShell, można dostosować dane wyjściowe i formatowanie automatyczne wiadomości e-mail do Twoich potrzeb.

Może dostosować rozwiązanie do tworzenia powiadomienia e-mail, na podstawie określonego zdarzenia dostrajania i do wielu odbiorców dla wielu baz danych, w zależności od scenariuszy niestandardowych lub subskrypcji.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej w jaki sposób automatycznego dostrajania może pomóc zwiększyć wydajność bazy danych, zobacz [dostrajania automatycznego usługi Azure SQL Database](sql-database-automatic-tuning.md).
- Aby włączyć dostrajania automatycznego usługi Azure SQL Database do zarządzania obciążenia, zobacz [Włączanie automatycznego dostrajania](sql-database-automatic-tuning-enable.md).
- Aby ręcznie przejrzeć i stosowanie zalecenia dotyczące dostrajania automatycznego, zobacz [Znajdowanie i stosowanie zaleceń dotyczących wydajności](sql-database-advisor-portal.md).
