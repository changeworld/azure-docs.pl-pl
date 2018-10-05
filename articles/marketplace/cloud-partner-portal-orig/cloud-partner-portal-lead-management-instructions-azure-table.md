---
title: Usługa Azure Table | Dokumentacja firmy Microsoft
description: Skonfiguruj zarządzanie potencjalnymi klientami dla tabeli platformy Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 60e3e3d81b07bf7ae681b5cef2d6d9681877a35f
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810890"
---
<a name="lead-management-instructions-for-azure-table"></a>Instrukcje prowadzić zarządzania dla usługi Azure Table
============================================

W tym artykule opisano sposób konfigurowania usługi Azure Table do przechowywania potencjalnych klientów. Tabela platformy Azure umożliwia przechowywanie i Dostosuj informacje o kliencie.

## <a name="to-configure-azure-table"></a>Aby skonfigurować usługi Azure Table

1.  Jeśli nie masz konta platformy Azure, możesz to zrobić [Utwórz bezpłatne konto próbne](https://azure.microsoft.com/pricing/free-trial/).

2.  Po uaktywnieniu konta platformy Azure, zaloguj się do [witryny Azure portal](https://portal.azure.com).
3.  W witrynie Azure portal Utwórz konto magazynu. Następny zrzut ekranu pokazuje, jak utworzyć konto magazynu. Aby uzyskać więcej informacji na temat cen magazynu, zobacz [ceny za magazyn](https://azure.microsoft.com/pricing/details/storage/).

    ![Kroki, aby utworzyć konto magazynu platformy Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

4.  Skopiuj parametry połączenia konta magazynu dla klucza i wklej go do **parametry połączenia konta magazynu** pole w portalu Cloud Partner. Na przykład stingu połączenia `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net `
    
    ![Klucz usługi Azure storage](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

Możesz użyć [Eksplorator usługi Azure storage](http://azurestorageexplorer.codeplex.com/) lub inne narzędzie, aby wyświetlić dane w tabeli magazynu. Możesz również wyeksportować dane w tabeli platformy Azure.
dane.

## <a name="optional-to-use-azure-functions-with-an-azure-table"></a>**(Opcjonalnie)**  Usługi Azure Functions za pomocą usługi Azure table

Jeśli chcesz dostosować, jak otrzymujesz potencjalnych klientów, użyj [usługi Azure Functions](https://azure.microsoft.com/services/functions/) z tabeli platformy Azure. Usługa Azure Functions pozwala zautomatyzować proces generowania potencjalnych klientów.

Poniższe kroki pokazują jak utworzyć funkcję platformy Azure, która korzysta z czasomierza. Co pięć minut funkcja wygląda w tabeli platformy Azure dla nowych rekordów, a następnie używa usługi SendGrid, aby wysłać wiadomość e-mail z powiadomieniem.


1.  [Utwórz](https://portal.azure.com/#create/SendGrid.SendGrid) bezpłatne konto usługi SendGrid w subskrypcji platformy Azure.

    ![Tworzenie usługi SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/createsendgrid.png)

2.  Utwórz klucz interfejsu API usługi SendGrid 
    - Wybierz **Zarządzaj** można przejść do interfejsu użytkownika usługi SendGrid
    - Wybierz **ustawienia**, **klucze interfejsu API**, a następnie utwórz klucz, który ma wysyłać wiadomości E-mail —\> pełny dostęp
    - Zapisz klucz interfejsu API


    ![Klucz interfejsu API usługi SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridkey.png)


3.  [Utwórz](https://portal.azure.com/#create/Microsoft.FunctionApp) aplikację funkcji platformy Azure przy użyciu opcji planie hostingu o nazwie "Plan zużycie".

    ![Tworzenie aplikacji funkcji platformy Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/createfunction.png)


4.  Utwórz nową definicję funkcji.

    ![Utwórz definicję funkcji platformy Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/createdefinition.png)
 

5.  Aby uzyskać funkcja do wysyłania aktualizacji w określonym czasie, wybierz **TimerTrigger-CSharp** jako opcję starter.

     ![Opcja wyzwalacza czasu w usłudze Azure — funkcja](./media/cloud-partner-portal-lead-management-instructions-azure-table/timetrigger.png)


6.  Zastąp kod "Tworzenie" na poniższy przykładowy kod. Edytuj adresy e-mail, adresy, których chcesz używać dla nadawcą i odbiorcą.

        #r "Microsoft.WindowsAzure.Storage"
        #r "SendGrid"
        using Microsoft.WindowsAzure.Storage.Table;
        using System;
        using SendGrid;
        using SendGrid.Helpers.Mail;
        public class MyRow : TableEntity
        {
            public string Name { get; set; }
        }
        public static void Run(TimerInfo myTimer, IQueryable<MyRow> inputTable, out Mail message, TraceWriter log)
        {
            // UTC datetime that is 5.5 minutes ago while the cron timer schedule is every 5 minutes
            DateTime dateFrom = DateTime.UtcNow.AddSeconds(-(5 * 60 + 30));
            var emailFrom = "YOUR EMAIL";
            var emailTo = "YOUR EMAIL";
            var emailSubject = "Azure Table Notification";
            // Look in the table for rows that were added recently
            var rowsList = inputTable.Where(r => r.Timestamp > dateFrom).ToList();
            // Check how many rows were added
            int rowsCount = rowsList.Count;
            if (rowsCount > 0)
            {
                log.Info($"Found {rowsCount} rows added since {dateFrom} UTC");
                // Configure the email message describing how many rows were added
                message = new Mail
                {
                    From = new Email(emailFrom),
                    Subject = emailSubject + " (" + rowsCount + " new rows)"
                };
                var personalization = new Personalization();
                personalization.AddTo(new Email(emailTo));
                message.AddPersonalization(personalization);
                var content = new Content
                {
                    Type = "text/plain",
                    Value = "Found " + rowsCount + " new rows added since " + dateFrom.ToString("yyyy-MM-dd HH:mm:ss") + " UTC"
                };
                message.AddContent(content);
            }
            else
            {
                // Do not send the email if no new rows were found
                message = null;
            }
        }

    ![Fragment kodu w usłudze Azure — funkcja](./media/cloud-partner-portal-lead-management-instructions-azure-table/code.png)


7.  Wybierz **integracja** i **dane wejściowe** zdefiniować połączenie usługi Azure Table.

    ![Integrowanie funkcji platformy Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/integrate.png)


8.  Wprowadź nazwę tabeli, a następnie określ parametry połączenia, wybierając **nowe**.


    ![Parametry połączenia tabeli platformy Azure — funkcja](./media/cloud-partner-portal-lead-management-instructions-azure-table/configtable.png)

9.  Teraz dane wyjściowe jest definiowana jako usługi SendGrid i Zachowaj ustawienia domyślne.

    ![Dane wyjściowe usługi SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridoutput.png)

    ![Domyślne dane wyjściowe usługi SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridoutputdefaults.png)

10. Dodaj klucz interfejsu API usługi SendGrid do ustawień aplikacji funkcji przy użyciu nazwy "SendGridApiKey" i wartość uzyskana z kluczy interfejsu API w interfejsie użytkownika usługi SendGrid

    ![Zarządzanie SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridmanage.png)
    ![SendGrid samodzielnie zarządzać kluczem](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridmanagekey.png)

Po zakończeniu konfigurowania funkcji kod w sekcji integracja powinien wyglądać podobnie jak w poniższym przykładzie.

    {
      "bindings": [
        {
          "name": "myTimer",
          "type": "timerTrigger",
          "direction": "in",
          "schedule": "0 */5 * * * *"
        },
        {
          "type": "table",
          "name": "inputTable",
          "tableName": "MarketplaceLeads",
          "take": 50,
          "connection": "yourstorageaccount_STORAGE",
          "direction": "in"
        },
        {
          "type": "sendGrid",
          "name": "message",
          "apiKey": "SendGridApiKey",
          "direction": "out"
        }
      ],
      "disabled": false
    }

11. Ostatnim krokiem jest przejdź do tworzenia interfejsu użytkownika funkcji, a następnie wybierz pozycję **Uruchom** do uruchomienia czasomierza. Teraz otrzymasz powiadomienie za każdym razem, gdy pochodzą nowego potencjalnego klienta.
