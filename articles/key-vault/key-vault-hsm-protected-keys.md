---
title: Jak wygenerować i przenieść klucze chronione przez moduł HSM dla usługi Azure Key Vault — usługi Azure Key Vault | Dokumentacja firmy Microsoft
description: Przedstawione w tym artykule ułatwiają planowanie, generowanie i następnie przenoszenie własnych kluczy chronionych modułem HSM za pomocą usługi Azure Key Vault. Także znana jako BYOK lub Użyj własnego klucza.
services: key-vault
documentationcenter: ''
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.assetid: 51abafa1-812b-460f-a129-d714fdc391da
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: barclayn
ms.openlocfilehash: 71931194c88412467252d16c5333d7a77338378c
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630616"
---
# <a name="how-to-generate-and-transfer-hsm-protected-keys-for-azure-key-vault"></a>Jak Generowanie i przenoszenie chronionego przez moduł HSM kluczy dla usługi Azure Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Dodano bezpieczeństwa korzystając z usługi Azure Key Vault, można importować lub generować klucze w sprzętowych modułach zabezpieczeń (HSM), które nigdy nie opuszczają modułu HSM. Ten scenariusz jest często nazywany *Użyj własnego klucza*, byok. Moduły HSM są zweryfikowane w trybie FIPS 140-2 poziom 2. Usługa Azure Key Vault używa rodziny nShield firmy Thales sprzętowych modułów zabezpieczeń do ochrony kluczy.

Skorzystaj z informacji w tym temacie, aby ułatwić planowanie, generowanie i następnie przenoszenie własnych kluczy chronionych modułem HSM za pomocą usługi Azure Key Vault.

Ta funkcja nie jest dostępna dla Chińskiej wersji platformy Azure.

> [!NOTE]
> Aby uzyskać więcej informacji na temat usługi Azure Key Vault, zobacz [co to jest usługa Azure Key Vault?](key-vault-whatis.md)  
> Aby uzyskać Samouczek wprowadzający, która obejmuje tworzenie magazynu kluczy dla kluczy chronionych modułem HSM, zobacz [co to jest usługa Azure Key Vault?](key-vault-overview.md).

Więcej informacji na temat Generowanie i przenoszenie klucza chronionego przez moduł HSM za pośrednictwem Internetu:

* Możesz wygenerować klucz w trybie offline stację roboczą, co zmniejsza możliwości ataku.
* Klucz jest zaszyfrowany przy użyciu klucza wymiany klucza (KEK), który pozostaje zaszyfrowany, dopóki nie zostanie przesłany do HSM usługi Azure Key Vault. Wyłącznie zaszyfrowana wersja klucza opuszcza pierwotną stację roboczą.
* Zestaw narzędzi ustawia właściwości klucz dzierżawy, który wiąże klucz zabezpieczeń security world usługi Azure Key Vault. Dlatego po HSM usługi Azure Key Vault odbiorą i odszyfrują klucz, tylko te sprzętowych modułów zabezpieczeń można użyć go. Nie można wyeksportować klucza. To powiązanie jest wymuszone przez sprzętowe moduły zabezpieczeń firmy Thales.
* Klucz wymiany klucza (KEK), który jest używany do szyfrowania klucza jest generowany wewnątrz HSM usługi Azure Key Vault i nie jest możliwy do eksportu. Moduły HSM wymusić, że może być brak wyczyść wersji KEK poza sprzętowe moduły zabezpieczeń. Ponadto zestaw narzędzi zawiera zaświadczenie od firmy Thales potwierdzające, że klucza KEK nie można wyeksportować i został on wygenerowany wewnątrz oryginalnego sprzętowego modułu zabezpieczeń, która została wyprodukowanego przez firmę Thales.
* Zestaw narzędzi zawiera zaświadczenie od firmy Thales potwierdzające, że środowiska zabezpieczeń security world usługi Azure Key Vault zostało także wygenerowane w oryginalnym sprzętowym module zabezpieczeń wyprodukowanym przez firmę Thales. To poświadczenie potwierdza, że firma Microsoft korzysta z oryginalnego sprzętu.
* Firma Microsoft używa oddzielnych kluczy Kek oraz oddzielne środowiska Security World w każdym regionie geograficznym. Ten rozdział zapewnia, że klucz może służyć tylko w centrach danych w regionie, w którym został zaszyfrowany. Na przykład klucz Europejskiego klienta nie może służyć w centrach danych w Ameryce Północnej ani Azji.

