---
title: Niestandardowe reguły alertów w usłudze Azure Security Center | Microsoft Docs
description: Ten dokument zawiera informacje pomocne podczas konfigurowania niestandardowych reguł alertów w usłudze Azure Security Center.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: f335d8c4-0234-4304-b386-6f1ecda07833
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2018
ms.author: terrylan
ms.openlocfilehash: 326b07a18b07f15850e0283ad51a05b42c1ef0ab
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2018
ms.locfileid: "37341317"
---
# <a name="custom-alert-rules-in-azure-security-center-preview"></a>Niestandardowe reguły alertów w usłudze Azure Security Center (wersja zapoznawcza)
Ten dokument zawiera informacje pomocne podczas konfigurowania niestandardowych reguł alertów w usłudze Azure Security Center.

## <a name="what-are-custom-alert-rules-in-security-center"></a>Co to są niestandardowe reguły alertów w usłudze Security Center?

Usługa Security Center oferuje zestaw wstępnie zdefiniowanych [alertów zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts), które są wyzwalane w przypadku wystąpienia zagrożenia lub podejrzanej aktywności. W niektórych scenariuszach możesz utworzyć niestandardowy alert w celu zaspokojenia potrzeb określonego środowiska.

Niestandardowe reguły alertów w usłudze Security Center umożliwiają definiowanie nowych alertów zabezpieczeń na podstawie danych, które zostały już zebrane ze środowiska. Możesz tworzyć zapytania, a ich wynik może być używany jako kryteria dla reguły niestandardowej. Reguła będzie wykonywana po dopasowaniu tych kryteriów. Do tworzenia zapytań niestandardowych możesz używać zdarzeń związanych z zabezpieczeniami komputerów, dzienników rozwiązań związanych z zabezpieczeniami partnera lub danych pozyskanych za pomocą interfejsów API.

> [!NOTE]
> Niestandardowe alerty nie są obsługiwane w usłudze Security Center [funkcji badania](security-center-investigation.md).
>
>

## <a name="how-to-create-a-custom-alert-rule-in-security-center"></a>Jak utworzyć niestandardową regułę alertu w usłudze Security Center?

Otwórz pulpit nawigacyjny usługi **Security Center** i wykonaj poniższe kroki, aby utworzyć niestandardową regułę alertu:

1.  W lewym okienku w obszarze **Wykrywanie** kliknij pozycję **Niestandardowe reguły alertów (wersja zapoznawcza)**.
2.  Na stronie **Security Center — niestandardowe reguły alertów (wersja zapoznawcza)** kliknij pozycję **Nowa niestandardowa reguła alertu**.

    ![Alert niestandardowy](./media/security-center-custom-alert/security-center-custom-alert-fig1.png)

3.  Zostanie wyświetlona strona tworzenia niestandardowej reguły alertu z następującymi opcjami:

    ![Przycisk Utwórz](./media/security-center-custom-alert/security-center-custom-alert-fig2.png)

4.  W polu **Nazwa** wpisz nazwę reguły niestandardowej.
5.  Wpisz krótki opis, który odzwierciedla cel tej reguły, w polu **Opis**.
6.  W polu **Ważność** wybierz poziom ważności (wysoki, średni, niski) zgodnie z potrzebami.
7.  W polu **Subskrypcja** wybierz subskrypcję, w której ta reguła ma zastosowanie.
8.  Wybierz obszar roboczy, który chcesz monitorować za pomocą tej reguły w **obszaru roboczego** pola, a następnie w **zapytania wyszukiwania** pola zapytania, które chcesz użyć w celu uzyskania wyników.

    > [!NOTE]
    > Musisz mieć uprawnienia zapisu w obszarze roboczym, do przechowywania niestandardowych alertu.
    >
    >

    Wynik zapytania wyzwala alert. Zauważ, że po wpisaniu prawidłowego zapytania w prawym rogu tego pola pojawia się zielony znacznik wyboru:

    ![Zapytanie](./media/security-center-custom-alert/security-center-custom-alert-fig3.png)

10. W polu **Okres** wybierz przedział czasu, w którym zapytanie będzie wykonywane. Zauważ, że wynik wyszukiwania w dolnej części tego pola będzie się zmieniać w zależności od wybranego przedziału czasowego.

    ![Okres](./media/security-center-custom-alert/security-center-custom-alert-fig4.png)

11. W polu **Ocena** wybierz częstotliwość oceniania i wykonywania tej reguły.
12. W polu **Liczba wyników** wybierz operator (większa lub mniejsza niż).
13. W polu **Próg** wpisz liczbę, która będzie używana jako odwołanie dla wybranego wcześniej operatora.
14. **Włącz opcję Pomiń alerty**, jeśli chcesz ustawić czas oczekiwania przed wysłaniem kolejnego alertu dla tej reguły w usłudze Security Center.
15. Kliknij przycisk **OK**, aby zakończyć.

Nowo utworzona reguła alertu zostanie wyświetlona na liście niestandardowych reguł alertów. Po spełnieniu warunków reguły nowy alert zostanie wyzwolony i będzie widoczny na pulpicie nawigacyjnym **Alerty zabezpieczeń**.

![Alerty](./media/security-center-custom-alert/security-center-custom-alert-fig5.png)

Zauważ, że parametry (zapytanie wyszukiwania, próg itp.), które zostały utworzone podczas tworzenia reguły, są dostępne w alercie dla tej reguły niestandardowej.

## <a name="see-also"></a>Zobacz także
W tym dokumencie przedstawiono sposób tworzenia niestandardowej reguły alertu w usłudze Azure Security Center. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń Azure, zobacz następujące artykuły:

* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Dowiedz się, jak zarządzać alertami i reagować na zdarzenia związane z bezpieczeństwem w usłudze Security Center.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md). Informacje na temat sposobu monitorowania kondycji zasobów platformy Azure.
* [Informacje o alertach zabezpieczeń w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Poznaj różne typy alertów zabezpieczeń.
* [Przewodnik rozwiązywania problemów z usługą Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Dowiedz się, jak rozwiązywać typowe problemy z usługą Security Center.
* [Azure Security Center — często zadawane pytania](security-center-faq.md). Odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure Security](http://blogs.msdn.com/b/azuresecurity/). Wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
