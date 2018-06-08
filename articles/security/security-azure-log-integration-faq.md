---
title: Integracja dzienników Azure — często zadawane pytania | Dokumentacja firmy Microsoft
description: W tym artykule odpowiedzi na pytania dotyczące integracji dziennika Azure.
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
ms.date: 06/06/2018
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 31524f24eea97082c8b148ba0a65bbeb24717039
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34839324"
---
# <a name="azure-log-integration-faq"></a>Integracja dzienników Azure — często zadawane pytania

Ten artykuł zawiera odpowiedzi na często zadawane pytania (FAQ) dotyczące integracji dziennika Azure.

>[!IMPORTANT]
> Funkcja integracji Azure dziennika zostaną wycofane przez 2019-06/01.  Pliki do pobrania AzLog zostanie wyłączone przez 27 cze 2018. Aby uzyskać wskazówki dotyczące co należy zrobić przenoszenie do przodu przeglądu post [monitor Azure używana do integracji z narzędzia SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/preview/?cdn=disable) 

Integracja dziennika Azure to usługa systemu operacyjnego Windows używanej do integracji nowych dzienników z zasobów platformy Azure w sieci lokalnej zabezpieczeń informacji i zdarzenia (SIEM) systemów zarządzania. Integracja ta zapewnia jednolity pulpit nawigacyjny dla wszystkich zasobów, lokalnie lub w chmurze. Możesz agregować, skorelowania, analizowanie i alertów zdarzeń zabezpieczeń skojarzonych z aplikacjami.

Preferowaną metodą integrowanie dzienników Azure jest za pomocą łącznika Azure Monitor dostawcą SIEM i wykonując te [instrukcje](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md). Jednak jeśli dostawcą SIEM nie łącznika do monitorowania Azure, można korzystać z usługi Azure dziennika integracji jako rozwiązanie tymczasowe (Jeśli system SIEM jest obsługiwana przez integrację dziennika Azure) do czasu udostępnienia łącznika programu.

## <a name="is-the-azure-log-integration-software-free"></a>Zwolnieniu oprogramowania integracji dziennika Azure?

Tak. Brak bezpłatne oprogramowanie Integration dziennika Azure.

## <a name="where-is-azure-log-integration-available"></a>Gdzie jest integracja dziennika Azure?

Jest obecnie dostępna w Azure handlowych i Azure dla instytucji rządowych i nie jest dostępny w Chinach lub Niemczech.

## <a name="how-can-i-see-the-storage-accounts-from-which-azure-log-integration-is-pulling-azure-vm-logs"></a>Jak można wyświetlić kont magazynu, z których integracji dziennika Azure jest ściąganie dzienniki maszyny Wirtualnej platformy Azure?

Uruchom polecenie **listy źródeł AzLog**.

## <a name="how-can-i-tell-which-subscription-the-azure-log-integration-logs-are-from"></a>Jak sprawdzić, subskrypcji, w której są dzienniki Azure dziennika integracji z?

W przypadku dzienników inspekcji, które są umieszczone w **AzureResourcemanagerJson** katalogów, subskrypcji identyfikator jest nazwa pliku dziennika. Dotyczy to również dzienniki w **AzureSecurityCenterJson** folderu. Na przykład:

20170407T070805_2768037.0000000023.**1111e5ee-1111-111b-a11e-1e111e1111dc**.json

Dzienniki inspekcji w usłudze Azure Active Directory zawierają identyfikator dzierżawy jako część nazwy.

Dzienniki diagnostyczne, które są odczytywane z Centrum zdarzeń nie ma identyfikator subskrypcji jako część nazwy. Zamiast tego zawierają przyjazną nazwę określony jako część tworzenie źródło zdarzenia koncentratora. 

## <a name="how-can-i-update-the-proxy-configuration"></a>Jak można zaktualizować konfiguracji serwera proxy?

Jeśli ustawienie serwera proxy nie zezwalają na dostęp do magazynu Azure bezpośrednio, otwórz **AZLOG. WYWOŁANIE PLIKU EXE. CONFIG** w pliku **c:\Program Files\Microsoft Azure dziennika integracji**. Aktualizowanie pliku, aby uwzględnić **defaultProxy —** sekcji o adresie serwera proxy w swojej organizacji. Po ukończeniu aktualizacji, Zatrzymaj i uruchom usługę za pomocą poleceń **net stop AzLog** i **net start AzLog**.

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

## <a name="how-can-i-see-the-subscription-information-in-windows-events"></a>Jak wyświetlić informacje dotyczące subskrypcji w zdarzeń systemu Windows

Dołącz identyfikator subskrypcji do przyjaznej nazwy podczas dodawania źródła:

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  
Zdarzenie XML ma następujące metadane, identyfikator subskrypcji w tym:

![Zdarzenie XML][1]

## <a name="error-messages"></a>Komunikaty o błędach
### <a name="when-i-run-the-command-azlog-createazureid-why-do-i-get-the-following-error"></a>Po uruchomieniu polecenia ```AzLog createazureid```, dlaczego uzyskać następujący błąd?

