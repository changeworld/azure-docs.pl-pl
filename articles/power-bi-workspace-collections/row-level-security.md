---
title: Zabezpieczenia na poziomie wiersza z kolekcjami obszaru roboczego programu Power BI
description: Szczegółowe informacje dotyczące zabezpieczeń na poziomie wiersza z kolekcjami obszaru roboczego programu Power BI
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ROBOTS: NOINDEX
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: 7256e2f798fbc32c098f19f60b62e577300868c7
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="row-level-security-with-power-bi-workspace-collections"></a>Zabezpieczenia na poziomie wiersza z kolekcjami obszaru roboczego programu Power BI

Wiersz poziomu zabezpieczeń można ograniczyć dostęp użytkownika do danych w raporcie lub zestawu danych, co pozwala na wielu różnych użytkowników korzystać z tego samego raportu podczas wszystkie inne dane oglądanie. Power BI obszaru roboczego kolekcje obsługują zestawy danych skonfigurowane zabezpieczenia na poziomie wiersza.

![Przepływ zabezpieczeń na poziomie wiersza w kolekcjach obszaru roboczego programu Power BI](media/row-level-security/flow-1.png)

> [!IMPORTANT]
> Kolekcje obszarów roboczych usługi Power BI są przestarzałe i będą dostępne do czerwca 2018 roku lub do daty podanej w kontrakcie. Zachęcamy do zaplanowania migracji do usługi Power BI Embedded, aby uniknąć przerw w działaniu aplikacji. Aby uzyskać informacje dotyczące sposobu przeprowadzenia migracji danych do usługi Power BI Embedded, zobacz [How to migrate Power BI Workspace Collections content to Power BI Embedded (Migrowanie zawartości kolekcji obszarów roboczych usługi Power BI do usługi Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Aby można było wykorzystać zabezpieczenia na poziomie wiersza, jest ważne, że rozumiesz trzy główne pojęcia; Użytkownicy, role i zasady. Spójrzmy bliższe spojrzenie na każdym:

**Użytkownicy** — te są rzeczywiste użytkownicy końcowi wyświetlania raportów. W kolekcjach obszaru roboczego programu Power BI użytkownicy są identyfikowani przez właściwość nazwy użytkownika w tokenie aplikacji.

**Role** — użytkownicy należą do ról. Rola to kontener dla reguły i może być nazwana "Menedżer sprzedaży" lub "Przedstawiciel". W kolekcjach obszaru roboczego programu Power BI użytkownicy są identyfikowani przez właściwość role w tokenie aplikacji.

**Reguły** — role mają zasady, a zasady te są rzeczywiste filtry, które mają być stosowane do danych. Może to być tak proste, jak "kraju = USA" lub coś bardziej dynamiczne.

### <a name="example"></a>Przykład

W pozostałej części tego artykułu udostępniamy przykładem tworzenia zabezpieczenia na poziomie wiersza i wykorzystywanie który w osadzonych aplikacji. Przedstawiony przykład używa [Retail Analysis próbki](http://go.microsoft.com/fwlink/?LinkID=780547) plików PBIX.

![Przykładowy raport sprzedaży](media/row-level-security/scenario-2.png)

Naszej próbki Retail Analysis przedstawia sprzedaż dla wszystkich magazynów w łańcuchu określonego sprzedaży detalicznej. Bez zabezpieczenia na poziomie wiersza, niezależnie od tego, które regionalnego Menedżera loguje i widoków raportów, zobaczy tych samych danych. Kierownictwo stwierdził każdego menedżera regionalnego sprzedaży dla magazynów, którymi zarządzają tylko powinny być widoczne, a w tym celu możemy użyć zabezpieczenia na poziomie wiersza.

Kontrola dostępu jest tworzone w programie Power BI Desktop. Po otwarciu zestawu danych i raportów, możemy przełączyć się do widoku diagramu, aby wyświetlić schemat:

![Diagram modelu w programie Power BI Desktop](media/row-level-security/diagram-view-3.png)

Poniżej przedstawiono kilka istotnych zauważyć tego schematu:

* Wszystkie miary, takich jak **Total Sales**, są przechowywane w **sprzedaży** tabeli faktów.
* Istnieją cztery tabele wymiarów powiązane dodatkowe: **elementu**, **czasu**, **magazynu**, i **regionalnego**.
* Strzałki na linii relacji wskazuje, jaki sposób filtry mogą przepływać z jednej tabeli do innego. Na przykład, jeśli filtr jest umieszczona na **czasu [Date]**, w bieżącym schemacie go tylko filtruje wartości w **sprzedaży** tabeli. Nie inne tabele może niekorzystnie wpływać tego filtru, ponieważ wszystkie strzałki na linii relacji wskazywać tabeli sprzedaży, a nie optymalizacji.
* **Regionalnego** tabela wskazuje będącego menedżera dla każdego regionalnego:
  
  ![Wiersze tabeli okręgu](media/row-level-security/district-table-4.png)

Na podstawie tego schematu, jeśli Trwa stosowanie filtru w celu **Menedżera regionalnego** kolumny w tabeli regionalnego i jeśli spełniają kryteria filtru tego użytkownika wyświetlanie raportu, który filtrować również filtrów w dół **magazynu** i  **Sprzedaży** tabele, aby tylko wyświetlić dane dla tego konkretnego regionalnego menedżera.

Oto jak:

1. Na karcie modelowania kliknij **Zarządzanie rolami**.  
   ![Zarządzanie przycisk ról w modelowania wstążki](media/row-level-security/modeling-tab-5.png)
2. Utwórz nową rolę o nazwie **Menedżera**.  
   ![Tworzenie ról w programie Power BI Desktop](media/row-level-security/manager-role-6.png)
3. W **regionalnego** tabeli wprowadź poniższe wyrażenie DAX: **[regionalnego Manager] = USERNAME()**  
   ![Wyrażenie filtru DAX dla tabeli w roli](media/row-level-security/manager-role-7.png)
4. Aby się upewnić, że zasady działają na **modelowania** , kliknij pozycję **widoku w postaci ról**, a następnie wprowadź następujące:  
   ![Wyświetl jako ról](media/row-level-security/view-as-roles-8.png)

   Raporty będą teraz wyświetlane dane tak, jakby użytkownik jest zalogowany jako **Andrew Ma**.

Stosowanie filtru, sposób, w tym miejscu robiliśmy filtruje dół wszystkie rekordy z **regionalnego**, **magazynu**, i **sprzedaży** tabel. Jednak ze względu na relacje między kierunek filtru **sprzedaży** i **czasu**, **sprzedaży** i **elementu**, i **elementu** i **czasu** tabele nie będą filtrowane w dół.

![Widok diagramu z wyróżnionym relacji](media/row-level-security/diagram-view-9.png)

Może to być ok tego wymagania, jednak jeśli nie chcemy, menedżerów, aby wyświetlić elementy, dla których nie ma żadnej sprzedaży, firma Microsoft może włączyć dwukierunkowego filtrowania krzyżowego relacji i przepływu filtru zabezpieczeń, w obu kierunkach. Można to zrobić, edytując relacji między **sprzedaży** i **elementu**, podobnie do następującej:

![Kierunek filtru relacji między](media/row-level-security/edit-relationship-10.png)

Teraz, filtry również mogą przepływać z tabeli sprzedaży **elementu** tabeli:

![Ikona kierunek filtru relacji w widoku diagramu](media/row-level-security/diagram-view-11.png)

> [!NOTE]
> Jeśli używasz trybu zapytania bezpośredniego dla danych, musisz włączyć między dwukierunkowego filtrowania zaznaczając te dwie opcje:

1. **Plik** -> **opcji i ustawień** -> **funkcje w wersji zapoznawczej** -> **Włącz filtrowanie krzyżowe w obu kierunkach dla zapytania bezpośredniego** .
2. **Plik** -> **opcji i ustawień** -> **DirectQuery** -> **Zezwalaj na nieograniczoną miar w trybie zapytania bezpośredniego**.

Aby dowiedzieć się więcej na temat filtrowania krzyżowego dwukierunkowego, Pobierz [dwukierunkowego filtrowania krzyżowego w SQL Server Analysis Services 2016 i Power BI Desktop](http://download.microsoft.com/download/2/7/8/2782DF95-3E0D-40CD-BFC8-749A2882E109/Bidirectional cross-filtering in Analysis Services 2016 and Power BI.docx) oficjalny dokument.

To opakowuje wszystkie pracy, które należy wykonać w programie Power BI Desktop, ale ma reguł jednego więcej element pracy należy wykonać w celu zabezpieczenia na poziomie wiersza zdefiniowanego w usłudze Power BI Embedded pracy. Użytkownicy uwierzytelniania i autoryzacji przez aplikację i tokenów aplikacji są używane udzielenia tego dostępu użytkownika do określonego raportu Power BI Embedded. Usługa Power BI Embedded nie ma żadnych określone informacje, na który jest użytkownika. Kontrola dostępu do pracy należy przekazać niektóre dodatkowy kontekst jako część token aplikacji:

* **Nazwa użytkownika** (opcjonalnie) — używane zabezpieczenia na poziomie wiersza to ciąg, który może służyć do identyfikowania użytkownika, stosując zasady zabezpieczenia na poziomie wiersza. Zobacz wiersz zabezpieczeń na poziomie przy użyciu usługi Power BI Embedded
* **role** — ciąg zawierający role, aby wybrać podczas stosowania zasad zabezpieczeń na poziomie wiersza. Jeśli przekazywanie więcej niż jednej roli, powinien zostać przekazany jako tablicy ciągów.

Utwórz token za pomocą [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#Microsoft_PowerBI_Security_PowerBIToken_CreateReportEmbedToken_System_String_System_String_System_String_System_DateTime_System_String_System_Collections_Generic_IEnumerable_System_String__) metody. Jeśli właściwość username jest obecny, należy także podać co najmniej jedną wartość w rolach.

Na przykład można zmienić EmbedSample. Wiersz DashboardController 55 może być aktualizowane z

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

na

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id, "Andrew Ma", ["Manager"]);'

Token pełnej aplikacji wygląda następująco:

![Przykład tokenu web JSON](media/row-level-security/app-token-string-12.png)

Teraz z wszystkich części razem, gdy ktoś loguje się do naszej aplikacji, aby wyświetlić ten raport zostanie wyświetlona dane, które mogą zobaczyć, zgodnie z definicją w naszym zabezpieczeń na poziomie wiersza.

![Raport wyświetlany w aplikacji](media/row-level-security/dashboard-13.png)

## <a name="see-also"></a>Zobacz także

[Zabezpieczenia na poziomie wiersza (kontrola dostępu) z zasilania](https://powerbi.microsoft.com/documentation/powerbi-admin-rls/)  
[Authenticating and authorizing with Power BI Workspace Collections (Uwierzytelnianie i autoryzowanie za pomocą kolekcji obszarów roboczych usługi Power BI)](app-token-flow.md)  
[Program Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Przykład osadzania skryptu JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Masz więcej pytań? [Dołącz do społeczności użytkowników usługi Power BI](http://community.powerbi.com/)