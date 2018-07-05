---
title: Stream dziennika aktywności platformy Azure do usługi Event Hubs
description: Dowiedz się, jak przesyłać strumieniowo dziennik aktywności platformy Azure do usługi Event Hubs.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/02/2018
ms.author: johnkem
ms.component: activitylog
ms.openlocfilehash: 45352c1cf4aca9043c23bbe12e94ba770a38c01b
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37436709"
---
# <a name="stream-the-azure-activity-log-to-event-hubs"></a>Stream dziennika aktywności platformy Azure do usługi Event Hubs
Można przesyłać strumieniowo [dziennika aktywności platformy Azure](monitoring-overview-activity-logs.md) niemal w czasie rzeczywistym do aplikacji przez:

* Za pomocą wbudowanych **wyeksportować** opcji w portalu
* Włączanie identyfikator reguły usługi Azure Service Bus w profilu dziennika za pomocą poleceń cmdlet programu Azure PowerShell lub wiersza polecenia platformy Azure

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>Co można zrobić za pomocą dzienników aktywności i usługi Event Hubs
Poniżej przedstawiono dwie metody, można użyć możliwości przesyłania strumieniowego dla dziennika aktywności:

* **Stream z systemami innych firm rejestrowania i dane telemetryczne**: wraz z upływem czasu przesyłania strumieniowego usługi Azure Event Hubs staną się mechanizm dziennika rozwiązań do analizy i przekazać dziennik aktywności do rozwiązania Siem innych firm.
* **Tworzenie niestandardowej telemetrii i rejestrowania platformy**: Jeśli już masz platformy niestandardowej telemetrii lub są myśleć o zbudowania jej, wysoce skalowalna publikowania/subskrybowania rodzaj usługi Event Hubs umożliwia elastyczne pozyskiwania dziennika aktywności. Aby uzyskać więcej informacji, zobacz [wideo Dan Rosanova o korzystaniu z usługi Event Hubs na platformie danych telemetrycznych w skali globalnej](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-the-activity-log"></a>Włączanie przesyłania strumieniowego dziennika aktywności
Możesz włączyć strumieniowe przesyłanie dziennika aktywności, programowo lub za pośrednictwem portalu. W obu przypadkach wybierz przestrzeni nazw usługi Event Hubs i zasady dostępu współdzielonego dla tej przestrzeni nazw. Centrum zdarzeń za pomocą nazwy insights — dzienniki operationallogs jest tworzony w tej przestrzeni nazw, gdy wystąpi pierwsze nowe zdarzenie dziennika aktywności. 

Jeśli użytkownik nie ma przestrzeni nazw usługi Event Hubs, należy najpierw utworzyć. Jeśli wcześniej przesyłane strumieniowo zdarzenia dziennika aktywności do tej przestrzeni nazw usługi Event Hubs, Centrum zdarzeń, który został wcześniej utworzony zostanie ono użyte ponownie. 

Zasady dostępu współdzielonego definiuje uprawnienia, które ma mechanizm przesyłania strumieniowego. Już dziś, przesyłanie strumieniowe do usługi Event Hubs wymaga **Zarządzaj**, **wysyłania**, i **nasłuchiwania** uprawnienia. Można tworzyć lub modyfikować zasady dostępu współdzielonego dla przestrzeni nazw usługi Event Hubs w witrynie Azure portal w obszarze **Konfiguruj** karty dla przestrzeni nazw usługi Event Hubs. 

Aby zaktualizować profil dziennika dziennika aktywności do uwzględnienia, przesyłanie strumieniowe, użytkownika, który jest wprowadzenie zmian musi mieć uprawnienie ListKey tej reguły autoryzacji usługi Event Hubs. Przestrzeń nazw usługi Event Hubs nie musi znajdować się w tej samej subskrypcji co w przypadku subskrypcji, który jest emitowane dzienniki, jak długo użytkownik, który konfiguruje ustawienie ma odpowiedni dostęp RBAC do obu subskrypcji.

### <a name="via-the-azure-portal"></a>W witrynie Azure portal
1. Przejdź do **dziennika aktywności** sekcji przy użyciu **wszystkich usług** wyszukiwania po lewej stronie portalu.
   
   ![Wybierając dziennik aktywności z listy usług w portalu](./media/monitoring-stream-activity-logs-event-hubs/activity.png)
2. Wybierz **wyeksportować** znajdujący się u góry dziennika.
   
   ![Przycisk Eksportuj w portalu](./media/monitoring-stream-activity-logs-event-hubs/export.png)

   Należy pamiętać, że wybrane ustawienia filtru w przypadku zastosowania podczas wyświetlania dziennika aktywności w poprzednim widoku nie mają wpływu na ustawienia eksportu. To są tylko do filtrowania, zobacz podczas przeglądania dziennika aktywności w portalu.
3. W wyświetlonej sekcji wybierz **we wszystkich regionach**. Nie należy wybierać poszczególnych regionów.
   
   ![Sekcja eksportu](./media/monitoring-stream-activity-logs-event-hubs/export-audit.png)

   > [!WARNING]  
   > Jeśli wybierzesz nic innego niż **we wszystkich regionach**, będzie przeoczyć kluczowych zdarzeń, których oczekujesz, że do odbierania. Dziennik aktywności jest globalne dziennika (inne niż regionalne), dzięki czemu większość zdarzeń ma region skojarzonych z nimi. 
   >

4. Wybierz **Zapisz** można zapisać tych ustawień. Ustawienia są natychmiast stosowane do Twojej subskrypcji.
5. Jeśli masz kilka subskrypcji, powtarzaj tę akcję, a następnie Wyślij wszystkie dane do tego samego Centrum zdarzeń.

### <a name="via-powershell-cmdlets"></a>Za pomocą poleceń cmdlet programu PowerShell
Jeśli istnieje już profil dziennika, należy najpierw usunąć istniejący profil dziennika, a następnie utwórz nowy profil dziennika.

1. Użyj `Get-AzureRmLogProfile` Aby ustalić, czy istnieje już profil dziennika.  Jeśli istnieje profil dziennika, zlokalizuj *nazwa* właściwości.
2. Użyj `Remove-AzureRmLogProfile` do usunięcia profilu dziennika, używając wartości z *nazwa* właściwości.

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzureRmLogProfile -Name "default"
    ```
3. Użyj `Add-AzureRmLogProfile` Aby utworzyć nowy profil dziennika:

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzureRmLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   Add-AzureRmLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```

### <a name="via-azure-cli"></a>Za pomocą wiersza polecenia platformy Azure
Jeśli istnieje już profil dziennika, należy najpierw usunąć istniejący profil dziennika, a następnie utwórz nowy profil dziennika.

1. Użyj `az monitor log-profiles list` Aby ustalić, czy istnieje już profil dziennika.
2. Użyj `az monitor log-profiles delete --name "<log profile name>` do usunięcia profilu dziennika, używając wartości z *nazwa* właściwości.
3. Użyj `az monitor log-profiles create` Aby utworzyć nowy profil dziennika:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

## <a name="consume-the-log-data-from-event-hubs"></a>Korzystanie z rejestrowanych danych dziennika z usługi Event Hubs
Schemat dla dziennika aktywności jest dostępny w [monitorowania aktywności subskrypcji z dziennika aktywności platformy Azure](monitoring-overview-activity-logs.md). Każde wydarzenie jest w tablicy obiektów blob JSON o nazwie *rekordów*.

## <a name="next-steps"></a>Kolejne kroki
* [Archiwizowanie dziennika aktywności na koncie magazynu](monitoring-archive-activity-log.md)
* [Zapoznaj się z omówieniem dziennika aktywności platformy Azure](monitoring-overview-activity-logs.md)
* [Ustawianie alertu na podstawie zdarzenia dziennika aktywności](insights-auditlog-to-webhook-email.md)

