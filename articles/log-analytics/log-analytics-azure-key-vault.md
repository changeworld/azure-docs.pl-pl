---
title: Azure rozwiązania analizy dzienników usługi Key Vault | Dokumentacja firmy Microsoft
description: Rozwiązanie usługi Azure Key Vault w analizy dzienników umożliwia Przejrzyj dzienniki usługi Azure Key Vault.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: jochan
editor: ''
ms.assetid: 5e25e6d6-dd20-4528-9820-6e2958a40dae
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/09/2017
ms.author: richrund
ms.component: na
ms.openlocfilehash: 47158d0c2b5a80ceae25b275836d45b63db50ff4
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37127046"
---
# <a name="azure-key-vault-analytics-solution-in-log-analytics"></a>Azure Key Vault Analytics rozwiązania analizy dzienników

![Symbol magazyn kluczy](./media/log-analytics-azure-keyvault/key-vault-analytics-symbol.png)

Za pomocą rozwiązania Azure Key Vault możesz w usłudze Log Analytics przeglądać dzienniki rozwiązania Azure Key Vault z kategorii AuditEvent.

Użycie rozwiązania, należy włączyć rejestrowanie usługi Azure Key Vault diagnostyki i bezpośrednie diagnostyki do obszaru roboczego analizy dzienników. Nie jest konieczne do zapisywania dzienników do magazynu obiektów Blob Azure.

