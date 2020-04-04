---
title: Włączanie inspekcji zabezpieczeń usług domenowych usługi Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak włączyć inspekcje zabezpieczeń w celu scentralizowania rejestrowania zdarzeń do analizy i alertów w usługach domenowych usługi Azure AD
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: ce910b553e14d09eefa35efc5f2973337dfa1309
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654673"
---
# <a name="enable-security-audits-for-azure-active-directory-domain-services"></a>Włączanie inspekcji zabezpieczeń usług domenowych Active Directory azure

Inspekcje zabezpieczeń usług domenowych Usługi active directory platformy Azure (Azure AD DS) umożliwiają strumieniowanie zdarzeń zabezpieczeń platformy Azure do zasobów docelowych. Te zasoby obejmują usługi Azure Storage, obszarów roboczych usługi Azure Log Analytics lub Usługi Azure Event Hub. Po włączeniu zdarzeń inspekcji zabezpieczeń usługa Azure AD DS wysyła wszystkie zdarzenia inspekcji dla wybranej kategorii do zasobu docelowego.

Zdarzenia można archiwizować w magazynie platformy Azure i przesyłać strumieniowo zdarzenia do oprogramowania do zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM) (lub równoważnego) przy użyciu usługi Azure Event Hubs lub wykonywać własną analizę i używać obszarów roboczych usługi Azure Log Analytics z witryny Azure portal.

> [!IMPORTANT]
> Inspekcje zabezpieczeń usług Ad DS platformy Azure są dostępne tylko dla wystąpień opartych na usłudze Azure Resource Manager. Aby uzyskać informacje dotyczące migracji, zobacz [Migrowanie usług Azure AD DS z klasycznego modelu sieci wirtualnej do Menedżera zasobów][migrate-azure-adds].

## <a name="security-audit-destinations"></a>Miejsca docelowe inspekcji zabezpieczeń

Usługi Azure Storage, Usługi Azure Event Hubs lub Azure Log Analytics można używać jako zasobu docelowego dla inspekcji zabezpieczeń usług Azure AD DS. Te miejsca docelowe można łączyć. Na przykład można użyć usługi Azure Storage do archiwizacji zdarzeń inspekcji zabezpieczeń, ale obszar roboczy usługi Azure Log Analytics do analizowania i raportowania informacji w krótkim okresie.

W poniższej tabeli przedstawiono scenariusze dla każdego docelowego typu zasobu.

> [!IMPORTANT]
> Przed włączeniem inspekcji zabezpieczeń usług Ad DS platformy Azure należy utworzyć zasób docelowy. Można utworzyć te zasoby przy użyciu witryny Azure Portal, Azure PowerShell lub interfejsu wiersza polecenia platformy Azure.

| Zasób docelowy | Scenariusz |
|:---|:---|
|Azure Storage| Ten cel powinien być używany, gdy podstawową potrzebą jest przechowywanie zdarzeń inspekcji zabezpieczeń do celów archiwalnych. Inne cele mogą być wykorzystywane do celów archiwalnych, jednak te cele zapewniają możliwości wykraczające poza podstawową potrzebę archiwizacji. <br /><br />Przed włączeniem zdarzeń inspekcji zabezpieczeń usług Ad DS platformy Azure należy najpierw [utworzyć konto usługi Azure Storage](../storage/common/storage-account-create.md).|
|Azure Event Hubs| Ten cel powinien być używany, gdy podstawową potrzebą jest udostępnianie zdarzeń inspekcji zabezpieczeń z dodatkowym oprogramowaniem, takim jak oprogramowanie do analizy danych lub informacje o zabezpieczeniach & oprogramowaniu do zarządzania zdarzeniami (SIEM).<br /><br />Przed włączeniem zdarzeń inspekcji zabezpieczeń usług Ad DS usługi [Azure, Utwórz centrum zdarzeń przy użyciu witryny Azure portal](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)|
|Obszar roboczy usługi Azure Log Analytics| Ten obiekt docelowy powinien być używany, gdy podstawową potrzebą jest analizowanie i przeglądanie bezpiecznych inspekcji bezpośrednio z witryny Azure portal.<br /><br />Przed włączeniem zdarzeń inspekcji zabezpieczeń usług Ad DS platformy Azure [utwórz obszar roboczy usługi Log Analytics w witrynie Azure portal.](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)|

