---
title: Uzyskiwanie szczegółowych informacji z usługi Azure Security Center za pomocą usługi Power BI | Microsoft Docs
description: Pakiet zawartości usługi Power BI Centrum zabezpieczeń Azure ułatwia znajdowanie alertów zabezpieczeń, zaleceń, zaatakowanych zasobów i trendów na podstawie zestawu danych, który został utworzony na potrzeby raportu.
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: ''
ms.assetid: 0ded6bc7-52e8-43b4-8940-0bee137526e3
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2017
ms.author: yurid
ms.openlocfilehash: 10f7b8f20cc41a5ebb1b1376e2bf17be02600ae4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "22715832"
---
# <a name="get-insights-from-azure-security-center-data-with-power-bi"></a>Uzyskiwanie szczegółowych informacji z Centrum zabezpieczeń Azure za pomocą usługi Power BI
[Pulpit nawigacyjny usługi Power BI](http://aka.ms/azure-security-center-power-bi) w Centrum zabezpieczeń Azure umożliwia wizualizację, analizowanie i filtrowanie zaleceń oraz alertów zabezpieczeń z dowolnego miejsca — także z urządzenia przenośnego. Pulpit nawigacyjny usługi Power BI służy do wyświetlania trendów i wzorców ataków. Można wyświetlać alerty zabezpieczeń według zasobu lub adresu IP źródła oraz nierozwiązane zagrożenia bezpieczeństwa według zasobu lub wieku.

Można również w różny sposób łączyć alerty zabezpieczeń i zalecenia z usługi Security Center z innymi danymi, na przykład korzystając z [dzienników inspekcji platformy Azure](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) i usługi [Azure SQL Database Auditing](https://powerbi.microsoft.com/blog/monitor-your-azure-sql-database-auditing-activity-with-power-bi/). Zarówno dzienniki inspekcji platformy Azure, jak i usługa Azure SQL Database Auditing, zawierają pulpity nawigacyjne usługi Power BI i umożliwiają również wyeksportowanie tych danych do programu Excel, co pozwoli na łatwe raportowanie stanu zabezpieczeń zasobów w chmurze.

## <a name="using-azure-security-center-dashboard-to-access-power-bi"></a>Uzyskiwanie dostępu do usługi Power BI za pomocą pulpitu nawigacyjnego Centrum zabezpieczeń Azure
Pulpit nawigacyjny Centrum zabezpieczeń Azure umożliwia również dostęp do raportów usługi Power BI. W tym celu wykonaj następujące kroki:

1. Na pulpicie nawigacyjnym **Azure Security Center** kliknij przycisk **Power BI**.

    ![Łączenie z Centrum zabezpieczeń Azure przy użyciu usługi Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-1-newUI-2017.png)
2. Po prawej stronie zostanie otwarty blok **Power BI** w sposób przedstawiony na poniższym ekranie:

    ![Łączenie z Centrum zabezpieczeń Azure przy użyciu usługi Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new11-2017.png)
3. Jeśli po raz pierwszy tworzysz pulpit nawigacyjny usługi Power BI, możesz wybrać jedną z poniższych opcji w bloku **Eksplorowanie w usłudze Power BI**:

   * **Pulpit nawigacyjny Szczegółowe informacje o zabezpieczeniach**: wybierz tę opcję, jeśli chcesz utworzyć pulpit nawigacyjny, który wyświetla wątki, wykrycia i stan zabezpieczeń. Jest to częściej stosowana opcja dla roli DevOps, która odpowiada za analizowanie stanu ochrony oraz wykrytych alertów w subskrypcjach.
   * **Pulpit nawigacyjny zarządzania zasadami**: wybierz tę opcję, jeśli chcesz uzyskać informacje na temat zasad zarządzania i wymuszania.  Jest to opcja częściej stosowana w centralnym dziale IT, który skupia się na zagadnieniach związanych z zarządzaniem. Ten pulpit nawigacyjny może pomóc w uzyskaniu szczegółowych informacji na temat przestrzegania zasad zabezpieczeń w całej organizacji.
   * Jeśli masz już pulpit nawigacyjny usługi Power BI, kliknij opcję **Przejdź do bieżącego pulpitu nawigacyjnego usługi Power BI**.
4. Na przykład kliknij opcję **Pulpit nawigacyjny Szczegółowe informacje o zabezpieczeniach**. Jeśli po raz pierwszy tworzysz pulpit nawigacyjny usługi Power BI dla usługi Security Center, zostanie wyświetlony monit o zainstalowanie pakietu zawartości. Kliknij przycisk **Pobierz** w oknie **Pakiety zawartości dla usługi Power BI**, jak pokazano na poniższym ekranie:

    ![Pulpit nawigacyjny szczegółowych informacji o zabezpieczeniach Centrum zabezpieczeń Azure](./media/security-center-powerbi/security-center-powerbi-fig1-new3.png)
5. Zostanie wyświetlone okno **Łączenie z pulpitem nawigacyjnym Szczegółowe informacje o zabezpieczeniach usługi Azure Security Center**. Upewnij się, że metoda wybrana w polu **Uwierzytelnianie** to **oAuth2**, jak pokazano poniżej, a następnie kliknij przycisk **Zaloguj**.

    ![Authentication](./media/security-center-powerbi/security-center-powerbi-fig1-new4.png)
6. Może zostać wyświetlony monit z prośbą o ponowne uwierzytelnienie przy użyciu poświadczeń platformy Azure. Po uwierzytelnieniu pulpit nawigacyjny zostanie utworzony. Po utworzeniu pulpitu nawigacyjnego zostanie wyświetlony raport ze strukturą podobną do tej przedstawionej na poniższym ekranie:

    ![Pulpit nawigacyjny usługi Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new5.png)

> [!NOTE]
> Zgodnie z harmonogramem odświeżanie raportu odbywa się codziennie. Jeśli odświeżenie nie powiedzie się, przeczytaj artykuł [Potential Refresh Issues with the Azure Security Center Power BI](https://blogs.msdn.microsoft.com/azuresecurity/2016/04/07/azure-security-center-power-bi-refresh-fails/) (Potencjalne problemy z odświeżaniem w usłudze Power BI Azure Security Center), aby uzyskać więcej informacji na temat rozwiązywania problemów.
>
>

Tutaj można zobaczyć liczbę alertów zabezpieczeń i zaleceń, a także liczbę maszyn wirtualnych, baz danych Azure SQL i zasobów sieciowych, które są monitorowane przez usługę Azure Security Center.

Link do Centrum zabezpieczeń Azure przekierowuje użytkownika do witryny Azure Portal. Wykresy ułatwiają wizualizowanie informacji o zaleceniach dotyczących zabezpieczeń i alertach, takich jak:

* Stan zabezpieczeń zasobów
* Oczekujące zalecenia
* Zalecenia dotyczące maszyny wirtualnej
* Alerty w czasie
* Zaatakowane zasoby
* Zaatakowane adresy IP

Każdy wykres zawiera dodatkowe informacje szczegółowe. Wybierz kafelek, aby zobaczyć więcej informacji. Na przykład kafelek **Kondycja zabezpieczeń zasobów** przedstawia dodatkowe informacje na temat oczekujących zaleceń według zasobów, jak pokazano na poniższym ekranie:

![Zalecenia](./media/security-center-powerbi/security-center-powerbi-fig1-new6.png)

Po kliknięciu wybranego wiersza wykresu pozostałe wiersze są wyszarzane, co ułatwia skupienie się na wybranym wierszu. Aby powrócić do pulpitu nawigacyjnego, kliknij przycisk **Centrum zabezpieczeń Azure** w obszarze **Pulpity nawigacyjne** w lewym okienku na tej stronie.

> [!NOTE]
> Raporty można edytować, aby dostosować je przez dodanie dodatkowych pól lub zmianę istniejących elementów wizualnych. Przeczytaj temat [Interakcja z raportem w widoku do edycji programu Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-interact-with-a-report-in-editing-view/), aby uzyskać więcej informacji.
>
>

Kafelki **Alerty w czasie, Zaatakowane zasoby** i **Adresy IP osoby atakującej** będą wyświetlać podobne dane wyjściowe po kliknięciu każdego z nich. Dzieje się tak, ponieważ raport agreguje informacje dotyczące wszystkich trzech zmiennych i określa je jako **Zaatakowane zasoby**, jak pokazano na poniższym ekranie:

![Zaatakowane zasoby](./media/security-center-powerbi/security-center-powerbi-fig1-new7.png)

W tym momencie można zapisać kopię tego raportu, wydrukować ją lub opublikować w sieci Web za pomocą opcji dostępnych w menu **Plik**.

![Menu Plik](./media/security-center-powerbi/security-center-powerbi-fig8.png)

## <a name="exploring-your-azure-security-center-data-with-power-bi-services"></a>Eksplorowanie danych w Centrum zabezpieczeń Azure za pomocą usług Power BI
Połącz się z [usługami pakietów zawartości usługi Power BI](https://msit.powerbi.com/groups/me/getdata/services) w usłudze Power BI i wykonaj następujące czynności:

1. W oknie **Pakiet zawartości dla usługi Power BI** zostaną wyświetlone dwie opcje w sposób przedstawiony poniżej.

    ![Pakiet zawartości usługi Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new.png)

   > [!NOTE]
   > Jeśli zostały już wykonane czynności z pierwszej części tego artykułu, zostanie wyświetlona tylko jedna opcja, którą jest Zarządzanie zasadami Centrum zabezpieczeń Azure.
   >
   >
2. Na potrzeby tego przykładu kliknij przycisk **Uzyskaj dostęp** w kafelku **Zarządzanie zasadami Centrum zabezpieczeń Azure**.
3. Pamiętaj, aby w oknie **Łączenie z zarządzaniem zasadami Centrum zabezpieczeń Azure** wybrać opcję **oAuth2** na liście rozwijanej **Metoda uwierzytelniania**, tak jak przedstawiono poniżej, a następnie kliknij przycisk **Zaloguj**.

    ![Okno Zarządzanie zasadami](./media/security-center-powerbi/security-center-powerbi-fig1-new8.png)
4. Nastąpi przekierowanie do strony uwierzytelniania, gdzie należy wpisać poświadczenia używane do nawiązywania połączeń z Centrum zabezpieczeń Azure. Po zakończeniu uwierzytelniania usługa Power BI rozpocznie importowanie danych używanych do tworzenia raportów. W tym czasie w prawym górnym rogu przeglądarki może zostać wyświetlony następujący komunikat:

    ![Łączenie z Centrum zabezpieczeń Azure przy użyciu usługi Power BI](./media/security-center-powerbi/security-center-powerbi-fig4.png)

   > [!NOTE]
   > Jeśli pulpit nawigacyjny jest tworzony po raz pierwszy, może to trwać dłużej niż zwykle — szczególnie w przypadku scenariuszy z wieloma subskrypcjami.
   >
   >
5. Po zakończeniu procesu pulpit nawigacyjny usługi Power BI w usłudze Azure Security Center zostanie załadowany z raportem **Zarządzanie zasadami** podobnym do tego przedstawionego poniżej:

    ![Pulpit nawigacyjny Zarządzanie zasadami](./media/security-center-powerbi/security-center-powerbi-fig1-new9.png)

## <a name="see-also"></a>Zobacz też
W tym dokumencie omówiono korzystanie z usługi Power BI w Centrum zabezpieczeń Azure. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń Azure, zobacz następujące artykuły:

* [Przewodnik planowania i użytkowania Centrum zabezpieczeń Azure](security-center-planning-and-operations-guide.md)— informacje o sposobie planowania wdrożenia usługi Azure Security Center.
* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](security-center-policies.md) — informacje na temat konfigurowania ustawień zabezpieczeń w usłudze Azure Security Center
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi
* [Blog Azure Security](http://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure
