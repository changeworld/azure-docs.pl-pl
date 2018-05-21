---
title: Przegląd zabezpieczeń bazy danych platformy Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie bazy danych Azure funkcje zabezpieczeń.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: TomSh
ms.openlocfilehash: 44abf7a4fc24893146179b34d3357f54450decab
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="azure-database-security-overview"></a>Przegląd zabezpieczeń bazy danych platformy Azure

Bezpieczeństwa jest szczególnie ważne dla zarządzania bazami danych i zawsze było priorytet bazy danych SQL Azure. Baza danych SQL Azure obsługuje zabezpieczenia połączeń z reguły zapory i szyfrowanie połączenia. Obsługuje on uwierzytelniania za pomocą nazwy użytkownika i hasła i uwierzytelniania usługi Azure Active Directory (Azure AD), które korzysta z tożsamości zarządzanych przez usługę Azure Active Directory. Autoryzacji używa kontroli dostępu opartej na rolach.

Baza danych SQL Azure obsługuje szyfrowanie, wykonując w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji w stanie spoczynku bez konieczności wprowadzania zmian w aplikacji.

Firma Microsoft udostępnia dodatkowe sposoby szyfrowanie danych przedsiębiorstwa:

-   Szyfrowanie na poziomie komórki jest dostępna do szyfrowania określonych kolumn lub nawet komórki danych z różnymi kluczami szyfrowania.
-   Jeśli potrzebujesz sprzętowego modułu zabezpieczeń lub centralne zarządzanie hierarchii klucza szyfrowania, należy wziąć pod uwagę przy użyciu usługi Azure Key Vault z programem SQL Server w maszynie wirtualnej platformy Azure (VM).
-   Zawsze zaszyfrowane (obecnie w wersji zapoznawczej) powoduje, że szyfrowanie przezroczysty do aplikacji. Umożliwia także klientów do szyfrowania poufnych danych w aplikacjach klienckich bez udostępniania kluczy szyfrowania z bazy danych SQL.

Usługa Azure SQL Database Auditing umożliwia przedsiębiorstwom rekordu dziennika inspekcji w usłudze Azure Storage. Usługa SQL Database Auditing jest również zintegrowana z usługą Microsoft Power BI, co ułatwia wykonywanie szczegółowych raportów i analiz.