## <a name="enable-security-audit-events-using-the-azure-portal"></a>Włączanie zdarzeń inspekcji zabezpieczeń przy użyciu witryny Azure portal

Aby włączyć zdarzenia inspekcji zabezpieczeń usług Azure AD DS przy użyciu witryny Azure portal, wykonaj następujące kroki.

> [!IMPORTANT]
> Inspekcje zabezpieczeń usług AD DS platformy Azure nie są z mocą wsteczną. Nie można pobierać ani powtarzać zdarzeń z przeszłości. Usługi Azure AD DS można wysyłać tylko zdarzenia, które występują po włączeniu inspekcji zabezpieczeń.

1. Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.
1. W górnej części witryny Azure portal wyszukaj i wybierz pozycję **Usługi domenowe usługi Azure AD .** Wybierz domenę zarządzana, na przykład *aaddscontoso.com*.
1. W oknie usług Ad DS wybierz **pozycję Ustawienia diagnostyczne** po lewej stronie.
1. Domyślnie nie są skonfigurowane żadne diagnostyki. Aby rozpocząć, wybierz **pozycję Dodaj ustawienie diagnostyczne**.

    ![Dodawanie ustawienia diagnostycznego dla usług domenowych usługi Azure AD](./media/security-audit-events/add-diagnostic-settings.png)

1. Wprowadź nazwę konfiguracji diagnostycznej, taką jak *inspekcja aadds*.

    Zaznacz pole wyboru miejsca docelowego inspekcji zabezpieczeń, które chcesz. Możesz wybrać konto usługi Azure Storage, centrum zdarzeń platformy Azure lub obszar roboczy usługi Log Analytics. Te zasoby docelowe muszą już istnieć w subskrypcji platformy Azure. Nie można utworzyć zasobów docelowych w tym kreatorze.

    ![Włącz wymagane miejsce docelowe i typ zdarzeń inspekcji do przechwytywania](./media/security-audit-events/diagnostic-settings-page.png)

    * **Magazyn platformy Azure**
        * Wybierz **pozycję Archiwizuj na konto magazynu,** a następnie wybierz pozycję **Konfiguruj**.
        * Wybierz **konto Subskrypcja** i **Magazyn,** którego chcesz użyć do archiwizacji zdarzeń inspekcji zabezpieczeń.
        * Gdy będzie gotowy, wybierz przycisk **OK**.
    * **Centra zdarzeń platformy Azure**
        * Wybierz **pozycję Strumień do centrum zdarzeń**, a następnie wybierz pozycję **Konfiguruj**.
        * Wybierz **obszar nazw Subskrypcja** i **Centrum zdarzeń**. W razie potrzeby wybierz również **nazwę Centrum zdarzeń,** a następnie **nazwę zasad Centrum zdarzeń**.
        * Gdy będzie gotowy, wybierz przycisk **OK**.
    * **Analityczne obszary robocze dziennika platformy Azure**
        * Wybierz **pozycję Wyślij do usługi Log Analytics**, a następnie wybierz obszar roboczy **Subskrypcja** i **usługa analizy dzienników,** którego chcesz użyć do przechowywania zdarzeń inspekcji zabezpieczeń.

1. Wybierz kategorie dzienników, które mają zostać uwzględnione dla określonego zasobu docelowego. Jeśli wysyłasz zdarzenia inspekcji do konta usługi Azure Storage, można również skonfigurować zasady przechowywania, który definiuje liczbę dni do przechowywania danych. Domyślne ustawienie *0* zachowuje wszystkie dane i nie obraca zdarzeń po pewnym czasie.

    Można wybrać różne kategorie dzienników dla każdego zasobu docelowego w ramach jednej konfiguracji. Ta możliwość pozwala wybrać, które dzienniki kategorii, które chcesz zachować dla usługi Log Analytics i które dzienniki kategorii chcesz zarchiwizować, na przykład.

1. Po zakończeniu wybierz **pozycję Zapisz,** aby zatwierdzić zmiany. Zasoby docelowe zaczynają otrzymywać zdarzenia inspekcji zabezpieczeń usług Ad DS platformy Azure wkrótce po zapisaniu konfiguracji.

## <a name="enable-security-audit-events-using-azure-powershell"></a>Włączanie zdarzeń inspekcji zabezpieczeń przy użyciu programu Azure PowerShell

Aby włączyć zdarzenia inspekcji zabezpieczeń usług Azure AD DS przy użyciu programu Azure PowerShell, wykonaj następujące kroki. W razie potrzeby najpierw [zainstaluj moduł programu Azure PowerShell i połącz się z subskrypcją platformy Azure.](/powershell/azure/install-az-ps)

> [!IMPORTANT]
> Inspekcje zabezpieczeń usług AD DS platformy Azure nie są z mocą wsteczną. Nie można pobierać ani powtarzać zdarzeń z przeszłości. Usługi Azure AD DS można wysyłać tylko zdarzenia, które występują po włączeniu inspekcji zabezpieczeń.

1. Uwierzytelnij się w subskrypcji platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount.](/powershell/module/Az.Accounts/Connect-AzAccount) Po wyświetleniu monitu wprowadź poświadczenia konta.

    ```azurepowershell
    Connect-AzAccount
    ```

1. Utwórz zasób docelowy dla zdarzeń inspekcji zabezpieczeń.

    * **Usługa Azure Storage** - [Tworzenie konta magazynu przy użyciu programu Azure PowerShell](../storage/common/storage-account-create.md?tabs=azure-powershell)
    * **Centra zdarzeń platformy Azure** - Tworzenie centrum zdarzeń przy użyciu programu[Azure PowerShell](../event-hubs/event-hubs-quickstart-powershell.md). Może być również konieczne użycie polecenia cmdlet [New-AzEventHubAuthorizationRule](/powershell/module/az.eventhub/new-azeventhubauthorizationrule) do utworzenia reguły autoryzacji, która przyznaje uprawnienia usługi Azure AD DS *do obszaru nazw*centrum zdarzeń. Reguła autoryzacji musi zawierać prawa **Zarządzanie,** **Nasłuchij**i **Wyślij.**

        > [!IMPORTANT]
        > Upewnij się, że ustawiono regułę autoryzacji w obszarze nazw centrum zdarzeń, a nie w samym centrum zdarzeń.

    * **Analityczne obszary robocze** - dziennika platformy Azure[Tworzenie obszaru roboczego usługi Log Analytics za pomocą programu Azure PowerShell](../azure-monitor/learn/quick-create-workspace-posh.md).

1. Pobierz identyfikator zasobu dla domeny zarządzanej usług Azure AD DS przy użyciu polecenia cmdlet [Get-AzResource.](/powershell/module/Az.Resources/Get-AzResource) Utwórz zmienną o nazwie *$aadds. Identyfikator zasobu* do przechowywania wartości:

    ```azurepowershell
    $aadds = Get-AzResource -name aaddsDomainName
    ```

1. Skonfiguruj ustawienia diagnostyki platformy Azure przy użyciu polecenia cmdlet [Set-AzDiagnosticSetting,](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting) aby użyć zasobu docelowego dla zdarzeń inspekcji zabezpieczeń usług domenowych usługi Azure AD. W poniższych przykładach zmienna *$aadds. Identyfikator zasobu* jest używany z poprzedniego kroku.

    * **Magazyn platformy Azure** — zamień *konto magazynu* na nazwę konta magazynu:

        ```powershell
        Set-AzDiagnosticSetting `
            -ResourceId $aadds.ResourceId `
            -StorageAccountId storageAccountId `
            -Enabled $true
        ```

    * **Usługi Azure event hubs** — zamień *eventHubName* na nazwę centrum zdarzeń i *eventHubRuleId* o identyfikatorze reguły autoryzacji:

        ```powershell
        Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId `
            -EventHubName eventHubName `
            -EventHubAuthorizationRuleId eventHubRuleId `
            -Enabled $true
        ```

    * **Obszary robocze** analizy dziennika platformy Azure — zamień *obszar roboczy* identyfikatorem obszaru roboczego usługi Log Analytics:

        ```powershell
        Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId `
            -WorkspaceID workspaceId `
            -Enabled $true
        ```

