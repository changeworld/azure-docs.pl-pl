---
title: Usługa Windows Defender Zaawansowana ochrona przed zagrożeniami w usłudze Azure Security Center
description: Ten dokument wprowadza integrację między usługą Azure Security Center i zaawansowanej ochrony przed zagrożeniami programu Windows Defender.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2018
ms.author: monhaber
ms.openlocfilehash: 1737856067b2490db4a993b4383b320cb13a7774
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551765"
---
# <a name="windows-defender-advanced-threat-protection-with-azure-security-center"></a>Usługa Windows Defender Zaawansowana ochrona przed zagrożeniami w usłudze Azure Security Center

Usługa Azure Security Center rozszerza ofertę platform ochrony obciążeń w chmurze dzięki integracji z usługą [Windows Defender Advanced Threat Protection](https://www.microsoft.com/en-us/WindowsForBusiness/windows-atp) (ATP).
Ta zmiana zapewnia kompleksowe możliwości dotyczące wykrywania i odpowiedzi punktu końcowego (EDR, Endpoint Detection and Response). Dzięki integracji z usługą Windows Defender ATP można wykryć nieprawidłowości. Można również wykrywanie oraz reagowanie na zaawansowane ataki na punkty końcowe serwera monitorowane przez usługę Azure Security Center.

## <a name="windows-defender-atp-features-in-security-center"></a>Funkcje usługi Windows Defender ATP w usłudze Security Center

Korzystając z usługi Windows Defender ATP zapewnia następujące korzyści:

- **Czujniki wykrywanie naruszenia wpis następnej generacji**: Usługi Windows Defender ATP czujniki, aby uzyskać serwery Windows zbierać szeroką gamę sygnały zachowań.

- **Wykrywanie naruszenia post na podstawie analizy, oparta na chmurze**: Windows Defender ATP szybko dostosowuje do zmieniających się zagrożeń. Korzysta ona z zaawansowanych analiz i danych big data. Windows Defender ATP jest większy dzięki możliwościom usługi Intelligent Security Graph przy użyciu sygnały Windows, platformy Azure i pakietu Office w celu wykrywania nieznanych zagrożeń. Ona alerty z możliwością wykonywania akcji i umożliwia szybkie reagowanie.

- **Analiza zagrożeń**: Windows Defender ATP identyfikuje narzędzia osoba atakująca, technik i procedur. Po wykryciu tych generuje alerty. Korzysta ona z danymi wygenerowanymi przez łowców zagrożeń firmy Microsoft i zespoły zabezpieczeń, rozszerzony o analizy udostępnianych przez partnerów.

Te możliwości są teraz dostępne w usłudze Azure Security Center:

- **Automatyczne dołączanie**: Czujnik usługi Windows Defender ATP jest automatycznie włączone dla serwerów Windows, które są dołączone do usługi Azure Security Center.

- **Jedną taflę szkła**: Konsola usługi Azure Security Center wyświetla alerty usługi Windows Defender ATP.

- **Szczegółowe badanie maszyny**: Usługa Azure Security Center klientom dostęp do konsoli usługi Windows Defender ATP w celu wykonania szczegółowe badania, aby odkryć zakresu naruszenia.

![Azure Security Center, wyświetlanie alertów i ogólnych informacji dotyczących poszczególnych alertów](media/security-center-wdatp/image1.png)

Możesz badać alertu, przez przestawianie w usłudze Windows Defender ATP. Można przeglądać dodatkowe informacje takie jak drzewo procesów alertu i zdarzeń wykresu. Widać również szczegółowe osi czasu, pokazujący każdego zachowanie historycznych okres sześciu miesięcy.

![Strona usługi Windows Defender ATP z szczegółowe informacje na temat alertu](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Obsługa platform

Program Windows Defender ATP w usłudze Security Center obsługuje wykrywanie w systemach operacyjnych Windows Server 2012 R2 i Windows Server 2016, należącymi do subskrypcji usług w warstwie standardowa.

> [!NOTE]
> Gdy używasz usługi Azure Security Center do monitorowania serwerów dzierżawy usługi Windows Defender ATP jest automatycznie tworzony i danych usługi Windows Defender ATP jest domyślnie przechowywane w Europie. Jeśli musisz przenieść dane do innej lokalizacji, należy skontaktować się z Microsoft Support zresetować dzierżawy.

## <a name="onboarding-servers-to-security-center"></a>Serwery przy dołączaniu do usługi Security Center 

Aby dodać serwery do usługi Security Center, kliknij przycisk **przejdź do usługi Azure Security Center, aby dołączyć serwery** z dołączania serwera usługi Windows Defender ATP.

1. W **dołączania** bloku wybierz lub Utwórz obszar roboczy, w którym będą przechowywane dane. <br>
2. Jeśli nie widzisz wszystkich Twoich obszarach roboczych, może być spowodowane brakiem uprawnień, upewnij się, że obszar roboczy jest ustawiony do warstwy Standard zabezpieczeń platformy Azure. Aby uzyskać więcej informacji, zobacz [uaktualnienie do warstwy standardowa Security Center w celu uzyskania zwiększonych zabezpieczeń](security-center-pricing.md).
    
3. Wybierz **dodawania serwerów** Aby wyświetlić instrukcje dotyczące sposobu instalowania programu Microsoft Monitoring Agent. 

4. Po dołączeniu, można monitorować maszyny znajdujące się w **obliczeniowe i aplikacje**.

   ![Dodawanie komputerów](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-windows-defender-atp-integration"></a>Włączanie integracji usługi Windows Defender ATP

Aby sprawdzić, czy integracja usługi Windows Defender ATP jest włączona, wybierz **usługi Security center** > **ceny u & stawienia** > kliknij w ramach Twojej subskrypcji.

  ![Zarządzanie zasadami Centrum zabezpieczeń Azure](media/security-center-wdatp/policy-management.png)

Tutaj można zobaczyć integracji aktualnie włączone.

  ![Strona ustawień wykrywania zagrożeń usługi Azure Security Center z włączoną integracją usługi Windows Defender ATP](media/security-center-wdatp/enable-integrations.png)

- Jeśli masz już dołączone serwery do usługi Azure Security Center w warstwie standardowa, muszą wykonywać żadnych dalszych czynności. Usługa Azure Security Center będzie automatycznie dołączyć serwery do usługi Windows Defender ATP. Może to potrwać do 24 godzin.

- Jeśli nigdy nie dołączono serwery do usługi Azure Security Center w warstwie standardowa dołączyć je do usługi Azure Security Center w zwykły sposób.

- Jeśli dołączono serwerami za pośrednictwem usługi Windows Defender ATP:
  - Zapoznaj się z dokumentacją, aby uzyskać wskazówki na [sposób odłączony maszyny server](https://go.microsoft.com/fwlink/p/?linkid=852906).
  - Dołączanie tych serwerów w usłudze Azure Security Center.

## <a name="access-to-the-windows-defender-atp-portal"></a>Dostęp do portalu usługi Windows Defender ATP

Postępuj zgodnie z instrukcjami w [przydzielać użytkownikom dostęp do portalu](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/assign-portal-access-windows-defender-advanced-threat-protection).

## <a name="set-the-firewall-configuration"></a>Ustaw konfigurację zapory

Jeśli masz serwer proxy lub zapory, która blokuje ruch anonimowe, jak czujnik usługi Windows Defender ATP jest połączenie w kontekście systemu, upewnij się, że anonimowe ruch jest dozwolony. Postępuj zgodnie z instrukcjami w [zapewnianie dostępu do adresów URL usługi Windows Defender ATP na serwerze proxy](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/configure-proxy-internet-windows-defender-advanced-threat-protection#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server).

## <a name="test-the-feature"></a>Testowanie funkcji

Aby wygenerować niegroźne alert testowy Windows Defender ATP:

1. Pulpit zdalny dostęp do maszyny Wirtualnej z systemem Windows Server 2012 R2 lub maszyny Wirtualnej z systemem Windows Server 2016.  Otwórz okno wiersza polecenia.

2. W wierszu polecenia należy skopiować i uruchomić następujące polecenie. W oknie wiersza polecenia zostanie automatycznie zamknięte.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-WDATP-test\\invoice.exe'); Start-Process 'C:\\test-WDATP-test\\invoice.exe'
    ```

   ![Okno wiersza polecenia przy użyciu powyższego polecenia](media/security-center-wdatp/image4.jpeg)

3. Jeśli polecenie zakończy się pomyślnie, zobaczysz nowego alertu na pulpicie nawigacyjnym Centrum zabezpieczeń Azure i portal usługi Windows Defender ATP. Ten alert może potrwać kilka minut.

4. Aby zapoznać się z alertu w usłudze Security Center, przejdź do **alerty zabezpieczeń** >  **podejrzane wiersz polecenia programu Powershell**.

5. W oknie badania wybierz łącze, aby przejść do portalu usługi Windows Defender ATP.

## <a name="next-steps"></a>Kolejne kroki

- [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md): Informacje o sposobie konfigurowania zasad zabezpieczeń dla subskrypcji platformy Azure i grup zasobów.
- [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md): Dowiedz się, w jaki sposób zalecenia ułatwiają ochronę zasobów platformy Azure.
- [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md): Informacje na temat sposobu monitorowania kondycji zasobów platformy Azure.
