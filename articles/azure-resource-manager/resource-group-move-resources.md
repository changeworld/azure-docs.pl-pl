---
title: Przenoszenie zasobów platformy Azure do nowej subskrypcji lub grupy zasobów | Dokumentacja firmy Microsoft
description: Umożliwia przenoszenie zasobów do nowej grupy zasobów lub subskrypcji usługi Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.assetid: ab7d42bd-8434-4026-a892-df4a97b60a9b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/07/2018
ms.author: tomfitz
ms.openlocfilehash: 0b0ddedde49208a85628cdfc226f870a32ff7170
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "45985868"
---
# <a name="move-resources-to-new-resource-group-or-subscription"></a>Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji

W tym artykule pokazano, jak przenieść zasoby na nową subskrypcję lub grupę zasobów w tej samej subskrypcji. Można przenieść zasobu, można użyć portalu, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST. Operacji przenoszenia, w tym artykule są dostępne bez żadnych pomocy od działu pomocy technicznej platformy Azure.

Podczas przenoszenia zasobów, zarówno grupy źródłowej i docelowej grupy są zablokowane podczas operacji. Pisanie i operacje usuwania są zablokowane na temat grup zasobów, dopiero po zakończeniu przenoszenia. Ta blokada oznacza, że nie Dodawanie, aktualizowanie lub usuwanie zasobów w grupach zasobów, ale nie oznacza, że zasoby są zablokowane. Na przykład jeśli zostanie przeniesiony do nowej grupy zasobów programu SQL Server i jego bazy danych, aplikacji korzystającej z bazy danych środowisk bez przestojów. Nadal może odczytywać i zapisywać w bazie danych.

Nie można zmienić lokalizacji zasobu. Przenoszenie zasobu tylko przenosi je do nowej grupy zasobów. Nowa grupa zasobów może mieć inną lokalizację, ale to nie ulega zmianie lokalizacji zasobu.

> [!NOTE]
> W tym artykule opisano sposób przenoszenia zasobów na platformie Azure istniejących kont oferty. Jeśli rzeczywiście chcesz zmienić konta platformy Azure, oferty (na przykład uaktualnianie z rzeczywistym użyciem, aby Zapłać z góry) przy jednoczesnym dalszym pracować z istniejących zasobów, zobacz [Przełącz subskrypcji platformy Azure na inną ofertę](../billing/billing-how-to-switch-azure-offer.md).
>
>

## <a name="checklist-before-moving-resources"></a>Listę kontrolną przed przenoszeniem zasobów

Przed przeniesieniem zasobu należy wykonać kilka ważnych kroków. Dzięki sprawdzeniu tych warunków można uniknąć błędów.

