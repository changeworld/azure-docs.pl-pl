---
title: Historia wydania agenta ochrony hasłem — usługa Azure Active Directory
description: Dokumenty wersja wydania i historia zmian zachowania
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71fd33388cb1bdf7c87c44fb3273c6850122a0cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74847853"
---
# <a name="azure-ad-password-protection-agent-version-history"></a>Historia wersji agenta usługi Azure AD Password Protection

## <a name="121250"></a>1.2.125.0

Data wydania: 22.03.2019

* Naprawianie drobnych błędów literówek w komunikatach dziennika zdarzeń
* Aktualizacja umowy EuLA do ostatecznej ogólnej wersji dostępności

> [!NOTE]
> Kompilacja 1.2.125.0 to kompilacja ogólna dostępność. Jeszcze raz dziękuję wszystkim, którzy przekazali opinie na temat produktu!

## <a name="121160"></a>1.2.116.0

Data wydania: 13.03.2019

* Polecenia cmdlet Get-AzureADPasswordProtectionProxy i Get-AzureADPasswordProtectionDCAgent zgłaszają teraz wersję oprogramowania i bieżącą dzierżawę platformy Azure z następującymi ograniczeniami:
  * Wersja oprogramowania i dane dzierżawy platformy Azure są dostępne tylko dla agentów kontrolera domeny i serwerów proxy z systemem 1.2.116.0 lub nowszym.
  * Dane dzierżawy platformy Azure nie mogą być zgłaszane do czasu ponownej rejestracji (lub odnowienia) serwera proxy lub lasu.
