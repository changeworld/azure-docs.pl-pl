---
title: File Integrity Monitoring w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: " Dowiedz się, jak włączyć File Integrity Monitoring w usłudze Azure Security Center. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: monhaber
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2019
ms.author: v-mohabe
ms.openlocfilehash: cc0c319357b39ddb3e88d515613273a6f7dc0867
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65968804"
---
# <a name="file-integrity-monitoring-in-azure-security-center"></a>File Integrity Monitoring w usłudze Azure Security Center
Dowiedz się, jak skonfigurować plik Integrity Monitoring (FIM) w usłudze Azure Security Center za pomocą tego przewodnika.

## <a name="what-is-fim-in-security-center"></a>Co to jest FIM w usłudze Security Center?
Plik Integrity Monitoring (FIM), nazywany też monitorowania zmian, sprawdza, czy plików i rejestrów systemu operacyjnego, aplikacji i innych zmian, które mogą wskazywać na atak. Metody porównania jest używana do określenia, jeśli bieżący stan pliku różni się od ostatniego skanowania pliku. Możesz korzystać z tego porównania, aby ustalić, czy wprowadzono prawidłowe, czy podejrzane zmiany w plikach.

Centrum zabezpieczeń File Integrity Monitoring weryfikuje integralność plików Windows, Windows rejestru i pliki systemu Linux. Możesz wybrać pliki, które mają być monitorowane przez włączenie programu FIM. Usługa Security Center monitoruje pliki za pomocą portalu FIM włączone dla działania, takie jak:

- Tworzenie plików i rejestru i usuwania
- Modyfikacje plików (zmiany rozmiaru pliku, listy kontroli dostępu i wyznaczania wartości skrótu zawartości)
- Modyfikacje rejestru (zmiany rozmiaru, listy kontroli dostępu, typu i zawartości)

Usługa Security Center zaleca jednostki do monitorowania, które można łatwo włączać programu FIM. Można również definiować własne zasady FIM lub jednostek do monitorowania. W tym przewodniku przedstawiono, jak.

> [!NOTE]
> Funkcja pliku Integrity Monitoring (FIM) działa w przypadku komputerów Windows i Linux oraz maszyn wirtualnych i jest dostępne w warstwie standardowa usługi Security Center. Zobacz [cennik](security-center-pricing.md), aby dowiedzieć się więcej na temat warstw cenowych usługi Security Center. FIM służy do przekazywania danych do obszaru roboczego usługi Log Analytics. Dane naliczane są opłaty, na podstawie ilości danych, które zostaną przesłane. Zobacz [cen usługi Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) Aby dowiedzieć się więcej.

