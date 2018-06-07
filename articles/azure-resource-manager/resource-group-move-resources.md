---
title: Przenoszenie zasobów platformy Azure do nowej grupy zasobów lub subskrypcji | Dokumentacja firmy Microsoft
description: Umożliwia przenoszenie zasobów do nowej grupy zasobów lub subskrypcji usługi Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ab7d42bd-8434-4026-a892-df4a97b60a9b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: tomfitz
ms.openlocfilehash: 2326f37afcb845b8c484bdf57db0876026f8e8a1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34602724"
---
# <a name="move-resources-to-new-resource-group-or-subscription"></a>Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji

W tym artykule przedstawiono sposób przenoszenia zasobów do nowej subskrypcji lub nowej grupy zasobów w tej samej subskrypcji. Aby przenieść zasobu można użyć portalu, programu PowerShell, interfejsu wiersza polecenia Azure lub interfejsu API REST. Operacji przenoszenia w tym artykule są dostępne bez pomocy w pomocy technicznej platformy Azure.

Podczas przenoszenia zasobów, zarówno grupy źródłowej i docelowej grupy są zablokowane podczas operacji. Zapisu i usuwania działań są zablokowane na grupy zasobów, dopiero po zakończeniu przenoszenia. Ta blokada oznacza, że nie można dodawać, aktualizować lub usuwać zasoby w grupie zasobów, ale nie oznacza to, że zasoby są zablokowane. Na przykład jeśli zostanie przeniesiony do nowej grupy zasobów programu SQL Server i jego bazy danych, aplikacji korzystającej z bazy danych napotyka bez przestojów. Można nadal odczytu i zapisu w bazie danych.

Nie można zmienić lokalizacji zasobu. Przeniesienie zasobu tylko przenosi je do nowej grupy zasobów. Nową grupę zasobów może zawierać innej lokalizacji, ale nie zmienia lokalizację zasobu.

> [!NOTE]
> W tym artykule opisano sposób przenoszenia zasobów w ramach platformy Azure istniejącego konta wysyłania ofert. Jeśli rzeczywiście chcesz zmienić konta platformy Azure oferty (takich jak uaktualnianie z płatność za rzeczywiste użycie wstępnie zapłacenia) podczas kontynuowanie pracy z istniejących zasobów, zobacz [Przełącz subskrypcji platformy Azure do innej oferty](../billing/billing-how-to-switch-azure-offer.md).
>
>

## <a name="checklist-before-moving-resources"></a>Lista kontrolna przed przeniesieniem zasobów

Przed przeniesieniem zasobu należy wykonać kilka ważnych kroków. Dzięki sprawdzeniu tych warunków można uniknąć błędów.

1. Subskrypcje źródłowych i docelowych musi istnieć w tym samym [dzierżawy usługi Azure Active Directory](../active-directory/active-directory-howto-tenant.md). Aby sprawdzić, czy obie subskrypcje mają ten sam identyfikator dzierżawy, użyj programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure.

  Dla programu Azure PowerShell użyj polecenia:

  ```powershell
  (Get-AzureRmSubscription -SubscriptionName <your-source-subscription>).TenantId
  (Get-AzureRmSubscription -SubscriptionName <your-destination-subscription>).TenantId
  ```

  W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

  ```azurecli-interactive
  az account show --subscription <your-source-subscription> --query tenantId
  az account show --subscription <your-destination-subscription> --query tenantId
  ```

  Jeśli dzierżawy identyfikatorów subskrypcji źródłowych i docelowych nie są takie same, aby uzgodnić dzierżawy identyfikatorów należy użyć następujących metod:

  * [Transfer ownership of an Azure subscription to another account](../billing/billing-subscription-transfer.md) (Przenoszenie własności subskrypcji platformy Azure na inne konto)
  * [Jak skojarzyć lub dodać subskrypcję platformy Azure do usługi Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md)

