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
ms.openlocfilehash: b5e9e412fddf727237b008e37238fa2c547694de
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="azure-database-security-overview"></a>Przegląd zabezpieczeń bazy danych platformy Azure

Zabezpieczeń jest szczególnie ważne w przypadku zarządzania bazami danych i zawsze było priorytet bazy danych SQL Azure. Baza danych SQL Azure obsługuje zabezpieczenia połączeń z reguły zapory i szyfrowanie połączenia. Obsługuje on uwierzytelniania za pomocą nazwy użytkownika i hasła i Azure uwierzytelnianie usługi Active Directory, który używa tożsamości zarządzanych przez usługę Azure Active Directory. Autoryzacji używa kontroli dostępu opartej na rolach.

Baza danych SQL Azure obsługuje szyfrowanie, wykonując w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji w stanie spoczynku bez konieczności wprowadzania zmian w aplikacji.

Firma Microsoft udostępnia dodatkowe sposoby szyfrowanie danych przedsiębiorstwa:

-   Szyfrowanie na poziomie komórki szyfrowanie kolumny lub nawet komórki danych z różnymi kluczami szyfrowania.
-   Jeśli potrzebujesz sprzętowego modułu zabezpieczeń lub centralne zarządzanie hierarchii klucza szyfrowania, należy rozważyć użycie usługi Azure Key Vault z programem SQL Server w maszynie Wirtualnej platformy Azure.
-   Zawsze zaszyfrowane (obecnie w wersji zapoznawczej) powoduje, że szyfrowanie przezroczysty do aplikacji i umożliwia klientom do szyfrowania poufnych danych w aplikacjach klienckich bez udostępniania kluczy szyfrowania z bazy danych SQL.

Usługa Azure SQL Database Auditing pozwala przedsiębiorstwom rekordów zdarzeń inspekcji logowania usługi Azure Storage. Usługa SQL Database Auditing jest również zintegrowana z usługą Microsoft Power BI, co ułatwia wykonywanie szczegółowych raportów i analiz.

 Baz danych SQL Azure mogą być ściśle chronione do zaspokojenia najbardziej przepisami lub wymaganiami zabezpieczeń, w tym HIPAA, ISO 27001/27002 i PCI DSS poziom 1, między innymi. Bieżącą listę certyfikaty zgodności zabezpieczeń jest dostępne pod adresem [witryny Microsoft Azure Trust Center](http://azure.microsoft.com/support/trust-center/services/).

W tym artykule przedstawiono podstawowe informacje dotyczące zabezpieczania Structured, tabelarycznych i danych relacyjnych baz danych Microsoft Azure. W szczególności opisano rozpoczęcie pracy z zasobami na potrzeby ochrony danych, kontrolowania dostępu i aktywnego monitorowania.

W tym artykule Omówienie zabezpieczeń bazy danych Azure skupia się wokół następujących obszarów:

-   Ochrona danych
-   Kontrola dostępu
-   Aktywne monitorowanie
-   Zarządzanie zabezpieczeniami scentralizowane
-   Rynek platformy Azure

## <a name="protect-data"></a>Ochrona danych

Baza danych SQL zabezpiecza dane przez zapewnienie szyfrowanie danych w ruchu przy użyciu [Transport Layer Security](https://support.microsoft.com/kb/3135244)dla rest danych przy użyciu [przezroczystego szyfrowania danych](http://go.microsoft.com/fwlink/?LinkId=526242)i danych za pomocą użyj [ Zawsze zaszyfrowane](https://msdn.microsoft.com/library/mt163865.aspx).

W tej sekcji są omawiane:

-   Szyfrowanie ruchu
-   Szyfrowanie w spoczynku
-   Szyfrowanie przy użyciu (klienta)

Można również rozważyć inne sposoby szyfrowania danych:

-   [Szyfrowanie na poziomie komórki](https://msdn.microsoft.com/library/ms179331.aspx) służące do szyfrowania określonej kolumny lub nawet poszczególnych komórek danych przy użyciu różnych kluczy szyfrowania.
-   Jeśli potrzebujesz sprzętowego modułu zabezpieczeń lub centralnego zarządzania hierarchią kluczy szyfrowania, rozważ użycie [funkcji Azure Key Vault w usłudze SQL Server w maszynie wirtualnej Azure](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).

### <a name="encryption-in-motion"></a>Szyfrowanie ruchu

Problem wspólne dla wszystkich aplikacji klient/serwer jest potrzebę prywatności, jak dane są przenoszone w sieciach prywatnych i publicznych. Jeśli dane przenoszenie za pośrednictwem sieci nie są szyfrowane, istnieje ryzyko, że mogą być przechwytywane i kradzieży przez nieautoryzowanych użytkowników. Podczas pracy z usługami bazy danych, musisz upewnij się, że dane są szyfrowane między bazy danych klienta i serwera, jak również między serwerami bazy danych, które komunikują się ze sobą i z aplikacji warstwy środkowej.

Jednym z problemów podczas administrowania siecią jest zabezpieczanie danych przesyłanych między aplikacjami przez niezaufaną sieć. Można użyć [protokoły TLS/SSL](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol) uwierzytelniania serwerów i klientów, a następnie użyć go do szyfrowania wiadomości między uwierzytelnionymi stronami.

W procesie uwierzytelniania klient TLS/SSL wysyła wiadomość do serwera TLS/SSL, a serwer odpowiada, informacje, czy serwer musi zostać uwierzytelniony. Klient i serwer dodatkowo wymieniają się kluczami sesji, a dialog uwierzytelniania kończy się. Po zakończeniu uwierzytelniania między serwerem a klientem przy użyciu kluczy szyfrowania symetrycznego, które są ustalane w procesie uwierzytelniania może rozpocząć komunikacja zabezpieczona protokołem SSL.

Wszystkie połączenia z usługą Azure SQL Database wymagają szyfrowania (SSL/TLS) podczas całego okresu, w którym dane są przesyłane do i z bazy danych. Usługi SQL Azure używa protokołu TLS/SSL do uwierzytelniania serwerów i klientów, a następnie użyć go do szyfrowania wiadomości między uwierzytelnionymi stronami. W parametrach połączenia aplikacji, należy określić parametry do szyfrowania połączenia oraz aby nie ufać certyfikatu serwera (odbywa się automatycznie po skopiowaniu parametrów połączenia z portalu Azure), w przeciwnym razie połączenie nie zweryfikuje tożsamość serwera i będzie narażony na ataki "man-in--middle". Na przykład sterownika ADO.NET te parametry ciągu połączenia są Szyfruj = True i TrustServerCertificate = False.

### <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku
Można wykonać kilka kroków, aby ułatwić zabezpieczanie bazy danych, takich jak projektowania bezpieczny system, szyfrowania poufnych zasobów i konfigurowania zapory wokół serwerów baz danych. Jednak w scenariuszu, w którym kradzieży nośnik fizyczny (takich jak dyski lub taśmy kopii zapasowej), strony złośliwych można po prostu przywrócić lub dołączyć bazy danych i przeglądać dane.

Jedno rozwiązanie jest do szyfrowania poufnych danych w bazie danych i zapewnia ochronę kluczy, które są używane do szyfrowania danych przy użyciu certyfikatu. Zapobiega to osobom niemającym kluczy przy użyciu danych, ale muszą być planowane tego rodzaju ochrony.

Aby rozwiązać ten problem, SQL Server i Azure SQL obsługuje [funkcji przezroczystego szyfrowania danych (TDE)](https://docs.microsoft.com/sql/relational-databases/securityrecryption/transparent-data-encryption-tde). Funkcji TDE szyfruje programu SQL Server i bazy danych SQL Azure plików danych, znane jako szyfrowanie danych magazynowanych.

Azure SQL Database przezroczystego szyfrowania danych chroni przed zagrożeniem złośliwych działań, wykonując w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych, i pliki dziennika transakcji w stanie spoczynku bez konieczności wprowadzania zmian w aplikacja.  

Funkcji TDE szyfruje magazyn całej bazy danych przy użyciu klucza symetrycznego o nazwie klucza szyfrowania bazy danych. W bazie danych SQL klucz szyfrowania bazy danych jest chroniony za pomocą certyfikatu wbudowanego serwera. Certyfikat serwera wbudowanych jest unikatowy dla każdego serwera bazy danych SQL.

Jeśli baza danych jest w relacji replikacji geograficznej GeoDR, jest chroniony przez inny klucz na każdym serwerze. Jeśli dwie bazy danych są połączone z tym samym serwerze, mają ten sam certyfikat wbudowanych. Microsoft automatycznie przełącza tych certyfikatów, co najmniej co 90 dni. Ogólny opis funkcji TDE, zobacz [funkcji przezroczystego szyfrowania danych (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde).

### <a name="encryption-in-use-client"></a>Szyfrowanie przy użyciu (klienta)

Większość naruszeń danych obejmują kradzież krytyczne dane, takie jak numer karty kredytowej lub dane osobowe. Bazy danych może być troves Skarb informacji poufnych. Zawierają dane osobowe, konkurencyjnych informacji poufnych i własności intelektualnej klientów. Utraty lub kradzieży danych, szczególnie danych klienta, może spowodować uszkodzenie marki, wadą konkurencyjnych i poważne kar — nawet spraw sądowych.

![Zawsze szyfrowane](./media/azure-databse-security-overview/azure-database-fig1.png)

[Zawsze zaszyfrowane](https://msdn.microsoft.com/library/mt163865.aspx) jest funkcją zaprojektowane w celu ochrony poufnych danych, takich jak numery kart kredytowych lub numerów identyfikacyjnych national (na przykład USA numerów ubezpieczenia społecznego), przechowywane w bazach danych usługi Azure SQL Database lub SQL Server. Zawsze zaszyfrowane umożliwia klientom do szyfrowania poufnych danych w aplikacjach klienckich i nigdy nie podawaj kluczy szyfrowania z aparatem bazy danych (bazy danych SQL lub SQL Server).

Zawsze zaszyfrowane zapewnia oddzielenie tych, którzy należą dane (i mogły go wyświetlać) oraz tych, którzy zarządzania danymi (ale powinien nie mają dostępu). Zapewniając lokalnych administratorów bazy danych w chmurze operatory bazy danych lub innych wysokimi uprawnieniami, ale nieautoryzowani użytkownicy nie dostępu do zaszyfrowanych danych.

Ponadto zawsze zaszyfrowane sprawia, że szyfrowanie przezroczysty do aplikacji. Zawsze zaszyfrowane włączone sterownik zainstalowany na komputerze klienckim, dzięki czemu można automatycznie szyfrowania i odszyfrowywania danych poufnych w aplikacji klienta. Sterownik szyfruje dane poufne kolumn przed przekazaniem ich z aparatem bazy danych i automatycznie ponownie zapisuje zapytania, tak aby zostały zachowane semantyki do aplikacji. Podobnie sterownik niewidocznie odszyfrowuje dane przechowywane w kolumnach bazy danych zaszyfrowanych, zawarte w wynikach zapytania.

## <a name="access-control"></a>Kontrola dostępu
Aby zapewnić bezpieczeństwo, usługa SQL Database kontroluje dostęp za pomocą reguł zapory, ograniczając adresy IP dla połączeń, stosując mechanizmy uwierzytelniania wymagające potwierdzenia tożsamości przez użytkowników oraz mechanizmy autoryzacji ograniczające użytkowników do określonych działań i danych.

### <a name="database-access"></a>Dostęp do bazy danych

Ochrona danych rozpoczyna się od kontrolowania dostępu do danych. Centrum danych hostującego danych zarządza fizyczny dostęp, mimo że możesz skonfigurować zapory do zarządzania zabezpieczeniami w warstwie sieci. Umożliwia również kontrolę dostępu, konfigurując nazw logowania dla uwierzytelniania i definiowanie uprawnień dla ról serwera i bazy danych.

W tej sekcji są omawiane:

-   Zapora i reguły zapory
-   Authentication
-   Autoryzacja

#### <a name="firewall-and-firewall-rules"></a>Zapora i reguły zapory

Usługa Microsoft Azure SQL Database udostępnia usługę relacyjnej bazy danych dla platformy Azure i innych aplikacji internetowych. Aby chronić dane, zapora uniemożliwia wszelki dostęp do serwera bazy danych do momentu określenia komputerów, które mają uprawnienia. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania. Aby uzyskać więcej informacji, zobacz [Omówienie reguł zapory usługi Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

[Bazy danych SQL Azure](https://azure.microsoft.com/services/sql-database/) usługi są dostępne tylko w porcie TCP 1433. Aby uzyskać dostęp do usługi SQL Database z komputera, upewnij się, że zapora komputera klienta umożliwia wychodzący ruch TCP na porcie TCP 1433. Należy zablokować połączenia przychodzące na porcie TCP 1433, jeśli nie są one wymagane przez inne aplikacje.

#### <a name="authentication"></a>Authentication

Uwierzytelnianie w usłudze SQL Database to sposób potwierdzenia tożsamości podczas nawiązywania połączenia z bazą danych. Usługa SQL Database obsługuje dwa typy uwierzytelniania:

-   **Uwierzytelnianie SQL:** konta pojedynczego logowania jest tworzony po utworzeniu logicznej wystąpienie serwera SQL o nazwie konto subskrybenta bazy danych SQL. To konto łączy za pomocą [uwierzytelniania programu SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview) (nazwa użytkownika i hasło). To konto jest administratorem wystąpienia serwera logicznego i wszystkich baz danych użytkownika dołączonych do tego wystąpienia. Uprawnień konta subskrybenta nie można ograniczać. Może istnieć tylko jedno z tych kont.
-   **Azure uwierzytelnianie usługi Active Directory:** [uwierzytelniania usługi Azure Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) mechanizm nawiązywać połączenia z Microsoft Azure SQL Database i SQL Data Warehouse przy użyciu tożsamości w usłudze Azure Active Directory ( Azure AD). Umożliwia to centralne zarządzanie tożsamościami użytkowników bazy danych.

![Authentication](./media/azure-databse-security-overview/azure-database-fig2.png)

 Zalety uwierzytelniania usługi Azure Active Directory obejmują:
  - Zapewnia zamiast uwierzytelniania programu SQL Server.
  - Również pomaga zatrzymać rozprzestrzenianie tożsamości użytkowników na serwerach bazy danych i umożliwia obrotu hasła w jednym miejscu.
  - Możesz zarządzać uprawnieniami bazy danych przy użyciu zewnętrznego grup (Azure Active Directory).
  - Można go wyeliminować zapisywania haseł przez włączenie zintegrowanego uwierzytelniania systemu Windows i innych metod uwierzytelniania obsługiwanych przez usługę Azure Active Directory.

#### <a name="authorization"></a>Autoryzacja
[Autoryzacji](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins) odwołuje się do co użytkownika mogą wykonywać w bazie danych SQL Azure i jest to kontrolowane przez konto użytkownika w bazie danych [członkostwo w rolach](https://msdn.microsoft.com/library/ms189121) i [uprawnienia na poziomie obiektu](https://msdn.microsoft.com/library/ms191291.aspx). Autoryzacja jest proces określania zabezpieczanego podmiot zabezpieczeń mogą uzyskiwać dostęp do zasobów i jakie operacje są dozwolone dla tych zasobów.

### <a name="application-access"></a>Dostęp do aplikacji

W tej sekcji są omawiane:

-   Dynamiczne maskowanie danych
-   Zabezpieczenia na poziomie wiersza

#### <a name="dynamic-data-masking"></a>Dynamiczne maskowanie danych
Z przedstawicielem w gnieździe wywołanie może identyfikować obiekty wywołujące przez kilka cyfr numeru karty kredytowej lub numer ubezpieczenia społecznego, ale te elementy danych powinien nie pełni widoczne dla działu obsługi.

Czy wszystkie maski, ale cztery ostatnie cyfry numer ubezpieczenia społecznego lub numer karty kredytowej, w wyniku ustawiono wszelkie zapytania można zdefiniować reguł maskowania.

![Dynamiczne maskowanie danych](./media/azure-databse-security-overview/azure-database-fig3.png)

Inny przykład można zdefiniować maskę odpowiednie dane do ochrony danych osobowych (dane osobowe), dzięki czemu deweloper może wysyłać zapytania środowisk produkcyjnych w celu rozwiązywania problemów bez naruszania przepisy dotyczące zgodności.

[SQL bazy danych dynamicznego maskowania danych](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) ogranicza ujawnienie danych poufnych przez maskowania go użytkownikom bez uprawnień. Maskowanie danych dynamicznych jest obsługiwana dla wersji V12 bazy danych SQL Azure.

[Maskowanie danych dynamicznych](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) pomaga zapobiec nieautoryzowanemu dostępowi do ważnych danych dzięki umożliwieniu wyznaczyć ilość poufnych danych w celu odkrycia przy minimalnym wpływie na warstwie aplikacji. Jest to funkcja zabezpieczeń oparta na zasadach, która ukrywa dane poufne w zestawie wyników zapytania w wyznaczonych polach bazy danych, przy czym dane w bazie danych pozostają bez zmian.


> [!Note]
> Maskowanie danych dynamicznych można skonfigurować przez administratora bazy danych Azure, administrator serwera lub role zabezpieczeń ds.

#### <a name="row-level-security"></a>Zabezpieczenia na poziomie wiersza
Innego typowe wymagania dotyczące zabezpieczeń dla wielodostępnych baz danych jest [zabezpieczenia na poziomie wiersza](https://msdn.microsoft.com/library/dn765131.aspx). Ta funkcja umożliwia kontrolowanie dostępu do wierszy w tabeli bazy danych na podstawie charakterystyk użytkownika wykonywania zapytania (np. grupy członkostwa lub wykonywania context).

![-Zabezpieczenia na poziomie wiersza](./media/azure-databse-security-overview/azure-database-fig4.png)

Logika ograniczenie dostępu jest znajduje się w warstwie bazy danych zamiast od danych w innej warstwie aplikacji. System bazy danych ma zastosowanie ograniczenia dostępu za każdym razem, gdy nastąpiła by dostęp do danych z dowolnej wersji. W efekcie systemu zabezpieczeń bardziej niezawodne i niezawodne zmniejszając powierzchni systemu zabezpieczeń.

Zabezpieczenia na poziomie wiersza wprowadza kontrola dostępu oparta na predykatu. Elastyczne, scentralizowane, na podstawie predykatu oceny, który można wziąć pod uwagę metadanych lub inne kryteria, które administrator określa odpowiednio zawiera funkcje. Predykat jest używany jako kryterium ustalenie, czy użytkownik ma odpowiedni dostęp do danych na podstawie atrybutów użytkownika. Kontrola dostępu oparta na etykiecie można zaimplementować przy użyciu kontroli dostępu na podstawie predykatu.

## <a name="proactive-monitoring"></a>Aktywne monitorowanie
Baza danych SQL zabezpiecza dane, zapewniając **inspekcji** i **wykrywanie zagrożeń** możliwości.

### <a name="auditing"></a>Inspekcja
SQL Database Auditing zwiększa możliwość wgląd w zdarzenia i zmiany wprowadzone w bazie danych, w tym aktualizacje i zapytań dotyczących danych.

[Usługa Azure SQL Database Auditing](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) śledzi zdarzenia bazy danych i zapisuje je inspekcji logowania na koncie magazynu Azure. Inspekcja pomaga zachować zgodność z przepisami, analizować aktywność bazy danych oraz uzyskać wgląd w odchylenia i anomalie, które mogą oznaczać problemy biznesowe lub podejrzane naruszenia zabezpieczeń. Inspekcja umożliwia i ułatwia przestrzeganie standardów zgodności, ale nie gwarantuje się zgodności.

Inspekcja bazy danych SQL umożliwia:

-   **Zachowaj** dziennik inspekcji wybranych zdarzeń. Można zdefiniować kategorie działań przeprowadzać inspekcję bazy danych.
-   **Raport** na działanie bazy danych. Wstępnie skonfigurowane raporty i pulpit nawigacyjny umożliwia szybkie rozpoczęcie pracy z aktywności i raportowanie zdarzeń.
-   **Analizowanie** raportów. Można znaleźć podejrzane zdarzenia, nietypowe działania i trendów.

Istnieją dwie metody inspekcji:

-   **Inspekcja obiektów blob** — dzienniki są zapisywane do magazynu obiektów Blob Azure. Jest to metoda inspekcji nowsza, który zapewnia większą wydajność i obsługuje wyższy poziom szczegółowości na poziomie obiektu inspekcji oraz niższy koszt.
-   **Inspekcja tabeli** — dzienniki są zapisywane do magazynu tabel platformy Azure.

### <a name="threat-detection"></a>Wykrywanie zagrożeń
[Wykrywanie zagrożeń bazy danych SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) wykrycia podejrzanych działań, które wskazują możliwe zagrożenia bezpieczeństwa. Wykrywanie zagrożeń umożliwia reagowania na podejrzane zdarzenia w bazie danych, takich jak iniekcji SQL, w jakiej występują. Zawiera alerty i zezwala na korzystanie z usługi Azure SQL Database Auditing do eksplorowania podejrzane zdarzenia.

![Wykrywanie zagrożeń](./media/azure-databse-security-overview/azure-database-fig5.jpg)

Na przykład iniekcja kodu SQL jest jednym z typowych problemów zabezpieczeń aplikacji sieci Web w Internecie, używane do ataków opartych na danych aplikacji. Osoby atakujące wykorzystać luki w zabezpieczeniach aplikacji do dodania złośliwego instrukcje SQL do pola wejścia aplikacji, naruszenia lub modyfikowanie danych w bazie danych.

Funkcjonariusze lub innych administratorów wyznaczonych można uzyskać natychmiastowego wysłania powiadomienia o bazy danych podejrzanych działaniach miarę ich występowania. Każde powiadomienie zawiera szczegółowe informacje o podejrzanych działaniach i zaleca jak do dalszego zbadania i ograniczyć zagrożenie.        

## <a name="centralized-security-management"></a>Zarządzanie zabezpieczeniami scentralizowane

Usługa [Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) pomaga w zapobieganiu zagrożeniom, wykrywaniu ich i reagowaniu na nie. Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które w przeciwnym razie mogłyby pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań z zakresu zabezpieczeń.

[Centrum zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-sql-database) pomaga chronić dane w bazie danych SQL, zapewniając wgląd w zabezpieczenia serwerów i baz danych. Centrum zabezpieczeń można:

-   Definiowanie zasad szyfrowania bazy danych SQL i inspekcji.
-   Monitorowanie zabezpieczeń zasobów bazy danych SQL za pośrednictwem wszystkich subskrypcji.
-   Szybkie identyfikowanie i Korygowanie problemów z zabezpieczeniami.
-   Integracja alertów z [wykrywanie zagrożeń bazy danych SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).
-   Centrum zabezpieczeń obsługuje dostęp opartej na rolach.

## <a name="azure-marketplace"></a>Azure Marketplace

Portal Azure Marketplace to rynek aplikacji i usług w trybie online, który umożliwia początkującym i niezależnym dostawcom oprogramowania oferowanie swoich rozwiązań klientom platformy Azure na całym świecie.
Portal Azure Marketplace łączy ekosystemy partnerów platformy Microsoft Azure w pojedynczą, jednolitą platformę, która lepiej służy naszym klientom i partnerom. Kliknij przycisk [tutaj](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1) oka produktów zabezpieczeń bazy danych na platformę handlową platformy Azure.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [zabezpieczenia bazy danych SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial).
- Dowiedz się więcej o [usługi Centrum zabezpieczeń Azure i usługi Azure SQL Database](https://docs.microsoft.com/azure/security-center/security-center-sql-database).
- Aby dowiedzieć się więcej na temat wykrywania zagrożeń, zobacz [wykrywanie zagrożeń bazy danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).
- Aby dowiedzieć się więcej, zobacz [wydajności bazy danych SQL poprawy](https://docs.microsoft.com/azure/sql-database/sql-database-performance-tutorial). 
