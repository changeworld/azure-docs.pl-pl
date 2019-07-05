---
title: Włączanie inspekcji dla usług domenowych Azure AD | Dokumentacja firmy Microsoft
description: Włączanie inspekcji dla usług domenowych Azure AD
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: iainfou
ms.openlocfilehash: 3105296b3c670d3d44789c93878fa1fc6076973b
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566497"
---
# <a name="enable-security-audits-for-azure-ad-domain-services-preview"></a>Włączanie inspekcji dla usług domenowych Azure AD (wersja zapoznawcza)
Inspekcja zabezpieczeń usługi w usłudze Azure AD Domain umożliwia klientom korzystanie z portalu usługi Azure AD Domain do strumienia zdarzeń inspekcji zabezpieczeń zasobów docelowych. Zasoby, które mogą odbierać zdarzenia te obejmują usługi Azure Storage, obszary robocze usługi Azure Log Analytics lub Centrum zdarzeń platformy Azure. Wkrótce po włączeniu zdarzeń inspekcji zabezpieczeń usług domenowych Azure AD wysyła zdarzenia inspekcji dla wybranej kategorii do zasobu docelowego. Zdarzenia inspekcji zabezpieczeń umożliwiać klientom archiwum poddane inspekcji zdarzenia w usłudze Azure storage. Ponadto klienci mogą przesyłanie strumieniowe zdarzeń do zabezpieczeń informacji i zdarzeń oprogramowania do zarządzania (SIEM) (lub odpowiednik) przy użyciu usługi event hubs lub własnych analizy i szczegółowych informacji za pomocą usługi Azure Log Analytics w witrynie Azure portal. 

> [!IMPORTANT]
> Inspekcja zabezpieczeń platformy Azure w usługach domenowych AD jest dostępne tylko w przypadku wystąpienia oparte na usłudze Azure Resource Manager dla usługi Azure AD Domain Services.
>
>

## <a name="auditing-event-categories"></a>Kategorie zdarzeń inspekcji
Inspekcja zabezpieczeń platformy Azure w usługach domenowych AD wyrównane tradycyjny inspekcji, który jest dostarczany dla kontrolerów domeny Active Directory Domain Services. Ponowne używanie istniejących wzorców inspekcji gwarantuje, że ta sama logika mogą być używane podczas analizowania zdarzenia. Inspekcja zabezpieczeń platformy Azure w usługach domenowych AD zawiera następujące kategorie zdarzeń.