## <a name="more-information-about-thales-hsms-and-microsoft-services"></a>Więcej informacji na temat sprzętowych modułów zabezpieczeń firmy Thales i usług firmy Microsoft

Firmy Thales e-Security jest wiodącym globalnym dostawcą szyfrowania danych i cybernetycznymi zabezpieczeń rozwiązania usług finansowych, nowoczesnych, produkcji, instytucji rządowych i sektorów technologii. 40 lat poświadczać ochrony firmowych i informacje dla instytucji rządowych rozwiązania firmy Thales są używane przez pięć największych firm lotnictwa i kosmonautyki i energii. Swoje rozwiązania są również używane przez 22 kraje NATO, a secure ponad 80 procent transakcji płatniczych na świecie.

Firma Microsoft podjęła współpracę z firmą Thales, które zwiększają pogłębić sprzętowych modułów zabezpieczeń. Opracowane udoskonalenia pozwalają uzyskać typowych zalet usług hostowanych bez potrzeby rezygnacji z kontroli nad kluczami. W szczególności ulepszenia te umożliwiają firmie Microsoft Zarządzanie sprzętowe moduły zabezpieczeń, tak aby nie trzeba. Jako usługa w chmurze usługi Azure Key Vault jest skalowany w górę w krótkim terminie w celu spełnienia nagłego zwiększenia zapotrzebowania organizacji. W tym samym czasie klucz jest chroniony wewnątrz sprzętowych modułów zabezpieczeń firmy Microsoft: Zachowuje się kontrolę nad cyklem życia klucza, ponieważ Wygeneruj klucz i przeniesienie go do sprzętowych modułów zabezpieczeń firmy Microsoft.

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Implementowanie Użyj własnego klucza (BYOK) dla usługi Azure Key Vault

Użyj następujące informacje i procedury, jeśli będzie wygenerować własny klucz chroniony przez moduł HSM i przenieść go do usługi Azure Key Vault — dostarczania scenariusza klucza (BYOK).

## <a name="prerequisites-for-byok"></a>Wymagania wstępne dotyczące funkcji BYOK

Znajdują się w następujących tabeli, aby uzyskać listę wymagań wstępnych dotyczących Użyj własnego klucza (BYOK) dla usługi Azure Key Vault.

