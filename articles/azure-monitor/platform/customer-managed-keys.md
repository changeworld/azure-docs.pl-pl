---
title: Azure Monitor konfigurację klucza zarządzanego przez klienta
description: Informacje i kroki służące do konfigurowania klucza zarządzanego przez klienta (CMK) w celu szyfrowania danych w obszarach roboczych Log Analytics przy użyciu klucza Azure Key Vault.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 01/11/2020
ms.openlocfilehash: 04bda5b016234f96d4bef7796799f2526296dd26
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75932757"
---
# <a name="azure-monitor-customer-managed-key-configuration"></a>Azure Monitor konfigurację klucza zarządzanego przez klienta 

Ten artykuł zawiera ogólne informacje i instrukcje dotyczące konfigurowania kluczy zarządzanych przez klienta (CMK) dla obszarów roboczych Log Analytics i składników Application Insights. Po skonfigurowaniu wszystkie dane wysyłane do obszarów roboczych lub składników są szyfrowane za pomocą klucza Azure Key Vault.

Zalecamy przejrzenie [ograniczeń i ograniczeń](#Limitations and constraints) poniżej przed rozpoczęciem konfiguracji.

## <a name="disclaimers"></a>Zastrzeżenia

- Azure Monitor CMK to funkcja wczesnego dostępu i włączona dla zarejestrowanej subskrypcji.

- Wdrożenie CMK opisane w tym artykule jest dostarczane w jakości produkcyjnej i obsługiwane w taki sposób, że jest to funkcja wczesnego dostępu.

- Funkcja CMK jest dostarczana w dedykowanym klastrze magazynów danych, który jest klastrem usługi Azure Eksplorator danych (ADX) i jest przeznaczony dla klientów wysyłających 1 TB dziennie. 

- Model cen CMK jest obecnie niedostępny i nie został uwzględniony w tym artykule. Model cenowy dedykowanego klastra ADX jest oczekiwany w drugim kwartale roku kalendarzowego (CY) 2020 i będzie miał zastosowanie do wszystkich istniejących wdrożeń CMK.

- W tym artykule opisano konfigurację CMK dla obszarów roboczych Log Analytics. Program CMK for Application Insights Components jest również obsługiwany przy użyciu tego artykułu, podczas gdy różnice są wymienione w dodatku.

> [!NOTE]
> Log Analytics i Application Insights używają tej samej platformy do przechowywania danych i aparatu zapytań.
> Te dwa magazyny są przełączane przez integrację Application Insights w Log Analytics, aby utworzyć pojedynczy magazyn ujednoliconych dzienników w Azure Monitor. Ta zmiana jest planowana w drugim kwartale roku kalendarzowego 2020. Jeśli nie musisz wdrażać CMK dla danych usługi Application Insights, zalecamy oczekiwanie na zakończenie konsolidacji, ponieważ takie wdrożenia zostaną zakłócone przez konsolidację i konieczne będzie ponowne skonfigurowanie CMK po migracji do dziennika Obszar roboczy analizy. Minimalny 1 TB dziennie dotyczy poziomu klastra i do momentu ukończenia konsolidacji w drugim kwartale Application Insights i Log Analytics wymagać oddzielnych klastrów.

## <a name="customer-managed-key-cmk-overview"></a>Klucz zarządzany przez klienta (CMK) — Omówienie

[Szyfrowanie w spoczynku](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) to wspólne wymagania w zakresie ochrony prywatności i bezpieczeństwa w organizacjach. Możesz pozwolić, aby platforma Azure całkowicie zarządzała szyfrowaniem w stanie spoczynku, podczas gdy masz różne opcje, aby dokładnie zarządzać szyfrowaniem lub kluczami szyfrowania.

Magazyn danych Azure Monitor zapewnia, że wszystkie dane zaszyfrowane przy użyciu kluczy zarządzanych przez platformę Azure są przechowywane w usłudze Azure Storage. Azure Monitor udostępnia również opcję szyfrowania danych przy użyciu własnego klucza przechowywanego w [magazynach kluczy platformy Azure](https://docs.microsoft.com/azure/key-vault/key-vault-overview), do którego można uzyskać dostęp przy użyciu uwierzytelniania [tożsamości zarządzanej](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) przypisanego przez system. Ten klucz może być [chroniony przez oprogramowanie lub sprzęt-moduł HSM](https://docs.microsoft.com/azure/key-vault/key-vault-overview).
Azure Monitor korzystania z szyfrowania jest taka sama jak w sposobie działania [szyfrowania usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption) .

Częstotliwość, z jaką Azure Monitor dostęp do magazynu Key Vault dla operacji zawijania i rozwinięcia, wynosi od 6 do 60 sekund. Magazyn Azure Monitor  
zawsze respektują zmiany w uprawnieniach klucza w ciągu godziny.

Dane odebrane w ciągu ostatnich 14 dni również są przechowywane w pamięci podręcznej (dysk SSD) w celu wydajnej operacji aparatu zapytań. Te dane pozostają zaszyfrowane przy użyciu kluczy firmy Microsoft bez względu na konfigurację CMK, ale pracujemy nad zaszyfrowaniem SSD z CMK wczesne 2020.

## <a name="how-cmk-works-in-azure-monitor"></a>Jak działa CMK w Azure Monitor

Azure Monitor korzysta z zarządzanej tożsamości przypisanej do systemu, aby udzielić dostępu do Azure Key Vault. Tożsamość zarządzana przypisana przez system może być skojarzona tylko z pojedynczym zasobem platformy Azure. Tożsamość Azure Monitorgo magazynu danych (klastra ADX) jest obsługiwana na poziomie klastra, co oznacza, że funkcja CMK jest dostarczana w dedykowanym klastrze ADX. Aby obsługiwać CMK w wielu obszarach roboczych, nowy zasób Log Analytics (*klaster*) pełni rolę pośredniego połączenia tożsamości między Key Vault i obszarami roboczymi log Analytics. Pojęcie to jest zgodne z ograniczeniem tożsamości przypisanym do systemu, a tożsamość jest utrzymywana między klastrem ADX i zasobem *klastra* log Analytics *,* podczas gdy dane wszystkich skojarzonych obszarów roboczych są chronione za pomocą klucza Key Vault. Magazyn klastra underlay ADX używa zarządzanej tożsamości, która\'s skojarzona z zasobem *klastra* do uwierzytelniania i uzyskiwania dostępu do Azure Key Vault za pośrednictwem Azure Active Directory.

![CMK — Omówienie](media/customer-managed-keys/cmk-overview.png)
1.  Key Vault klienta.
2.  Zasób klastra Log Analytics klienta mającego zarządzaną tożsamość z uprawnieniami do Key Vault — tożsamość jest obsługiwana na poziomie magazynu danych (ADX klaster).
3.  Azure Monitor dedykowany klaster ADX.
4.  Obszary robocze klientów skojarzone z zasobem klastra na potrzeby szyfrowania CMK.

## <a name="encryption-keys-management"></a>Zarządzanie kluczami szyfrowania

Istnieją trzy typy kluczy, które wiążą się z szyfrowaniem danych magazynu:

- Klucz szyfrowania klucza **KEK** w Key Vault (CMK)
- Klucz szyfrowania konta **AEK**
- Klucz szyfrowania danych

Mają zastosowanie następujące zasady:

- Konto magazynu ADX generuje unikatowy klucz szyfrowania dla każdego konta magazynu, który jest znany jako AEK.

- AEK jest używany do wyprowadzania DEKs, które są kluczami, które są używane do szyfrowania poszczególnych bloków danych zapisywana na dysku.

- Podczas konfigurowania klucza w Key Vault i odwoływania się do niego w ramach zasobu *klastra* usługa Azure Storage zawija AEK z KEK w Azure Key Vault.

- KEK nigdy nie opuści Key Vault i w przypadku klucza HSM nigdy nie opuszcza sprzętu.

- Usługa Azure Storage korzysta z zarządzanej tożsamości skojarzonej z zasobem *klastra* w celu uwierzytelniania i dostępu do Azure Key Vault za pośrednictwem Azure Active Directory.

- W przypadku operacji odczytu/zapisu usługa Azure Storage wysyła żądania do Azure Key Vault, aby otoczyć i odszyfrować AEK w celu wykonania operacji szyfrowania i odszyfrowywania.

## <a name="cmk-provisioning-procedure"></a>Procedura inicjowania obsługi CMK

Proces aprowizacji obejmuje następujące kroki:

1. Listy dozwolonych subskrypcji — jest to wymagane w przypadku tej funkcji wczesnego dostępu
2. Tworzenie Azure Key Vault i przechowywanie klucza
3. Tworzenie zasobu *klastra*
4. Udziel uprawnień do Key Vault
5. Inicjowanie obsługi Azure Monitor magazynu danych (klastra ADX)
6. Skojarzenie obszarów roboczych Log Analytics

Procedura nie jest obecnie obsługiwana w interfejsie użytkownika, a proces aprowizacji jest wykonywany za pośrednictwem interfejsu API REST.

> [!IMPORTANT]
> Wszystkie żądania interfejsu API muszą zawierać Token autoryzacji okaziciela w nagłówku żądania.

Przykład:

```rst
GET
https://management.azure.com/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>?api-version=2015-11-01-preview
Authorization: Bearer eyJ0eXAiO....
```

Gdzie *eyJ0eXAiO....* reprezentuje token pełnej autoryzacji. 

Token można uzyskać, korzystając z jednej z następujących metod:

1. Użyj metody [rejestracje aplikacji](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens) .

2. W witrynie Azure Portal
    1. Przejdź do Azure Portal w narzędziu deweloperskim (F12)
    1. Wyszukaj ciąg autoryzacji w obszarze "nagłówki żądania" w jednym z wystąpień "Batch? API-Version". Wygląda na to: "Authorization: Bearer \<token\>". 
    1. Skopiuj i dodaj go do wywołania interfejsu API zgodnie z poniższymi przykładami.

3. Przejdź do witryny dokumentacji REST platformy Azure. Naciśnij pozycję "Wypróbuj" w dowolnym interfejsie API i skopiuj token okaziciela.

### <a name="subscription-whitelisting"></a>Listy dozwolonych subskrypcji

CMK to funkcja wczesnego dostępu. Subskrypcje, w których planujesz utworzyć zasoby *klastra* , muszą być listy dozwolonych wcześniej przez grupę produktów platformy Azure. Użyj swoich kontaktów do firmy Microsoft, aby podać identyfikatory subskrypcji.

> [!WARNING]
> Funkcja CMK jest regionalna. Twoje Azure Key Vault, konto magazynu, zasób *klastra* i skojarzone log Analytics obszary robocze muszą znajdować się w tym samym regionie, ale mogą znajdować się w różnych subskrypcjach.

### <a name="storing-encryption-key-kek"></a>Przechowywanie klucza szyfrowania (KEK)

Utwórz zasób Azure Key Vault, a następnie Wygeneruj lub zaimportuj klucz, który ma być używany do szyfrowania danych.

Azure Key Vault należy skonfigurować jako możliwy do odzyskania, aby chronić klucz i dostęp do danych Azure Monitor.

Te ustawienia są dostępne za pośrednictwem interfejsu wiersza polecenia i PowerSell:
- [Usuwanie nietrwałe](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) musi być włączone
- [Ochrona przed przeczyszczeniem](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) powinna być włączona, aby zabezpieczyć przed wymuszeniem usunięcia wpisu tajnego lub magazynu nawet po usunięciu nietrwałego

### <a name="create-cluster-resource"></a>Utwórz zasób *klastra*

Ten zasób jest używany jako połączenie tożsamości pośredniej między Key Vault i obszarami roboczymi. Po otrzymaniu potwierdzenia, że subskrypcje zostały listy dozwolonych, utwórz zasób *klastra* log Analytics w regionie, w którym znajdują się obszary robocze. Application Insights i Log Analytics wymagają oddzielnych zasobów klastra. Typ zasobu klastra jest definiowany w czasie tworzenia przez ustawienie właściwości "clustertype" na wartość "LogAnalytics" lub "ApplicationInsights". Nie można zmienić typu zasobu klastra.

**Tworzenie**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "location": "<region-name>",
   "properties": {
      "clusterType": "LogAnalytics"   //Should be "ApplicationInsights" for Application Insights CMK
    },
   "identity": {
      "type": "systemAssigned"
   }
}
```

**Odpowiedź**

Tożsamość jest przypisana do zasobu *klastra* podczas jego tworzenia.

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"    //A GUID that was generated by the managed identity service
  },
  "properties": {
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"    //A GUID that Log Analytics generates for the cluster
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",    //The cluster resource Id
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}

```
> [!IMPORTANT]
> Skopiuj i Zachowaj wartość "Cluster-ID", ponieważ będzie ona potrzebna w następnych krokach.

Jeśli chcesz usunąć zasób *klastra* z dowolnego powodu (na przykład utworzyć go z inną nazwą), użyj tego wywołania interfejsu API:

```rst
DELETE
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
```

### <a name="grant-key-vault-permissions"></a>Przyznawanie uprawnień Key Vault

Zaktualizuj Key Vault i Dodaj zasady dostępu dla zasobu klastra. Uprawnienia do Key Vault są następnie propagowane do Azure Monitor magazynu, który będzie używany do szyfrowania danych.
Otwórz Key Vault w Azure Portal, a następnie kliknij pozycję "zasady dostępu" i "+ Dodaj zasady dostępu", aby utworzyć nowe zasady z następującymi ustawieniami:

- Uprawnienia klucza: Wybierz uprawnienia "Pobierz", "Zawijanie klucza" i "Cofnij Zawijanie klucza".

- Wybierz podmiot zabezpieczeń: Wprowadź identyfikator klastra, który jest wartością "clusterId" w odpowiedzi poprzedniego kroku.

![Przyznawanie uprawnień Key Vault](media/customer-managed-keys/grant-key-vault-permissions.png)

Uprawnienie *Get* jest wymagane do sprawdzenia, czy Key Vault jest skonfigurowany jako możliwy do odzyskania w celu ochrony klucza i dostępu do danych Azure monitor.

Potrwa kilka minut, dopóki zasób *klastra* nie zostanie rozpropagowany w Azure Resource Manager. Podczas konfigurowania tych zasad dostępu natychmiast po utworzeniu zasobu *klastra* może wystąpić błąd przejściowy. W takim przypadku spróbuj ponownie za kilka minut.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Aktualizowanie zasobu klastra przy użyciu szczegółów identyfikatora klucza

Ta procedura ma zastosowanie również w przypadku tworzenia nowej wersji klucza.

Zaktualizuj zasób klastra za pomocą Azure Key Vault szczegóły identyfikatora klucza, aby umożliwić usłudze Azure Monitor Storage używanie nowej wersji klucza. Wybierz bieżącą wersję klucza w Azure Key Vault, aby uzyskać szczegółowe informacje o identyfikatorze klucza:

![Przyznawanie uprawnień Key Vault](media/customer-managed-keys/key-identifier-8bit.png)

Zaktualizuj KeyVaultProperties zasobów *klastra* przy użyciu szczegółów identyfikatora klucza.

**Aktualizacja**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
   "properties": {
       "KeyVaultProperties": {     //Key Vault key identifier details taken from Key identifier URI
            KeyVaultUri: "https://<key-vault-name>.vault.azure.net",
            KeyName: "<key-name>",
            KeyVersion: "<current-version>"
            },
   },
   "location":"<region-name>",
   "identity": { 
        "type": "systemAssigned" 
        }
}
```

**Odpowiedź**

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"    //A GUID that was generated by the managed identity service
  },
  "properties": {
       "KeyVaultProperties": {     // Key Vault key identifier
            KeyVaultUri: "https://key-vault-name.vault.azure.net",
            KeyName: "key-name",
            KeyVersion: "current-version"
            },
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",    //The cluster resource Id
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"    //Example: Switzerland North
}
```

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>Inicjowanie obsługi Azure Monitor magazynu danych (klastra ADX)

