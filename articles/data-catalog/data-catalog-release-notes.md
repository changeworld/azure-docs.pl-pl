---
title: Usługa Azure Data Catalog — informacje o wersji
description: Informacje o wersji usługi Azure Data Catalog.
services: data-catalog
author: steelanddata
ms.author: maroche
ms.assetid: 3aca9c49-45a4-4352-92e6-bd25ee3eacf7
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 539ef4f591a1ef0c6ab344e1d93a750133d9e089
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43053745"
---
# <a name="azure-data-catalog-release-notes"></a>Usługa Azure Data Catalog — informacje o wersji
## <a name="notes-for-the-november-20-2015-release-of-azure-data-catalog"></a>20 listopada 2015 r. informacje o wersji usługi Azure Data Catalog
### <a name="opening-data-sources-in-power-bi-desktop"></a>Otwieranie źródeł danych w programie Power BI Desktop
Korzystając z opcji "Otwórz w programie Power BI Desktop" z **usługi Azure Data Catalog** portalu użytkowników może wystąpić jeden z dwóch problemów w aplikacji Power BI Desktop:

* Zostanie wyświetlone okno dialogowe z tytułem "Nie do otwartego dokumentu"
* Zostanie otwarty w aplikacji Power BI Desktop, ale plik może być pusta.

W każdej sytuacji, ten problem można rozwiązać przez pobranie i zainstalowanie najnowszej wersji programu Power BI Desktop z [PowerBI.com](https://powerbi.com).

## <a name="notes-for-the-november-13-2015-release-of-azure-data-catalog"></a>13 listopada 2015 r. informacje o wersji usługi Azure Data Catalog
### <a name="registering-and-connecting-to-teradata"></a>Rejestrowanie i łączenie się programu Teradata
Podczas łączenia się z użytkownikami źródeł danych Teradata musi być zainstalowany sterownik Teradata ODBC, który odpowiada liczbie bitów (32-bitowy lub 64-bitowy) oprogramowanie wykorzystywane w.

Tej daty ADC wydania, najbardziej aktualną [Teradata sterownik dla systemu windows (wersja 15.10)](http://downloads.teradata.com/download/connectivity/odbc-driver/windows) jest zgodny z pakietem Office 2013, ale nie w pakiecie Office 2016.

## <a name="notes-for-the-july-13-2015-release-of-azure-data-catalog"></a>Informacje o 13 lipca 2015 wersji usługi Azure Data Catalog
### <a name="registering-and-connecting-to-oracle-database"></a>Rejestrowanie i nawiązywania połączenia z bazą danych Oracle
Podczas nawiązywania połączenia ze źródłami danych Oracle Database użytkowników należy zainstalować odpowiednie sterowniki Oracle, które odpowiadają wartości bitowości (32-bitowy lub 64-bitowy) oprogramowanie wykorzystywane.

* Podczas rejestrowania źródła danych Oracle na komputerze z systemem Windows 32-bitowy, 32-bitowe sterowniki Oracle będzie służyć
* Podczas rejestrowania źródła danych Oracle na komputerze z systemem Windows 64-bitowym, 64-bitowe sterowniki Oracle będzie służyć
* Podczas nawiązywania połączenia ze źródłami danych Oracle przy użyciu programu Excel na komputerze z systemem 32-bitowej wersji pakietu Microsoft Office, włącznie z Windows 64-bitowych 32-bitowe sterowniki Oracle będzie służyć
* Podczas nawiązywania połączenia ze źródłami danych Oracle przy użyciu programu Excel na komputerze z systemem 64-bitowej wersji pakietu Microsoft Office, będą używane 64-bitowe sterowniki Oracle

### <a name="registering-and-connecting-to-sql-server-reporting-services"></a>Rejestrowanie i nawiązywania połączenia z SQL Server Reporting Services
Obsługa źródła danych programu SQL Server Reporting Services (SSRS) jest obecnie ograniczona do tylko serwery w trybie macierzystym. Obsługa usług SSRS w trybie programu SharePoint zostanie dodany w nowszej wersji.

### <a name="opening-data-assets-in-excel"></a>Otwieranie zasobów danych w programie Excel
Podczas otwierania zasobów danych w programie Microsoft Excel z **usługi Azure Data Catalog** portalu użytkowników może zostać wyświetlony monit o **powiadomienie o zabezpieczeniach programu Microsoft Excel** okno dialogowe. Jest to standardowy, oczekiwane zachowanie i użytkowników można wybrać **Włącz** aby kontynuować.

Aby uzyskać więcej informacji, zobacz [Włącz lub Wyłącz alerty zabezpieczeń o linkach i pliki z podejrzanych witrynach internetowych](https://support.office.com/article/Enable-or-disable-security-alerts-about-links-and-files-from-suspicious-websites-A1AC6AE9-5C4A-4EB3-B3F8-143336039BBE).

### <a name="proxy-and-policy-configuration-and-data-source-registration"></a>Serwer proxy i zasad konfiguracji i danych rejestracji źródła
Użytkownicy mogą napotkać sytuację, w których mogą oni się zalogować do portalu usługi Azure Data Catalog, ale po podjęciu próby logowania do narzędzia rejestracji źródła danych, mogą wystąpić komunikat o błędzie, który uniemożliwia logowanie.

Istnieją dwa możliwych przyczyn tego problemu zachowania:

**Przyczyny 1: Konfiguracja Active Directory Federation Services** narzędzia rejestracji źródła danych korzysta z uwierzytelniania formularzy do weryfikowania logowania użytkowników w usłudze Active Directory. Do pomyślnego logowania należy włączyć uwierzytelnianie formularzy w ramach globalnych zasad uwierzytelniania przez administratora usługi Active Directory.

W niektórych sytuacjach zachowanie ten błąd może wystąpić tylko wtedy, gdy użytkownik jest w sieci firmowej lub tylko wtedy, gdy użytkownik nawiązuje połączenie z spoza sieci firmowej. Globalne zasady uwierzytelniania umożliwia metod uwierzytelniania, należy włączyć osobno dla połączeń intranetowych i ekstranetowych. Błędy logowania mogą wystąpić, jeśli nie włączono uwierzytelniania formularzy dla sieci, z którego użytkownik nawiązuje połączenie.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad uwierzytelniania](https://technet.microsoft.com/library/dn486781.aspx).

**Przyczyny 2: Konfiguracja serwera proxy sieci** Jeśli sieć firmowa używa serwera proxy, narzędzie do rejestracji nie może być możliwe nawiązanie połączenia z usługi Azure Active Directory za pośrednictwem serwera proxy. Użytkownicy upewnij się, że narzędzie do rejestracji, edytując plik konfiguracji tego narzędzia, dodając w tej sekcji do pliku:

      <system.net>
        <defaultProxy useDefaultCredentials="true" enabled="true">
          <proxy usesystemdefault="True"
                          proxyaddress="http://<your corporate network proxy url>"
                          bypassonlocal="True"/>
        </defaultProxy>
      </system.net>


Aby zlokalizować plik RegistrationTool.exe.config, uruchom narzędzie do rejestracji, a następnie otwórz narzędzie Menedżera zadań Windows. Na karcie Szczegóły w Menedżerze zadań kliknij prawym przyciskiem myszy na RegistrationTool.exe, a następnie wybierz Otwórz lokalizację pliku z menu podręcznego.
