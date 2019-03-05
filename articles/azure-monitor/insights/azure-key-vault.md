---
title: Rozwiązanie na platformie Azure Key Vault w usłudze Log Analytics | Dokumentacja firmy Microsoft
description: Rozwiązanie Azure Key Vault w usłudze Log Analytics umożliwia przeglądanie dzienników usługi Azure Key Vault.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: jochan
editor: ''
ms.assetid: 5e25e6d6-dd20-4528-9820-6e2958a40dae
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/09/2017
ms.author: richrund
ms.openlocfilehash: c3148ebe11ba0e23cbded5965234ece9fb6082aa
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2019
ms.locfileid: "57317700"
---
# <a name="azure-key-vault-analytics-solution-in-log-analytics"></a>Rozwiązanie na platformie Azure Key Vault Analytics w usłudze Log Analytics

![Symbol usługi Key Vault](media/azure-key-vault/key-vault-analytics-symbol.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Za pomocą rozwiązania Azure Key Vault możesz w usłudze Log Analytics przeglądać dzienniki rozwiązania Azure Key Vault z kategorii AuditEvent.

Aby skorzystać z rozwiązania, należy włączyć rejestrowanie diagnostyki usługi Azure Key Vault i kierują je bezpośrednio diagnostyki do obszaru roboczego usługi Log Analytics. Nie jest konieczne do zapisu w dziennikach do usługi Azure Blob storage.

> [!NOTE]
> W stycznia 2017 r. zmienić sposób obsługiwane przesyłania dzienników z usługi Key Vault do usługi Log Analytics. Jeśli używasz rozwiązania Key Vault pokazuje *(przestarzałe)* w tytule, zapoznaj się [migracji ze starego rozwiązania Key Vault](#migrating-from-the-old-key-vault-solution) kroki należy wykonać.
>
>

## <a name="install-and-configure-the-solution"></a>Instalowanie i konfigurowanie rozwiązania
Użyj poniższych instrukcji, aby zainstalować i skonfigurować rozwiązanie Azure Key Vault:

1. Włączanie rozwiązania usługi Azure Key Vault z [portalu Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview) lub przy użyciu procesu opisanego w [rozwiązań Dodaj usługi Log Analytics z galerii rozwiązań](../../azure-monitor/insights/solutions.md).
2. Włączanie rejestrowania diagnostycznego dla zasobów usługi Key Vault monitorować, za pomocą [portal](#enable-key-vault-diagnostics-in-the-portal) lub [programu PowerShell](#enable-key-vault-diagnostics-using-powershell)

### <a name="enable-key-vault-diagnostics-in-the-portal"></a>Włącz diagnostykę usługi Key Vault w portalu

1. W witrynie Azure portal przejdź do zasobu usługi Key Vault do monitorowania
2. Wybierz *dzienniki diagnostyczne* można otworzyć na następującej stronie

   ![Obraz przedstawiający Kafelek usługi Azure Key Vault](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics01.png)
3. Kliknij przycisk *Włącz diagnostykę* można otworzyć na następującej stronie

   ![Obraz przedstawiający Kafelek usługi Azure Key Vault](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics02.png)
4. Aby włączyć diagnostykę, kliknij *na* w obszarze *stanu*
5. Kliknij pole wyboru *wysyłanie do usługi Log Analytics*
6. Wybierz istniejący obszar roboczy usługi Log Analytics lub Utwórz obszar roboczy
7. Aby włączyć *AuditEvent* dzienniki, kliknij pole wyboru w obszarze dziennika
8. Kliknij przycisk *Zapisz* Aby włączyć rejestrowanie diagnostyczne do usługi Log Analytics

### <a name="enable-key-vault-diagnostics-using-powershell"></a>Włącz diagnostykę usługi Key Vault przy użyciu programu PowerShell
Poniższy skrypt programu PowerShell zawiera przykładowy sposób użycia `Set-AzDiagnosticSetting` Aby włączyć rejestrowanie diagnostyczne dla usługi Key Vault:
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'

Set-AzDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```



## <a name="review-azure-key-vault-data-collection-details"></a>Przejrzyj szczegóły zbierania danych usługi Azure Key Vault
Rozwiązanie Azure Key Vault zbiera dzienniki diagnostyczne bezpośrednio z usługi Key Vault.
Nie jest konieczne do zapisu w dziennikach do usługi Azure Blob storage i agent nie jest wymagany do zbierania danych.

W poniższej tabeli przedstawiono metody zbierania danych i inne szczegóły dotyczące sposobu dane są zbierane dla usługi Azure Key Vault.

| Platforma | Agent bezpośredni | Systems Center Operations Manager agent | Azure | Wymagane programu Operations Manager? | Danych agenta programu Operations Manager wysyłane za pośrednictwem grupy zarządzania | Częstotliwość zbierania |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  | Po dostarczeniu |

## <a name="use-azure-key-vault"></a>Korzystanie z rozwiązania Azure Key Vault
Po [zainstalować rozwiązanie](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview), Wyświetl dane usługi Key Vault, klikając **usługi Azure Key Vault** Kafelek z **Przegląd** strony usługi Log Analytics.

![Obraz przedstawiający Kafelek usługi Azure Key Vault](media/azure-key-vault/log-analytics-keyvault-tile.png)

Po kliknięciu **Przegląd** Kafelek, możesz wyświetlić podsumowanie dzienników i następnie przejść do szczegółów w ramach następujących kategorii:

* Wolumin wszystkie operacje usługi key vault, wraz z upływem czasu
* Nie powiodło się woluminy operacyjne wraz z upływem czasu
* Średnie opóźnienie operacyjne przez operację
* Jakość usług dla operacji na wartościach liczby operacji, które przyjmują więcej niż 1000 ms i listę operacji, które przyjmują więcej niż 1000 ms

![Obraz przedstawiający pulpit nawigacyjny usługi Azure Key Vault](media/azure-key-vault/log-analytics-keyvault01.png)

![Obraz przedstawiający pulpit nawigacyjny usługi Azure Key Vault](media/azure-key-vault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Aby wyświetlić szczegóły dotyczące każdej operacji
1. Na **Przegląd** kliknij **usługi Azure Key Vault** kafelka.
2. Na **usługi Azure Key Vault** pulpitu nawigacyjnego, przejrzyj dane podsumowania w jednym z bloków, a następnie kliknij je do wyświetlenia szczegółowych informacji na stronie wyszukiwania dziennika.

    Na wszystkich stronach wyszukiwania dziennika możesz wyświetlić wyników według czasu, szczegółowe wyniki i historię wyszukiwania dziennika. Można również filtrować według zestawu reguł, aby zawęzić wyniki.

## <a name="log-analytics-records"></a>Rekordy usługi Log Analytics
Rozwiązanie Azure Key Vault analizuje rekordy, które mają typ **KeyVaults** , są zbierane z [dzienniki AuditEvent](../../key-vault/key-vault-logging.md) w usłudze Diagnostyka Azure.  Właściwości te rekordy są w poniższej tabeli:  

| Właściwość | Opis |
|:--- |:--- |
| Type |*AzureDiagnostics* |
| SourceSystem |*Azure* |
| CallerIpAddress |Adres IP klienta, który wysłał żądanie |
| Kategoria | *AuditEvent* |
| CorrelationId |Opcjonalny identyfikator GUID, który klient może przekazać w celu skorelowania dzienników po stronie klienta z dziennikami po stronie usługi (Key Vault). |
| durationMs |Czas potrzebny do obsłużenia żądania interfejsu API REST podany w milisekundach. Tym razem nie obejmuje opóźnienia sieci, więc czas, który ma być mierzony po stronie klienta mogą być niezgodne z tym razem. |
| httpStatusCode_d |Zwróconym przez żądanie jest kod stanu HTTP (na przykład *200*) |
| id_s |Unikatowy identyfikator żądania |
| identity_claim_appid_g | Identyfikator GUID dla identyfikatora aplikacji |
| OperationName |Nazwa operacji zgodnie z opisem w [rejestrowanie usługi Azure Key Vault](../../key-vault/key-vault-logging.md) |
| OperationVersion |Wersja interfejsu API REST zażądane przez klienta (na przykład *2015-06-01*) |
| requestUri_s |Identyfikator URI żądania |
| Zasób |Nazwa magazynu kluczy |
| ResourceGroup |Grupa zasobów magazynu kluczy |
| ResourceId |Identyfikator zasobu usługi Azure Resource Manager W przypadku dzienników usługi Key Vault jest identyfikator zasobu usługi Key Vault |
| ResourceProvider |*MICROSOFT.KEYVAULT* |
| ResourceType | *VAULTS* |
| ResultSignature |Stan HTTP (na przykład *OK*) |
| ResultType |Wynik żądania interfejsu API REST (na przykład *Powodzenie*) |
| SubscriptionId |Identyfikator subskrypcji platformy Azure w subskrypcji zawierającej usługi Key Vault |

## <a name="migrating-from-the-old-key-vault-solution"></a>Migracja ze starego rozwiązania Key Vault
W stycznia 2017 r. zmienić sposób obsługiwane przesyłania dzienników z usługi Key Vault do usługi Log Analytics. Te zmiany zapewniają następujące korzyści:
+ Dzienniki są zapisywane bezpośrednio do usługi Log Analytics, bez konieczności używania konta magazynu
+ Mniejszych opóźnień, od momentu, gdy są generowane dzienniki do nich są dostępne w usłudze Log Analytics
+ Mniejszej liczby czynności konfiguracyjnych
+ Wspólny format dla wszystkich typów diagnostyki platformy Azure

Aby użyć zaktualizowane rozwiązanie:

1. [Konfigurowanie diagnostyki, wysyłane bezpośrednio do usługi Log Analytics z usługi Key Vault](#enable-key-vault-diagnostics-in-the-portal)  
2. Włączanie rozwiązania usługi Azure Key Vault przy użyciu procesu opisanego w [rozwiązań Dodaj usługi Log Analytics z galerii rozwiązań](../../azure-monitor/insights/solutions.md)
3. Zaktualizuj wszelkie zapisane zapytania, pulpity nawigacyjne lub alerty, aby użyć nowego typu danych
  + Typ jest zmiana: KeyVaults do AzureDiagnostics. Do filtrowania Key Vault dzienniki, można użyć typu zasobu.
  - Zamiast: `KeyVaults`, użyj `AzureDiagnostics | where ResourceType'=="VAULTS"`
  + Pola: (Nazwy pól jest rozróżniana wielkość liter)
  - Dla dowolnego pola, które ma sufiks \_s, \_d, lub \_g nazwę, zmienić pierwszy znak na małe litery
  - Dla dowolnego pola, które ma sufiks \_o w nazwie, dane zostanie podzielona na poszczególne pola na podstawie nazw pól zagnieżdżonych. Na przykład nazwy UPN obiektu wywołującego jest przechowywany w polu `identity_claim_http_schemas_xmlsoap_org_ws_2005_05_identity_claims_upn_s`
   - Pole CallerIpAddress zmieniony na CallerIPAddress
   - RemoteIPCountry pole nie jest już obecne
4. Usuń *Key Vault Analytics (przestarzałe)* rozwiązania. Jeśli używasz programu PowerShell użyć `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "KeyVault" -Enabled $false`

Dane zbierane, zanim zmiany nie jest widoczna w nowym rozwiązaniu. Można utworzyć zapytanie dotyczące tych danych za pomocą starego typu i nazwy pola.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Kolejne kroki
* Użyj [przeszukiwanie dzienników w usłudze Log Analytics](../../azure-monitor/log-query/log-query-overview.md) Aby przeglądać szczegółowe dane usługi Azure Key Vault.
