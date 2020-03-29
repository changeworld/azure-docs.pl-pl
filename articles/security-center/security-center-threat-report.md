---
title: Raport analizy zagrożeń usługi Azure Security Center | Microsoft Docs
description: Ten dokument ułatwia korzystanie z raportów analizy zagrożeń usługi Azure Security Center w trakcie badania w celu uzyskania większej ilości informacji na temat alertu zabezpieczeń.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5662e312-e8c2-4736-974e-576eeb333484
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: memildin
ms.openlocfilehash: f8b4063d87fa9a89dccd42eddea644609bd6ff27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921253"
---
# <a name="azure-security-center-threat-intelligence-report"></a>Raport analizy zagrożeń usługi Azure Security Center
W tym dokumencie wyjaśniono, w jaki sposób raporty analizy zagrożeń usługi Azure Security Center mogą ułatwić uzyskanie większej ilości informacji na temat zagrożenia, które spowodowało wygenerowanie alertu zabezpieczeń.

## <a name="what-is-a-threat-intelligence-report"></a>Czym jest raport analizy zagrożeń?
Ochrona przed zagrożeniami usługi Security Center polega na monitorowaniu informacji o zabezpieczeniach z zasobów platformy Azure, sieci i połączonych rozwiązań partnerskich. Analizuje ona te informacje, często zestawiając informacje z wielu źródeł, aby zidentyfikować zagrożenia. Aby uzyskać więcej informacji, zobacz [Jak usługa Azure Security Center wykrywa zagrożenia i reaguje na nie](security-center-alerts-overview.md#detect-threats).

Gdy usługa Security Center zidentyfikuje zagrożenie, wywoła [alert zabezpieczeń](security-center-managing-and-responding-alerts.md), który zawiera szczegółowe informacje dotyczące określonego zdarzenia — w tym propozycje rozwiązania problemu. Aby pomóc zespołom reagowania na incydenty, zbadać i skorygować zagrożenia, Usługa Security Center zawiera raport analizy zagrożeń zawierający informacje o wykrytym zagrożeniu, w tym informacje, takie jak:

* tożsamość lub powiązania osoby atakującej (jeśli takie informacje są dostępne);
* cele osoby atakującej;
* bieżące i wcześniejsze kampanie ataków (jeśli takie informacje są dostępne);
* Taktyka, narzędzia i procedury atakujących
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

Tego typu informacje są przydatne podczas procesu reagowania na incydenty, gdzie toczy się dochodzenie w celu zrozumienia źródła ataku, motywacji osoby atakującej i co zrobić, aby złagodzić ten problem.

## <a name="how-to-access-the-threat-intelligence-report"></a>Jak uzyskać dostęp do raportu analizy zagrożeń?
Bieżące alerty można przeglądać przy użyciu kafelka **Alerty zabezpieczeń**. Otwórz witrynę Azure portal i wykonaj poniższe czynności, aby uzyskać więcej informacji na temat każdego alertu:

1. Na pulpicie nawigacyjnym Centrum zabezpieczeń widoczny jest kafelek **Alerty zabezpieczeń**.
2. Kliknij kafelek, aby otworzyć blok **Alerty zabezpieczeń**, który zawiera więcej szczegółowych informacji na temat alertów, i kliknij alert zabezpieczeń, o którym chcesz uzyskać więcej informacji.

    ![Alerty zabezpieczeń](./media/security-center-threat-report/security-center-threat-report-fig1.png)
3. W takim przypadku **podejrzany proces wykonany** ostrze pokazuje szczegóły dotyczące alertu, jak pokazano na rysunku poniżej:

    ![Szczegóły alertu zabezpieczeń](./media/security-center-threat-report/security-center-threat-report-fig2.png)
4. Ilość informacji dostępnych dla każdego alertu zabezpieczeń zależy od typu alertu. W polu **RAPORTY** masz łącze do raportu analizy zagrożeń. Po jego kliknięciu zostanie wyświetlone kolejne okno przeglądarki z plikiem PDF.

   ![Wybór magazynu](./media/security-center-threat-report/security-center-threat-report-fig3.png)

Tutaj można pobrać plik PDF raportu i przeczytać więcej na temat wykrytego problemu z zabezpieczeniami oraz podjąć działania w oparciu o dostarczone informacje.

## <a name="see-also"></a>Zobacz też
W tym dokumencie opisano, jak przydatne mogą być raporty analizy zagrożeń usługi Azure Security Center w trakcie sprawdzania alertów zabezpieczeń. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń Azure, zobacz następujące artykuły:

* [Przewodnik po planowaniu i operacjach usługi Azure Security Center](security-center-planning-and-operations-guide.md). Informacje na temat planowania i zagadnień projektowych podczas wdrażania usługi Azure Security Center.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md). Informacje na temat sposobu zarządzania alertami zabezpieczeń i reagowania na nie.
* [Obsługa zdarzeń naruszenia zabezpieczeń w usłudze Azure Security Center](security-center-incident.md)