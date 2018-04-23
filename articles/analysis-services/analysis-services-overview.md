---
title: Omówienie usługi Azure Analysis Services | Microsoft Docs
description: Dowiedz się więcej o usługach Analysis Services na platformie Azure.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: overview
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: da2ab7b0d8b83238def346790362b680cd8eda23
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="azure-analysis-services-overview"></a>Omówienie usługi Azure Analysis Services
![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Usługi Azure Analysis Services udostępniają funkcje modelowania danych klasy korporacyjnej w chmurze. Jest to w pełni zarządzane rozwiązanie „platforma jako usługa” (PaaS), zintegrowane z usługami platformy danych Azure. 

Usługi Analysis Services umożliwiają mieszanie i łączenie danych z wielu źródeł, definiowanie metryk oraz zabezpieczanie danych w jednym zaufanym modelu danych semantycznych. Model danych umożliwia użytkownikom łatwiejsze i szybsze przeglądanie dużych ilości danych przy użyciu aplikacji klienckich, takich jak Power BI, Excel, Reporting Services, aplikacje innych firm i aplikacje niestandardowe.

![Źródła danych](./media/analysis-services-overview/aas-overview-data-sources.png)

Zapoznaj się z [tym wideo](https://sec.ch9.ms/ch9/d6dd/a1cda46b-ef03-4cea-8f11-68da23c5d6dd/AzureASoverview_high.mp4), aby dowiedzieć się, jak usługa Azure Analysis Services wpasowuje się w ogólne możliwości analizy biznesowej oferowane przez firmę Microsoft, i jak można skorzystać na przeniesieniu modeli danych do chmury.

## <a name="built-on-sql-server-analysis-services"></a>Powstałe na bazie usług SQL Server Analysis Services
Usługi Azure Analysis Services są zgodne z wieloma wspaniałymi funkcjami, które już istnieją w usługach SQL Server Analysis Services Enterprise Edition. Usługi Azure Analysis Services obsługują modele tabelaryczne na [poziomach zgodności](analysis-services-compat-level.md) 1200 i 1400. Obsługiwane są partycje, zabezpieczenia na poziomie wierszy, relacje dwukierunkowe i przekształcenia. Tryby W pamięci i DirectQuery oznaczają błyskawiczne przetwarzanie zapytań względem ogromnych, złożonych zestawów danych.

Modele tabelaryczne oferują szybkie tworzenie rozwiązań i są wysoce dostosowywalne. Dla deweloperów modele tabelaryczne mają usługę TOM (Tabular Object Model) do opisywania obiektów modeli. Usługa TOM jest dostępna w pakiecie JSON za pośrednictwem języka [TMSL (Tabular Model Scripting Language)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) oraz języka definicji danych AMO w przestrzeni nazw [Microsoft.AnalysisServices.Tabular](https://msdn.microsoft.com/library/microsoft.analysisservices.tabular.aspx).

## <a name="better-with-azure"></a>Lepiej korzystać z platformy Azure
Usługi Azure Analysis Services można zintegrować z wieloma usługami platformy Azure, umożliwiając tworzenie wyrafinowanych rozwiązań analitycznych. Integracja z usługą [Azure Active Directory](../active-directory/active-directory-whatis.md) zapewnia bezpieczny i oparty na rolach dostęp do kluczowych danych. Jest możliwa integracja z potokami usługi [Azure Data Factory](../data-factory/introduction.md) przez uwzględnienie działania ładującego dane do modelu. Istnieje możliwość prostego organizowania modeli za pomocą usług [Azure Automation](../automation/automation-intro.md) i [Azure Functions](../azure-functions/functions-overview.md) oraz niestandardowego kodu.

## <a name="get-up-and-running-quickly"></a>Szybkie rozpoczęcie pracy
W witrynie Azure Portal [serwer można utworzyć](analysis-services-create-server.md) w ciągu kilku minut. [Szablony](../azure-resource-manager/resource-manager-create-first-template.md) usługi Azure Resource Manager i program PowerShell pozwalają natomiast aprowizować serwery za pomocą szablonu deklaratywnego. Pojedynczy szablon pozwala wdrożyć wiele usług wraz z innymi składnikami platformy Azure, takimi jak konta magazynu i usługa Azure Functions. 

Gdy już serwer zostanie utworzony, można utworzyć model tabelaryczny bezpośrednio w witrynie Azure Portal. Dzięki nowej [funkcji projektanta internetowego](analysis-services-create-model-portal.md) (wersja zapoznawcza) możesz połączyć się z usługą Azure SQL Database albo źródłem danych usługi Azure SQL Data Warehouse lub zaimportować plik pbix programu Power BI Desktop. Relacje między tabelami są tworzone automatycznie, a następnie można utworzyć miary lub edytować plik model.bim w formacie json bezpośrednio z przeglądarki.

## <a name="scale-to-your-needs"></a>Skalowanie zgodnie z potrzebami

### <a name="the-right-tier-when-you-need-it"></a>Odpowiednia warstwa dopasowana do potrzeb

Usługi Azure Analysis Services są dostępne w warstwach Deweloper, Podstawowa i Standardowa. W każdej warstwie cena planu zależy od mocy przetwarzania, jednostek QPU i rozmiaru pamięci. Plan w ramach warstwy wybiera się podczas tworzenia serwera. W ramach tej samej warstwy można zmieniać plany w górę lub w dół albo przejść na wyższą warstwę, ale nie można przejść z warstwy wyższej na niższą.

Serwer można skalować w górę, skalować w dół lub wstrzymywać. Można do tego użyć witryny Azure Portal lub, aby mieć pełną kontrolę na bieżąco, użyć programu PowerShell. Płaci się wyłącznie za użyte zasoby. Aby dowiedzieć się więcej na temat różnych planów i warstw oraz skorzystać z kalkulatora cen w celu wybrania właściwego planu dla siebie, zobacz [cennik usługi Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

### <a name="scale-out-resources-for-fast-query-responses"></a>Skalowanie zasobów w poziomie w celu szybkiego uzyskiwania odpowiedzi na zapytania

W przypadku skalowania usług Azure Analysis Services w poziomie zapytania klienta są dystrybuowane między wieloma *replikami zapytania* w puli zapytania. Repliki zapytania mają zsynchronizowane kopie modeli tabelarycznych. Dzięki rozproszeniu obciążenia zapytania można zredukować czas odpowiedzi w przypadku dużych obciążeń związanych z zapytaniami. Operacje przetwarzania modelu można oddzielić od puli zapytania, dzięki czemu operacje przetwarzania nie będą wpływać negatywnie na zapytania klienta. Można utworzyć pulę zapytania z nawet siedmioma dodatkowymi replikami zapytania (łącznie ośmioma, wliczając serwer). 

Podobnie jak w przypadku zmiany warstwy użytkownik może skalować repliki zapytania w poziomie zgodnie z potrzebami. Konfiguracja skalowania w poziomie jest możliwa w portalu lub przy użyciu interfejsów API REST. Aby dowiedzieć się więcej, zobacz artykuł [Azure Analysis Services scale-out (Skalowanie usług Azure Analysis Services w poziomie)](analysis-services-scale-out.md).

## <a name="keep-your-data-close"></a>Przechowywanie danych w zasięgu ręki
Serwery usług Azure Analysis Services można tworzyć w następujących [regionach platformy Azure](https://azure.microsoft.com/regions/):

| Ameryki | Europa | Azja i Pacyfik |
|----------|--------|--------------|
|  Brazylia Południowa<br> Kanada Środkowa<br> Wschodnie stany USA 2<br> Środkowo-północne stany USA<br> Środkowo-południowe stany USA<br> Środkowo-zachodnie stany USA<br> Zachodnie stany USA | Europa Północna<br> Południowe Zjednoczone Królestwo<br> Europa Zachodnia |   Australia Południowo-Wschodnia<br> Japonia Wschodnia<br> Azja Południowo-Wschodnia<br> Indie Zachodnie  |

Przez cały czas dodawane są nowe regiony, więc ta lista może być niepełna. Lokalizację wybiera się przy tworzeniu serwera za pomocą witryny Azure Portal lub szablonów usługi Azure Resource Manager. Aby uzyskać najlepszą wydajność, wybierz lokalizację najbliższą względem Twojej największej bazy użytkowników. Zagwarantuj [wysoką dostępność](analysis-services-bcdr.md), wdrażając swoje modele na serwerach nadmiarowych w wielu regionach.

## <a name="migrate-your-existing-tabular-models"></a>Migrowanie istniejących modeli tabelarycznych
Jeśli masz już istniejące lokalne rozwiązania z modelami usług SQL Server Analysis Services, możesz przeprowadzić migrację do usług Azure Analysis Services bez znaczących zmian. Aby przeprowadzić migrację, możesz skorzystać z narzędzi SSDT do wdrożenia modelu na serwerze. Możesz też w programie SSMS użyć funkcji tworzenia i przywracania kopii zapasowych albo języka TMSL.

Jeśli masz lokalne źródła danych, musisz zainstalować i skonfigurować [lokalną bramę danych](analysis-services-gateway.md). Jeśli masz już skonfigurowane role i ich członków, role podlegają migracji, ale ich członków musisz dodać ponownie przy użyciu programu SSMS lub PowerShell.

## <a name="connect-to-popular-data-sources"></a>Łączenie z popularnymi źródłami danych
Usługi Azure Analysis Services obsługują [połączenia z lokalnymi źródłami danych](analysis-services-datasource.md) w organizacji i źródłami danych w chmurze. Połącz dane z lokalnych źródeł danych i źródeł danych w chmurze, aby uzyskać rozwiązanie hybrydowe. 

Nowe modele tabelaryczne 1400 używają nowoczesnej funkcji Get Data w narzędziach SSDT, bazując na formułowym języku zapytań M. Dzięki funkcji Get Data dostępnych jest więcej funkcji przekształceń danych i obsługi mashupów oraz możliwość tworzenia i edytowania własnych, zaawansowanych zapytań języka formuł M. Na przykład dzięki modelom tabelarycznym 1400 można tworzyć modele w oparciu o pliki danych w usłudze Azure Blob Storage.

## <a name="use-the-tools-you-already-know"></a>Korzystaj z narzędzi, które już znasz

![Narzędzia programistyczne BI](./media/analysis-services-overview/aas-overview-dev-tools.png)

#### <a name="sql-server-data-tools-ssdt-for-visual-studio"></a>Narzędzia SQL Server Data Tools (SSDT) dla Visual Studio
Twórz i wdrażaj modele za pomocą bezpłatnych narzędzi [SQL Server Data Tools (SSDT) dla Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Narzędzia SSDT obejmują szablony projektów usług Analysis Services, które pozwalają szybko rozpocząć pracę. Narzędzia SSDT teraz obejmują nowoczesną funkcję Get Data do obsługi zapytań do źródeł danych oraz mashupów dla tabelarycznych modeli 1400. Jeśli znasz już funkcję Get Data w programach Power BI Desktop i Excel 2016, wiesz już, jak łatwo tworzy się wysoce dostosowywalne zapytania do źródeł danych.

#### <a name="sql-server-management-studio"></a>Sql Server Management Studio
Zarządzaj serwerami i bazami danych modeli przy użyciu [programu SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Łącz się ze swoimi serwerami w chmurze. Uruchamiaj skrypty TMSL bezpośrednio z okna zapytań XMLA i automatyzuj zadania za pomocą skryptów TMSL. Nowe funkcje i możliwości pojawiają się bardzo szybko — program SSMS jest aktualizowany co miesiąc.

#### <a name="powershell"></a>PowerShell
Zadania zarządzania zasobami serwerowymi, takie jak tworzenie serwerów, zawieszanie lub wznawianie operacji serwerowych czy zmienianie poziomu (warstwy) serwera korzystają z poleceń cmdlet usługi Azure Resource Manager (AzureRM). Inne zadania do zarządzania bazami danych, takie jak dodawanie lub usuwanie członków ról, przetwarzanie czy uruchamianie skryptów TMSL używają poleceń cmdlet w module SqlServer. Usługa AzureRM i moduły SQLServer są dostępne w [galerii programu PowerShell](https://www.powershellgallery.com/).


## <a name="your-data-is-secure"></a>Twoje dane są bezpieczne
![Wizualizacja danych](./media/analysis-services-overview/aas-overview-secure.png)

#### <a name="authentication"></a>Authentication
Uwierzytelnianie użytkownika dla usług Azure Analysis Services jest obsługiwane przez [usługi Azure Active Directory (AAD)](../active-directory/active-directory-whatis.md). Podczas próby logowania do bazy danych usług Azure Analysis Services użytkownicy używają tożsamości konta organizacji z dostępem do bazy danych, z którą chcą uzyskać połączenie. Te tożsamości użytkowników muszą być członkami domyślnej usługi Azure Active Directory dla subskrypcji, w których znajduje się serwer usług Azure Analysis Services. Aby dowiedzieć się więcej, zobacz [Authentication and user permissions (Uwierzytelnianie i uprawnienia użytkownika)](analysis-services-manage-users.md).

#### <a name="data-security"></a>Bezpieczeństwo danych
Usługi Azure Analysis Services używają magazynu obiektów Blob Azure jako trwałego magazynu przechowania danych i metadanych dla baz danych usług Analysis Services. Pliki danych w ramach obiektu Blob są szyfrowane za pomocą szyfrowania po stronie serwera (SSE) Azure Blob. W trybie zapytania bezpośredniego przechowywane są tylko metadane. Dostęp do danych rzeczywistych ze źródła danych następuje podczas przeszukiwania.

#### <a name="firewall"></a>Zapora

Zapora usług Azure Analysis Services blokuje wszystkie połączenia klienta inne niż określone w regułach. Skonfiguruj reguły, określając dozwolone adresy IP przy użyciu poszczególnych adresów IP klientów lub zakresu. Połączenia usługi Power BI również mogą być dozwolone lub zablokowane. 

#### <a name="on-premises-data-sources"></a>Lokalne źródła danych
Bezpieczny dostęp do danych przechowywanych lokalnie w organizacji jest osiągany przez instalację i skonfigurowanie [lokalnej bramy danych](analysis-services-gateway.md). Bramy zapewniają dostęp do danych w trybach zapytania bezpośredniego i dostępu w pamięci. Po nawiązaniu połączenia pomiędzy modelem usług Azure Analysis Services a źródłem danych lokalnych jest tworzone zapytanie wraz z zaszyfrowanymi poświadczeniami dla źródła danych lokalnych. Usługa bramy w chmurze analizuje zapytanie i wypycha je na magistralę Azure Service Bus. Brama lokalna sonduje magistralę Azure Service Bus, poszukując żądań oczekujących. Następnie brama pobiera zapytanie, odszyfrowuje poświadczenia i nawiązuje połączenie ze źródłem danych w celu wykonania zapytania. Wyniki są wysyłane ze źródła danych do bramy, a następnie do bazy danych usług Azure Analysis Services.

Usługi Azure Analysis Services są objęte [warunkami dotyczącymi usług online firmy Microsoft](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) i [zasadami zachowania poufności informacji w witrynie Microsoft Online Services](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx).
Aby dowiedzieć się więcej na temat zabezpieczeń platformy Azure, zobacz witrynę 	[Centrum Zaufania Microsoft](https://www.microsoft.com/trustcenter/Security/AzureSecurity).



## <a name="supports-the-latest-client-tools"></a>Obsługuje najnowsze narzędzia klienckie
![Wizualizacja danych](./media/analysis-services-overview/aas-overview-clients.png)

Nowoczesne narzędzia do eksploracji i wizualizacji danych, takie jak Power BI, Excel, SQL Server 2017 Reporting Services czy narzędzia innych firm są obsługiwane i udostępniają użytkownikom wysoce interakcyjny i rozbudowany wizualnie wgląd w dane modelu. 

Do łączenia się z serwerami Analysis Services klienci używają [bibliotek klienta](analysis-services-data-providers.md) MSOLAP, AMO lub ADOMD. Aplikacje klienckie firmy Microsoft, takie jak Power BI Desktop i Excel, instalują wszystkie trzy biblioteki klienta. Należy jednak pamiętać, że zależnie od wersji lub częstotliwości aktualizacji, biblioteki klienta mogą nie być w najnowszej wersji wymaganej przez usługi Azure Analysis Services. To samo dotyczy aplikacji niestandardowych lub innych interfejsów, takich jak AsCmd, TOM czy ADOMD.NET. Te aplikacje zwykle wymagają ręcznej instalacji bibliotek jako części pakietu.


## <a name="get-help"></a>Uzyskiwanie pomocy

#### <a name="documentation"></a>Dokumentacja
Usługi Azure Analysis Services są łatwe w konfiguracji i zarządzaniu. Tutaj możesz znaleźć wszystkie informacje potrzebne do tworzenia usług serwera i zarządzania nimi. Tworzenie modelu danych w celu jego wdrożenia na serwerze w znacznym stopniu przypomina tworzenie modelu przeznaczonego do wdrożenia na serwerze lokalnym. W [witrynie pomocy usług SQL Server Analysis Services](https://docs.microsoft.com/sql/analysis-services/analysis-services) jest dostępna szeroka gama artykułów dotyczących koncepcji, procedur, szkoleń i samouczków.

#### <a name="videos"></a>Filmy wideo
Zapoznaj się z pomocnymi wideo, odwiedzając dział [usług Azure Analysis Services w witrynie Channel 9](https://channel9.msdn.com/series/Azure-Analysis-Services).

#### <a name="blogs"></a>Blogi
Wszystko zmienia się tak szybko. Najnowsze informacje zawsze można uzyskać w [blogu zespołu usług Analysis Services](https://blogs.msdn.microsoft.com/analysisservices/) i [blogu Azure](https://azure.microsoft.com/blog/).

#### <a name="community"></a>Społeczność
Społeczność użytkowników usług Analysis Services jest bardzo aktywna. Dołącz do konwersacji na [forum usług Azure Analysis Services](https://aka.ms/azureanalysisservicesforum).

## <a name="feedback"></a>Opinia
Masz sugestie lub propozycje nowych funkcji? Zostaw swoje komentarze w witrynie [Azure Analysis Services Feedback](https://aka.ms/azureanalysisservicesfeedback).

Masz sugestie dotyczące dokumentacji? Możesz dodać komentarz przy użyciu funkcji Livefyre dostępnej w dolnej części każdego artykułu.

## <a name="next-steps"></a>Następne kroki
Teraz, kiedy wiesz więcej na temat usług Azure Analysis Services, nadszedł czas na rozpoczęcie pracy. Dowiedz się, jak [utworzyć serwer](analysis-services-create-server.md) na platformie Azure. Gdy serwer będzie gotowy, przejdź [samouczek Adventure Works](tutorials/aas-adventure-works-tutorial.md), aby dowiedzieć się, jak utworzyć w pełni funkcjonalny model tabelaryczny i wdrożyć go na swoim serwerze.
