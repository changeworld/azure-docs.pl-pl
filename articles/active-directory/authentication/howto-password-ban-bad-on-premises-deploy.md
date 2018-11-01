---
title: Wdrażanie ochrony haseł usługi Azure AD w wersji zapoznawczej
description: Wdrażanie Podgląd ochrony haseł usługi Azure AD, tak aby zablokować błędnego hasła lokalnie
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 10/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 6599d634ec1e13715bdd34b6e8ab6fbd9f4f3e61
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/01/2018
ms.locfileid: "50743268"
---
# <a name="preview-deploy-azure-ad-password-protection"></a>Wersja zapoznawcza: Wdrażanie ochrona za pomocą hasła usługi Azure AD

|     |
| --- |
| Ochrony hasłem w usłudze Azure AD jest funkcją publicznej wersji zapoznawczej usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych usług Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Skoro mamy już zrozumienia [jak wymusić ochrona za pomocą hasła usługi Azure AD dla usługi Active Directory systemu Windows Server](concept-password-ban-bad-on-premises.md), następnym krokiem jest planowania i wykonywania wdrożenia.

## <a name="deployment-strategy"></a>Strategia wdrażania

Firma Microsoft sugeruje, że wszystkie wdrożenia rozruchu w trybie inspekcji. Tryb inspekcji jest ustawienie początkowej domyślne, których hasła mogą w dalszym ciągu można ustawić i dowolny, które zostałby zablokowany utworzyć wpisy w dzienniku zdarzeń. Gdy serwery proxy i agentów DC zostały w pełni wdrożone w trybie inspekcji, regularne monitorowanie ma się odbywać w celu ustalenia, jakie zasady haseł wpływu wymuszania miałby on użytkowników i środowiska, jeśli zasada była wymuszana.

Na etapie inspekcji znaleźć wiele organizacji:

* Należy poprawić istniejące procesy operacyjne z bardziej bezpiecznych haseł.
* Użytkownicy są przyzwyczajeni do regularnie wybierając niezabezpieczonych hasła
* Muszą oni o tym poinformować użytkowników o nadchodzących zmianę wymuszania zabezpieczeń, wpływ może mieć na nich i pomagają lepiej zrozumieć, jak można wybrać bardziej bezpiecznych haseł.

Gdy ta funkcja działa w trybie inspekcji rozsądnym czasie, konfigurację wymuszania można odwrócić z **inspekcji** do **Wymuś** dzięki czemu wymagają bardziej bezpiecznych haseł. Dobrym pomysłem jest monitorowanie wąsko zdefiniowany w tym czasie.

## <a name="deployment-requirements"></a>Wymagania dotyczące wdrażania

* Wszystkie kontrolery domeny z zainstalowaną usługę agenta ochrony kontrolera domeny hasła usługi Azure AD musi działać system Windows Server 2012 lub nowszy.

   > [!NOTE]
   > Server Core systemu operacyjnego nie są obecnie obsługiwane. Ta obsługa jest planowana na GA.

* Wszystkie komputery z zainstalowanym ochrony hasłem usługi Azure AD, usługa serwera Proxy musi działać system Windows Server 2012 R2 lub nowszym.

   > [!NOTE]
   > Server Core systemu operacyjnego nie są obecnie obsługiwane. Ta obsługa jest planowana na GA.