* Usługa proxy wymaga teraz zainstalowania platformy .NET 4.7.
  * Program .NET 4.7 powinien być już zainstalowany na w pełni zaktualizowanym systemie Windows Server. Jeśli tak nie jest, pobierz i uruchom instalator znajdujący się w [instalatorze trybu offline .NET Framework 4.7 dla systemu Windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
  * W systemach Server Core może być konieczne przekazanie flagi /q do instalatora .NET 4.7, aby odnieść sukces.
* Usługa Proxy obsługuje teraz automatyczne uaktualnianie. Automatyczne uaktualnianie korzysta z usługi Microsoft Azure AD Connect Agent Updater, która jest instalowana obok usługi proxy. Automatyczne uaktualnianie jest domyślnie włączone.
* Automatyczne uaktualnianie można włączyć lub wyłączyć za pomocą polecenia cmdlet Set-AzureADPasswordProtectionProxyConfiguration. Bieżące ustawienie można zbadać przy użyciu polecenia cmdlet Get-AzureADPasswordProtectionProxyConfiguration.
* Nazwa pliku binarnego usługi usługi kontrolera domeny została zmieniona na AzureADPasswordProtectionDCAgent.exe.
* Nazwa pliku binarnego usługi Proxy została zmieniona na AzureADPasswordProtectionProxy.exe. Reguły zapory mogą wymagać modyfikacji odpowiednio, jeśli zapora innej firmy jest w użyciu.
  * UWAGA: jeśli plik konfiguracyjny serwera proxy http był używany w poprzedniej instalacji serwera proxy, po tym uaktualnieniu należy go zmienić nazwę (z *proxyservice.exe.config* do *AzureADPasswordProtectionProxy.exe.config).*
* Wszystkie ograniczone czasowo sprawdzanie funkcjonalności zostało usunięte z agenta kontrolera domeny.
* Drobne poprawki błędów i ulepszenia rejestrowania.

## <a name="12650"></a>1.2.65.0

Data wydania: 2/1/2019

Zmiany:

* Agent kontrolera domeny i usługa serwera proxy są teraz obsługiwane w programie Server Core. Wymagania dotyczące systemu operacyjnego Mininimum pozostają niezmienione w porównaniu z poprzednimi wersjami: System Windows Server 2012 dla agentów kontrolera domeny i Windows Server 2012 R2 dla serwerów proxy.
* Polecenia cmdlet Register-AzureADPasswordProtectionProxy i Register-AzureADPasswordProtectionForest obsługują teraz tryby uwierzytelniania platformy Azure oparte na kodzie urządzenia.
* Polecenie cmdlet Get-AzureADPasswordProtectionDCAgent zignoruje zniekształcone i/lub nieprawidłowe punkty połączenia usługi. Spowoduje to naprawienie błędu, w którym kontrolery domeny czasami pojawiały się wiele razy w danych wyjściowych.
* Polecenie cmdlet Get-AzureADPasswordProtectionSummaryReport zignoruje zniekształcone i/lub nieprawidłowe punkty połączenia usługi. Spowoduje to naprawienie błędu, w którym kontrolery domeny czasami pojawiały się wiele razy w danych wyjściowych.
* Moduł programu Proxy powershell jest teraz zarejestrowany z %ProgramFiles%\WindowsPowerShell\Modules. Zmienna środowiskowa PSModulePath komputera nie jest już modyfikowana.
* Nowy get-AzureADPasswordProtectionProxy cmdlet został dodany do pomocy w odnajdowaniu zarejestrowanych serwerów proxy w lesie lub domenie.
* Agent kontrolera domeny używa nowego folderu w udziale sysvol do replikowania zasad haseł i innych plików.

   Stara lokalizacja folderu:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Nowa lokalizacja folderu:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   (Ta zmiana została wyz.)

   > [!NOTE]
   > Między starym a nowym folderem nie będzie odbywać się migracja ani udostępnianie danych. Starsze wersje agenta kontrolera domeny będą nadal używać starej lokalizacji, dopóki nie zostaną uaktualnione do tej wersji lub nowszej. Gdy wszystkie agenci kontrolera domeny są uruchomione w wersji 1.2.65.0 lub nowszej, stary folder sysvol może zostać ręcznie usunięty.

* Agent kontrolera domeny i usługa serwera proxy będą teraz wykrywać i usuwać zniekształcone kopie odpowiednich punktów połączenia usługi.
* Każdy agent kontrolera domeny będzie okresowo usuwać zniekształcone i przestarzałe punkty połączenia usługi w swojej domenie, zarówno dla agenta kontrolera domeny, jak i punktów połączenia usługi proxy. Zarówno agent kontrolera domeny, jak i punkty połączenia usługi proxy są uważane za przestarzałe, jeśli jego sygnatura czasowa pulsu jest starsza niż siedem dni.
* Agent kontrolera domeny odnowi teraz certyfikat lasu zgodnie z potrzebami.
* Usługa proxy odnowi teraz certyfikat serwera proxy w razie potrzeby.
* Aktualizacje algorytmu sprawdzania poprawności hasła: globalna lista zakazanych haseł i lista zakazanych haseł dla klienta (jeśli jest skonfigurowana) są łączone przed weryfikacją hasła. Podane hasło może teraz zostać odrzucone (tylko niepowodzenie lub tylko do inspekcji), jeśli zawiera tokeny z listy globalnej i specyficznej dla klienta. Dokumentacja dziennika zdarzeń została zaktualizowana w celu odzwierciedlenia tego; zobacz [Monitoruj ochronę hasłem usługi Azure AD](howto-password-ban-bad-on-premises-monitor.md).
* Poprawiono wydajność i niezawodność
* Ulepszone rejestrowanie

> [!WARNING]
> Ograniczone czasowo funkcje: usługa agenta kontrolera domeny w tej wersji (1.2.65.0) przestanie przetwarzać żądania sprawdzania poprawności hasła od 1 września 2019 r.  Usługi agenta kontrolera domeny w poprzednich wersjach (patrz lista poniżej) przestaną być przetwarzane od 1 lipca 2019 r. Usługa agenta kontrolera domeny we wszystkich wersjach będzie rejestrować zdarzenia 10021 do dziennika zdarzeń administratora w ciągu dwóch miesięcy poprzedzających te terminy. Wszystkie ograniczenia dotyczące limitu czasu zostaną usunięte w nadchodzącej wersji GA. Usługa agenta proxy nie jest ograniczona czasowo w żadnej wersji, ale nadal powinna zostać uaktualniona do najnowszej wersji, aby skorzystać ze wszystkich kolejnych poprawek błędów i innych ulepszeń.

## <a name="12250"></a>1.2.25.0

Data wydania: 11/01/2018

Poprawki:

* Agent kontrolera domeny i usługa serwera proxy nie powinny już zakończyć się niepowodzeniem z powodu błędów zaufania certyfikatów.
* Agent kontrolera domeny i usługa proxy mają dodatkowe poprawki dla maszyn zgodnych z FIPS.
* Usługa serwera proxy będzie teraz działać poprawnie w środowisku sieciowym tylko dla protokołu TLS 1.2.
* Drobne poprawki wydajności i odporności
* Ulepszone rejestrowanie

Zmiany:

* Minimalny wymagany poziom systemu operacyjnego dla usługi proxy to teraz System Windows Server 2012 R2. Minimalny wymagany poziom systemu operacyjnego dla usługi agenta kontrolera domeny pozostaje w systemie Windows Server 2012.
* Usługa proxy wymaga teraz .NET w wersji 4.6.2.
* Algorytm sprawdzania poprawności hasła używa rozwiniętej tabeli normalizacji znaków. Może to spowodować odrzucenie haseł, które zostały zaakceptowane w poprzednich wersjach.

## <a name="12100"></a>1.2.10.0

Data wydania: 17.08.2018

Poprawki:

* Register-AzureADPasswordProtectionProxy i Register-AzureADPasswordProtectionForest obsługuje teraz uwierzytelnianie wieloskładnikowe
* Register-AzureADPasswordProtectionProxy wymaga kontrolera domeny WS2012 lub nowszego w domenie, aby uniknąć błędów szyfrowania.
* Usługa agenta kontrolera domeny jest bardziej niezawodne w żądaniu nowych zasad haseł z platformy Azure podczas uruchamiania.
* Usługa agenta kontrolera domeny zażąda nowych zasad haseł z platformy Azure co godzinę, jeśli to konieczne, ale teraz będzie to robić w losowo wybranym czasie rozpoczęcia.
* Usługa agenta kontrolera domeny nie będzie już powodować nieokreślonego opóźnienia w nowej anonsie kontrolera domeny po zainstalowaniu na serwerze przed jego promocją jako replika.
* Usługa agenta kontrolera domeny będzie teraz honorować ustawienie konfiguracji "Włącz ochronę hasłem w usłudze Active Directory systemu Windows Server"
* Zarówno instalatory agenta kontrolera domeny, jak i serwer proxy będą teraz obsługiwać uaktualnianie w miejscu podczas uaktualniania do przyszłych wersji.

> [!WARNING]
> Uaktualnienie w miejscu z wersji 1.1.10.3 nie jest obsługiwane i spowoduje błąd instalacji. Aby uaktualnić do wersji 1.2.10 lub nowszej, należy najpierw całkowicie odinstalować agenta kontrolera domeny i oprogramowanie usługi proxy, a następnie zainstalować nową wersję od podstaw. Wymagana jest ponowna rejestracja usługi proxy ochrony hasłem usługi Azure AD.  Nie jest wymagane ponowne zarejestrowanie lasu.

> [!NOTE]
> Uaktualnienia w miejscu oprogramowania agenta kontrolera domeny będą wymagały ponownego uruchomienia komputera.

* Agent kontrolera domeny i usługa serwera proxy obsługują teraz uruchamianie na serwerze skonfigurowanym do używania tylko algorytmów zgodnych ze standardem FIPS.
* Drobne poprawki wydajności i odporności
* Ulepszone rejestrowanie

## <a name="11103"></a>1.1.10.3

Data wydania: 15.06.2018

Wstępna publiczna wersja zapoznawcza

## <a name="next-steps"></a>Następne kroki

[Wdrażanie ochrony hasłem usługi Azure AD](howto-password-ban-bad-on-premises-deploy.md)
