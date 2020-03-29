---
title: Opis bezpiecznych stacji roboczych zarządzanych przez platformę Azure — usługa Azure Active Directory
description: Dowiedz się więcej o bezpiecznych stacjach roboczych zarządzanych przez platformę Azure i dowiedz się, dlaczego są one ważne.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05a3a8cf14a591dd3037175e4eed5b5bd8d3096c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672657"
---
# <a name="understand-secure-azure-managed-workstations"></a>Opis bezpiecznych stacji roboczych zarządzanych przez platformę Azure

Zabezpieczone, odizolowane stacje robocze mają kluczowe znaczenie dla bezpieczeństwa poufnych ról, takich jak administratorzy, deweloperzy i operatorzy usług krytycznych. Jeśli bezpieczeństwo stacji roboczej klienta zostanie naruszone, wiele zabezpieczeń i gwarancji zabezpieczeń może zakończyć się niepowodzeniem lub być nieskuteczne.

W tym dokumencie wyjaśniono, czego potrzebujesz do tworzenia bezpiecznej stacji roboczej, często nazywane uprzywilejowaną stacją roboczą (PAW). Artykuł zawiera również szczegółowe instrukcje konfigurowania początkowych zabezpieczeń. W tych wskazówkach opisano, jak technologia oparta na chmurze może zarządzać usługą. Opiera się na możliwościach zabezpieczeń wprowadzonych w systemie Windows 10RS5, Microsoft Defender Advanced Threat Protection (ATP), usłudze Azure Active Directory i usłudze Microsoft Intune.

> [!NOTE]
> W tym artykule wyjaśniono pojęcie bezpiecznej stacji roboczej i jej znaczenie. Jeśli znasz już tę koncepcję i chcesz przejść do wdrożenia, odwiedź stronę [Wdrażanie bezpiecznej stacji roboczej](howto-azure-managed-workstation.md).

## <a name="why-secure-workstation-access-is-important"></a>Dlaczego bezpieczny dostęp do stacji roboczej jest ważny

Szybkie wdrożenie usług w chmurze i możliwość pracy z dowolnego miejsca stworzyły nową metodę wykorzystania. Wykorzystując słabe mechanizmy kontroli zabezpieczeń na urządzeniach, na których pracują administratorzy, osoby atakujące mogą uzyskać dostęp do uprzywilejowanych zasobów.

