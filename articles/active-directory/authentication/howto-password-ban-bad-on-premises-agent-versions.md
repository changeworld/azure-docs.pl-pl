---
title: W środowisku lokalnym Historia wersji agenta ochrony haseł usługi Azure AD
description: W wersji dokumentów i zachowania historii zmian
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8897651c963b0036bc2ac3d8cdb06a52d6f52ba1
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56188040"
---
# <a name="preview--azure-ad-password-protection-agent-version-history"></a>Wersja zapoznawcza:  Historia wersji agenta w usłudze Azure ochrona za pomocą hasła usługi AD

|     |
| --- |
| Ochrony hasłem w usłudze Azure AD jest funkcją publicznej wersji zapoznawczej usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych usług Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="12650"></a>1.2.65.0

Data wydania: 2/1/2019

Changes:

* Kontroler domeny usługi agenta i serwer proxy są teraz obsługiwane w instalacji Server Core. Wymagania systemu operacyjnego minimalna są bez zmian od przed: Windows Server 2012 dla agentów DC i Windows Server 2012 R2 dla serwerów proxy.
* Polecenia cmdlet Register-AzureADPasswordProtectionProxy i zarejestruj AzureADPasswordProtectionForest obsługują teraz tryby uwierzytelniania platformy Azure na podstawie kodu urządzeń.
* Polecenie cmdlet Get-AzureADPasswordProtectionDCAgent będzie ignorować punktów połączenia usługi zniekształcone i/lub nieprawidłowy. To naprawia usterki, w których kontrolery domeny będą czasami wyświetlane wiele razy w danych wyjściowych.
* Polecenie cmdlet Get-AzureADPasswordProtectionSummaryReport będzie ignorować punktów połączenia usługi zniekształcone i/lub nieprawidłowy. To naprawia usterki, w których kontrolery domeny będą czasami wyświetlane wiele razy w danych wyjściowych.
* Moduł programu powershell serwera Proxy jest obecnie zarejestrowany z % ProgramFiles%\WindowsPowerShell\Modules. Zmienna środowiskowa PSModulePath komputera nie jest modyfikowany.
* Nowe polecenie cmdlet Get-AzureADPasswordProtectionProxy dołączonym do pomocy w odnajdywanie zarejestrowanych serwerów proxy w domenie lub lesie.
* Agent kontrolera domeny używa nowego folderu w udziale sysvol dla replikacji zasad haseł i innych plików.

   Stary lokalizacja folderu:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Nowa lokalizacja folderu:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   (Ta zmiana została wprowadzona w celu uniknięcia wyników fałszywie dodatnich "oddzielonych obiektów zasad grupy" ostrzeżenia).

   > [!NOTE]
   > Migracja nie jest lub udostępniania danych, zostanie wykonane między folderem stary i nowy folder. Starsze wersje agenta kontrolera domeny będzie w dalszym ciągu używać starej lokalizacji do momentu uaktualnienia do tej wersji lub nowszej. Gdy wszyscy agenci kontroler domeny jest uruchomiona wersja 1.2.65.0 lub później, stary folder sysvol może usunąć ją ręcznie.

* Usługa agent i serwer proxy kontrolera domeny teraz wykryć i usunąć zniekształcone kopie ich odpowiednich punkty połączeń.
* Każdy agent DC okresowo spowoduje usunięcie punktów połączenia usługi zniekształcone i stałe w jego domenie dla obu kontrolera domeny i agent proxy punktów połączenia usługi. Zarówno kontroler domeny i agent proxy punktów połączenia usługi, są traktowane jako nieaktualne, jeśli jego sygnatury czasowej pulsu jest starsza niż 7 dni.
* Agent kontrolera domeny będzie teraz Odnów certyfikat lasu, zgodnie z potrzebami.
* Usługa serwera Proxy zostanie teraz Odnów certyfikat serwera proxy, zgodnie z potrzebami.
* Aktualizacje algorytmu weryfikacji hasła: listy zakazanych haseł w globalnych i specyficznych dla klienta listy zakazanych haseł (jeśli jest skonfigurowane), które są połączone przed sprawdzania poprawności hasła. Teraz mogą zostać odrzucone podanym hasłem (się nie powieść lub tylko do inspekcji) zawiera tokenów z list globalnych i specyficznych dla klienta. Dokumentacja dziennik zdarzeń został zaktualizowany w celu przedstawienia tej; zobacz [ochrony haseł usługi Azure AD Monitor](howto-password-ban-bad-on-premises-monitor.md).
* Wydajność i niezawodność poprawki
* Ulepszone rejestrowanie

