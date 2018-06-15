---
title: Plik integralności monitorowania w Centrum zabezpieczeń Azure (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: " Dowiedz się, jak włączyć monitorowanie integralności plików w Centrum zabezpieczeń Azure. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2018
ms.author: terrylan
ms.openlocfilehash: 722a4fd11f35f04ed22d73638f07d15c49ea3c26
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34162176"
---
# <a name="file-integrity-monitoring-in-azure-security-center-preview"></a>Plik integralności monitorowania w Centrum zabezpieczeń Azure (wersja zapoznawcza)
Dowiedz się, jak skonfigurować monitorowanie integralności plików (FIM) w Centrum zabezpieczeń Azure za pomocą tego przewodnika.

## <a name="what-is-fim-in-security-center"></a>Co to jest FIM w Centrum zabezpieczeń?
Plik integralności monitorowania (FIM), nazywany również monitorowanie zmian, sprawdza pliki i rejestrów systemu operacyjnego, aplikacji i innych zmian, które mogą wskazywać na atak. Metodę porównywania służy do określenia, czy bieżący stan pliku różni się od ostatniego skanowania pliku. Można wykorzystać to porównanie, aby określić, jeśli prawidłowe lub podejrzane zmiany zostały wprowadzone do plików.

Monitorowanie integralności plików Centrum zabezpieczeń weryfikuje integralność plików systemu Windows, rejestru systemu Windows i Linux plików. Należy wybrać pliki, które mają być monitorowane przez włączenie programu FIM. Centrum zabezpieczeń monitoruje plików za pomocą portalu FIM włączone dla działania, takie jak:

- Tworzenie plików i rejestru i usuwania
- Modyfikacje plików (zmiany rozmiaru pliku, list kontroli dostępu i skrót zawartości)
- Modyfikacje rejestru (zmiany w rozmiarze, listy sterowania dostępu typu i zawartości)

Centrum zabezpieczeń zaleca jednostek do monitorowania, które można łatwo włączać FIM. Można również definiować własne zasady FIM lub jednostki do monitorowania. W tym przewodniku przedstawiono sposób.

> [!NOTE]
> Funkcja monitorowania integralności plików (FIM) działa w przypadku komputerów z systemem Windows i Linux oraz maszyn wirtualnych i jest dostępny w warstwie standardowa Centrum zabezpieczeń. Zobacz [cennik](security-center-pricing.md), aby dowiedzieć się więcej na temat warstw cenowych usługi Security Center.
FIM operacji przekazywania danych do obszaru roboczego analizy dzienników. Dane opłaty, na podstawie ilości danych, które zostaną przesłane. Zobacz [cennik analizy dzienników](https://azure.microsoft.com/pricing/details/log-analytics/) Aby dowiedzieć się więcej.
>
>

> [!NOTE]
> FIM używa rozwiązania Azure śledzenia zmian do śledzenia i identyfikować zmiany w danym środowisku. Podczas monitorowania integralności plików jest włączona, masz **śledzenia zmian** zasobu typu rozwiązania. Jeśli usuniesz **śledzenia zmian** zasobów, należy wyłączyć integralność pliku monitorowania funkcji w Centrum zabezpieczeń.
>
>

## <a name="which-files-should-i-monitor"></a>Pliki, które należy monitorować?
Możesz pomyśleć o plikach, które są krytyczne dla systemu i aplikacji, podczas wybierania który plików do monitorowania. Warto wybrać pliki, które nie powinny być zmienić bez planowania. Wybieranie plików, które często są zmieniane przez aplikacji lub systemu operacyjnego (takich jak pliki dziennika i plików tekstowych) Utwórz dużo hałasu, która utrudnia wykrycie ataku z.

Zaleca Centrum zabezpieczeń, które pliki można monitorować domyślnie zgodnie z wzorcami znane ataki, które obejmują zmiany plików i rejestru.

## <a name="using-file-integrity-monitoring"></a>Przy użyciu pliku monitorowania integralności
1. Otwórz pulpit nawigacyjny usługi **Security Center**.
2. W lewym okienku w obszarze **zaawansowane obrony chmury**, wybierz pozycję **monitorowania integralności plików**.
![Pulpit nawigacyjny Centrum zabezpieczeń][1]

**Plik integralności monitorowania** otwiera.
  ![Pulpit nawigacyjny Centrum zabezpieczeń][2]

Poniżej przedstawiono informacje dla każdego obszaru roboczego:

- Całkowita liczba zmian, które wystąpiły w ostatnim tygodniu (mogą pojawić się kreską "-" Jeśli FIM nie jest włączona w obszarze roboczym)
- Łączna liczba komputerów i raportowania do obszaru roboczego maszyny wirtualne
- Lokalizacji geograficznej obszaru roboczego
- Subskrypcja platformy Azure, który znajduje się w obszarze roboczym

Następujące przyciski mogą być również wyświetlane dla obszaru roboczego:

- ![Włącz ikony][3] Wskazuje, czy FIM nie jest włączona dla obszaru roboczego. Wybieranie obszaru roboczego umożliwia FIM na wszystkich komputerach w obszarze roboczym.
- ![Ikona plan uaktualniania][4] wskazuje, że obszaru roboczego lub subskrypcji nie działa w warstwie standardowa Centrum zabezpieczeń. Aby korzystać z funkcji programu FIM, subskrypcji musi być uruchomiona Standard.  Wybieranie obszaru roboczego można uaktualnić do wersji Standard. Aby dowiedzieć się więcej na temat warstwy standardowa i sposobu uaktualniania, zobacz [uaktualnienie do warstwy standardowa Centrum zabezpieczeń w celu zwiększenia bezpieczeństwa](security-center-pricing.md).
- Puste (nie jest dostępny przycisk) oznacza, że FIM jest już włączona w obszarze roboczym.

W obszarze **monitorowania integralności plików**, można wybrać obszar roboczy, aby włączyć FIM dla tego obszaru roboczego, wyświetlić pulpit nawigacyjny monitorowania integralności plików dla tego obszaru roboczego lub [uaktualnienia](security-center-pricing.md) obszar roboczy, aby Standard.

## <a name="enable-fim"></a>Włącz FIM
Aby włączyć FIM w obszarze roboczym:

1. W obszarze **monitorowania integralności plików**, wybierz obszar roboczy z **włączyć** przycisku.
2. **Aby włączyć monitorowanie integralności plików** zostanie otwarty zawierający liczba komputerów z systemem Windows i Linux w obszarze roboczym.

   ![Aby włączyć monitorowanie integralności plików][5]

   Zalecane ustawienia dla systemu Windows i Linux są także wyświetlane.  Rozwiń węzeł **plików systemu Windows**, **rejestru**, i **pliki Linux** aby zobaczyć pełną listę zalecanych elementów.

3. Usuń zaznaczenie pola wyboru żadnych zalecanych jednostek, czy chcesz zastosować FIM do.
4. Wybierz **zastosować monitorowania integralności plików** umożliwiające FIM.

> [!NOTE]
> Ustawienia można zmienić w dowolnym momencie. Zobacz [edycji monitorowane jednostek](security-center-file-integrity-monitoring.md#edit-monitored-items) poniżej, aby dowiedzieć się więcej.
>
>

## <a name="view-the-fim-dashboard"></a>Widok pulpitu nawigacyjnego programu FIM
**Monitorowania integralności plików** pulpicie nawigacyjnym, dla których włączono FIM obszarów roboczych. Pulpit nawigacyjny FIM otwiera po włączeniu usługi FIM w obszarze roboczym lub wybierz obszar roboczy w **monitorowania integralności plików** okna, który ma już włączone w programie FIM.

![Pulpit nawigacyjny monitorowania integralności w pliku][6]

Pulpit nawigacyjny programu FIM dla obszaru roboczego zostaną wyświetlone następujące informacje:

- Całkowita liczba urządzeń podłączonych do obszaru roboczego
- Łączna liczba zmian, które wystąpiły podczas wybranego okresu
- Podział Zmień typ (pliki, rejestr)
- Podział Zmień kategorię (zmodyfikowane, dodane, usunięte)

Wybieranie filtrów w górnej części pulpitu nawigacyjnego umożliwia zastosowanie czas, który chcesz zobaczyć zmiany.

![Filtr okresu czasu][7]

**Komputery** kartę (pokazanym powyżej) zawiera listę wszystkich maszyn raportowania do tego obszaru roboczego. Dla każdej maszyny pulpit nawigacyjny zawiera:

- Łączna liczba zmian, które wystąpiły w wybranym okresie czasu
- Podział łączna liczba zmian jako zmiany pliku lub zmiany w rejestrze

**Zaloguj się wyszukiwania** zostanie otwarty podczas wprowadzania nazwy komputera do wyszukiwania lub wybierz maszynę kategorii kartę komputery. Dziennik wyszukiwania wyświetla wszystkie zmiany wprowadzone podczas wybranego okresu dla komputera. Można rozwinąć zmiany, aby uzyskać więcej informacji.

![Przeszukiwanie dzienników][8]

**Zmiany** (pokazana poniżej) są wyświetlane wszystkie zmiany w obszarze roboczym podczas wybranego okresu. Dla każdej jednostki, która została zmieniona na listach pulpitu nawigacyjnego:

- Zmiana została wprowadzona w komputerze
- Typ zmiany (rejestru lub plików)
- Kategoria zmiany (zmodyfikowane, dodane, usunięte)
- Data i godzina zmiany

![Zmiany w obszarze roboczym][9]

**Zmień szczegóły** zostanie otwarty podczas wprowadzania zmian w wyszukiwania lub wybierz jednostkę kategorii **zmiany** kartę.

![Zmień szczegóły][10]

## <a name="edit-monitored-entities"></a>Edytuj monitorowane jednostek

1. Wróć do **pulpit nawigacyjny monitorowania integralności plików** i wybierz **ustawienia**.

  ![Ustawienia][11]

  **Konfiguracja obszaru roboczego** otwiera wyświetlanie trzema kartami: **rejestru systemu Windows**, **plików systemu Windows**, i **pliki Linux**. Każda karta zawiera listę jednostek, które można edytować w tej kategorii. Dla każdej jednostki wymienione Centrum zabezpieczeń określa, czy FIM jest włączony (true) lub wyłączona (false).  Edytowanie jednostki pozwala włączyć lub wyłączyć usługi FIM.

  ![Konfiguracja obszaru roboczego][12]

2. Wybierz identityprotection. W tym przykładzie wybrano element w rejestrze systemu Windows. **Edytowanie śledzenia zmian** otwiera.

  ![Edytuj lub śledzenie zmian][13]

W obszarze **edytowanie śledzenia zmian** można wykonywać następujące czynności:

- Włącz (True) lub Wyłącz monitorowanie integralności plików (False)
- Podaj lub zmienić nazwę jednostki
- Podaj lub zmień wartość lub ścieżki
- Usuń jednostki, odrzucić zmiany lub zapisać zmiany

## <a name="add-a-new-entity-to-monitor"></a>Dodaj nową jednostkę do monitorowania
1. Wróć do **integirty pliku pulpit nawigacyjny monitorowania** i wybierz **ustawienia** u góry. **Konfiguracja obszaru roboczego** otwiera.
2. W obszarze **konfiguracji obszaru roboczego**, wybierz kartę dla typu jednostki, która ma zostać dodany: rejestru systemu Windows, plików systemu Windows lub Linux plików. W tym przykładzie wybrano **pliki Linux**.

  ![Dodaj nowy element do monitorowania][14]

3. Wybierz pozycję **Dodaj**. **Dodaj do śledzenia zmian** otwiera.

  ![Wprowadź wymagane informacje][15]

4. Na **Dodaj** , wpisz wymagane informacje i wybrać opcję **zapisać**.

## <a name="disable-monitored-entities"></a>Wyłącz monitorowane jednostek
1. Wróć do **monitorowania integralności plików** pulpitu nawigacyjnego.
2. Wybierz obszar roboczy, gdzie FIM jest obecnie włączona. Obszaru roboczego jest włączone dla programu FIM, jeśli brakuje Włącz przycisk lub uaktualnić Plan.

  ![Wybierz obszar roboczy, w których włączono FIM][16]

3. W obszarze monitorowania integralności plików, wybierz **ustawienia**.

  ![Wybierz ustawienia][17]

4. W obszarze **konfiguracji obszaru roboczego**, wybierz grupę gdzie **włączone** jest ustawiona na true.

  ![Konfiguracja obszaru roboczego][18]

5. W obszarze **edytowanie śledzenia zmian** zestaw okna **włączone** na wartość False.

  ![Enabled ustaw wartość false][19]

6. Wybierz pozycję **Zapisz**.

## <a name="disable-fim"></a>Wyłącz usługi FIM
Można wyłączyć usługi FIM. FIM używa rozwiązania Azure śledzenia zmian do śledzenia i identyfikować zmiany w danym środowisku. Po wyłączeniu programu FIM, należy usunąć rozwiązania śledzenia zmian z wybranego obszaru roboczego.

1. Aby wyłączyć FIM, wróć do **monitorowania integralności plików** pulpitu nawigacyjnego.
2. Wybierz obszar roboczy.
3. W obszarze **monitorowania integralności plików**, wybierz pozycję **wyłączyć**.

  ![Wyłącz usługi FIM][20]

4. Wybierz **Usuń** można wyłączyć.

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono Użyj monitorowania integralności plików (FIM) w Centrum zabezpieczeń. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń](security-center-policies.md) — informacje o sposobie konfigurowania zasad zabezpieczeń dla subskrypcji platformy Azure i grup zasobów.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń](security-center-recommendations.md) — Dowiedz się, w jaki sposób zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń](security-center-monitoring.md)— informacje o sposobie monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi](security-center-managing-and-responding-alerts.md)— Dowiedz się, jak reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich](security-center-partner-solutions.md) — informacje o sposobie monitorowania stanu kondycji rozwiązań partnerskich.
* [Często zadawane pytania dotyczące Centrum zabezpieczeń](security-center-faq.md)— często zadawane pytania dotyczące korzystania z usługi wyszukiwania.
* [Blog Azure Security](http://blogs.msdn.com/b/azuresecurity/) — najnowsze informacje na temat zabezpieczeń platformy Azure.

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
