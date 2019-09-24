---
title: Azure Security Center — bezpieczeństwo danych | Microsoft Docs
description: W tym dokumencie wyjaśniono, jak zarządzane i chronione są dane w usłudze Azure Security Center.
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
ms.openlocfilehash: 30f4358bea2670cfa8d825bf1ef75feeba7ea851
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202346"
---
# <a name="azure-security-center-data-security"></a>Azure Security Center — bezpieczeństwo danych
Aby ułatwić klientom zapobieganie zagrożeniom, wykrywanie ich i reagowanie na nie, usługa Azure Security Center zbiera i przetwarza dane dotyczące zabezpieczeń, w tym informacje o konfiguracji, metadane, dzienniki zdarzeń, pliki zrzutu awaryjnego i inne. Firma Microsoft przestrzega surowych wymogów z zakresu zabezpieczeń i zgodności — od kodu po działanie usługi.

W tym artykule wyjaśniono, jak zarządzane i chronione są dane w usłudze Azure Security Center.

## <a name="data-sources"></a>Źródła danych
Usługa Azure Security Center analizuje dane z następujących źródeł, aby zapewnić wgląd w stan zabezpieczeń, zidentyfikować luki w zabezpieczeniach i polecić rozwiązania oraz wykryć aktywne zagrożenia:

- Usługi platformy Azure: Używa informacji o konfiguracji wdrożonych usług platformy Azure, komunikując się z dostawcą zasobów tej usługi.
- Ruch sieciowy: Używa przykładowych metadanych ruchu sieciowego z infrastruktury firmy Microsoft, takich jak źródłowy/docelowy adres IP/port, rozmiar pakietu i protokół sieciowy.
- Rozwiązania partnerskie: Program używa alertów zabezpieczeń z zintegrowanych rozwiązań partnerskich, takich jak zapory i rozwiązania chroniące przed złośliwym kodem.
- Twoje Virtual Machines i serwery: Program używa informacji o konfiguracji i informacji dotyczących zdarzeń zabezpieczeń, takich jak dzienniki zdarzeń systemu Windows i inspekcje, dzienniki programu IIS, komunikaty dziennika systemowego i pliki zrzutu awaryjnego z maszyn wirtualnych. Ponadto w przypadku tworzenia alertu usługa Azure Security Center może wygenerować migawkę dysku uwzględnionej maszyny wirtualnej i wyodrębnić artefakty maszyny powiązane z alertem z dysku maszyny wirtualnej, takie jak plik rejestru, dla celów przeprowadzania ekspertyz.


## <a name="data-protection"></a>Ochrona danych
Podział **danych**: Dane są logicznie oddzielone dla każdego składnika w ramach usługi. Wszystkie dane są otagowane informacjami o organizacji. To tagowanie jest obecne przez cały cykl życia danych i jest wymuszane w każdej warstwie usługi.

**Dostęp do danych**: Aby zapewnić zalecenia dotyczące zabezpieczeń i zbadać potencjalne zagrożenia bezpieczeństwa, personel firmy Microsoft może uzyskać dostęp do informacji zbieranych lub analizowanych przez usługi platformy Azure, w tym plików zrzutu awaryjnego, zdarzeń tworzenia procesów, migawek dysków maszyny wirtualnej i artefaktów, co może przypadkowo obejmować dane klienta lub dane osobowe z maszyn wirtualnych. Stosujemy się do [warunków korzystania z usług Microsoft Online Services i zasad zachowania poufności informacji](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), które stanowią, że firma Microsoft nie będzie używać danych klienta ani informacji pochodnych do żadnych celów reklamowych ani zbliżonych celów komercyjnych. Danych klienta używamy tylko w razie potrzeby w celu świadczenia usług platformy Azure, włączając w to cele zgodne ze świadczeniem tych usług. Użytkownik zachowuje wszystkie uprawnienia do danych klienta.

**Użycie danych**: Firma Microsoft używa wzorców i analizy zagrożeń widocznych dla wielu dzierżawców w celu zwiększenia możliwości zapobiegania i wykrywania. jest to zgodne z zobowiązaniami dotyczącymi ochrony prywatności opisanymi w zasadach [zachowania poufności informacji](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx).

## <a name="data-location"></a>Lokalizacja danych

**Twoje obszary robocze**: Obszar roboczy jest określony dla następujących Georegiony, a dane zbierane z maszyn wirtualnych platformy Azure, w tym Zrzuty awaryjne i niektóre typy danych alertów, są przechowywane w najbliższym obszarze roboczym.

| Lokalizacja geograficzna maszyny wirtualnej                              | Lokalizacja geograficzna obszaru roboczego |
|-------------------------------------|---------------|
| Stany Zjednoczone, Brazylia, Afryka Południowa | Stany Zjednoczone |
| Kanada                              | Kanada        |
| Europa (z wyjątkiem Zjednoczonego Królestwa)   | Europa        |
| Zjednoczone Królestwo                      | Zjednoczone Królestwo |
| Azja (z wyjątkiem Indii, Japonia, Korea, Chiny)   | Azja i Pacyfik  |
| Korea Południowa                              | Azja i Pacyfik  |
| Indie                               | Indie         |
| Japonia                               | Japonia         |
| Chiny                               | Chiny         |
| Australia                           | Australia     |