## <a name="query-and-view-security-audit-events-using-azure-monitor"></a>Kwerenda i wyświetlanie zdarzeń inspekcji zabezpieczeń przy użyciu usługi Azure Monitor

Dziennik analitycznych obszarów roboczych umożliwiają wyświetlanie i analizowanie zdarzeń inspekcji zabezpieczeń przy użyciu usługi Azure Monitor i języka zapytań Kusto. Ten język kwerendy jest przeznaczony do użytku tylko do odczytu, który oferuje możliwości analityczne zasilania z łatwą do odczytania składnią. Aby uzyskać więcej informacji, aby rozpocząć korzystanie z języków zapytań Kusto, zobacz następujące artykuły:

* [Dokumentacja usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)
* [Wprowadzenie do usługi Log Analytics w usłudze Azure Monitor](../azure-monitor/log-query/get-started-portal.md)
* [Wprowadzenie do zapytań dziennika w usłudze Azure Monitor](../azure-monitor/log-query/get-started-queries.md)
* [Tworzenie i udostępnianie pulpitów nawigacyjnych z danymi usługi Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md)

Następujące przykładowe kwerendy mogą służyć do rozpoczęcia analizowania zdarzeń inspekcji zabezpieczeń z usługi Azure AD DS.

### <a name="sample-query-1"></a>Przykładowe zapytanie 1

Wyświetl wszystkie zdarzenia blokady konta z ostatnich siedmiu dni:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>Przykładowe zapytanie 2

Zobacz wszystkie zdarzenia blokady konta (*4740*) między 3 lutego 2020 r. o godzinie 9:00. i 10 lutego 2020 r., posortowane rosnąco według daty i godziny:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-02-03 09:00) and TimeGenerated <= datetime(2020-02-10)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>Przykładowe zapytanie 3

Wyświetl zdarzenia logowania do konta siedem dni temu (od teraz) dla konta o nazwie użytkownik:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>Przykładowe zapytanie 4

Wyświetl zdarzenia logowania do konta siedem dni temu od teraz dla konta o nazwie użytkownika, który próbował zalogować się przy użyciu złego hasła (*0xC00000006a*):

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>Przykładowe zapytanie 5

Wyświetl zdarzenia logowania do konta siedem dni temu od teraz dla konta o nazwie użytkownik, który próbował się zalogować, gdy konto zostało zablokowane (*0xC0000234*):

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>Przykładowe zapytanie 6

Wyświetl liczbę zdarzeń logowania do konta siedem dni temu od teraz dla wszystkich prób logowania, które wystąpiły dla wszystkich zablokowanych użytkowników:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="audit-event-categories"></a>Inspekcja kategorii zdarzeń

Inspekcje zabezpieczeń usług Ad DS platformy Azure są zgodne z tradycyjną inspekcją tradycyjnych kontrolerów domeny usług AD DS. W środowiskach hybrydowych można ponownie użyć istniejących wzorców inspekcji, więc ta sama logika może być używana podczas analizowania zdarzeń. W zależności od scenariusza, który należy rozwiązać lub przeanalizować, różne kategorie zdarzeń inspekcji muszą być kierowane.

Dostępne są następujące kategorie zdarzeń inspekcji:

| Nazwa kategorii inspekcji | Opis |
|:---|:---|
| Konto logowania|Inspekcje próbuje uwierzytelnić dane konta na kontrolerze domeny lub w lokalnym Menedżerze kont zabezpieczeń (SAM).</p>Ustawienia zasad logowania i wylogowywania oraz zdarzenia śledzą próby uzyskania dostępu do określonego komputera. Ustawienia i zdarzenia w tej kategorii koncentrują się na bazie danych kont, która jest używana. Ta kategoria obejmuje następujące podkategorie:<ul><li>[Inspekcja weryfikacji poświadczeń](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[Inspekcja usługi uwierzytelniania Kerberos](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[Inspekcja operacji biletów usługi Kerberos](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[Inspekcja innych zdarzeń logowania/wylogowywania](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| Zarządzanie kontami|Przeprowadza inspekcje zmian na kontach i grupach użytkowników i komputerów. Ta kategoria obejmuje następujące podkategorie:<ul><li>[Inspekcja zarządzania grupami aplikacji](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[Inspekcja zarządzania kontami komputerów](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[Inspekcja zarządzania grupami dystrybucyjnymi](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[Inspekcja innych zarządzania kontem](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[Inspekcja zarządzania grupami zabezpieczeń](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[Inspekcja zarządzania kontami użytkowników](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| Śledzenie szczegółów|Przeprowadza inspekcje działań poszczególnych aplikacji i użytkowników na tym komputerze oraz w celu zrozumienia, w jaki sposób używany jest komputer. Ta kategoria obejmuje następujące podkategorie:<ul><li>[Inspekcja działań DPAPI](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[Inspekcja działania PNP](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[Tworzenie procesu inspekcji](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[Inspekcja kończenia procesu](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[Inspekcja zdarzeń RPC](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| Dostęp do usług katalogowych|Inspekcje próbuje uzyskać dostęp do obiektów i zmodyfikować ich w Usługach domenowych Active Directory (AD DS). Te zdarzenia inspekcji są rejestrowane tylko na kontrolerach domeny. Ta kategoria obejmuje następujące podkategorie:<ul><li>[Inspekcja szczegółowej replikacji usługi katalogowej](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[Przeprowadź inspekcję dostępu do usługi katalogowej](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[Inspekcja zmian usługi katalogowej](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[Inspekcja replikacji usługi katalogowej](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| Logowanie-wylogowanie|Inspekcje próbuje zalogować się do komputera interaktywnie lub za pośrednictwem sieci. Zdarzenia te są przydatne do śledzenia aktywności użytkowników i identyfikowania potencjalnych ataków na zasoby sieciowe. Ta kategoria obejmuje następujące podkategorie:<ul><li>[Inspekcja blokady konta](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[Inspekcja oświadczeń użytkownika/urządzenia](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[Inspekcja trybu rozszerzonego protokołu IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[Członkostwo w grupie audytu](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[Inspekcja trybu głównego protokołu IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[Inspekcja trybu szybkiego protokołu IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[Inspekcja wylogowania](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logoff)</li><li>[Inspekcja logowania](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logon)</li><li>[Inspekcja serwera zasad sieciowych](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[Inspekcja innych zdarzeń logowania/wylogowywania](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[Inspekcja logowania specjalnego](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|Dostęp do obiektów| Inspekcje próbuje uzyskać dostęp do określonych obiektów lub typów obiektów w sieci lub komputerze. Ta kategoria obejmuje następujące podkategorie:<ul><li>[Wygenerowano aplikację inspekcji](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[Inspekcja usług certyfikacji](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[Inspekcja szczegółowego udziału plików](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[Inspekcja udziału plików](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-share)</li><li>[System plików inspekcji](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-system)</li><li>[Inspekcja połączenia platformy filtrowania](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[Inspekcja porzucania pakietów platformy filtrowania](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[Manipulowanie uchwytem inspekcji](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[Audyt obiektu jądra](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[Inspekcja innych zdarzeń dostępu do obiektów](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[Rejestr inspekcji](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-registry)</li><li>[Inspekcja Magazynu wymiennego](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[Inspekcja SAM](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sam)</li><li>[Inspekcja centralnej zasady dostępu Staging](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|Zmiana zasad|Przeprowadza inspekcje zmian w ważnych zasadach zabezpieczeń w systemie lokalnym lub sieci. Zasady są zazwyczaj ustanawiane przez administratorów, aby pomóc w zabezpieczeniu zasobów sieciowych. Monitorowanie zmian lub prób zmiany tych zasad może być ważnym aspektem zarządzania zabezpieczeniami dla sieci. Ta kategoria obejmuje następujące podkategorie:<ul><li>[Inspekcja zmiany zasad inspekcji](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[Inspekcja zmiany zasad uwierzytelniania](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[Inspekcja zmiany zasad autoryzacji](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[Inspekcja zmiany zasad platformy filtrowania](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[Inspekcja zmiany zasad na poziomie reguł MPSSVC](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[Inspekcja innych zmian zasad](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|Użycie uprawnień| Inspekcje użycia niektórych uprawnień w jednym lub więcej systemach. Ta kategoria obejmuje następujące podkategorie:<ul><li>[Inspekcja użycia zwykłych uprawnień](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[Inspekcja użycia poufnych uprawnień](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[Inspekcja innych zdarzeń użycia uprawnień](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|System| Przeprowadza inspekcje zmian na poziomie systemu na komputerze, który nie jest uwzględniony w innych kategoriach i które mają potencjalne konsekwencje dla bezpieczeństwa. Ta kategoria obejmuje następujące podkategorie:<ul><li>[Inspekcja sterownika IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[Inspekcja innych zdarzeń systemowych](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[Inspekcja zmian stanu zabezpieczeń](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[Inspekcja rozszerzenia systemu zabezpieczeń](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[Inspekcja integralności systemu](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>Identyfikatory zdarzeń dla kategorii

 Inspekcje zabezpieczeń usług Ad DS usługi Azure rejestrują następujące identyfikatory zdarzeń, gdy określona akcja wyzwala zdarzenie podlegające inspekcji:

| Nazwa kategorii zdarzenia | Identyfikatory zdarzeń |
|:---|:---|
|Zabezpieczenia logowania do konta|4767, 4774, 4775, 4776, 4777|
|Zabezpieczenia zarządzania kontem|4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4730, 4731, 4732, 4733, 4734, 4735, 4737, 4738, 4740, 4741, 4742, 4743, 4754, 4755, 4756, 4757, 4758, 4764, 4765, 4766, 4780, 4781, 4782, 4793, 4798, 4799, 5376, 5377|
|Bezpieczeństwo śledzenia szczegółów|Brak|
|Zabezpieczenia ds.|5136, 5137, 5138, 5139, 5141|
|Zabezpieczenia logowania i wylogowania|4624, 4625, 4634, 4647, 4648, 4672, 4675, 4964|
|Zabezpieczenia dostępu do obiektów|Brak|
|Zabezpieczenia dotyczące zmiany zasad|4670, 4703, 4704, 4705, 4706, 4707, 4713, 4715, 4716, 4717, 4718, 4719, 4739, 4864, 4865, 4866, 4867, 4904, 4906, 4911, 4912|
|Zabezpieczenia użytkowania uprawnień|4985|
|Zabezpieczenia systemu|4612, 4621|

## <a name="next-steps"></a>Następne kroki

Szczegółowe informacje na temat Kusto można znaleźć w następujących artykułach:

* [Omówienie](/azure/kusto/query/) języka zapytania Kusto.
* [Kusto samouczek,](/azure/kusto/query/tutorial) aby zapoznać się z podstawami zapytań.
* [Przykładowe zapytania,](/azure/kusto/query/samples) które pomogą Ci dowiedzieć się nowych sposobów, aby zobaczyć dane.
* [Kusto najlepsze praktyki](/azure/kusto/query/best-practices) w celu optymalizacji zapytań do sukcesu.

<!-- LINKS - Internal -->
[migrate-azure-adds]: migrate-from-classic-vnet.md
