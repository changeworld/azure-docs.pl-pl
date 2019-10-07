---
title: Zabezpieczenia danych Azure Security Center | Microsoft Docs
description: W tym dokumencie opisano sposób zarządzania danymi i ich ochrony w Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: memildin
ms.openlocfilehash: 3c63283a1a13e2deb178a53dfc5045d371d6a28a
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996923"
---
# <a name="azure-security-center-data-security"></a>Zabezpieczenia danych Azure Security Center
Aby ułatwić klientom zapobieganie zagrożeniom, wykrywanie ich i reagowanie na nie, Azure Security Center zbiera i przetwarza dane związane z zabezpieczeniami, w tym informacje o konfiguracji, metadane, dzienniki zdarzeń, pliki zrzutu awaryjnego i nie tylko. Firma Microsoft stosuje się do rygorystycznych wskazówek dotyczących zgodności i zabezpieczeń — od kodowania do obsługi usługi.

W tym artykule opisano sposób zarządzania danymi i ich ochrony w Azure Security Center.

## <a name="data-sources"></a>Źródła danych
Azure Security Center analizuje dane z następujących źródeł, aby zapewnić widoczność stanu zabezpieczeń, identyfikować luki w zabezpieczeniach i zalecać środki zaradcze oraz wykrywać aktywne zagrożenia:

- Usługi platformy Azure: program korzysta z informacji o konfiguracji wdrożonych usług platformy Azure, komunikując się z dostawcą zasobów tej usługi.
- Ruch sieciowy: używa próbkowanych metadanych ruchu sieciowego z infrastruktury firmy Microsoft, takich jak źródłowy/docelowy adres IP/port, rozmiar pakietu i protokół sieciowy.
- Rozwiązania partnerskie: program używa alertów zabezpieczeń zintegrowanych rozwiązań partnerskich, takich jak zapory i rozwiązania chroniące przed złośliwym kodem.
- Twoje Virtual Machines i serwery: używa informacji o konfiguracji i informacji o zdarzeniach zabezpieczeń, takich jak dzienniki zdarzeń systemu Windows i inspekcji, dzienniki programu IIS, komunikaty dziennika systemowego i pliki zrzutu awaryjnego z maszyn wirtualnych. Ponadto podczas tworzenia alertu Azure Security Center może wygenerować migawkę danego dysku maszyny wirtualnej i wyodrębnić artefakty maszynowe związane z alertem z dysku maszyny wirtualnej, na przykład plik rejestru, do celów dowodowych.


## <a name="data-protection"></a>Ochrona danych
Podział **danych**: dane są logicznie oddzielone dla każdego składnika w ramach usługi. Wszystkie dane są znakowane na organizację. Ten znakowanie zachowuje się w całym cyklu życia danych i jest wymuszany dla każdej warstwy usługi.

**Dostęp do danych**: aby zapewnić zalecenia dotyczące zabezpieczeń i zbadać potencjalne zagrożenia bezpieczeństwa, personel firmy Microsoft może uzyskać dostęp do informacji zbieranych lub analizowanych przez usługi platformy Azure, w tym plików zrzutu awaryjnego, zdarzeń tworzenia procesów, dysku maszyny wirtualnej migawki i artefakty, które mogą przypadkowo obejmować dane klienta lub dane osobowe z maszyn wirtualnych. Przestrzegamy [warunków i zasad zachowania poufności informacji w usługach online firmy Microsoft](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), co oznacza, że firma Microsoft nie będzie używać danych klienta ani informacji pochodnych dla jakichkolwiek reklam lub podobnych celów komercyjnych. Dane klienta są używane tylko w razie potrzeby w celu udostępnienia usług platformy Azure, w tym do celów związanych z udostępnianiem tych usług. Zachowujesz wszystkie prawa do danych klienta.

**Użycie danych**: Firma Microsoft używa wzorców i analizy zagrożeń, które są widoczne dla wielu dzierżawców w celu zwiększenia możliwości zapobiegania i wykrywania. jest to zgodne z zobowiązaniami dotyczącymi ochrony prywatności opisanymi w zasadach [zachowania poufności informacji](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx).

## <a name="data-location"></a>Lokalizacja danych

**Obszary robocze**: obszar roboczy jest określony dla następujących Georegiony, a dane zbierane z maszyn wirtualnych platformy Azure, w tym Zrzuty awaryjne i niektóre typy danych alertów, są przechowywane w najbliższym obszarze roboczym.

| Geograficzna maszyny wirtualnej                              | Lokalizacja geograficzna obszaru roboczego |
|-------------------------------------|---------------|
| Stany Zjednoczone, Brazylia, Afryka Południowa | Stany Zjednoczone |
| Kanada                              | Kanada        |
| Europa (z wyjątkiem Zjednoczonego Królestwa)   | Terenie        |
| Wielka Brytania                      | Wielka Brytania |
| Azja (z wyjątkiem Indii, Japonia, Korea, Chiny)   | Azja i Pacyfik  |
| Korea                              | Azja i Pacyfik  |
| Indie                               | Indie         |
| Japonia                               | Japonia         |
| Chinach                               | Chinach         |
| Australia                           | Australia     |


Migawki dysków maszyny wirtualnej są przechowywane na tym samym koncie magazynu co dysk maszyny wirtualnej.

W przypadku maszyn wirtualnych i serwerów z innymi środowiskami, np. lokalnie, można określić obszar roboczy i region, w którym są przechowywane zebrane dane.

**Azure Security Center Storage**: informacje dotyczące alertów zabezpieczeń, w tym alerty partnerów, są przechowywane regionalnie zgodnie z lokalizacją powiązanego zasobu platformy Azure, a informacje o stanie kondycji zabezpieczeń i rekomendacji są przechowywane centralnie w Stany Zjednoczone lub Europie zgodnie z lokalizacją klienta.
Azure Security Center zbiera tymczasowe kopie plików zrzutu awaryjnego i analizuje je pod kątem dowodu prób wykorzystania luk w zabezpieczeniach i udanych naruszeń. Azure Security Center wykonuje tę analizę w tym samym obszarze geograficznym, co obszar roboczy, i usuwa tymczasowe kopie po zakończeniu analizy.

Artefakty maszyn są przechowywane centralnie w tym samym regionie co maszyna wirtualna.


## <a name="managing-data-collection-from-virtual-machines"></a>Zarządzanie zbieraniem danych z maszyn wirtualnych

Po włączeniu Security Center na platformie Azure zbieranie danych jest włączone dla każdej subskrypcji platformy Azure. Możesz również włączyć zbieranie danych dla subskrypcji w sekcji zasady zabezpieczeń w Azure Security Center. Gdy zbieranie danych jest włączone, program Azure Security Center udostępnia Microsoft Monitoring Agent we wszystkich istniejących obsługiwanych maszynach wirtualnych platformy Azure oraz utworzonych nowych.
Program Microsoft Monitoring Agent skanuje w poszukiwaniu różnych konfiguracji i zdarzeń związanych z zabezpieczeniami, aby uzyskać ślady [śledzenia zdarzeń systemu Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW). Ponadto system operacyjny będzie zgłaszać zdarzenia dziennika zdarzeń w trakcie działania maszyny. Przykładami takich danych są: typ i wersja systemu operacyjnego, Dzienniki systemu operacyjnego (dzienniki zdarzeń systemu Windows), uruchomione procesy, Nazwa maszyny, adresy IP, zalogowany użytkownik i identyfikator dzierżawy. Microsoft Monitoring Agent odczytuje wpisy dziennika zdarzeń i śledzenia ETW i kopiuje je do obszarów roboczych na potrzeby analizy. Microsoft Monitoring Agent kopiuje także pliki zrzutu awaryjnego do obszarów roboczych, włącza zdarzenia tworzenia procesów i włącza inspekcję wiersza polecenia.

Jeśli używasz Azure Security Center Free, możesz również wyłączyć zbieranie danych z maszyn wirtualnych w ramach zasad zabezpieczeń. Zbieranie danych jest wymagane w przypadku subskrypcji w warstwie Standardowa. Migawki dysków maszyny wirtualnej i zbieranie artefaktów nadal będą włączone nawet wtedy, gdy zbieranie danych zostało wyłączone.

## <a name="data-consumption"></a>Użycie danych

Klienci mogą korzystać z Security Center powiązanych danych z różnych strumieni danych, jak pokazano poniżej:

* **Aktywność platformy Azure**: wszystkie alerty zabezpieczeń, zatwierdzone Security Center żądań [just-in-Time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) oraz wszystkie alerty wygenerowane przez [adaptacyjne kontrolki aplikacji](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application).
* **Dzienniki Azure monitor**: wszystkie alerty zabezpieczeń.


> [!NOTE]
> Zalecenia dotyczące zabezpieczeń mogą być również używane za pośrednictwem interfejsu API REST. Aby uzyskać więcej informacji, przeczytaj informacje o [interfejsie API REST dostawcy zasobów zabezpieczeń](https://msdn.microsoft.com/library/mt704034(Azure.100).aspx) .

## <a name="see-also"></a>Zobacz także
W tym dokumencie przedstawiono sposób zarządzania danymi i ich ochrony w Azure Security Center. Aby dowiedzieć się więcej na temat Azure Security Center, zobacz:

* [Azure Security Center Przewodnik planowania i](security-center-planning-and-operations-guide.md) obsługi — Dowiedz się, jak planować i zrozumieć zagadnienia dotyczące projektowania, aby przyjąć Azure Security Center.
* [Monitorowanie kondycji zabezpieczeń w Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure
* [Zarządzanie alertami zabezpieczeń i reagowanie na nie w Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat zarządzania alertami zabezpieczeń i reagowania na nie
* [Monitorowanie rozwiązań partnerskich za pomocą Azure Security Center](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center często zadawane](security-center-faq.md) pytania — Znajdź często zadawane pytania dotyczące korzystania z usługi
* [Blog dotyczący zabezpieczeń platformy Azure](https://blogs.msdn.com/b/azuresecurity/) — wpisy w blogu dotyczące zabezpieczeń i zgodności platformy Azure
