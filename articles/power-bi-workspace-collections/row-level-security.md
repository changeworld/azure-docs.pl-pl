---
title: Zabezpieczenia na poziomie wierszy przy użyciu kolekcji obszarów roboczych usługi Power BI
description: Szczegółowe informacje dotyczące zabezpieczeń na poziomie wiersza z kolekcji obszarów roboczych usługi Power BI
services: power-bi-workspace-collections
ms.service: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 2d0c8629e4abf9e27cd204b9a13b79808b177501
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64715197"
---
# <a name="row-level-security-with-power-bi-workspace-collections"></a>Zabezpieczenia na poziomie wierszy przy użyciu kolekcji obszarów roboczych usługi Power BI

Zabezpieczenia na poziomie wiersza (RLS) mogą służyć do ograniczania dostępu użytkowników do danych w raporcie lub zestawie danych, dzięki czemu wielu różnych użytkowników użyć tego samego raportu podczas wszystkich nich będzie widzieć inne dane. Kolekcje obszarów roboczych usługi Power BI obsługują zestawy danych skonfigurowano zabezpieczenia na poziomie wiersza.

![Przepływ zabezpieczenia na poziomie wiersza w kolekcji obszarów roboczych usługi Power BI](media/row-level-security/flow-1.png)

> [!IMPORTANT]
> Kolekcje obszarów roboczych usługi Power BI są przestarzałe i będą dostępne do czerwca 2018 roku lub do daty podanej w kontrakcie. Zachęcamy do zaplanowania migracji do usługi Power BI Embedded, aby uniknąć przerw w działaniu aplikacji. Aby uzyskać informacje dotyczące sposobu przeprowadzenia migracji danych do usługi Power BI Embedded, zobacz [How to migrate Power BI Workspace Collections content to Power BI Embedded (Migrowanie zawartości kolekcji obszarów roboczych usługi Power BI do usługi Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Aby móc korzystać z zabezpieczeń RLS, ważne jest, aby zrozumieć trzech głównych pojęć: Użytkowników, ról i reguł. Przyjrzyjmy się bliżej przy każdym:

**Użytkownicy** — są to faktyczni użytkownicy końcowi wyświetlania raportów. W kolekcji obszarów roboczych usługi Power BI użytkownicy są identyfikowani przy właściwości nazwy użytkownika w tokenie aplikacji.

**Role** — użytkownicy należą do ról. Rola to kontener dla reguły i może być nadano nazwę "Sales Manager" lub "Przedstawiciel handlowy". W kolekcji obszarów roboczych usługi Power BI użytkownicy są identyfikowane za pomocą właściwości ról w tokenu aplikacji.

**Reguły** — role mają reguły, a zasady te są faktyczne filtry, które mają być stosowane do danych. Może to być proste i polega na "Kraj = USA" lub dużo bardziej dynamiczne.

### <a name="example"></a>Przykład

W pozostałej części tego artykułu udostępniamy przykład tworzenia zabezpieczeń RLS, a następnie wykorzystywania ich w osadzonej aplikacji. Przedstawiony przykład używa [próbka analizy handlu detalicznego](https://go.microsoft.com/fwlink/?LinkID=780547) pliku PBIX.

![Przykładowy raport sprzedaży](media/row-level-security/scenario-2.png)

Nasze próbka analizy handlu detalicznego przedstawiają sprzedaż dla wszystkich sklepów w łańcuchu dostaw określonego. Bez zabezpieczeń na poziomie wiersza, regionalny Menedżer loguje się i wyświetleniu raportu zobaczy takie same dane. Starszy zdecydowano, że każdy menedżer regionalny powinien widzieć tylko sprzedaż dotyczącą sklepów, którymi zarządzają Aby to zrobić, możemy użyć zabezpieczeń na poziomie wiersza.

Zabezpieczenia na poziomie wiersza są tworzone w programie Power BI Desktop. Po otwarciu zestawu danych i raportu możemy przełączyć do widoku diagramu, aby wyświetlić schemat:

![Diagram modelu w programie Power BI Desktop](media/row-level-security/diagram-view-3.png)

Poniżej przedstawiono kilka istotnych kwestii dotyczących tego schematu:

* Wszystkie miary, takie jak **Total Sales**, są przechowywane w **sprzedaży** tabeli faktów.
* Istnieją cztery dodatkowe powiązane tabele wymiarów: **Element**, **czasu**, **Store**, i **District**.
* Strzałki w liniach relacji wskazują którędy filtry mogą przepływać z jednej tabeli do innej. Na przykład, jeśli filtr zostanie umieszczony na **Time [Date]**, w bieżącym schemacie będzie filtrować wyłącznie wartości w **sprzedaży** tabeli. Żadne inne tabele będzie mieć wpływ ten filtr, ponieważ wszystkie strzałki w liniach relacji wskazują do tabeli sprzedaży, a nie od niej.
* **District** tabela wskazuje, który menedżera każdego regionu:
  
  ![Wiersze tabeli District](media/row-level-security/district-table-4.png)

Na podstawie tego schematu, jeśli zastosujemy filtr, aby **menedżerów** kolumny w tabeli District i jeśli ten filtr będzie zgodny użytkownikiem wyświetlającym raport, który odfiltrowanie także filtry **Store** i  **Sprzedaż** tabele tylko Pokaż dane dla tego konkretnego district manager.

Oto, jak to zrobić:

1. Na karcie modelowanie kliknij **Zarządzanie rolami**.  
   ![Zarządzanie przycisk ról w Wstążka modelowania](media/row-level-security/modeling-tab-5.png)
2. Utwórz nową rolę o nazwie **Menedżera**.  
   ![Tworzenie ról w programie Power BI Desktop](media/row-level-security/manager-role-6.png)
3. W **District** tabeli wprowadź następujące wyrażenie DAX: **[District Manager] = USERNAME()**  
   ![Wyrażenie filtru DAX dla tabeli w roli](media/row-level-security/manager-role-7.png)
4. Aby upewnić się, że reguły działają na **modelowania** kliknij pozycję **Wyświetl jako role**, a następnie wprowadź następujące dane:  
   ![Wyświetl jako role](media/row-level-security/view-as-roles-8.png)

   Raporty będą teraz pokazywać dane tak, jakby użytkownik jest zalogowany jako **Andrew Ma**.

Zastosowanie filtru w taki sposób, teraz zostało zrobione, filtry w dół wszystkie rekordy z **District**, **Store**, i **sprzedaży** tabel. Jednak ze względu na kierunek filtrowania w relacjach między **sprzedaży** i **czasu**, **sprzedaży** i **elementu**i **Elementu** i **czasu** tabele nie będą filtrowane w dół.

![Widok diagramu z wyróżnioną pozycją relacjami](media/row-level-security/diagram-view-9.png)

Może to być ok tego wymagania, jednak jeśli nie chcemy menedżerów, aby wyświetlić elementy, dla których nie masz podatku od sprzedaży, można włączyć dwukierunkowe filtrowanie krzyżowe relacji i flow filtr zabezpieczeń w obu kierunkach. Można to zrobić, edytując relacji między **sprzedaży** i **elementu**, podobnie do następującego:

![Kierunek filtrowania dla relacji krzyżowego](media/row-level-security/edit-relationship-10.png)

Teraz filtry również mogą przepływać z tabeli Sales, aby **elementu** tabeli:

![Ikona kierunek filtru relacji w widoku diagramu](media/row-level-security/diagram-view-11.png)

> [!NOTE]
> Jeśli używasz trybu zapytania bezpośredniego dla swoich danych, należy włączyć wielu dwukierunkowego filtrowania, wybierając te dwie opcje:

1. **Plik** -> **opcje i ustawienia** -> **funkcje w wersji zapoznawczej** -> **Włącz filtrowanie krzyżowe w obu kierunkach dla zapytania bezpośredniego** .
2. **Plik** -> **opcje i ustawienia** -> **zapytania bezpośredniego** -> **Zezwalaj na nieograniczone miary w trybie zapytania bezpośredniego**.

Aby dowiedzieć się więcej na temat dwukierunkowe filtrowanie krzyżowe, Pobierz [dwukierunkowe filtrowanie krzyżowe w SQL Server Analysis Services 2016 oraz programie Power BI Desktop](https://download.microsoft.com/download/2/7/8/2782DF95-3E0D-40CD-BFC8-749A2882E109/Bidirectional%20cross-filtering%20in%20Analysis%20Services%202016%20and%20Power%20BI.docx) oficjalny dokument.

To opakowuje pracy, które należy wykonać w programie Power BI Desktop, ale ma jednego więcej element pracy musi odbywać się na poziomie reguł zdefiniowanych pracy w Power BI Embedded. Użytkownicy są uwierzytelnieni i autoryzowani przez aplikację i tokenów aplikacji są używane do udzielania użytkownikom dostępu do konkretnego raportu Power BI Embedded. Power BI Embedded nie ma żadnych konkretnych informacji o tym, kim jest użytkownik. Zabezpieczenia na poziomie wiersza musisz przekazać dodatkowy kontekst jako część Twojego tokenu aplikacji:

* **Nazwa użytkownika** (opcjonalnie) — używany na poziomie wiersza jest to ciąg, który może służyć do zidentyfikowania użytkownika podczas stosowania reguł zabezpieczeń na poziomie wiersza. Zobacz wiersz zabezpieczenia na poziomie w usłudze Power BI Embedded
* **role** — ciąg zawierający role do wybrania podczas stosowania reguł zabezpieczeń na poziomie wiersza. W przypadku przekazywania więcej niż jednej roli, należy je przekazywać jako tablicę ciągów.

Utwórz token za pomocą [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) metody. Jeśli właściwość username jest obecny, możesz też przekazać co najmniej jedną wartość w ramach ról.

Na przykład można zmienić EmbedSample. Można zaktualizować wiersza DashboardController 55

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

na

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id, "Andrew Ma", ["Manager"]);'

Token pełnej aplikacji wygląda następująco:

![Przykład tokenu sieci web JSON](media/row-level-security/app-token-string-12.png)

Teraz przy użyciu wszystkich elementów, gdy ktoś zaloguje się do naszej aplikacji do wyświetlenia tego raportu, zobaczą dane, które mogą zobaczyć, zgodnie z definicją naszych zabezpieczenia na poziomie wiersza.

![Raport wyświetlany w aplikacji](media/row-level-security/dashboard-13.png)

## <a name="see-also"></a>Zobacz także

[Zabezpieczenia na poziomie wiersza (RLS) wraz z mocą](https://powerbi.microsoft.com/documentation/powerbi-admin-rls/)  
[Authenticating and authorizing with Power BI Workspace Collections (Uwierzytelnianie i autoryzowanie za pomocą kolekcji obszarów roboczych usługi Power BI)](app-token-flow.md)  
[Program Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Przykład osadzania skryptu JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Masz więcej pytań? [Dołącz do społeczności użytkowników usługi Power BI](https://community.powerbi.com/)
