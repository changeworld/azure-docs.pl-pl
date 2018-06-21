---
title: Jak Generowanie i przenoszenie kluczy chronionych modułem HSM dla usługi Azure Key Vault | Dokumentacja firmy Microsoft
description: Użyj w tym artykule, aby ułatwić planowanie, generowanie, a następnie przenieść własne klucze chronione przez moduł HSM do użycia z usługą Azure Key Vault. Znany również jako BYOK lub własnego klucza.
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 51abafa1-812b-460f-a129-d714fdc391da
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: barclayn
ms.openlocfilehash: 5f7cccfe0288b547f84d5642c46c21ab4095a7bf
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287725"
---
# <a name="how-to-generate-and-transfer-hsm-protected-keys-for-azure-key-vault"></a>Jak Generowanie i przenoszenie chronionego przez moduł HSM kluczy dla usługi Azure Key Vault
## <a name="introduction"></a>Wprowadzenie
Dla dodatkowego bezpieczeństwa korzystając z usługi Azure Key Vault, można zaimportować lub wygenerować klucze w sprzętowych modułów zabezpieczeń (HSM), które nigdy nie opuszczają granicy modułów HSM. Ten scenariusz jest często określany jako *własnego klucza*, byok. Moduły HSM są zweryfikowane w trybie FIPS 140-2 poziom 2. Usługa Azure Key Vault używa rodziny nShield firmy Thales sprzętowych modułów zabezpieczeń do ochrony kluczy.

Skorzystaj z informacji w tym temacie, aby ułatwić planowanie, generowanie, a następnie przenieść własne klucze chronione przez moduł HSM do użycia z usługą Azure Key Vault.

Ta funkcja nie jest dostępna dla Chińskiej wersji platformy Azure.

> [!NOTE]
> Aby uzyskać więcej informacji na temat usługi Azure Key Vault, zobacz [co to jest usługa Azure Key Vault?](key-vault-whatis.md)  
>
> Pobieranie samouczek Wprowadzenie, łącznie z tworzeniem magazynu kluczy dla kluczy chronionych modułem HSM, zobacz [wprowadzenie do usługi Azure Key Vault](key-vault-get-started.md).
>
>

Więcej informacji na temat Generowanie i przenoszenie klucza chronionego przez moduł HSM za pośrednictwem Internetu:

* Generowania klucza w trybie offline stację roboczą, co zmniejsza możliwości ataku.
* Klucz jest zaszyfrowany z klucza wymiany klucza (KEK), który pozostaje zaszyfrowany, dopóki nie zostanie przesłany do modułów HSM usługi Azure Key Vault. Wyłącznie zaszyfrowana wersja klucza opuszcza pierwotną stację roboczą.
* Zestaw narzędzi ustawia właściwości klucz dzierżawy, który wiąże klucz do środowiska zabezpieczeń security world usługi Azure Key Vault. Dlatego po modułów HSM usługi Azure Key Vault odbiorą i odszyfrują klucz, tylko te moduły HSM może być używany. Nie można wyeksportować klucza. To powiązanie jest wymuszone przez moduły HSM firmy Thales.
* Klucz wymiany klucza (KEK), który jest używany do szyfrowania klucza jest generowany wewnątrz modułów HSM usługi Azure Key Vault i nie jest możliwy do eksportu. Sprzętowe moduły zabezpieczeń wymusić nie był dostępny nie ma wersji wyczyść KEK poza ich obrębem. Ponadto zestaw narzędzi zawiera zaświadczenie od firmy Thales, że klucza KEK nie można wyeksportować i został wygenerowany w oryginalnym module HSM, który wyprodukowanego przez firmę Thales.
* Zestaw narzędzi zawiera zaświadczenie od firmy Thales, że środowiska zabezpieczeń security world usługi Azure Key Vault zostało także wygenerowane w oryginalnym sprzętowym module zabezpieczeń wyprodukowanym przez firmę Thales. To poświadczenie gwarantuje, że firma Microsoft korzysta z oryginalnego sprzętu.
* Firma Microsoft używa oddzielnych kluczy Kek i oddzielne środowiska Security World w każdym regionie geograficznym. Ta separacja gwarantuje, że klucz może służyć wyłącznie w centrach danych w regionie, w którym został zaszyfrowany. Na przykład klucz Europejskiego klienta nie można użyć w centrach danych w Ameryce Północnej ani Azji.

## <a name="more-information-about-thales-hsms-and-microsoft-services"></a>Więcej informacji na temat sprzętowych modułów zabezpieczeń firmy Thales i usług firmy Microsoft
Firmy Thales e-Security jest wiodącym globalnym dostawcą szyfrowania danych i rozwiązań zabezpieczeń przez branży usług finansowych, zaawansowanych technologii, produkcyjnym, dla instytucji rządowych i sektorów technologii. 40 lat udokumentowanego doświadczenia ochrony firmy i informacje dla instytucji rządowych że rozwiązania firmy Thales są używane przez cztery pięć największych firm z sektora energetycznego i aerospace. Ich rozwiązania są również używane przez 22 kraje NATO, a secure ponad 80 procent transakcji płatniczych na świecie.

Firma Microsoft podjęła współpracę z firmą Thales, aby pogłębić wiedzę do sprzętowych modułów zabezpieczeń. Te ulepszenia umożliwiają uzyskanie z typowych zalet usług hostowanych bez potrzeby rezygnacji z kontroli nad kluczami. W szczególności ulepszenia te umożliwiają firmie Microsoft Zarządzanie modułów HSM, dzięki czemu nie trzeba. Usługa w chmurze usługi Azure Key Vault skaluje w krótkim czasie w celu zaspokojenia nagłego Twojej organizacji. W tym samym czasie, klucz jest chroniony wewnątrz sprzętowych modułów zabezpieczeń firmy Microsoft: użytkownik zachowuje kontrolę nad cyklu życia klucza, ponieważ klucz wygenerowany i przenieść ją do sprzętowych modułów zabezpieczeń firmy Microsoft.

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Implementowanie Użyj własnego klucza (BYOK) dla usługi Azure Key Vault
Użyj następujące informacje i procedury, jeśli będzie generowanie własnego klucza chronionego przez moduł HSM, a następnie przenieś ją do usługi Azure Key Vault — bring scenariusz klucza (BYOK).

## <a name="prerequisites-for-byok"></a>Wymagania wstępne dotyczące funkcji BYOK
Zobacz poniższą tabelę na listę warunków wstępnych, Użyj własnego klucza (BYOK) dla usługi Azure Key Vault.