| Nazwa kategorii inspekcji | Opis |
|:---|:---|
| Konto logowania|Przeprowadza inspekcję prób uwierzytelnienia dane konta na kontrolerze domeny lub na lokalnym Menedżera kont zabezpieczeń (SAM).</p>Logowanie i wylogowywanie, ustawienia zasad i zdarzenia śledzenia prób uzyskania dostępu do określonego komputera. Ustawienia i zdarzenia w tej kategorii należy skoncentrować się na konta bazy danych, który jest używany. Ta kategoria zawiera następujące podkategorie:<ul><li>[Inspekcja weryfikacji poświadczeń](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[Inspekcja usługi uwierzytelniania Kerberos](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[Inspekcja operacji biletów usługi Kerberos](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[Inspekcja innych zdarzeń logowania/wylogowania](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| Zarządzanie kontami|Inspekcje zmienia się na konta użytkowników i komputerów i grup. Ta kategoria zawiera następujące podkategorie:<ul><li>[Inspekcja zarządzania grupami aplikacji](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[Inspekcja zarządzania kontami komputerów](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[Inspekcja zarządzania grupami dystrybucyjnymi](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[Przeprowadź inspekcję zarządzania kontami, inne](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[Inspekcja zarządzania grupami zabezpieczeń](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[Inspekcja zarządzania kontami użytkowników](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| Szczegóły śledzenia|Działania inspekcji poszczególnych aplikacji i użytkowników na tym komputerze i zrozumienie sposobu korzystania z komputera. Ta kategoria zawiera następujące podkategorie:<ul><li>[Inspekcja działań DPAPI](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[Inspekcja aktywności PNP](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[Tworzenie procesu inspekcji](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[Inspekcja kończenia procesu](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[Inspekcja zdarzeń RPC](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| Dostęp do usług katalogowych|Inspekcje podejmie próbę uzyskania dostępu i modyfikowania obiektów w usługach domenowych w usłudze Active Directory (AD DS). Inspekcja, te zdarzenia są rejestrowane tylko na kontrolerach domeny. Ta kategoria zawiera następujące podkategorie:<ul><li>[Inspekcja szczegółowej replikacji usługi katalogowej](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[Przeprowadź inspekcję dostępu do usługi katalogowej](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[Inspekcja zmian usługi katalogowej](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[Inspekcja replikacji usługi katalogowej](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| Logowanie i wylogowywanie|Przeprowadza inspekcję prób logowania na komputerze interaktywnie lub za pośrednictwem sieci. Zdarzenia te są przydatne do śledzenia działań użytkownika oraz identyfikowanie potencjalnych ataków na zasoby sieciowe. Ta kategoria zawiera następujące podkategorie:<ul><li>[Inspekcja blokady konta](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[Inspekcja oświadczenia użytkownika i urządzenia](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[Tryb rozszerzony protokołu IPsec inspekcji](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[Członkostwo w grupie inspekcji](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[Inspekcja trybu głównego protokołu IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[Inspekcja trybu szybkiego protokołu IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[Inspekcja wylogowania](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logoff)</li><li>[Inspekcja logowania](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logon)</li><li>[Inspekcja serwera zasad sieciowych](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[Inspekcja innych zdarzeń logowania/wylogowania](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[Inspekcja logowania specjalnego](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|Dostęp do obiektów| Przeprowadza inspekcję prób uzyskania dostępu do określonych obiektów lub typów obiektów w sieci lub na komputerze. Ta kategoria zawiera następujące podkategorie:<ul><li>[Wygenerowano aplikację inspekcji](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[Inspekcja usług certyfikacji](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[Inspekcja szczegółowego udziału plików](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[Inspekcja udziału plików](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-share)</li><li>[Przeprowadź inspekcję systemu plików](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-system)</li><li>[Inspekcja połączenia platformy filtrowania](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[Inspekcja porzucania pakietów platformy filtrowania](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[Inspekcja manipulowania dojściem](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[Inspekcja obiektu jądra](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[Inspekcja innych zdarzeń dostępu do obiektu](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[Inspekcja rejestru](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-registry)</li><li>[Inspekcja magazynu wymiennego](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[Inspekcja SAM](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sam)</li><li>[Przeprowadź inspekcję przemieszczania centralnych zasad dostępu](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|Zmiany zasad|Inspekcji zmian zasad zabezpieczeń ważne do systemu lokalnego lub w sieci. Zasady zwykle są wyznaczane przez administratorów, które ułatwia zasobów bezpiecznej sieci. Monitorowanie zmian i próby zmiany tych zasad może być istotnym elementem zarządzania zabezpieczeniami sieci. Ta kategoria zawiera następujące podkategorie:<ul><li>[Inspekcja zmiany zasad inspekcji](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[Inspekcja zmiany zasad uwierzytelniania](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[Inspekcja zmiany zasad autoryzacji](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[Inspekcja zmiany zasad platformy filtrowania](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[Inspekcja zmiany zasad poziomie reguł MPSSVC](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[Inne zmiany zasad inspekcji](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|Użycie uprawnień| Przeprowadza inspekcję użycia niektórych uprawnień na jednym lub kilku systemów. Ta kategoria zawiera następujące podkategorie:<ul><li>[Inspekcja użycia zwykłych uprawnień](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[Inspekcja użycia poufnych uprawnień](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[Inspekcja innych zdarzeń użycia uprawnień](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|System| Inspekcji poziom systemu zmiany na komputerze, nie są uwzględnione w innych kategoriach, które dotyczą potencjalne zabezpieczeń. Ta kategoria zawiera następujące podkategorie:<ul><li>[Audit IPsec Driver](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[Inspekcja innych zdarzeń systemowych](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[Inspekcja zmian stanu zabezpieczeń](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[Inspekcja rozszerzenia systemu zabezpieczeń](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[Inspekcja integralności systemu](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>Identyfikatory zdarzeń według kategorii
 Inspekcji usługi Azure AD Domain Services zabezpieczeń rejestruje następujące identyfikatory zdarzeń, gdy określonej akcji wyzwala zdarzenie inspekcji.

| Nazwa kategorii zdarzenia | Identyfikatory zdarzeń |
|:---|:---|
|Zabezpieczenia logowania konta|4767, 4774, 4775, 4776, 4777|
|Zabezpieczenia zarządzania konta|4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4730, 4731, 4732, 4733, 4734, 4735, 4737, 4738, 4740, 4741, 4742, 4743, 4754, 4755, 4756, 4757, 4758, 4764, 4765, 4766, 4780, 4781, 4782, 4793, 4798, 4799, 5376, 5377|
|Szczegóły śledzenia zabezpieczeń|Brak|
|Zabezpieczenia dostępu do usługi Katalogowej|5136, 5137, 5138, 5139, 5141|
|Logowanie i wylogowywanie zabezpieczeń|4624, 4625, 4634, 4647, 4648, 4672, 4675, 4964|
|Zabezpieczenia dostępu do obiektu|Brak|
|Zasady zmian zabezpieczeń|4670, 4703, 4704, 4705, 4706, 4707, 4713, 4715, 4716, 4717, 4718, 4719, 4739, 4864, 4865, 4866, 4867, 4904, 4906, 4911, 4912|
|Korzystanie z uprawnień zabezpieczeń|4985|
|Zabezpieczenia systemu|4612, 4621|

## <a name="enable-security-audit-events"></a>Włącz zdarzenia inspekcji zabezpieczeń
Poniższe wskazówki ułatwiają zasubskrybować zdarzeń inspekcji zabezpieczeń usługi Azure AD Domain Services.

> [!IMPORTANT]
> Inspekcja zabezpieczeń w usłudze Azure AD Domain Services nie są wstecznego. Nie jest możliwe, można pobrać zdarzenia z ostatnich lub odtwarzać ponownie zdarzenia z przeszłości. Usługę można wysyłać tylko zdarzenia, które wystąpiły po jego włączeniu.
>

### <a name="choose-the-target-resource"></a>Wybierz zasób docelowy
Dla Twojej inspekcji zabezpieczeń, można użyć dowolnej kombinacji usługi Azure Storage, Azure Event Hubs lub obszary robocze usługi Azure Log Analytics jako zasób docelowy. Należy wziąć pod uwagę w poniższej tabeli zostanie najlepszy zasób dla przypadków użycia.

> [!IMPORTANT]
> Musisz utworzyć zasób docelowy, przed włączeniem inspekcji zabezpieczeń w usługach domenowych Azure AD.
>

| Zasób docelowy | Scenariusz |
|:---|:---|
|Azure Storage|Należy rozważyć użycie tego obiektu docelowego po podstawowego wymaganego do przechowywania zdarzeń inspekcji zabezpieczeń w celu jego archiwizacji. Inne obiekty docelowe mogą służyć do celów archiwizacji; Jednak te obiekty docelowe zapewniają większe możliwości niż podstawowy potrzeb archiwizacji. Aby utworzyć konto usługi Azure Storage, wykonaj [Tworzenie konta magazynu.](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal#create-a-storage-account-1)|
|Azure Event Hubs|Należy rozważyć użycie tego obiektu docelowego po podstawowego wymaganego na udostępnianie zdarzeń inspekcji zabezpieczeń dodatkowego oprogramowania, takie jak oprogramowanie do analizy danych lub oprogramowania zabezpieczającego, informacji i zdarzeń zarządzania (SIEM). Aby utworzyć Centrum zdarzeń, wykonaj [Szybki Start: Tworzenie Centrum zdarzeń za pomocą witryny Azure portal.](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)|
|Azure Log Analytics Workspace|Należy rozważyć użycie tego obiektu docelowego po podstawowego wymaganego do analizowania i przejrzyj bezpośrednio bezpiecznego inspekcji w witrynie Azure portal.  Aby utworzyć obszar roboczy usługi Log Analytics, wykonaj [Utwórz obszar roboczy usługi Log Analytics w witrynie Azure portal.](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)|

## <a name="using-the-azure-portal-to-enable-security-audit-events"></a>Zdarzenia inspekcji zabezpieczeń przy użyciu witryny Azure portal 
1. Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.  W witrynie Azure portal kliknij pozycję wszystkie usługi. Na liście zasobów wpisz **domeny**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Kliknij przycisk **usług domenowych Azure AD**.
2. Kliknij wystąpienie usług domenowych Azure AD z listy.
3. Kliknij przycisk **ustawienia diagnostyczne (wersja zapoznawcza)** z listy akcji po lewej stronie.</p>
![Akcja ustawienie diagnostyczne](./media/security-audit-events/diagnostic-settings-action.png)
4. Wpisz nazwę konfiguracji diagnostycznych (**inspekcji aadds** jako przykład).</p>
![Strona ustawień diagnostycznych](./media/security-audit-events/diagnostic-settings-page.png)
5. Zaznacz odpowiednie pole wyboru obok zasobów docelowych, które ma być używany do zdarzeń inspekcji zabezpieczeń.
    > [!NOTE]
    > Zasoby docelowe nie można utworzyć na tej stronie.
    >
    
    **Usługa Azure storage:**</p>
    Wybierz **Zarchiwizuj na koncie magazynu**. Kliknij pozycję **Konfiguruj**. Wybierz **subskrypcji** i **konta magazynu** chcesz użyć do archiwizacji zdarzeń inspekcji zabezpieczeń. Kliknij przycisk **OK**.</p>
    
    ![ustawienia diagnostyczne magazynu](./media/security-audit-events/diag-settings-storage.png)
    
    **Usługi Azure event hubs:**</p>
    Wybierz **Stream do usługi event hub**. Kliknij pozycję **Konfiguruj**. W **strony Centrum zdarzeń wybierz**, wybierz opcję **subskrypcji** używany do tworzenia Centrum zdarzeń. Następnie wybierz pozycję **przestrzeń nazw Centrum zdarzeń**, **nazwę Centrum zdarzeń**, i **Nazwa zasad Centrum zdarzeń**. Kliknij przycisk **OK**.</p>
    ![Ustawienia Centrum zdarzeń diagnostycznych](./media/security-audit-events/diag-settings-eventhub.png)
    
    **Usługa Azure Log Analytics obszary robocze:**</p>
    Wybierz **wysyłanie do usługi Log Analytics**. Wybierz **subskrypcji** i **obszar roboczy usługi Log Analytics** używany do przechowywania zdarzeń inspekcji zabezpieczeń.</p>
    ![ustawienia diagnostyczne obszaru roboczego](./media/security-audit-events/diag-settings-log-analytics.png)

6. Wybierz kategorie dziennika, który chcesz dołączyć zasobu określonego docelowego. Jeśli używasz konta magazynu, można skonfigurować zasady przechowywania.

    > [!NOTE]
    > Można wybrać kategorie dziennika dla każdego zasobu docelowego w ramach jednej konfiguracji. Dzięki temu można wybrać, który rejestruje kategorie, które chcesz zachować dla usługi Log Analytics i która rejestruje kategorie chcesz zarchiwizować.
    >

7. Kliknij przycisk **Zapisz** aby zatwierdzić zmiany. Zasoby docelowe otrzymają zdarzeń inspekcji zabezpieczeń usług domenowych Azure AD wkrótce po zapisaniu konfigurację.

## <a name="using-azure-powershell-to-enable-security-audit-events"></a>Przy użyciu programu Azure PowerShell w celu udostępnienia zdarzeń inspekcji zabezpieczeń
 
### <a name="prerequisites"></a>Wymagania wstępne

Postępuj zgodnie z instrukcjami w artykule, aby [Instalowanie modułu Azure PowerShell i nawiązać połączenie z subskrypcją platformy Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="enable-security-audits"></a>Włączanie inspekcji zabezpieczeń

1. Uwierzytelnianie na Azure Resource Manager dla odpowiedniego dzierżawy i subskrypcji za pomocą **Connect AzAccount** polecenia cmdlet programu Azure PowerShell.
2. Utwórz zasób docelowy dla zabezpieczeń zdarzeń inspekcji.</p>
    **Usługa Azure storage:**</p>
    Postępuj zgodnie z [Tworzenie konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-powershell) do utworzenia konta magazynu.</p>
    **Usługi Azure event hubs:**</p>
    Postępuj zgodnie z [Szybki Start: Tworzenie Centrum zdarzeń za pomocą programu Azure PowerShell](https://docs.microsoft.com/azure/event-hubs/event-hubs-quickstart-powershell) do tworzenia Centrum zdarzeń. Również może być konieczne użycie [New AzEventHubAuthorizationRule](https://docs.microsoft.com/powershell/module/az.eventhub/new-azeventhubauthorizationrule?view=azps-2.3.2) polecenia cmdlet programu Azure PowerShell, aby utworzyć regułę autoryzacji, aby umożliwić uprawnień w usługach domenowych AD w usłudze Active Directory do Centrum zdarzeń **przestrzeni nazw**. Reguła autoryzacji musi zawierać **Zarządzaj**, **nasłuchiwania**, i **wysyłania** praw.
    > [!IMPORTANT]
    > Upewnij się, że w przypadku ustawienia reguły autoryzacji na przestrzeń nazw Centrum zdarzeń i nie Centrum zdarzeń.
       
    </p>
    
    **Usługa Azure Log Analytics obszary robocze:**</p>
    Postępuj zgodnie z [Utwórz obszar roboczy usługi Log Analytics przy użyciu programu Azure PowerShell](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace-posh) do utworzenia obszaru roboczego.
3. Pobierz identyfikator zasobu dla swojego wystąpienia usługi Azure AD Domain Services. W otwartym, uwierzytelniony konsoli środowiska Windows PowerShell wpisz następujące polecenie. Użyj **$aadds. ResourceId** zmiennej jako parametru dla Identyfikatora zasobu usług domenowych Azure AD dla poleceń cmdlet w przyszłości.
    ```powershell
    $aadds = Get-AzResource -name aaddsDomainName
    ``` 
4. Użyj **AzDiagnosticSetting zestaw** polecenia cmdlet, aby skonfigurować ustawienia diagnostyki Azure do użycia zasobu docelowego dla zdarzeń inspekcji zabezpieczeń usługi Azure AD Domain Services. W przykładach poniżej zmiennej $aadds. ResourceId reprezentuje identyfikator zasobu wystąpienia usługi Azure AD Domain Services (zobacz krok 3).</p>
    **Usługa Azure storage:**
    ```powershell
    Set-AzDiagnosticSetting `
    -ResourceId $aadds.ResourceId` 
    -StorageAccountId storageAccountId `
    -Enabled $true
    ```
    Zastąp *storageAccountId* identyfikatorem konta usługi storage</p>
    
    **Usługi Azure event hubs:**
    ```powershell
    Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId ` 
    -EventHubName eventHubName `
    -EventHubAuthorizationRuleId eventHubRuleId `
    -Enabled $true
    ```
    Zastąp *eventHubName* nazwą Centrum zdarzeń. Zastąp *eventHubRuleId* za pomocą Identyfikatora reguły autoryzacji została wcześniej utworzona.</p>
    
    **Usługa Azure Log Analytics obszary robocze:**
    ```powershell
    Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId ` 
    -WorkspaceID workspaceId `
    -Enabled $true
    ```
    Zastąp *workspaceId* z Identyfikatorem obszaru roboczego usługi Log Analytics został wcześniej utworzony. 

## <a name="view-security-audit-events-using-azure-monitor"></a>Wyświetlanie zdarzeń inspekcji zabezpieczeń za pomocą usługi Azure Monitor
Obszary robocze usługi log Analytics umożliwia wyświetlanie i analizowanie zdarzeń inspekcji zabezpieczeń przy użyciu usługi Azure Monitor i język zapytania Kusto. Język zapytań jest przeznaczony do użytku tylko do odczytu, która oferuje duże możliwości analityczne ze składnią łatwych do zrozumienia.
Oto kilka zasobów, aby pomóc Ci rozpocząć pracę z językami zapytania Kusto.
* [Dokumentacja usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)
* [Rozpoczynanie pracy z usługą Log Analytics w usłudze Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)
* [Wprowadzenie do zapytań dzienników w usłudze Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)
* [Tworzenie i udostępnianie pulpitów nawigacyjnych z danymi usługi Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-logs-dashboards)

## <a name="sample-queries"></a>Przykładowe zapytania

### <a name="sample-query-1"></a>Przykładowe Zapytanie 1
Wszystkie konta blokady zdarzenia w ciągu ostatnich siedmiu dni.
```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>Przykładowe zapytanie 2
Wszystkie konta blokady zdarzenia (4740) od 26 czerwca 2019, 9: 00 i północ 1 lipca 2019 r, sortowane rosnąco daty i godziny.
```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2019-06-26 09:00) and TimeGenerated <= datetime(2019-07-01) 
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>Przykładowe zapytanie 3
Konto dziennika zdarzeń siedem dni (od teraz) dla konta o nazwie użytkownika.
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>Przykładowe zapytanie 4
Zdarzeń logowania na koncie użytkownika, który próbowano zalogować się przy użyciu nieprawidłowego hasła (0xC0000006a) o nazwie siedem dni od teraz dla konta.
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>Przykładowe zapytanie 5
Zdarzenia logowania na kontach siedem dni od teraz dla konta o nazwie użytkownik próbował się zalogować, gdy konto zostało zablokowane (0xC0000234).
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>Przykładowe zapytanie 6
Liczba zdarzeń logowania na koncie siedem dni od teraz dla wszystkich Zaloguj się w przypadku prób ataków, które wystąpiły dla wszystkich użytkowników blokady.
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="related-content"></a>Powiązana zawartość
* [Omówienie](https://docs.microsoft.com/azure/kusto/query/) Kusto języka zapytań.
* [Samouczek Kusto](https://docs.microsoft.com/azure/kusto/query/tutorial) na zapoznanie się z podstawowe informacje o zapytań.
* [Przykładowe zapytania](https://docs.microsoft.com/azure/kusto/query/samples) ułatwiające dowiesz się, zobacz swoje dane na nowe sposoby.
* Kusto [najlepsze praktyki](https://docs.microsoft.com/azure/kusto/query/best-practices) — zoptymalizowanie zapytań w celu osiągnięcia sukcesu.














 
