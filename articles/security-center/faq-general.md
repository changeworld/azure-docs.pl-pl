---
title: Często zadawane pytania dotyczące Azure Security Center — pytania ogólne
description: Często zadawane pytania ogólne dotyczące Azure Security Center, produktu, który pomaga zapobiegać zagrożeniom, wykrywać je i reagować na nie
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: b13e5e0000cdd8e33f459400cc4c24d107ccaba3
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604794"
---
# <a name="faq---general-questions-about-azure-security-center"></a>Często zadawane pytania — ogólne pytania dotyczące Azure Security Center

## <a name="what-is-azure-security-center"></a>Co to jest Centrum zabezpieczeń Azure?
Azure Security Center pomaga zapobiegać zagrożeniom, wykrywać je i reagować na nie dzięki zwiększonej widoczności i kontroli nad bezpieczeństwem zasobów. Umożliwia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami dla wszystkich subskrypcji, pomaga wykrywać zagrożenia, które w przeciwnym razie mogłyby pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań zabezpieczających.

Security Center używa Microsoft Monitoring Agent do zbierania i przechowywania danych. Szczegółowe informacje znajdują się w temacie [zbieranie danych w Azure Security Center](security-center-enable-data-collection.md).


## <a name="how-do-i-get-azure-security-center"></a>Jak uzyskać usługę Azure Security Center?
Azure Security Center jest włączona z subskrypcją Microsoft Azure i jest dostępna z [Azure Portal](https://azure.microsoft.com/features/azure-portal/). Aby uzyskać do niego dostęp, [Zaloguj się do portalu](https://portal.azure.com), wybierz pozycję **Przeglądaj**i przewiń do **Security Center**.


## <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Jakie zasoby platformy Azure są monitorowane przez usługę Azure Security Center?
Usługa Azure Security Center monitoruje następujących zasobów platformy Azure:

* Maszyny wirtualne (z uwzględnieniem [Cloud Services](../cloud-services/cloud-services-choose-me.md))
* Zestawy skalowania maszyn wirtualnych
* Sieci wirtualne platformy Azure
* Usługa Azure SQL
* Konto usługi Azure Storage
* Web Apps platformy Azure (w [App Service Environment](../app-service/environment/intro.md))
* Rozwiązań partnerskich zintegrowanych z subskrypcją platformy Azure, takich jak zapory aplikacji sieci web na maszynach wirtualnych i w środowisku App Service Environment

Ponadto komputery z systemem innym niż Azure (łącznie z lokalnymi) mogą być monitorowane przez Azure Security Center (obsługiwane są zarówno [komputery Windows](./quick-onboard-windows-computer.md) , jak i komputery z systemem [Linux](./quick-onboard-linux-computer.md) )


## <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Jak wyświetlić bieżący stan zabezpieczeń zasobów platformy Azure, Moje?
Na stronie **przegląd Security Centeru** są wyświetlane ogólne stan zabezpieczeń środowiska, podzielone na dane dotyczące zasobów obliczeniowych, sieci, magazynu & i aplikacji. Każdy typ zasobu ma przedstawiający wskaźnik, jeśli zidentyfikowano żadnych potencjalnych luk w zabezpieczeniach. Klikając każdy Kafelek Wyświetla listę problemów z zabezpieczeniami identyfikowane przez usługę Security Center oraz spis zasobów w ramach subskrypcji.



## <a name="what-is-a-security-policy"></a>Co to są zasady zabezpieczeń?
Zasady zabezpieczeń określają zestaw mechanizmów kontrolnych, które są zalecane dla zasobów w ramach określonej subskrypcji. W usłudze Azure Security Center można zdefiniować zasady dla subskrypcji platformy Azure zgodnie z wymaganiami w zakresie zabezpieczeń firmy i typem aplikacji oraz poufnością danych w poszczególnych subskrypcjach.

Zasady zabezpieczeń włączone w usłudze Azure Security Center dysku — zalecenia dotyczące zabezpieczeń i monitorowania. Aby dowiedzieć się więcej na temat zasad zabezpieczeń, zobacz [monitorowanie kondycji zabezpieczeń w Azure Security Center](security-center-monitoring.md).


## <a name="who-can-modify-a-security-policy"></a>Kto może modyfikować zasady zabezpieczeń?
Aby zmodyfikować zasady zabezpieczeń, musi być kontem administratora zabezpieczeń lub właścicielem lub współautorem subskrypcji.

Aby dowiedzieć się, jak skonfigurować zasady zabezpieczeń, zobacz [Ustawianie zasad zabezpieczeń w Azure Security Center](tutorial-security-policy.md).


## <a name="what-is-a-security-recommendation"></a>Co to jest zalecana ze względów bezpieczeństwa?
Usługa Azure Security Center analizuje stan zabezpieczeń zasobów platformy Azure. Po zidentyfikowaniu potencjalnych luk w zabezpieczeniach tworzone są zalecenia. Zaleceń prowadzi przez proces konfigurowania wymaganych kontroli. Oto przykłady:

* Inicjowanie obsługi ochrony przed złośliwym kodem do identyfikacji i usuwania złośliwego oprogramowania
* [Sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md) i reguły sterujące ruchem do maszyn wirtualnych
* Inicjowanie obsługi administracyjnej zapory aplikacji sieci web, aby pomóc Ci chronić przed atakami przeznaczone dla aplikacji sieci web
* Wdrażanie brakujących aktualizacji systemu
* Modyfikowanie konfiguracji systemu operacyjnego, które odbiegają od zalecanych standardów

Tylko te zalecenia, które są włączone w zasadach zabezpieczeń są wyświetlane w tym miejscu.



## <a name="what-triggers-a-security-alert"></a>Co wyzwala alert zabezpieczeń?
Usługa Azure Security Center automatycznie zbiera, analizuje i fuses dane dzienników z zasobów platformy Azure, sieci i rozwiązań partnerskich, takich jak ochrony przed złośliwym oprogramowaniem i zapory. Po wykryciu zagrożenia tworzony jest alert zabezpieczeń. Przykłady obejmują wykrywanie:

* Zagrożonych maszyn wirtualnych komunikujących się ze znanymi złośliwymi adresami IP
* Zaawansowanego złośliwego oprogramowania wykrytego za pomocą usługi raportowania błędów Windows
* Ataków siłowych wobec maszyn wirtualnych
* Alerty zabezpieczeń z rozwiązań zabezpieczeń zintegrowanych partnerów, takich jak chroniące przed złośliwym kodem lub zapory aplikacji sieci Web


## Dlaczego zostały zmienione wartości oceny? <a name="secure-score-faq"></a>
Od lutego 2019 Security Center skorygowany wynik kilku zaleceń w celu lepszego dopasowania ich ważności. W wyniku tej korekty mogą wystąpić zmiany ogólnych, bezpiecznych wartości oceny.  Aby uzyskać więcej informacji na temat zabezpieczeń oceny, zobacz [Secure Score calculation](security-center-secure-score.md).


## <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Jaka jest różnica między zagrożenia wykryte i alerty o przez program Microsoft Security Response Center i Azure Security Center?
Narzędzie Microsoft Security Response Center (MSRC) wykonuje monitorowania zabezpieczeń wybierz sieć platformy Azure i infrastrukturą i odbiera skarg analizy i nadużyć zagrożenia pochodzące od innych firm. Gdy MSRC uświadamia sobie, że dane klienta były używane na przez nieupoważnione lub lub że korzystanie z platformy Azure przez klienta nie jest zgodne z warunkami dopuszczalne użycie, Menedżer zdarzenia zabezpieczeń powiadamia klienta. Powiadomienia są zazwyczaj występuje, wysyłając wiadomość e-mail do kontaktów zabezpieczeń określone w usłudze Azure Security Center lub właściciel subskrypcji platformy Azure, jeśli nie określono kontaktu zabezpieczeń.

Usługa Security Center jest usługą platformy Azure, która stale monitoruje środowiska platformy Azure przez klienta i stosuje analytics, aby automatycznie wykrywać szeroką gamę potencjalnie złośliwych działań. Wykrywane odmiany są udostępniane jako alerty zabezpieczeń w pulpicie nawigacyjnym usługi Security Center.