Bazy danych SQL Azure mogą być ściśle chronione do zaspokojenia najbardziej przepisami lub wymaganiami zabezpieczeń, w tym HIPAA, ISO 27001/27002 i PCI DSS poziom 1. Bieżącą listę certyfikaty zgodności zabezpieczeń jest dostępne pod adresem [witryny Microsoft Azure Trust Center](http://azure.microsoft.com/support/trust-center/services/).

W tym artykule przedstawiono podstawy Zabezpieczanie bazy danych SQL systemu Microsoft Azure dla danych strukturalnych, tabelarycznych i relacyjny. W szczególności opisano rozpoczęcie pracy z zasobami na potrzeby ochrony danych, kontrolowania dostępu i aktywnego monitorowania.

## <a name="protection-of-data"></a>Ochrona danych

Baza danych SQL ułatwia zabezpieczanie danych, zapewniając szyfrowania:

- Dla danych w ruchu za pośrednictwem [zabezpieczeń TLS (Transport Layer)](https://support.microsoft.com/kb/3135244).
- Dla danych magazynowanych za pośrednictwem [przezroczystego szyfrowania danych](http://go.microsoft.com/fwlink/?LinkId=526242).
- Dla danych za pośrednictwem [zawsze zaszyfrowane](https://msdn.microsoft.com/library/mt163865.aspx).

Można również rozważyć inne sposoby szyfrowania danych:

-   [Szyfrowanie na poziomie komórki](https://msdn.microsoft.com/library/ms179331.aspx) służące do szyfrowania określonej kolumny lub nawet poszczególnych komórek danych przy użyciu różnych kluczy szyfrowania.
-   [Usługa Azure Key Vault z programem SQL Server w maszynie Wirtualnej platformy Azure](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx), jeśli potrzebujesz sprzętowego modułu zabezpieczeń lub centralne zarządzanie hierarchii klucza szyfrowania.

### <a name="encryption-in-motion"></a>Szyfrowanie ruchu

Problem wspólne dla wszystkich aplikacji klient/serwer jest potrzebę prywatności, jak dane są przenoszone w sieciach prywatnych i publicznych. Jeśli dane przenoszenie za pośrednictwem sieci nie są szyfrowane, istnieje ryzyko, że mogą być przechwytywane i kradzieży przez nieautoryzowanych użytkowników. Gdy jest zajmujących się usługi bazy danych, upewnij się, że dane są szyfrowane między bazy danych klienta i serwera. Również upewnić się, że dane są szyfrowane między serwerami bazy danych, które komunikują się ze sobą i z aplikacji warstwy środkowej.

Jednym z problemów podczas administrowania siecią jest zabezpieczanie danych przesyłanych między aplikacjami przez niezaufaną sieć. Można użyć [protokoły TLS/SSL](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol) do uwierzytelniania serwerów i klientów, a następnie użyć jej do szyfrowania wiadomości między uwierzytelnionymi stronami.

W procesie uwierzytelniania klient TLS/SSL wysyła wiadomość do serwera TLS/SSL. Serwer odpowiada, podając informacje, które serwer musi zostać uwierzytelniony. Klient i serwer dodatkowo wymieniają się kluczami sesji, a dialog uwierzytelniania kończy się. Po zakończeniu uwierzytelniania komunikacja zabezpieczona protokołem SSL można rozpocząć między serwerem a klientem przy użyciu kluczy szyfrowania symetrycznego, które są ustalane w procesie uwierzytelniania.

Wszystkie połączenia z bazą danych SQL Azure Wymaganie szyfrowania (TLS/SSL) w zawsze danych "w trakcie przesyłania" do i z bazy danych. Baza danych SQL używa protokołu TLS/SSL do uwierzytelniania serwerów i klientów, a następnie użyć go do szyfrowania wiadomości między uwierzytelnionymi stronami. 

W parametrach połączenia aplikacji należy określić parametry do szyfrowania połączenia i nie ufa certyfikatowi serwera. (Jest to realizowane automatycznie kopiowania parametrów połączenia z portalu Azure.) W przeciwnym razie połączenie nie będzie zweryfikować tożsamość serwera i będzie narażony na ataki "man-in--middle". Sterownika ADO.NET, na przykład te parametry ciągu połączenia są `Encrypt=True` i `TrustServerCertificate=False`.

### <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku
Można wykonać kilka kroków, aby ułatwić zabezpieczanie bazy danych. Na przykład projektowania bezpieczny system, szyfrowania poufnych zasobów i kompilacji zapory wokół serwerów baz danych. Jednak w scenariuszu, w którym kradzieży nośnik fizyczny (takich jak dyski lub taśmy kopii zapasowej), złośliwego strona po prostu przywrócić lub dołączyć bazy danych i przeglądać dane.

Jest jedno rozwiązanie do szyfrowania poufnych danych w bazie danych i ochrony kluczy, które są używane do szyfrowania danych przy użyciu certyfikatu. To rozwiązanie zapobiega osobom niemającym kluczy przy użyciu danych, ale muszą być planowane tego rodzaju ochrony.

Aby rozwiązać ten problem, SQL Server i obsługi bazy danych SQL [przezroczystego szyfrowania danych](https://docs.microsoft.com/sql/relational-databases/securityrecryption/transparent-data-encryption-tde). Przezroczystego szyfrowania danych szyfruje programu SQL Server i bazy danych SQL plików danych, znane jako szyfrowanie danych magazynowanych.

Przezroczystego szyfrowania danych chroni przed zagrożeniem złośliwych działań. Wykonuje w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji w stanie spoczynku bez konieczności wprowadzania zmian w aplikacji.  

Przezroczystego szyfrowania danych szyfruje magazyn całej bazy danych przy użyciu klucza symetrycznego o nazwie klucza szyfrowania bazy danych. W bazie danych SQL klucz szyfrowania bazy danych jest chroniony za pomocą certyfikatu wbudowanego serwera. Certyfikat serwera wbudowanych jest unikatowy dla każdego serwera bazy danych SQL.

Jeśli baza danych jest w relacji replikacji geograficznej — warstwa DR, jest chroniony przez inny klucz na każdym serwerze. Jeśli dwie bazy danych są połączone z tym samym serwerze, mają ten sam certyfikat wbudowanych. Microsoft automatycznie przełącza tych certyfikatów, co najmniej co 90 dni. 

Aby uzyskać więcej informacji, zobacz [przezroczystego szyfrowania danych](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde).

### <a name="encryption-in-use-client"></a>Szyfrowanie przy użyciu (klienta)

Większość naruszeń danych obejmują kradzież krytyczne dane, takie jak numer karty kredytowej lub dane osobowe. Bazy danych może być troves Skarb informacji poufnych. Zawierają danych osobowych (na przykład numery identyfikacyjne national), informacje poufne konkurencyjnych i własności intelektualnej klientów. Utraty lub kradzieży danych, szczególnie danych klienta, może spowodować uszkodzenie marki, wadą konkurencyjnych i poważne kar — nawet spraw sądowych.

![Funkcja zawsze zaszyfrowane, przedstawione jako blokady i klucz](./media/azure-databse-security-overview/azure-database-fig1.png)

[Zawsze zaszyfrowane](https://msdn.microsoft.com/library/mt163865.aspx) jest funkcją zaprojektowane w celu ochrony poufnych danych przechowywanych w bazach danych usługi Azure SQL Database lub SQL Server. Zawsze zaszyfrowane umożliwia klientom do szyfrowania poufnych danych w aplikacjach klienckich i nigdy nie podawaj kluczy szyfrowania z aparatem bazy danych (bazy danych SQL lub SQL Server).

Zawsze zaszyfrowane zapewnia rozdzielenie osoby własnych danych (i można go wyświetlić) i osoby zarządzania danymi (ale powinien nie mają dostępu). Pomaga zapewnić, że lokalnej bazy danych, Administratorzy, Operatorzy chmury bazy danych lub innych wysokimi uprawnieniami, ale nieautoryzowani użytkownicy nie może uzyskać dostępu do zaszyfrowanych danych.

Ponadto zawsze zaszyfrowane sprawia, że szyfrowanie przezroczysty do aplikacji. Sterownik obsługą zawsze zaszyfrowane jest zainstalowany na komputerze klienckim, dzięki czemu można automatycznie szyfrowania i odszyfrowywania danych poufnych w aplikacji klienta. Sterownik szyfruje dane poufne kolumn przed przekazaniem ich z aparatem bazy danych. Sterownika automatycznie ponownie zapisuje zapytania, tak aby zostały zachowane semantyki do aplikacji. Podobnie sterownik niewidocznie odszyfrowuje dane przechowywane w kolumnach bazy danych zaszyfrowanych, zawarte w wynikach zapytania.

## <a name="access-control"></a>Kontrola dostępu
Aby zapewnić ochronę, baza danych SQL kontroluje dostęp przy użyciu:

- Reguły zapory, ograniczające łączności za pomocą adresu IP.
- Mechanizmy uwierzytelniania wymagające użytkowników w celu potwierdzenia ich tożsamości.
- Mechanizmów autoryzacji, które Ogranicz użytkowników do określonych akcji i dane.

### <a name="database-access"></a>Dostęp do bazy danych

Ochrona danych rozpoczyna się od kontrolowania dostępu do danych. Centrum danych, który jest hostem danych zarządza dostępem fizycznym. Można skonfigurować zapory do zarządzania zabezpieczeniami w warstwie sieci. Umożliwia również kontrolę dostępu, konfigurując nazw logowania dla uwierzytelniania i definiowanie uprawnień dla ról serwera i bazy danych.

#### <a name="firewall-and-firewall-rules"></a>Zapora i reguły zapory

[Baza danych SQL Azure](https://azure.microsoft.com/services/sql-database/) to usługa relacyjnej bazy danych platformy Azure i innych aplikacji internetowych. Aby chronić dane, zapora uniemożliwia wszelki dostęp do serwera bazy danych do momentu określenia komputerów, które mają uprawnienia. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania. Aby uzyskać więcej informacji, zobacz [Omówienie reguł zapory usługi Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

Usługa bazy danych SQL Azure jest dostępna tylko za pośrednictwem portu TCP 1433. Aby uzyskać dostęp do bazy danych SQL z komputera, sprawdź, czy zapory komputera klienta pozwalają wychodzących komunikacji TCP na porcie TCP 1433. Jeśli połączenia przychodzące, nie są wymagane przez inne aplikacje, należy je zablokować na porcie TCP 1433.

#### <a name="authentication"></a>Authentication

Uwierzytelnianie to sposób potwierdzenia tożsamości podczas nawiązywania połączenia z bazą danych. Usługa SQL Database obsługuje dwa typy uwierzytelniania:

-   **Uwierzytelnianie programu SQL Server**: konto logowania pojedynczego jest tworzony po utworzeniu logicznej wystąpienie serwera SQL o nazwie konto subskrybenta bazy danych SQL. To konto łączy się przy użyciu [uwierzytelniania programu SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview) (nazwa użytkownika i hasło). To konto jest administratorem wystąpienia serwera logicznego i wszystkich baz danych użytkownika dołączonych do tego wystąpienia. Uprawnień konta subskrybenta nie mogą być ograniczone. Może istnieć tylko jedno z tych kont.
-   **Usługa Azure Active Directory authentication**: [uwierzytelniania usługi Azure AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) mechanizm nawiązywać połączenia z bazą danych SQL Azure i usługi Azure SQL Data Warehouse przy użyciu tożsamości w usłudze Azure AD. Służy on do centralne zarządzanie tożsamościami użytkowników bazy danych.

![Azure AD authentication z bazy danych SQL](./media/azure-databse-security-overview/azure-database-fig2.png)

 Zalety usługi Azure AD authentication obejmują:
  - Zapewnia zamiast uwierzytelniania programu SQL Server.
  - Pomaga zatrzymać rozprzestrzenianie tożsamości użytkowników na serwerach bazy danych, a umożliwia obrotu hasła w jednym miejscu.
  - Uprawnienia dotyczące bazy danych można zarządzać za pomocą grup zewnętrzne (Azure AD).
  - Można go wyeliminować zapisywania haseł przez włączenie zintegrowanego uwierzytelniania systemu Windows i innych metod uwierzytelniania, który obsługuje usługę Azure AD.

#### <a name="authorization"></a>Autoryzacja
[Autoryzacji](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins) odwołuje się do użytkownika czynności w ramach bazy danych Azure SQL. Jest kontrolowana przez konto użytkownika w bazie danych [członkostwo w rolach](https://msdn.microsoft.com/library/ms189121) i [uprawnienia na poziomie obiektu](https://msdn.microsoft.com/library/ms191291.aspx). Autoryzacja jest proces określania zabezpieczanego podmiot zabezpieczeń mogą uzyskiwać dostęp do zasobów i jakie operacje są dozwolone dla tych zasobów.

### <a name="application-access"></a>Dostęp do aplikacji

#### <a name="dynamic-data-masking"></a>Dynamiczne maskowanie danych
Przedstawiciel usługi roboczego wywołanie może identyfikować obiekty wywołujące przez kilka cyfr numeru karty kredytowej lub numer ubezpieczenia społecznego. Jednak te elementy danych powinien nie pełni widoczne dla działu obsługi.

Można zdefiniować reguły maskowania maski wszystkie oprócz cztery ostatnie cyfry numer ubezpieczenia społecznego lub numer karty kredytowej w zestawie wyników wszelkie zapytania.

![Maskowania na kilku przesyłanych między SQL bazy danych i biznesowych aplikacji](./media/azure-databse-security-overview/azure-database-fig3.png)

Inny przykład można zdefiniować maskę odpowiednie dane do ochrony informacji umożliwiających identyfikację użytkownika. Deweloper może następnie zapytania środowisk produkcyjnych w celu rozwiązywania problemów bez naruszania przepisy dotyczące zgodności.

Funkcja [dynamicznego maskowania danych w bazie danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) ogranicza ujawnianie poufnych danych przez ich maskowanie dla nieuprzywilejowanych użytkowników. Maskowanie danych dynamicznych jest obsługiwana dla wersji V12 bazy danych SQL Azure.

[Maskowanie danych dynamicznych](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) pomaga zapobiec nieautoryzowanemu dostępowi do ważnych danych dzięki umożliwieniu wyznaczyć ilość poufnych danych w celu odkrycia przy minimalnym wpływie na warstwie aplikacji. Jest to funkcja zabezpieczeń oparta na zasadach, która ukrywa dane poufne w zestawie wyników zapytania w wyznaczonych polach bazy danych, przy czym dane w bazie danych pozostają bez zmian.


> [!Note]
> Maskowanie danych dynamicznych można skonfigurować przez administratora bazy danych Azure, administrator serwera lub role zabezpieczeń ds.

#### <a name="row-level-security"></a>Zabezpieczenia na poziomie wierszy
Innego typowe wymagania dotyczące zabezpieczeń dla wielodostępnych baz danych jest [zabezpieczenia na poziomie wiersza](https://msdn.microsoft.com/library/dn765131.aspx). Ta funkcja służy do kontrolowania dostępu do wierszy w tabeli bazy danych na podstawie charakterystyk użytkownika, który wykonuje zapytania. (Przykład właściwości są grupy członkostwa i kontekst wykonania).

![Zabezpieczenia na poziomie wiersza Umożliwianie użytkownikowi dostęp do wierszy w tabeli za pośrednictwem aplikacji klienta](./media/azure-databse-security-overview/azure-database-fig4.png)

Logika ograniczenie dostępu jest znajduje się w warstwie bazy danych zamiast od danych w innej warstwie aplikacji. System bazy danych ma zastosowanie ograniczenia dostępu za każdym razem, gdy nastąpiła by dostęp do danych z dowolnej wersji. W efekcie systemu zabezpieczeń bardziej niezawodne i niezawodne zmniejszając powierzchni systemu zabezpieczeń.

Zabezpieczenia na poziomie wiersza wprowadza kontroli dostępu na podstawie predykatu. Elastyczne, scentralizowane oceny, który można wziąć pod uwagę metadanych lub inne kryteria, które administrator określa odpowiednio zawiera funkcje. Predykat jest używany jako kryterium ustalenie, czy użytkownik ma odpowiedni dostęp do danych na podstawie atrybutów użytkownika. Kontrola dostępu oparta na etykiecie można wdrożyć przy użyciu kontroli dostępu na podstawie predykatu.

## <a name="proactive-monitoring"></a>Aktywne monitorowanie
Baza danych SQL ułatwia zabezpieczanie danych, zapewniając *inspekcji* i *wykrywanie zagrożeń* możliwości.

### <a name="auditing"></a>Inspekcja
[Inspekcja bazy danych SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) zwiększa możliwość wgląd w zdarzenia i zmiany wprowadzone w bazie danych. Przykłady są aktualizacje i zapytań dotyczących danych.

SQL Database auditing śledzi zdarzenia bazy danych i zapisuje je inspekcji logowania na koncie magazynu Azure. Inspekcja pomaga zachować zgodność z przepisami, analizować aktywność bazy danych oraz uzyskać wgląd w odchylenia i anomalie, które mogą wskazywać na problemy biznesowe lub podejrzane naruszenia zabezpieczeń. Inspekcja umożliwia i ułatwia przestrzeganie standardów zgodności, ale nie gwarantuje się zgodności.

Możesz użyć inspekcji bazy danych SQL:

-   **Zachowaj** dziennik inspekcji wybranych zdarzeń. Można zdefiniować kategorie działań przeprowadzać inspekcję bazy danych.
-   **Raport** na działanie bazy danych. Wstępnie skonfigurowane raporty i pulpit nawigacyjny umożliwia szybkie rozpoczęcie pracy z aktywności i raportowanie zdarzeń.
-   **Analizowanie** raportów. Można znaleźć podejrzane zdarzenia, nietypowe działania i trendów.

Istnieją dwie metody inspekcji:

-   **Inspekcja obiektów blob**: dzienniki są zapisywane w magazynie obiektów Blob Azure. Jest to metoda inspekcji nowsza. On zapewnia większą wydajność, obsługuje wyższy poziom szczegółowości na poziomie obiektu inspekcji i jest bardziej ekonomiczne.
-   **Inspekcja tabeli**: dzienniki są zapisywane do magazynu tabel Azure.

### <a name="threat-detection"></a>Wykrywanie zagrożeń
[Wykrywanie zagrożeń bazy danych SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) wykrycia podejrzanych działań, które wskazują możliwe zagrożenia bezpieczeństwa. Wykrywanie zagrożeń służy do reagowania na podejrzane zdarzenia w bazie danych, takich jak iniekcji SQL, w jakiej występują. Zawiera alerty i zezwala na korzystanie z bazy danych SQL Azure inspekcji, aby eksplorować podejrzane zdarzenia.

![Wykrywanie zagrożeń dla bazy danych SQL i aplikacji sieci web, osoba atakująca zewnętrznych i złośliwe oprogramowanie](./media/azure-databse-security-overview/azure-database-fig5.jpg)

Na przykład iniekcja kodu SQL jest jednym z typowych problemów z zabezpieczeniami dla aplikacji sieci web. Służy do przeprowadzania ataków opartych na danych aplikacji. Osoby atakujące wykorzystać luki w zabezpieczeniach aplikacji do dodania złośliwego instrukcje SQL do pola wejścia aplikacji, naruszenia lub modyfikowanie danych w bazie danych.

Funkcjonariusze lub innych administratorów wyznaczonych można uzyskać natychmiastowego wysłania powiadomienia o bazy danych podejrzanych działaniach miarę ich występowania. Każde powiadomienie zawiera szczegółowe informacje o podejrzanych działaniach i zaleca jak do dalszego zbadania i ograniczyć zagrożenie.        

## <a name="centralized-security-management"></a>Zarządzanie zabezpieczeniami scentralizowane

Usługa [Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) pomaga w zapobieganiu zagrożeniom, wykrywaniu ich i reagowaniu na nie. Zapewnia zabezpieczenia zintegrowane monitorowanie i zarządzanie zasadami subskrypcji platformy Azure. Pomaga wykrywać zagrożenia, które mogłyby w przeciwnym razie pozostać niezauważone, a jego współpracuje z szerokim ekosystemem rozwiązań zabezpieczających.

[Centrum zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-sql-database) pomaga chronić dane w bazie danych SQL, zapewniając wgląd w zabezpieczenia serwerów i baz danych. Centrum zabezpieczeń można:

-   Definiowanie zasad szyfrowania bazy danych SQL i inspekcji.
-   Monitorowanie zabezpieczeń zasobów bazy danych SQL za pośrednictwem wszystkich subskrypcji.
-   Szybkie identyfikowanie i Korygowanie problemów z zabezpieczeniami.
-   Integracja alertów z [wykrywanie zagrożeń bazy danych SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).

Centrum zabezpieczeń obsługuje dostęp opartej na rolach.

## <a name="azure-marketplace"></a>Azure Marketplace

Portal Azure Marketplace to rynek aplikacji i usług w trybie online, który umożliwia początkującym i niezależnym dostawcom oprogramowania oferowanie swoich rozwiązań klientom platformy Azure na całym świecie.
W portalu Azure Marketplace łączy ekosystemami partnerów firmy Microsoft Azure w to ujednolicona platforma lepsze służą klientom i partnerom. Możesz [przeprowadzić wyszukiwania](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1) Aby wyświetlić produkty zabezpieczeń bazy danych jest dostępne w portalu Azure Marketplace.

## <a name="next-steps"></a>Kolejne kroki

- [Zabezpieczenia bazy danych Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial)
- [Usługa Centrum zabezpieczeń Azure i bazy danych SQL Azure](https://docs.microsoft.com/azure/security-center/security-center-sql-database)
- [Wykrywanie zagrożeń bazy danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)
- [Poprawianie wydajności bazy danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-performance-tutorial)
