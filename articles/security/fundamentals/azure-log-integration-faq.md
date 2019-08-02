---
title: Azure Log Integration często zadawane pytania | Microsoft Docs
description: W tym artykule znajdują się odpowiedzi na pytania dotyczące Azure Log Integration.
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: d06d1ac5-5c3b-49de-800e-4d54b3064c64
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload8: na
ms.date: 05/28/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 7ed63364a9dc4b96470a23af3a4bff832d42621b
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727620"
---
# <a name="azure-log-integration-faq"></a>Azure Log Integration często zadawane pytania

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące Azure Log Integration.

>[!IMPORTANT]
> Funkcja integracja z usługą Azure log zostanie zaniechana przez 06/15/2019. Pliki do pobrania AzLog zostały wyłączone w dniu 27 czerwca 2018. Aby uzyskać wskazówki na temat tego, co należy zrobić, przejrzyj temat publikowanie [za pomocą usługi Azure monitor w celu integracji z narzędziami Siem](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Azure Log Integration to usługa systemu operacyjnego Windows, za pomocą której można zintegrować surowe dzienniki z zasobów platformy Azure w lokalnych systemach zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM). Ta integracja zapewnia ujednolicony pulpit nawigacyjny dla wszystkich zasobów, lokalnie lub w chmurze. Następnie można agregować, skorelować, analizować i generować alerty dla zdarzeń zabezpieczeń skojarzonych z aplikacjami.

Preferowaną metodą integrowania dzienników platformy Azure jest użycie łącznika Azure Monitor dostawcy SIEM i postępując zgodnie z tymi [instrukcjami](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs). Jeśli jednak dostawca SIEM nie udostępnia łącznika do Azure Monitor, można użyć Azure Log Integration jako rozwiązania tymczasowego (jeśli SIEM jest obsługiwany przez Azure Log Integration) do momentu udostępnienia takiego łącznika.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="is-the-azure-log-integration-software-free"></a>Czy Azure Log Integration oprogramowanie jest bezpłatne?

Tak. Za oprogramowanie Azure Log Integration nie są naliczane opłaty.

## <a name="where-is-azure-log-integration-available"></a>Gdzie jest Azure Log Integration dostępne?

Jest ona obecnie dostępna na platformie Azure komercyjnej i Azure Government i nie jest dostępna w Chinach ani w Niemczech.

## <a name="how-can-i-see-the-storage-accounts-from-which-azure-log-integration-is-pulling-azure-vm-logs"></a>Jak można zobaczyć konta magazynu, z których Azure Log Integration są ściągane dzienniki maszyn wirtualnych platformy Azure?

Uruchom **listę źródeł poleceń AzLog**.

## <a name="how-can-i-tell-which-subscription-the-azure-log-integration-logs-are-from"></a>Skąd mam wiedzieć, z której subskrypcji dzienniki Azure Log Integration pochodzą?

W przypadku dzienników inspekcji umieszczonych w katalogach **AZURERESOURCEMANAGERJSON** Identyfikator subskrypcji ma nazwę pliku dziennika. Dotyczy to również dzienników w folderze **AzureSecurityCenterJson** . Na przykład:

20170407T070805_2768037.0000000023.**1111e5ee-1111-111b-a11e-1e111e1111dc**.json

Dzienniki inspekcji Azure Active Directory zawierają identyfikator dzierżawy jako część nazwy.

Dzienniki diagnostyczne odczytywane z centrum zdarzeń nie zawierają identyfikatora subskrypcji jako części nazwy. Zamiast tego zawierają przyjazną nazwę określoną w ramach tworzenia źródła centrum zdarzeń. 

## <a name="how-can-i-update-the-proxy-configuration"></a>Jak mogę zaktualizować konfigurację serwera proxy?

Jeśli ustawienie serwera proxy nie zezwala na bezpośredni dostęp do usługi Azure Storage, Otwórz **AZLOG. EXE. Plik konfiguracji** w **folderze c:\Program Files\Microsoft Azure log Integration**. Zaktualizuj plik, aby zawierał sekcję **defaultProxy** z adresem serwera proxy organizacji. Po zakończeniu aktualizacji Zatrzymaj i uruchom usługę za pomocą poleceń **net stop AzLog** i **net start AzLog**.

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

## <a name="how-can-i-see-the-subscription-information-in-windows-events"></a>Jak można zobaczyć informacje o subskrypcji w zdarzeniach systemu Windows?

Dołącz Identyfikator subskrypcji do przyjaznej nazwy podczas dodawania źródła:

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  
Plik XML zdarzenia zawiera następujące metadane, w tym identyfikator subskrypcji:

![KOD XML zdarzenia](media/azure-log-integration-faq/event-xml.png)

## <a name="error-messages"></a>Komunikaty o błędach
### <a name="when-i-run-the-command-azlog-createazureid-why-do-i-get-the-following-error"></a>Dlaczego po uruchomieniu polecenia ```AzLog createazureid```pojawia się następujący błąd?

Błąd:

  *Nie można utworzyć aplikacji usługi AAD — dzierżawca 72f988bf-86f1-41AF-91ab-2d7cd011db37-Przyczyna = "zabronione"-Message = "niewystarczające uprawnienia do ukończenia operacji".*

**Azlog createazureid** polecenie próbuje utworzyć nazwę główną usługi we wszystkich dzierżawach usługi Azure AD dla subskrypcji, do których ma dostęp identyfikator logowania platformy Azure. Jeśli Twoja nazwa logowania platformy Azure jest tylko użytkownikiem gościa w tej dzierżawie usługi Azure AD, polecenie kończy się niepowodzeniem z "niewystarczającymi uprawnieniami do ukończenia operacji". Poproszenie administratora dzierżawy o dodanie Twojego konta jako użytkownika w dzierżawie.

### <a name="when-i-run-the-command-azlog-authorize-why-do-i-get-the-following-error"></a>Po uruchomieniu polecenia **azlog Autoryzuj**, Dlaczego otrzymuję następujący błąd?

Błąd:

  *Ostrzeżenie tworzenia przypisania roli-AuthorizationFailed: Klient Janedo\@Microsoft.com "o identyfikatorze obiektu" fe9e03e4-4dad-4328-910f-fd24a9660bd2 "nie ma autoryzacji do wykonania akcji" Microsoft. Authorization/roleAssignments/Write "w zakresie"/subscriptions/" 70d95299-d689-4c97-b971-0d8ff0000000'.*

**Azlog Autoryzuj** polecenie przypisuje rolę czytnika do jednostki usługi Azure AD (utworzonej przy użyciu **azlog createazureid**) w podanych subskrypcjach. Jeśli logowanie na platformie Azure nie jest współadministratorem lub właścicielem subskrypcji, kończy się niepowodzeniem z komunikatem o błędzie "Autoryzacja nie powiodła się". Do ukończenia tej akcji jest wymagana Access Control oparte na rolach (RBAC) dla współadministratora lub właściciela.

## <a name="where-can-i-find-the-definition-of-the-properties-in-the-audit-log"></a>Gdzie mogę znaleźć definicję właściwości w dzienniku inspekcji?

Zobacz:

* [Operacje inspekcji przy użyciu Azure Resource Manager](/azure/azure-resource-manager/resource-group-audit)
* [Wyświetlanie listy zdarzeń zarządzania w ramach subskrypcji w interfejsie API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## <a name="where-can-i-find-details-on-azure-security-center-alerts"></a>Gdzie można znaleźć szczegółowe informacje o alertach Azure Security Center?

Zobacz temat [Zarządzanie alertami zabezpieczeń i reagowanie na nie w Azure Security Center](/azure/security-center/security-center-managing-and-responding-alerts).

## <a name="how-can-i-modify-what-is-collected-with-vm-diagnostics"></a>Jak mogę zmodyfikować zawartość zebraną w ramach diagnostyki maszyn wirtualnych?

Aby uzyskać szczegółowe informacje na temat pobierania, modyfikowania i ustawiania konfiguracji Diagnostyka Azure, zobacz [Używanie programu PowerShell do włączania Diagnostyka Azure na maszynie wirtualnej z systemem Windows](/azure/virtual-machines/windows/ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Poniższy przykład pobiera konfigurację Diagnostyka Azure:

    Get-AzVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

Poniższy przykład modyfikuje konfigurację Diagnostyka Azure. W tej konfiguracji tylko zdarzenia o IDENTYFIKATORze 4624 i identyfikator zdarzenia 4625 są zbierane z dziennika zdarzeń zabezpieczeń. Zdarzenia programu Microsoft chroniące przed złośliwym kodem dla platformy Azure są zbierane z dziennika zdarzeń systemu. Aby uzyskać szczegółowe informacje na temat używania wyrażeń XPath, zobacz temat [zużywanie zdarzeń](https://msdn.microsoft.com/library/windows/desktop/dd996910(v=vs.85)).

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

W poniższym przykładzie ustawiono konfigurację Diagnostyka Azure:

    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

Po wprowadzeniu zmian Sprawdź konto magazynu, aby upewnić się, że zbierane są poprawne zdarzenia.

Jeśli podczas instalacji i konfiguracji występują jakieś problemy, Otwórz [żądanie obsługi](/azure/azure-supportability/how-to-create-azure-support-request). Wybierz pozycję **integracja dzienników** jako usługę, dla której chcesz uzyskać pomoc techniczną.

## <a name="can-i-use-azure-log-integration-to-integrate-network-watcher-logs-into-my-siem"></a>Czy można używać Azure Log Integration do integrowania dzienników Network Watcher z moją SIEM?

Usługa Azure Network Watcher generuje duże ilości informacji rejestrowania. Te dzienniki nie są przeznaczone do wysłania do SIEM. Jedyną obsługiwaną lokalizacją docelową dla dzienników Network Watcher jest konto magazynu. Azure Log Integration nie obsługuje odczytywania tych dzienników i udostępniania ich dla SIEM.


