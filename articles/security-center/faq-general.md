---
title: Usługa Azure Security Center — często zadawane pytania — pytania ogólne
description: Często zadawane ogólne pytania dotyczące usługi Azure Security Center, produktu, który pomaga zapobiegać zagrożeniom, wykrywać je i reagować na nie
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
ms.openlocfilehash: f9043ae1414b63f25583d52100774f3e87754a74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77661843"
---
# <a name="faq---general-questions-about-azure-security-center"></a>Często zadawane pytania — ogólne pytania dotyczące usługi Azure Security Center

## <a name="what-is-azure-security-center"></a>Co to jest Centrum zabezpieczeń Azure?
Usługa Azure Security Center pomaga zapobiegać zagrożeniom, wykrywać ich i reagować na nie, zwiększając wgląd w zabezpieczenia zasobów i kontrolę nad nim. Umożliwia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami dla wszystkich subskrypcji, pomaga wykrywać zagrożenia, które w przeciwnym razie mogłyby pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań zabezpieczających.

Usługa Security Center używa agenta monitorowania firmy Microsoft do zbierania i przechowywania danych. Aby uzyskać szczegółowe informacje, zobacz [Zbieranie danych w usłudze Azure Security Center.](security-center-enable-data-collection.md)


## <a name="how-do-i-get-azure-security-center"></a>Jak uzyskać usługę Azure Security Center?
Usługa Azure Security Center jest włączona wraz z subskrypcją platformy Microsoft Azure i dostępna z [witryny Azure.](https://azure.microsoft.com/features/azure-portal/) Aby uzyskać do niego dostęp, [zaloguj się do portalu](https://portal.azure.com), wybierz pozycję **Przeglądaj**i przewiń do **Centrum zabezpieczeń**.


## <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Które zasoby platformy Azure są monitorowane przez usługę Azure Security Center?
Usługa Azure Security Center monitoruje następujące zasoby platformy Azure:

* Maszyny wirtualne (w tym usługi w [chmurze)](../cloud-services/cloud-services-choose-me.md)
* Zestawy skalowania maszyn wirtualnych
* Sieci wirtualne platformy Azure
* Kontenery
* Usługa SQL platformy Azure
* Konto usługi Azure Storage
* Usługi Azure Web Apps (w [środowisku usługi aplikacji)](../app-service/environment/intro.md)
* Rozwiązania partnerskie zintegrowane z subskrypcją platformy Azure, takie jak zapora aplikacji sieci web na maszynach wirtualnych i w środowisku usługi aplikacji

Ponadto maszyny spoza platformy Azure (w tym lokalne) mogą być również monitorowane przez usługę Azure Security Center. Obsługiwane są zarówno [maszyny z systemem Windows,](./quick-onboard-windows-computer.md) jak i [linux.](./quick-onboard-linux-computer.md)


## <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Jak wyświetlić bieżący stan zabezpieczeń moich zasobów platformy Azure?
Strona **Przegląd usługi Security Center** przedstawia ogólną postawę zabezpieczeń środowiska w podziale według danych obliczeniowych, sieci, magazynu & i aplikacji. Każdy typ zasobu ma wskaźnik pokazujący zidentyfikowane luki w zabezpieczeniach. Kliknięcie każdego kafelka powoduje wyświetlenie listy problemów z zabezpieczeniami zidentyfikowanych przez program Security Center wraz z spisem zasobów w ramach subskrypcji.



## <a name="what-is-a-security-policy"></a>Co to jest polityka bezpieczeństwa?
Zasady zabezpieczeń definiuje zestaw formantów, które są zalecane dla zasobów w ramach określonej subskrypcji. W usłudze Azure Security Center definiujesz zasady dotyczące subskrypcji platformy Azure zgodnie z wymaganiami dotyczącymi zabezpieczeń firmy oraz typem aplikacji lub czułością danych w każdej subskrypcji.

Zasady zabezpieczeń włączone w usłudze Azure Security Center napędzają zalecenia dotyczące zabezpieczeń i monitorowanie. Aby dowiedzieć się więcej o zasadach zabezpieczeń, zobacz [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md).


## <a name="who-can-modify-a-security-policy"></a>Kto może modyfikować zasady zabezpieczeń?
Aby zmodyfikować zasady zabezpieczeń, użytkownik musi być administratorem zabezpieczeń lub właścicielem lub współautorem tej subskrypcji.

Aby dowiedzieć się, jak skonfigurować zasady zabezpieczeń, zobacz [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md).


## <a name="what-is-a-security-recommendation"></a>Co to jest zalecenie dotyczące zabezpieczeń?
Usługa Azure Security Center analizuje stan zabezpieczeń zasobów platformy Azure. Po zidentyfikowaniu potencjalnych luk w zabezpieczeniach tworzone są zalecenia. Zalecenia prowadzą użytkownika przez proces konfigurowania potrzebnej kontroli. Oto przykłady:

* Inicjowanie obsługi administracyjnej ochrony przed złośliwym oprogramowaniem w celu identyfikacji i usuwania złośliwego oprogramowania
* [Sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md) i reguły do kontrolowania ruchu na maszynach wirtualnych
* Aprowizacji zapory aplikacji sieci web, aby pomóc w obronie przed atakami kierowania aplikacji internetowych
* Wdrażanie brakujących aktualizacji systemu
* Modyfikowanie konfiguracji systemu operacyjnego, które odbiegają od zalecanych standardów

W tym miejscu wyświetlane są tylko zalecenia, które są włączone w zasadach zabezpieczeń.



## <a name="what-triggers-a-security-alert"></a>Co wyzwala alert zabezpieczeń?
Usługa Azure Security Center automatycznie zbiera, analizuje i łączy dane dziennika z zasobów platformy Azure, sieci i rozwiązań partnerskich, takich jak ochrona przed złośliwym oprogramowaniem i zapory. Po wykryciu zagrożenia tworzony jest alert zabezpieczeń. Przykłady obejmują wykrywanie:

* Zagrożonych maszyn wirtualnych komunikujących się ze znanymi złośliwymi adresami IP
* Zaawansowane złośliwe oprogramowanie wykryte przy użyciu raportowania błędów systemu Windows
* Ataków siłowych wobec maszyn wirtualnych
* Alerty zabezpieczeń pochodzące ze zintegrowanych rozwiązań zabezpieczeń partnerów, takich jak ochrona przed złośliwym oprogramowaniem lub zapory aplikacji sieci Web


## <a name="why-did-secure-score-values-change"></a>Dlaczego wartości Secure Score zmieniły się? <a name="secure-score-faq"></a>
Od lutego 2019 r. Centrum Zabezpieczeń skorygowało ocenę kilku zaleceń, aby lepiej dopasować je do ich ważności. W wyniku tego dostosowania mogą wystąpić zmiany w ogólnych wartościach bezpiecznego wyniku.  Aby uzyskać więcej informacji na temat bezpiecznego wyniku, zobacz [Obliczanie bezpiecznego wyniku](security-center-secure-score.md).


## <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Jaka jest różnica między zagrożeniami wykrytymi i alertami przez Centrum odpowiedzi zabezpieczeń firmy Microsoft a usługą Azure Security Center?
Centrum microsoft security response center (MSRC) wykonuje wybrane monitorowanie zabezpieczeń sieci i infrastruktury platformy Azure oraz otrzymuje analizy zagrożeń i skargi dotyczące nadużyć od stron trzecich. Gdy msrc dowie się, że dane klienta zostały dostępne przez stronę niezgodną z prawem lub nieautoryzowane lub że korzystanie przez klienta z platformy Azure nie jest zgodne z warunkami dopuszczalnego użytkowania, menedżer incydentów zabezpieczeń powiadamia klienta. Powiadomienie zazwyczaj występuje, wysyłając wiadomość e-mail do kontaktów zabezpieczeń określonych w usłudze Azure Security Center lub właściciela subskrypcji platformy Azure, jeśli kontakt zabezpieczeń nie jest określony.

Usługa Security Center to usługa platformy Azure, która stale monitoruje środowisko platformy Azure klienta i stosuje analizy do automatycznego wykrywania szerokiego zakresu potencjalnie złośliwych działań. Te wykrycia są wykrywane jako alerty zabezpieczeń na pulpicie nawigacyjnym centrum zabezpieczeń.