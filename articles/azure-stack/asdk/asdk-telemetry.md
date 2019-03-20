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
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 25a832be432f1d1a6d9b18dfc8f7384c564c3060
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "58103438"
---
# <a name="azure-stack-telemetry"></a>Usługa Azure Stack telemetrii

Danych systemu Azure Stack lub dane telemetryczne, jest automatycznie przekazywana do firmy Microsoft za pośrednictwem środowiska użytkownika połączenia. Dane zbierane z telemetrii usługi Azure Stack jest używana przez zespoły Microsoft głównie w celu ulepszenia środowiska pracy naszych klientów i analizy zabezpieczeń, ochrony zdrowia, jakości i wydajności.

Jako operatorów usługi Azure Stack dane telemetryczne mogą zapewnić wartościowe wyniki analizy wdrożeń w przedsiębiorstwach i daje głosu ułatwiająca kształtu w przyszłych wersjach usługi Azure Stack.

> [!NOTE]
> Usługa Azure Stack można również skonfigurować do przekazywania informacji do platformy Azure do rozliczeń. Jest to wymagane dla klientów z wieloma węzłami usługi Azure Stack, którzy zdecydowali rozliczeń płatność za użycie. Funkcje raportowania użycia jest kontrolowane niezależnie od danych telemetrycznych i nie jest wymagany w przypadku klientów z wieloma węzłami, którzy zdecydowali modelu wydajności oraz dla użytkowników usługi Azure Stack Development Kit. Dla tych scenariuszy raportowania użycia może być wyłączone [przy użyciu skryptu rejestracji](https://docs.microsoft.com/azure/azure-stack/azure-stack-usage-reporting).

Usługa Azure Stack telemetrii opiera się na składnik połączone środowisko użytkownika systemu Windows Server 2016 i dane telemetryczne, który używa [śledzenie zdarzeń dla Windows (ETW)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) śledzenia rejestrowania technologii do zbierania i przechowywania zdarzeń telemetrii i danych. Składniki platformy Azure Stack publikować zdarzenia i dane, które są zbierane przy użyciu rejestrowania zdarzeń publicznego systemu operacyjnego i śledzenie interfejsów API za pomocą tej samej technologii rejestrowania. Przykładami składników usługi Azure Stack dostawca zasobów sieciowych, dostawcy zasobów usługi Storage, dostawca zasobów monitorowania i aktualizacji dostawcy zasobów. Składnik połączone środowisko użytkownika i dane telemetryczne szyfruje dane przy użyciu protokołu SSL i używa certyfikatu, przypinanie do przesyłania danych telemetrycznych za pośrednictwem protokołu HTTPS do usługi zarządzania danymi firmy Microsoft.

> [!NOTE]
> Aby zapewnić obsługę przepływ danych telemetrycznych, port 443 (HTTPS), należy otworzyć w sieci. Połączone środowisko użytkownika i Telemetrię składnika łączy się z usługą zarządzania danymi firmy Microsoft, w https://v10.vortex-win.data.microsoft.com. Połączone środowisko użytkownika i Telemetrię składnika również łączy się z https://settings-win.data.microsoft.com można pobrać informacji o konfiguracji.

## <a name="privacy-considerations"></a>Kwestie dotyczące ochrony prywatności
Usługa ETW kieruje dane telemetryczne do magazynu w chmurze chronionej. Zasady dostęp do danych telemetrycznych z mniejszymi przewodników. Potrzeby biznesowe tylko personel firmy Microsoft mogą uzyskiwać dostęp do danych telemetrycznych. Firma Microsoft udostępnia danych osobowych klientów osobom trzecim, z wyjątkiem uznania przez klienta lub ograniczone do celów opisanych w [usługi Azure Stack — zasady zachowania poufności informacji](https://privacy.microsoft.com/PrivacyStatement). Możemy udostępniać Raporty biznesowe producentów OEM i partnerów, które zawierają informacje o telemetrii zagregowane, anonimowe. Udostępniania decyzje dotyczące danych są wykonywane przez wewnętrzny zespół firmy Microsoft, w tym zainteresowane strony zarządzania ochrony prywatności, informacje prawne i danych.

Firma Microsoft uważa, w i praktyki minimalizacji informacji. Dążymy do zbierania tylko informacje, że potrzebujemy i przechowujemy tylko tak długo, jak jest potrzebne do świadczenia usług lub analizy. Wiele informacji dotyczących sposobu funkcjonowania systemu Azure Stack i usług platformy Azure zostaną usunięte w ciągu sześciu miesięcy. Podsumowanie lub zagregowane dane są przechowywane przez dłuższy czas.

Rozumiemy, poufność i bezpieczeństwo informacji naszych klientów jest ważne. Przekierowaliśmy przydatnego i kompleksowego podejścia do zasad ochrony prywatności klientów i ochrony danych klienta dzięki usłudze Azure Stack. Administratorzy IT mają kontrolki umożliwiające dostosowanie funkcji i ustawień prywatności w dowolnym momencie. Nasze zaangażowanie przejrzystości i zaufania jest wyczyść:
- Jesteśmy Otwórz za pomocą klientów o typach danych, które zbieramy.
- Umieściliśmy klientów korporacyjnych w formancie — może dostosować własne ustawienia ochrony prywatności.
- Najpierw umieściliśmy klienta prywatności i bezpieczeństwa.
- Jesteśmy przezroczyste o sposobie używania pobiera dane telemetryczne.
- Używamy telemetrii do poprawy środowiska klientów.

Microsoft nie zamierza zbierać poufne informacje, takie jak numery kart kredytowych, nazwy użytkowników i hasła, adresy e-mail lub innych podobnie poufne informacje. Jeśli określamy przypadkowo otrzymane informacje poufne, zostaną usunięte.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Przykłady jak firma Microsoft używa danych telemetrycznych
Dane telemetryczne odgrywa ważną rolę pomagający nam szybko zidentyfikować i rozwiązać problemy ze zgodnością krytyczne w wdrożeń klientów i konfiguracji. Wgląd w dane telemetryczne, które zbieramy, Pomóż nam szybkie identyfikowanie problemów z usługami lub konfiguracji sprzętu. Możliwość pobrania tych danych klientów i ulepszenia dysków w ekosystemie usługi firmy Microsoft pomaga podnoszenia poprzeczki w zakresie jakości naszych zintegrowanych rozwiązań usługi Azure Stack.

Telemetria pomaga również firmy Microsoft, aby lepiej zrozumieć, jak klienci wdrożyć składniki, korzystanie z funkcji i użyć usług, aby osiągnąć swoje cele biznesowe. Uzyskiwanie szczegółowych informacji z danych pomaga Wyznaczanie priorytetów inwestycji inżynieryjnych, w obszarach, które mogą mieć bezpośredni wpływ środowiska naszych klientów i obciążeń.

Niektóre przykłady użycia przez klientów z kontenerów, magazynu i konfiguracji sieci, które są skojarzone z rolami usługi Azure Stack. Korzystamy także szczegółowe informacje, aby poprawiać i analizy na niektóre z naszych zarządzania i monitorowania rozwiązań. To pomaga klientom w celu diagnozowania problemów z jakością i oszczędzać pieniądze, wprowadzając obsługę mniejszej liczby wywołań do firmy Microsoft.

## <a name="manage-telemetry-collection"></a>Zarządzanie zbieranie danych telemetrycznych
Nie jest zalecane, możesz wyłączyć obsługę telemetrii w Twojej organizacji jako dane telemetryczne udostępnia dane, obsługujące funkcje produktu ulepszone i stabilności. Wiemy, że w niektórych przypadkach może to być konieczne.

W tych przypadkach można skonfigurować poziom dane telemetryczne wysyłane do firmy Microsoft, za pomocą ustawień rejestru — przed wdrożeniem lub po wdrożeniu punktów końcowych danych Telemetrycznych.

### <a name="set-telemetry-level-in-the-windows-registry"></a>Ustaw poziom telemetrii w rejestrze systemu Windows
Edytor rejestru Windows służy do ręcznego ustawiania poziom telemetrii na komputerze hosta fizycznego, przed wdrożeniem usługi Azure Stack. Jeśli już istnieje zasad zarządzania, takie jak zasady grupy, zastępuje to ustawienie rejestru.

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

Poziomy dane telemetryczne są zbiorcze i podzielonych na kategorie na cztery poziomy (0 – 3):

**0 (zabezpieczenia)**. Tylko dane zabezpieczeń. Bezpieczeństwo informacji, które są wymagane do zapewnienia systemu operacyjnego, włącznie z danymi o ustawienia składnika połączone środowisko użytkownika i danych Telemetrycznych i usługa Windows Defender. Brak telemetrii określonej usługi Azure Stack jest emitowane na tym poziomie.

**1 (podstawowe)**. Bezpieczeństwo danych i podstawowe kondycji oraz jakości danych. Informacje o urządzeniach podstawowych, w tym: dane dotyczące jakości, zgodność aplikacji, dane dotyczące użycia aplikacji i danych z poziomu zabezpieczeń. Ustawienie poziomu danych telemetrycznych do telemetrii usługi Azure Stack umożliwia podstawowe. Dane zbierane na tym poziomie obejmują:

- **Informacje o urządzeniu podstawowe** umożliwia zrozumienie o typach i konfiguracje natywnych i zwirtualizowanych wystąpień systemu Windows Server 2016 w ekosystemie, w tym:
  - Atrybuty maszyny, takie jak OEM, model,
  - Atrybuty sieci, takie jak liczba i szybkość kart sieciowych
  - Procesor i pamięć atrybutów, takich jak liczba rdzeni, ilość pamięci,
  - Magazyn atrybutów, takich jak liczba dysków, typ i rozmiar.
- **Funkcje telemetrii**, takie jak procent przekazane zdarzenia, porzuconych zdarzeń i ostatni czas przekazywania.
- **Informacje dotyczące jakości** temu Microsoft opracowywanie podstawową wiedzę na temat jak działa usługa Azure Stack. Przykładem jest liczba alertów krytycznych w konfiguracji określonego sprzętu.
- **Dane zgodności**, która pomaga w zapewnieniu zrozumienia o tym, które są zainstalowane w systemie i maszyny wirtualnej dostawców zasobów i identyfikuje potencjalne problemy ze zgodnością.

**2 (rozszerzony)**. Dodatkowe informacje szczegółowe, w tym: jak używany system operacyjny i inne usługi Azure Stack, ich działanie, dane o niezawodności zaawansowane i danych z poziomami podstawowa i zabezpieczeń.

**3 (pełne)**. Wszystkie dane potrzebne do identyfikowania i pomagają rozwiązywać problemy, a także dane z **zabezpieczeń**, **podstawowe**, i **rozszerzony** poziomów.

> [!NOTE]
> Wartość poziomu telemetrii domyślna to 2 (rozszerzony).

Wyłączanie telemetrii Windows i usługi Azure Stack wyłącza telemetrię SQL. Aby uzyskać dodatkowe informacje na temat skutków telemetrią systemu Windows Server [oficjalny dokument dotyczący Windows Telemetrii](https://aka.ms/winservtelemetry).

> [!IMPORTANT]
> Te poziomy danych telemetrycznych dotyczą tylko składniki Microsoft Azure Stack. Składniki oprogramowania innych firm i usług, które są uruchomione na hoście sprzętu cyklu życia, od partnerów sprzętowych w usłudze Azure Stack mogą komunikować się z usług w chmurze poza te poziomy danych telemetrycznych. Powinien współpracować z dostawcą usługi Azure Stack sprzętu rozwiązania, aby zrozumieć swoich zasad danych telemetrycznych i jak włączyć lub zrezygnować z.

### <a name="enable-or-disable-telemetry-after-deployment"></a>Włączanie lub wyłączanie telemetrii po wdrożeniu

Aby włączyć lub wyłączyć telemetrię po wdrożeniu, musisz mieć dostęp do uprzywilejowanych punktu końcowego (program ten), która jest widoczna na maszynach wirtualnych ERCS.
1.  Aby włączyć: `Set-Telemetry -Enable`
2.  Aby wyłączyć: `Set-Telemetry -Disable`

Szczegóły parametrów:
> . PARAMETR Enable - Włącz przekazywanie danych telemetrii
> 
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
[Uruchamianie i zatrzymywanie ASDK](asdk-start-stop.md)