Migawki dysków maszyny wirtualnej są przechowywane na tym samym koncie magazynu jako dysk maszyny wirtualnej.

Dla maszyn wirtualnych i serwerów działających w innych środowiskach, na przykład lokalnie, można określić obszar roboczy i region, w którym będą przechowywane zebrane dane.

**Azure Security Center Storage**: Informacje dotyczące alertów zabezpieczeń, w tym alerty partnerów, są przechowywane regionalnie zgodnie z lokalizacją powiązanego zasobu platformy Azure, a informacje o stanie kondycji zabezpieczeń i zalecenia są przechowywane centralnie w Stany Zjednoczone lub Europa zgodnie z lokalizacją klienta.
Usługa Azure Security Center zbiera efemeryczne kopie plików zrzutu awaryjnego i analizuje je pod kątem dowodów na próby ich naruszenia i pomyślnie przeprowadzonych ataków. Usługa Azure Security Center dokonuje tej analizy w ramach tego samego obszaru geograficznego, co obszar roboczy, i usuwa efemeryczne kopie po zakończeniu analizy.

Artefakty maszyny są przechowywane centralnie w tym samym regionie, co maszyna wirtualna.


## <a name="managing-data-collection-from-virtual-machines"></a>Zarządzanie zbieraniem danych z maszyn wirtualnych

W przypadku włączenia usługi Security Center na platformie Azure zbieranie danych jest włączone dla każdej subskrypcji platformy Azure. Zbieranie danych można również włączyć dla subskrypcji w sekcji Zasady zabezpieczeń usługi Azure Security Center. Jeśli zbieranie danych jest włączone, usługa Azure Security Center aprowizuje program Microsoft Monitoring Agent na wszystkich maszynach wirtualnych, które już istnieją i są obsługiwane, oraz na tych, które dopiero zostaną utworzone.
Program Microsoft Monitoring Agent przeprowadza skanowanie w poszukiwaniu różnych konfiguracji i zdarzeń związanych z zabezpieczeniami i przekazuje je do śladów funkcji [Śledzenie zdarzeń systemu Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx). Ponadto system operacyjny będzie zgłaszać zdarzenia dziennika zdarzeń w trakcie pracy maszyny. Przykłady takich danych to typ systemu operacyjnego i jego wersja, dzienniki systemu operacyjnego (dzienniki zdarzeń systemu Windows), uruchomione procesy, nazwa maszyny, adresy IP, zalogowany użytkownik i identyfikator dzierżawy. Program Microsoft Monitoring Agent odczytuje wpisy dziennika zdarzeń, a narzędzie Śledzenie zdarzeń systemu Windows śledzi i kopiuje je do obszarów roboczych w celu dokonania analizy. Program Microsoft Monitoring Agent kopiuje również pliki zrzutu awaryjnego do obszarów roboczych oraz włącza zdarzenia tworzenia procesów i inspekcję wiersza polecenia.

Jeśli jest używana warstwa Bezpłatna usługi Azure Security Center, zbieranie danych można również wyłączyć z poziomu maszyn wirtualnych w sekcji Zasady zabezpieczeń. Zbieranie danych jest wymagane dla subskrypcji w warstwie Standardowa. Kolekcja artefaktów i migawki dysków maszyny wirtualnej będzie nadal włączona, nawet jeśli wyłączono zbieranie danych.

## <a name="data-consumption"></a>Użycie danych

Klienci mogą używać danych związanych z usługą Security Center pochodzących z różnych strumieni danych, jak pokazano poniżej:

* **Aktywność platformy Azure**: wszystkie alerty zabezpieczeń, zatwierdzone żądania [dokładnie na czas](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) usługi Security Center i wszystkie alerty generowane przez [adaptacyjne kontrolki aplikacji](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application).
* **Dzienniki Azure monitor**: wszystkie alerty zabezpieczeń.


> [!NOTE]
> Zalecenia dotyczące zabezpieczeń można także wykorzystywać za pośrednictwem interfejsu API REST. Aby uzyskać więcej informacji, zobacz [Security Resource Provider REST API Reference (Dokumentacja interfejsu API REST dostawcy zasobów zabezpieczeń)](https://msdn.microsoft.com/library/mt704034(Azure.100).aspx).

## <a name="see-also"></a>Zobacz także
W tym dokumencie wyjaśniono, jak zarządzane i chronione są dane w usłudze Azure Security Center. Aby dowiedzieć się więcej na temat usługi Azure Security Center, zobacz:

* [Przewodnik planowania i obsługi usługi Azure Security Center](security-center-planning-and-operations-guide.md)— informacje na temat planowania i zagadnień projektowych podczas wdrażania usługi Azure Security Center.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi
* [Monitorowanie rozwiązań partnerskich w usłudze Azure Security Center](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure
