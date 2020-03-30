---
title: Eksportowanie dziennika aktywności platformy Azure
description: Eksportuj dziennik aktywności platformy Azure do magazynu w celu archiwizacji lub usługi Azure Event Hubs w celu eksportowania poza platformę Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: edaa585ffb3448a80b021aa924a9d654ac829931
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096289"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>Eksportowanie dziennika aktywności platformy Azure do magazynu lub usługi Azure Event Hubs

> [!IMPORTANT]
> Metoda wysyłania dziennika aktywności platformy Azure do usługi Azure Storage i usługi Azure Event Hubs została zmieniona na [ustawienia diagnostyczne.](diagnostic-settings.md) W tym artykule opisano starszą metodę, która jest w trakcie przestarzałe. Zobacz uaktualnienie do [kolekcji dzienników aktywności platformy Azure i eksportowanie](diagnostic-settings-legacy.md) do porównania.


[Dziennik aktywności platformy Azure](platform-logs-overview.md) zapewnia wgląd w zdarzenia na poziomie subskrypcji, które wystąpiły w ramach subskrypcji platformy Azure. Oprócz przeglądania dziennika aktywności w portalu Azure lub kopiowania go do obszaru roboczego usługi Log Analytics, gdzie można go analizować z innymi danymi zebranymi przez usługę Azure Monitor, można utworzyć profil dziennika w celu zarchiwizowania dziennika aktywności na koncie magazynu platformy Azure lub przesłać go strumieniowo do usługi Centrum zdarzeń.

## <a name="archive-activity-log"></a>Dziennik aktywności archiwum
Archiwizowanie dziennika aktywności na koncie magazynu jest przydatne, jeśli chcesz zachować dane dziennika dłużej niż 90 dni (z pełną kontrolą nad zasadami przechowywania) do inspekcji, analizy statycznej lub tworzenia kopii zapasowych. Jeśli musisz zachować zdarzenia tylko przez 90 dni lub mniej, nie musisz konfigurowanie archiwizacji konta magazynu, ponieważ zdarzenia dziennika aktywności są zachowywane na platformie Azure przez 90 dni.

## <a name="stream-activity-log-to-event-hub"></a>Dziennik aktywności strumienia do Centrum zdarzeń
[Usługa Azure Event Hubs](/azure/event-hubs/) to platforma przesyłania strumieniowego danych i usługa pozyskiwania zdarzeń, która może odbierać i przetwarzać miliony zdarzeń na sekundę. Dane wysłane do centrum zdarzeń mogą zostać przekształcone i zmagazynowane przy użyciu dowolnego dostawcy analityki czasu rzeczywistego lub adapterów przetwarzania wsadowego/magazynowania. Dwa sposoby korzystania z funkcji przesyłania strumieniowego dla dziennika aktywności to:
* **Przesyłanie strumieniowe do systemów rejestrowania i telemetrii innych firm:** z biegiem czasu przesyłanie strumieniowe usługi Azure Event Hubs stanie się mechanizmem do przesyłania dzienników aktywności do siemień innych firm i rozwiązań do analizy dzienników.
* **Tworzenie niestandardowej platformy telemetryczne i rejestrowania:** Jeśli masz już niestandardową platformę telemetryczną lub myślisz o utworzeniu jednej, wysoce skalowalny charakter publikowania i subskrybowania centrów zdarzeń umożliwia elastyczne pozyskiwanie dziennika aktywności.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="storage-account"></a>Konto magazynu
Jeśli archiwizujesz dziennik aktywności, musisz [utworzyć konto magazynu,](../../storage/common/storage-account-create.md) jeśli jeszcze go nie masz. Nie należy używać istniejącego konta magazynu, które ma inne, nieobjętych monitorowania danych przechowywanych w nim, dzięki czemu można lepiej kontrolować dostęp do danych monitorowania. Jeśli jednak archiwizujesz również dzienniki i metryki na koncie magazynu, możesz użyć tego samego konta magazynu, aby zachować wszystkie dane monitorowania w centralnej lokalizacji.

Konto magazynu nie musi znajdować się w tej samej subskrypcji co dzienniki emitujące subskrypcję, o ile użytkownik, który konfiguruje to ustawienie, ma odpowiedni dostęp RBAC do obu subskrypcji. 

> [!TIP]
> Zobacz [Konfigurowanie zapór usługi Azure Storage i sieci wirtualnych](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) w celu zapewnienia dostępu do konta magazynu za zabezpieczoną siecią wirtualną.

### <a name="event-hubs"></a>Usługa Event Hubs
Jeśli wysyłasz dziennik aktywności do centrum zdarzeń, musisz [utworzyć centrum zdarzeń,](../../event-hubs/event-hubs-create.md) jeśli jeszcze go nie masz. Jeśli wcześniej przesyłano strumieniowo zdarzenia dziennika aktywności do tej przestrzeni nazw centrów zdarzeń, centrum zdarzeń zostanie ponownie użyczone.

Zasady dostępu współdzielonego definiuje uprawnienia, które ma mechanizm przesyłania strumieniowego. Przesyłanie strumieniowe do centrów zdarzeń wymaga uprawnień Zarządzanie, Wysyłanie i Nasłuchij. Można utworzyć lub zmodyfikować zasady dostępu udostępnionego dla obszaru nazw Centrum zdarzeń w witrynie Azure portal w obszarze Konfigurowanie witryny dla obszaru nazw centrum zdarzeń.

Aby zaktualizować profil dziennika aktywności w celu uwzględnienia przesyłania strumieniowego, musisz mieć uprawnienie ListKey dla tej reguły autoryzacji centrum zdarzeń. Obszar nazw centrum zdarzeń nie musi być w tej samej subskrypcji co subskrypcja emitująca dzienniki, o ile użytkownik, który konfiguruje to ustawienie, ma odpowiedni dostęp RBAC do obu subskrypcji i obie subskrypcje znajdują się w tej samej dzierżawie usługi AAD.

Przesyłaj strumieniowo dziennik aktywności do Centrum zdarzeń, [tworząc profil dziennika](#create-a-log-profile).

## <a name="create-a-log-profile"></a>Tworzenie profilu dziennika
Definiujesz sposób eksportowania dziennika aktywności platformy Azure przy użyciu **profilu dziennika.** Każda subskrypcja platformy Azure może mieć tylko jeden profil dziennika. Te ustawienia można skonfigurować za pomocą opcji **Eksportuj** w bloku Dziennik aktywności w portalu. Można je również skonfigurować programowo [przy użyciu interfejsu API REST usługi Azure Monitor,](https://msdn.microsoft.com/library/azure/dn931927.aspx)poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia.

Profil dziennika definiuje następujące.

**Gdzie należy wysłać dziennik aktywności.** Obecnie dostępne opcje to Konto magazynu lub Centra zdarzeń.

**Jakie kategorie zdarzeń powinny być wysyłane.** Znaczenie *kategorii* w profilach dzienników i zdarzenia dziennika aktywności jest inna. W profilu dziennika *kategoria* reprezentuje typ operacji (Zapis, Usuń, Akcja). W przypadku dziennika aktywności *właściwość kategorii*"* reprezentuje źródło lub typ zdarzenia (na przykład Administracja, ServiceHealth i Alert).

**Które regiony (lokalizacje) powinny być eksportowane.** Należy uwzględnić wszystkie lokalizacje, ponieważ wiele zdarzeń w dzienniku aktywności są zdarzeniami globalnymi.

**Jak długo dziennik aktywności powinien być zachowywany na koncie magazynu.** Przechowywanie przez zero dni oznacza, że dzienniki są przechowywane w nieskończoność. W przeciwnym razie wartość może być dowolną liczbę dni od 1 do 365.

Jeśli zasady przechowywania są ustawione, ale przechowywanie dzienników na koncie magazynu jest wyłączone, zasady przechowywania nie mają wpływu. Zasady przechowywania są stosowane na dzień, więc na koniec dnia (UTC), dzienniki od dnia, który jest teraz poza zasadami przechowywania są usuwane. Na przykład jeśli masz zasady przechowywania jednego dnia, na początku dnia dzisiaj dzienniki z poprzedniego wczoraj zostaną usunięte. Proces usuwania rozpoczyna się o północy czasu UTC, ale należy pamiętać, że może upłynąć do 24 godzin dla dzienników, które mają być usunięte z konta magazynu.


> [!IMPORTANT]
> Może pojawić się błąd podczas tworzenia profilu dziennika, jeśli dostawca zasobów Microsoft.Insights nie jest zarejestrowany. Zobacz [dostawców zasobów platformy Azure i typów,](../../azure-resource-manager/management/resource-providers-and-types.md) aby zarejestrować tego dostawcę.


### <a name="create-log-profile-using-the-azure-portal"></a>Tworzenie profilu dziennika przy użyciu portalu Azure

Utwórz lub edytuj profil dziennika za pomocą opcji **Eksportuj do centrum zdarzeń** w witrynie Azure portal.

1. Z menu **Azure Monitor** w witrynie Azure portal wybierz **dziennik aktywności**.
3. Kliknij pozycję **Ustawienia diagnostyczne**.

   ![Ustawienia diagnostyczne](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Kliknij fioletowy baner, aby zapoznać się ze starszą wersji.

    ![Starsze doświadczenie](media/diagnostic-settings-subscription/legacy-experience.png)

3. W wyświetlonym bloku określ następujące elementy:
   * Regiony ze zdarzeniami do wyeksportowania. Należy wybrać wszystkie regiony, aby upewnić się, że nie przegapisz kluczowych zdarzeń, ponieważ dziennik aktywności jest globalnym (nieregionacyjnym) dziennikiem, a więc większość zdarzeń nie ma regionu skojarzonego z nimi.
   * Jeśli chcesz zapisać na konto magazynu:
       * Konto magazynu, na którym chcesz zapisać zdarzenia.
       * Liczba dni, przez które chcesz zachować te zdarzenia w magazynie. Ustawienie 0 dni zachowuje dzienniki na zawsze.
   * Jeśli chcesz napisać do Centrum zdarzeń:
       * Obszar nazw usługi Service Bus, w którym chcesz centrum zdarzeń mają być tworzone do przesyłania strumieniowego tych zdarzeń.

     ![Blok Dziennika aktywności eksportu](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. Kliknij **przycisk Zapisz,** aby zapisać te ustawienia. Ustawienia są natychmiastowo stosowane do subskrypcji.


### <a name="configure-log-profile-using-powershell"></a>Konfigurowanie profilu dziennika przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Jeśli profil dziennika już istnieje, należy najpierw usunąć istniejący profil dziennika, a następnie utworzyć nowy.

1. Służy `Get-AzLogProfile` do identyfikowania, czy istnieje profil dziennika.  Jeśli istnieje profil dziennika, należy zwrócić uwagę na właściwość *name.*

1. Służy `Remove-AzLogProfile` do usuwania profilu dziennika przy użyciu wartości z właściwości *name.*

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. Służy `Add-AzLogProfile` do tworzenia nowego profilu dziennika:

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | Właściwość | Wymagany | Opis |
    | --- | --- | --- |
    | Nazwa |Tak |Nazwa profilu dziennika. |
    | Dysk danych MagazynuKonto |Nie |Identyfikator zasobu konta magazynu, na którym należy zapisać dziennik aktywności. |
    | usługaBusRuleId |Nie |Identyfikator reguły usługi Service Bus dla obszaru nazw usługi Service Bus, w której mają być utworzone centra zdarzeń. Jest to ciąg o `{service bus resource ID}/authorizationrules/{key name}`formacie: . |
    | Lokalizacja |Tak |Oddzielona przecinkami lista regionów, dla których chcesz zbierać zdarzenia dziennika aktywności. |
    | RetencjaWdays |Tak |Liczba dni, dla których zdarzenia powinny być przechowywane na koncie magazynu, od 1 do 365. Wartość zero przechowuje dzienniki przez czas nieokreślony. |
    | Kategoria |Nie |Oddzielona przecinkami lista kategorii zdarzeń, które powinny być zbierane. Możliwe wartości to _Write_, _Delete_i _Action_. |

### <a name="example-script"></a>Przykładowy skrypt
Poniżej przedstawiono przykładowy skrypt programu PowerShell w celu utworzenia profilu dziennika, który zapisuje dziennik aktywności zarówno na koncie magazynu, jak i w centrum zdarzeń.

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>Konfigurowanie profilu dziennika przy użyciu interfejsu wiersza polecenia platformy Azure

Jeśli profil dziennika już istnieje, należy najpierw usunąć istniejący profil dziennika, a następnie utworzyć nowy profil dziennika.

1. Służy `az monitor log-profiles list` do identyfikowania, czy istnieje profil dziennika.
2. Służy `az monitor log-profiles delete --name "<log profile name>` do usuwania profilu dziennika przy użyciu wartości z właściwości *name.*
3. Służy `az monitor log-profiles create` do tworzenia nowego profilu dziennika:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Właściwość | Wymagany | Opis |
    | --- | --- | --- |
    | name |Tak |Nazwa profilu dziennika. |
    | identyfikator konta magazynu |Tak |Identyfikator zasobu konta magazynu, na którym powinny być zapisywane dzienniki aktywności. |
    | locations |Tak |Oddzielona przestrzenią lista regionów, dla których chcesz zbierać zdarzenia dziennika aktywności. Za pomocą `az account list-locations --query [].name`programu . |
    | Dni |Tak |Liczba dni, dla których zdarzenia powinny być zachowane, od 1 do 365. Wartość zero będzie przechowywać dzienniki przez czas nieokreślony (na zawsze).  Jeśli zero, a następnie włączony parametr powinien być ustawiony na false. |
    |enabled | Tak |Prawda lub fałsz.  Służy do włączania lub wyłączania zasad przechowywania.  Jeśli True, a następnie days parametr musi być wartość większa niż 0.
    | categories |Tak |Oddzielona przestrzenią lista kategorii zdarzeń, które powinny być zbierane. Możliwe wartości to Write, Delete i Action. |



## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o dzienniku aktywności](../../azure-resource-manager/management/view-activity-logs.md)
* [Zbieranie dziennika aktywności w dziennikach monitora platformy Azure](activity-log-collect.md)
