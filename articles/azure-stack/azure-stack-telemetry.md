---
title: Azure stosu telemetrii | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania ustawień telemetrii stosu Azure przy użyciu programu PowerShell.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/07/2018
ms.author: jeffgilb
ms.reviewer: comartin
ms.openlocfilehash: ed3f09f942bdaa803ae8024d5c02230173190107
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248201"
---
# <a name="azure-stack-telemetry"></a>Azure telemetrii stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Azure telemetrii stosu automatyczne przekazywanie danych systemu do firmy Microsoft za pośrednictwem środowiska użytkownika połączenia. Zespoły firmy Microsoft przy użyciu danych telemetrii stosu Azure zbierające zwiększające środowiska użytkownika. Te dane są używane również do zabezpieczeń, kondycji, jakości i analiza wydajności.

Dla operatora stosu Azure telemetrii zapewniają cenne wgląd w przedsiębiorstwach i daje głosu ułatwiający kształtu przyszłych wersji Azure stosu.

> [!NOTE]
> Można również skonfigurować stosu Azure do przekazywania informacji o użyciu do platformy Azure dla rozliczeń. Jest to wymagane dla stosu Azure wielowęzłowego klientów, którzy wybierz rozliczeń płatności jako — użytkownik użycia. Raportowanie użycia jest kontrolowany niezależnie od telemetrii i nie jest wymagana dla wielowęzłowego klientów, którzy wybierz model pojemności lub dla użytkowników usługi Azure stosu Development Kit. W tych sytuacjach raportowanie użycia, można wyłączyć [przy użyciu skryptu rejestracji](https://docs.microsoft.com/azure/azure-stack/azure-stack-usage-reporting).

Azure telemetrii stosu zależy od składnika połączone środowisko użytkownika systemu Windows Server 2016 i dane telemetryczne, który używa [funkcji Śledzenie zdarzeń systemu Windows ()](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) TraceLogging technologii do gromadzenia i przechowywania zdarzeń i danych. Azure składniki stosu używać tej samej technologii do publikowania zdarzeń i danych użytkownika zebranych za pomocą rejestrowania zdarzeń publicznego systemu operacyjnego i śledzenie interfejsów API. Te składniki stosu Azure przykładów tych dostawców: zasobów sieci, zasobów magazynu monitorowania zasobów i aktualizacji zasobów. Składnik połączone środowisko użytkownika i dane telemetryczne szyfruje dane przy użyciu protokołu SSL i używa certyfikatu przypinanie do przesyłania danych za pośrednictwem protokołu HTTPS z usługą zarządzania danymi firmy Microsoft.

> [!IMPORTANT]
> Aby umożliwić przepływ danych telemetrii, port 443 (HTTPS), należy otworzyć w sieci. Składnik połączone środowisko użytkownika i dane telemetryczne łączy się z usługą zarządzania danymi firmy Microsoft w https://v10.vortex-win.data.microsoft.com. Składnik połączone środowisko użytkownika i dane telemetryczne również łączy się z https://settings-win.data.microsoft.com można pobrać informacji o konfiguracji.

## <a name="privacy-considerations"></a>Zagadnienia dotyczące ochrony prywatności

Usługa ETW kieruje dane telemetryczne do magazynu w chmurze chronionej. Zasadą najniższych uprawnień prowadzi dostępu do danych telemetrycznych. Tylko personel firmy Microsoft o potrzebach biznesowych prawidłowy mających dostęp do danych telemetrycznych. Firma Microsoft nie udostępniać danych osobowych klienta osobom trzecim, z wyjątkiem uznania klienta lub ograniczona celów opisanych w [zasady zachowania poufności informacji firmy Microsoft](https://privacy.microsoft.com/PrivacyStatement). Raporty biznesowe, które są udostępniane producentów OEM i partnerów obejmować dane zagregowane, anonimowe. Udostępnianie decyzje danych są wykonywane przez wewnętrzny zespół firmy Microsoft, łącznie z zainteresowanymi stronami zarządzania prywatności, prawne i danych.

Firma Microsoft uznaje w i rozwiązań minimalizacja informacji. Staramy się zebrać informacje, która jest wymagana i zapisz go w celu tylko tak długo, jak niezbędne do świadczenia usług lub do analizy. Wiele informacji dotyczących sposobu funkcjonowania systemu Azure stosu i usług Azure jest usuwany w ciągu sześciu miesięcy. Podsumowanie lub zagregowane dane będą przechowywane przez dłuższy czas.

Zdajemy prywatności i bezpieczeństwa informacji klienta jest ważne.  Microsoft przyjmuje przemyślane i wszechstronne podejście do klienta prywatności i ochrony danych klienta w stosie usługi Azure. Administratorzy IT mogą kontrolki umożliwiające dostosowanie funkcji i ustawień prywatności w dowolnym momencie. Wynika z naszych zobowiązań przezroczystość i zaufania:

- Jesteśmy Otwórz za pomocą klientów dotyczące typu danych, które zbieramy.
- Testujemy przedsiębiorstwa w formancie — można dostosowywać ustawień prywatności.
- Najpierw testujemy klienta prywatności i bezpieczeństwa.
- Jesteśmy przezroczysty o sposobie używania pobiera dane telemetryczne.
- Używamy danych telemetrycznych do poprawy środowiska użytkownika.

Microsoft nie zamierzasz zbierać dane poufne, takie jak numery kart kredytowych, nazwy użytkowników i hasła, adresy e-mail lub podobne informacje poufne. Jeśli określić przypadkowo otrzymano informacje poufne, możemy ją usunąć.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Przykłady jak firma Microsoft używa danych telemetrii

Dane telemetryczne odgrywa ważną rolę w pomaga szybko zidentyfikować i rozwiązać problemy ze zgodnością krytyczne w wdrożeń klienta i konfiguracji. Wgląd w dane dotyczące danych telemetrycznych pomocne w identyfikacji problemów z usługami lub konfiguracji sprzętu. Zdolność firmy Microsoft można pobrać te dane z klientów i ulepszenia dysku z ekosystemem wywołuje pasek jakość zintegrowanego rozwiązania Azure stosu.

Dane telemetryczne pomaga również firmy Microsoft, aby lepiej zrozumieć, jak klienci wdrażać składniki, korzystanie z funkcji i korzystania z usług w celach biznesowych. Informacje na temat tych technologii pomocy priorytety engineering inwestycji w obszarach, które może wpływać bezpośrednio na środowiska użytkownika i obciążeń.

Oto kilka przykładów użycia klienta kontenerów, magazynu i konfiguracji sieci, które są skojarzone z rolami stosu Azure. Możemy również użyć szczegółowych danych do ulepszenia dysku i analizy do stosu Azure zarządzanie i monitorowanie rozwiązań. Te ulepszenia ułatwiają klientom diagnozowanie problemów i Zapisz pieniądze, wprowadzając obsługę mniejszej liczby wywołań do firmy Microsoft.

## <a name="manage-telemetry-collection"></a>Zarządzanie gromadzenia danych telemetrii

Firma Microsoft nie są zalecane wyłączanie telemetrii w Twojej organizacji. Jednak w niektórych przypadkach może to być konieczne.

W tych scenariuszach można skonfigurować poziom telemetrii wysyłane do firmy Microsoft, za pomocą ustawień rejestru przed wdrożeniem stosu Azure lub za pomocą punktów końcowych Telemetrii po wdrożeniu usługi Azure stosu.

### <a name="telemetry-levels-and-data-collection"></a>Dane telemetryczne poziomów i gromadzenia danych

Przed zmianą ustawień telemetrii, zapoznaj się poziomy telemetrii i jakie dane są zbierane na każdym poziomie.

Ustawienia telemetrii są zgrupowane w czterech poziomów (0-3), które są zbiorcze i skategoryzowane w następujący sposób:

**0 (zabezpieczenia)**</br>
Tylko dane zabezpieczeń. Informacje, które są wymagane w celu poprawy bezpieczeństwa systemu operacyjnego. Dotyczy to również dane dotyczące połączone środowisko użytkownika i dane telemetryczne ustawienia składnika, usługa Windows Defender. Brak danych telemetrycznych specyficzne dla platformy Azure stosu jest emitowany na tym poziomie.

**1 (podstawowe)**</br>
Dane zabezpieczeń i podstawowe Kondycja i jakość danych. Podstawowych informacji urządzenia, w tym: dane dotyczące jakości i aplikacji, dane użycia aplikacji i danych z **zabezpieczeń** poziom. Ustawienie poziomu telemetrii na telemetrii stosu Azure umożliwia podstawowe. Dane zebrane na tym poziomie obejmują:

- *Informacje o urządzeniach podstawowych* zapewnia zrozumienia typów i konfiguracje wystąpień systemu Windows Server 2016 native i wirtualne w ekosystemie. Obejmuje to:

  - Atrybuty maszyny, takie jak OEM i modelu.
  - Atrybuty sieci, takie jak liczba kart sieciowych i ich szybkości.
  - Atrybuty procesora i pamięci, takie jak liczba rdzeni i ilości zainstalowanej pamięci.
  - Magazyn atrybutów, takich jak liczba dysków, typ dysku i rozmiar dysku.

- *Funkcja telemetrii*, tym procent przekazane zdarzenia, porzuconych zdarzenia i dane o ostatniej Przekaż czasu.
- *Informacje dotyczące jakości* który ułatwia opracowywanie podstawową wiedzę na temat sposobu wykonywania jest stos Azure firmy Microsoft. Na przykład liczba alertów krytycznych na określoną konfiguracją sprzętu.
- *Dane zgodności* czy pomaga zapewnić zrozumienia o tym, które dostawców zasobów są zainstalowane w systemie i maszyny wirtualnej. Identyfikuje potencjalne problemy ze zgodnością.

**2 (rozszerzony)**</br>
Dodatkowe informacje szczegółowe, w tym: użycia usług Azure stosu i systemu operacyjnego, jak wykonać tych usług, zaawansowane niezawodność danych i danych z **zabezpieczeń** i **podstawowe** poziomów.

> [!NOTE]
> Jest to domyślne ustawienie telemetrii.

**3 (pełne)**</br>
Wszystkie dane niezbędne do identyfikowania i pomoc, aby rozwiązać problemy, a także dane z **zabezpieczeń**, **podstawowe**, i **rozszerzony** poziomów.

> [!IMPORTANT]
> Te poziomy telemetrii dotyczą tylko składniki programu Microsoft Azure stosu. Składniki oprogramowania innych firm i usług, które są uruchomione na hoście cyklu życia sprzętu z dostawców sprzętu będących partnerami stosu Azure może komunikować się z ich usługi w chmurze poza tymi poziomami telemetrii. Powinien współpracować z dostawcy rozwiązań sprzętu stosu Azure, aby zrozumieć ich zasad telemetrii i jak włączyć lub zrezygnować.

Wyłączanie telemetrii systemu Windows i stosu Azure wyłącza także telemetrii SQL. Aby uzyskać więcej informacji o skutki ustawieniami telemetrii systemu Windows Server, zobacz [Windows Telemetrii w oficjalnym dokumencie](https://aka.ms/winservtelemetry).

### <a name="asdk-set-the-telemetry-level-in-the-windows-registry"></a>ASDK: Ustaw poziom telemetrii w rejestrze systemu Windows

Edytor rejestru systemu Windows umożliwia ręcznie ustawić poziom telemetrii na komputerze hosta fizycznego, przed wdrożeniem stosu Azure. Jeśli zasady zarządzania już istnieje, takie jak zasady grupy zastępuje ustawienie rejestru.

Przed wdrożeniem stosu Azure na hoście development kit, rozruch w CloudBuilder.vhdx i uruchom następujący skrypt w oknie programu PowerShell z podwyższonym poziomem uprawnień:

```powershell
### Get current AllowTelmetry value on DVM Host
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
### Set & Get updated AllowTelemetry value for ASDK-Host
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name "AllowTelemetry" -Value '0' # Set this value to 0,1,2,or3.  
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
```

### <a name="asdk-and-multi-node-enable-or-disable-telemetry-after-deployment"></a>ASDK i wielowęzłowego: Włączanie lub wyłączanie telemetrii po wdrożeniu

Aby włączyć lub wyłączyć telemetrii po wdrożeniu, musisz mieć dostęp do uprzywilejowanych punktu końcowego (program ten), który jest narażony na maszynach wirtualnych ERCS.

1. Aby włączyć: `Set-Telemetry -Enable`
2. Aby wyłączyć: `Set-Telemetry -Disable`

Szczegóły PARAMETRU:
> . PARAMETR Enable - Włącz przekazywania danych telemetrii</br>
> . Wyłącz PARAMETR - Wyłącz przekazywania danych telemetrii  

**Skrypt umożliwiający telemetrii:**

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
$psSession = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred
Invoke-Command -Session $psSession {Set-Telemetry -Enable}
if($psSession)
{
    Remove-PSSession $psSession
}
```

**Skrypt służący do wyłączania telemetrii:**

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
$psSession = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred
Invoke-Command -Session $psSession {Set-Telemetry -Disable}
if($psSession)
{
    Remove-PSSession $psSession
}
```

## <a name="next-steps"></a>Kolejne kroki

- [Pobierz pakiet wdrażania zestawu programowanie Azure stosu](https://azure.microsoft.com/overview/azure-stack/try/?v=try)

- [Wdrażanie zestaw deweloperski Azure stosu](azure-stack-run-powershell-script.md)