| Wymaganie | Więcej informacji |
| --- | --- |
| Subskrypcja platformy Azure |Do utworzenia usługi Azure Key Vault, musisz mieć subskrypcję platformy Azure: [Zamów bezpłatną wersję próbną](https://azure.microsoft.com/pricing/free-trial/) |
| Warstwy usługi Azure Key Vault — wersja Premium do obsługi kluczy chronionych przez moduł HSM |Aby uzyskać więcej informacji na temat warstw usługi i możliwości usługi Azure Key Vault, zobacz [cenami usługi Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) witryny sieci Web. |
| Modułu HSM firmy Thales, karty inteligentne i oprogramowanie |Musi mieć dostęp do sprzętowego modułu zabezpieczeń firmy Thales i podstawowe wiedzę na temat działania sprzętowych modułów zabezpieczeń firmy Thales. Zobacz [sprzętowego modułu zabezpieczeń firmy Thales](https://www.thales-esecurity.com/msrms/buy) lista zgodnych modeli lub kupić modułu HSM, jeśli nie masz. |
| Następujący sprzęt i oprogramowanie:<ol><li>W trybie offline x64 stacja robocza z minimalny system operacyjny Windows systemu Windows 7 oraz oprogramowaniem Thales nShield co najmniej wersji 11.50.<br/><br/>W przypadku stacji roboczych z systemem Windows 7, należy [instalacja programu Microsoft .NET Framework 4.5](https://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe).</li><li>Stacji roboczej, który jest połączony z Internetem i ma system operacyjny Windows Windows 7 i [programu Azure PowerShell](/powershell/azure/overview?view=azps-1.2.0) **minimalna wersja 1.1.0** zainstalowane.</li><li>Lub inne przenośne urządzenie pamięci masowej z co najmniej 16 MB wolnego miejsca na dysku USB.</li></ol> |Ze względów bezpieczeństwa zaleca się, że pierwszej stacji roboczej nie jest połączony z siecią. Jednak to zalecenie jest nie ograniczenia natury programistycznej.<br/><br/>W instrukcji tą stacją roboczą nazywa się rozłączonej stacji roboczej.</p></blockquote><br/>Ponadto jeśli klucz dzierżawy jest dla środowiska produkcyjnego, zaleca się użyć drugiej, oddzielnej stacji roboczej, aby pobrać zestaw narzędzi i przesłania klucza dzierżawy. Jednak do celów testowych możesz użyć tej samej stacji roboczej jako pierwsza z nich.<br/><br/>W instrukcji druga stacja robocza jest nazywany stacji roboczej podłączonej do Internetu.</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Generowanie i przeniesienia klucza usługi Azure Key Vault

Użyjesz następujące pięć kroków, aby wygenerować i przenieść swój klucz do modułu HSM usługi Azure Key Vault:

* [Krok 1. Przygotowanie stacji roboczej podłączonej do Internetu](#step-1-prepare-your-internet-connected-workstation)
* [Krok 2. Przygotowanie odłączonej stacji roboczej](#step-2-prepare-your-disconnected-workstation)
* [Krok 3. Generowanie klucza](#step-3-generate-your-key)
* [Krok 4. Przygotowanie klucza do przesłania](#step-4-prepare-your-key-for-transfer)
* [Krok 5. Przesłanie klucza do usługi Azure Key Vault](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>Krok 1: Przygotowanie stacji roboczej podłączonej do Internetu

To pierwszy krok wykonaj następujące procedury na stacji roboczej podłączonej do Internetu.

### <a name="step-11-install-azure-powershell"></a>Krok 1.1: Instalowanie programu Azure PowerShell

Ze stacji roboczej podłączonej do Internetu należy pobrać i zainstalować moduł programu Azure PowerShell, który zawiera polecenia cmdlet do zarządzania usługi Azure Key Vault. Aby uzyskać instrukcje dotyczące instalacji, zobacz [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview).

### <a name="step-12-get-your-azure-subscription-id"></a>Krok 1.2: Pobierz swój identyfikator subskrypcji platformy Azure

Uruchom sesję programu Azure PowerShell, a następnie zaloguj się do konta platformy Azure przy użyciu następującego polecenia:

```Powershell
   Connect-AzAccount
```
W podręcznym oknie przeglądarki wprowadź nazwę użytkownika i hasło dla konta platformy Azure. Następnie należy użyć [Get AzSubscription](/powershell/module/az.accounts/get-azsubscription) polecenia:

```powershell
   Get-AzSubscription
```
Z danych wyjściowych zlokalizuj identyfikator subskrypcji, które będą używane dla usługi Azure Key Vault. Ten identyfikator subskrypcji będzie potrzebny później.

Nie zamykaj okna programu Azure PowerShell.

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>Krok 1.3. Pobierz zestaw narzędzi BYOK dla usługi Azure Key Vault

Przejdź do witryny Microsoft Download Center i [pobrania zestawu narzędzi rozwiązania BYOK usługi Azure Key Vault](https://www.microsoft.com/download/details.aspx?id=45345) dla regionu geograficznego lub wystąpienie platformy Azure. Aby zidentyfikować nazwę pakietu do pobrania i jego odpowiedniego skrótu pakietu algorytmu SHA-256, należy użyć następujących informacji:

- - -
**Stany Zjednoczone:**

KeyVault-BYOK-Tools-UnitedStates.zip

2E8C00320400430106366A4E8C67B79015524E4EC24A2D3A6DC513CA1823B0D4

- - -
**Europa:**

KeyVault-BYOK-Tools-Europe.zip

9AAA63E2E7F20CF9BB62485868754203721D2F88D300910634A32DFA1FB19E4A

- - -
**Azja Wschodnia:**

KeyVault-BYOK-Tools-AsiaPacific.zip

4BC14059BF0FEC562CA927AF621DF665328F8A13616F44C977388EC7121EF6B5

- - -
**Ameryka Łacińska:**

KeyVault-BYOK-Tools-LatinAmerica.zip

E7DFAFF579AFE1B9732C30D6FD80C4D03756642F25A538922DD1B01A4FACB619

- - -
**Japonia:**

KeyVault-BYOK-Tools-Japan.zip

3933C13CC6DC06651295ADC482B027AF923A76F1F6BF98B4D4B8E94632DEC7DF

- - -
**Korea:**

KeyVault-BYOK-Tools-Korea.zip

71AB6BCFE06950097C8C18D532A9184BEF52A74BB944B8610DDDA05344ED136F

- - -
**Republika Południowej Afryki:**

KeyVault-BYOK-Tools-SouthAfrica.zip

C41060C5C0170AAAAD896DA732E31433D14CB9FC83AC3C67766F46D98620784A

- - -
**ZJEDNOCZONE EMIRATY ARABSKIE:**

KeyVault-BYOK-Tools-UAE.zip

FADE80210B06962AA0913EA411DAB977929248C65F365FD953BB9F241D5FC0D3

- - -
**Australia:**

KeyVault-BYOK-Tools-Australia.zip

CD0FB7365053DEF8C35116D7C92D203C64A3D3EE2452A025223EEB166901C40A

- - -
[**Platforma Azure Government:**](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-Tools-USGovCloud.zip

F8DB2FC914A7360650922391D9AA79FF030FD3048B5795EC83ADC59DB018621A

- - -
**Instytucje rządowe USA — DOD:**

KeyVault-BYOK-Tools-USGovernmentDoD.zip

A79DD8C6DFFF1B00B91D1812280207A205442B3DDF861B79B8B991BB55C35263

- - -
**Kanada:**

KeyVault-BYOK-Tools-Canada.zip

61BE1A1F80AC79912A42DEBBCC42CF87C88C2CE249E271934630885799717C7B

- - -
**Niemcy:**

KeyVault-BYOK-Tools-Germany.zip

5385E615880AAFC02AFD9841F7BADD025D7EE819894AA29ED3C71C3F844C45D6

- - -
**Indie:**

KeyVault-BYOK-Tools-India.zip

49EDCEB3091CF1DF7B156D5B495A4ADE1CFBA77641134F61B0E0940121C436C8

- - -
**Francja:**

KeyVault-BYOK-Tools-France.zip

5C9D1F3E4125B0C09E9F60897C9AE3A8B4CB0E7D13A14F3EDBD280128F8FE7DF

- - -
**Wielka Brytania:**

KeyVault-BYOK-Tools-UnitedKingdom.zip

432746BD0D3176B708672CCFF19D6144FCAA9E5EB29BB056489D3782B3B80849

- - -

Aby sprawdzić integralność swoje pobranego zestawu narzędzi rozwiązania BYOK, z poziomu sesji programu Azure PowerShell należy użyć [Get FileHash](https://technet.microsoft.com/library/dn520872.aspx) polecenia cmdlet.

   ```powershell
   Get-FileHash KeyVault-BYOK-Tools-*.zip
   ```

Zestaw narzędzi zawiera:

* Pakiet klucza wymiany klucza (KEK), którego nazwa zaczyna się od **BYOK-KEK - pkg-.**
* Pakiet środowiska zabezpieczeń Security World, którego nazwa zaczyna się od **BYOK-SecurityWorld - pkg-.**
* Skrypt w języku python o nazwie **verifykeypackage.py.**
* Plik wykonywalny wiersza polecenia o nazwie **KeyTransferRemote.exe** oraz powiązane biblioteki dll.
* Pakiet redystrybucyjny Visual C++, o nazwie **vcredist_x64.exe.**

Skopiuj pakiet na dysku USB lub innego przenośnego urządzenia pamięci masowej.

## <a name="step-2-prepare-your-disconnected-workstation"></a>Krok 2: Przygotowanie odłączonej stacji roboczej

Ten drugi krok wykonaj następujące procedury na stacji roboczej, który nie jest podłączony do sieci (Internetu lub sieci wewnętrznej).

### <a name="step-21-prepare-the-disconnected-workstation-with-thales-hsm"></a>Krok 2.1. Przygotowanie odłączonej stacji roboczej za pomocą modułu HSM firmy Thales

Zainstaluj oprogramowanie wspomagające nCipher (firmy Thales) na komputerze Windows, a następnie dołącz HSM firmy Thales do tego komputera.

Upewnij się, że narzędzia firmy Thales znajdują się w ścieżce (**%nfast_home%\bin**). Na przykład wpisz następujące polecenie:

  ```cmd
  set PATH=%PATH%;"%nfast_home%\bin"
  ```

Aby uzyskać więcej informacji zobacz w podręczniku użytkownika dołączonym do modułu HSM firmy Thales.

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>Krok 2.2. Zainstaluj zestaw narzędzi BYOK na odłączonej stacji roboczej

Skopiuj pakiet zestawu narzędzi BYOK z dysku USB lub innego przenośnego urządzenia pamięci masowej, a następnie wykonaj następujące czynności:

1. Wyodrębnij pliki z pobranego pakietu do dowolnego folderu.
2. Z tego folderu Uruchom vcredist_x64.exe.
3. Postępuj zgodnie z instrukcjami instalacji składników środowiska wykonawczego Visual C++ dla Visual Studio 2013.

## <a name="step-3-generate-your-key"></a>Krok 3: Generowanie klucza

To trzeci krok wykonaj następujące procedury na rozłączonej stacji roboczej. Aby ukończyć ten krok modułu HSM musi być w trybie inicjowania. 

### <a name="step-31-change-the-hsm-mode-to-i"></a>Krok 3.1. Zmień tryb sprzętowego modułu zabezpieczeń na wartość "I"

Jeśli używasz nShield firmy Thales Edge, aby zmienić tryb: 1. Użyj przycisku tryb, aby wyróżnić wymagane trybu. 2. W ciągu kilku sekund naciśnij i przytrzymaj przycisk Wyczyść przez kilka sekund. Zmiana trybu LED nowy tryb zatrzymuje migające i świeci. LED stanu może być nieregularnie flash w kilka sekund i następnie miga regularnie, gdy urządzenie jest gotowe. W przeciwnym razie urządzenia pozostaje w bieżącym trybie, za pomocą odpowiedni tryb LED świeci.

### <a name="step-32-create-a-security-world"></a>Krok 3.2 Tworzenie środowiska zabezpieczeń security world

Uruchom wiersz polecenia, a następnie uruchom program nowym świecie firmy Thales.

   ```cmd
    new-world.exe --initialize --cipher-suite=DLf3072s256mRijndael --module=1 --acs-quorum=2/3
   ```

Ten program tworzy **środowiska zabezpieczeń Security World** plik w lokalizacji % NFAST_KMDATA%\local\world, co odnosi się do folderu C:\ProgramData\nCipher\Key Management Data\local. Można użyć różnych wartości dla kworum, ale w tym przykładzie zostanie wyświetlony monit wpisz trzech pustych kart oraz kodów PIN dla każdego z nich. Następnie dowolne dwie spośród kart nadania praw pełnego dostępu do środowiska zabezpieczeń security world. Te karty stają się **zestaw kart administratora** dla nowego środowiska zabezpieczeń security world.

> [!NOTE]
> Jeśli modułu HSM nie obsługuje nowszych DLf3072s256mRijndael pakietu szyfrowania, możesz zastąpić — mechanizmów szyfrowania = DLf3072s256mRijndael with--mechanizmów szyfrowania = DLf1024s160mRijndael

Następnie wykonaj poniższe czynności:

* Utwórz kopię zapasową pliku środowiska zabezpieczeń. Zabezpieczanie ochrony pliku środowiska zabezpieczeń, karty administratora oraz ich kody PIN i upewnij się, że nikt nie ma dostępu do więcej niż jedną kartę.

### <a name="step-33-change-the-hsm-mode-to-o"></a>Krok 3.3. Zmień tryb przez moduł HSM, aby ' O'

Jeśli używasz nShield firmy Thales Edge, aby zmienić tryb: 1. Użyj przycisku tryb, aby wyróżnić wymagane trybu. 2. W ciągu kilku sekund naciśnij i przytrzymaj przycisk Wyczyść przez kilka sekund. Zmiana trybu LED nowy tryb zatrzymuje migające i świeci. LED stanu może być nieregularnie flash w kilka sekund i następnie miga regularnie, gdy urządzenie jest gotowe. W przeciwnym razie urządzenia pozostaje w bieżącym trybie, za pomocą odpowiedni tryb LED świeci.

### <a name="step-34-validate-the-downloaded-package"></a>Krok 3.4: Zweryfikować pobrany pakiet

Ten krok jest opcjonalny, ale też zalecany, dzięki czemu można sprawdzić poprawność następujących:

* Klucz wymiany klucza, który znajduje się w zestawie narzędzi został wygenerowany w oryginalnym module HSM firmy Thales.
* Skrót środowiska zabezpieczeń Security World, który znajduje się w zestawie narzędzi został wygenerowany w oryginalnym module HSM firmy Thales.
* Nie można eksportować jest klucz wymiany klucza.

> [!NOTE]
> Aby zweryfikować pobrany pakiet, moduł HSM musi być podłączony i włączony i musi mieć środowiska zabezpieczeń security world go (na przykład tego, który właśnie został utworzony).

Aby zweryfikować pobrany pakiet:

1. Uruchom skrypt verifykeypackage.py, wpisując jeden z poniższych pozycji w zależności od regionu geograficznego lub wystąpienie platformy Azure:

   * Dla Ameryki Północnej:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
   * Dla Europy:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
   * Azja:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
   * W Ameryce Łacińskiej:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
   * W Japonii:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
   * W przypadku Korei:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-KOREA-1 -w BYOK-SecurityWorld-pkg-KOREA-1
   * Aby uzyskać Republika Południowej Afryki:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SA-1 -w BYOK-SecurityWorld-pkg-SA-1
   * Aby uzyskać Zjednoczone Emiraty Arabskie:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UAE-1 -w BYOK-SecurityWorld-pkg-UAE-1
   * Dla Australii:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
   * Dla [Azure dla instytucji rządowych](https://azure.microsoft.com/features/gov/), który używa wystąpienie dla instytucji rządowych USA platformy Azure:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
   * Dla instytucji rządowych USA — DOD:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
   * Dla Kanady:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
   * Niemcy:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * Aby uzyskać Indii:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
   * Francja:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-FRANCE-1 -w BYOK-SecurityWorld-pkg-FRANCE-1
   * Zjednoczone Królestwo:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UK-1 -w BYOK-SecurityWorld-pkg-UK-1

     > [!TIP]
     > Oprogramowanie firmy Thales obejmuje języka python w %NFAST_HOME%\python\bin
     >
     >
2. Upewnij się, że zobaczysz następujący widok, co oznacza pomyślnej weryfikacji: **Wynik: SUCCESS**

Ten skrypt sprawdza łańcuch osoby podpisującej aż klucza głównego firmy Thales. Skrót klucza głównego jest osadzony w skrypcie i jej wartość powinna być **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. Można również potwierdzić tę wartość osobno, odwiedzając [witryny sieci Web firmy Thales](http://www.thalesesec.com/).

Teraz możesz przystąpić do tworzenia nowego klucza.

### <a name="step-35-create-a-new-key"></a>Krok 3.5. Utwórz nowy klucz

Generowanie klucza za pomocą firmy Thales **do generowania kluczy** program.

Uruchom następujące polecenie, aby wygenerować klucz:

    generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

Po uruchomieniu tego polecenia, użyj tych instrukcji:

* Parametr *chronić* musi być ustawione na wartość **modułu**, jak pokazano. Spowoduje to utworzenie klucza chronionego przez moduł. Zestaw narzędzi BYOK nie obsługuje kluczy chronionych przez serwer OCS.
* Zastąp wartość *contosokey* dla **ident** i **plainname** z dowolną wartością ciągu. Aby zminimalizować ogólne koszty administracyjne i zmniejszyć ryzyko błędów, zaleca się używać tej samej wartości dla obu. **Ident** wartość musi zawierać tylko cyfry, łączniki i małe litery.
* Parametr pubexp został pozostawiony pusty (ustawienie domyślne), w tym przykładzie, ale można określić konkretne jego wartości. Aby uzyskać więcej informacji zobacz dokumentację firmy Thales.

To polecenie tworzy plik Stokenizowanego klucza w folderze %NFAST_KMDATA%\local za pomocą nazwę zaczynającą się od **key_simple_**, a następnie **ident** podany w poleceniu. Na przykład: **key_simple_contosokey**. Ten plik zawiera zaszyfrowany klucz.

Utwórz kopię zapasową tego pliku Stokenizowanego klucza w bezpiecznym miejscu.

> [!IMPORTANT]
> Po późniejszym przesłaniu klucza do usługi Azure Key Vault, Microsoft nie może wyeksportować tego klucza do Ciebie, staje się ona bardzo ważne, bezpieczne przechowywanie kopii zapasowej Twojego klucza oraz środowiska zabezpieczeń. Aby uzyskać wskazówki i najlepsze rozwiązania dotyczące tworzenia kopii zapasowej klucza, skontaktuj się z pomocą firmy Thales.
>


Teraz można przystąpić do przenoszenia klucza do usługi Azure Key Vault.

## <a name="step-4-prepare-your-key-for-transfer"></a>Krok 4: Przygotowanie klucza do przesłania

Ten krok czwarty wykonaj następujące procedury na rozłączonej stacji roboczej.

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>Krok 4.1: Tworzenie kopii klucza z ograniczonymi uprawnieniami

Otwórz nowy wiersz polecenia i zmień bieżący katalog do lokalizacji, w którym rozpakowano plik zip BYOK. Aby ograniczyć uprawnienia do tego klucza, z poziomu wiersza polecenia, uruchom jedną z następujących czynności, w zależności od regionu geograficznego lub wystąpienie platformy Azure:

* Dla Ameryki Północnej:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
* Dla Europy:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
* Azja:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
* W Ameryce Łacińskiej:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
* W Japonii:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
* W przypadku Korei:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1
* Aby uzyskać Republika Południowej Afryki:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1
* Aby uzyskać Zjednoczone Emiraty Arabskie:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1
* Dla Australii:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
* Dla [Azure dla instytucji rządowych](https://azure.microsoft.com/features/gov/), który używa wystąpienie dla instytucji rządowych USA platformy Azure:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
* Dla instytucji rządowych USA — DOD:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
* Dla Kanady:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
* Niemcy:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* Aby uzyskać Indii:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1
* Francja:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-FRANCE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-FRANCE-1
* Zjednoczone Królestwo:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1

Podczas uruchamiania tego polecenia Zastąp *contosokey* z taką samą wartość podana w **krok 3.5: Utwórz nowy klucz** z [wygenerowanie własnego klucza](#step-3-generate-your-key) kroku.

Zostanie wyświetlony monit monit o podłączenie kart administratora zabezpieczeń świata.

Po zakończeniu wykonywania polecenia zostanie wyświetlony **wynik: Powodzenie** a kopia klucza z ograniczonymi uprawnieniami znajdują się w pliku o nazwie key_xferacId_<contosokey>.

Może być bada, listy ACL za pomocą następującego polecenia, używając narzędzi firmy Thales:

* aclprint.py:

        "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
* kmfile-dump.exe:

        "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
  Po uruchomieniu tych poleceń Zastąp contosokey tę samą wartość, określona w **krok 3.5: Utwórz nowy klucz** z [wygenerowanie własnego klucza](#step-3-generate-your-key) kroku.

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>Krok 4.2 Zaszyfrowanie klucza przy użyciu klucza wymiany klucza firmy Microsoft

Uruchom jedno z następujących poleceń w zależności od regionu geograficznego lub wystąpienie platformy Azure:

* Dla Ameryki Północnej:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Dla Europy:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Azja:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* W Ameryce Łacińskiej:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* W Japonii:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* W przypadku Korei:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Aby uzyskać Republika Południowej Afryki:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Aby uzyskać Zjednoczone Emiraty Arabskie:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Dla Australii:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Dla [Azure dla instytucji rządowych](https://azure.microsoft.com/features/gov/), który używa wystąpienie dla instytucji rządowych USA platformy Azure:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Dla instytucji rządowych USA — DOD:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Dla Kanady:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Niemcy:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Aby uzyskać Indii:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Francja:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-France-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-France-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Zjednoczone Królestwo:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey

Po uruchomieniu tego polecenia, użyj tych instrukcji:

* Zastąp *contosokey* z identyfikatorem użytym do wygenerowania klucza w **krok 3.5: Utwórz nowy klucz** z [wygenerowanie własnego klucza](#step-3-generate-your-key) kroku.
* Zastąp *SubscriptionID* z Identyfikatorem subskrypcji platformy Azure, która zawiera Twój magazyn kluczy. Możesz pobrać tę wartość poprzedniej wersji programu **opisem kroku 1.2: Pobierz swój identyfikator subskrypcji platformy Azure** z [Przygotowanie stacji roboczej podłączonej do Internetu](#step-1-prepare-your-internet-connected-workstation) kroku.
* Zastąp *ContosoFirstHSMKey* etykietą, która jest używana do nazwy pliku wyjściowego.

Po zakończeniu tego procesu pomyślnie, wyświetla **wynik: Powodzenie** i nowy plik w bieżącym folderze, który ma następującą nazwę: KeyTransferPackage-*ContosoFirstHSMkey*.byok

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>Krok 4.3: Kopiowanie pakietu przekazywania klucza do stacji roboczej podłączonej do Internetu

Aby skopiować plik wyjściowy z poprzedniego kroku (KeyTransferPackage-ContosoFirstHSMkey.byok) do stacji roboczej podłączonej do Internetu, należy użyć dysku USB lub innego przenośnego urządzenia pamięci masowej.

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>Krok 5. Przesłanie klucza do usługi Azure Key Vault

W tym ostatnim kroku na stacji roboczej podłączonej do Internetu, użyj [AzKeyVaultKey Dodaj](/powershell/module/az.keyvault/add-azkeyvaultkey) polecenia cmdlet, aby przesłać pakiet przekazywania klucza, który został skopiowany z odłączonej stacji roboczej do magazynu Azure klucza sprzętowego modułu zabezpieczeń:

   ```powershell
        Add-AzKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'
   ```

Jeśli przekazywanie zakończy się pomyślnie, zobaczysz wyświetlone właściwości klucza, który właśnie został dodany.

## <a name="next-steps"></a>Kolejne kroki

Można teraz używać tego klucza chronionego przez moduł HSM w magazynie kluczy. Aby uzyskać więcej informacji, zobacz tej funkcji i cen [porównania](https://azure.microsoft.com/pricing/details/key-vault/).
