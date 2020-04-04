---
title: Wdrażanie lokalnego programu Azure AD Password Protection
description: Dowiedz się, jak planować i wdrażać ochronę hasłem usługi Azure AD w lokalnym środowisku usług domenowych Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ac9b76dd8d3c950b14f6d7b331f15647427ac89
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652736"
---
# <a name="plan-and-deploy-on-premises-azure-active-directory-password-protection"></a>Planowanie i wdrażanie lokalnego programu Ochrony hasłem usługi Azure Active Directory

Użytkownicy często tworzą hasła, które używają typowych lokalnych słów, takich jak szkoła, drużyna sportowa lub znana osoba. Te hasła są łatwe do odgadnięcia i słabe przed atakami opartymi na słowniku. Aby wymusić silne hasła w organizacji, ochrona hasłem usługi Azure Active Directory (Azure AD) zapewnia globalną i niestandardową listę haseł zbanowanych. Żądanie zmiany hasła kończy się niepowodzeniem, jeśli na tej liście zablokowanych haseł znajduje się dopasowanie.

Aby chronić lokalne środowisko usług domenowych Active Directory (AD DS), można zainstalować i skonfigurować usługę Azure AD Password Protection do pracy z kontrolerem domeny on-prem. W tym artykule pokazano, jak zainstalować i zarejestrować usługę proxy usługi Azure AD Password Protection i agenta kontrolera domeny ochrony haseł usługi Azure AD w środowisku lokalnym.

Aby uzyskać więcej informacji na temat działania ochrony hasłem usługi Azure AD w środowisku lokalnym, zobacz [Jak wymusić ochronę hasłem usługi Azure AD dla usługi Windows Server Active Directory](concept-password-ban-bad-on-premises.md).

## <a name="deployment-strategy"></a>Strategia wdrażania

Na poniższym diagramie pokazano, jak podstawowe składniki usługi Azure AD Password Protection współpracują ze sobą w lokalnym środowisku usługi Active Directory:

