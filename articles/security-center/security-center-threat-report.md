---
title: Raport analizy zagrożeń usługi Azure Security Center | Microsoft Docs
description: Ten dokument ułatwia korzystanie z raportów analizy zagrożeń usługi Azure Security Center w trakcie badania w celu uzyskania większej ilości informacji na temat alertu zabezpieczeń.
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: 5662e312-e8c2-4736-974e-576eeb333484
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: yurid
ms.openlocfilehash: f0c1588633f548b740e6b9f6a7a3121ef791500a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51234259"
---
# <a name="azure-security-center-threat-intelligence-report"></a>Raport analizy zagrożeń usługi Azure Security Center
W tym dokumencie wyjaśniono, w jaki sposób raporty analizy zagrożeń usługi Azure Security Center mogą ułatwić uzyskanie większej ilości informacji na temat zagrożenia, które spowodowało wygenerowanie alertu zabezpieczeń.

## <a name="what-is-a-threat-intelligence-report"></a>Czym jest raport analizy zagrożeń?
Wykrywanie zagrożeń za pomocą usługi Security Center polega na monitorowaniu informacji o zabezpieczeniach uzyskanych z zasobów platformy Azure, sieci i powiązanych rozwiązań partnerskich. Analizuje ona te informacje, często zestawiając informacje z wielu źródeł, aby zidentyfikować zagrożenia. Proces ten jest częścią [funkcji wykrywania](security-center-detection-capabilities.md) w usłudze Security Center.

Gdy usługa Security Center zidentyfikuje zagrożenie, wywoła [alert zabezpieczeń](security-center-managing-and-responding-alerts.md), który zawiera szczegółowe informacje dotyczące określonego zdarzenia — w tym propozycje rozwiązania problemu. Aby pomóc zespołom reagowania na zdarzenia w badaniu i usuwaniu zagrożeń, usługa Security Center obejmuje raport analizy zagrożeń zawierający informacje na temat wykrytego zagrożenia, takie jak:

* tożsamość lub powiązania osoby atakującej (jeśli takie informacje są dostępne);
* cele osoby atakującej;
* bieżące i wcześniejsze kampanie ataków (jeśli takie informacje są dostępne);
* taktyka, narzędzia i procedury osoby atakującej;
* skojarzone wskaźniki naruszenia (IoC), np. adresy URL i skróty plików;
* wiktymologia, czyli informacje dotyczące branży i obszaru geograficznego, które mogą być pomocne w ustaleniu, czy zasoby na platformie Azure są zagrożone;
* informacje dotyczące ograniczania i usuwania zagrożeń.

> [!NOTE]
> Ilość informacji w poszczególnych raportach będzie różna. Poziom szczegółowości zależy od aktywności i powszechności złośliwego oprogramowania.
>
>

Usługa Security Center obejmuje trzy typy raportów zagrożeń, które mogą różnić się w zależności od ataku. Dostępne raporty:

* **Raport grupy działań**: zawiera dokładne informacje dotyczące atakujących osób, ich celów oraz taktyki.
* **Raport kampanii**: koncentruje się na szczegółach określonych kampanii ataków.
* **Raport z podsumowaniem zagrożenia**: obejmuje wszystkie elementy z poprzednich dwóch raportów.

Informacje tego typu są bardzo przydatne w procesie [reagowania na zdarzenia](security-center-incident-response.md), w trakcie którego stale odbywa się badanie źródła ataku, motywacji atakującej osoby oraz sposobów ograniczenia zagrożeń związanych z tym problemem.

## <a name="how-to-access-the-threat-intelligence-report"></a>Jak uzyskać dostęp do raportu analizy zagrożeń?
Bieżące alerty można przeglądać przy użyciu kafelka **Alerty zabezpieczeń**. Otwórz witrynę Azure Portal i wykonaj poniższe kroki, aby wyświetlić więcej szczegółowych informacji dotyczących poszczególnych alertów:

1. Na pulpicie nawigacyjnym Centrum zabezpieczeń widoczny jest kafelek **Alerty zabezpieczeń**.
2. Kliknij kafelek, aby otworzyć blok **Alerty zabezpieczeń**, który zawiera więcej szczegółowych informacji na temat alertów, i kliknij alert zabezpieczeń, o którym chcesz uzyskać więcej informacji.

    ![Alerty zabezpieczeń](./media/security-center-threat-report/security-center-threat-report-fig1.png)
3. W tym przypadku blok **Suspicious process executed** (Wykonano podejrzany proces) zawiera szczegółowe informacje dotyczące alertu, jak przedstawiono na rysunku poniżej:

    ![Szczegóły alertu zabezpieczeń](./media/security-center-threat-report/security-center-threat-report-fig2.png)
4. Ilość informacji dostępnych dla każdego alertu zabezpieczeń zależy od typu alertu. W polu **RAPORTY** znajduje się link do raportu analizy zagrożeń. Po jego kliknięciu zostanie wyświetlone kolejne okno przeglądarki z plikiem PDF.

   ![Wybór magazynu](./media/security-center-threat-report/security-center-threat-report-fig3.png)

Tutaj można pobrać plik PDF raportu i przeczytać więcej na temat wykrytego problemu z zabezpieczeniami oraz podjąć działania w oparciu o dostarczone informacje.

## <a name="see-also"></a>Zobacz także
W tym dokumencie opisano, jak przydatne mogą być raporty analizy zagrożeń usługi Azure Security Center w trakcie sprawdzania alertów zabezpieczeń. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń Azure, zobacz następujące artykuły:

* [Azure Security Center — często zadawane pytania](security-center-faq.md). Odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Korzystanie z usługi Azure Security Center w celu reagowania na zdarzenia](security-center-incident-response.md)
* [Funkcje wykrywania usługi Azure Security Center](security-center-detection-capabilities.md)
* [Przewodnik planowania i obsługi usługi Azure Security Center](security-center-planning-and-operations-guide.md). Informacje na temat planowania i zagadnień projektowych podczas wdrażania usługi Azure Security Center.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md). Informacje na temat sposobu zarządzania alertami zabezpieczeń i reagowania na nie.
* [Obsługa zdarzeń naruszenia zabezpieczeń w usłudze Azure Security Center](security-center-incident.md)
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/). Wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
