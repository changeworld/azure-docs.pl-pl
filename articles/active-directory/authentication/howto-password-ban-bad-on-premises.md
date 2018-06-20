---
title: Wdrażanie usługi Azure AD hasło ochrony w wersji zapoznawczej
description: Wdrażanie Podgląd ochrony hasła usługi Azure AD, aby zakaz lokalne błędnego hasła
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 6fda373f832d6e24d1252587a19c88b0f464dda6
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232417"
---
# <a name="preview-deploy-azure-ad-password-protection"></a>Podgląd: Wdrażanie usługi Azure AD ochrony hasłem

|     |
| --- |
| Ochrony hasłem w usłudze Azure AD i listy zabronionych hasła niestandardowego są funkcje publicznej wersji zapoznawczej usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [uzupełniające warunki użytkowania dotyczącym wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Teraz, gdy mamy zrozumienia [sposobu wymuszania ochrony hasłem usługi Azure AD dla usługi Active Directory systemu Windows Server](concept-password-ban-bad-on-premises.md), następnym krokiem jest planowania i wykonywania wdrożenia.

## <a name="deployment-strategy"></a>Strategia wdrażania

Firma Microsoft sugeruje, że wszystkie wdrożenia rozruchu w trybie inspekcji. Tryb inspekcji jest ustawienie początkowej domyślne, których hasła mogą w dalszym ciągu można ustawić i wszelkie, które zostałby zablokowany utworzyć wpisy w dzienniku zdarzeń. Gdy serwery proxy i agentów kontroler domeny pełni są wdrożone w trybie inspekcji, regular monitorowanie ma się odbywać w celu ustalenia, jaki wpływ zasad haseł wymuszania może mieć na użytkowników i środowisko, jakby zasada była wymuszana.

Na etapie inspekcji znaleźć wiele organizacji:

* Należy poprawić istniejących procesów operacyjnych do używania z bardziej bezpiecznych haseł.
* Użytkownicy są zapoznanie się regularnie Wybieranie niezabezpieczone hasła
* Należy powiadomić użytkowników o nadchodzących zmianie Wymuszanie zabezpieczeń, wpływ, może być na nich i pomóc im lepiej zrozumieć, jak można wybrać więcej, bezpiecznych haseł.

Gdy funkcja działa w trybie inspekcji od rozsądnym czasie, konfigurację wymuszania można odwrócić z **inspekcji** do **Wymuś** dzięki czemu wymagają bardziej bezpiecznych haseł. Dobrym pomysłem jest ukierunkowanych monitorowania w tym czasie.

## <a name="known-limitation"></a>Znane ograniczenia

Jest to znane ograniczenie w wersji zapoznawczej proxy ochrony hasła usługi Azure AD. Korzystanie z konta administratora dzierżawy, które wymagają usługi MFA jest nieobsługiwany. Przyszłej aktualizacji proxy ochrony hasła usługi Azure AD będzie obsługiwać serwer proxy rejestracji z konta administratora, które wymagają usługi MFA.

## <a name="single-forest-deployment"></a>Pojedynczy las wdrożenia

W wersji zapoznawczej usługi Azure AD ochrony hasłem jest wdrażany przy użyciu usługi serwera proxy na maksymalnie dwa serwery, a usługa agenta kontrolera domeny można przyrostowo wdrożyć do wszystkich kontrolerów domeny w lesie usługi Active Directory.

![W jaki sposób składniki ochrony hasła usługi Azure AD współpracują ze sobą](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

### <a name="download-the-software"></a>Pobierz oprogramowanie

Istnieją dwa wymagane pliki instalacyjne ochrony hasłem usługi Azure AD, który można pobrać z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=57071)

### <a name="install-and-configure-the-azure-ad-password-protection-proxy-service"></a>Instalowanie i konfigurowanie usługi Serwer proxy ochrony hasła usługi Azure AD

1. Wybierz jeden lub więcej serwerów do obsługi usługi serwera proxy ochrony hasła usługi Azure AD.
   * Każdej z takich usług zapewniają tylko zasady haseł dla pojedynczego lasu, a komputer hosta musi być przyłączony do domeny w domenie (główna i podrzędna są równie obsługiwane) w tym lesie. Dla usługi Azure AD hasło ochrony serwera proxy do wykonania jej misji musi istnieć łączność sieciową między co najmniej jeden kontroler domeny w każdej domenie w lesie, a komputer hosta serwera Proxy ochrony hasła usługi Azure AD.
   * Jest obsługiwana, aby zainstalować i uruchomić usługę serwera proxy usługi Azure AD hasło ochrony na kontrolerze domeny do celów testowych, ale następnie wymaga łączności z Internetem.

   > [!NOTE]
   > Publicznej wersji zapoznawczej obsługuje maksymalnie dwóch (2) serwerów proxy dla każdego lasu.

2. Zainstaluj oprogramowanie usługi Proxy zasad hasła przy użyciu pakietu AzureADPasswordProtectionProxy.msi MSI.
   * Instalacja oprogramowania nie jest wymagane ponowne uruchomienie komputera. Instalacja oprogramowania mogą zostać zautomatyzowane przy użyciu standardowych procedur MSI, na przykład: `msiexec.exe /i AzureADPasswordProtectionProxy.msi /quiet /qn`

3. Otwórz okno programu PowerShell jako Administrator.
   * Ochrona hasła usługi Azure AD Proxy oprogramowania ma nowy moduł PowerShell o nazwie AzureADPasswordProtection. Poniższe kroki są oparte na uruchomienie poleceń cmdlet różne od ten moduł programu PowerShell i założono, że otwarto nowe okno programu PowerShell i zaimportowano nowy moduł w następujący sposób:
      * `Import-Module AzureADPasswordProtection`

      > [!NOTE]
      > Oprogramowanie instalacyjne modyfikuje zmiennej środowiskowej PSModulePath komputerze hosta. Aby ta zmiana została uwzględniona, tak aby zaimportowane i użyć modułu programu powershell AzureADPasswordProtection może być konieczne otwarcie nowego okna konsoli programu PowerShell.

   * Sprawdź, czy usługa jest uruchomiona za pomocą następującego polecenia programu PowerShell: `Get-Service AzureADPasswordProtectionProxy | fl`.
      * Wynik powinna dawać wynik z **stan** zwracanie wyniku "Uruchomiona".

4. Zarejestruj serwer proxy.
   * Po wykonaniu kroku 3 usługi serwera proxy ochrony hasła usługi Azure AD jest uruchomiona na komputerze, ale nie ma jeszcze niezbędne poświadczenia, aby komunikować się z usługą Azure AD. Aby włączyć za pomocą tej możliwości wymagana jest rejestracja usługi Azure AD `Register-AzureADPasswordProtectionProxy` polecenia cmdlet programu PowerShell. Polecenie cmdlet wymaga administratora globalnego poświadczeń dla dzierżawy usługi Azure, jak również lokalne uprawnienia administratora domeny usługi Active Directory w domenie głównej lasu. Gdy zakończyła się pomyślnie danego serwera proxy usługi dodatkowe wywołań `Register-AzureADPasswordProtectionProxy` nadal się powieść, ale nie są konieczne.
      * Polecenia cmdlet mogą być uruchamiane w następujący sposób:

         ```
         $tenantAdminCreds = Get-Credential
         Register-AzureADPasswordProtectionProxy -AzureCredential $tenantAdminCreds
         ```

         Przykład działa tylko, jeśli aktualnie zalogowanego użytkownika jest również administratorem domeny usługi Active Directory dla domeny głównej. Alternatywą jest podanie poświadczeń domeny konieczne za pośrednictwem `-ForestCredential` parametru.

   > [!TIP]
   > Może istnieć znaczne opóźnienie (w sekundach wiele) przy pierwszym uruchomieniu tego polecenia cmdlet jest uruchamiane dla danej dzierżawy usługi Azure ukończenia wykonywania polecenia cmdlet. O ile został zgłoszony błąd to opóźnienie nie należy traktować gorsze.

   > [!NOTE]
   > Rejestracja usługi serwera proxy ochrony hasła usługi Azure AD ma być jednorazowe krok w okresie istnienia usługi. Usługa serwera proxy automatycznie zostanie przeprowadzone wszystkie niezbędne maintainenance z tego punktu i jego nowszych wersjach. Po pomyślnym dla podanego lasu dodatkowe wywołania "Register-AzureADPasswordProtectionProxy" nadal się powieść, ale są niepotrzebne.

5. Zarejestruj lasu.
   * W lokalnym lesie usługi Active Directory musi być zainicjowany niezbędne poświadczenia, aby komunikować się z platformy Azure przy użyciu `Register-AzureADPasswordProtectionForest` polecenia cmdlet programu Powershell. Polecenie cmdlet wymaga administratora globalnego poświadczeń dla dzierżawy usługi Azure, jak również lokalne uprawnienia administratora domeny usługi Active Directory w domenie głównej lasu. W tym kroku jest uruchamiane jeden raz dla każdego lasu.
      * Polecenia cmdlet mogą być uruchamiane w następujący sposób:

         ```
         $tenantAdminCreds = Get-Credential
         Register-AzureADPasswordProtectionForest -AzureCredential $tenantAdminCreds
         ```

         Przykład działa tylko, jeśli aktualnie zalogowanego użytkownika jest również administratorem domeny usługi Active Directory dla domeny głównej. Alternatywą jest podać poświadczenia domeny konieczne za pomocą parametru - ForestCredential.

         > [!NOTE]
         > Jeśli wielu serwerów proxy są zainstalowane w danym środowisku, które konkretnego serwera proxy powyżej powyższa procedura jest wykonywana po nie ma znaczenia.

         > [!TIP]
         > Może istnieć znaczne opóźnienie (w sekundach wiele) przy pierwszym uruchomieniu tego polecenia cmdlet jest uruchamiane dla danej dzierżawy usługi Azure ukończenia wykonywania polecenia cmdlet. O ile został zgłoszony błąd to opóźnienie nie należy traktować gorsze.

   > [!NOTE]
   > Rejestracja w lesie usługi Active Directory oczekuje jednorazowe krok w okresie istnienia w lesie. Agenci kontrolera domeny w lesie automatycznie zostanie przeprowadzone wszystkie niezbędne maintainenance z tego punktu i jego nowszych wersjach. Po powiodła się dla podanego lasu, dodatkowe wywołania `Register-AzureADPasswordProtectionForest` nadal się powieść, ale nie są konieczne.

6. Opcjonalnie: Konfigurowanie usługi Azure AD hasło ochrony serwera proxy do nasłuchiwania na konkretnym porcie.
   * RPC przez protokół TCP jest używany przez usługi Azure AD ochrony hasłem oprogramowanie agenta kontrolera domeny na kontrolerach domeny do komunikowania się z usługą Azure AD hasło ochrony serwera proxy. Domyślnie ochrony hasłem usługi Azure AD usługi Proxy zasad haseł nasłuchuje na wszystkie dostępne dynamiczne punktem końcowym. Jeśli to konieczne, ze względu na wymagania dotyczące zapory lub topologię sieci, usługa zamiast tego można skonfigurować do nasłuchiwania na konkretnym porcie TCP.
      * Aby skonfigurować usługę, aby była uruchamiana z portu statycznego, użyj `Set-AzureADPasswordProtectionProxyConfiguration` polecenia cmdlet.
         ```
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > Należy zatrzymać i ponownie uruchomić usługę, aby te zmiany zaczęły obowiązywać.

      * Aby skonfigurować usługę do uruchamiania w port dynamiczny, użyj tej samej procedury ale StaticPort ponownie ustawiony na wartość zero, w następujący sposób:
         ```
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > Należy zatrzymać i ponownie uruchomić usługę, aby te zmiany zaczęły obowiązywać.

   > [!NOTE]
   > Usługi serwera proxy ochrony hasła usługi Azure AD wymaga ręcznego ponownego uruchomienia po każdej zmianie konfiguracji portu. Nie jest konieczne ponowne uruchomienie uruchomionych na kontrolerach domeny po wprowadzeniu zmian w konfiguracji tego rodzaju oprogramowanie usługi agenta kontrolera domeny.

   * Bieżąca konfiguracja usługi można wyświetlić, przy użyciu `Get-AzureADPasswordProtectionProxyConfiguration` jak w poniższym przykładzie przedstawiono polecenie cmdlet:

      ```
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0 
      ```

### <a name="install-the-azure-ad-password-protection-dc-agent-service"></a>Instalacja usługi agenta ochrony DC hasła usługi Azure AD

* Instalowanie programu Azure AD hasło ochrony DC agenta usługi oprogramowania przy użyciu `AzureADPasswordProtectionDCAgent.msi` pakiet MSI:
   * Instalacja oprogramowania wymaga ponownego uruchomienia w instalacji i Odinstaluj ze względu na wymagania systemu operacyjnego tylko załadowany lub zwolnione po ponownym rozruchu biblioteki DLL filtru haseł.
   * Możliwe jest, aby zainstalować usługę agenta kontrolera domeny na komputerze, który nie jest jeszcze kontrolera domeny. W takim przypadku usługa zostanie uruchomiona, i uruchom, ale w przeciwnym razie zostanie być nieaktywne do momentu po maszynie jest podwyższany do kontrolera domeny.

   Instalacja oprogramowania mogą zostać zautomatyzowane przy użyciu standardowych procedur MSI, na przykład:  `msiexec.exe /i AzureADPasswordProtectionDCAgent.msi /quiet /qn`

   > [!WARNING]
   > Przykładowe polecenie msiexec spowoduje natychmiastowego ponownego uruchomienia; można tego uniknąć, określając `/norestart` flagi.

Po zainstalowaniu na kontrolerze domeny i ponownego uruchomienia, zakończeniu ochrony hasłem usługi Azure AD instalacji oprogramowania agenta z kontrolera domeny. Inne konfiguracja nie jest wymagane lub niemożliwe.

## <a name="multiple-forest-deployments"></a>Wielu wdrożeń lasu

Nie ma żadnych dodatkowych wymagań do wdrożenia w wielu lasach ochrony hasłem usługi Azure AD. Każdy las niezależnie jest skonfigurowany zgodnie z opisem w sekcji Wdrażanie jednego lasu. Każdy ochrony hasłem usługi Azure AD Proxy może obsługiwać tylko kontrolery domeny z lasu, który jest dołączony do. Oprogramowanie ochrony hasła usługi Azure AD w danym lesie nie rozpoznaje oprogramowania ochrony hasła usługi Azure AD wdrożony w innym lesie, niezależnie od ustawień zaufania usługi Active Directory.

## <a name="read-only-domain-controllers"></a>Kontrolery domeny tylko do odczytu

Changes\sets hasło nigdy nie są przetwarzane i utrwalane na kontrolerach domeny tylko do odczytu (RODC); Zamiast tego są one przekazywane do zapisywalne kontrolery domeny. W związku z tym nie istnieje potrzeba instalacji oprogramowania agenta kontrolera domeny na kontrolera RODC.

## <a name="high-availability"></a>Wysoka dostępność

Głównym problemem z zapewniające wysoką dostępność ochrony hasłem usługi Azure AD jest dostępności serwerów proxy, podczas próby pobrania nowych zasad lub innych danych z platformy Azure kontrolerów domeny w lesie. Publicznej wersji zapoznawczej obsługuje maksymalnie dwa serwery proxy w każdym lesie. Każdy agent, kontroler domeny używa algorytmu prostego stylu okrężnego podczas wybierania który serwer proxy do wywołania, a pomija za pośrednictwem serwerów proxy, które nie odpowiadają.
Zwykle problemów związanych z wysokiej dostępności, zostały skorygowane przez projekt oprogramowanie agenta kontrolera domeny. Agent kontroler domeny przechowuje lokalnej pamięci podręcznej najbardziej ostatnio pobranego zasady haseł. Nawet jeśli wszystkie zarejestrowane serwery proxy stać się niedostępne z jakiegokolwiek powodu, agentach DC nadal wymuszać swoje zasady haseł w pamięci podręcznej. Częstotliwość aktualizacji uzasadnione zasad haseł w dużych wdrożeniach jest zwykle na kolejności dni nie godzin lub mniej.   W związku z tym krótki awarie serwerów proxy, nie powodują znacznie wpłynąć na działanie funkcji ochrony hasła usługi Azure AD lub jego korzyści w zakresie zabezpieczeń.

## <a name="next-steps"></a>Kolejne kroki

Teraz, które zostały zainstalowane usługi wymagany dla ochrony hasłem usługi Azure AD na serwerach lokalnych wykonać [po instalacji konfiguracji i zbieranie informacji raportów](howto-password-ban-bad-on-premises-operations.md) do ukończenia wdrożenia.

[Omówienie pojęć dotyczących ochrony hasłem usługi Azure AD](concept-password-ban-bad-on-premises.md)