![Współpraca składników usługi Azure AD password protection](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

Przed wdrożeniem oprogramowania warto zapoznać się z jego działaniem. Aby uzyskać więcej informacji, zobacz [Omówienie koncepcyjne ochrony hasłem usługi Azure AD](concept-password-ban-bad-on-premises.md).

Zaleca się uruchomienie wdrożeń w trybie *inspekcji.* Tryb inspekcji jest domyślnym ustawieniem początkowym, w którym hasła mogą być nadal ustawiane. Hasła, które zostaną zablokowane są rejestrowane w dzienniku zdarzeń. Po wdrożeniu serwerów proxy i agentów kontrolera domeny w trybie inspekcji należy monitorować wpływ zasad haseł na użytkowników po wymuszaniu zasad.

Na etapie audytu wiele organizacji uważa, że mają zastosowanie następujące sytuacje:

* Muszą udoskonalić istniejące procesy operacyjne, aby używać bezpieczniejszych haseł.
* Użytkownicy często używają niezabezpieczonych haseł.
* Muszą informować użytkowników o nadchodzącej zmianie w egzekwowaniu zabezpieczeń, możliwym wpływie na nich i o tym, jak wybrać bezpieczniejsze hasła.

Istnieje również możliwość silniejszego sprawdzania poprawności haseł, aby wpłynąć na istniejącą automatyzację wdrażania kontrolera domeny usługi Active Directory. Zaleca się, aby co najmniej jedna promocja kontrolera domeny i jedna degradacja kontrolera domeny wystąpiły podczas oceny okresu inspekcji, aby pomóc w wykryciu takich problemów. Aby uzyskać więcej informacji zobacz następujące artykuły:

* [Ntdsutil.exe nie może ustawić słabego hasła trybu naprawy usług katalogowych](howto-password-ban-bad-on-premises-troubleshoot.md#ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [Promocja replik kontrolera domeny kończy się niepowodzeniem z powodu słabego hasła trybu naprawy usług katalogowych](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [Obniżenie poziomu kontrolera domeny kończy się niepowodzeniem z powodu słabego hasła lokalnego administratora](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

Po uruchomieniu tej funkcji w trybie inspekcji przez rozsądny okres można przełączyć konfigurację z *Inspekcja* na *Wymuszanie,* aby wymagać bezpieczniejszych haseł. Dodatkowe monitorowanie w tym czasie jest dobrym pomysłem.

### <a name="multiple-forest-considerations"></a>Wiele zagadnień dotyczących lasu

Nie ma żadnych dodatkowych wymagań dotyczących wdrażania ochrony hasłem usługi Azure AD w wielu lasach.

Każdy las jest niezależnie skonfigurowany, zgodnie z opisem w poniższej sekcji, aby [wdrożyć ochronę hasłem usługi Azure AD na przedm.](#download-required-software) Każdy serwer proxy usługi Azure AD Password Protection może obsługiwać tylko kontrolery domeny z lasu, do który jest przyłączony.

Oprogramowanie ochrony hasłem usługi Azure AD w dowolnym lesie nie jest świadome oprogramowania do ochrony hasłem, które jest wdrażane w innych lasach, niezależnie od konfiguracji zaufania usługi Active Directory.

### <a name="read-only-domain-controller-considerations"></a>Zagadnienia dotyczące kontrolera domeny tylko do odczytu

Zmiany hasła lub ustawić zdarzenia nie są przetwarzane i utrwalone na kontrolerach domeny tylko do odczytu (RODC). Zamiast tego są one przekazywane do zapisywalnych kontrolerów domeny. Nie trzeba instalować oprogramowania agenta kontrolera domeny ochrony haseł usługi Azure AD na kontrolerze RODC.

Ponadto nie jest obsługiwana do uruchamiania usługi proxy usługi Azure AD Password Protection na kontrolerze domeny tylko do odczytu.

### <a name="high-availability-considerations"></a>Wysokie kwestie dostępności

Głównym problemem ochrony hasłem jest dostępność serwerów proxy usługi Azure AD Password Protection, gdy kontrolery domeny w lesie próbują pobrać nowe zasady lub inne dane z platformy Azure. Każdy agent kontrolera kontrolera domeny ochrony haseł usługi Azure AD używa prostego algorytmu w stylu okrężnego przy podejmowaniu decyzji o wywoływaniu serwera proxy. Agent pomija serwery proxy, które nie odpowiadają.

W przypadku większości w pełni połączonych wdrożeń usługi Active Directory, które mają w dobrej kondycji replikację zarówno katalogu, jak i stanu folderu sysvol, dwa serwery proxy usługi Azure AD Password Protection wystarczą, aby zapewnić dostępność. Ta konfiguracja powoduje terminowe pobieranie nowych zasad i innych danych. W razie potrzeby można wdrożyć dodatkowe serwery proxy usługi Azure AD Password Protection.

Projekt oprogramowania agenta kontrolera domeny ochrony haseł usługi Azure AD ogranicza zwykłe problemy, które są skojarzone z wysoką dostępnością. Agent kontrolera domeny ochrony haseł usługi Azure AD przechowuje lokalną pamięć podręczną ostatnio pobranych zasad haseł. Nawet jeśli wszystkie zarejestrowane serwery proxy stają się niedostępne, agenci kontrolera domeny ochrony haseł usługi Azure AD nadal wymuszają swoje zasady haseł w pamięci podręcznej.

Rozsądna częstotliwość aktualizacji dla zasad haseł w dużym wdrożeniu jest zwykle dni, nie godziny lub mniej. Tak więc krótkie awarie serwerów proxy nie mają istotnego wpływu na ochronę hasłem usługi Azure AD.

## <a name="deployment-requirements"></a>Wymagania dotyczące wdrażania

Aby uzyskać informacje na temat licencjonowania, zobacz [Wymagania licencyjne dotyczące usługi Azure AD Password Protection](concept-password-ban-bad.md#license-requirements).

Obowiązują następujące podstawowe wymagania:

* Wszystkie maszyny, w tym kontrolery domeny, na których są zainstalowane składniki ochrony hasłem usługi Azure AD, muszą mieć zainstalowany uniwersalny czas wykonywania C.
    * Środowisko wykonawcze można uzyskać, upewniając się, że masz wszystkie aktualizacje z witryny Windows Update. Lub można go uzyskać w pakiecie aktualizacji specyficzne dla systemu operacyjnego. Aby uzyskać więcej informacji, zobacz [Aktualizacja uniwersalnego środowiska wykonawczego C w systemie Windows](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows).
* Do zarejestrowania lasu usługi Active Directory w usłudze Azure AD potrzebne jest konto z uprawnieniami administratora domeny usługi Active Directory w domenie głównej lasu.
* Usługa dystrybucji kluczy musi być włączona na wszystkich kontrolerach domeny w domenie z systemem Windows Server 2012. Domyślnie ta usługa jest włączona za pomocą ręcznego uruchamiania wyzwalacza.
* Łączność sieciowa musi istnieć między co najmniej jednym kontrolerem domeny w każdej domenie a co najmniej jednym serwerem obsługującym usługę proxy dla ochrony hasłem usługi Azure AD. Ta łączność musi umożliwiać kontrolerowi domeny dostęp do portu mappera punktu końcowego RPC 135 i portu serwera RPC w usłudze proxy.
    * Domyślnie port serwera RPC jest dynamicznym portem RPC, ale można go skonfigurować do [używania portu statycznego](#static).
* Wszystkie komputery, na których zostanie zainstalowana usługa Azure AD Password Protection Proxy, muszą mieć dostęp do sieci następujących punktów końcowych:

    |**Punktu końcowego**|**Przeznaczenie**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Żądania uwierzytelniania|
    |`https://enterpriseregistration.windows.net`|Funkcja ochrony hasłem usługi Azure AD|

### <a name="azure-ad-password-protection-dc-agent"></a>Agent kontrolera domeny ochrony hasłem usługi Azure AD

Następujące wymagania dotyczą agenta kontrolera domeny ochrony haseł usługi Azure AD:

* Wszystkie komputery, na których zostanie zainstalowane oprogramowanie agenta kontrolera domeny ochrony haseł usługi Azure AD, muszą działać w systemie Windows Server 2012 lub nowszym.
    * Domena lub las usługi Active Directory nie musi znajdować się na poziomie funkcjonalności domeny systemu Windows Server 2012 (DFL) ani na poziomie funkcjonalności lasu (FFL). Jak wspomniano w [zasadach projektowania,](concept-password-ban-bad-on-premises.md#design-principles)nie ma minimalnego DFL lub FFL wymagane dla agenta kontrolera domeny lub oprogramowania proxy do uruchomienia.
* Wszystkie maszyny z agentem kontrolera kontrolera azure ad password protection muszą mieć zainstalowany plik .NET 4.5.
* Każda domena usługi Active Directory, która uruchamia usługę agenta kontrolera domeny ochrony haseł usługi Azure AD, musi używać replikacji rozproszonego systemu plików (DFSR) do replikacji sysvol.
   * Jeśli twoja domena nie jest jeszcze przy użyciu dfsr, należy przeprowadzić migrację przed zainstalowaniem usługi Azure AD Password Protection. Aby uzyskać więcej informacji, zobacz [Przewodnik migracji replikacji SYSVOL: Replikacja usługi FRS do systemu plików DFS](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

    > [!WARNING]
    > Oprogramowanie agenta kontrolera domeny ochrony haseł usługi Azure AD będzie obecnie instalowane na kontrolerach domeny w domenach, które nadal używają usługi FRS (poprzednia technologia do DFSR) do replikacji sysvol, ale oprogramowanie NIE będzie działać poprawnie w tym środowisku.
    >
    > Dodatkowe negatywne skutki uboczne obejmują poszczególne pliki, które nie są replikowane, oraz procedury przywracania sysvol, które wydają się zakończyć się pomyślnie, ale po cichu nie replikują wszystkich plików.
    >
    > Migruj domenę, aby jak najszybciej użyć usługi DFSR, zarówno w celu uzyskania nieodłącznych korzyści związanych z usługą DFSR, jak i odblokowania wdrożenia usługi Azure AD Password Protection. Przyszłe wersje oprogramowania zostaną automatycznie wyłączone podczas uruchamiania w domenie, która nadal korzysta z usługi FRS.

### <a name="azure-ad-password-protection-proxy-service"></a>Usługa proxy usługi Azure AD Password Protection

Następujące wymagania dotyczą usługi proxy usługi Azure AD Password Protection:

* Wszystkie komputery, na których zostanie zainstalowana usługa proxy usługi Azure AD Password Protection, muszą działać w systemie Windows Server 2012 R2 lub nowszym.

    > [!NOTE]
    > Wdrożenie usługi proxy usługi Azure AD Password Protection jest obowiązkowym wymaganiem wdrażania ochrony hasłem usługi Azure AD, nawet jeśli kontroler domeny może mieć wychodzącą bezpośrednią łączność z Internetem.

* Wszystkie komputery, na których zostanie zainstalowana usługa proxy usługi Azure AD Password Protection, muszą mieć zainstalowany plik .NET 4.7.
    * Program .NET 4.7 powinien być już zainstalowany na w pełni zaktualizowanym systemie Windows Server. Jeśli to konieczne, pobierz i uruchom instalator znajdujący się w [instalatorze trybu offline .NET Framework 4.7 dla systemu Windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
* Wszystkie maszyny obsługujące usługę proxy usługi Azure AD Password Protection muszą być skonfigurowane w taki sposób, aby przyznać kontrolerom domeny możliwość logowania się do usługi proxy. Ta możliwość jest kontrolowana za pomocą przypisania uprawnień "Dostęp do tego komputera z sieci".
* Wszystkie maszyny obsługujące usługę proxy usługi Azure AD Password Protection muszą być skonfigurowane tak, aby zezwalały na ruch HTTP tls 1.2.
* Konto *administratora globalnego* do rejestrowania usługi proxy i lasu usługi Azure AD password protection w usłudze Azure AD.
* Dostęp do sieci musi być włączony dla zestawu portów i adresów URL określonych w [procedurach konfiguracji środowiska serwera proxy aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment).

### <a name="microsoft-azure-ad-connect-agent-updater-prerequisites"></a>Wymagania wstępne programu aktualizacji agenta usługi Microsoft Azure AD Connect

Usługa Microsoft Azure AD Connect Agent Updater jest instalowana obok usługi Serwera proxy ochrony hasłem usługi Azure AD. Dodatkowa konfiguracja jest wymagana, aby usługa Microsoft Azure AD Connect Agent Updater mogła działać:

* Jeśli w środowisku jest używany serwer proxy HTTP, postępuj zgodnie z wytycznymi określonymi w [polu Praca z istniejącymi lokalnymi serwerami proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers).
* Usługa microsoft azure ad connect agent updater wymaga również kroki TLS 1.2 określone w [wymaganiach protokołu TLS](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#tls-requirements).

> [!WARNING]
> Serwer proxy usługi Azure AD Password Protection i serwer proxy aplikacji usługi Azure AD instalują różne wersje usługi Microsoft Azure AD Connect Agent Updater, dlatego instrukcje odnoszą się do zawartości serwera proxy aplikacji. Te różne wersje są niezgodne, gdy są instalowane obok siebie, więc nie zaleca się instalowania serwera proxy ochrony hasłem usługi Azure AD i serwera proxy aplikacji na tym samym komputerze.

## <a name="download-required-software"></a>Pobierz wymagane oprogramowanie

Istnieją dwa wymagane instalatory dla lokalnego wdrożenia usługi Azure AD Password Protection:

* Agent kontrolera kontrolera ochrony hasłem usługi Azure AD (*AzureADPasswordProtectionDCAgentSetup.msi*)
* Serwer proxy usługi Azure AD Password Protection *(AzureADPasswordProtectionProxySetup.exe)*

Pobierz oba instalatory z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="install-and-configure-the-proxy-service"></a>Instalowanie i konfigurowanie usługi proxy

Usługa serwera proxy usługi Azure AD Password Protection jest zazwyczaj na serwerze członkowskim w lokalnym środowisku usług AD DS. Po zainstalowaniu usługa proxy usługi Azure AD Password Protection komunikuje się z usługą Azure AD w celu obsługi kopii list haseł z zakazem dostępu do usługi Azure AD dla dzierżawy usługi Azure AD.

W następnej sekcji należy zainstalować agentów kontrolera domeny ochrony haseł usługi Azure AD na kontrolerach domeny w lokalnym środowisku usług AD DS. Ci agenci kontrolera domeny komunikują się z usługą proxy, aby uzyskać najnowsze listy zakazanych haseł do użycia podczas przetwarzania zdarzeń zmiany hasła w domenie.

Wybierz jeden lub więcej serwerów do obsługi usługi proxy usługi Azure AD Password Protection. W przypadku serwerów(ów) obowiązują następujące zagadnienia:

* Każda taka usługa może udostępniać tylko zasady haseł dla jednego lasu. Komputer-host musi być przyłączony do domeny w tym lesie. Domeny główne i podrzędne są obsługiwane. Potrzebna jest łączność sieciowa między co najmniej jednym kontrolerem domeny w każdej domenie lasu a komputerem ochrony hasłem.
* Usługę serwera proxy usługi Azure AD Password Protection można uruchomić na kontrolerze domeny do testowania, ale ten kontroler domeny wymaga łączności z Internetem. Ta łączność może być problemem bezpieczeństwa. Firma Microsoft zaleca tę konfigurację tylko do testowania.
* Zalecamy co najmniej dwa serwery proxy usługi Azure AD Password Protection w celu zapewnienia nadmiarowości, jak wspomniano w poprzedniej sekcji [dotyczącej zagadnień dotyczących wysokiej dostępności](#high-availability-considerations).
* Nie jest obsługiwana do uruchamiania usługi proxy usługi Azure AD Password Protection na kontrolerze domeny tylko do odczytu.

Aby zainstalować usługę proxy usługi Azure AD Password Protection, wykonaj następujące kroki:

1. Aby zainstalować usługę proxy usługi Azure `AzureADPasswordProtectionProxySetup.exe` AD Password Protection, uruchom instalator oprogramowania.

    Instalacja oprogramowania nie wymaga ponownego uruchomienia i może być zautomatyzowana przy użyciu standardowych procedur MSI, jak w poniższym przykładzie:
    
    ```console
    AzureADPasswordProtectionProxySetup.exe /quiet
    ```
    
    > [!NOTE]
    > Usługa Zapora systemu Windows musi `AzureADPasswordProtectionProxySetup.exe` być uruchomiona przed zainstalowaniem pakietu, aby uniknąć błędu instalacji.
    >
    > Jeśli Zapora systemu Windows jest skonfigurowana tak, aby nie była uruchamiana, obejście polega na tymczasowym włączeniu i uruchomieniu usługi Zapora podczas instalacji. Oprogramowanie proxy nie ma określonej zależności od Zapory systemu Windows po instalacji.
    >
    > Jeśli używasz zapory innej firmy, nadal musi być skonfigurowany tak, aby spełniał wymagania dotyczące wdrażania. Obejmują one umożliwienie dostępu przychodzącego do portu 135 i portu serwera RPC serwera proxy. Aby uzyskać więcej informacji, zobacz poprzednią sekcję dotyczącą [wymagań dotyczących wdrażania](#deployment-requirements).

1. Oprogramowanie proxy usługi Azure AD Password Protection zawiera `AzureADPasswordProtection`nowy moduł programu PowerShell, . W poniższych krokach uruchomi się różne polecenia cmdlet z tego modułu programu PowerShell.

    Aby użyć tego modułu, otwórz okno programu PowerShell jako administrator i zaimportuj nowy moduł w następujący sposób:
    
    ```powershell
    Import-Module AzureADPasswordProtection
    ```

1. Aby sprawdzić, czy usługa proxy usługi Azure AD Password Protection jest uruchomiona, użyj następującego polecenia programu PowerShell:

    ```powershell
    Get-Service AzureADPasswordProtectionProxy | fl
    ```

    Wynik powinien wyświetlać **stan** *uruchamiania*.

1. Usługa proxy jest uruchomiona na komputerze, ale nie ma poświadczeń do komunikowania się z usługą Azure AD. Zarejestruj serwer proxy usługi Azure AD Password `Register-AzureADPasswordProtectionProxy` Protection w usłudze Azure AD przy użyciu polecenia cmdlet.

    To polecenie cmdlet wymaga poświadczeń administratora globalnego dla dzierżawy platformy Azure. W domenie głównej lasu potrzebne są również lokalne uprawnienia administratora domeny usługi Active Directory. To polecenie cmdlet musi być również uruchamiane przy użyciu konta z uprawnieniami administratora lokalnego:

    Po tym poleceniu powiedzie się raz dla usługi proxy usługi Azure AD Password Protection, dodatkowe wywołania go zakończyć się pomyślnie, ale są niepotrzebne.

    Polecenie `Register-AzureADPasswordProtectionProxy` cmdlet obsługuje następujące trzy tryby uwierzytelniania. Pierwsze dwa tryby obsługują uwierzytelnianie wieloskładnikowe platformy Azure, ale trzeci tryb nie.

    > [!TIP]
    > Może wystąpić zauważalne opóźnienie przed zakończeniem po raz pierwszy, że to polecenie cmdlet jest uruchamiany dla określonej dzierżawy platformy Azure. O ile nie zostanie zgłoszona awaria, nie martw się o to opóźnienie.

     * Tryb uwierzytelniania interaktywnego:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Ten tryb nie działa w systemach operacyjnych Server Core. Zamiast tego należy użyć jednego z następujących trybów uwierzytelniania. Ponadto ten tryb może zakończyć się niepowodzeniem, jeśli włączona jest konfiguracja zwiększonych zabezpieczeń programu Internet Explorer. Obejście polega na wyłączeniu tej konfiguracji, zarejestrowaniu serwera proxy, a następnie ponownym włączeniu go.

     * Tryb uwierzytelniania kodu urządzenia:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        Po wyświetleniu monitu, po kliknięciu łącza, aby otworzyć przeglądarkę internetową i wprowadzić kod uwierzytelniania.

     * Tryb uwierzytelniania cichego (opartego na hasłach):

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Ten tryb kończy się niepowodzeniem, jeśli dla Twojego konta jest wymagane uwierzytelnianie wieloskładnikowe platformy Azure. W takim przypadku należy użyć jednego z dwóch poprzednich trybów uwierzytelniania lub zamiast tego użyć innego konta, które nie wymaga usługi MFA.
        >
        > Można również zobaczyć usługi MFA wymagane, jeśli rejestracja urządzenia platformy Azure (który jest używany w ramach okładek przez usługę Azure AD Password Protection) został skonfigurowany do globalnie wymagają usługi MFA. Aby obejść to wymaganie, można użyć innego konta, które obsługuje usługi MFA z jednym z dwóch poprzednich trybów uwierzytelniania lub może również tymczasowo wyłączyć wymagania usługi Azure Device Registration MFA.
        >
        > Aby wprowadzić tę zmianę, wyszukaj i wybierz **pozycję Usługa Azure Active Directory** w witrynie Azure portal, a następnie wybierz pozycję Urządzenia > ustawienia **urządzeń**. Ustaw **wymagaj wieloczynnikowej auth do łączenia urządzeń** do *nr*. Pamiętaj, aby ponownie skonfigurować to ustawienie z powrotem na *Tak* po zakończeniu rejestracji.
        >
        > Zaleca się, że wymagania usługi MFA należy pominąć tylko do celów testowych.

    Obecnie nie trzeba określać *-ForestCredential* parametr, który jest zarezerwowany dla przyszłych funkcji.

    Rejestracja usługi proxy usługi Azure AD Password Protection jest konieczna tylko raz w okresie istnienia usługi. Następnie usługa proxy usługi Azure AD Password Protection automatycznie wykona wszelkie inne niezbędne czynności konserwacyjne.

1. Teraz zarejestruj lokalny las usługi Active Directory z poświadczeniami niezbędnymi `Register-AzureADPasswordProtectionForest` do komunikowania się z platformą Azure przy użyciu polecenia cmdlet programu PowerShell.

    > [!NOTE]
    > Jeśli w twoim środowisku jest zainstalowanych wiele serwerów proxy usługi Azure AD Password Protection, nie ma znaczenia, który serwer proxy jest używany do rejestrowania lasu.

    Polecenie cmdlet wymaga poświadczeń administratora globalnego dla dzierżawy platformy Azure. Należy również uruchomić to polecenie cmdlet przy użyciu konta z uprawnieniami administratora lokalnego. Wymaga również lokalnych uprawnień administratora usługi Active Directory Enterprise. Ten krok jest uruchamiany raz na las.

    Polecenie `Register-AzureADPasswordProtectionForest` cmdlet obsługuje następujące trzy tryby uwierzytelniania. Pierwsze dwa tryby obsługują uwierzytelnianie wieloskładnikowe platformy Azure, ale trzeci tryb nie.

    > [!TIP]
    > Może wystąpić zauważalne opóźnienie przed zakończeniem po raz pierwszy, że to polecenie cmdlet jest uruchamiany dla określonej dzierżawy platformy Azure. O ile nie zostanie zgłoszona awaria, nie martw się o to opóźnienie.

     * Tryb uwierzytelniania interaktywnego:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Ten tryb nie będzie działać w systemach operacyjnych Server Core. Zamiast tego użyj jednego z następujących dwóch trybów uwierzytelniania. Ponadto ten tryb może zakończyć się niepowodzeniem, jeśli włączona jest konfiguracja zwiększonych zabezpieczeń programu Internet Explorer. Obejście polega na wyłączeniu tej konfiguracji, zarejestrowaniu lasu, a następnie ponownym włączeniu go.  

     * Tryb uwierzytelniania kodu urządzenia:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        Po wyświetleniu monitu, po kliknięciu łącza, aby otworzyć przeglądarkę internetową i wprowadzić kod uwierzytelniania.

     * Tryb uwierzytelniania cichego (opartego na hasłach):

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Ten tryb kończy się niepowodzeniem, jeśli dla Twojego konta jest wymagane uwierzytelnianie wieloskładnikowe platformy Azure. W takim przypadku należy użyć jednego z dwóch poprzednich trybów uwierzytelniania lub zamiast tego użyć innego konta, które nie wymaga usługi MFA.
        >
        > Można również zobaczyć usługi MFA wymagane, jeśli rejestracja urządzenia platformy Azure (który jest używany w ramach okładek przez usługę Azure AD Password Protection) został skonfigurowany do globalnie wymagają usługi MFA. Aby obejść to wymaganie, można użyć innego konta, które obsługuje usługi MFA z jednym z dwóch poprzednich trybów uwierzytelniania lub może również tymczasowo wyłączyć wymagania usługi Azure Device Registration MFA.
        >
        > Aby wprowadzić tę zmianę, wyszukaj i wybierz **pozycję Usługa Azure Active Directory** w witrynie Azure portal, a następnie wybierz pozycję Urządzenia > ustawienia **urządzeń**. Ustaw **wymagaj wieloczynnikowej auth do łączenia urządzeń** do *nr*. Pamiętaj, aby ponownie skonfigurować to ustawienie z powrotem na *Tak* po zakończeniu rejestracji.
        >
        > Zaleca się, że wymagania usługi MFA należy pominąć tylko do celów testowych.

       Te przykłady powiodą się tylko wtedy, gdy aktualnie zalogowany użytkownik jest również administratorem domeny usługi Active Directory dla domeny głównej. Jeśli tak nie jest, można podać alternatywne poświadczenia domeny za pośrednictwem *-ForestCredential* parametru.

    Rejestracja lasu usługi Active Directory jest konieczna tylko raz w okresie istnienia lasu. Następnie agenci kontrolera domeny ochrony hasłem usługi Azure AD w lesie automatycznie wykonują wszelkie inne niezbędne czynności konserwacyjne. Po `Register-AzureADPasswordProtectionForest` pomyślnym uruchomieniu lasu dodatkowe wywołania polecenia cmdlet zakończyć się pomyślnie, ale są niepotrzebne.
    
    Aby `Register-AzureADPasswordProtectionForest` pomyślnie, co najmniej jeden kontroler domeny z systemem Windows Server 2012 lub nowszym musi być dostępny w domenie serwera proxy usługi Azure AD Password Protection. Oprogramowanie agenta kontrolera domeny ochrony haseł usługi Azure AD nie musi być instalowane na żadnych kontrolerach domeny przed tym krokiem.

### <a name="configure-the-proxy-service-to-communicate-through-an-http-proxy"></a>Konfigurowanie usługi proxy do komunikacji za pośrednictwem serwera proxy HTTP

Jeśli środowisko wymaga użycia określonego serwera proxy HTTP do komunikowania się z platformą Azure, skorzystaj z następujących kroków, aby skonfigurować usługę Azure AD Password Protection.

Utwórz plik *AzureADPasswordProtectionProxy.exe.config* w folderze. `%ProgramFiles%\Azure AD Password Protection Proxy\Service` Dołącz następującą zawartość:

   ```xml
   <configuration>
      <system.net>
         <defaultProxy enabled="true">
         <proxy bypassonlocal="true"
            proxyaddress="http://yourhttpproxy.com:8080" />
         </defaultProxy>
      </system.net>
   </configuration>
   ```

Jeśli serwer proxy HTTP wymaga uwierzytelniania, dodaj tag *useDefaultCredentials:*

   ```xml
   <configuration>
      <system.net>
         <defaultProxy enabled="true" useDefaultCredentials="true">
         <proxy bypassonlocal="true"
            proxyaddress="http://yourhttpproxy.com:8080" />
         </defaultProxy>
      </system.net>
   </configuration>
   ```

W obu przypadkach `http://yourhttpproxy.com:8080` zastąp adresem i portem określonego serwera proxy HTTP.

Jeśli serwer proxy HTTP jest skonfigurowany do używania zasad autoryzacji, należy udzielić dostępu do konta komputera usługi Active Directory na komputerze obsługującym usługę proxy w celu ochrony hasłem.

Zaleca się zatrzymanie i ponowne uruchomienie usługi proxy usługi Azure AD Password Protection po utworzeniu lub zaktualizowaniu pliku *AzureADPasswordProtectionProtectionProxy.exe.config.*

Usługa serwera proxy nie obsługuje używania określonych poświadczeń do łączenia się z serwerem proxy HTTP.

### <a name="configure-the-proxy-service-to-listen-on-a-specific-port"></a>Konfigurowanie usługi proxy do nasłuchiwania na określonym porcie

Oprogramowanie agenta kontrolera domeny ochrony haseł usługi Azure AD używa rpc za pomocą protokołu TCP do komunikowania się z usługą proxy. Domyślnie usługa proxy usługi Azure AD Password Protection nasłuchuje dowolnego dostępnego dynamicznego punktu końcowego RPC. Usługę można skonfigurować do nasłuchiwania na określonym porcie TCP, jeśli to konieczne ze względu na topologię sieci lub wymagania zapory w danym środowisku.

<a id="static" /></a>Aby skonfigurować usługę do uruchamiania w porcie statycznym, należy użyć polecenia cmdlet w `Set-AzureADPasswordProtectionProxyConfiguration` następujący sposób:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
```

> [!WARNING]
> Aby te zmiany zostały wprowadzone, należy zatrzymać i ponownie uruchomić usługę serwera proxy usługi Azure AD Password Protection.

Aby skonfigurować usługę do uruchamiania w porcie dynamicznym, należy użyć tej samej procedury, ale ustaw *StaticPort* z powrotem na zero:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
```

> [!WARNING]
> Aby te zmiany zostały wprowadzone, należy zatrzymać i ponownie uruchomić usługę serwera proxy usługi Azure AD Password Protection.

Usługa serwera proxy usługi Azure AD Password Protection wymaga ręcznego ponownego uruchomienia po każdej zmianie konfiguracji portu. Po dokonaniu tych zmian konfiguracji nie trzeba ponownie uruchamiać usługi agenta kontrolera domeny ochrony haseł usługi Azure AD na kontrolerach domeny.

Aby zbadać bieżącą konfigurację usługi, `Get-AzureADPasswordProtectionProxyConfiguration` użyj polecenia cmdlet, jak pokazano w poniższym przykładzie

```powershell
Get-AzureADPasswordProtectionProxyConfiguration | fl
```

Poniższe przykładowe dane wyjściowe pokazują, że usługa proxy usługi Azure AD Password Protection używa portu dynamicznego:

```output
ServiceName : AzureADPasswordProtectionProxy
DisplayName : Azure AD password protection Proxy
StaticPort  : 0
```

## <a name="install-the-dc-agent-service"></a>Instalowanie usługi agenta kontrolera domeny

Aby zainstalować usługę agenta kontrolera domeny `AzureADPasswordProtectionDCAgentSetup.msi` ochrony haseł usługi Azure AD, uruchom pakiet.

Instalację oprogramowania można zautomatyzować przy użyciu standardowych procedur MSI, jak pokazano w poniższym przykładzie:

```console
msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart
```

Flagę `/norestart` można pominąć, jeśli wolisz, aby instalator automatycznie ponownie uruchomić komputer.

Instalacja oprogramowania lub dezinstalacja wymaga ponownego uruchomienia. To wymaganie jest, ponieważ biblioteki DLL filtrowania haseł są ładowane lub zwalniane tylko przez ponowne uruchomienie.

Instalacja ochrony hasłem usługi Azure AD na prem została zakończona po zainstalowaniu oprogramowania agenta kontrolera domeny na kontrolerze domeny i ponownym uruchomieniu tego komputera. Żadna inna konfiguracja nie jest wymagana ani możliwa. Zdarzenia zmiany hasła względem kontrolerów domeny na przedsprzedaży używają skonfigurowanych list zablokowanych haseł z usługi Azure AD.

Aby włączyć ochronę hasłem usługi Azure AD w wersji wstępnej z witryny Azure portal lub skonfigurować niestandardowe hasła zbanowane, zobacz [Włączanie lokalnego programu Azure AD Password Protection](howto-password-ban-bad-on-premises-operations.md).

> [!TIP]
> Agent kontrolera domeny ochrony haseł usługi Azure AD można zainstalować na komputerze, który nie jest jeszcze kontrolerem domeny. W takim przypadku usługa uruchamia się i działa, ale pozostają nieaktywne, dopóki komputer nie zostanie awansowany do kontrolera domeny.

## <a name="upgrading-the-proxy-service"></a>Uaktualnianie usługi proxy

Usługa proxy usługi Azure AD Password Protection obsługuje automatyczne uaktualnianie. Automatyczne uaktualnianie korzysta z usługi Microsoft Azure AD Connect Agent Updater, która jest instalowana obok usługi proxy. Automatyczne uaktualnianie jest domyślnie włączone i może `Set-AzureADPasswordProtectionProxyConfiguration` być włączone lub wyłączone przy użyciu polecenia cmdlet.

Bieżące ustawienie można wyszukiwać `Get-AzureADPasswordProtectionProxyConfiguration` za pomocą polecenia cmdlet. Zaleca się, że ustawienie automatycznego uaktualniania jest zawsze włączone.

Polecenie `Get-AzureADPasswordProtectionProxy` cmdlet może służyć do wykonywania zapytań o wersję oprogramowania wszystkich aktualnie zainstalowanych serwerów proxy usługi Azure AD Password Protection w lesie.

### <a name="manual-upgrade-process"></a>Proces ręcznego uaktualniania

Ręczne uaktualnienie odbywa się po `AzureADPasswordProtectionProxySetup.exe` uruchomieniu najnowszej wersji instalatora oprogramowania. Najnowsza wersja oprogramowania jest dostępna w [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

Nie jest wymagane odinstalowanie bieżącej wersji usługi proxy usługi Azure AD Password Protection — instalator wykonuje uaktualnienie w miejscu. Podczas uaktualniania usługi proxy nie powinno być wymagane ponowne uruchomienie komputera. Uaktualnienie oprogramowania może być zautomatyzowane przy `AzureADPasswordProtectionProxySetup.exe /quiet`użyciu standardowych procedur MSI, takich jak .

## <a name="upgrading-the-dc-agent"></a>Uaktualnianie agenta kontrolera domeny

Gdy dostępna jest nowsza wersja oprogramowania agenta kontrolera domeny ochrony haseł usługi `AzureADPasswordProtectionDCAgentSetup.msi` Azure AD, uaktualnienie jest realizowane przez uruchomienie najnowszej wersji pakietu oprogramowania. Najnowsza wersja oprogramowania jest dostępna w [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

Nie jest wymagane odinstalowanie bieżącej wersji oprogramowania agenta kontrolera domeny — instalator wykonuje uaktualnienie w miejscu. Ponowne uruchomienie komputera jest zawsze wymagane podczas uaktualniania oprogramowania agenta kontrolera domeny — to wymaganie jest spowodowane podstawowym zachowaniem systemu Windows.

Uaktualnienie oprogramowania może być zautomatyzowane przy `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`użyciu standardowych procedur MSI, takich jak .

Możesz pominąć `/norestart` flagę, jeśli wolisz, aby instalator automatycznie ponownie uruchamiał komputer.

Polecenie `Get-AzureADPasswordProtectionDCAgent` cmdlet może służyć do wykonywania zapytań o wersję oprogramowania wszystkich aktualnie zainstalowanych agentów kontrolera domeny ochrony haseł usługi Azure AD w lesie.

## <a name="next-steps"></a>Następne kroki

Teraz, po zainstalowaniu usług, które są potrzebne dla ochrony hasłem usługi Azure AD na serwerach lokalnych, [włącz ochronę hasłem usługi Azure AD w witrynie Azure Portal,](howto-password-ban-bad-on-premises-operations.md) aby zakończyć wdrażanie.
