---
title: Zaawansowana ochrona przed zagrożeniami w usłudze Windows Defender przy użyciu Azure Security Center
description: W tym dokumencie przedstawiono integrację między programem Azure Security Center a zaawansowaną ochroną przed zagrożeniami w usłudze Windows Defender.
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
ms.author: v-mohabe
ms.openlocfilehash: 87f5a14bcd6003ad81b663ed97e5349dcbff2a30
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2019
ms.locfileid: "68296527"
---
# <a name="windows-defender-advanced-threat-protection-with-azure-security-center"></a>Zaawansowana ochrona przed zagrożeniami w usłudze Windows Defender przy użyciu Azure Security Center

Usługa Azure Security Center rozszerza ofertę platform ochrony obciążeń w chmurze dzięki integracji z usługą [Windows Defender Advanced Threat Protection](https://www.microsoft.com/en-us/WindowsForBusiness/windows-atp) (ATP).
Ta zmiana zapewnia kompleksowe możliwości dotyczące wykrywania i odpowiedzi punktu końcowego (EDR, Endpoint Detection and Response). Dzięki integracji z usługą Windows Defender ATP można wychwycić nietypowe. Możesz także wykryć zaawansowane ataki i odpowiedzieć na nie w punktach końcowych serwera monitorowanych przez Azure Security Center.

## <a name="windows-defender-atp-features-in-security-center"></a>Funkcje Windows Defender ATP w Security Center

W przypadku korzystania z programu Windows Defender ATP uzyskasz następujące korzyści:

- **Czujniki wykrywania wykrycia po wykryciu nowej generacji**: Czujniki usługi Windows Defender ATP dla serwerów z systemem Windows zbierają ogromną gamę sygnałów z zachowaniem.

- **Wykrywanie naruszeń po wykorzystaniu analiz, oparte na chmurze**: Usługa Windows Defender ATP szybko dostosowuje się do zmieniających się zagrożeń. Używa zaawansowanej analizy i danych Big Data. Usługa Windows Defender ATP jest wzmocniona możliwościami Intelligent Security Graph z sygnałami w systemach Windows, Azure i Office w celu wykrywania nieznanych zagrożeń. Zapewnia alerty funkcjonalne i umożliwia szybkie reagowanie na nie.

- **Analiza zagrożeń**: Usługa Windows Defender ATP identyfikuje narzędzia, techniki i procedury atakujących. Po jego wykryciu program generuje alerty. Używa on danych wygenerowanych przez program Microsoft Threat myśliwych i zespoły ds. zabezpieczeń, uzupełnione o analizy udostępniane przez partnerów.

Te możliwości są teraz dostępne w usłudze Azure Security Center:

- **Automatyczne**dołączanie: Czujnik usługi Windows Defender ATP jest automatycznie włączany dla serwerów z systemem Windows, które są dołączane do Azure Security Center.

- **Pojedyncze okienko szkła**: W konsoli Azure Security Center są wyświetlane alerty programu Windows Defender ATP.

- **Szczegółowa badanie maszynowe**: Azure Security Center klienci mogą uzyskać dostęp do konsoli programu Windows Defender ATP w celu przeprowadzenia szczegółowej analizy w celu odzyskania zakresu naruszenia.

![Azure Security Center, wyświetlanie listy alertów i ogólnych informacji o każdym alercie](media/security-center-wdatp/image1.png)

Można dokładniej zbadać alert, przestawiając się do programu Windows Defender ATP. Można wyświetlić dodatkowe informacje, takie jak drzewo procesu alertu i wykres incydentu. Możesz również wyświetlić szczegółową oś czasu komputera, która pokazuje każde zachowanie w okresie historycznym przez maksymalnie sześć miesięcy.

![Strona Windows Defender ATP ze szczegółowymi informacjami o alercie](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Obsługa platform

Usługa Windows Defender ATP w Security Center obsługuje wykrywanie w systemach operacyjnych Windows Server 2012 R2 i Windows Server 2016 należących do standardowej subskrypcji usługi.

> [!NOTE]
> W przypadku używania Azure Security Center do monitorowania serwerów usługa Windows Defender ATP jest automatycznie tworzona i domyślnie są przechowywane dane ATP dotyczące programu Windows Defender. Jeśli musisz przenieść dane do innej lokalizacji, musisz skontaktować się z pomoc techniczna firmy Microsoft w celu zresetowania dzierżawy.

## <a name="onboarding-servers-to-security-center"></a>Dołączanie serwerów do Security Center 

Aby dołączać serwery do Security Center, kliknij przycisk **Przejdź do Azure Security Center do** dołączania serwerów z usługi Windows Defender ATP Server dołączanie.

1. W bloku  dołączania wybierz lub Utwórz obszar roboczy, w którym mają zostać zapisane dane. <br>
2. Jeśli nie widzisz wszystkich obszarów roboczych, może to być spowodowane brakiem uprawnień, upewnij się, że obszar roboczy jest ustawiony na warstwę Standardowa usługi Azure Security. Aby uzyskać więcej informacji [, zobacz Uaktualnianie do warstwy standardowa Security Center w celu zwiększenia bezpieczeństwa](security-center-pricing.md).
    
3. Wybierz pozycję **Dodaj serwery** , aby wyświetlić instrukcje dotyczące sposobu instalowania Microsoft Monitoring Agent. 

4. Po dołączeniu można monitorować maszyny w obszarze **obliczenia i aplikacje**.

   ![Dołączanie komputerów](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-windows-defender-atp-integration"></a>Włącz integrację z programem Windows Defender ATP

Aby sprawdzić, czy integracja z usługą Windows Defender ATP jest włączona, wybierz pozycję Cennik usługi **Security Center** >  **& Ustawienia** > kliknij subskrypcję.

  ![Azure Security Center Zarządzanie zasadami](media/security-center-wdatp/policy-management.png)

W tym miejscu można zobaczyć aktualnie włączone integracje.

  ![Strona ustawień wykrywania zagrożeń Azure Security Center z włączoną integracją z usługą Windows Defender ATP](media/security-center-wdatp/enable-integrations.png)

- Jeśli serwery zostały już dołączone do Azure Security Center warstwy Standardowa, nie musisz podejmować żadnych dalszych działań. Azure Security Center automatycznie dołączać serwery do programu Windows Defender ATP. Może to potrwać do 24 godzin.

- Jeśli nie dołączysz serwerów do Azure Security Center warstwy Standardowa, Dołącz je do Azure Security Center jak zwykle.

- W przypadku dołączania serwerów za pomocą programu Windows Defender ATP:
  - Zapoznaj się z dokumentacją, aby uzyskać wskazówki dotyczące [odłączania maszyn serwera](https://go.microsoft.com/fwlink/p/?linkid=852906).
  - Dołącz te serwery do Azure Security Center.

## <a name="access-to-the-windows-defender-atp-portal"></a>Dostęp do portalu Windows Defender ATP

Postępuj zgodnie z instrukcjami w temacie [Przypisywanie dostępu użytkowników do portalu](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/assign-portal-access-windows-defender-advanced-threat-protection).

## <a name="set-the-firewall-configuration"></a>Ustawianie konfiguracji zapory

Jeśli masz serwer proxy lub zaporę blokującą ruch anonimowy, ponieważ czujnik usługi Windows Defender ATP jest połączony z kontekstu systemu, upewnij się, że ruch anonimowy jest dozwolony. Postępuj zgodnie z instrukcjami w temacie [Włączanie dostępu do adresów URL usługi Windows Defender ATP na serwerze proxy](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/configure-proxy-internet-windows-defender-advanced-threat-protection#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server).

## <a name="test-the-feature"></a>Testowanie funkcji

Aby wygenerować niegroźny alert testu Windows Defender ATP:

1. Użyj Pulpit zdalny, aby uzyskać dostęp do maszyny wirtualnej z systemem Windows Server 2012 R2 lub z maszyną wirtualną z systemem Windows Server 2016.  Otwórz okno wiersza polecenia.

2. W wierszu polecenia skopiuj i uruchom następujące polecenie. Okno wiersza polecenia zostanie zamknięte automatycznie.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-WDATP-test\\invoice.exe'); Start-Process 'C:\\test-WDATP-test\\invoice.exe'
    ```

   ![Okno wiersza polecenia z powyższym poleceniem](media/security-center-wdatp/image4.jpeg)

3. Jeśli polecenie zakończy się pomyślnie, zobaczysz nowy Alert na pulpicie nawigacyjnym Azure Security Center i portalu Windows Defender ATP. Ten alert może potrwać kilka minut.

4. Aby sprawdzić alert w Security Center, przejdź do **alertów** >  zabezpieczeń**podejrzany wiersz polecenia programu PowerShell**.

5. W oknie badanie wybierz link, aby przejść do portalu usługi Windows Defender ATP.

## <a name="next-steps"></a>Kolejne kroki

- [Ustawianie zasad zabezpieczeń w Azure Security Center](tutorial-security-policy.md): Dowiedz się, jak skonfigurować zasady zabezpieczeń dla subskrypcji i grup zasobów platformy Azure.
- [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Azure Security Center](security-center-recommendations.md): Dowiedz się, jak zalecenia ułatwiają ochronę zasobów platformy Azure.
- [Monitorowanie kondycji zabezpieczeń w Azure Security Center](security-center-monitoring.md): Informacje na temat sposobu monitorowania kondycji zasobów platformy Azure.