W okresie wczesnego dostępu do funkcji klaster ADX jest inicjowany ręcznie przez zespół produktu po zakończeniu poprzednich kroków. Skorzystaj z kanału firmy Microsoft, aby podać następujące informacje:

1. Potwierdzenie wykonania powyższych czynności

2. Odpowiedź interfejsu API zasobu klastra. można go pobrać w dowolnym momencie za pomocą wywołania Get interfejsu API.

**Odczytywanie identyfikatora zasobu *klastra***

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
```

**Odpowiedź**
```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principal-Id"
  },
  "properties": {
       "KeyVaultProperties": {    // Key Vault key identifier
            KeyVaultUri: "https://key-vault-name.vault.azure.net",
            KeyName: "key-name",
            KeyVersion: "current-version"
            },
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="workspace-association-to-cluster-resource"></a>Skojarzenie obszaru roboczego z zasobem *klastra*

> [!NOTE]
> Ten krok należy wykonać **dopiero** po otrzymaniu potwierdzenia od grupy produktów za pośrednictwem kanału firmy Microsoft, że **zainicjowano Inicjowanie obsługi administracyjnej magazynu danych Azure monitor (ADX)** . W przypadku kojarzenia obszarów roboczych i pozyskiwania danych przed rozpoczęciem **aprowizacji**dane zostaną usunięte i nie będzie można ich odzyskać.

**Kojarzenie obszaru roboczego z zasobem *klastra* przy użyciu [obszarów roboczych — Tworzenie lub aktualizowanie](https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate) interfejsu API**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>?api-version=2015-11-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "source": "Azure",
    "customerId": "<workspace-id>",    //Available in Azure portal under Log Analytics workspace Overview section
    "features": {
      "clusterDefinitionId": "<cluster-id>"    //It's the "clusterId" value provided in the respond from the previous step 
    }
  },
  "id": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>",
  "name": "<workspace-name>",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "<region-name>"
}
```

**Odpowiedź**

```json
{
  "properties": {
    "source": "Azure",
    "customerId": "workspace-id",
    "retentionInDays": value,
    "features": {
      "legacy": value,
      "searchVersion": value,
      "clusterDefinitionId": "cluster-id" //The id of the Cluster resource
    },
    "workspaceCapping": {
      "dailyQuotaGb": value,
      "quotaNextResetTime": "timeStamp",
      "dataIngestionStatus": "RespectQuota"
    }
  },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region-name"
}
```

Po skojarzeniu dane wysyłane do obszarów roboczych są przechowywane w postaci zaszyfrowanej przy użyciu klucza zarządzanego.

## <a name="cmk-kek-revocation"></a>CMK (KEK) — odwołanie

Azure Monitor Storage zawsze będzie respektują zmiany w uprawnieniach klucza w ciągu godziny, zwykle wcześniej, a magazyn stanie się niedostępny — wszystkie dane pozyskane do obszarów roboczych skojarzonych z zasobem *klastra* zostaną usunięte, a zapytania zakończą się niepowodzeniem. Wcześniej pozyskiwane dane pozostają niedostępne w magazynie Azure Monitor, dopóki klucz zostanie odwołany, a obszary robocze nie zostaną usunięte. Dane niedostępne podlegają zasadom przechowywania danych i zostaną usunięte po osiągnięciu okresu przechowywania.

Magazyn będzie okresowo sondował Key Vault, aby próbować odszyfrować klucz szyfrowania, a następnie uzyskać dostęp do pozyskiwania danych i wznowienia zapytania w ciągu 30 minut.

## <a name="cmk-kek-rotation"></a>CMK (KEK)

Obrót CMK wymaga jawnej aktualizacji zasobu klastra przy użyciu nowej wersji klucza Azure Key Vault. Aby zaktualizować Azure Monitor przy użyciu nowej wersji klucza, postępuj zgodnie z instrukcjami w kroku "Aktualizowanie zasobu *klastra* z identyfikatorem klucza szczegóły".

-   Jeśli klucz zostanie obrócony w Key Vault i nie będzie można zaktualizować nowej wersji w Azure Monitor krótko po tym, klucz będzie niedostępny przez Azure Monitor magazyn.

## <a name="limitations-and-constraints"></a>Ograniczenia i ograniczenia

- Funkcja CMK jest obsługiwana na poziomie klastra ADX i wymaga dedykowanego klastra Azure Monitor ADX

- Maksymalna liczba zasobów *klastra* na subskrypcję jest ograniczona do 5

- Skojarzenie zasobów *klastra* z obszarem roboczym należy wykonać dopiero po otrzymaniu potwierdzenia od grupy produktów, że inicjowanie obsługi klastra ADX zostało spełnione. Dane wysyłane przed przystąpieniem do tej aprowizacji zostaną usunięte i nie będzie można ich odzyskać.

- Szyfrowanie CMK ma zastosowanie do nowo wprowadzonych danych po konfiguracji CMK. Dane, które zostały pozyskiwane przed konfiguracją CMK, pozostawały zaszyfrowane za pomocą klucza firmy Microsoft. Dane można badać przed bezproblemową konfiguracją i po niej.

- CMK funkcja jest regionalna — Azure Key Vault, zasób *klastra* i skojarzone obszary robocze muszą znajdować się w tym samym regionie, ale mogą znajdować się w różnych subskrypcjach.

- Po skojarzeniu obszaru roboczego z zasobem *klastra* nie można go usunąć z zasobu *klastra* , ponieważ dane są szyfrowane za pomocą klucza i nie są dostępne bez KEK w Azure Key Vault.

- Azure Key Vault musi być skonfigurowany jako możliwy do odzyskania. Te właściwości nie są domyślnie włączone:

  - [Usuwanie nietrwałe](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) jest włączone
  - Funkcja "nie czyść" jest włączona, aby zapewnić ochronę przed usunięciem wpisu tajnego/magazynu nawet po usunięciu nietrwałego

- Application Insights i Log Analytics wymagają oddzielnych zasobów *klastra* . Typ zasobu *klastra* jest definiowany w czasie tworzenia przez ustawienie właściwości "clustertype" na wartość "LogAnalytics" lub "ApplicationInsights". Nie można zmienić typu zasobu *klastra* .

- Zasób *klastra* przeniesiony do innej grupy zasobów lub subskrypcji nie jest obecnie obsługiwany.

- Skojarzenie obszaru roboczego z zasobem *klastra* zakończy się niepowodzeniem, jeśli zasób *klastra* znajduje się w innej dzierżawie.

-   Skojarzenie obszaru roboczego z zasobem *klastra* zakończy się niepowodzeniem, jeśli jest ono skojarzone z innym zasobem *klastra*

## <a name="troubleshooting-and-management"></a>Rozwiązywanie problemów i zarządzanie

- Dostępność Key Vault
    - W przypadku normalnego działania pamięć podręczna magazynu AEK przez krótkie okresy czasu są okresowo przywracane do Key Vault w celu odpakowania.
    
    - Błędy połączeń przejściowych. Magazyn obsługuje błędy przejściowe (przekroczenia limitu czasu, błędy połączeń, problemy z usługą DNS) przez umożliwienie pozostawania kluczy w pamięci podręcznej przez krótki czas, a ten przeniesie wszystkie małe Blips w ramach funkcji zapytania dostępności.
            -Pozyskiwanie jest kontynuowane bez przerw w działaniu
    
    - Niedostępność aktywnej witryny o około 30 minutach spowoduje, że konto magazynu będzie niedostępne.
            Funkcja **zapytania** -   jest niedostępna **— pozyskiwanie — dane** są buforowane przez kilka godzin przy użyciu klawisza Microsoft, aby uniknąć utraty danych. Po przywróceniu dostępu do Key Vault jest on dostępny, a tymczasowe dane przechowywane w pamięci podręcznej są przechowywane w magazynie danych i szyfrowane za pomocą CMK.

- Jeśli utworzysz zasób *klastra* i natychmiast określisz KeyVaultProperties, operacja może zakończyć się niepowodzeniem, ponieważ nie można zdefiniować zasad dostępu do momentu przypisania tożsamości systemu do zasobu *klastra* .

- W przypadku zaktualizowania istniejącego zasobu *klastra* przy użyciu KeyVaultProperties i braku zasad dostępu do klucza "Get" w Key Vault operacja zakończy się niepowodzeniem.

- Próba usunięcia zasobu *klastra* skojarzonego z obszarem roboczym spowoduje niepowodzenie operacji usuwania.

- Pobierz wszystkie zasoby *klastra* dla grupy zasobów:

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```
    