Uprzywilejowane nadużycia i ataki w łańcuchu dostaw należą do pięciu najlepszych metod używanych przez osoby atakujące do naruszania organizacji. Są one również drugą najczęściej wykrywatą taktyką w incydentach zgłoszonych w 2018 roku według [raportu Verizon Threat](https://enterprise.verizon.com/resources/reports/dbir/)i Raportu Wywiadu [Bezpieczeństwa.](https://aka.ms/sir)

Większość atakujących wykonać następujące kroki:

1. Rekonesans, aby znaleźć sposób, często specyficzne dla branży.
1. Analiza w celu zebrania informacji i zidentyfikowania najlepszego sposobu infiltracji stacji roboczej, która jest postrzegana jako niska wartość.
1. Wytrwałość szukać środków, aby przejść [w stan spozy.](https://en.wikipedia.org/wiki/Network_Lateral_Movement)
1. Eksfiltracja poufnych i poufnych danych.

Podczas rekonesansu atakujący często infiltrują urządzenia, które wydają się niskie ryzyko lub niedoceniane. Używają tych podatnych urządzeń, aby znaleźć możliwość ruchu bocznego i znaleźć użytkowników administracyjnych i urządzenia. Po uzyskaniu dostępu do ról uprzywilejowanych użytkowników osoby atakujące identyfikują dane o wysokiej wartości i pomyślnie eksfiltrują te dane.

![Typowy wzór kompromisowy](./media/concept-azure-managed-workstation/typical-timeline.png)

W tym dokumencie opisano rozwiązanie, które może pomóc chronić urządzenia komputerowe przed takimi atakami bocznymi. Rozwiązanie izoluje zarządzanie i usługi od mniej wartościowych urządzeń zwiększających produktywność, przełamując łańcuch, zanim urządzenie, które ma dostęp do poufnych zasobów chmury, może zostać przeniknięte. Rozwiązanie korzysta z natywnych usług platformy Azure, które są częścią stosu Microsoft 365 Enterprise:

* Usługa Intune do zarządzania urządzeniami oraz bezpieczna lista aplikacji i adresów URL
* Autopilot do konfigurowania, wdrażania i odświeżania urządzeń
* Usługa Azure AD do zarządzania użytkownikami, dostępu warunkowego i uwierzytelniania wieloskładnikowego
* Windows 10 (aktualna wersja) dla zaświadczania o kondycji urządzenia i doświadczenia użytkownika
* Usługa Defender ATP do ochrony, wykrywania i reagowania na punkty końcowe zarządzane przez chmurę
* Usługa Azure AD PIM do zarządzania autoryzacją i uprzywilejowanym dostępem do zasobów (Just-in-Time)
* Usługa Log Analytics i Sentinel do monitorowania i ostrzegania

## <a name="who-benefits-from-a-secure-workstation"></a>Kto korzysta z bezpiecznej stacji roboczej?

Wszyscy użytkownicy i operatorzy korzystają podczas korzystania z bezpiecznej stacji roboczej. Osoba atakująca, która naruszy problem komputera lub urządzenia, może podszyć się pod wszystkie konta w pamięci podręcznej. Po zalogowaniu się do urządzenia mogą również używać poświadczeń i tokenów. To ryzyko sprawia, że ważne jest, aby zabezpieczyć urządzenia, które są używane do ról uprzywilejowanych, w tym praw administracyjnych. Urządzenia z kontami uprzywilejowanymi są celami dla ataków eskalacji ruchu poprzecznego i uprawnień. Konta te mogą być używane dla różnych aktywów, takich jak:

* Administrator systemów lokalnych lub chmurowych
* Stacja robocza dla deweloperów dla systemów krytycznych
* Administrator konta w mediach społecznościowych z wysoką ekspozycją
* Bardzo wrażliwa stacja robocza, taka jak terminal płatniczy SWIFT
* Stacja robocza obsługująca tajemnice handlowe

Aby zmniejszyć ryzyko, należy zaimplementować podwyższone środki kontroli zabezpieczeń dla uprzywilejowanych stacji roboczych korzystających z tych kont. Aby uzyskać więcej informacji, zobacz [przewodnik po wdrażaniu funkcji usługi Azure Active Directory](../fundamentals/active-directory-deployment-checklist-p2.md), [plan działania usługi Office 365](https://aka.ms/o365secroadmap)i [plan zabezpieczania dostępu uprzywilejowanego](https://aka.ms/sparoadmap)).

## <a name="why-use-dedicated-workstations"></a>Dlaczego warto korzystać z dedykowanych stacji roboczych?

Chociaż można dodać zabezpieczenia do istniejącego urządzenia, lepiej zacząć od bezpiecznego fundamentu. Aby umieścić organizację w najlepszej pozycji, aby utrzymać wysoki poziom zabezpieczeń, zacznij od urządzenia, o które wiesz, że jest bezpieczne i zaimplementuj zestaw znanych zabezpieczeń.

Rosnąca liczba wektorów ataków za pośrednictwem poczty e-mail i przeglądania stron internetowych sprawia, że coraz trudniej jest mieć pewność, że urządzeniu można zaufać. W tym przewodniku przyjęto założenie, że dedykowana stacja robocza jest odizolowana od standardowej produktywności, przeglądania i poczty e-mail. Usuwanie produktywności, przeglądania stron internetowych i poczty e-mail z urządzenia może mieć negatywny wpływ na produktywność. Jednak to zabezpieczenie jest zazwyczaj dopuszczalne w scenariuszach, w których zadania zadania nie wymagają wyraźnie, a ryzyko zdarzenia zabezpieczeń jest wysokie.

> [!NOTE]
> Przeglądanie stron internetowych w tym miejscu odnosi się do ogólnego dostępu do dowolnych stron internetowych, które mogą być działalnością wysokiego ryzyka. Takie przeglądanie różni się znacznie od korzystania z przeglądarki sieci Web w celu uzyskania dostępu do niewielkiej liczby znanych witryn administracyjnych dla usług, takich jak Azure, Office 365, innych dostawców chmury i aplikacji SaaS.

Strategie powstrzymywania zwiększają bezpieczeństwo, zwiększając liczbę i typ formantów, które zniechęcają osobę atakującą do uzyskania dostępu do poufnych zasobów. Model opisany w tym artykule używa projektu uprawnień warstwowych i ogranicza uprawnienia administracyjne do określonych urządzeń.

## <a name="supply-chain-management"></a>Zarządzanie łańcuchem dostaw

Niezbędne dla zabezpieczonej stacji roboczej jest rozwiązanie łańcucha dostaw, w którym używasz zaufanej stacji roboczej zwanej "korzeniem zaufania". Technologia, która musi być brana pod uwagę przy wyborze sprzętu głównego zaufania, powinna obejmować następujące technologie zawarte w nowoczesnych laptopach: 

* [Moduł zaufanych platform (TPM) 2.0](/windows-hardware/design/device-experiences/oem-tpm)
* [Szyfrowanie dysków funkcją BitLocker](/windows-hardware/design/device-experiences/oem-bitlocker)
* [Bezpieczny rozruch UEFI](/windows-hardware/design/device-experiences/oem-secure-boot)
* [Sterowniki i oprogramowanie układowe dystrybuowane za pośrednictwem witryny Windows Update](/windows-hardware/drivers/dashboard/understanding-windows-update-automatic-and-optional-rules-for-driver-distribution)
* [Wirtualizacja i funkcja HVCI](/windows-hardware/design/device-experiences/oem-vbs)
* [Sterowniki i aplikacje HVCI-Ready](/windows-hardware/test/hlk/testref/driver-compatibility-with-device-guard)
* [Windows Hello](/windows-hardware/design/device-experiences/windows-hello-biometric-requirements)
* [Ochrona we/wy DMA](/windows/security/information-protection/kernel-dma-protection-for-thunderbolt)
* [Osłona systemu](/windows/security/threat-protection/windows-defender-system-guard/system-guard-how-hardware-based-root-of-trust-helps-protect-windows)
* [Nowoczesny tryb gotowości](/windows-hardware/design/device-experiences/modern-standby)

W przypadku tego rozwiązania root of trust zostanie wdrożony przy użyciu technologii [Microsoft Autopilot](/windows/deployment/windows-autopilot/windows-autopilot) ze sprzętem spełniaym nowoczesne wymagania techniczne. Aby zabezpieczyć stację roboczą, autopilot umożliwia korzystanie z urządzeń z systemem Windows 10 zoptymalizowanych pod kątem systemu Microsoft OEM. Urządzenia te są w znanym dobrym stanie od producenta. Zamiast ponownie zagosić potencjalnie niezabezpieczone urządzenie, autopilot może przekształcić urządzenie z systemem Windows w stan "gotowy do pracy" w trybie biznesowym. Stosuje ustawienia i zasady, instaluje aplikacje, a nawet zmienia wersję systemu Windows 10. Na przykład autopilot może zmienić instalację systemu Windows urządzenia z Systemu Windows 10 Pro na Windows 10 Enterprise, aby mógł korzystać z zaawansowanych funkcji.

![Bezpieczne poziomy stacji roboczej](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>Role i profile urządzeń

Niniejsze wskazówki odnoszą się do kilku profili zabezpieczeń i ról, które mogą pomóc w tworzeniu bezpieczniejszych rozwiązań dla użytkowników, deweloperów i personelu IT. Profile te równoważą użyteczność i ryzyko dla zwykłych użytkowników, którzy mogą korzystać z ulepszonej lub bezpiecznej stacji roboczej. Konfiguracje ustawień podane w tym miejscu są oparte na standardach przyjętych przez branżę. W tych wskazówkach pokazano, jak wzmocnić system Windows 10 i zmniejszyć ryzyko związane z naruszeniem zabezpieczeń urządzenia lub użytkownika. Aby skorzystać z nowoczesnej technologii sprzętowej i urządzenia zaufania, użyjemy [zaświadczania o kondycji urządzenia](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Support-Tip-Using-Device-Health-Attestation-Settings-as-Part-of/ba-p/282643), które jest włączone począwszy od profilu **Wysokiego Bezpieczeństwa.** Ta funkcja jest obecna, aby upewnić się, że osoby atakujące nie mogą być trwałe podczas wczesnego rozruchu urządzenia. Czyni to za pomocą zasad i technologii, aby pomóc w zarządzaniu funkcjami zabezpieczeń i zagrożeniami.
![Bezpieczne poziomy stacji roboczej](./media/concept-azure-managed-workstation/seccon-levels.png)

* **Podstawowe zabezpieczenia** — zarządzana, standardowa stacja robocza stanowi dobry punkt wyjścia dla większości domów i małych firm. Te urządzenia są zarejestrowane w usłudze Azure AD i zarządzane za pomocą usługi Intune. Ten profil umożliwia użytkownikom uruchamianie dowolnych aplikacji i przeglądanie dowolnej witryny sieci Web. Rozwiązanie chroniące przed złośliwym oprogramowaniem, takie jak [Microsoft Defender,](https://www.microsoft.com/windows/comprehensive-security) powinno być włączone.

* **Zwiększone bezpieczeństwo** — to podstawowe, chronione rozwiązanie jest dobre dla użytkowników domowych, użytkowników małych firm i ogólnych deweloperów.

   Ulepszona stacja robocza jest opartym na zasadach sposobem zwiększenia bezpieczeństwa niskiego profilu zabezpieczeń. Zapewnia bezpieczny sposób pracy z danymi klientów, a jednocześnie korzysta z narzędzi zwiększających produktywność, takich jak poczta e-mail i przeglądanie stron internetowych. Za pomocą zasad inspekcji i usługi Intune można monitorować ulepszoną stację roboczą pod kątem zachowania użytkowników i użycia profilu. Ulepszony profil stacji roboczej można wdrożyć za pomocą skryptu systemu Windows10 (1809) i korzysta z zaawansowanej ochrony przed złośliwym oprogramowaniem przy użyciu [zaawansowanej ochrony przed zagrożeniami (ATP).](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

* **Wysokie bezpieczeństwo** — najskuteczniejszym sposobem zmniejszenia powierzchni ataku stacji roboczej jest usunięcie możliwości samodzielnego administrowania stacją roboczą. Usunięcie lokalnych praw administracyjnych jest krokiem, który zwiększa bezpieczeństwo, ale może mieć wpływ na produktywność, jeśli zostanie wdrożony nieprawidłowo. Wysoki profil zabezpieczeń opiera się na rozszerzonym profilu zabezpieczeń z jedną istotną zmianą: usunięcie administratora lokalnego. Ten profil jest przeznaczony dla użytkowników o wysokim profilu: kadry kierowniczej, listy płac i poufnych danych użytkowników, osoby zatwierdzające dla usług i procesów.

   Użytkownik o wysokim poziomie bezpieczeństwa wymaga bardziej kontrolowanego środowiska, a jednocześnie jest w stanie wykonywać takie czynności, jak poczta e-mail i przeglądanie stron internetowych w prostym w użyciu środowisku. Użytkownicy oczekują, że funkcje, takie jak pliki cookie, ulubione i inne skróty do pracy. Jednak ci użytkownicy nie mogą wymagać możliwości modyfikowania lub debugowania swojego urządzenia. Nie muszą też instalować sterowników. Wysoki profil zabezpieczeń jest wdrażany przy użyciu skryptu Wysokiego poziomu zabezpieczeń — Windows10 (1809).

* **Wyspecjalizowane** — atakujący kierują reklamy do deweloperów i administratorów IT, ponieważ mogą zmieniać interesujące systemy dla atakujących. Wyspecjalizowana stacja robocza rozszerza zasady stacji roboczej o wysokim poziomie bezpieczeństwa, zarządzając lokalnymi aplikacjami i ograniczając witryny sieci Web. Ogranicza również możliwości zwiększające produktywność wysokiego ryzyka, takie jak ActiveX, Java, wtyczki przeglądarki i inne kontrolki systemu Windows. Ten profil jest wdrażany za pomocą skryptu securitybaseline DeviceConfiguration_NCSC — Windows10 (1803).

* **Zabezpieczony** — osoba atakująca, która naruszy konto administracyjne, może spowodować znaczne szkody biznesowe spowodowane kradzieżą danych, zmianą danych lub zakłóceniem usługi. W tym stanie wzmocnionej stacji roboczej włącza wszystkie mechanizmy kontroli zabezpieczeń i zasady, które ograniczają bezpośrednią kontrolę zarządzania aplikacjami lokalnymi. Zabezpieczona stacja robocza nie ma narzędzi zwiększających produktywność, więc urządzenie jest trudniejsze do złamania. Blokuje najczęstszy wektor ataków phishingowych: e-mail i media społecznościowe. Zabezpieczoną stację roboczą można wdrożyć za pomocą skryptu Secure Workstation - Windows10 (1809) SecurityBaseline.

   ![Zabezpieczona stacja robocza](./media/concept-azure-managed-workstation/secure-workstation.png)

   Bezpieczna stacja robocza zapewnia administratorowi wzmocnioną stację roboczą, która ma przejrzystą kontrolę aplikacji i ochronę aplikacji. Stacja robocza używa osłony poświadczeń, osłony urządzeń i ochrony przed exploitami, aby chronić hosta przed złośliwym zachowaniem. Wszystkie dyski lokalne są również szyfrowane za pomocą funkcji BitLocker.

* **Izolowane** — ten niestandardowy scenariusz w trybie offline reprezentuje skrajny koniec spektrum. W tym przypadku nie są udostępniane żadne skrypty instalacyjne. Może być konieczne zarządzanie funkcją o znaczeniu krytycznym dla firmy, która wymaga nieobsługiwał lub nieobsługiwał starszego systemu operacyjnego. Na przykład linia produkcyjna o wysokiej wartości lub system podtrzymuje życie. Ponieważ zabezpieczenia są krytyczne, a usługi w chmurze są niedostępne, można zarządzać tymi komputerami i aktualizować je ręcznie lub za pomocą izolowanej architektury lasu usługi Active Directory, takiej jak rozszerzone środowisko administracyjne zabezpieczeń (ESAE). W takich okolicznościach należy rozważyć usunięcie wszystkich dostępu z wyjątkiem podstawowych kontroli kondycji usługi Intune i ATP.

   * [Wymagania dotyczące komunikacji sieciowej usługi Intune](/intune/network-bandwidth-use)
   * [Wymagania dotyczące komunikacji sieciowej ATP](/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>Następne kroki

[Wdrażanie bezpiecznej stacji roboczej zarządzanej przez platformę Azure](howto-azure-managed-workstation.md).