FIM używa rozwiązania Azure Change Tracking w celu śledzenia i identyfikowania zmian w Twoim środowisku. Po włączeniu usługa File Integrity Monitoring **Change Tracking** zasobu o typie **rozwiązania**. Częstotliwość zbierania danych szczegółowe informacje, zobacz [Change Tracking szczegółów kolekcji danych](https://docs.microsoft.com/azure/automation/automation-change-tracking#change-tracking-data-collection-details) do śledzenia zmian platformy Azure.

> [!NOTE]
> Jeśli usuniesz **Change Tracking** zasobów, możesz również spowoduje wyłączenie File Integrity Monitoring funkcji w usłudze Security Center.

## <a name="which-files-should-i-monitor"></a>Pliki, które należy monitorować?
Możesz pomyśleć o plikach, które mają kluczowe znaczenie dla systemu i aplikacji, podczas wybierania plików do monitorowania. Należy wziąć pod uwagę w Wybieranie plików, które nie chcą zmienić bez planowania. Wybranie pozycji pliki, które są często zmieniane przez aplikacje lub system operacyjny (na przykład plików dziennika i plików tekstowych) utworzyć wiele hałasu, który był trudny do identyfikowania ataków.

Zaleceniem usługi Security Center, które pliki powinny monitorować domyślnie zgodnie ze znanymi wzorcami ataków, które zawierają zmiany plików i rejestru.

## <a name="using-file-integrity-monitoring"></a>Za pomocą File Integrity Monitoring
1. Otwórz pulpit nawigacyjny usługi **Security Center**.
2. W okienku po lewej stronie w obszarze **Zaawansowana ochrona w chmurze**, wybierz opcję **File Integrity Monitoring**.
![Pulpit nawigacyjny Centrum zabezpieczeń][1]

**File Integrity Monitoring** zostanie otwarty.
  ![Pulpit nawigacyjny Centrum zabezpieczeń][2]

Poniżej przedstawiono informacje dla każdego obszaru roboczego:

- Całkowita liczba zmian, które wystąpiły w ostatnim tygodniu (może być widoczna jest kreska "-" Jeśli FIM nie jest włączona w obszarze roboczym)
- Łączna liczba komputerów i maszynach wirtualnych wysyłających raporty do obszaru roboczego
- Lokalizacja geograficzna obszaru roboczego
- Obszar roboczy jest w ramach subskrypcji platformy Azure

Następujące przyciski mogą być również wyświetlane dla obszaru roboczego:

- ![Tylko włączyć ikonę][3] Wskazuje, że FIM nie jest włączone dla obszaru roboczego. Wybierając obszar roboczy pozwala Ci umożliwiać FIM na wszystkich komputerach w obszarze roboczym.
- ![Ikona Uaktualnij plan][4] wskazuje, że obszar roboczy lub subskrypcji nie jest uruchomiona w ramach warstwy standardowa usługi Security Center. Aby używać funkcji programu FIM, Twoja subskrypcja musi działać Standard.  Wybieranie obszaru roboczego, można przejść na warstwę standardowa. Aby dowiedzieć się więcej na temat warstwy standardowa i sposobu uaktualniania, zobacz [uaktualnienie do warstwy standardowa Security Center w celu uzyskania zwiększonych zabezpieczeń](security-center-pricing.md).
- Pusty (nie jest dostępny przycisk) oznacza, że FIM została już włączona w obszarze roboczym.

W obszarze **File Integrity Monitoring**, możesz wybrać obszar roboczy, aby włączyć usługi FIM dla danego obszaru roboczego, wyświetlić pulpit nawigacyjny File Integrity Monitoring dla danego obszaru roboczego lub [uaktualnienia](security-center-pricing.md) obszar roboczy na warstwę standardowa.

## <a name="enable-fim"></a>Włączanie usługi FIM
Aby włączyć usługi FIM w obszarze roboczym:

1. W obszarze **File Integrity Monitoring**, wybierz obszar roboczy z **Włącz** przycisku.
2. **Włącz usługa file integrity monitoring** otwiera wyświetlanie liczby maszyn Windows i Linux w obszarze roboczym.

   ![Włącz usługa file integrity monitoring][5]

   Zalecane ustawienia dla Windows i Linux są także wyświetlane.  Rozwiń **pliki Windows**, **rejestru**, i **pliki systemu Linux** aby zobaczyć pełną listę zalecanych elementów.

3. Usuń zaznaczenie pola wyboru żadnych zalecanych jednostek, czy chcesz zastosować FIM do.
4. Wybierz **zastosować usługa file integrity monitoring** umożliwiające programu FIM.

> [!NOTE]
> Ustawienia można zmienić w dowolnym momencie. Zobacz Edytuj monitorowane jednostek poniżej, aby dowiedzieć się więcej.
>
>

## <a name="view-the-fim-dashboard"></a>Wyświetl pulpit nawigacyjny programu FIM
**Usługa File integrity monitoring** pulpit nawigacyjny wyświetla się w przypadku obszarów roboczych, w której włączono usługi FIM. Zostanie otwarty pulpit nawigacyjny programu FIM, po włączeniu usługi FIM w obszarze roboczym lub wybierz obszar roboczy w **File Integrity Monitoring** okna, w którym jest już włączona w programie FIM.

![Pulpit nawigacyjny monitorowania integralności w pliku][6]

Pulpit nawigacyjny programu FIM dla obszaru roboczego wyświetla następujące informacje:

- Łączna liczba maszyn, połączone z obszarem roboczym
- Łączna liczba zmian, które wystąpiły podczas wybranego okresu
- Podział Zmień typ (pliki, rejestr)
- Lista kategorii zmian (zmodyfikowany, dodane, usunięte)

Wybieranie filtru w górnej części pulpitu nawigacyjnego umożliwia zastosowanie okres czasu, przez jaki chcesz zobaczyć zmiany.

![Filtr okresu czasu][7]

**Komputerów** karcie (jak pokazano powyżej) znajduje się lista wszystkich maszynach aktualnie raportujących do tego obszaru roboczego. Dla każdej maszyny pulpit nawigacyjny Wyświetla:

- Łączna liczba zmian, które wystąpiły w wybranym okresie czasu
- Podział łączna liczba zmian jako zmiany pliku lub zmiany w rejestrze

**Wyszukiwania w dzienniku** zostanie otwarta po wprowadzeniu nazwy komputera w wyszukiwaniu pola lub wybierz maszynę, na liście na karcie komputery. Wyszukiwanie w dzienniku Wyświetla wszystkie zmiany wprowadzone w czasie wybranego przedziału czasu dla maszyny. Można rozwinąć zmiany, aby uzyskać więcej informacji.

![Przeszukiwanie dzienników][8]

**Zmiany** (pokazana poniżej) zawiera listę wszystkich zmian dla obszaru roboczego w wybranym okresie. Dla każdej jednostki, który został zmieniony na listach pulpitu nawigacyjnego:

- Komputer, kiedy wystąpiła zmiana na
- Typ zmiany (rejestru lub plików)
- Kategoria zmiany (zmodyfikowany, dodane, usunięte)
- Data i godzina zmiany

![Zmiany w obszarze roboczym][9]

**Zmień szczegóły** zostanie otwarta po wprowadzeniu zmian w wyszukiwaniu pola lub wybierz jednostkę, w obszarze **zmiany** kartę.

![Szczegóły zmiany][10]

## <a name="edit-monitored-entities"></a>Edytuj monitorowane jednostek

1. Wróć do **File Integrity Monitoring pulpit nawigacyjny** i wybierz **ustawienia**.

   ![Ustawienia][11]

   **Konfiguracja obszaru roboczego** zostanie otwarty trzy karty: **Rejestr Windows**, **pliki Windows**, i **pliki systemu Linux**. Każda karta zawiera listę jednostek, które można edytować w tej kategorii. Dla każdej jednostki, na liście, usługa Security Center określa, czy program FIM będzie włączona (PRAWDA) lub wyłączona (false).  Edytowanie podmiotu pozwala włączyć lub wyłączyć usługi FIM.

   ![Konfiguracja obszaru roboczego][12]

2. Wybierz ochronę tożsamości. W tym przykładzie Wybraliśmy elementu w obszarze rejestru Windows. **Edytowanie na potrzeby śledzenia zmian** zostanie otwarty.

   ![Edytuj lub śledzenie zmian][13]

W obszarze **edytowanie na potrzeby śledzenia zmian** możesz:

- Włącz (True) lub wyłączyć (False) usługa file integrity monitoring
- Podaj lub zmień nazwę jednostki
- Podaj lub zmień wartość lub ścieżki
- Usunąć jednostki, odrzucić zmianę lub zapisać zmiany

## <a name="add-a-new-entity-to-monitor"></a>Dodaj nową jednostkę do monitorowania
1. Wróć do **File integrity monitoring pulpit nawigacyjny** i wybierz **ustawienia** u góry. **Konfiguracja obszaru roboczego** zostanie otwarty.
2. W obszarze **Konfiguracja obszaru roboczego**, wybierz kartę dla typu jednostki, który chcesz dodać: Windows rejestru, plików Windows lub pliki systemu Linux. W tym przykładzie Wybraliśmy **pliki systemu Linux**.

   ![Dodaj nowy element do monitorowania][14]

3. Wybierz pozycję **Dodaj**. **Dodaj do śledzenia zmian** zostanie otwarty.

   ![Wprowadź wymagane informacje.][15]

4. Na **Dodaj** strony, wpisz żądane informacje i wybierz **Zapisz**.

## <a name="disable-monitored-entities"></a>Wyłączanie monitorowania jednostek
1. Wróć do **File Integrity Monitoring** pulpitu nawigacyjnego.
2. Wybierz obszar roboczy, gdzie FIM jest obecnie włączona. Obszar roboczy jest włączona dla programu FIM, jeśli brakuje Włącz przycisk lub Uaktualnij Plan.

   ![Wybierz obszar roboczy, w której włączono usługi FIM][16]

3. Usługa File Integrity Monitoring wybierz **ustawienia**.

   ![Wybierz ustawienia][17]

4. W obszarze **Konfiguracja obszaru roboczego**, wybierz grupę gdzie **włączone** jest ustawiona na wartość true.

   ![Konfiguracja obszaru roboczego][18]

5. W obszarze **edytowanie na potrzeby śledzenia zmian** zestaw okna **włączone** na wartość False.

   ![Enabled ustaw wartość false][19]

6. Wybierz pozycję **Zapisz**.

## <a name="folder-and-path-monitoring-using-wildcards"></a>Folderu i ścieżka monitorowania przy użyciu symboli wieloznacznych

Używać symboli wieloznacznych, aby uprościć śledzenia między katalogami. Po skonfigurowaniu folderu monitorowania przy użyciu symboli wieloznacznych, stosowane są następujące reguły:
-   Symbole wieloznaczne są wymagane do śledzenia wielu plików.
-   Symboli wieloznacznych można używać tylko w ostatnim segmencie ścieżki, takie jak C:\folder\file lub /etc/*.conf
-   Jeśli zmienna środowiskowa zawiera ścieżkę, która jest nieprawidłowa, weryfikacja zakończy się powodzeniem, ale ścieżka zakończy się niepowodzeniem po uruchomieniu spisu.
-   Podczas ustawiania ścieżki, należy unikać ogólne ścieżek, takich jak c:\*. * co spowoduje zbyt wiele folderów-są przenoszone.

## <a name="disable-fim"></a>Disable FIM
Można wyłączyć usługi FIM. FIM używa rozwiązania Azure Change Tracking w celu śledzenia i identyfikowania zmian w Twoim środowisku. Po wyłączeniu usługi FIM, usuniesz rozwiązania Change Tracking z wybranego obszaru roboczego.

1. Aby wyłączyć programu FIM, wróć do **File Integrity Monitoring** pulpitu nawigacyjnego.
2. Wybierz obszar roboczy.
3. W obszarze **File Integrity Monitoring**, wybierz opcję **wyłączyć**.

   ![Disable FIM][20]

4. Wybierz **Usuń** można wyłączyć.

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono Użyj pliku Integrity Monitoring (FIM) w usłudze Security Center. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń](tutorial-security-policy.md) — informacje o sposobie konfigurowania zasad zabezpieczeń dla subskrypcji platformy Azure i grup zasobów.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń](security-center-recommendations.md) — Dowiedz się, w jaki sposób zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń](security-center-monitoring.md)— informacje o sposobie monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi](security-center-managing-and-responding-alerts.md)— Dowiedz się, jak zarządzać i reagować na alerty zabezpieczeń.
* [Monitorowanie rozwiązań partnerskich](security-center-partner-solutions.md) — informacje o sposobie monitorowania stanu kondycji rozwiązań partnerskich.
* [Usługa Security Center — często zadawane pytania](security-center-faq.md)— odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/) — najnowsze informacje na temat zabezpieczeń platformy Azure.

<!--Image references-->
[1]: ./media/security-center-file-integrity-monitoring/security-center-dashboard.png
[2]: ./media/security-center-file-integrity-monitoring/file-integrity-monitoring.png
[3]: ./media/security-center-file-integrity-monitoring/enable.png
[4]: ./media/security-center-file-integrity-monitoring/upgrade-plan.png
[5]: ./media/security-center-file-integrity-monitoring/enable-fim.png
[6]: ./media/security-center-file-integrity-monitoring/fim-dashboard.png
[7]: ./media/security-center-file-integrity-monitoring/filter.png
[8]: ./media/security-center-file-integrity-monitoring/log-search.png
[9]: ./media/security-center-file-integrity-monitoring/changes-tab.png
[10]: ./media/security-center-file-integrity-monitoring/change-details.png
[11]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings.png
[12]: ./media/security-center-file-integrity-monitoring/workspace-config.png
[13]: ./media/security-center-file-integrity-monitoring/edit.png
[14]: ./media/security-center-file-integrity-monitoring/add.png
[15]: ./media/security-center-file-integrity-monitoring/add-item.png
[16]: ./media/security-center-file-integrity-monitoring/fim-dashboard-disable.png
[17]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings-disabled.png
[18]: ./media/security-center-file-integrity-monitoring/workspace-config-disable.png
[19]: ./media/security-center-file-integrity-monitoring/edit-disable.png
[20]: ./media/security-center-file-integrity-monitoring/disable-fim.png
