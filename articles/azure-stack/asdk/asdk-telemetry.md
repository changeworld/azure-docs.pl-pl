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
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: bfd16901c5ce036719a1ed19e9a5b5c6ef52be93
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="azure-stack-telemetry"></a>Azure telemetrii stosu

Danych systemu Azure stosu lub dane telemetryczne, automatycznie jest przesyłane do firmy Microsoft za pośrednictwem środowiska użytkownika połączenia. Dane zbierane z telemetrii stosu Azure jest używana przez zespoły firmy Microsoft głównie w celu ulepszenia środowiska naszych klientów, a także analizy zabezpieczeń, jakości, kondycji i wydajności.

Jako operator stosu Azure telemetrii zapewniają cenne wgląd w przedsiębiorstwach i daje głosu ułatwiający kształtu przyszłych wersji Azure stosu.

> [!NOTE]
> Stos Azure można również skonfigurować do informacji o użyciu do przodu do platformy Azure dla rozliczeń. Jest to wymagane dla stosu Azure wielowęzłowego klientów, którzy wybierz rozliczeń płatności jako — użytkownik użycia. Raportowanie użycia jest kontrolowany niezależnie od telemetrii i nie jest wymagana dla wielowęzłowego klientów, którzy wybierz model pojemności lub dla użytkowników usługi Azure stosu Development Kit. W tych sytuacjach raportowanie użycia, można wyłączyć [przy użyciu skryptu rejestracji](https://docs.microsoft.com/azure/azure-stack/azure-stack-usage-reporting).

Azure telemetrii stosu zależy od składnika połączone środowisko użytkownika systemu Windows Server 2016 i dane telemetryczne, który używa [funkcji Śledzenie zdarzeń systemu Windows ()](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) technologii rejestrowania do gromadzenia i przechowywania zdarzeń telemetrii i danych śledzenia. Azure składniki stosu używać tej samej technologii rejestrowania do publikowania zdarzeń i danych, które są zbierane przy użyciu rejestrowania zdarzeń publicznego systemu operacyjnego i śledzenie interfejsów API. Przykładami składniki stosu Azure dostawcy zasobów sieciowych, dostawcy zasobów magazynu dostawcy zasobów monitorowania i aktualizacji dostawcy zasobów. Składnik połączone środowisko użytkownika i dane telemetryczne szyfruje dane przy użyciu protokołu SSL i używa certyfikatu przypinanie do przesyłania danych telemetrycznych za pośrednictwem protokołu HTTPS z usługą zarządzania danymi firmy Microsoft.

> [!NOTE]
> Aby zapewnić obsługę przepływu danych telemetrii, port 443 (HTTPS), należy otworzyć w sieci. Składnik połączone środowisko użytkownika i dane telemetryczne łączy się z usługą zarządzania danymi firmy Microsoft w https://v10.vortex-win.data.microsoft.com. Składnik połączone środowisko użytkownika i dane telemetryczne również łączy się z https://settings-win.data.microsoft.com można pobrać informacji o konfiguracji.

## <a name="privacy-considerations"></a>Zagadnienia dotyczące ochrony prywatności
Usługa ETW kieruje dane telemetryczne do magazynu w chmurze chronionej. Zasadą najniższych uprawnieniach przewodniki dostęp do danych telemetrycznych. Tylko personel firmy Microsoft o potrzebach biznesowych prawidłowy mogą uzyskiwać dostęp do danych telemetrycznych. Firma Microsoft udostępnia dane osobowe naszych klientów osobom trzecim, z wyjątkiem uznania klienta lub ograniczona celów opisanych w [Azure stosu — zasady zachowania poufności informacji](https://privacy.microsoft.com/PrivacyStatement). Firma Microsoft udostępnia raporty biznesowe z producentów OEM i partnerów, które obejmują informacje o telemetrii zagregowane, anonimowe. Udostępnianie decyzje danych są wykonywane przez wewnętrzny zespół firmy Microsoft, łącznie z zainteresowanymi stronami zarządzania prywatności, prawne i danych.

Firma Microsoft uznaje w i rozwiązań minimalizacja informacji. Staramy się zebrać tylko te informacje, musimy, czy przechowujemy tylko tak długo jak jest potrzebna do świadczenia usług lub do analizy. Wiele informacji dotyczących sposobu funkcjonowania systemu Azure stosu i usług Azure jest usuwany w ciągu sześciu miesięcy. Podsumowanie lub zagregowane dane są przechowywane przez dłuższy okres.

Zdajemy prywatności i bezpieczeństwa informacji klientów jest ważne. Przekierowaliśmy przemyślane i wszechstronne podejście do klienta prywatności i ochrony danych klienta z usługi Azure stosu. Administratorzy IT mogą kontrolki umożliwiające dostosowanie funkcji i ustawień prywatności w dowolnym momencie. Wynika z naszych zobowiązań przezroczystość i zaufania:
- Możemy Otwórz za pomocą klientów dotyczące typu danych, które zbieramy.
- Testujemy przedsiębiorstwa w formancie — można dostosowywać ustawień prywatności.
- Najpierw testujemy klienta prywatności i bezpieczeństwa.
- Firma Microsoft są niewidoczne o sposobie używania pobiera telemetrii.
- Używamy danych telemetrycznych do poprawy środowiska użytkownika.

Microsoft zamierzasz zbierać poufne informacje, takie jak numery kart kredytowych, nazwy użytkowników i hasła, adresy e-mail lub innych podobnie poufne informacje. Jeśli określić przypadkowo otrzymano informacje poufne, możemy ją usunąć.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Przykłady jak firma Microsoft używa danych telemetrii
Dane telemetryczne odgrywa ważną rolę w NAS szybko zidentyfikować i rozwiązać problemy ze zgodnością krytyczne w wdrożeń klientów i konfiguracji. Wgląd w dane telemetryczne, które zbieramy Pomóż nam szybkie identyfikowanie problemów z usługami lub konfiguracji sprzętu. Zdolność firmy Microsoft można pobrać te dane z klientów i ulepszenia dysku w ekosystemie pomaga podnieść pasek jakość naszych zintegrowanych rozwiązań stosu Azure.

Dane telemetryczne pomaga również firmy Microsoft, aby lepiej zrozumieć, jak klienci wdrażać składniki, korzystanie z funkcji i korzystania z usług w celach biznesowych. Uzyskiwanie szczegółowych informacji z tych danych pomaga ustalić ich priorytety engineering inwestycji w obszarach, które może wpływać bezpośrednio na środowiska i obciążeń klientów.

Oto kilka przykładów użycia przez klientów z kontenerów, magazynu i konfiguracji sieci, które są skojarzone z rolami stosu Azure. Możemy również użyć szczegółowych danych do ulepszenia dysku i analizy na niektóre z naszych zarządzanie i monitorowanie rozwiązań. Pomaga klientom w celu zdiagnozowania problemów dotyczących jakości i Zapisz pieniądze, wprowadzając obsługę mniejszej liczby wywołań do firmy Microsoft.

## <a name="manage-telemetry-collection"></a>Zarządzanie gromadzenia danych telemetrii
Nie zaleca się wyłączenie telemetrii w organizacji jako dane telemetryczne zawiera dane, które dyski produktu ulepszone funkcje i stabilność. Wiemy, że w niektórych przypadkach może to być konieczne.

W takich przypadkach można skonfigurować poziom telemetrii wysyłane do firmy Microsoft przy użyciu przed wdrożeniem ustawień rejestru lub punktów końcowych Telemetrii po wdrożeniu.

### <a name="set-telemetry-level-in-the-windows-registry"></a>Ustaw poziom telemetrii w rejestrze systemu Windows
Edytor rejestru systemu Windows należy ręcznie ustawić poziom telemetrii na komputerze hosta fizycznego, przed wdrożeniem stosu Azure. Jeśli zasady zarządzania już istnieje, takie jak zasady grupy zastępuje ustawienie rejestru.

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

Poziomy dane telemetryczne są zbiorcze i kategorie do czterech poziomów (0-3):

**0 (zabezpieczenia)**. Tylko dane zabezpieczeń. Zabezpiecz informacje, które są wymagane, aby zapewnić systemu operacyjnego, włącznie z danymi o połączone środowisko użytkownika i dane telemetryczne ustawienia składnika i usługa Windows Defender. Brak danych telemetrycznych do określonych stosu Azure jest emitowany na tym poziomie.

**1 (podstawowe)**. Dane zabezpieczeń i podstawowe Kondycja i jakość danych. Informacje o urządzeniach podstawowych, w tym: dane dotyczące jakości, zgodności aplikacji, dane dotyczące użycia aplikacji i danych z poziomu zabezpieczeń. Ustawienie poziomu telemetrii na telemetrii stosu Azure umożliwia podstawowe. Dane zebrane na tym poziomie obejmują:

- **Informacje o urządzeniach podstawowych** czy pomaga zapewnić zrozumienia typów i konfiguracje natywnych i zwirtualizowanych wystąpień systemu Windows Server 2016 w ekosystemie, w tym:
 - Atrybuty maszyny, takie jak OEM, modelu,
 - Atrybuty sieci, takie jak liczbę i szybkość kart sieciowych
 - Procesor i pamięć atrybuty, takie jak liczba rdzeni, rozmiar pamięci
 - Magazyn atrybutów, takich jak liczba dysków, typ i rozmiar.
- **Funkcja telemetrii**, takie jak procent przekazane zdarzenia, porzuconych zdarzeń oraz za ostatni Przekaż czasu.
- **Informacje dotyczące jakości** który ułatwia opracowywanie podstawową wiedzę na temat sposobu wykonywania jest stos Azure firmy Microsoft. Przykładem jest liczba alertów krytycznych na określoną konfiguracją sprzętu.
- ** Dane zgodności pomaga zapewnić zrozumienia o tym, które dostawców zasobów są zainstalowane w systemie i maszyny wirtualnej i identyfikuje potencjalne problemy ze zgodnością.

**2 (rozszerzony)**. Dodatkowe informacje szczegółowe, w tym: jak używać systemu operacyjnego i innych usług Azure stosu, jak wykonać dane o niezawodności zaawansowane i danych z poziomu podstawowego i zabezpieczeń.

**3 (pełne)**. Wszystkie dane niezbędne do identyfikowania i pomoc, aby rozwiązać problemy, a także dane z **zabezpieczeń**, **podstawowe**, i **rozszerzony** poziomów.

> [!NOTE]
> Wartość poziomu telemetrii domyślna to 2 (rozszerzony).

Wyłączanie telemetrii systemu Windows i stosu Azure wyłącza telemetrię SQL. Aby uzyskać dodatkowe informacje o skutki ustawieniami telemetrii systemu Windows Server, odwołanie [Windows Telemetrii w oficjalnym dokumencie](https://aka.ms/winservtelemetry).

> [!IMPORTANT]
> Te poziomy telemetrii dotyczą tylko składniki programu Microsoft Azure stosu. Składniki oprogramowania innych firm i usług, które są uruchomione na hoście cyklu życia sprzętu z dostawców sprzętu będących partnerami stosu Azure może komunikować się z ich usługi w chmurze poza tymi poziomami telemetrii. Powinien współpracować z dostawcy rozwiązań sprzętu stosu Azure, aby zrozumieć ich zasad telemetrii i jak włączyć lub zrezygnować.

### <a name="enable-or-disable-telemetry-after-deployment"></a>Włączanie lub wyłączanie telemetrii po wdrożeniu

Aby włączyć lub wyłączyć telemetrii po wdrożeniu, musisz mieć dostęp do uprzywilejowanych punktu końcowego (program ten), który jest narażony na maszynach wirtualnych ERCS.
1.  Aby włączyć: `Set-Telemetry -Enable`
2.  Aby wyłączyć: `Set-Telemetry -Disable`

PARAMETR szczegółów:
> . PARAMETR Enable - Włącz przekazywania danych telemetrii

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
[Dodaj element marketplace](asdk-marketplace-item.md)