2. Usługa musi mieć możliwość przenoszenia zasobów. W tym artykule wymieniono usług, które umożliwiają przenoszenie zasobów i usług, które nie należy włączać przenoszenia zasobów.
3. Subskrypcja docelowa musi być zarejestrowana dla dostawcy przenoszonego zasobu. Jeśli nie, zostanie wyświetlony komunikat o błędzie informujący, że **subskrypcja nie jest zarejestrowana dla typu zasobu**. Ten problem może wystąpić podczas przenoszenia zasobu do nowej subskrypcji, która nigdy nie była używana z tym typem zasobu.

  Dla programu PowerShell Użyj następujących poleceń w celu pobrania stanu rejestracji:

  ```powershell
  Set-AzureRmContext -Subscription <destination-subscription-name-or-id>
  Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
  ```

  Aby zarejestrować dostawcy zasobów, należy użyć:

  ```powershell
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
  ```

  Dla wiersza polecenia platformy Azure Użyj następujących poleceń w celu pobrania stanu rejestracji:

  ```azurecli-interactive
  az account set -s <destination-subscription-name-or-id>
  az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
  ```

  Aby zarejestrować dostawcy zasobów, należy użyć:

  ```azurecli-interactive
  az provider register --namespace Microsoft.Batch
  ```

4. Konto przenoszenia zasobów musi mieć co najmniej następujące uprawnienia:

   * **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action** dla źródłowej grupy zasobów.
   * **Microsoft.Resources/subscriptions/resourceGroups/write** w docelowej grupie zasobów.

5. Jeśli to możliwe, podziału dużych przenosi do operacji przenoszenia oddzielne. Menedżer zasobów natychmiast kończy się niepowodzeniem próbami przenoszenie więcej niż 800 zasobów w ramach jednej operacji. Jednak przenoszenia zasobów mniej niż 800 może również zakończyć się niepowodzeniem przez przekroczeniem limitu czasu.

## <a name="when-to-call-support"></a>Kiedy Zadzwoń do pomocy technicznej

Można przenieść najwięcej zasobów za pomocą operacji samoobsługi wyświetlany w tym artykule. Użyj operacje samoobsługi:

* Przenieś zasoby usługi Resource Manager.
* Przenoszenie zasobów klasycznych zgodnie z [ograniczenia wdrażania klasycznego](#classic-deployment-limitations).

Skontaktuj się z [obsługuje](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) konieczność:

* Przenoszenie zasobów dla nowego konta platformy Azure (i dzierżawy usługi Azure Active Directory) i potrzebujesz pomocy z instrukcjami w poprzedniej sekcji.
* Przenoszenie zasobów klasycznych, ale występują problemy z ograniczeniami.

## <a name="services-that-can-be-moved"></a>Usługi, które mogą zostać przeniesione.

Usługi umożliwiające przeniesienie do nowej grupy zasobów i subskrypcji są:

* API Management
* Usługi aplikacji — aplikacje (aplikacje sieci web) — zobacz [ograniczenia usługi aplikacji](#app-service-limitations)
* Certyfikaty usługi App Service
* Application Insights
* Automatyzacja
* Azure Cosmos DB
* Azure Relay
* Batch
* Mapy Bing
* CDN
* Usługi w chmurze — zobacz [Classic deployment ograniczenia](#classic-deployment-limitations)
* Cognitive Services
* Content Moderator
* Data Catalog
* Fabryka danych - V1 można można przenieść, ale przeniesienie V2 (wersja zapoznawcza) nie jest obsługiwana.
* Data Lake Analytics
* Data Lake Store
* DNS
* Event Hubs
* Klastry HDInsight — zobacz [ograniczenia usługi HDInsight](#hdinsight-limitations)
* Centra IoT Hub
* Usługa Key Vault
* Moduły równoważenia obciążenia — zobacz [ograniczenia usługi równoważenia obciążenia](#lb-limitations)
* Log Analytics
* Logic Apps
* Uczenie maszynowe — usługi sieci web mogą zostać przeniesione do grupy zasobów w tej samej subskrypcji, ale inną subskrypcję usługi Machine Learning Studio. Inne zasoby uczenia maszynowego można przenosić między subskrypcjami.
* Media Services
* Mobile Engagement
* Notification Hubs
* Operational Insights
* Operations Management
* Usługa Power BI — zarówno usługi Power BI Embedded i włączenie kolekcji obszarów roboczych usługi BI
* Publiczny adres IP — Zobacz [ograniczenia publicznego adresu IP](#pip-limitations)
* Pamięć podręczna Redis
* Scheduler
* Wyszukiwanie
* Zarządzanie serwerem
* Service Bus
* Service Fabric
* Magazyn
* Magazyn (klasyczne) — zobacz [Classic deployment ograniczenia](#classic-deployment-limitations)
* Stream Analytics — usługi analiza strumienia zadania nie można przenieść uruchomionej w stanie.
* Baza danych SQL server — bazy danych i serwera musi znajdować się w tej samej grupie zasobów. W przypadku przenoszenia programu SQL server, również są przenoszone jej baz danych. Ten problem dotyczy baz danych Azure SQL Database i Azure SQL Data Warehouse. 
* Traffic Manager
* Nie można przenieść maszyny wirtualne — maszyny wirtualne z dyskami zarządzanych. Zobacz [ograniczenia maszyny wirtualne](#virtual-machines-limitations)
* Maszyny wirtualne (klasyczne) — zobacz [Classic deployment ograniczenia](#classic-deployment-limitations)
* Zestawy skalowania maszyny wirtualnej — zobacz [ograniczenia maszyny wirtualne](#virtual-machines-limitations)
* Sieci wirtualne — Zobacz [ograniczenia sieci wirtualnych](#virtual-networks-limitations)
* Visual Studio Team Services — konta usługi VSTS z rozszerzeniem innych niż Microsoft zakupi musi [anulować zakupów](https://go.microsoft.com/fwlink/?linkid=871160) przed konta mogą przenosić między subskrypcjami.
* VPN Gateway

## <a name="services-that-cannot-be-moved"></a>Usługi, których nie można przenieść

Usługi, które aktualnie nie należy włączać przenoszenie zasobu to:

* Usługi domenowe AD
* Usługa kondycji hybrydowe AD
* Application Gateway
* Azure Database for MySQL
* Azure Database for PostgreSQL
* Azure Migrate
* BizTalk Services
* Certyfikaty — można przenieść certyfikaty usługi aplikacji, ale ma przekazane certyfikaty [ograniczenia](#app-service-limitations).
* Włączono DevTest Labs — aby przejść do nowej grupy zasobów w tej samej subskrypcji, ale przenoszenia między subskrypcji nie jest włączona.
* Dynamics LCS
* ExpressRoute
* Usługa Kubernetes
* Moduły równoważenia obciążenia — zobacz [ograniczenia usługi równoważenia obciążenia](#lb-limitations)
* Aplikacje zarządzane
* Zarządzane dysków, zobacz [ograniczenia maszyny wirtualne](#virtual-machines-limitations)
* Publiczny adres IP — Zobacz [ograniczenia publicznego adresu IP](#pip-limitations)
* Magazyn usług odzyskiwania — czy też nie przeniesienie zasobów obliczeniowych, sieci i magazynu skojarzone z magazynu usług odzyskiwania, zobacz [ograniczenia usług odzyskiwania](#recovery-services-limitations).
* Bezpieczeństwo
* Menedżer urządzeń StorSimple
* Sieci wirtualne (klasyczne) — zobacz [Classic deployment ograniczenia](#classic-deployment-limitations)

## <a name="virtual-machines-limitations"></a>Ograniczenia maszyny wirtualne

Dyski zarządzane nie obsługują przenoszenia. To ograniczenie oznacza, że kilka powiązanych zasobów nie można przenieść za. Nie można przenieść:

* Dyski zarządzane
* Maszyny wirtualne z dyskami zarządzanych
* Obrazy utworzone na podstawie dysków zarządzanych
* Migawek utworzonych z dysków zarządzanych
* Zestawy dostępności z maszynami wirtualnymi z dyskami zarządzanych

Mimo że nie można przenieść dyskiem zarządzanym, możesz utworzyć kopię i następnie utwórz nową maszynę wirtualną z istniejącego dysku zarządzanego. Aby uzyskać więcej informacji, zobacz:

* Kopiowanie dysków zarządzanych w tej samej subskrypcji lub różnych subskrypcji z [PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md) lub [wiersza polecenia platformy Azure](../virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md)
* Utwórz maszynę wirtualną przy użyciu istniejącego dysku systemu operacyjnego zarządzanego z [PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md) lub [interfejsu wiersza polecenia Azure](../virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md).

Nie można przenieść maszyny wirtualne utworzone na podstawie zasobów Marketplace z planami dołączony między grupami zasobów lub subskrypcji. Anulowanie zastrzeżenia maszynę wirtualną w bieżącej subskrypcji i Wdróż ponownie w nowej subskrypcji.

Maszyny wirtualne z certyfikatem przechowywane w magazynie kluczy, mogą zostać przeniesione do nowej grupy zasobów w tej samej subskrypcji, ale nie w subskrypcjach.

## <a name="virtual-networks-limitations"></a>Ograniczenia sieci wirtualnej

Podczas przenoszenia sieci wirtualnej, należy przenieść jej zasoby zależne. Na przykład należy przenieść bramy z siecią wirtualną.

Aby przenieść peered sieci wirtualnej, należy najpierw wyłączyć komunikację równorzędną sieci wirtualnej. Po wyłączeniu można przenieść sieci wirtualnej. Po przeniesieniu należy ponownie włączyć komunikację równorzędną sieci wirtualnej.

Nie można przenieść sieć wirtualną do innej subskrypcji, jeśli sieć wirtualna zawiera podsieci z zasobów łącza nawigacji. Na przykład jeśli zasobów pamięci podręcznej Redis jest wdrażana w podsieci, tej podsieci ma łącza nawigacji zasobu.

Nie można przenieść sieć wirtualną do innej subskrypcji, jeśli sieć wirtualna zawiera niestandardowy serwer DNS. Aby przenieść sieć wirtualną, należy ustawić go na serwer domyślny (DNS platformy Azure). Po przeniesieniu należy ponownie skonfigurować niestandardowy serwer DNS.

## <a name="app-service-limitations"></a>Ograniczenia usługi aplikacji

Ograniczenia dotyczące przenoszenia zasobów usługi aplikacji — różnią się w zależności od tego, czy są przenoszenia zasobów w ramach subskrypcji lub do nowej subskrypcji.

Ograniczenia opisane w tych sekcjach dotyczą certyfikaty przekazane, nie certyfikaty usługi aplikacji. Certyfikaty usługi aplikacji można przenieść do nowej grupy zasobów lub subskrypcji bez ograniczeń. Jeśli masz wiele aplikacji sieci web, które używają tego samego certyfikatu usługi aplikacji, najpierw przenieść wszystkie aplikacje sieci web, następnie przenieść certyfikat.

### <a name="moving-within-the-same-subscription"></a>Przenoszenie w ramach tej samej subskrypcji

Podczas przenoszenia aplikacji sieci Web _w ramach tej samej subskrypcji_, nie można przenieść przekazane certyfikaty SSL. Jednak aplikacji sieci Web można przenieść do nowej grupy zasobów, bez przenoszenia jego przekazano certyfikat SSL, a funkcja SSL aplikacji nadal działa.

Jeśli chcesz przenieść certyfikatu SSL za pomocą aplikacji sieci Web, wykonaj następujące czynności:

1.  Przekazany certyfikat należy usunąć z aplikacji sieci Web.
2.  Przenoszenie aplikacji sieci Web.
3.  Przekaż certyfikat do przeniesionego aplikacji sieci Web.

### <a name="moving-across-subscriptions"></a>Przenoszenie między subskrypcjami

Podczas przenoszenia aplikacji sieci Web _w subskrypcjach_, obowiązują następujące ograniczenia:

- Docelowej grupy zasobów nie może mieć żadnych istniejących zasobów usługi aplikacji. Zasoby usługi App Service obejmują:
    - Web Apps
    - Plany usługi App Service
    - Przekazany lub zaimportowanych certyfikatów SSL
    - Środowiska usługi App Service
- Wszystkie zasoby usługi aplikacji w grupie zasobów, muszą zostać przeniesione razem.
- Zasoby usługi aplikacji można przenieść tylko z grupy zasobów, w której zostały pierwotnie utworzone. Jeśli zasób usługi aplikacji nie jest już w jego oryginalnej grupa zasobów, go musi zostać przeniesiona z powrotem do tego oryginalnego grupy zasobów najpierw, a następnie mogą zostać przeniesione w subskrypcjach.

## <a name="classic-deployment-limitations"></a>Wdrożenie klasyczne ograniczenia

Opcje przenoszenia zasobów wdrażać przy użyciu modelu klasycznego różnią się w zależności od tego, czy są przenoszenia zasobów w ramach subskrypcji lub do nowej subskrypcji.

### <a name="same-subscription"></a>Tej samej subskrypcji

Przenoszenie zasobów między grupami zasobów w innej grupie zasobów w ramach tej samej subskrypcji, mają zastosowanie następujące ograniczenia:

* Nie można przenieść sieci wirtualnych (klasyczny).
* Maszyny wirtualne (klasyczne) muszą zostać przeniesione z usługą w chmurze.
* Usługi w chmurze tylko przeniesienia po przeniesieniu obejmuje wszystkie maszyny wirtualne.
* Usługi w chmurze tylko jeden mogą zostać przeniesione w czasie.
* Jednocześnie można przenosić tylko jedno konto magazynu (klasyczne).
* Nie można przenieść konta magazynu (klasyczne) w tej samej operacji z maszyny wirtualnej lub usługi w chmurze.

Aby przenieść zasoby klasyczne do nowej grupy zasobów w ramach tej samej subskrypcji, użyj operacji przenoszenia standardowe, za pomocą [portal](#use-portal), [programu Azure PowerShell](#use-powershell), [interfejsu wiersza polecenia Azure](#use-azure-cli), lub [interfejsu API REST](#use-rest-api). Możesz użyć tej samej operacji, ponieważ używane do przenoszenia zasoby usługi Resource Manager.

### <a name="new-subscription"></a>Nowa subskrypcja

Podczas przenoszenia zasobów na nową subskrypcję, obowiązują następujące ograniczenia:

* Wszystkie zasoby klasyczne w subskrypcji muszą zostać przeniesione w tej samej operacji.
* Subskrypcja docelowa nie może zawierać inne zasoby klasyczne.
* Przeniesienie się żądać tylko wtedy za pomocą osobnych interfejsu API REST dla przenosi klasycznego. Standardowe polecenia move Menedżera zasobów nie działają podczas przenoszenia zasobów klasycznych na nową subskrypcję.

Aby przenieść zasoby klasyczne do nowej subskrypcji, użyj operacji REST, które są specyficzne dla zasobów klasycznych. Aby używać REST, wykonaj następujące czynności:

1. Sprawdź, czy subskrypcja źródłowa mogą uczestniczyć w przeniesienia między subskrypcjami. Użyj następujących operacji:

  ```HTTP
  POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
  ```

     W treści żądania obejmują:

  ```json
  {
    "role": "source"
  }
  ```

     Odpowiedź dla operacji sprawdzania poprawności jest w następującym formacie:

  ```json
  {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
  }
  ```

2. Sprawdź, czy subskrypcji docelowej mogą uczestniczyć w przeniesienia między subskrypcjami. Użyj następujących operacji:

  ```HTTP
  POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
  ```

     W treści żądania obejmują:

  ```json
  {
    "role": "target"
  }
  ```

     Odpowiedź jest w tym samym formacie co sprawdzania poprawności subskrypcji źródłowej.
3. Jeśli obie subskrypcje przeszedł sprawdzania poprawności, Przenieś wszystkie zasoby klasyczne z jedną subskrypcję, do innej subskrypcji z następujących operacji:

  ```HTTP
  POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
  ```

    W treści żądania obejmują:

  ```json
  {
    "target": "/subscriptions/{target-subscription-id}"
  }
  ```

Operacja może trwać kilka minut.

## <a name="recovery-services-limitations"></a>Ograniczenia usługi odzyskiwania

Przenieś nie jest włączona dla magazynu, sieci lub używana do konfigurowania odzyskiwania po awarii z usługą Azure Site Recovery zasoby obliczeniowe.

Na przykład załóżmy, że skonfigurowano replikację maszyn lokalnych do konta magazynu (Storage1) i chcesz chronionej maszyny znaleziona po w tryb failover na platformie Azure jako maszynę wirtualną (VM1) dołączona do sieci wirtualnej (Network1). Nie można przenieść żadnego z tych zasobów Azure - Storage1 VM1 i Network1 - między grupami zasobów w ramach tej samej subskrypcji lub różnych subskrypcji.

Aby przenieść Maszynę wirtualną w **kopia zapasowa Azure** między grupami zasobów:
 1. Tymczasowo Zatrzymaj wykonywanie kopii zapasowej i Zachowaj dane kopii zapasowej
 2. Przenieś maszynę Wirtualną do docelowej grupy zasobów
 3. Włącz ponownie jej ochronę w tym samym/nowy magazyn, które użytkownicy mogą przywracać z dostępnych punktów przywracania utworzone przed operacji przenoszenia.
Jeśli użytkownik przesuwa kopii zapasowej maszyny Wirtualnej w subskrypcjach, krok 1 i 2 pozostają takie same. W kroku 3 użytkownik musi ochronę maszyny Wirtualnej w obszarze Nowy magazyn istnieje / utworzone w docelowej subskrypcji. Magazyn usług odzyskiwania nie obsługuje kopii zapasowych między subskrypcji.

## <a name="hdinsight-limitations"></a>Ograniczenia usługi HDInsight

Klastry usługi HDInsight można przenieść do nowej subskrypcji lub grupy zasobów. Jednak nie można przenieść w subskrypcjach zasoby sieciowe połączone z klastrem usługi HDInsight (na przykład sieci wirtualnej, karty Sieciowej lub usługi równoważenia obciążenia). Ponadto nie można przenieść do nowej grupy zasobów kart interfejsu Sieciowego, który jest dołączony do maszyny wirtualnej do klastra.

Podczas przenoszenia klastra usługi HDInsight na nową subskrypcję, należy najpierw przenieść innych zasobów (takich jak konta magazynu). Następnie przenieś klastra usługi HDInsight przez samego siebie.

## <a name="search-limitations"></a>Ograniczenia dotyczące wyszukiwania

Nie można przenieść wielu zasobów wyszukiwania w całości umieszczony w różnych regionach.
W takim przypadku należy przenieść je oddzielnie.

## <a name="lb-limitations"></a> Ograniczenia usługi równoważenia obciążenia

Podstawowy SKU usługi równoważenia obciążenia, mogą zostać przeniesione.
Nie można przenieść standardowy SKU usługi równoważenia obciążenia.

## <a name="pip-limitations"></a> Publiczny ograniczenia adresu IP

Podstawowe publicznego adresu IP SKU mogą zostać przeniesione.
Nie można przenieść standardowe publicznego adresu IP jednostki SKU.

## <a name="use-portal"></a>Korzystanie z portalu

Instrukcję przenoszenia zasobów, wybierz grupę zasobów, zawierające te zasoby, a następnie wybierz **Przenieś** przycisku.

![Przenoszenie zasobów](./media/resource-group-move-resources/select-move.png)

Określ, czy przenosisz zasobów do nowej grupy zasobów lub nową subskrypcję.

Wybierz zasoby do przeniesienia i docelowej grupy zasobów. Potwierdzić, że trzeba zaktualizować skrypty dla tych zasobów i wybierz **OK**. Jeśli w poprzednim kroku zostało wybrane ikonę Edytuj subskrypcję, musisz wybrać subskrypcji docelowej.

![Wybierz miejsce docelowe](./media/resource-group-move-resources/select-destination.png)

W **powiadomienia**, zobaczysz, że działa operacji przenoszenia.

![Pokaż stan przenoszenia](./media/resource-group-move-resources/show-status.png)

Po ukończeniu, użytkownik jest powiadamiany o wynik.

![Pokaż Przenieś wyników](./media/resource-group-move-resources/show-result.png)

## <a name="use-powershell"></a>Korzystanie z programu PowerShell

Aby przenieść istniejące zasoby do innej grupy zasobów lub subskrypcji, należy użyć [AzureRmResource przenoszenia](/powershell/module/azurerm.resources/move-azurermresource) polecenia. Poniższy przykład przedstawia sposób przenoszenia wielu zasobów do nowej grupy zasobów.

```powershell
$webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Aby przejść do nowej subskrypcji, zawierają wartość `DestinationSubscriptionId` parametru.

## <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby przenieść istniejące zasoby do innej grupy zasobów lub subskrypcji, należy użyć [przeniesienia zasobu az](/cli/azure/resource?view=azure-cli-latest#az_resource_move) polecenia. Podaj identyfikatorów zasobów przenoszenia zasobów. Poniższy przykład przedstawia sposób przenoszenia wielu zasobów do nowej grupy zasobów. W `--ids` parametru, podaj rozdzieloną spacjami listę identyfikatorów przenoszenia zasobów.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Aby przejść do nowej subskrypcji, należy podać `--destination-subscription-id` parametru.

## <a name="use-rest-api"></a>Korzystanie z interfejsu API REST

Aby przenieść istniejące zasoby do innej grupy zasobów lub subskrypcji, uruchom polecenie:

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

W treści żądania Określ docelowa grupa zasobów i zasobów, aby przenieść. Aby uzyskać więcej informacji na temat operacji REST przenoszenia zobacz [przenoszenia zasobów](/rest/api/resources/Resources/MoveResources).

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat poleceń cmdlet programu PowerShell do zarządzania subskrypcją, zobacz [przy użyciu programu Azure PowerShell z usługą Resource Manager](powershell-azure-resource-manager.md).
* Informacje na temat polecenia wiersza polecenia platformy Azure do zarządzania subskrypcją, zobacz [przy użyciu wiersza polecenia platformy Azure z usługą Resource Manager](xplat-cli-azure-resource-manager.md).
* Aby dowiedzieć się więcej o funkcjach portalu do zarządzania subskrypcją, zobacz [przy użyciu portalu Azure do zarządzania zasobami](resource-group-portal.md).
* Informacje na temat stosowania logiczna organizacji do zasobów, zobacz [przy użyciu tagów do organizowania zasobów](resource-group-using-tags.md).
