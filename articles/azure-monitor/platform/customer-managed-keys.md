---
title: Azure Monitor konfigurację klucza zarządzanego przez klienta
description: Informacje i kroki służące do konfigurowania klucza zarządzanego przez klienta (CMK) w celu szyfrowania danych w obszarach roboczych Log Analytics przy użyciu klucza Azure Key Vault.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 02/24/2020
ms.openlocfilehash: 2ea77be0a7aabefaf8f6ed9a5bd841ea1fdda263
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77620310"
---
# <a name="azure-monitor-customer-managed-key-configuration"></a>Azure Monitor konfigurację klucza zarządzanego przez klienta 

Ten artykuł zawiera ogólne informacje i instrukcje dotyczące konfigurowania kluczy zarządzanych przez klienta (CMK) dla obszarów roboczych Log Analytics i składników Application Insights. Po skonfigurowaniu wszystkie dane wysyłane do obszarów roboczych lub składników są szyfrowane za pomocą klucza Azure Key Vault.

Zalecamy przejrzenie [ograniczeń i ograniczeń](#limitations-and-constraints) poniżej przed rozpoczęciem konfiguracji.

## <a name="disclaimers"></a>Zastrzeżenia

- Azure Monitor CMK to funkcja wczesnego dostępu i włączona dla zarejestrowanej subskrypcji.

- Wdrożenie CMK opisane w tym artykule jest dostarczane w jakości produkcyjnej i obsługiwane w taki sposób, że jest to funkcja wczesnego dostępu.

- Funkcja CMK jest dostarczana w dedykowanym klastrze magazynów danych, który jest klastrem usługi Azure Eksplorator danych (ADX) i jest przeznaczony dla klientów wysyłających 1 TB dziennie. 

- Model cen CMK jest obecnie niedostępny i nie został uwzględniony w tym artykule. Model cenowy dedykowanego klastra ADX jest oczekiwany w drugim kwartale roku kalendarzowego (CY) 2020 i będzie miał zastosowanie do wszystkich istniejących wdrożeń CMK.

- W tym artykule opisano konfigurację CMK dla obszarów roboczych Log Analytics. Program CMK for Application Insights Components jest również obsługiwany przy użyciu tego artykułu, podczas gdy różnice są wymienione w dodatku.

> [!NOTE]
> Log Analytics i Application Insights używają tej samej platformy do przechowywania danych i aparatu zapytań.
> Te dwa magazyny są przełączane przez integrację Application Insights w Log Analytics, aby utworzyć pojedynczy magazyn ujednoliconych dzienników w Azure Monitor. Ta zmiana jest planowana w drugim kwartale roku kalendarzowego 2020. Jeśli nie musisz wdrażać CMK dla danych Application Insights, zalecamy oczekiwanie na zakończenie konsolidacji, ponieważ takie wdrożenia zostaną zakłócone przez konsolidację i konieczne będzie ponowne skonfigurowanie CMK po migracji do dziennika Obszar roboczy analizy. Wartość 1 TB na dzień jest stosowana na poziomie klastra i do momentu ukończenia konsolidacji w drugim kwartale Application Insights i Log Analytics wymagają oddzielnych klastrów.

## <a name="customer-managed-key-cmk-overview"></a>Klucz zarządzany przez klienta (CMK) — Omówienie

[Szyfrowanie w spoczynku](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) to wspólne wymagania w zakresie ochrony prywatności i bezpieczeństwa w organizacjach. Możesz pozwolić, aby platforma Azure całkowicie zarządzała szyfrowaniem w stanie spoczynku, podczas gdy masz różne opcje, aby dokładnie zarządzać szyfrowaniem lub kluczami szyfrowania.

Magazyn danych Azure Monitor zapewnia, że wszystkie dane zaszyfrowane przy użyciu kluczy zarządzanych przez platformę Azure są przechowywane w usłudze Azure Storage. Azure Monitor udostępnia również opcję szyfrowania danych przy użyciu własnego klucza przechowywanego w [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview), do którego uzyskuje się dostęp przy użyciu uwierzytelniania [tożsamości zarządzanej](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) przypisanego przez system. Ten klucz może być [chroniony przez oprogramowanie lub sprzęt-moduł HSM](https://docs.microsoft.com/azure/key-vault/key-vault-overview).
Azure Monitor korzystania z szyfrowania jest taka sama jak w sposobie działania [szyfrowania usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption) .

Częstotliwość, z jaką Azure Monitor dostęp do magazynu Key Vault dla operacji zawijania i rozwinięcia, wynosi od 6 do 60 sekund. Azure Monitor Storage zawsze uwzględnia zmiany w uprawnieniach klucza w ciągu godziny.

Dane odebrane w ciągu ostatnich 14 dni również są przechowywane w pamięci podręcznej (dysk SSD) w celu wydajnej operacji aparatu zapytań. Te dane pozostają zaszyfrowane przy użyciu kluczy firmy Microsoft bez względu na konfigurację CMK, ale pracujemy nad zaszyfrowaniem SSD z CMK wczesne 2020.

## <a name="how-cmk-works-in-azure-monitor"></a>Jak działa CMK w Azure Monitor

Azure Monitor korzysta z zarządzanej tożsamości przypisanej do systemu, aby udzielić dostępu do Azure Key Vault. Tożsamość zarządzana przypisana przez system może być skojarzona tylko z pojedynczym zasobem platformy Azure. Tożsamość Azure Monitorgo magazynu danych (klastra ADX) jest obsługiwana na poziomie klastra, co oznacza, że funkcja CMK jest dostarczana w dedykowanym klastrze ADX. Aby obsługiwać CMK w wielu obszarach roboczych, nowy zasób Log Analytics (*klaster*) pełni rolę pośredniego połączenia tożsamości między Key Vault i obszarami roboczymi log Analytics. Pojęcie to jest zgodne z ograniczeniem tożsamości przypisanym do systemu, a tożsamość jest utrzymywana między klastrem ADX i zasobem *klastra* log Analytics, podczas gdy dane wszystkich skojarzonych obszarów roboczych są chronione za pomocą klucza Key Vault. Magazyn klastra underlay ADX używa zarządzanej tożsamości, która\'s skojarzona z zasobem *klastra* do uwierzytelniania i uzyskiwania dostępu do Azure Key Vault za pośrednictwem Azure Active Directory.

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

Mają zastosowanie następujące reguły:

- Konto magazynu ADX generuje unikatowy klucz szyfrowania dla każdego konta magazynu, który jest znany jako AEK.

- AEK jest używany do wyprowadzania DEKs, które są kluczami, które są używane do szyfrowania poszczególnych bloków danych zapisywana na dysku.

- Podczas konfigurowania klucza w Key Vault i odwoływania się do niego w ramach zasobu *klastra* usługa Azure Storage zawija AEK z KEK w Azure Key Vault.

- KEK nigdy nie opuści Key Vault i w przypadku klucza HSM nigdy nie opuszcza sprzętu.

- Usługa Azure Storage korzysta z zarządzanej tożsamości skojarzonej z zasobem *klastra* w celu uwierzytelniania i dostępu do Azure Key Vault za pośrednictwem Azure Active Directory.

- W przypadku operacji odczytu/zapisu usługa Azure Storage wysyła żądania do Azure Key Vault, aby otoczyć i odszyfrować AEK w celu wykonania operacji szyfrowania i odszyfrowywania.

## <a name="cmk-provisioning-procedure"></a>Procedura inicjowania obsługi CMK

Aby uzyskać Application Insights konfigurację CMK, postępuj zgodnie z zawartością dodatku dla kroków 3 i 6.

1. Listy dozwolonych subskrypcji — jest to wymagane w przypadku tej funkcji wczesnego dostępu
2. Tworzenie Azure Key Vault i przechowywanie klucza
3. Tworzenie zasobu *klastra*
4. Inicjowanie obsługi Azure Monitor magazynu danych (klastra ADX)
5. Udziel uprawnień do Key Vault
6. Skojarzenie obszarów roboczych Log Analytics

Procedura nie jest obecnie obsługiwana w interfejsie użytkownika, a proces aprowizacji jest wykonywany za pośrednictwem interfejsu API REST.

> [!IMPORTANT]
> Wszystkie żądania interfejsu API muszą zawierać Token autoryzacji okaziciela w nagłówku żądania.

Na przykład:

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

> [!IMPORTANT]
> Funkcja CMK jest regionalna. Twoje Azure Key Vault, konto magazynu, zasób *klastra* i skojarzone log Analytics obszary robocze muszą znajdować się w tym samym regionie, ale mogą znajdować się w różnych subskrypcjach.

### <a name="storing-encryption-key-kek"></a>Przechowywanie klucza szyfrowania (KEK)

Utwórz zasób Azure Key Vault, a następnie Wygeneruj lub zaimportuj klucz, który ma być używany do szyfrowania danych.

Azure Key Vault należy skonfigurować jako możliwy do odzyskania, aby chronić klucz i dostęp do danych Azure Monitor.

Te ustawienia są dostępne za pośrednictwem interfejsu wiersza polecenia i programu PowerShell:
- [Usuwanie nietrwałe](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) musi być włączone
- [Ochrona przed przeczyszczeniem](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) powinna być włączona, aby zabezpieczyć przed wymuszeniem usunięcia wpisu tajnego lub magazynu nawet po usunięciu nietrwałego

### <a name="create-cluster-resource"></a>Utwórz zasób *klastra*

Ten zasób jest używany jako połączenie tożsamości pośredniej między Key Vault i obszarami roboczymi. Po otrzymaniu potwierdzenia, że subskrypcje zostały listy dozwolonyche, utwórz zasób *klastra* log Analytics w regionie, w którym znajdują się obszary robocze. Application Insights i Log Analytics wymagają oddzielnych zasobów klastra. Typ zasobu *klastra* jest definiowany w czasie tworzenia przez ustawienie właściwości "clustertype" na wartość "LogAnalytics" lub "ApplicationInsights". Nie można zmienić typu zasobu klastra.

Aby uzyskać Application Insights konfigurację CMK, postępuj zgodnie z zawartością dodatku dla tego kroku.

**Tworzenie**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "location": "<region-name>",
   "properties": {
      "clusterType": "LogAnalytics"
    },
   "identity": {
      "type": "systemAssigned"
   }
}
```
Tożsamość jest przypisywana do zasobu *klastra* podczas tworzenia.
wartość "clustertype" to "ApplicationInsights" dla Application Insights CMK.

**Odpowiedź**

202 zostało zaakceptowane. Jest to standardowa Menedżer zasobówa odpowiedź dla operacji asynchronicznych.

Jeśli chcesz usunąć zasób *klastra* z dowolnego powodu — na przykład utwórz go przy użyciu innej nazwy lub typu klastra, użyj tego interfejsu API REST:

```rst
DELETE
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
```

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>Inicjowanie obsługi Azure Monitor magazynu danych (klastra ADX)

W okresie wczesnego dostępu do funkcji klaster ADX jest inicjowany ręcznie przez zespół produktu po zakończeniu poprzednich kroków. Użyj kanału firmy Microsoft, aby podać szczegóły zasobu *klastra* . Skopiuj odpowiedź JSON z interfejsu API REST zasobu *klastra* :

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

"principalId" to identyfikator GUID generowany przez zarządzaną usługę tożsamości dla zasobu *klastra* .

> [!IMPORTANT]
> Skopiuj i Zachowaj wartość "Cluster-ID", ponieważ będzie ona potrzebna w następnych krokach.


### <a name="grant-key-vault-permissions"></a>Przyznawanie uprawnień Key Vault

> [!IMPORTANT]
> Ten krok należy wykonać po otrzymaniu potwierdzenia od grupy produktów za pośrednictwem kanału firmy Microsoft, że zainicjowano Inicjowanie obsługi administracyjnej magazynu danych Azure Monitor (ADX). Aktualizacja zasad dostępu Key Vault przed tą obsługą może zakończyć się niepowodzeniem.

Zaktualizuj Key Vault przy użyciu nowych zasad dostępu, które przyznają uprawnienia do zasobu *klastra* . Te uprawnienia są używane przez podnieśenie Azure Monitor magazynu na potrzeby szyfrowania danych.
Otwórz Key Vault w Azure Portal, a następnie kliknij pozycję "zasady dostępu" i "+ Dodaj zasady dostępu", aby utworzyć nowe zasady z następującymi ustawieniami:

- Uprawnienia klucza: Wybierz uprawnienia "Pobierz", "Zawijanie klucza" i "Cofnij Zawijanie klucza".
- Wybierz podmiot zabezpieczeń: wprowadź wartość w polu Nazwa klastra, która została zwrócona w odpowiedzi w poprzednim kroku.

![Przyznawanie uprawnień Key Vault](media/customer-managed-keys/grant-key-vault-permissions.png)

Uprawnienie *Get* jest wymagane do sprawdzenia, czy Key Vault jest skonfigurowany jako możliwy do odzyskania w celu ochrony klucza i dostępu do danych Azure monitor.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Aktualizowanie zasobu klastra przy użyciu szczegółów identyfikatora klucza

Ten krok ma zastosowanie w przypadku aktualizacji z kolejnej wersji klucza w Key Vault. Zaktualizuj zasób *klastra* za pomocą Key Vault szczegóły *identyfikatora klucza* , aby umożliwić usłudze Azure monitor Storage używanie nowej wersji klucza. Wybierz bieżącą wersję klucza w Azure Key Vault, aby uzyskać szczegółowe informacje o identyfikatorze klucza.

![Przyznawanie uprawnień Key Vault](media/customer-managed-keys/key-identifier-8bit.png)

Zaktualizuj KeyVaultProperties zasobów *klastra* przy użyciu szczegółów identyfikatora klucza.

**Aktualizacja**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
   "properties": {
     "KeyVaultProperties": {
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
"KeyVaultProperties" zawiera szczegóły identyfikatora klucza Key Vault.

**Odpowiedź**

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
  },
  "properties": {
       "KeyVaultProperties": {
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

Aby uzyskać Application Insights konfigurację CMK, postępuj zgodnie z zawartością dodatku dla tego kroku.

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2019-08-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```
*ClusterDefinitionId* jest wartością *clusterId* podaną w odpowiedzi z poprzedniego kroku.

**Odpowiedź**

```json
{
  "properties": {
    "WriteAccessResourceId": "subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name/linkedservices/cluster",
  "name": "workspace-name/cluster",
  "type": "microsoft.operationalInsights/workspaces/linkedServices",
}
```

Po skojarzeniu dane wysyłane do obszarów roboczych są przechowywane w postaci zaszyfrowanej przy użyciu klucza zarządzanego.

### <a name="workspace-association-verification"></a>Weryfikacja skojarzenia obszaru roboczego
Możesz sprawdzić, czy obszar roboczy jest skojarzony z zasobem *Custer* , przeglądając [obszary robocze — Pobierz](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) odpowiedź. Skojarzony obszar roboczy będzie miał Właściwość "clusterResourceId" z IDENTYFIKATORem zasobu *klastra* .

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2015-11-01-preview
```

**Odpowiedź**

```json
{
  "properties": {
    "source": "Azure",
    "customerId": "workspace-name",
    "provisioningState": "Succeeded",
    "sku": {
      "name": "pricing-tier-name",
      "lastSkuUpdate": "Tue, 28 Jan 2020 12:26:30 GMT"
    },
    "retentionInDays": days,
    "features": {
      "legacy": 0,
      "searchVersion": 1,
      "enableLogAccessUsingOnlyResourcePermissions": true/false,
      "clusterResourceId": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name"
    },
    "workspaceCapping": {
      "dailyQuotaGb": -1.0,
      "quotaNextResetTime": "Tue, 28 Jan 2020 14:00:00 GMT",
      "dataIngestionStatus": "RespectQuota"
    }
  },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region-name"
}
```

## <a name="cmk-kek-revocation"></a>CMK (KEK) — odwołanie

Azure Monitor Storage zawsze będzie uwzględniać zmiany w uprawnieniach klucza w ciągu godziny, zwykle wcześniej, a magazyn stanie się niedostępny. Wszelkie dane pozyskane do obszarów roboczych skojarzonych z zasobem *klastra* zostaną usunięte, a zapytania zakończą się niepowodzeniem. Wcześniej pozyskiwane dane pozostają niedostępne w magazynie Azure Monitor, dopóki klucz zostanie odwołany, a obszary robocze nie zostaną usunięte. Dane niedostępne podlegają zasadom przechowywania danych i zostaną usunięte po osiągnięciu okresu przechowywania.

Magazyn będzie okresowo sondował Key Vault, aby próbować odszyfrować klucz szyfrowania, a następnie uzyskać dostęp do pozyskiwania danych i wznowienia zapytania w ciągu 30 minut.

## <a name="cmk-kek-rotation"></a>CMK (KEK)

Obrót CMK wymaga jawnej aktualizacji zasobu *klastra* przy użyciu nowej wersji klucza Azure Key Vault. Aby zaktualizować Azure Monitor przy użyciu nowej wersji klucza, postępuj zgodnie z instrukcjami w kroku "Aktualizowanie zasobu *klastra* z *identyfikatorem klucza* szczegóły".

Jeśli klucz zostanie zaktualizowany w Key Vault i nie zostanie zaktualizowany nowy *Identyfikator klucza* w zasobie *klastra* *, Azure monitor magazyn będzie nadal korzystać z poprzedniego klucza.

## <a name="limitations-and-constraints"></a>Ograniczenia i ograniczenia

- Funkcja CMK jest obsługiwana na poziomie klastra ADX i wymaga dedykowanego klastra Azure Monitor ADX

- Maksymalna liczba zasobów *klastra* na subskrypcję jest ograniczona do 5

- Skojarzenie zasobów *klastra* z obszarem roboczym należy wykonać dopiero po otrzymaniu potwierdzenia od grupy produktów, że inicjowanie obsługi klastra ADX zostało spełnione. Dane wysyłane przed przystąpieniem do tej aprowizacji zostaną usunięte i nie będzie można ich odzyskać.

- Szyfrowanie CMK ma zastosowanie do nowo wprowadzonych danych po konfiguracji CMK. Dane, które zostały pozyskiwane przed konfiguracją CMK, pozostawały zaszyfrowane za pomocą klucza firmy Microsoft. Dane można badać przed bezproblemową konfiguracją i po niej.

- Po skojarzeniu obszaru roboczego z zasobem *klastra* nie można go usunąć z zasobu *klastra* , ponieważ dane są szyfrowane za pomocą klucza i nie są dostępne bez KEK w Azure Key Vault.

- Azure Key Vault musi być skonfigurowany jako możliwy do odzyskania. Te właściwości nie są domyślnie włączone i należy je skonfigurować przy użyciu interfejsu wiersza polecenia i programu PowerShell:

  - [Usuwanie nietrwałe](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) musi być włączone
  - [Ochrona przed przeczyszczeniem](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) powinna być włączona, aby zabezpieczyć przed wymuszeniem usunięcia wpisu tajnego lub magazynu nawet po usunięciu nietrwałego

- Application Insights i Log Analytics wymagają oddzielnych zasobów *klastra* . Typ zasobu *klastra* jest definiowany w czasie tworzenia przez ustawienie właściwości "clustertype" na wartość "LogAnalytics" lub "ApplicationInsights". Nie można zmienić typu zasobu *klastra* .

- Zasób *klastra* przeniesiony do innej grupy zasobów lub subskrypcji nie jest obecnie obsługiwany.

- Azure Key Vault, zasób *klastra* i powiązane obszary robocze muszą znajdować się w tym samym regionie i w tej samej dzierżawie Azure Active Directory (Azure AD), ale mogą znajdować się w różnych subskrypcjach.

- Skojarzenie obszaru roboczego z zasobem *klastra* zakończy się niepowodzeniem, jeśli jest ono skojarzone z innym zasobem *klastra*

## <a name="troubleshooting-and-management"></a>Rozwiązywanie problemów i zarządzanie

- Dostępność Key Vault
    - W przypadku normalnego działania pamięć podręczna magazynu AEK przez krótkie okresy czasu są okresowo przywracane do Key Vault w celu odpakowania.
    
    - Błędy połączeń przejściowych. Magazyn obsługuje błędy przejściowe (przekroczenia limitu czasu, błędy połączeń, problemy z usługą DNS), dzięki czemu klucze mogą pozostać w pamięci podręcznej przez krótki czas i jest to zbyt małe Blips w dostępności. Możliwości zapytań i pozyskiwania kontynuują działanie bez przeszkód.
    
    - Aktywna witryna, niedostępność przez około 30 minut, spowoduje, że konto magazynu stanie się niedostępne. Funkcja zapytania jest niedostępna, a dane pozyskiwane są buforowane przez kilka godzin przy użyciu klawisza Microsoft, aby uniknąć utraty danych. Po przywróceniu dostępu do Key Vault jest on dostępny, a tymczasowe dane przechowywane w pamięci podręcznej są przechowywane w magazynie danych i szyfrowane za pomocą CMK.

- Jeśli utworzysz zasób *klastra* i natychmiast określisz KeyVaultProperties, operacja może zakończyć się niepowodzeniem, ponieważ nie można zdefiniować zasad dostępu do momentu przypisania tożsamości systemu do zasobu *klastra* .

- W przypadku zaktualizowania istniejącego zasobu *klastra* przy użyciu KeyVaultProperties i braku zasad dostępu do klucza "Get" w Key Vault operacja zakończy się niepowodzeniem.

- Próba usunięcia zasobu *klastra* skojarzonego z obszarem roboczym spowoduje niepowodzenie operacji usuwania.

- Użyj tego interfejsu API, aby pobrać wszystkie zasoby *klastra* dla grupy zasobów:

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
           "KeyVaultProperties": {
              KeyVaultUri: "https://key-vault-name.vault.azure.net",
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

- Użyj tego wywołania interfejsu API, aby pobrać wszystkie zasoby *klastra* dla subskrypcji:

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```
    
  **Odpowiedź**
    
  Taka sama odpowiedź jak dla "zasobów*klastra* dla grupy zasobów", ale w zakresie subskrypcji.
    
- Użyj tego wywołania interfejsu API, aby usunąć zasób *klastra* — należy usunąć wszystkie skojarzone obszary robocze, aby można było usunąć zasób *klastra* :

  ```rst
  DELETE
  https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```

  **Odpowiedź**

  200 OK


## <a name="appendix"></a>Dodatek

Application Insights jest również obsługiwany klucz zarządzany przez klienta (CMK), jednak należy wziąć pod uwagę następujące zmiany, aby ułatwić Planowanie wdrożenia CMK dla składników usługi Application Insights.

Log Analytics i Application Insights używają tej samej platformy do przechowywania danych i aparatu zapytań. Te dwa magazyny są przełączane za pośrednictwem integracji Application Insights w Log Analytics, aby zapewnić jeden ujednolicony magazyn dzienników w Azure Monitor w drugim kwartale
2020. Ta zmiana spowoduje przełączenie danych usługi Application Insights do obszarów roboczych Log Analytics i przeprowadzenie zapytań, szczegółowych informacji i innych ulepszeń w czasie, gdy konfiguracja CMK w obszarze roboczym zostanie również zastosowana do Application Insights danych.

> [!NOTE]
> Jeśli nie musisz wdrażać CMK dla danych usługi Application Insights przed integracją, zalecamy oczekiwanie na Application Insights CMK, ponieważ takie wdrożenia zostaną zakłócone przez integrację i konieczne będzie ponowne skonfigurowanie CMK po migracji do dziennika Obszar roboczy analizy. Wartość 1 TB na dzień jest stosowana na poziomie klastra i do momentu ukończenia konsolidacji w drugim kwartale Application Insights i Log Analytics wymagają oddzielnych klastrów.

## <a name="application-insights-cmk-configuration"></a>Application Insights konfiguracja CMK

Konfiguracja Application Insights CMK jest identyczna z procesem przedstawionym w tym artykule, w tym ograniczenia i rozwiązywanie problemów, z wyjątkiem następujących kroków:

- Tworzenie zasobu *klastra*

- Kojarzenie składnika z zasobem *klastra*

Podczas konfigurowania CMK dla Application Insights należy wykonać te czynności zamiast wymienionych powyżej.

### <a name="create-a-cluster-resource"></a>Tworzenie zasobu *klastra*

Ten zasób jest używany jako połączenie tożsamości pośredniej między Key Vault i składnikami. Po otrzymaniu potwierdzenia, że subskrypcje zostały listy dozwolonyche, utwórz zasób *klastra* log Analytics w regionie, w którym znajdują się składniki. Typ zasobu *klastra* jest definiowany w czasie tworzenia przez ustawienie właściwości *clustertype* na wartość *LogAnalytics*lub *ApplicationInsights*. Powinien być *ApplicationInsights* dla Application Insights CMK. Nie można zmienić ustawienia *klastratype* po konfiguracji.

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
    "principalId": "principle-id"
  },
  "properties": {
    "provisioningState": "Succeeded",
    "clusterType": "ApplicationInsights",    //The value is ‘ApplicationInsights’ for Application Insights CMK
    "clusterId": "cluster-id" 
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name", //The cluster resource Id
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```
"principalId" jest identyfikatorem GUID, który został wygenerowany przez zarządzaną usługę tożsamości.

> [!IMPORTANT]
> Skopiuj i Zachowaj wartość "Cluster-ID", ponieważ będzie ona potrzebna w następnych krokach.

### <a name="associate-a-component-to-a-cluster-resource-using-components---create-or-update-api"></a>Kojarzenie składnika z zasobem *klastra* przy użyciu [składników — Tworzenie lub aktualizowanie](https://docs.microsoft.com/rest/api/application-insights/components/createorupdate) interfejsu API

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Insights/components/<component-name>?api-version=2015-05-01
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "clusterDefinitionId": "cluster-id"
  },
  "location": "<region-name>",
  "kind": "<component-type>"
}
```
"clusterDefinitionId" jest wartością "clusterId" podaną w odpowiedzi z poprzedniego kroku.
przykład "Kind" to "Web".

**Odpowiedź**

```json
{
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.insights/components/component-name",
  "name": "component-name",
  "type": "Microsoft.Insights/components",
  "location": "region-name",
  "tags": "",
  "kind": "",
  "properties": {
    "clusterDefinitionId": "cluster-id"
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
"clusterDefinitionId" jest IDENTYFIKATORem zasobu *klastra* skojarzonym z tym składnikiem.

Po skojarzeniu dane wysyłane do składników są przechowywane w postaci zaszyfrowanej przy użyciu klucza zarządzanego.
