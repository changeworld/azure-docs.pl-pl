---
title: Zaawansowana ochrona przed zagrożeniami usługi Microsoft Defender — Centrum zabezpieczeń platformy Azure
description: W tym dokumencie wprowadzono integrację między usługą Azure Security Center a zaawansowaną ochroną przed zagrożeniami usługi Microsoft Defender.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: memildin
ms.openlocfilehash: 5136a00421aebaa72998b1dfcdf75feb935851c6
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435454"
---
# <a name="microsoft-defender-advanced-threat-protection-with-azure-security-center"></a>Zaawansowana ochrona przed zagrożeniami usługi Microsoft Defender za pomocą usługi Azure Security Center

Usługa Azure Security Center rozszerza swoją ofertę platform ochrony obciążenia w chmurze, integrując się z [zaawansowaną ochroną przed zagrożeniami](https://www.microsoft.com/microsoft-365/windows/microsoft-defender-atp) (ATP) usługi Microsoft Defender.
Ta zmiana zapewnia kompleksowe możliwości dotyczące wykrywania i odpowiedzi punktu końcowego (EDR, Endpoint Detection and Response). Dzięki integracji z programem Microsoft Defender ATP można wykryć nieprawidłowości. Można również wykryć i reagować na zaawansowane ataki na punkty końcowe serwera monitorowane przez usługę Azure Security Center.

## <a name="microsoft-defender-atp-features-in-security-center"></a>Funkcje narzędzia Microsoft Defender ATP w Centrum zabezpieczeń

Korzystając z usługi Microsoft Defender ATP, otrzymujesz:

- **Zaawansowane czujniki wykrywania po naruszeniu**: czujniki Microsoft Defender ATP dla serwerów Windows zbierają szeroką gamę sygnałów behawioralnych.

- **Oparte na analizie, oparte na chmurze wykrywanie po naruszeniu :** Microsoft Defender ATP szybko dostosowuje się do zmieniających się zagrożeń. Wykorzystuje zaawansowaną analizę i duże zbiory danych. Usługa Microsoft Defender ATP jest wzmacniana przez moc inteligentnego wykresu zabezpieczeń z sygnałami w systemach Windows, Azure i Office w celu wykrycia nieznanych zagrożeń. Zapewnia alerty zasłaniane i umożliwia szybką reakcję.

- **Analiza zagrożeń:** Narzędzie Microsoft Defender ATP generuje alerty, gdy identyfikuje narzędzia, techniki i procedury atakującego. Wykorzystuje dane generowane przez łowców zagrożeń firmy Microsoft i zespoły zabezpieczeń, wzbogacone o analizy dostarczone przez partnerów.

Następujące funkcje są teraz dostępne w usłudze Azure Security Center:

- **Automatyczne dołączanie:** czujnik Narzędzia ATP usługi Microsoft Defender jest automatycznie włączany dla serwerów systemu Windows, które są dołączane do usługi Azure Security Center.

- **Pojedyncze okienko szkła:** w konsoli usługi Azure Security Center są wyświetlane alerty usługi Microsoft Defender ATP.

- **Szczegółowe badanie maszyny:** klienci usługi Azure Security Center mogą używać konsoli Microsoft Defender ATP do przeprowadzenia szczegółowego dochodzenia w celu wykrycia zakresu naruszenia.

![Usługa Azure Security Center, wyświetlająca listę alertów i ogólne informacje o każdym alertie](media/security-center-wdatp/image1.png)

Aby zbadać dalej, należy użyć narzędzia Microsoft Defender ATP. Narzędzie Microsoft Defender ATP zawiera dodatkowe informacje, takie jak drzewo procesu alertów i wykres zdarzeń. Można również wyświetlić szczegółową oś czasu maszyny, która pokazuje każde zachowanie przez okres historyczny do sześciu miesięcy.

![Strona Narzędzia Microsoft Defender ATP ze szczegółowymi informacjami o alertie](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Obsługa platform

Usługa Microsoft Defender ATP w centrum zabezpieczeń obsługuje wykrywanie na systemach Windows Server 2016, 2012 R2 i 2008 R2 ZWZ1, dla maszyn wirtualnych platformy Azure potrzebujesz subskrypcji warstwy standardowej, a dla maszyn wirtualnych innych niż Azure potrzebujesz warstwy standardowej tylko na poziomie obszaru roboczego.

> [!NOTE]
> Podczas korzystania z usługi Azure Security Center do monitorowania serwerów, dzierżawa usługi Microsoft Defender ATP jest tworzony automatycznie i microsoft defender ATP dane są przechowywane w Europie domyślnie. Jeśli musisz przenieść dane do innej lokalizacji, musisz skontaktować się z pomocą techniczną firmy Microsoft, aby zresetować dzierżawę. Monitorowanie punktu końcowego serwera z wykorzystaniem tej integracji zostało wyłączone dla klientów usługi Office 365 GCC.

## <a name="onboarding-servers-to-security-center"></a>Dołączanie serwerów do usługi Security Center 

Aby włączyć serwery wbudowane do Centrum zabezpieczeń, kliknij pozycję **Przejdź do usługi Azure Security Center, aby włączyć serwery wbudowane** na serwerze usługi Microsoft Defender ATP.

1. W obszarze **Dołączanie** zaznacz lub utwórz obszar roboczy, w którym mają być przechowywane dane. <br>
2. Jeśli nie widzisz wszystkich obszarów roboczych, może to być spowodowane brakiem uprawnień, upewnij się, że obszar roboczy jest ustawiony na warstwę Azure Security Standard. Aby uzyskać więcej informacji, zobacz [Uaktualnianie do warstwy standardowej usługi Security Center, aby uzyskać większe bezpieczeństwo](security-center-pricing.md).
    
3. Wybierz **pozycję Dodaj serwery,** aby wyświetlić instrukcje dotyczące instalowania agenta usługi Log Analytics. 

4. Po wliczeniem można monitorować maszyny w obszarze **Obliczenia i aplikacje**.

   ![Komputery pokładowe](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-microsoft-defender-atp-integration"></a>Włączanie integracji z pakietem MICROSOFT Defender ATP

Aby wyświetlić, czy integracja usługi Microsoft Defender ATP jest włączona, wybierz**pozycję Ustawienia & cennika** **centrum** > zabezpieczeń > kliknij subskrypcję.
W tym miejscu można zobaczyć integracje aktualnie włączone.

  ![Strona ustawień wykrywania zagrożeń usługi Azure Security Center z włączoną integracją z usługą Microsoft Defender ATP](media/security-center-wdatp/enable-integrations.png)

- Jeśli serwery zostały już włączone do warstwy standardowej usługi Azure Security Center, nie musisz podejmować żadnych dalszych działań. Usługa Azure Security Center automatycznie dołącza serwery do usługi Microsoft Defender ATP. Dołączanie może potrwać do 24 godzin.

- Jeśli serwery nigdy nie były dołączane do warstwy standardowej usługi Azure Security Center, należy je w zwykły sposób do usługi Azure Security Center.

- Jeśli serwery zostały włączone za pośrednictwem usługi Microsoft Defender ATP:
  - Zapoznaj się z dokumentacją, aby uzyskać wskazówki [dotyczące wyłączania komputerów serwerowych.](https://go.microsoft.com/fwlink/p/?linkid=852906)
  - Po dołączanie tych serwerów do usługi Azure Security Center.

## <a name="access-to-the-microsoft-defender-atp-portal"></a>Dostęp do portalu Microsoft Defender ATP

Postępuj zgodnie z instrukcjami w [Przypisz dostęp użytkownika do portalu](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access).

## <a name="set-the-firewall-configuration"></a>Ustawianie konfiguracji zapory

Jeśli masz serwer proxy lub zaporę, która blokuje ruch anonimowy, ponieważ czujnik Narzędzia ATP usługi Microsoft Defender łączy się z kontekstu systemu, upewnij się, że ruch anonimowy jest dozwolony. Postępuj zgodnie z instrukcjami w [włącz dostęp do adresów URL usługi Microsoft Defender ATP na serwerze proxy](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server).

## <a name="test-the-feature"></a>Przetestuj funkcję

Aby wygenerować łagodny alert testowy narzędzia Microsoft Defender ATP:

1. Utwórz folder "C:\test-MDATP-test".

1. Użyj pulpitu zdalnego, aby uzyskać dostęp do maszyny Wirtualnej systemu Windows Server 2012 R2 lub maszyny Wirtualnej systemu Windows Server 2016. Otwórz okno wiersza polecenia.

1. W wierszu polecenia skopiuj i uruchom następujące polecenie. Okno Wiersz polecenia zostanie zamknięte automatycznie.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```

   ![Okno wiersza polecenia z powyższym poleceniem](media/security-center-wdatp/image4.jpeg)

3. Jeśli polecenie zakończy się pomyślnie, zobaczysz nowy alert na pulpicie nawigacyjnym usługi Azure Security Center i portalu Microsoft Defender ATP. Ten alert może potrwać kilka minut.

4. Aby przejrzeć alert w Centrum zabezpieczeń, przejdź do **pozycji Alerty** > zabezpieczeń**Suspicious PowerShell CommandLine**.

5. W oknie badania wybierz łącze, aby przejść do portalu Microsoft Defender ATP.

## <a name="next-steps"></a>Następne kroki

- [Platformy i funkcje obsługiwane przez usługę Azure Security Center](security-center-os-coverage.md)
- [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center:](tutorial-security-policy.md)Dowiedz się, jak skonfigurować zasady zabezpieczeń dla subskrypcji platformy Azure i grup zasobów.
- [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w usłudze Azure Security Center:](security-center-recommendations.md)Dowiedz się, jak zalecenia pomagają chronić zasoby platformy Azure.
- [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