Błąd:

  *Nie można utworzyć aplikację AAD - dzierżawy 72f988bf-86f1-41af-91ab-2d7cd011db37-Przyczyna = "Zabronione" - komunikat = "Wystarczających uprawnień do ukończenia tej operacji."*

**Azlog createazureid** polecenie podejmuje próbę utworzenia nazwy głównej usługi w wszystkich dzierżaw usługi Azure AD dla subskrypcji, w których Azure logowania ma dostęp. Jeśli logowanie w usłudze Azure jest tylko użytkownik-Gość w tej dzierżawie usługi Azure AD, polecenie kończy się niepowodzeniem "Wystarczających uprawnień do ukończenia tej operacji." Poproś administratora dzierżawy. Aby dodać konto użytkownika w dzierżawie.

### <a name="when-i-run-the-command-azlog-authorize-why-do-i-get-the-following-error"></a>Po uruchomieniu polecenia **azlog autoryzować**, dlaczego uzyskać następujący błąd?

Błąd:

  *Ostrzeżenie tworzenia przypisania roli - AuthorizationFailed: klient janedo@microsoft.com"z obiektem id"fe9e03e4-4dad-4328-910f-fd24a9660bd2"nie ma autoryzacji do wykonania akcji"Microsoft.Authorization/roleAssignments/write"w zakresie" / Subskrypcje / 70d 95299-d689-4c 97-b971-0d8ff0000000 ".*

**Autoryzować azlog** polecenia przypisuje rolę czytelnika do nazwy głównej usługi Azure AD (utworzone za pomocą **azlog createazureid**) do podanego subskrypcji. Jeśli logowanie w usłudze Azure nie jest administratora współpracującego lub właściciela subskrypcji, nie jest on z komunikatem o błędzie "Autoryzacja nie powiodła się.". Azure opartej na rolach kontroli dostępu (RBAC) administratora współpracującego lub właściciela wymaganego do ukończenia tej akcji.

## <a name="where-can-i-find-the-definition-of-the-properties-in-the-audit-log"></a>Gdzie można znaleźć definicji właściwości w dzienniku inspekcji?

Zobacz:

* [Operacje inspekcji z usługi Azure Resource Manager](../azure-resource-manager/resource-group-audit.md)
* [Wyświetl listę zdarzeń zarządzania w ramach subskrypcji w interfejsie API REST Azure monitora](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## <a name="where-can-i-find-details-on-azure-security-center-alerts"></a>Gdzie znaleźć szczegółowe informacje o alertach Centrum zabezpieczeń Azure

Zobacz [reagowanie na alerty zabezpieczeń w Centrum zabezpieczeń Azure i zarządzanie nimi](../security-center/security-center-managing-and-responding-alerts.md).

## <a name="how-can-i-modify-what-is-collected-with-vm-diagnostics"></a>Jak zmodyfikować, jakie informacje są zbierane z diagnostyki maszyny Wirtualnej?

Aby uzyskać szczegółowe informacje dotyczące sposobu uzyskania, modyfikowania i ustawiania konfiguracji diagnostyki Azure zobacz [Użyj programu PowerShell, aby włączyć na maszynie wirtualnej z systemem Windows Azure Diagnostics](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Poniższy przykład pobiera konfigurację diagnostyki Azure:

    -AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

Poniższy przykład modyfikuje konfigurację diagnostyki Azure. W tej konfiguracji tylko zdarzeń 4624 identyfikator i identyfikator 4625 są zbierane z dziennika zdarzeń zabezpieczeń. Antimalware Microsoft Azure zdarzenia są zbierane z dziennika zdarzeń systemu. Aby uzyskać więcej informacji dotyczących korzystania z wyrażenia XPath, zobacz [wykorzystywanie zdarzenia](https://msdn.microsoft.com/library/windows/desktop/dd996910(v=vs.85)).

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

Poniższy przykład przedstawia konfigurację diagnostyki Azure:

    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

Po wprowadzeniu zmian sprawdź konto magazynu, aby upewnić się, że poprawne zdarzenia są zbierane.

Jeśli masz problemy podczas instalacji i konfiguracji, otwórz [żądania obsługi](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Wybierz **integracji dziennika** jako usługa żądania pomocy technicznej.

## <a name="can-i-use-azure-log-integration-to-integrate-network-watcher-logs-into-my-siem"></a>Azure dziennika integracji można używać do integracji dzienniki obserwatora sieciowego Mój SIEM?

Azure obserwatora sieciowego generuje duże ilości danych rejestrowania. Dzienniki te nie są przeznaczone do wysłania do rozwiązania SIEM. Tylko obsługiwane miejsce docelowe dla dzienników obserwatora sieciowego jest konto magazynu. Integracja z usługą Azure dziennika nie obsługuje odczytywania te dzienniki i udostępnienie ich SIEM.

<!--Image references-->
[1]: ./media/security-azure-log-integration-faq/event-xml.png