> [!WARNING]
> Czas ograniczoną funkcjonalność: Usługa agenta kontrolera domeny w tej wersji (1.2.65.0) zostanie zatrzymane przetwarzanie żądania weryfikacji hasła od 2019 1 września.  Usługi agenta kontrolera domeny w poprzednich wersjach (patrz lista poniżej) spowoduje zatrzymanie przetwarzania od 2019 1 lipca. Usługa agenta DC we wszystkich wersjach będzie rejestrować 10021 zdarzenia w dzienniku zdarzeń administratora w ciągu dwóch miesięcy prowadzących do tych terminów. Wszystkie ograniczenia limitu czasu zostanie usunięta w przyszłych wersji ogólnie dostępnej. Usługa agenta serwera Proxy nie jest ograniczony czasowo w dowolnej wersji, ale nadal powinny zostać uaktualnione do najnowszej wersji, aby można było korzystać z kolejnych poprawki i inne ulepszenia.

## <a name="12250"></a>1.2.25.0

Data wydania: 11/01/2018

Poprawki:

* Kontroler domeny agent i serwer proxy usługi powinna już zakończyć się niepowodzeniem z powodu błędów zaufania certyfikatu.
* Kontroler domeny agent i serwer proxy usługi mają dodatkowe poprawki dla maszyn zgodne ze standardem FIPS.
* Usługa serwera proxy będzie teraz działać prawidłowo w protokół TLS 1.2 — tylko środowisku sieciowym.
* Mniejszy przyrost wydajności i niezawodności poprawek
* Ulepszone rejestrowanie

Changes:

* Minimalny wymagany poziom systemu operacyjnego dla usługi serwera Proxy jest teraz systemu Windows Server 2012 R2. Minimalny wymagany poziom systemu operacyjnego dla usługi agenta DC pozostaje w systemie Windows Server 2012.
* Usługa Serwer Proxy wymaga teraz platformy .NET w wersji 4.6.2.
* Algorytm sprawdzania poprawności hasła używa tabeli normalizacji rozwiniętej znaków. Może to spowodować, że hasła są odrzucane, które zostały zaakceptowane w poprzednich wersjach.

## <a name="12100"></a>1.2.10.0

Data wydania: 8/17/2018

Poprawki:

* AzureADPasswordProtectionProxy Zarejestruj się i zarejestruj AzureADPasswordProtectionForest obsługują teraz uwierzytelnianie wieloskładnikowe
* Zarejestruj AzureADPasswordProtectionProxy wymaga WS2012 kontrolera domeny lub nowszym w domenie, aby uniknąć błędów szyfrowania.
* Usługa agenta kontrolera domeny jest bardziej niezawodna dotyczących żądania nowych zasad haseł z platformy Azure przy uruchamianiu.
* Usługa agenta kontrolera domeny będzie żądać nowych zasad haseł z platformy Azure co godzinę, jeśli to konieczne, ale teraz będzie wpływać na losowo wybranej godzinie rozpoczęcia.
* Usługa agenta kontrolera domeny nie jest już spowoduje opóźnienie nieokreślony w nowy anons kontrolera domeny podczas instalacji na serwerze przed jego promocję jako replika.
* Usługa agenta kontrolera domeny będzie obsługiwać ustawienie konfiguracji "Włącz ochrona za pomocą hasła w systemie Windows Server Active Directory"
* Oba instalatory agent i serwer proxy kontrolera domeny będzie teraz obsługiwać uaktualnienia w miejscu, w przypadku uaktualniania do kolejnych wersji.

> [!WARNING]
> Uaktualnienie w miejscu z wersji 1.1.10.3 nie jest obsługiwane i spowodują błąd instalacji. Do uaktualnienia do wersji 1.2.10 lub nowszej, należy najpierw całkowicie odinstalować oprogramowanie usługi agenta i serwer proxy kontrolera domeny, a następnie zainstalowanie nowej wersji od podstaw. Ponowna rejestracja ochrony hasłem usługi Azure AD, usługa serwera Proxy jest wymagany.  Ponownie zarejestrować lasu nie jest wymagany.

> [!NOTE]
> Uaktualnienie oprogramowania agenta kontrolera domeny będzie wymagać ponownego uruchomienia systemu.

* Kontroler domeny usługi agenta i serwer proxy jest teraz obsługiwana na serwerze skonfigurowanym do użycia tylko algorytmów zgodnych ze standardem FIPS.
* Mniejszy przyrost wydajności i niezawodności poprawek
* Ulepszone rejestrowanie

## <a name="11103"></a>1.1.10.3

Data wydania: 6/15/2018

Początkowa publicznej wersji zapoznawczej

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie ochrony haseł usługi Azure AD](howto-password-ban-bad-on-premises-deploy.md)
