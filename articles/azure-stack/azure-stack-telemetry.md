---
title: Usługa Azure Stack telemetrii | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania ustawień telemetrii usługi Azure Stack przy użyciu programu PowerShell.
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
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: jeffgilb
ms.reviewer: comartin
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 1977ced65b6dd62a023a79ce8949a8b428d2f965
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2019
ms.locfileid: "57760362"
---
# <a name="azure-stack-telemetry"></a>Usługa Azure Stack telemetrii

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Azure Stack telemetrii automatyczne przekazywanie danych systemu do firmy Microsoft za pośrednictwem środowiska użytkownika połączenia. Zespoły firmy Microsoft używają danych, który zbiera dane telemetryczne z usługi Azure Stack, na ulepszenie środowiska klienta. Te dane są również używane dla zabezpieczeń, ochrony zdrowia, jakości i analizy wydajności.

Dla operatorów usługi Azure Stack dane telemetryczne mogą zapewnić wartościowe wyniki analizy wdrożeń w przedsiębiorstwach i daje głosu ułatwiająca kształtu w przyszłych wersjach usługi Azure Stack.

> [!NOTE]
> Można również skonfigurować usługi Azure Stack do przekazywania informacji o użyciu do platformy Azure do rozliczeń. Jest to wymagane dla klientów z wieloma węzłami usługi Azure Stack, którzy zdecydowali rozliczeń płatność za użycie. Funkcje raportowania użycia jest kontrolowane niezależnie od danych telemetrycznych i nie jest wymagany w przypadku klientów z wieloma węzłami, którzy zdecydowali modelu wydajności oraz dla użytkowników usługi Azure Stack Development Kit. Dla tych scenariuszy raportowania użycia może być wyłączone [przy użyciu skryptu rejestracji](https://docs.microsoft.com/azure/azure-stack/azure-stack-usage-reporting).

Usługa Azure Stack telemetrii opiera się na składnik połączone środowisko użytkownika systemu Windows Server 2016 i dane telemetryczne, który używa [śledzenie zdarzeń dla Windows (ETW)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) TraceLogging technologii do zbierania i przechowywania zdarzeń i danych. Składniki platformy Azure Stack publikować zdarzenia i dane zbierane przy użyciu rejestrowania zdarzeń publicznego systemu operacyjnego i śledzenie interfejsów API za pomocą tej samej technologii. Przykłady te składniki usługi Azure Stack tych dostawców: Sieci zasób, zasób magazynu, monitorowanie zasobów i zaktualizuj zasób. Składnik połączone środowisko użytkownika i dane telemetryczne szyfruje dane przy użyciu protokołu SSL i używa certyfikatu, przypinanie do przesyłania danych za pośrednictwem protokołu HTTPS do usługi zarządzania danymi firmy Microsoft.

> [!IMPORTANT]
> Aby włączyć przepływ danych telemetrycznych, port 443 (HTTPS), należy otworzyć w sieci. Połączone środowisko użytkownika i Telemetrię składnika łączy się z usługą zarządzania danymi firmy Microsoft, w https://v10.vortex-win.data.microsoft.com. Połączone środowisko użytkownika i Telemetrię składnika również łączy się z https://settings-win.data.microsoft.com można pobrać informacji o konfiguracji.

## <a name="privacy-considerations"></a>Kwestie dotyczące ochrony prywatności

Usługa ETW kieruje dane telemetryczne do magazynu w chmurze chronionej. Podmiot zabezpieczeń najniższych uprawnień zawiera informacje na temat dostępu do danych telemetrycznych. Potrzeby biznesowe tylko personel firmy Microsoft mających dostęp do danych telemetrycznych. Firma Microsoft nie udostępnia danych osobowych klienta osobom trzecim, z wyjątkiem uznania przez klienta lub ograniczone do celów opisanych w [zasady zachowania poufności informacji firmy Microsoft](https://privacy.microsoft.com/PrivacyStatement). Raportów biznesowych, które są współużytkowane z producentów OEM i partnerów obejmować dane zagregowane, anonimowe. Udostępniania decyzje dotyczące danych są wykonywane przez wewnętrzny zespół firmy Microsoft, w tym zainteresowane strony zarządzania ochrony prywatności, informacje prawne i danych.

Firma Microsoft uważa, w i praktyki minimalizacji informacji. Dokładamy wszelkich starań zebrać informacje, które są potrzebne i przechowywać je tylko tak długo, co jest niezbędne do świadczenia usług lub do analizy. Wiele informacji dotyczących sposobu funkcjonowania systemu Azure Stack i usług platformy Azure zostaną usunięte w ciągu sześciu miesięcy. Podsumowanie lub zagregowane dane będą przechowywane przez dłuższy czas.

Rozumiemy, poufność i bezpieczeństwo danych klienta jest ważne.  Firma Microsoft podejmuje przydatnego i kompleksowego podejścia do zasad ochrony prywatności klientów i ochrony danych klienta w usłudze Azure Stack. Administratorzy IT mają kontrolki umożliwiające dostosowanie funkcji i ustawień prywatności w dowolnym momencie. Nasze zaangażowanie przejrzystości i zaufania jest wyczyść:

- Jesteśmy Otwórz za pomocą klientów o typach danych, które zbieramy.
- Umieściliśmy klientów korporacyjnych w formancie — może dostosować własne ustawienia ochrony prywatności.
- Najpierw umieściliśmy klienta prywatności i bezpieczeństwa.
- Jesteśmy przezroczyste o sposobie używania pobiera dane telemetryczne.
- Używamy danych telemetrycznych na ulepszenie środowiska klienta.

Microsoft nie zamierzasz zbierać dane poufne, takie jak numery kart kredytowych, nazwy użytkowników i hasła, adresy e-mail lub podobne informacje poufne. Jeśli określamy przypadkowo otrzymane informacje poufne, zostaną usunięte.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Przykłady jak firma Microsoft używa danych telemetrycznych

Dane telemetryczne odgrywa ważną rolę w pomaganiu szybko zidentyfikować i rozwiązać problemy ze zgodnością krytyczne w konfiguracje i wdrożenia klienta. Szczegółowe informacje na podstawie danych telemetrycznych może pomóc w identyfikowaniu problemów dotyczących usługi lub konfiguracji sprzętu. Możliwości firmy Microsoft, aby uzyskać te dane z klientów i poprawiać z ekosystemem zgłasza pasku jakości zintegrowanych rozwiązań usługi Azure Stack.

Telemetria pomaga również firmy Microsoft, aby lepiej zrozumieć, jak klienci wdrożyć składniki, korzystanie z funkcji i użyć usług, aby osiągnąć swoje cele biznesowe. Te informacje pomagają Wyznaczanie priorytetów inwestycji inżynieryjnych w obszarach, które mogą mieć bezpośredni wpływ środowiska pracy klientów i obciążeń.

Przykłady: Korzystanie z kontenerów, magazynu i konfiguracji sieci, które są skojarzone z rolami usługi Azure Stack klienta. Korzystamy także szczegółowe informacje, aby poprawiać i analizy w usłudze Azure Stack, zarządzania i monitorowania rozwiązań. Te ulepszenia ułatwiają klientom w celu diagnozowania problemów i oszczędzać pieniądze, wprowadzając obsługę mniejszej liczby wywołań do firmy Microsoft.

## <a name="manage-telemetry-collection"></a>Zarządzanie zbieranie danych telemetrycznych

Firma Microsoft nie zalecane wyłączanie telemetrii w Twojej organizacji. Jednak w niektórych przypadkach może to być konieczne.

W tych scenariuszach można skonfigurować poziom dane telemetryczne wysyłane do firmy Microsoft, za pomocą ustawień rejestru, przed wdrożeniem usługi Azure Stack lub za pomocą punktów końcowych danych Telemetrycznych po wdrożeniu usługi Azure Stack.

### <a name="telemetry-levels-and-data-collection"></a>Zbieranie danych telemetrycznych z poziomów i danych

Zanim zmienisz ustawieniami telemetrii, należy zrozumieć poziomy danych telemetrycznych i jakie dane są zbierane na każdym poziomie.

Ustawieniami telemetrii są pogrupowane w cztery poziomy (0 – 3), które są zbiorcze i podzielonych na kategorie w następujący sposób:

**0 (zabezpieczenia)**</br>
Tylko dane zabezpieczeń. Informacje, które są wymagane, aby zabezpieczyć systemu operacyjnego. Obejmuje to dane o ustawienia składnika połączone środowisko użytkownika i danych Telemetrycznych i usługa Windows Defender. Brak telemetrii, które są specyficzne dla usługi Azure Stack jest emitowane na tym poziomie.

**1 (podstawowe)**</br>
Bezpieczeństwo danych i podstawowe kondycji oraz jakości danych. Informacje o urządzeniach podstawowych, w tym: dane dotyczące jakości, zgodność aplikacji, dane dotyczące użycia aplikacji i dane z **zabezpieczeń** poziom. Ustawienie poziomu danych telemetrycznych do telemetrii usługi Azure Stack umożliwia podstawowe. Dane zbierane na tym poziomie obejmują:

- *Informacje o urządzeniu podstawowe* zawierający opis o typach i konfiguracje natywnych i wirtualnych wystąpień systemu Windows Server 2016, należący do ekosystemu. Obejmuje to:

  - Atrybuty maszyny, takie jak OEM i modelu.
  - Sieć atrybutów, takich jak liczba kart sieciowych i ich szybkością.
  - Atrybuty procesora i pamięci, takie jak liczba rdzeni i ilość pamięci zainstalowanej.
  - Magazyn atrybutów, takich jak liczba dysków, typ dysku i rozmiar dysku.

- *Funkcje telemetrii*, łącznie z wartością procentową przekazane zdarzenia, porzuconych zdarzenia i dane o ostatniej czas przekazywania.
- *Informacje dotyczące jakości* temu Microsoft opracowywanie podstawową wiedzę na temat jak działa usługa Azure Stack. Na przykład liczba alertów krytycznych w konfiguracji określonego sprzętu.
- *Dane zgodności* umożliwia zrozumienie o tym, które dostawcy zasobów są zainstalowane w systemie i maszyny wirtualnej. Identyfikuje potencjalne problemy ze zgodnością.

**2 (rozszerzony)**</br>
Dodatkowe informacje szczegółowe, w tym: jak używany system operacyjny i usługi Azure Stack, sposób wykonywania tych usług, zaawansowane niezawodność danych i danych z **zabezpieczeń** i **podstawowe** poziomów.

> [!NOTE]
> Jest to domyślne ustawienie danych telemetrycznych.

**3 (pełne)**</br>
Wszystkie dane potrzebne do identyfikowania i pomagają rozwiązywać problemy, a także dane z **zabezpieczeń**, **podstawowe**, i **rozszerzony** poziomów.

> [!IMPORTANT]
> Te poziomy danych telemetrycznych dotyczą tylko składniki Microsoft Azure Stack. Składniki oprogramowania innych firm i usług, które są uruchomione na hoście sprzętu cyklu życia, od partnerów sprzętowych w usłudze Azure Stack mogą komunikować się z usług w chmurze poza te poziomy danych telemetrycznych. Powinien współpracować z dostawcą usługi Azure Stack sprzętu rozwiązania, aby zrozumieć swoich zasad danych telemetrycznych i jak włączyć lub zrezygnować z.

Wyłączanie telemetrii Windows i usługi Azure Stack dodatkowo wyłącza telemetrii SQL. Aby uzyskać więcej informacji na temat skutków telemetrią systemu Windows Server, zobacz [oficjalny dokument dotyczący Windows Telemetrii](https://aka.ms/winservtelemetry).

### <a name="asdk-set-the-telemetry-level-in-the-windows-registry"></a>ASDK: Ustaw poziom telemetrii w rejestrze systemu Windows

Korzystanie z Edytora rejestru Windows, aby ręcznie ustawić poziom telemetrii na komputerze hosta fizycznego, przed wdrożeniem usługi Azure Stack. Jeśli już istnieje zasad zarządzania, takie jak zasady grupy, zastępuje to ustawienie rejestru.

Przed wdrożeniem usługi Azure Stack na hoście development kit, rozruch CloudBuilder.vhdx i uruchom następujący skrypt w oknie programu PowerShell z podwyższonym poziomem uprawnień:

```powershell
### Get current AllowTelemetry value on DVM Host
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
### Set & Get updated AllowTelemetry value for ASDK-Host
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name "AllowTelemetry" -Value '0' # Set this value to 0,1,2,or3.  
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
```

### <a name="asdk-and-multi-node-enable-or-disable-telemetry-after-deployment"></a>ASDK i wieloma węzłami: Włączanie lub wyłączanie telemetrii po wdrożeniu

Aby włączyć lub wyłączyć telemetrię po wdrożeniu, musisz mieć dostęp do uprzywilejowanych punktu końcowego (program ten), która jest widoczna na maszynach wirtualnych ERCS.

1. Aby włączyć: `Set-Telemetry -Enable`
2. Aby wyłączyć: `Set-Telemetry -Disable`

Szczegóły parametrów:
> . PARAMETR Enable - Włącz przekazywanie danych telemetrii</br>
> . Wyłącz PARAMETR - wyłącz przekazywanie danych telemetrii  

**Skrypt, aby włączyć telemetrię:**

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

**Skrypt służący do wyłączania danych telemetrycznych:**

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

[Rejestrowania usługi Azure Stack na platformie Azure](azure-stack-registration.md)