1. Subskrypcje źródłowe i docelowe muszą istnieć w tej samej [dzierżawy usługi Azure Active Directory](../active-directory/develop/quickstart-create-new-tenant.md). Aby sprawdzić, czy obie subskrypcje mają ten sam identyfikator dzierżawy, należy użyć programu Azure PowerShell lub wiersza polecenia platformy Azure.

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

  Jeśli identyfikatorów dzierżawy subskrypcje źródłowe i docelowe nie są takie same, należy użyć następujących metod, aby uzgodnić identyfikatorów dzierżawy:

  * [Transfer ownership of an Azure subscription to another account](../billing/billing-subscription-transfer.md) (Przenoszenie własności subskrypcji platformy Azure na inne konto)
  * [Jak skojarzyć lub dodać subskrypcję platformy Azure do usługi Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. Subskrypcja docelowa musi być zarejestrowana dla dostawcy przenoszonego zasobu. Jeśli nie, pojawi się komunikat o błędzie informujący, że **subskrypcja nie jest zarejestrowana dla typu zasobu**. Ten problem może wystąpić podczas przenoszenia zasobu do nowej subskrypcji, która nigdy nie była używana z tym typem zasobu.

  W przypadku programu PowerShell Użyj następujących poleceń, można pobrać stanu rejestracji:

  ```powershell
  Set-AzureRmContext -Subscription <destination-subscription-name-or-id>
  Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
  ```

  Aby zarejestrować dostawcę zasobów, należy użyć:

  ```powershell
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
  ```

  Wiersza polecenia platformy Azure Użyj następujących poleceń, aby uzyskać stan rejestracji:

  ```azurecli-interactive
  az account set -s <destination-subscription-name-or-id>
  az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
  ```

  Aby zarejestrować dostawcę zasobów, należy użyć:

  ```azurecli-interactive
  az provider register --namespace Microsoft.Batch
  ```

1. Konto przenoszenia zasobów musi mieć co najmniej następujące uprawnienia:

   * **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action** na źródłową grupę zasobów.
   * **Microsoft.Resources/subscriptions/resourceGroups/write** w docelowej grupie zasobów.

1. Przed przeniesieniem zasoby, sprawdź limity przydziału subskrypcji dla subskrypcji, którą przenosisz zasoby. Jeśli przenoszenia zasobów oznacza, że subskrypcja przekroczy limit, należy przejrzeć, czy możesz poprosić o zwiększenie limitu przydziału. Aby uzyskać listę limitów i sposób poprosić o zwiększenie zobacz [subskrypcji platformy Azure i limity, przydziały i ograniczenia](../azure-subscription-service-limits.md).

1. Jeśli to możliwe, podział dużych przenosi do operacji przenoszenia oddzielne. Menedżer zasobów natychmiast kończy prób przenosić ponad 800 zasoby w ramach jednej operacji. Jednak przenoszenia zasobów mniej niż 800 może również zakończyć się niepowodzeniem, przekroczeniem limitu czasu.

1. Usługa musi mieć możliwość przenoszenia zasobów. Aby ustalić, czy przeniesienie kończy się pomyślnie, [zweryfikować Twoje żądanie przeniesienia](#validate-move). Zobacz sekcje poniżej, w tym artykule [usługi umożliwiają przenoszenie zasobów](#services-that-can-be-moved) oraz tych, które [usługi nie umożliwiają przenoszenie zasobów](#services-that-cannot-be-moved).

## <a name="when-to-call-support"></a>Kiedy z działem pomocy technicznej

Można przenieść najwięcej zasobów za pomocą operacji samoobsługi przedstawione w tym artykule. Użyj operacji samoobsługi, aby:

* Przenieś zasoby usługi Resource Manager.
* Przenoszenie zasobów klasycznych zgodnie z opisem w [ograniczeniami wdrożenia klasycznego](#classic-deployment-limitations).

Skontaktuj się z pomocą [obsługuje](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) potrzebne:

* Przenoszenie zasobów do nowego konta platformy Azure (i dzierżawy usługi Azure Active Directory) i potrzebujesz pomocy z instrukcjami w poprzedniej sekcji.
* Przenoszenie zasobów klasycznych, ale występują problemy związane z ograniczeniami.

## <a name="validate-move"></a>Waliduj przeniesienie

[Zweryfikować operacji przeniesienia](/rest/api/resources/resources/validatemoveresources) umożliwia testowanie scenariusza przenoszenia bez faktycznego przenoszenia zasobów. Ta operacja umożliwia określenie, przeniesienie, zostanie wykonane pomyślnie. Aby wykonać tę operację, musisz mieć:

* Nazwa źródłowa grupa zasobów
* Identyfikator zasobu docelowa grupa zasobów
* Identyfikator zasobu poszczególnych zasobów, aby przenieść
* [token dostępu](/rest/api/azure/#acquire-an-access-token) dla swojego konta

Wyślij żądanie następujące:

```
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2018-02-01
Authorization: Bearer <access-token>
Content-type: application/json
```

Z treści żądania:

```json
{
 "resources": ['<resource-id-1>', '<resource-id-2>'],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Jeśli żądanie jest prawidłowo sformatowany, zwraca wartość operacji:

```
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

Kod stanu 202 wskazuje żądanie weryfikacji został zaakceptowany, ale nie zostało jeszcze nieustalona, operację przenoszenia, zostanie wykonana pomyślnie. `location` Wartość zawiera adres URL, którego używasz, aby sprawdzić stan operacji długotrwałej.  

Aby sprawdzić stan, Wyślij następujące żądanie:

```
GET <location-url>
Authorization: Bearer <access-token>
```

Podczas operacji jest nadal uruchomione, nadal jest wyświetlany kod stanu 202. Oczekiwania w sekundach `retry-after` wartość przed podjęciem ponownej próby. Jeśli sprawdzanie poprawności operacji przenoszenia przebiegnie pomyślnie, zostanie wyświetlony kod stanu 204. W przypadku niepowodzenia weryfikacji przeniesienia pojawi się komunikat o błędzie, takich jak:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="services-that-can-be-moved"></a>Usługi, które mogą zostać przeniesione

Poniższa lista zawiera podsumowanie ogólne usług platformy Azure, które mogą zostać przeniesione do nowej grupy zasobów i subskrypcji. Aby uzyskać więcej szczegółów, zobacz [przenoszenie obsługi operacji dla zasobów](move-support-resources.md).

* Analysis Services
* API Management
* Aplikacje usługi App Service (aplikacje sieci web) — zobacz [ograniczenia usługi App Service](#app-service-limitations)
* Certyfikaty usługi App Service
* Application Insights
* Automatyzacja
* Azure Active Directory B2C
* Azure Cosmos DB
* Azure DevOps — organizacjom przeprowadzającym platformy Azure z rozszerzeniem firmy Microsoft kupuje musi [anulować opłacenia zakupów](https://go.microsoft.com/fwlink/?linkid=871160) przed konta mogą przenosić między subskrypcjami.
* Azure Maps
* Azure Relay
* Usługa Azure Stack — rejestracji
* Batch
* BizTalk Services
* Bot Service
* CDN
* Cloud Services — zobacz [ograniczenia wdrożenia klasycznego](#classic-deployment-limitations)
* Cognitive Services
* Container Registry
* Content Moderator
* Cost Management
* Customer Insights
* Data Catalog
* Fabryka danych
* Data Lake Analytics
* Data Lake Store
* DNS
* Event Grid
* Event Hubs
* Klastry HDInsight — zobacz [ograniczenia HDInsight](#hdinsight-limitations)
* IoT Central
* Centra IoT Hub
* Usługa Key Vault
* Moduły równoważenia obciążenia — zobacz [ograniczenia modułu równoważenia obciążenia](#lb-limitations)
* Log Analytics
* Logic Apps
* Machine Learning — Machine Learning Studio, usług sieci web mogą zostać przeniesione do grupy zasobów w tej samej subskrypcji, ale nie w ramach innej subskrypcji. Inne zasoby usługi Machine Learning można przenosić między subskrypcjami.
* Tożsamość zarządzana — przypisanych przez użytkownika
* Media Services
* Mobile Engagement
* Notification Hubs
* Operational Insights
* Operations Management
* Pulpity nawigacyjne portalu
* Usługa Power BI — zarówno Power BI Embedded i Power kolekcji obszarów roboczych usługi BI
* Publiczny adres IP — Zobacz [ograniczenia publicznego adresu IP](#pip-limitations)
* Pamięć podręczna redis — skonfigurowanie wystąpienia usługi Redis Cache za pomocą sieci wirtualnej wystąpienia nie można przenieść do innej subskrypcji. Zobacz [ograniczenia sieci wirtualnych](#virtual-networks-limitations).
* Scheduler
* Wyszukiwanie
* Service Bus
* Service Fabric
* Usługa Service Fabric siatki
* Usługa SignalR
* Nie można przenieść magazyn — konta magazynu w różnych regionach, w tej samej operacji. Zamiast tego należy użyć oddzielnych operacji dla każdego regionu.
* Magazyn (klasyczny) — zobacz [ograniczenia wdrożenia klasycznego](#classic-deployment-limitations)
* Stream Analytics — Stream Analytics, zadania nie można przenieść uruchamianego w stanie.
* Bazy danych programu SQL server — bazy danych i serwera musi znajdować się w tej samej grupie zasobów. Gdy przesuniesz programu SQL server, jego baz danych są również przenoszone. To zachowanie ma zastosowanie do baz danych Azure SQL Database i Azure SQL Data Warehouse.
* Time Series Insights
* Traffic Manager
* Nie można przenieść maszyny wirtualne — maszyny wirtualne z dyskami zarządzanymi. Zobacz [ograniczenia maszyn wirtualnych](#virtual-machines-limitations)
* Maszyny wirtualne (klasyczne) — zobacz [ograniczenia wdrożenia klasycznego](#classic-deployment-limitations)
* Zestawy skalowania maszyn wirtualnych — zobacz [ograniczenia maszyn wirtualnych](#virtual-machines-limitations)
* Sieci wirtualne — Zobacz [ograniczenia sieci wirtualnych](#virtual-networks-limitations)
* VPN Gateway

## <a name="services-that-cannot-be-moved"></a>Usługi, których nie można przenieść

Poniższa lista zawiera podsumowanie ogólne usług platformy Azure, których nie można przenieść do nowej grupy zasobów i subskrypcji. Aby uzyskać więcej szczegółów, zobacz [przenoszenie obsługi operacji dla zasobów](move-support-resources.md).

* Usługi domenowe AD
* Usługa kondycji AD hybrydowych
* Application Gateway
* Azure Database for MySQL
* Azure Database for PostgreSQL
* Migracja bazy danych platformy Azure
* Azure Databricks
* Azure Migrate
* Usługa Batch — sztuczna inteligencja
* Certyfikaty — certyfikaty usługi App Service można przenosić, ale zostały przekazane certyfikaty [ograniczenia](#app-service-limitations).
* Container Instances
* Container Service
* Data Box
* Miejsca do magazynowania dev
* Dynamics LCS
* ExpressRoute
* Kubernetes Service
* Włączono Lab Services — przejście do nowej grupy zasobów w tej samej subskrypcji, ale przenoszenie między subskrypcjami nie jest włączona.
* Moduły równoważenia obciążenia — zobacz [ograniczenia modułu równoważenia obciążenia](#lb-limitations)
* Aplikacje zarządzane
* Managed Disks — zobacz [ograniczenia maszyn wirtualnych](#virtual-machines-limitations)
* Microsoft Genomics
* NetApp
* Publiczny adres IP — Zobacz [ograniczenia publicznego adresu IP](#pip-limitations)
* Magazyn usługi Recovery Services - również nie przeniesienie zasobów obliczeniowych, sieci i magazynu skojarzonego z magazynem usługi Recovery Services, zobacz [ograniczenia usług Recovery Services](#recovery-services-limitations).
* Oprogramowanie SAP HANA na platformie Azure
* Bezpieczeństwo
* Site Recovery
* Menedżer urządzeń StorSimple
* Sieci wirtualne (klasyczne) — zobacz [ograniczenia wdrożenia klasycznego](#classic-deployment-limitations)

## <a name="virtual-machines-limitations"></a>Ograniczenia dotyczące maszyn wirtualnych

Dyski zarządzane nie obsługuje przenoszenia. To ograniczenie oznacza, że kilka powiązanych zasobów nie można przenieść za. Nie można przenieść:

* Dyski zarządzane
* Maszyny wirtualne z dyskami zarządzanymi
* Obrazów utworzonych przy użyciu dysków zarządzanych
* Migawek utworzonych z dyskami zarządzanymi
* Zestawy dostępności mające maszyny wirtualne z dyskami zarządzanymi

Mimo że nie można przenieść dysk zarządzany, można utworzyć kopię i następnie utworzyć nową maszynę wirtualną z istniejącego dysku zarządzanego. Aby uzyskać więcej informacji, zobacz:

* Kopiowanie dysków zarządzanych w ramach tej samej subskrypcji lub w innej subskrypcji przy użyciu [PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md) lub [wiersza polecenia platformy Azure](../virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md)
* Utwórz maszynę wirtualną przy użyciu istniejącego zarządzanego dysku systemu operacyjnego za pomocą [PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md) lub [wiersza polecenia platformy Azure](../virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md).

Utworzona na podstawie zasobów w portalu Marketplace z planami dołączone maszyny wirtualne nie można przenosić między grupami zasobów lub subskrypcji. Anulowanie aprowizacji maszyny wirtualnej w bieżącej subskrypcji i Wdróż ponownie w nowej subskrypcji.

Maszyn wirtualnych przy użyciu certyfikatu przechowywanego w usłudze Key Vault można przenieść do nowej grupy zasobów w tej samej subskrypcji, ale nie w subskrypcjach.

## <a name="virtual-networks-limitations"></a>Wirtualne sieci ograniczenia

Podczas przenoszenia sieci wirtualnej, możesz również przenieść jego zasobów zależnych. Dla bramy sieci VPN należy przenieść adresy IP, bramy sieci wirtualnej i wszystkie zasoby skojarzone z nimi połączenie. Bramy sieci lokalnej może znajdować się w innej grupie zasobów.

Aby przenieść równorzędnej sieci wirtualnej, należy najpierw wyłączyć komunikacji równorzędnej sieci wirtualnej. Po wyłączeniu można przenieść sieci wirtualnej. Po przeniesieniu ponownie włączyć komunikację równorzędną sieci wirtualnej.

Nie można przenieść sieci wirtualnej do innej subskrypcji, jeśli sieć wirtualna zawiera podsieci za pomocą linków nawigacji zasobów. Na przykład jeśli zasób pamięci podręcznej redis Cache jest wdrożona w podsieci, tej podsieci ma linku nawigacji zasobu.

## <a name="app-service-limitations"></a>Ograniczenia usługi App Service

Ograniczenia dotyczące przenoszenia zasobów usługi App Service zależą od tego, czy przenosisz zasoby w ramach subskrypcji lub do nowej subskrypcji.

Ograniczenia opisane w tych sekcjach dotyczą przekazane certyfikaty, a nie certyfikatów usługi App Service. Certyfikaty usługi App Service można przenieść do nowej grupy zasobów lub subskrypcji bez ograniczeń. Jeśli masz wiele aplikacji sieci web, które używają tego samego certyfikatu usługi App Service, najpierw przenieść wszystkie aplikacje sieci web, następnie przenieść certyfikat.

### <a name="moving-within-the-same-subscription"></a>Przenoszenie w ramach tej samej subskrypcji

Podczas przenoszenia aplikacji sieci Web _w ramach tej samej subskrypcji_, nie można przenieść przekazane certyfikaty SSL. Jednak aplikacja sieci Web można przenieść do nowej grupy zasobów, bez konieczności przenoszenia jego przekazanego certyfikatu SSL i funkcje protokołu SSL aplikacji nadal działa.

Jeśli chcesz przenieść certyfikatu SSL z aplikacją sieci Web, wykonaj następujące czynności:

1.  Usuń przekazany certyfikat z aplikacji sieci Web.
2.  Przenoszenie aplikacji sieci Web.
3.  Przekaż certyfikat do przeniesionych aplikacji sieci Web.

### <a name="moving-across-subscriptions"></a>Przenoszenie między subskrypcjami

Podczas przenoszenia aplikacji sieci Web _w subskrypcjach_, obowiązują następujące ograniczenia:

- Docelowa grupa zasobów nie może mieć żadnych istniejących zasobów usługi App Service. Zasoby usługi App Service obejmują:
    - Web Apps
    - Plany usługi App Service
    - Przekazane lub zaimportowane certyfikaty SSL
    - Środowiska usługi App Service
- Wszystkie zasoby usługi App Service w grupie zasobów, muszą zostać przeniesione razem.
- Zasoby usługi App Service mogą być przenoszone z grupy zasobów, w której zostały pierwotnie utworzone. Jeśli zasób usługi App Service nie jest już w jego oryginalnej grupie zasobów, należy je przenieść wróć do tej grupy oryginalnego najpierw, a następnie mogą być przenoszone między subskrypcjami.

## <a name="classic-deployment-limitations"></a>Ograniczenia wdrożenia klasycznego

Opcje przenoszenia zasobów wdrożonych za pośrednictwem modelu klasycznego się różnić w zależności od tego, czy przenosisz zasoby w ramach subskrypcji lub do nowej subskrypcji.

### <a name="same-subscription"></a>Tej samej subskrypcji

Podczas przenoszenia zasobów między grupami zasobów do innej grupy zasobów w ramach tej samej subskrypcji, obowiązują następujące ograniczenia:

* Nie można przenieść sieci wirtualne (klasyczne).
* Maszyny wirtualne (klasyczne) należy przenieść z usługą w chmurze.
* Usługa w chmurze mogą być przenoszone po przeniesieniu obejmuje wszystkie maszyny wirtualne.
* Jednocześnie można przenosić tylko jedną usługę w chmurze.
* Jednocześnie można przenosić tylko jedno konto magazynu (klasyczne).
* Nie można przenieść konta magazynu (klasyczne) w tej samej operacji przy użyciu maszyny wirtualnej lub usługi w chmurze.

Aby przenieść zasobów klasycznych do nowej grupy zasobów w ramach tej samej subskrypcji, użyj operacji przenoszenia standard, za pośrednictwem [portal](#use-portal), [programu Azure PowerShell](#use-powershell), [wiersza polecenia platformy Azure](#use-azure-cli), lub [interfejsu API REST](#use-rest-api). Możesz użyć tych samych operacji jak używane do przenoszenia zasobów usługi Resource Manager.

### <a name="new-subscription"></a>Nowa subskrypcja

Podczas przenoszenia zasobów do nowej subskrypcji, obowiązują następujące ograniczenia:

* Wszystkie zasoby klasyczne w subskrypcji należy przenieść w tej samej operacji.
* Subskrypcja docelowa nie może zawierać inne zasoby klasyczne.
* Przeniesienie można jedynie żądać użycie oddzielnych interfejsu API REST, aby poruszać się klasycznego. Standardowe polecenia move Menedżera zasobów nie działają podczas przenoszenia zasobów klasycznych do nowej subskrypcji.

Aby przenieść zasobów klasycznych do nowej subskrypcji, użyj operacji REST, które są specyficzne dla zasobów klasycznych. Aby korzystanie z interfejsu REST, wykonaj następujące czynności:

1. Sprawdź, jeśli subskrypcja źródłowa mogą brać udział w przenoszenia między subskrypcjami. Użyj następujących operacji:

  ```HTTP
  POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
  ```

     W treści żądania obejmują:

  ```json
  {
    "role": "source"
  }
  ```

     Odpowiedź dla operacji sprawdzania poprawności znajduje się w następującym formacie:

  ```json
  {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
  }
  ```

2. Sprawdź, jeśli subskrypcja docelowa mogą brać udział w przenoszenia między subskrypcjami. Użyj następujących operacji:

  ```HTTP
  POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
  ```

     W treści żądania obejmują:

  ```json
  {
    "role": "target"
  }
  ```

     Odpowiedź jest w formacie sprawdzania poprawności subskrypcji źródłowej.
3. Jeśli obie subskrypcje pomyślnie weryfikacji, Przenieś wszystkie zasoby klasyczne z jednej subskrypcji do innej subskrypcji za pomocą następujących operacji:

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

## <a name="recovery-services-limitations"></a>Ograniczenia usług odzyskiwania

Przeniesienie nie jest włączone dla zasobów magazynu, sieci lub obliczeń umożliwia konfigurowanie odzyskiwania po awarii przy użyciu usługi Azure Site Recovery.

Na przykład załóżmy, że zostały skonfigurowane do replikacji maszyn lokalnych na koncie magazynu (Storage1) i chcesz chronionej maszyny, co pozwoli uzyskać po włączeniu trybu failover na platformie Azure jako maszyna wirtualna (MW1) dołączone do sieci wirtualnej (Network1). Nie można przenieść jedną z następujących zasobów platformy Azure — Storage1, VM1 i Network1 — między grupami zasobów w ramach tej samej subskrypcji lub w różnych subskrypcjach.

Aby przenieść Maszynę wirtualną zarejestrowane w **usługa Azure backup** między grupami zasobów:
 1. Tymczasowo Zatrzymaj kopię zapasową i Zachowaj dane kopii zapasowej
 2. Przenieś maszynę Wirtualną do docelowej grupy zasobów
 3. Włącz ponownie jej ochronę w ramach tego samego/nowego magazynu, które użytkownicy mogą przywracać z dostępnych punktów przywracania utworzonych przed operacji przenoszenia.
Jeśli użytkownik przenosi kopii zapasowej maszyny Wirtualnej między subskrypcjami, krok 1 i 2 pozostają takie same. W kroku 3 użytkownik musi ochronę maszyny Wirtualnej w nowym magazynie obecne / utworzonych w subskrypcji docelowej. Magazyn usługi Recovery Services nie obsługuje tworzenia kopii zapasowych między subskrypcjami.

## <a name="hdinsight-limitations"></a>Ograniczenia HDInsight

Klastry HDInsight można przenieść do nowej subskrypcji lub grupy zasobów. Jednak nie można przenosić między subskrypcjami, zasoby sieciowe połączone z klastrem HDInsight (na przykład sieci wirtualnej, kart interfejsu Sieciowego lub modułu równoważenia obciążenia). Ponadto nie można przenieść do nowej grupy zasobów interfejsu Sieciowego, który jest dołączony do maszyny wirtualnej do klastra.

Podczas przenoszenia klastra usługi HDInsight do nowej subskrypcji, należy najpierw przenieść innych zasobów (np. konta magazynu). Następnie przenieś klaster HDInsight samodzielnie.

## <a name="search-limitations"></a>Ograniczenia dotyczące wyszukiwania

Nie można przenieść wiele zasobów wyszukiwania w całości umieszczony w różnych regionach.
W takim przypadku należy przenieść je oddzielnie.

## <a name="lb-limitations"></a> Ograniczenia dotyczące modułu równoważenia obciążenia

Podstawowa jednostka SKU modułu równoważenia obciążenia mogą być przenoszone.
Nie można przenieść standardowa jednostka SKU modułu równoważenia obciążenia.

## <a name="pip-limitations"></a> Publiczne ograniczenia adresów IP

Można przenieść podstawowa publiczny adres IP jednostki SKU.
Nie można przenieść standardowego publicznego adresu IP jednostki SKU.

## <a name="use-portal"></a>Korzystanie z portalu

Instrukcję przenoszenia zasobów, wybierz grupę zasobów zawierającą te zasoby, a następnie wybierz **przenieść** przycisku.

![Przenoszenie zasobów](./media/resource-group-move-resources/select-move.png)

Wybierz, czy przenosisz zasoby do nowej grupy zasobów lub nowej subskrypcji.

Wybierz zasoby do przeniesienia i docelowa grupa zasobów. Potwierdzić, że należy zaktualizować skrypty dla tych zasobów, a następnie wybierz pozycję **OK**. Jeśli w poprzednim kroku wybrano opcję edycji Ikona subskrypcji, możesz również wybrać subskrypcja docelowa.

![Wybierz miejsce docelowe](./media/resource-group-move-resources/select-destination.png)

W **powiadomienia**, zobaczysz, że działa operacji przenoszenia.

![Pokaż stan przenoszenia](./media/resource-group-move-resources/show-status.png)

Po ukończeniu, otrzymasz powiadomienie o wyniku.

![Pokaż wynik przenoszenia](./media/resource-group-move-resources/show-result.png)

## <a name="use-powershell"></a>Korzystanie z programu PowerShell

Aby przenieść istniejące zasoby do innej grupy zasobów lub subskrypcji, użyj [Move-AzureRmResource](/powershell/module/azurerm.resources/move-azurermresource) polecenia. Poniższy przykład pokazuje, jak przenieść wiele zasobów do nowej grupy zasobów.

```powershell
$webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Aby przejść do nowej subskrypcji, należy dołączyć wartość dla `DestinationSubscriptionId` parametru.

## <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby przenieść istniejące zasoby do innej grupy zasobów lub subskrypcji, użyj [przenoszenia zasobów az](/cli/azure/resource?view=azure-cli-latest#az-resource-move) polecenia. Podaj identyfikatory zasobów można przenieść zasobu. Poniższy przykład pokazuje, jak przenieść wiele zasobów do nowej grupy zasobów. W `--ids` parametru, podaj listę rozdzielonych spacjami identyfikatorów można przenieść zasobów.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Aby przejść do nowej subskrypcji, podać `--destination-subscription-id` parametru.

## <a name="use-rest-api"></a>Korzystanie z interfejsu API REST

Aby przenieść istniejące zasoby do innej grupy zasobów lub subskrypcji, uruchom polecenie:

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

W treści żądania należy określić docelową grupę zasobów i zasobów, aby przenieść. Aby uzyskać więcej informacji na temat operacji REST przenoszenia zobacz [przenoszenia zasobów](/rest/api/resources/Resources/MoveResources).

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat poleceń cmdlet programu PowerShell dotyczące zarządzania subskrypcją, zobacz [przy użyciu programu Azure PowerShell z usługą Resource Manager](powershell-azure-resource-manager.md).
* Aby uzyskać informacje dotyczące polecenia wiersza polecenia platformy Azure do zarządzania subskrypcją, zobacz [przy użyciu wiersza polecenia platformy Azure przy użyciu usługi Resource Manager](xplat-cli-azure-resource-manager.md).
* Aby dowiedzieć się więcej o funkcjach portalu do zarządzania subskrypcją, zobacz [przy użyciu witryny Azure portal do zarządzania zasobami](resource-group-portal.md).
* Aby dowiedzieć się, stosując organizację logiczną do zasobów, zobacz [organizowanie zasobów za pomocą tagów](resource-group-using-tags.md).