* Wszystkie komputery z zainstalowanym składniki ochrony hasła usługi Azure AD w tym kontrolery domeny muszą mieć zainstalowanego środowiska uruchomieniowego języka Universal C.
Najlepiej jest to realizowane poprzez wdrażanie poprawek w pełni maszyny za pośrednictwem usługi Windows Update. W przeciwnym razie odpowiedni pakiet aktualizacji specyficznych dla systemu operacyjnego może być zainstalowana — zobacz [aktualizacji dla uniwersalnego środowiska uruchomieniowego c. w Windows](https://support.microsoft.com/help/2999226/update-for-universal-c-runtime-in-windows)
* Łączność sieciowa musi istnieć między co najmniej jeden kontroler domeny w każdej domenie i co najmniej jeden serwer obsługujący usługę serwera proxy ochrony haseł usługi Azure AD. To połączenie musi zezwalać na kontrolerze domeny, aby uzyskiwać dostęp do portu mapowania punktu końcowego RPC (135) i port serwera RPC na usługę serwera proxy.  Port serwera RPC jest domyślnie dynamicznego portu RPC, ale można go skonfigurować (zobacz poniżej), aby korzystanie z portu statycznego.
* Konto administratora globalnego, można zarejestrować usługi serwera proxy ochrony hasła usługi Azure AD i lasu za pomocą usługi Azure AD.
* Konta z uprawnieniami administratora domeny usługi Active Directory w domenie głównej lasu, aby zarejestrować lasu usługi Active Directory systemu Windows Server z usługą Azure AD.
* Dowolnej domeny usługi Active Directory, uruchamianie kontrolera domeny oprogramowanie usługi agenta należy użyć DFSR do replikacji folderu sysvol.

## <a name="single-forest-deployment"></a>Wdrożenie pojedynczego lasu

Ochrona za pomocą hasła usługi Azure AD w wersji zapoznawczej jest wdrażana przy użyciu usługi Serwer proxy na maksymalnie dwa serwery, a usługa agenta kontrolera domeny można stopniowo wdrożyć do wszystkich kontrolerów domeny w lesie usługi Active Directory.

![W jaki sposób składniki ochrony hasła usługi Azure AD współpracują ze sobą](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

### <a name="download-the-software"></a>Pobierz oprogramowanie

Istnieją dwa wymagane pliki instalacyjne ochrony hasłem usługi Azure AD, który można pobrać z [Centrum pobierania firmy Microsoft](https://www.microsoft.com/download/details.aspx?id=57071)

### <a name="install-and-configure-the-azure-ad-password-protection-proxy-service"></a>Instalowanie i konfigurowanie usługi Serwer proxy ochrony haseł usługi Azure AD

1. Wybierz co najmniej jeden serwer do obsługi usługi serwera proxy ochrony haseł usługi Azure AD.
   * Każdej z tych usług można tylko zasady dotyczące haseł dla pojedynczego lasu, i maszynie hosta muszą być przyłączone do domeny (główna i podrzędna są zarówno równie obsługiwane) w tym lesie. Usługi Azure AD hasło ochrony serwera proxy do spełnienia swoją misję musi istnieć łączność sieciową między co najmniej jeden kontroler domeny w każdej domenie w lesie i komputer hosta serwera Proxy ochrony haseł usługi Azure AD.
   * Można zainstalować i uruchomić usługę serwera proxy usługi Azure AD hasło ochrony na kontrolerze domeny, do testowania celów, ale kontroler domeny następnie wymaga łączności z Internetem.

   > [!NOTE]
   > Publiczna wersja zapoznawcza obsługuje maksymalnie dwóch (2) serwerów proxy jednego lasu.

2. Zainstaluj oprogramowanie usługa serwera Proxy zasady haseł za pomocą pakietu AzureADPasswordProtectionProxy.msi MSI.
   * Instalacja oprogramowania nie jest wymagane ponowne uruchomienie komputera. Instalacja oprogramowania mogą zostać zautomatyzowane przy użyciu standardowych procedur MSI, na przykład: `msiexec.exe /i AzureADPasswordProtectionProxy.msi /quiet /qn`

3. Otwórz okno programu PowerShell jako Administrator.
   * Ochrona haseł usługi Azure AD oprogramowanie serwera Proxy obejmuje nowy moduł programu PowerShell o nazwie AzureADPasswordProtection. Poniższe kroki opierają się na uruchamianie różnych poleceń cmdlet z tego modułu programu PowerShell i założono, że otwarto nowe okno programu PowerShell i zaimportowano nowy moduł w następujący sposób:
      * `Import-Module AzureADPasswordProtection`

      > [!NOTE]
      > Oprogramowanie instalacyjne modyfikuje zmienną środowiskową PSModulePath maszynie hosta. Aby ta zmiana zaczęła obowiązywać, dzięki czemu AzureADPasswordProtection moduł programu powershell mogą importowane i używane może być konieczne otwarcie nowego okna konsoli programu PowerShell.

   * Upewnij się, że usługa jest uruchomiona przy użyciu następującego polecenia programu PowerShell: `Get-Service AzureADPasswordProtectionProxy | fl`.
      * Wynik powinno to dawać wynik z parametrem **stan** zwróceniem wyników "Uruchomiona".

4. Zarejestruj serwer proxy.
   * Po ukończeniu kroku 3 usługi serwera proxy ochrony haseł usługi Azure AD jest uruchomiona na komputerze, ale nie ma jeszcze poświadczenia niezbędne do komunikacji z usługą Azure AD. Rejestracja w usłudze Azure AD jest wymagany do włączenia, że używanie możliwości `Register-AzureADPasswordProtectionProxy` polecenia cmdlet programu PowerShell. Polecenie cmdlet wymaga administratora globalnego poświadczeń dla Twojej dzierżawy platformy Azure, a także uprawnienia administratora domeny usługi Active Directory w domenie głównej lasu lokalnego. Gdy zakończyła się pomyślnie dla danego serwera proxy usługi dodatkowe wywołań `Register-AzureADPasswordProtectionProxy` nadal pomyślnie tworzone, ale nie są konieczne.
      * Polecenia cmdlet mogą być uruchamiane w następujący sposób:

         ```
         $tenantAdminCreds = Get-Credential
         Register-AzureADPasswordProtectionProxy -AzureCredential $tenantAdminCreds
         ```

         Przykład tylko wtedy, gdy aktualnie zalogowanego użytkownika jest również administratorem domeny usługi Active Directory dla domeny katalogu głównego. Alternatywą jest podać poświadczenia domeny konieczne za pośrednictwem `-ForestCredential` parametru.

   > [!NOTE]
   > Ta operacja może zakończyć się niepowodzeniem, jeśli konfiguracja zwiększonych zabezpieczeń programu Internet Explorer jest włączona. Obejście polega na wyłączyć IESC, Zarejestruj serwer proxy, a następnie ponownie włącz IESC.

   > [!NOTE]
   > Rejestracja usługi serwera proxy ochrony haseł usługi Azure AD jest oczekiwaną jednorazowy krok w okresie istnienia usługi. Usługa serwera proxy automatycznie wykona wszelkie niezbędne maintainenance od tej pory i nowszych wersjach. Po pomyślnym dla podanego lasu dodatkowe wywołań "Register-AzureADPasswordProtectionProxy" nadal pomyślnie tworzone, ale nie są konieczne.

   > [!TIP]
   > Może istnieć znaczne opóźnienie (w sekundach wielu) przy pierwszym uruchomieniu tego polecenia cmdlet jest uruchamiane dla danej dzierżawy usługi Azure, przed polecenie cmdlet kończy wykonywanie. Chyba że błąd jest zgłaszany to opóźnienie nie powinny być uznawane za monitów.

5. Zarejestruj lasu.
   * W środowisku lokalnym lesie usługi Active Directory musi zostać zainicjowany z poświadczenia niezbędne do komunikacji z platformy Azure za pomocą `Register-AzureADPasswordProtectionForest` polecenia cmdlet programu Powershell. Polecenie cmdlet wymaga administratora globalnego poświadczeń dla Twojej dzierżawy platformy Azure, a także uprawnienia administratora domeny usługi Active Directory w domenie głównej lasu lokalnego. Ten krok zostanie uruchomiony jeden raz w każdym lesie.
      * Polecenia cmdlet mogą być uruchamiane w następujący sposób:

         ```
         $tenantAdminCreds = Get-Credential
         Register-AzureADPasswordProtectionForest -AzureCredential $tenantAdminCreds
         ```

         Przykład tylko wtedy, gdy aktualnie zalogowanego użytkownika jest również administratorem domeny usługi Active Directory dla domeny katalogu głównego. Alternatywą jest podać poświadczenia domeny konieczne za pomocą parametru - ForestCredential.

         > [!NOTE]
         > Ta operacja może zakończyć się niepowodzeniem, jeśli konfiguracja zwiększonych zabezpieczeń programu Internet Explorer jest włączona. Obejście polega na wyłączyć IESC, Zarejestruj serwer proxy, a następnie ponownie włącz IESC.

         > [!NOTE]
         > Jeśli wielu serwerów proxy są zainstalowane w danym środowisku, nie ma znaczenia, serwer proxy, który określono w poprzedniej procedurze.

         > [!TIP]
         > Może istnieć znaczne opóźnienie (w sekundach wielu) przy pierwszym uruchomieniu tego polecenia cmdlet jest uruchamiane dla danej dzierżawy usługi Azure, przed polecenie cmdlet kończy wykonywanie. Chyba że błąd jest zgłaszany to opóźnienie nie powinny być uznawane za monitów.

   > [!NOTE]
   > Rejestracja lasu usługi Active Directory oczekuje jednorazowy krok w okresie istnienia w lesie. Agentów kontrolera domeny w lesie automatycznie będzie wykonywać wszystkie niezbędne maintainenance od tej pory i nowszych wersjach. Gdy powiodła się dla podanego lasu, dodatkowe wywołań `Register-AzureADPasswordProtectionForest` nadal pomyślnie tworzone, ale nie są konieczne.

   > [!NOTE]
   > Aby `Register-AzureADPasswordProtectionForest` została wykonana pomyślnie co najmniej jeden system Windows Server 2012 lub nowszym domeny musi być dostępny kontroler domeny serwera proxy. Jednak nie jest wymagane zainstalowanie oprogramowania agenta kontrolera domeny kontrolerów domeny przed ten krok.

6. Opcjonalnie: Konfigurowanie usługi Serwer proxy usługi Azure AD hasło ochrony do nasłuchiwania na konkretnym porcie.
   * RPC za pośrednictwem protokołu TCP jest używany przez funkcję ochrony hasło usługi Azure AD oprogramowanie agenta kontrolera domeny na kontrolerach domeny, do komunikowania się z usługą serwera proxy ochrony haseł usługi Azure AD. Domyślnie ochrony hasłem usługi Azure AD, usługa serwera Proxy zasad haseł nasłuchuje na wszystkie dostępne dynamiczne końcowych wywołań RPC. Jeśli to konieczne ze względu na topologii sieci i wymagania dotyczące zapory, usługi zamiast tego można skonfigurować do nasłuchiwania na konkretnym porcie TCP.
      * Aby skonfigurować usługę, aby działała z portu statycznego, należy użyć `Set-AzureADPasswordProtectionProxyConfiguration` polecenia cmdlet.
         ```
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > Należy zatrzymać i ponownie uruchomić usługę, aby zmiany zaczęły obowiązywać.

      * Aby skonfigurować usługę, aby była uruchamiana z portów dynamicznych, użyj tej samej procedury ale StaticPort ponownie ustawiony na wartość zero, w następujący sposób:
         ```
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > Należy zatrzymać i ponownie uruchomić usługę, aby zmiany zaczęły obowiązywać.

   > [!NOTE]
   > Usługa serwera proxy ochrony haseł usługi Azure AD wymaga ręcznego ponownego uruchomienia po każdej zmianie konfiguracji portu. Nie jest konieczne ponowne uruchomienie kontrolera domeny oprogramowani usługi uruchomione na kontrolerach domeny, po wprowadzeniu zmian w konfiguracji tego rodzaju.

   * Bieżąca konfiguracja usługi może być odpytywany za pomocą `Get-AzureADPasswordProtectionProxyConfiguration` jak w poniższym przykładzie przedstawiono polecenie cmdlet:

      ```
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0 
      ```

### <a name="install-the-azure-ad-password-protection-dc-agent-service"></a>Instalacja usługi agenta ochrony kontrolera domeny hasła usługi Azure AD

* Instalowanie programu Azure AD hasło ochrony kontrolera domeny agenta usługi oprogramowania przy użyciu `AzureADPasswordProtectionDCAgent.msi` pakietu MSI:
   * Instalacja oprogramowania wymaga ponownego uruchomienia po instalacji i odinstalować ze względu na wymagania systemu operacyjnego tylko załadowane lub zwolnione po ponownym rozruchu biblioteki DLL filtru haseł.
   * Możliwe jest, aby zainstalować usługę agenta kontrolera domeny na komputerze, który nie jest jeszcze kontrolera domeny. W tym przypadku usługa zostanie uruchomiona i wykonywania, ale będą, w przeciwnym razie być nieaktywne do momentu po komputer zostanie podwyższony do kontrolera domeny.

   Instalacja oprogramowania mogą zostać zautomatyzowane przy użyciu standardowych procedur MSI, na przykład:  `msiexec.exe /i AzureADPasswordProtectionDCAgent.msi /quiet /qn`

   > [!WARNING]
   > Przykładowe polecenie msiexec spowoduje natychmiastowe ponowne uruchomienie; można tego uniknąć, określając `/norestart` flagi.

Po zainstalowaniu na kontrolerze domeny i ponownie uruchomiony, zakończeniu ochrony hasłem usługi Azure AD instalacji oprogramowania agenta kontrolera domeny. Konfiguracja nie jest wymagane, czy możliwa.

## <a name="multiple-forest-deployments"></a>Wielu wdrożeń będących częścią lasu

Nie ma żadnych dodatkowych wymagań, aby wdrożyć ochrona za pomocą hasła usługi Azure AD w wielu lasach. Zgodnie z opisem w sekcji Wdrażanie pojedynczego lasu, każdy las jest skonfigurowany niezależnie. Każda usługa Azure AD ochrona za pomocą hasła serwera Proxy może obsługiwać tylko kontrolery domeny z lasu, który jest dołączony do. Oprogramowanie ochrony haseł usługi Azure AD w danym lesie nie rozpoznaje oprogramowanie do ochrony przed hasła usługi Azure AD wdrożony w innym lesie, niezależnie od konfiguracji relacji zaufania usługi Active Directory.

## <a name="read-only-domain-controllers"></a>Kontrolery domeny tylko do odczytu

Changes\sets hasło nigdy nie są przetwarzane i utrwalane na kontrolerach domeny tylko do odczytu (RODC); Zamiast tego są one przekazywane do zapisywalne kontrolery domeny. W związku z tym nie ma potrzeby zainstalować oprogramowanie agenta kontrolera domeny na kontrolery RODC.

## <a name="high-availability"></a>Wysoka dostępność

Głównym problemem przy zapewnieniu wysokiej dostępności ochrona za pomocą hasła usługi Azure AD jest dostępność serwerów proxy, podczas próby pobrania nowych zasad lub innych danych z platformy Azure kontrolerów domeny w lesie. Publiczna wersja zapoznawcza obsługuje maksymalnie dwa serwery proxy w każdym lesie. Każdy agent kontrolera domeny używa algorytmu prostym stylem działanie okrężne, podejmując decyzję o który serwer proxy do wywołania i pomija za pośrednictwem serwerów proxy, które nie odpowiadają.
Zwykle problemy związane z wysoką dostępność zostaną skorygowane przez projekt oprogramowania agenta kontrolera domeny. Agent kontroler domeny przechowuje lokalnej pamięci podręcznej najbardziej ostatnio pobrane zasady haseł. Nawet wtedy, gdy wszystkie zarejestrowane serwery proxy stać się niedostępne z jakiegokolwiek powodu, agentach DC w dalszym ciągu wymuszać swoje zasady haseł w pamięci podręcznej. Częstotliwość aktualizacji uzasadnione dla zasad haseł w dużych wdrożeniach jest zwykle od kolejności, dni, a nie godzin lub mniej.   W związku z tym krótki awarii serwery proxy nie powodują znaczącego wpływu na działanie funkcji ochrony haseł usługi Azure AD lub jego korzyści w zakresie zabezpieczeń.

## <a name="next-steps"></a>Kolejne kroki

Po zainstalowaniu usług wymaganych do ochrony haseł usługi Azure AD na serwerach lokalnych wykonać czynności opisane [po instalacji, konfiguracji i zbieranie raportowanie informacji o](howto-password-ban-bad-on-premises-operations.md) do ukończenia danego wdrożenia.

[Omówienie pojęć dotyczących ochrona za pomocą hasła usługi Azure AD](concept-password-ban-bad-on-premises.md)