**Odpowiedź**

```json
{
  "value": [
    {
      "identity": {
        "type": "SystemAssigned",
        "tenantId": "tenant-id",
        "principalId": "principal-Id"
      },
      "properties": {
         "KeyVaultProperties": {    // Key Vault key identifier
            KeyVaultUri: "https://{key-vault-name}.vault.azure.net",
            KeyName: "key-name",
            KeyVersion: "current-version"
            },
        "provisioningState": "Succeeded",
        "clusterType": "LogAnalytics", 
        "clusterId": "cluster-id"
      },
      "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
      "name": "cluster-name",
      "type": "Microsoft.OperationalInsights/clusters",
      "location": "region-name"
    }
  ]
}
```

- Pobierz wszystkie zasoby *klastra* dla subskrypcji

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```
    
**Odpowiedź**
    
Taka sama odpowiedź jak dla "zasobów*klastra* dla grupy zasobów", ale w zakresie subskrypcji.
    
- Usuwanie zasobu *klastra* — należy usunąć wszystkie skojarzone obszary robocze, aby można było usunąć zasób *klastra* :

  ```rst
  DELETE
  https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```

**Odpowiedź**

200 OK


## <a name="appendix"></a>Dodatek

Ten artykuł ma zastosowanie do Application Insights klucza zarządzanego przez klienta (CMK), chociaż należy wziąć pod uwagę nadchodzącą zmianę, która ułatwi Planowanie wdrożenia CMK dla składników usługi Application Insights.

Log Analytics i Application Insights korzystają z tej samej platformy magazynu danych i aparatu zapytań — te dwa magazyny są połączone za pośrednictwem integracji Application Insights z usługą Log Analytics w celu zapewnienia pojedynczego, ujednoliconego magazynu dzienników na Azure Monitor przez drugi kwartał
2020. Ta zmiana spowoduje przełączenie danych usługi Application Insights do obszarów roboczych Log Analytics i przeprowadzenie zapytań, szczegółowych informacji i innych ulepszeń w czasie, gdy konfiguracja CMK w obszarze roboczym zostanie również zastosowana do Application Insights danych.

> [!NOTE]
> Jeśli nie musisz wdrażać CMK dla danych usługi Application Insights, zalecamy oczekiwanie na zakończenie konsolidacji, ponieważ takie wdrożenia zostaną zakłócone przez konsolidację i konieczne będzie ponowne skonfigurowanie CMK po migracji do dziennika Obszar roboczy analizy. Minimalny 1 TB dziennie dotyczy poziomu klastra i do momentu ukończenia konsolidacji w drugim kwartale Application Insights i Log Analytics wymagać oddzielnych klastrów.

## <a name="application-insights-cmk-configuration"></a>Application Insights konfiguracja CMK

Konfiguracja Application Insights CMK jest identyczna z procesem przedstawionym w tym artykule, w tym ograniczenia i rozwiązywanie problemów, z wyjątkiem następujących kroków:

- Tworzenie zasobu *klastra*

- Kojarzenie składnika z zasobem *klastra*

Podczas konfigurowania CMK dla Application Insights należy wykonać te czynności zamiast wymienionych powyżej.

### <a name="create-a-cluster-resource"></a>Tworzenie zasobu *klastra*

Ten zasób jest używany jako połączenie tożsamości pośredniej między Key Vault i składnikami. Po otrzymaniu potwierdzenia, że subskrypcje zostały listy dozwolonyche, utwórz zasób klastra Log Analytics w regionie, w którym znajdują się składniki. Typ zasobu klastra jest definiowany w czasie tworzenia przez ustawienie właściwości *clustertype* na wartość *LogAnalytics*lub *ApplicationInsights*. Powinien być *ApplicationInsights* dla Application Insights CMK. Nie można zmienić ustawienia *klastratype* po konfiguracji.

**Tworzenie**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "location": "<region-name>",
  "properties": {
      "clusterType":"ApplicationInsights"
  },
  "identity": {
      "type": "systemAssigned"
  }
}
```

**Odpowiedź**

Tożsamość jest przypisana do zasobu *klastra* podczas jego tworzenia.

```json

{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"    //A GUID that was generated by the managed identity service
  },
  "properties": {
    "provisioningState": "Succeeded",
    "clusterType": "ApplicationInsights",    //The value is ‘ApplicationInsights’ for Application Insights CMK
    "clusterId": "cluster-id"   //A GUID that Log Analytics generates for the cluster - copy it since you need it for Key Vault and components association 
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name", //The cluster resource Id
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="associate-a-component-to-a-cluster-resource"></a>Kojarzenie składnika z zasobem *klastra*

```rst
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Insights/components/{component-name}?api-version=2015-05-01
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "clusterDefinitionId": "cluster-id" //It's the "clusterId" value provided in the respond from the previous step
  },
  "location": "region-name",
  "kind": "component-type",
}
```

**Odpowiedź**

```json
{
  "id": "/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.insights/components/{component-name}",
  "name": "component-name",
  "type": "Microsoft.Insights/components",
  "location": "region-name",
  "tags": "",
  "kind": "",
  "properties": {
    "clusterDefinitionId": "cluster-id" //The Cluster resource ID that is associated to this component
    "ApplicationId": "",
    "AppId": "",
    "Application_Type": "",
    "Flow_Type": "",
    "Request_Source": "",
    "InstrumentationKey": "",
    "CreationDate": "",
    "TenantId": "",
    "HockeyAppId": "",
    "HockeyAppToken": "",
    "provisioningState": "",
    "SamplingPercentage":,
    "RetentionInDays":,
    "ConnectionString": "",
    "DisableIpMasking":,
    "ImmediatePurgeDataOn30Days": 
  }
}
```

Po skojarzeniu dane wysyłane do składników są przechowywane w postaci zaszyfrowanej przy użyciu klucza zarządzanego.