> [!NOTE]
> W stycznia 2017 r zmienić obsługiwanych sposób wysyłania dzienników z magazynu kluczy do analizy dzienników. Jeśli używasz usługi Key Vault rozwiązanie zawiera *(przestarzałe)* w tytule odnosi się do [migracja z rozwiązania starego magazynu kluczy](#migrating-from-the-old-key-vault-solution) kroki należy wykonać.
>
>

## <a name="install-and-configure-the-solution"></a>Instalowanie i konfigurowanie rozwiązania
Użyj poniższych instrukcji, aby zainstalować i skonfigurować usługi Azure Key Vault rozwiązania:

1. Włączyć rozwiązania usługi Azure Key Vault z [witrynę Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview) lub przy użyciu procesu opisanego w [rozwiązań dodać analizy dzienników z galerii rozwiązań](log-analytics-add-solutions.md).
2. Włączanie rejestrowania diagnostyki dla zasobów magazynu kluczy do monitorowania, za pomocą [portal](#enable-key-vault-diagnostics-in-the-portal) lub [programu PowerShell](#enable-key-vault-diagnostics-using-powershell)

### <a name="enable-key-vault-diagnostics-in-the-portal"></a>Włącz diagnostykę Key Vault w portalu

1. W portalu Azure przejdź do zasobu usługi Key Vault do monitorowania
2. Wybierz *dzienników diagnostycznych* można otworzyć na następującej stronie

   ![obrazów kafelków usługi Azure Key Vault](./media/log-analytics-azure-keyvault/log-analytics-keyvault-enable-diagnostics01.png)
3. Kliknij przycisk *Włącz diagnostykę* można otworzyć na następującej stronie

   ![obrazów kafelków usługi Azure Key Vault](./media/log-analytics-azure-keyvault/log-analytics-keyvault-enable-diagnostics02.png)
4. Aby włączyć diagnostykę, kliknij przycisk *na* w obszarze *stanu*
5. Kliknij pole wyboru *wysyłać do analizy dzienników*
6. Wybierz istniejący obszar roboczy analizy dzienników lub tworzenie obszaru roboczego
7. Aby włączyć *AuditEvent* dzienniki, kliknij pole wyboru w obszarze dziennika
8. Kliknij przycisk *zapisać* Włączanie rejestrowania diagnostyki do analizy dzienników

### <a name="enable-key-vault-diagnostics-using-powershell"></a>Włącz diagnostykę Key Vault przy użyciu programu PowerShell
Poniższy skrypt programu PowerShell zawiera przykład sposobu użycia `Set-AzureRmDiagnosticSetting` Włączanie rejestrowania diagnostyki dla usługi Key Vault:
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'

Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```



## <a name="review-azure-key-vault-data-collection-details"></a>Przejrzyj szczegóły zbierania danych usługi Azure Key Vault
Azure Key Vault rozwiązania służy do zbierania dzienników diagnostycznych bezpośrednio z magazynu kluczy.
Nie jest konieczne do zapisywania dzienników do magazynu obiektów Blob platformy Azure i agent nie jest wymagany do zbierania danych.

W poniższej tabeli przedstawiono metody zbierania danych i inne szczegółowe informacje o sposobie dane są zbierane dla usługi Azure Key Vault.

| Platforma | Bezpośrednie agenta | Agent menedżera operacji centrum systemów | Azure | Wymagane programu Operations Manager? | Danych agenta programu Operations Manager są wysyłane za pośrednictwem grupy zarządzania | Częstotliwość zbierania |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  | po przybyciu |

## <a name="use-azure-key-vault"></a>Korzystanie z rozwiązania Azure Key Vault
Po [zainstalować rozwiązania](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview), przeglądać dane usługi Key Vault, klikając **usługi Azure Key Vault** Kafelek z **omówienie** strony analizy dzienników.

![obrazów kafelków usługi Azure Key Vault](./media/log-analytics-azure-keyvault/log-analytics-keyvault-tile.png)

Po kliknięciu **omówienie** kafelka, możesz wyświetlić podsumowania dzienników i przejść do szczegółów do szczegółów w ramach następujących kategorii:

* Wolumin wszystkie operacje magazynu kluczy w czasie
* Nie powiodła się operacja woluminów wraz z upływem czasu
* Średni czas oczekiwania operacyjnej, przez operację
* Jakość usługi dla operacji z liczby operacji przyjmujących więcej niż 1000 ms oraz listę działań, które przyjmują więcej niż 1000 ms

![Obraz pulpitu nawigacyjnego usługi Azure Key Vault](./media/log-analytics-azure-keyvault/log-analytics-keyvault01.png)

![Obraz pulpitu nawigacyjnego usługi Azure Key Vault](./media/log-analytics-azure-keyvault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Aby wyświetlić szczegóły dotyczące każdej operacji
1. Na **omówienie** kliknij przycisk **usługi Azure Key Vault** kafelka.
2. Na **usługi Azure Key Vault** pulpitu nawigacyjnego, sprawdź informacje w jednym z bloków, a następnie kliknij jedną, aby wyświetlić szczegółowe informacje na stronie dziennik wyszukiwania.

    Na wszystkich stronach wyszukiwania dziennika można wyświetlić wyniki według czasu, szczegółowe wyniki i historię dziennik wyszukiwania. Można również filtrować według aspekty, aby zawęzić wyniki.

## <a name="log-analytics-records"></a>Rekordy usługi Log Analytics
Rozwiązanie usługi Azure Key Vault analizuje rekordów, które mają typ **KeyVaults** które są zbierane z [dzienniki AuditEvent](../key-vault/key-vault-logging.md) w diagnostyce Azure.  Właściwości te rekordy są w poniższej tabeli:  

| Właściwość | Opis |
|:--- |:--- |
| Typ |*AzureDiagnostics* |
| SourceSystem |*Azure* |
| CallerIpAddress |Adres IP klienta, który wysłał żądanie |
| Kategoria | *AuditEvent* |
| CorrelationId |Opcjonalny identyfikator GUID, który klient może przekazać w celu skorelowania dzienników po stronie klienta z dziennikami po stronie usługi (Key Vault). |
| DurationMs |Czas potrzebny do obsłużenia żądania interfejsu API REST podany w milisekundach. Tym razem nie obejmuje opóźnienia sieci, więc czas zmierzony po stronie klienta mogą być niezgodne z tym razem. |
| httpStatusCode_d |Kod stanu HTTP zwracane przez żądanie (na przykład *200*) |
| id_s |Unikatowy identyfikator żądania |
| identity_claim_appid_g | Identyfikator GUID dla identyfikatora aplikacji |
| operationName |Nazwa operacji zgodnie z opisem w [rejestrowanie usługi Azure Key Vault](../key-vault/key-vault-logging.md) |
| OperationVersion |Wersja interfejsu API REST żądanego przez klienta (na przykład *2015-06-01*) |
| requestUri_s |Identyfikator URI żądania |
| Zasób |Nazwa magazynu kluczy |
| ResourceGroup |Grupa zasobów magazynu kluczy |
| ResourceId |Identyfikator zasobu usługi Azure Resource Manager W przypadku dzienników usługi Key Vault jest z identyfikatorem zasobu magazynu kluczy. |
| ResourceProvider |*MICROSOFT.KEYVAULT* |
| ResourceType | *VAULTS* |
| ResultSignature |Stan HTTP (na przykład *OK*) |
| Typ ResultType |Wynik żądania interfejsu API REST (na przykład *Powodzenie*) |
| SubscriptionId |Identyfikator subskrypcji platformy Azure zawierającą magazyn kluczy |

## <a name="migrating-from-the-old-key-vault-solution"></a>Migracja z rozwiązania starego magazynu kluczy
W stycznia 2017 r zmienić obsługiwanych sposób wysyłania dzienników z magazynu kluczy do analizy dzienników. Te zmiany zapewniają następujące korzyści:
+ Dzienniki są zapisywane bezpośrednio do analizy dzienników bez konieczności używania konta magazynu
+ Mniejszych opóźnień od czasu wygenerowania dzienników do nich są dostępne w analizy dzienników
+ Mniejszej liczby kroków konfiguracji
+ Format wspólne dla wszystkich typów Diagnostyka Azure

Użycie zaktualizowanych rozwiązania:

1. [Skonfigurować diagnostykę zostanie wysłane bezpośrednio do analizy dzienników usługi Key Vault](#enable-key-vault-diagnostics-in-the-portal)  
2. Włącz rozwiązanie usługi Azure Key Vault przy użyciu procesu opisanego w [dodać analizy dzienników rozwiązania z galerii rozwiązań](log-analytics-add-solutions.md)
3. Wszystkie zapisane kwerendy, pulpity nawigacyjne lub alerty, aby użyć nowego typu danych
  + Typ jest zmiana z: KeyVaults do AzureDiagnostics. Aby filtrować dzienniki magazynu kluczy, można użyć typu zasobu.
  - Zamiast: `KeyVaults`, użyj `AzureDiagnostics | where ResourceType'=="VAULTS"`
  + Pola: (nazwy pól jest rozróżniana wielkość liter)
  - Dla dowolnego pola, które sufiks \_s, \_d, lub \_g w nazwie, Zmień pierwszy znak na małe litery
  - Dla dowolnego pola, które sufiks \_o w polu Nazwa danych jest podzielony na poszczególnych pól na podstawie nazw pól zagnieżdżonych. Na przykład nazwy UPN wywołującego jest przechowywany w polu `identity_claim_http_schemas_xmlsoap_org_ws_2005_05_identity_claims_upn_s`
   - Zmieniony na CallerIPAddress CallerIpAddress pola
   - Pole RemoteIPCountry nie jest już obecne
4. Usuń *analityka magazynu kluczy (przestarzały)* rozwiązania. Jeśli używasz programu PowerShell, użyj `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "KeyVault" -Enabled $false`

Dane zbierane przed zmiany nie jest widoczny w nowego rozwiązania. Można nadal wysyłać zapytania dotyczące tych danych za pomocą starego typu i nazwy pola.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Kolejne kroki
* Użyj [Zaloguj wyszukiwania analizy dzienników](log-analytics-log-searches.md) Aby wyświetlić szczegółowe dane usługi Azure Key Vault.