| Wymaganie | Więcej informacji |
| --- | --- |
| Subskrypcja platformy Azure |Do utworzenia magazynu kluczy Azure, potrzebujesz subskrypcji platformy Azure: [utworzyć konto bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/) |
| Warstwy usług Premium magazynu kluczy Azure do obsługi klucze chronione przez moduł HSM |Aby uzyskać więcej informacji o warstwy usług i możliwości usługi Azure Key Vault, zobacz [cennik usługi Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) witryny sieci Web. |
| Modułu HSM firmy Thales, karty inteligentne i oprogramowanie |Musi mieć dostęp do sprzętowego modułu zabezpieczeń firmy Thales i podstawowe wiedzę na temat działania sprzętowych modułów zabezpieczeń firmy Thales. Zobacz [sprzętowego modułu zabezpieczeń firmy Thales](https://www.thales-esecurity.com/msrms/buy) listę zgodnych modeli lub w celu zakupu sprzętowego, jeśli nie istnieje. |
| Następujący sprzęt i oprogramowanie:<ol><li>W trybie offline x64 stacja robocza z systemem operacyjnym Windows z systemem Windows 7 oraz oprogramowaniem Thales nShield w wersji 11.50.<br/><br/>Jeśli stacja robocza z systemem Windows 7, należy najpierw [instalacja programu Microsoft .NET Framework 4.5](http://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe).</li><li>Stacji roboczej, który jest połączony z Internetem i ma minimalny system operacyjny Windows, Windows 7 i [programu Azure PowerShell](/powershell/azure/overview) **minimalna wersja 1.1.0** zainstalowane.</li><li>Dysk USB lub inne przenośne urządzenie pamięci masowej z co najmniej 16 MB wolnego miejsca.</li></ol> |Ze względów bezpieczeństwa zaleca się, że pierwszej stacji roboczej nie jest podłączony do sieci. Jednak to zalecenie nie jest programowo wymuszana.<br/><br/>Należy pamiętać, że w postępuj zgodnie z instrukcjami stacja robocza jest określana jako odłączonej stacji roboczej.</p></blockquote><br/>Ponadto jeśli klucz dzierżawy jest przeznaczony dla środowiska produkcyjnego, zaleca się użyć drugiej, oddzielnej stacji roboczej do pobrania zestawu narzędzi i przesłania klucza dzierżawy. Jednak do celów testowych, można użyć tej samej stacji roboczej, jako pierwsza z nich.<br/><br/>Należy pamiętać, że w postępuj zgodnie z instrukcjami druga stacja robocza jest określana jako stacji roboczej podłączonej do Internetu.</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Generowanie i przenoszenie klucza do modułu HSM usługi Azure Key Vault
Poniższe kroki pięć umożliwia generowanie i przenoszenie klucza do modułu HSM usługi Azure Key Vault:

* [Krok 1: Przygotowanie stacji roboczej podłączonej do Internetu](#step-1-prepare-your-internet-connected-workstation)
* [Krok 2: Przygotowanie odłączonej stacji roboczej](#step-2-prepare-your-disconnected-workstation)
* [Krok 3: Generowanie klucza](#step-3-generate-your-key)
* [Krok 4: Przygotowanie klucza do przeniesienia](#step-4-prepare-your-key-for-transfer)
* [Krok 5: Przesłanie klucza do usługi Azure Key Vault](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>Krok 1: Przygotowanie stacji roboczej podłączonej do Internetu
Pierwszym kroku wykonaj następujące procedury na stacji roboczej, który jest połączony z Internetem.

### <a name="step-11-install-azure-powershell"></a>Krok 1.1: Instalowanie programu Azure PowerShell
Od stacji roboczej podłączonej do Internetu Pobierz i zainstaluj moduł Azure PowerShell, który zawiera polecenia cmdlet do zarządzania usługą Azure Key Vault. Wymaga minimalnej wersji 0.8.13.

Aby uzyskać instrukcje instalacji, zobacz [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

### <a name="step-12-get-your-azure-subscription-id"></a>Krok 1.2: Pobieranie identyfikator subskrypcji platformy Azure
Uruchom sesję programu PowerShell Azure i zaloguj się do konta platformy Azure przy użyciu następującego polecenia:

```Powershell
   Add-AzureAccount
```
W podręcznym oknie przeglądarki wprowadź nazwę użytkownika i hasło dla konta platformy Azure. Następnie należy użyć [Get-AzureSubscription](/powershell/module/azure/get-azuresubscription?view=azuresmps-3.7.0) polecenia:

```powershell
   Get-AzureSubscription
```
Z danych wyjściowych zlokalizuj identyfikator subskrypcji, które będą używane dla usługi Azure Key Vault. Ta Subskrypcja będzie potrzebny później.

Nie zamykaj okna programu Azure PowerShell.

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>Krok 1.3: Pobranie zestawu narzędzi BYOK dla usługi Azure Key Vault
Przejdź do witryny Microsoft Download Center i [pobrania zestawu narzędzi BYOK magazynu kluczy Azure](http://www.microsoft.com/download/details.aspx?id=45345) regionu geograficznego lub wystąpienia platformy Azure. Aby zidentyfikować nazwę pakietu do pobrania i jego odpowiedniego skrótu pakietu algorytmu SHA-256, należy użyć następujących informacji:

- - -
**Stany Zjednoczone:**

States.zip KeyVault-BYOK-Tools Zjednoczone

2E8C00320400430106366A4E8C67B79015524E4EC24A2D3A6DC513CA1823B0D4

- - -
**Europa:**

KeyVault-BYOK-Tools-Europe.zip

9AAA63E2E7F20CF9BB62485868754203721D2F88D300910634A32DFA1FB19E4A

- - -
**Azja:**

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
**Australia:**

KeyVault-BYOK-Tools-Australia.zip

CD0FB7365053DEF8C35116D7C92D203C64A3D3EE2452A025223EEB166901C40A

- - -
[**Azure dla instytucji rządowych:**](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-Tools-USGovCloud.zip

F8DB2FC914A7360650922391D9AA79FF030FD3048B5795EC83ADC59DB018621A

- - -
**Dla instytucji rządowych USA DOD:**

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

Aby zweryfikować integralność z pobranego zestawu narzędzi BYOK, w sesji programu Azure PowerShell, użyj [Get-FileHash](https://technet.microsoft.com/library/dn520872.aspx) polecenia cmdlet.

   ```powershell
   Get-FileHash KeyVault-BYOK-Tools-*.zip
   ```

Zestaw narzędzi zawiera następujące czynności:

* Pakiet klucza wymiany klucza (KEK), którego nazwa zaczyna się od **BYOK-KEK - pkg-.**
* Pakiet środowiska zabezpieczeń Security World, którego nazwa zaczyna się od **BYOK-SecurityWorld - pkg-.**
* Skrypt w języku python o nazwie **verifykeypackage.py.**
* Plik wykonywalny wiersza polecenia o nazwie **KeyTransferRemote.exe** oraz skojarzone biblioteki dll.
* Pakiet redystrybucyjny Visual C++, o nazwie **vcredist_x64.exe.**

Skopiuj pakiet na dysk USB lub innego przenośnego urządzenia pamięci masowej.

## <a name="step-2-prepare-your-disconnected-workstation"></a>Krok 2: Przygotowanie odłączonej stacji roboczej
Ten krok drugi wykonaj następujące procedury na stacji roboczej, która nie jest podłączony do sieci (Internetu lub sieci wewnętrznej).

### <a name="step-21-prepare-the-disconnected-workstation-with-thales-hsm"></a>Krok 2.1: Przygotowanie odłączonej stacji roboczej z modułu HSM firmy Thales
Zainstaluj oprogramowanie wspomagające nCipher (firmy Thales) na komputerze z systemem Windows, a następnie dołącz HSM firmy Thales do komputera.

Upewnij się, że narzędzia firmy Thales znajdują się w ścieżce (**%nfast_home%\bin**). Na przykład wpisz następujące polecenie:

  ```cmd
  set PATH=%PATH%;"%nfast_home%\bin"
  ```

Aby uzyskać więcej informacji zobacz Podręcznik użytkownika dołączone do modułu HSM firmy Thales.

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>Krok 2.2: Instalacja zestawu narzędzi BYOK na odłączonej stacji roboczej
Skopiuj pakiet zestawu narzędzi BYOK z dysku USB lub innego przenośnego urządzenia pamięci masowej, a następnie wykonaj następujące czynności:

1. Wyodrębnij pliki z pobranego pakietu do dowolnego folderu.
2. Uruchom program vcredist_x64.exe z tego folderu.
3. Postępuj zgodnie z instrukcjami instalacji składników środowiska uruchomieniowego Visual C++ dla programu Visual Studio 2013.

## <a name="step-3-generate-your-key"></a>Krok 3: Generowanie klucza
Ten krok trzeci wykonaj następujące procedury na odłączonej stacji roboczej. Aby ukończyć ten krok modułu HSM, musi być w trybie inicjowania. 


### <a name="step-31-change-the-hsm-mode-to-i"></a>Krok 3.1: Zmień tryb HSM "I"
Jeśli używasz nShield firmy Thales krawędzi, aby zmienić tryb: 1. Przycisk trybu zaznacz opcję Tryb wymagane. 2. W ciągu kilku sekund naciśnij i przytrzymaj ją przycisk Wyczyść kilka sekund. W przypadku zmiany trybu, LED nowy tryb zatrzymuje miga i pozostaje podświetlone. LED stanu może flash nieregularnych przez kilka sekund i następnie miga regularnie, gdy urządzenie jest gotowe. W przeciwnym razie urządzenia pozostaje w bieżącym trybie odpowiedni tryb LED włączone.

### <a name="step-32-create-a-security-world"></a>Krok 3.2: Utworzenie środowiska zabezpieczeń security world
Uruchom wiersz polecenia, a następnie uruchom program nowe world firmy Thales.

   ```cmd
    new-world.exe --initialize --cipher-suite=DLf1024s160mRijndael --module=1 --acs-quorum=2/3
   ```

Ten program tworzy **środowiska zabezpieczeń Security World** pliku w lokalizacji % NFAST_KMDATA%\local\world, co odpowiada folderu C:\ProgramData\nCipher\Key Management Data\local. Można użyć innych wartości dla kworum, ale w tym przykładzie zostanie wyświetlony monit o wprowadzenie trzech pustych kart oraz kodów PIN dla każdego z nich. Następnie dowolne dwie spośród kart zapewnić pełny dostęp do środowiska zabezpieczeń security world. Te karty stają się **zestaw kart administratora** dla nowego środowiska zabezpieczeń security world.

Następnie wykonaj poniższe czynności:

* Utwórz kopię zapasową pliku środowiska zabezpieczeń. Zabezpieczenie i ochronę pliku środowiska zabezpieczeń, karty administratora i numerów PIN i upewnij się, że nie jedna osoba ma dostęp do więcej niż jedną kartę.

### <a name="step-33-change-the-hsm-mode-to-o"></a>Krok 3.3: Zmień tryb HSM, aby ' O'
Jeśli używasz nShield firmy Thales krawędzi, aby zmienić tryb: 1. Przycisk trybu zaznacz opcję Tryb wymagane. 2. W ciągu kilku sekund naciśnij i przytrzymaj ją przycisk Wyczyść kilka sekund. W przypadku zmiany trybu, LED nowy tryb zatrzymuje miga i pozostaje podświetlone. LED stanu może flash nieregularnych przez kilka sekund i następnie miga regularnie, gdy urządzenie jest gotowe. W przeciwnym razie urządzenia pozostaje w bieżącym trybie odpowiedni tryb LED włączone.


### <a name="step-34-validate-the-downloaded-package"></a>Krok 3.4: Weryfikacja pobranego pakietu
Ten krok jest opcjonalny, ale zalecany, aby sprawdzić następujące czynności:

* Klucz wymiany klucza, który znajduje się w zestawie narzędzi został wygenerowany w oryginalnym module HSM firmy Thales.
* Skrót środowiska zabezpieczeń Security World, który znajduje się w zestawie narzędzi został wygenerowany w oryginalnym module HSM firmy Thales.
* Klucz wymiany klucza jest nie może zostać wyeksportowany.

> [!NOTE]
> Aby zweryfikować pobrany pakiet, moduł HSM musi być podłączony i włączony i mieć zabezpieczeń security world na nim (na przykład tego, który właśnie został utworzony).
>
>

Aby zweryfikować pobrany pakiet:

1. Uruchom skrypt verifykeypackage.py, wpisując jedną z nich, zależnie od regionu geograficznego i wystąpienia platformy Azure:

   * Dla Ameryki Północnej:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
   * Dla Europy:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
   * Dla Azji:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
   * Dla Ameryka Łacińska:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
   * W Japonii:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
   * W przypadku Korei:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-KOREA-1 -w BYOK-SecurityWorld-pkg-KOREA-1
   * Dla Australii:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
   * Aby uzyskać [Azure dla instytucji rządowych](https://azure.microsoft.com/features/gov/), który używa wystąpienia dla instytucji rządowych USA Azure:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
   * Dla instytucji rządowych USA DOD:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
   * Dla Kanady:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
   * Niemcy:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * Dla Indie:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
   * Francja:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-FRANCE-1 -w BYOK-SecurityWorld-pkg-FRANCE-1
   * Zjednoczone Królestwo:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UK-1 -w BYOK-SecurityWorld-pkg-UK-1

     > [!TIP]
     > Oprogramowanie firmy Thales obejmuje python w %NFAST_HOME%\python\bin
     >
     >
2. Upewnij się, że zostanie wyświetlony następujący komunikat, który wskazuje pomyślnym zweryfikowaniem: **wynik: powodzenie**

Ten skrypt sprawdza łańcuch osoby podpisującej aż klucza głównego firmy Thales. Skrót klucza głównego jest osadzony w skrypcie i jego wartość powinna wynosić **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. Można również potwierdzić tę wartość osobno, odwiedzając [witryny sieci Web firmy Thales](http://www.thalesesec.com/).

Teraz możesz utworzyć nowy klucz.

### <a name="step-35-create-a-new-key"></a>Krok 3.5: Utwórz nowy klucz
Generowanie klucza za pomocą firmy Thales **generatekey** programu.

Uruchom następujące polecenie, aby wygenerować klucz:

    generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

Po uruchomieniu tego polecenia, użyj tych instrukcji:

* Parametr *chronić* musi mieć ustawioną wartość **modułu**, jak pokazano. Spowoduje to utworzenie klucza chronionego przez moduł. Zestaw narzędzi BYOK nie obsługuje klucze chronione przez OCS.
* Zastąp wartość *contosokey* dla **ident** i **plainname** z dowolną wartością ciągu. Aby zminimalizować ogólne koszty administracyjne i zmniejszyć ryzyko błędów, zaleca się używać tej samej wartości dla obu. **Ident** wartość musi zawierać tylko cyfry, łączniki i małych liter.
* Parametr pubexp został pozostawiony pusty (wartość domyślna), w tym przykładzie, ale można określić konkretne jego wartości. Aby uzyskać więcej informacji zobacz dokumentację firmy Thales.

To polecenie tworzy plik Stokenizowanego klucza w folderze %NFAST_KMDATA%\local o nazwach rozpoczynających z **key_simple_**, a następnie **ident** podany w poleceniu. Na przykład: **key_simple_contosokey**. Ten plik zawiera zaszyfrowany klucz.

Utwórz kopię zapasową tego pliku Stokenizowanego klucza w bezpiecznym miejscu.

> [!IMPORTANT]
> Po późniejszym przekazaniu klucza do usługi Azure Key Vault, Microsoft nie może wyeksportować tego klucza powrót do tak ważne bezpiecznie kopii zapasowej z klucza oraz środowiska zabezpieczeń. Skontaktuj się z firmą Thales wskazówki i najlepsze rozwiązania dotyczące tworzenia kopii zapasowej klucza.
>
>

Teraz można przystąpić do przenoszenia klucza do usługi Azure Key Vault.

## <a name="step-4-prepare-your-key-for-transfer"></a>Krok 4: Przygotowanie klucza do przeniesienia
Dla tego czwarty krok wykonaj następujące procedury na odłączonej stacji roboczej.

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>Krok 4.1: Utwórz kopię klucza z ograniczonymi uprawnieniami

Otwórz nowy wiersz polecenia i zmień bieżący katalog do lokalizacji, w którym unzipped BYOK pliku zip. Aby ograniczyć uprawnienia na klucz, w wierszu polecenia, uruchom jedno z nich, zależnie od regionu geograficznego i wystąpienie usługi Azure:

* Dla Ameryki Północnej:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
* Dla Europy:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
* Dla Azji:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
* Dla Ameryka Łacińska:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
* W Japonii:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
* W przypadku Korei:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1
* Dla Australii:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
* Aby uzyskać [Azure dla instytucji rządowych](https://azure.microsoft.com/features/gov/), który używa wystąpienia dla instytucji rządowych USA Azure:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
* Dla instytucji rządowych USA DOD:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
* Dla Kanady:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
* Niemcy:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* Dla Indie:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1
* Francja:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-FRANCE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-FRANCE-1
* Zjednoczone Królestwo:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1

Podczas uruchamiania tego polecenia Zastąp *contosokey* taką samą wartością jak podana w **3.5 krok: Utwórz nowy klucz** z [generowanie klucza](#step-3-generate-your-key) kroku.

Zostanie wyświetlona prośba o podłączenie kart administratora zabezpieczeń world.

Po zakończeniu wykonywania polecenia, zostanie wyświetlony **wynik: powodzenie** , a kopia klucza z ograniczonymi uprawnieniami znajdują się w pliku o nazwie key_xferacId_<contosokey>.

Może przeprowadzający, listy ACL za pomocą następujących poleceń przy użyciu narzędzia firmy Thales:

* aclprint.py:

        "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
* kmfile-dump.exe:

        "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
  Po uruchomieniu tych poleceń Zastąp contosokey o tej samej wartości określone w **3.5 krok: Utwórz nowy klucz** z [generowanie klucza](#step-3-generate-your-key) kroku.

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>Krok 4.2: Zaszyfrowanie klucza przy użyciu klucza wymiany kluczy firmy Microsoft
Uruchom jedno z poniższych poleceń, w zależności od Twojego regionu geograficznego lub wystąpienia usługi Azure:

* Dla Ameryki Północnej:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Dla Europy:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Dla Azji:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Dla Ameryka Łacińska:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* W Japonii:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* W przypadku Korei:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Dla Australii:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Aby uzyskać [Azure dla instytucji rządowych](https://azure.microsoft.com/features/gov/), który używa wystąpienia dla instytucji rządowych USA Azure:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Dla instytucji rządowych USA DOD:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Dla Kanady:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Niemcy:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Dla Indie:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Francja:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-France-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-France-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Zjednoczone Królestwo:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey

Po uruchomieniu tego polecenia, użyj tych instrukcji:

* Zastąp *contosokey* z identyfikatorem użytym do wygenerowania klucza w **3.5 krok: Utwórz nowy klucz** z [generowanie klucza](#step-3-generate-your-key) kroku.
* Zastąp *SubscriptionID* z Identyfikatorem subskrypcji Azure, która zawiera magazynu kluczy. Tę wartość można pobrać wcześniej w **kroku 1.2: Uzyskaj identyfikator subskrypcji platformy Azure** z [Przygotowanie stacji roboczej podłączonej do Internetu](#step-1-prepare-your-internet-connected-workstation) kroku.
* Zastąp *ContosoFirstHSMKey* etykietą, która jest używana do nazwy pliku wyjściowego.

Po pomyślnym zakończeniu, wyświetla **wynik: powodzenie** i ma nowy plik w bieżącym folderze o nazwie następujących: KeyTransferPackage -*ContosoFirstHSMkey*.byok

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>Krok 4.3: Skopiowanie pakietu przekazywania klucza do stacji roboczej podłączonej do Internetu
Aby skopiować plik wyjściowy z poprzedniego kroku (KeyTransferPackage-ContosoFirstHSMkey.byok) do stacji roboczej podłączonej do Internetu, należy użyć dysku USB lub innego przenośnego urządzenia pamięci masowej.

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>Krok 5: Przesłanie klucza do usługi Azure Key Vault
W tym kroku końcowego na stacji roboczej podłączonej do Internetu, użyj [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurermkeyvaultkey) polecenia cmdlet, aby przekazać pakiet transfer klucza skopiowany z odłączonej stacji roboczej do modułu HSM usługi Azure Key Vault:

   ```powershell
        Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'
   ```

Jeśli przekazywanie zakończy się pomyślnie, zostanie wyświetlony wyświetlane właściwości klucza, który właśnie został dodany.

## <a name="next-steps"></a>Kolejne kroki
Można teraz używać tego klucza chronionego przez moduł HSM w magazynie kluczy. Aby uzyskać więcej informacji, zobacz **Jeśli chcesz użyć sprzętowego modułu zabezpieczeń (HSM)** sekcji [wprowadzenie do korzystania z usługi Azure Key Vault](key-vault-get-started.md) samouczka.
