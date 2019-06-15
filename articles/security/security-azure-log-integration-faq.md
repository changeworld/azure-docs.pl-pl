---
title: Integracja dzienników platformy Azure — często zadawane pytania | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera odpowiedzi na pytania dotyczące usługi Azure Log Integration.
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: d06d1ac5-5c3b-49de-800e-4d54b3064c64
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload8: na
ms.date: 05/28/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 9f7d482b088003e3800debb2db9f6f26bda1672a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66298186"
---
# <a name="azure-log-integration-faq"></a>Integracja dzienników platformy Azure — często zadawane pytania

Ten artykuł zawiera odpowiedzi na często zadawane pytania (FAQ) dotyczących integracji dzienników Azure.

>[!IMPORTANT]
> Funkcja integracji dziennika Azure zostaną wycofane 06/15/2019 r. Pliki do pobrania AzLog zostały wyłączone w dniu 27 cze 2018. Aby uzyskać wskazówki dotyczące co należy zrobić przenoszenie do przodu przeglądu wpis [użycia w usłudze Azure monitor do integracji z narzędziami rozwiązania SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Integracja dzienników platformy Azure to usługa systemu operacyjnego Windows, która służy do integracji nieprzetworzonych dzienników z zasobów platformy Azure w Twojej lokalnej zabezpieczeń informacjami i zdarzeniami (SIEM) systemów zarządzania. Integracja ta zapewnia jednolity pulpit nawigacyjny dla wszystkich zasobów w środowisku lokalnym lub w chmurze. Użytkownik może, a następnie agregacji, korelowanie, analizowanie i alertów dla zdarzeń dotyczących zabezpieczeń skojarzone z aplikacjami.

Preferowaną metodą integracji dzienników platformy Azure jest za pomocą łącznika usługi Azure Monitor z dostawcą rozwiązania SIEM, a także następujące [instrukcje](../azure-monitor/platform/stream-monitoring-data-event-hubs.md). Jednak jeśli dostawcy rozwiązania SIEM nie udostępnia łącznik do usługi Azure Monitor, może być można używać integracji dzienników platformy Azure jako rozwiązanie tymczasowe (Jeśli system SIEM jest obsługiwany przez usługi Azure Log Integration) do czasu udostępnienia łącznika programu.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="is-the-azure-log-integration-software-free"></a>Oprogramowanie integracji dzienników platformy Azure jest bezpłatne?

Tak. Nie ma opłat dotyczących oprogramowania Azure Log Integration.

## <a name="where-is-azure-log-integration-available"></a>Gdzie jest integracja dzienników platformy Azure?

Jest obecnie dostępna w wersji Azure Commercial i Azure dla instytucji rządowych i nie jest dostępna w Chinach i Niemczech.

## <a name="how-can-i-see-the-storage-accounts-from-which-azure-log-integration-is-pulling-azure-vm-logs"></a>Jak sprawdzić, kont magazynu, z których Azure Log Integration jest ściąganie dzienników maszyny Wirtualnej platformy Azure?

Uruchom polecenie **listy źródeł AzLog**.

## <a name="how-can-i-tell-which-subscription-the-azure-log-integration-logs-are-from"></a>Jak sprawdzić, dzienniki usługi Azure Log Integration pochodzą z subskrypcji?

W przypadku dzienników inspekcji, które są umieszczane w **AzureResourcemanagerJson** katalogów, subskrypcji, identyfikator ma nazwę pliku dziennika. Dotyczy to także dzienniki w **AzureSecurityCenterJson** folderu. Na przykład:

20170407T070805_2768037.0000000023.**1111e5ee-1111-111b-a11e-1e111e1111dc**.json

Dzienniki inspekcji w usłudze Azure Active Directory zawierają identyfikator dzierżawy, jako część nazwy.

Dzienniki diagnostyczne, które są odczytywane z Centrum zdarzeń nie ma identyfikator subskrypcji, jako część nazwy. Zamiast tego należy umieścić przyjazna nazwa określona jako część tworzenia źródła Centrum zdarzeń. 

## <a name="how-can-i-update-the-proxy-configuration"></a>Jak można zaktualizować konfiguracji serwera proxy?

Jeśli ustawienia serwera proxy nie zezwala na dostęp do usługi Azure storage bezpośrednio, otwórz **AZLOG. PLIK EXE. CONFIG** w pliku **c:\Program Files\Microsoft Azure Log Integration**. Zaktualizuj plik, aby uwzględnić **defaultProxy** części organizacji przy użyciu adresu serwera proxy. Po zakończeniu aktualizacji, Zatrzymaj i uruchom usługę za pomocą poleceń **net stop AzLog** i **net start AzLog**.

    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.net>
        <connectionManagement>
          <add address="*" maxconnection="400" />
        </connectionManagement>
        <defaultProxy>
          <proxy usesystemdefault="true"
          proxyaddress="http://127.0.0.1:8888"
          bypassonlocal="true" />
        </defaultProxy>
      </system.net>
      <system.diagnostics>
        <performanceCounters filemappingsize="20971520" />
      </system.diagnostics>   

## <a name="how-can-i-see-the-subscription-information-in-windows-events"></a>Jak wyświetlić informacje o subskrypcji zdarzeń Windows?

Dołącz identyfikator subskrypcji przyjazną nazwę podczas dodawania źródła:

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  
Zdarzenie XML ma następujące metadane, w tym identyfikator subskrypcji:

![Zdarzenia XML][1]

## <a name="error-messages"></a>Komunikaty o błędach
### <a name="when-i-run-the-command-azlog-createazureid-why-do-i-get-the-following-error"></a>Po uruchomieniu polecenia ```AzLog createazureid```, dlaczego występuje następujący błąd?

Błąd:

  *Nie można utworzyć aplikacji usługi AAD — dzierżawy 72f988bf-86f1-41af-91ab-2d7cd011db37-Przyczyna = komunikat "Zabronione" - = "Uprawnienia niewystarczające do ukończenia tej operacji."*

**Azlog createazureid** próbuje utworzyć nazwę główną usługi w dzierżawach usługi Azure AD w przypadku subskrypcji, w których logowania do systemu Azure ma dostęp do polecenia. Logowania do systemu Azure jest tylko użytkownik-Gość w tej dzierżawie usługi Azure AD, polecenie nie powiedzie się z "Uprawnienia niewystarczające do ukończenia tej operacji." Poproś administratora dzierżawy, aby dodać konto jako użytkownik w dzierżawie.

### <a name="when-i-run-the-command-azlog-authorize-why-do-i-get-the-following-error"></a>Po uruchomieniu polecenia **autoryzować azlog**, dlaczego występuje następujący błąd?

Błąd:

  *Tworzenie przypisania roli - AuthorizationFailed Ostrzeżenie: Janedo klienta\@microsoft.com "z obiektem identyfikatora"fe9e03e4-4dad-4328-910f-fd24a9660bd2"nie ma autoryzacji do wykonania akcji"Microsoft.Authorization/roleAssignments/write"w zakresie" /subscriptions/ 70d 95299 d689 4c 97-b971-0d8ff0000000 ".*

**Autoryzować azlog** polecenie przypisuje rolę czytelnika do jednostki usługi Azure AD (utworzonych za pomocą **azlog createazureid**) dla podanej subskrypcji. Jeśli logowania do systemu Azure nie jest kontem administratora współpracującego czy jesteś właścicielem subskrypcji, zwróci błąd z komunikatem o błędzie "Autoryzacja nie powiodła się.". Azure opartej na rolach kontrola dostępu (RBAC) z kontem administratora współpracującego czy właściciel jest potrzebny do ukończenia tej akcji.

## <a name="where-can-i-find-the-definition-of-the-properties-in-the-audit-log"></a>Gdzie można znaleźć definicji właściwości w dzienniku inspekcji?

Zobacz:

* [Inspekcja operacji za pomocą usługi Azure Resource Manager](../azure-resource-manager/resource-group-audit.md)
* [Wyświetl listę zdarzeń zarządzania w ramach subskrypcji w interfejsu API REST usługi Azure Monitor](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## <a name="where-can-i-find-details-on-azure-security-center-alerts"></a>Gdzie znaleźć szczegółowe informacje na temat alertów w usłudze Azure Security Center?

Zobacz [reagowanie na alerty zabezpieczeń w Centrum zabezpieczeń Azure i zarządzanie nimi](../security-center/security-center-managing-and-responding-alerts.md).

## <a name="how-can-i-modify-what-is-collected-with-vm-diagnostics"></a>Jak modyfikować, jakie informacje są zbierane przy użyciu diagnostyki maszyny Wirtualnej?

Aby uzyskać szczegółowe informacje na temat sposobu uzyskania, modyfikowania i ustawiania konfiguracji diagnostyki platformy Azure, zobacz [Diagnostyka Azure maszyny wirtualnej z systemem Windows w programie PowerShell](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Poniższy przykład pobiera konfigurację diagnostyki platformy Azure:

    Get-AzVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

Poniższy przykład modyfikuje konfigurację diagnostyki platformy Azure. W tej konfiguracji tylko zdarzenia o identyfikatorze 4624 i zdarzenie o identyfikatorze 4625 są zbierane z dziennika zdarzeń zabezpieczeń. Microsoft Antimalware dla zdarzeń dotyczących platformy Azure są zbierane z dziennika zdarzeń systemu. Aby uzyskać szczegółowe informacje związane z użyciem wyrażenia XPath, zobacz [Consuming Events](https://msdn.microsoft.com/library/windows/desktop/dd996910(v=vs.85)).

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

W poniższym przykładzie ustawiono konfiguracji usługi Azure Diagnostics:

    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

Po wprowadzeniu zmian, sprawdź konto magazynu, aby upewnić się, że poprawne zdarzenia są zbierane.

Jeśli masz problemy podczas instalacji i konfiguracji, otwórz [żądania pomocy technicznej](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Wybierz **integracji dzienników** jako usługę, dla której zażądano pomocy technicznej.

## <a name="can-i-use-azure-log-integration-to-integrate-network-watcher-logs-into-my-siem"></a>Aby zintegrować dzienniki usługi Network Watcher Moje SIEM można używać usługi Azure Log Integration?

Usługa Azure Network Watcher generuje dużą ilość informacji rejestrowania. Dzienniki te nie są przeznaczone do wysłania do rozwiązania SIEM. Tylko obsługiwanej lokalizacji docelowej dla dzienników usługi Network Watcher to konto magazynu. Integracja dzienników platformy Azure nie obsługuje odczytywania dzienników i udostępnieniem ich do rozwiązania SIEM.

<!--Image references-->
[1]: ./media/security-azure-log-integration-faq/event-xml.png
