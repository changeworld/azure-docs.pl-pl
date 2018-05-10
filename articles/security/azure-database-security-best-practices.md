---
title: Najlepsze rozwiązania bazy danych platformy Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera zestaw najlepsze rozwiązania dotyczące zabezpieczeń bazy danych platformy Azure.
services: security
documentationcenter: na
author: unifycloud
manager: mbaldwin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: 81acf90f0d600c0c3dafa2a4ccd2f8564fd18c9a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="azure-database-security-best-practices"></a>Najlepsze rozwiązania Azure bazy danych

Zabezpieczeń jest szczególnie ważne w przypadku zarządzania bazami danych i zawsze było priorytet bazy danych SQL Azure. Baz danych mogą być ściśle chronione w celu zaspokojenia najbardziej przepisami lub wymaganiami zabezpieczeń, w tym HIPAA, ISO 27001/27002 i PCI DSS poziom 1, między innymi. Bieżącą listę certyfikaty zgodności zabezpieczeń jest dostępne pod adresem [witryny Microsoft Trust Center](http://azure.microsoft.com/support/trust-center/services/). Możesz również umieścić baz danych w określonych centrach danych platformy Azure, na podstawie przepisów wymagań.

W tym artykule omówiono kolekcja najlepszych rozwiązań dotyczących zabezpieczeń bazy danych platformy Azure. Następujące najlepsze rozwiązania są uzyskiwane z wiemy z doświadczenia z zabezpieczeń bazy danych platformy Azure i doświadczenia klientów, takich jak samodzielnie.

Dla każdego najlepszym rozwiązaniem prezentujemy zasady:

-   Co to jest najlepszym rozwiązaniem
-   Dlaczego chcesz włączyć tej najlepsze praktyki
-   Jeśli nie zostanie włączone najlepszym rozwiązaniem, co może być skutkiem
-   Jak można znaleźć umożliwiające najlepsze praktyki

W tym artykule najlepsze rozwiązania zabezpieczeń bazy danych Azure na podstawie opinii konsensu i funkcji platformy Azure i funkcja ustawia istniejących w chwili ten artykuł dotyczy. Opinie i technologie ulegną zmianom, a ten artykuł będzie aktualizowany na bieżąco w celu odzwierciedlenia tych zmian.

Bazy danych platformy Azure najlepsze rozwiązania omówione w tym artykule obejmują:

-   Użyj reguł zapory, aby ograniczyć dostęp do bazy danych
-   Włącz uwierzytelnianie bazy danych
-   Ochrona danych przy użyciu szyfrowania
-   Ochrona danych podczas przesyłania
-   Włączanie inspekcji bazy danych
-   Włączyć wykrywanie zagrożeń bazy danych


## <a name="use-firewall-rules-to-restrict-database-access"></a>Użyj reguł zapory, aby ograniczyć dostęp do bazy danych

Usługa Microsoft Azure SQL Database udostępnia usługę relacyjnej bazy danych dla platformy Azure i innych aplikacji internetowych. Aby zabezpieczyć dostęp do bazy danych SQL kontroluje dostęp przy użyciu reguł zapory ograniczenie łączności według adresu IP, mechanizmów uwierzytelniania wymaganie od użytkowników w celu potwierdzenia ich tożsamości i mechanizmów autoryzacji ograniczanie użytkowników do określonych akcji i dane. Zapory uniemożliwić dostęp do serwera bazy danych do chwili określenia komputery, które ma uprawnienia. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.

![Reguły zapory](./media/azure-database-security-best-practices/azure-database-security-best-practices-Fig1.png)

Usługa Azure SQL Database jest dostępna tylko za pośrednictwem portu TCP 1433. Aby uzyskać dostęp do usługi SQL Database z komputera, upewnij się, że zapora komputera klienta umożliwia wychodzący ruch TCP na porcie TCP 1433. Jeśli nie jest to wymagane przez inne aplikacje, należy go zablokować połączenia przychodzące na porcie TCP 1433 przy użyciu reguł zapory.

W ramach procesu łączenia wszystkie połączenia przychodzące z maszyny wirtualnej platformy Azure są przekierowywane na inny adres IP i port, unikatowy dla każdej roli procesu roboczego. Numer portu należy do zakresu od 11000 do 11999. Aby uzyskać więcej informacji na temat portów TCP, zobacz [porty inne niż 1433 ADO.NET 4.5 i SQL Database2](https://docs.microsoft.com/azure/sql-database/sql-database-develop-direct-route-ports-adonet-v12).

> [!Note]
> Aby uzyskać więcej informacji na temat reguł zapory w usłudze SQL Database, zobacz [Omówienie reguł zapory usługi SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

## <a name="enable-database-authentication"></a>Włącz uwierzytelnianie bazy danych
Baza danych SQL obsługuje dwa typy uwierzytelniania, uwierzytelnianie SQL i Azure uwierzytelnianie usługi Active Directory (Azure AD Authentication).

**Uwierzytelnianie SQL** jest zalecane w następujących przypadkach:

-   Dzięki temu SQL Azure do obsługi środowisk mieszanych systemach operacyjnych, gdzie wszyscy użytkownicy nie są uwierzytelniane przez domeny systemu Windows.
-   Umożliwia SQL Azure do obsługi starszych aplikacji i aplikacji dostarczanych przez osoby trzecie, które wymagają uwierzytelniania programu SQL Server.
-   Zezwala użytkownikom na łączenie się z nieznanej lub niezaufanej domeny. Na przykład aplikacji, których klienci ustalonych nawiązuje połączenie z przypisane logowania do programu SQL Server do otrzymywania stanu ich zleceń.
-   Umożliwia SQL Azure na potrzeby obsługi aplikacji sieci Web, gdzie użytkownicy tworzyć własne tożsamości.
-   Umożliwia programistom dystrybucja aplikacji przy użyciu hierarchii złożonych uprawnienia oparte na znane, wstępnie zdefiniowane identyfikatory logowania programu SQL Server.

> [!Note]
> Jednak uwierzytelnianie programu SQL Server nie może używać protokołu zabezpieczeń protokołu Kerberos.

Jeśli używasz **uwierzytelniania SQL** należy:

-   Zarządzanie silnych poświadczeń użytkownika.
-   Ochrona poświadczeń w parametrach połączenia.
-   (Potencjalnie) chronić poświadczenia przekazywane za pośrednictwem sieci z serwera sieci Web do bazy danych. Aby uzyskać więcej informacji, zobacz [porady: nawiązać połączenie przy użyciu SQL uwierzytelniania programu SQL Server w programie ASP.NET 2.0](https://msdn.microsoft.com/library/ms998300.aspx).

**Usługa Azure Active Directory authentication** mechanizm nawiązywać połączenia z bazą danych SQL Azure Microsoft i [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) przy użyciu tożsamości w usłudze Azure Active Directory (Azure AD). Przy użyciu uwierzytelniania usługi Azure AD mogą centralnie zarządzać tożsamości użytkowników bazy danych i innych usług firmy Microsoft w jednej centralnej lokalizacji. Centralne zarządzanie identyfikator udostępnia jedno miejsce do zarządzania użytkownikami bazy danych i upraszcza zarządzanie uprawnieniami. Następujące korzyści:

-   Zapewnia zamiast uwierzytelniania programu SQL Server.
-   Pomaga zatrzymać rozprzestrzenianie tożsamości użytkowników na serwerach bazy danych.
-   Umożliwia obrotu hasła w jednym miejscu.
-   Klientów można zarządzać za pomocą zewnętrznego grup (AAD) uprawnień do bazy danych.
-   Można go wyeliminować zapisywania haseł przez włączenie zintegrowanego uwierzytelniania systemu Windows i innych metod uwierzytelniania obsługiwanych przez usługę Azure Active Directory.
-   Usługa Azure AD authentication używa użytkowników zawartej bazy danych do uwierzytelniania tożsamości na poziomie bazy danych.
-   Usługi Azure AD obsługuje uwierzytelnianie na podstawie tokenu dla aplikacji łączenia z bazą danych SQL.
-   Usługa Azure AD authentication obsługuje usług AD FS (Federacja domen) lub native użytkownika i hasło uwierzytelniania lokalnej usługi Azure Active Directory bez synchronizacji domeny.
-   Usługi Azure AD obsługuje połączenia z programu SQL Server Management Studio, które używają uniwersalnych uwierzytelnianie usługi Active Directory, która obejmuje usługi Multi-Factor Authentication (MFA). Silne uwierzytelnianie za pomocą różnych opcji weryfikacji łatwe obejmuje MFA — połączenie telefoniczne, wiadomość tekstowa, karty inteligentne z numeru pin lub powiadomienie aplikacji mobilnej. Aby uzyskać więcej informacji, zobacz [SSMS obsługę usługi Azure AD MFA z bazy danych SQL i usługi SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication).

Kroki konfiguracji obejmują następujących procedur, aby skonfigurować i korzystać z uwierzytelniania usługi Azure Active Directory.

-   Utworzyć i wypełnić usługi Azure AD.
-   Opcjonalnie: Skojarz lub zmienić usługi active directory, który jest aktualnie powiązany z subskrypcją platformy Azure.
-   Tworzenie administratora usługi Azure Active Directory dla serwera Azure SQL lub [magazyn danych SQL Azure](https://azure.microsoft.com/services/sql-data-warehouse/).
- Konfigurowanie komputerów klienckich.
-   Utwórz użytkowników zawartej bazy danych w bazie danych mapowany na tożsamość usługi Azure AD.
-   Połączenia z bazą danych przy użyciu tożsamości usługi Azure AD.

Informacje szczegółowe informacje można znaleźć [tutaj](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication).

## <a name="protect-your-data-using-encryption"></a>Ochrona danych przy użyciu szyfrowania

[Azure SQL Database przezroczystego szyfrowania danych (funkcji TDE)](https://msdn.microsoft.com/library/dn948096.aspx) pomaga chronić przed zagrożeniem złośliwych działań, wykonując w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych, i pliki dziennika transakcji w stanie spoczynku bez konieczności wprowadzania zmian w aplikacji. Funkcji TDE szyfruje magazyn całej bazy danych przy użyciu klucza symetrycznego o nazwie klucza szyfrowania bazy danych.

Nawet wtedy, gdy cały magazyn jest zaszyfrowany, bardzo ważne jest także szyfrowanie Twojej bazy danych. Jest to implementacja obrony w głębokość podejście do ochrony danych. Jeśli używasz bazy danych SQL Azure i chcesz chronić poufne dane, takie jak karty kredytowej lub numerów ubezpieczenia społecznego, można zaszyfrować baz danych z FIPS 140-2 zweryfikowane 256 bitowe szyfrowanie AES spełniające wymagania wiele standardy branżowe (np. HIPAA, PCI).

Ważne jest, aby zrozumieć, że pliki związane z [(BPE). rozszerzenie puli bufora](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) nie są szyfrowane, gdy baza danych jest szyfrowana przy użyciu funkcji TDE. Należy użyć narzędzia szyfrowania na poziomie systemu plików, takich jak [funkcji BitLocker](https://technet.microsoft.com/library/cc732774) lub [system szyfrowania plików (EFS)](https://technet.microsoft.com/library/cc700811.aspx) dla BPE powiązane pliki.

Od autoryzowanym użytkownikiem, takie jak administrator zabezpieczeń lub administrator bazy danych ma dostęp do danych, nawet jeśli bazy danych jest szyfrowany przy funkcji TDE, należy również postępować zgodnie z poniższymi zaleceniami:

-   Włącz uwierzytelnianie SQL na poziomie bazy danych.
-   Użyj usługi Azure AD uwierzytelnianie przy użyciu [role RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview).
-   Użytkownicy i aplikacje powinny używać osobnych kont do uwierzytelniania. W ten sposób można ograniczyć uprawnień użytkowników i aplikacje i zmniejszenia ryzyka złośliwych działań.
-   Implementowanie zabezpieczeń na poziomie bazy danych przy użyciu ról stałej bazy danych (takich jak db_datareader lub db_datawriter), lub można utworzyć niestandardowe role w aplikacji można przyznać uprawnienia jawne do zaznaczonych obiektów bazy danych.

Można również rozważyć inne sposoby szyfrowania danych:

-   [Szyfrowanie na poziomie komórki](https://msdn.microsoft.com/library/ms179331.aspx) służące do szyfrowania określonej kolumny lub nawet poszczególnych komórek danych przy użyciu różnych kluczy szyfrowania.
-   Szyfrowania używany przy użyciu zawsze zaszyfrowane: [zawsze zaszyfrowane](https://msdn.microsoft.com/library/mt163865.aspx) umożliwia klientom szyfrowania poufnych danych w aplikacjach klienckich i nigdy nie podawaj kluczy szyfrowania z aparatem bazy danych (bazy danych SQL lub SQL Server). W związku z tym zawsze zaszyfrowane zapewnia oddzielenie tych, którzy należą dane (i mogły go wyświetlać) oraz tych, którzy zarządzania danymi (ale powinien nie mają dostępu).
-   Przy użyciu zabezpieczeń na poziomie wiersza: zabezpieczenia na poziomie wiersza służy do kontrolowania dostępu do wierszy w tabeli bazy danych na podstawie charakterystyk użytkownika wykonywania zapytania (np. grupy członkostwa lub wykonywania context). Aby uzyskać więcej informacji, zobacz [Zabezpieczenia na poziomie wierszy](https://msdn.microsoft.com/library/dn765131).

## <a name="protect-data-in-transit"></a>Ochrona danych podczas przesyłania
Ochrona danych podczas przesyłania powinien być integralną część strategii ochrony danych. Ponieważ dane będą przenoszone i z powrotem w wielu lokalizacjach, ogólne zalecenie jest zawsze używają protokołów SSL/TLS do wymiany danych w różnych lokalizacjach. W niektórych sytuacjach można odizolować kanału całej komunikacji między lokalnymi i w chmurze infrastruktury przy użyciu wirtualnej sieci prywatnej (VPN).

Przenoszenie między lokalną infrastrukturą i Azure danych należy rozważyć odpowiednie zabezpieczenia, takie jak HTTPS lub sieci VPN.

Dla organizacji, które trzeba bezpieczny dostęp z wielu stacji roboczych lokalnego do platformy Azure, użyj [Azure VPN lokacja lokacja](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-site-to-site-create).

Organizacje, które trzeba zabezpieczyć dostęp z poszczególnych stacji roboczych lokalnymi lub poza siedzibą firmy na platformie Azure, warto rozważyć użycie [punkt-lokacja sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-point-to-site-create).

Większych zestawów danych można przenieść za pośrednictwem dedykowanej o dużej szybkości łącza sieci WAN takich jak [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Jeśli chcesz używać usługi ExpressRoute, można także szyfrowanie danych na poziomie aplikacji przy użyciu [SSL/TLS](https://support.microsoft.com/kb/257591) lub innymi protokołami zapewnia dodatkową ochronę.

Jeśli użytkownik korzysta z usługi Azure Storage za pośrednictwem portalu Azure, wszystkich transakcji jest realizowana za pośrednictwem protokołu HTTPS. [Interfejs API REST magazynu](https://msdn.microsoft.com/library/azure/dd179355.aspx) over HTTPS można również wchodzić w interakcje z [usługi Azure Storage](https://azure.microsoft.com/services/storage/) i [bazy danych SQL Azure](https://azure.microsoft.com/services/sql-database/).

Organizacje, które się nie powieść, aby chronić przesyłane dane są bardziej narażony na [ataków man-in--middle](https://technet.microsoft.com/library/gg195821.aspx), [podsłuchiwaniu](https://technet.microsoft.com/library/gg195641.aspx) i przejęcie kontroli sesji. Tego rodzaju ataki może być pierwszym krokiem w uzyskiwaniu dostępu do poufnych danych.

Aby dowiedzieć się więcej o opcji sieci VPN platformy Azure, przeczytaj artykuł [planowania i projektowania dla bramy sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

## <a name="enable-database-auditing"></a>Włączanie inspekcji bazy danych
Inspekcja wystąpienia aparatu bazy danych programu SQL Server lub jedna baza danych obejmuje śledzenie i rejestrowanie zdarzeń dotyczących aparatu bazy danych. SQL Server audit umożliwia tworzenie inspekcji serwera, które może zawierać specyfikacji inspekcji serwera dla zdarzenia na poziomie serwera i specyfikacji inspekcji bazy danych dla zdarzenia na poziomie bazy danych. Zdarzenia inspekcji mogą być zapisywane w dziennikach zdarzeń lub plików inspekcji.

Istnieje kilka poziomów inspekcji dla programu SQL Server, w zależności od rząd lub standardów wymagań instalacyjnych. SQL Server Audit zapewnia narzędzia i procesy, które muszą mieć, aby włączyć, przechowywania i wyświetlania na różnych obiektów serwera i bazy danych inspekcji.

[Usługa Azure SQL Database Auditing](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) śledzi zdarzenia bazy danych i zapisuje je inspekcji logowania na koncie magazynu Azure.

Inspekcja pomaga zachować zgodność z przepisami, analizować aktywność bazy danych oraz uzyskać wgląd w odchylenia i anomalie, które mogą oznaczać problemy biznesowe lub podejrzane naruszenia zabezpieczeń.

Inspekcja umożliwia i ułatwia przestrzeganie standardów zgodności, ale nie gwarantuje się zgodności.

Aby dowiedzieć się więcej o inspekcji bazy danych oraz jak je włączyć, przeczytaj artykuł [włączyć inspekcję i wykrywania zagrożeń na serwerach SQL w Centrum zabezpieczeń Azure](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-servers).

## <a name="enable-database-threat-detection"></a>Włączyć wykrywanie zagrożeń bazy danych
Wykrywanie zagrożeń SQL umożliwia wykrywanie i odpowiadanie na potencjalne zagrożenia w miarę ich występowania, zapewniając alerty zabezpieczeń w nietypowych działań. Zostanie wyświetlony alert po bazy danych podejrzanych działań, potencjalnych luk i ataki, a także bazy danych nietypowe wzorce dostępu. Wykrywanie zagrożeń SQL alerty zawierają szczegółowe informacje o podejrzanych działaniach i zalecane działania dotyczące sposobu badania i ograniczyć zagrożenie.

Na przykład iniekcja kodu SQL jest jednym z typowych problemów zabezpieczeń aplikacji sieci Web w Internecie, używane do ataków opartych na danych aplikacji. Osoby atakujące wykorzystać luki w zabezpieczeniach aplikacji do dodania złośliwego instrukcje SQL do pola wejścia aplikacji, naruszenia lub modyfikowanie danych w bazie danych.

Aby dowiedzieć się więcej o konfigurowaniu wykrywanie zagrożeń dla bazy danych w portalu Azure można znaleźć [wykrywanie zagrożeń bazy danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).

Ponadto wykrywanie zagrożeń SQL integruje alerty z [Centrum zabezpieczeń Azure](https://azure.microsoft.com/services/security-center/). Zapraszamy do bezpłatnego wypróbowania tej funkcji przez 60 dni.

Aby dowiedzieć się więcej na temat wykrywania zagrożeń bazy danych oraz jak je włączyć, przeczytaj artykuł [włączyć inspekcję i wykrywania zagrożeń na serwerach SQL w Centrum zabezpieczeń Azure](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-servers).

## <a name="conclusion"></a>Podsumowanie
Bazy danych platformy Azure to platforma niezawodne bazy danych, z pełnym zakresem funkcji zabezpieczeń, które spełniają wymagania organizacyjne i przepisami zgodności wiele. Możesz pomóc w ochronie danych przez kontrolowanie fizyczny dostęp do danych i za pomocą różnych opcji zabezpieczeń danych w pliku, kolumny lub poziom wiersza niewidocznego szyfrowania danych, szyfrowanie na poziomie komórki lub zabezpieczenia na poziomie wiersza. Zawsze zaszyfrowane umożliwia również operacji względem zaszyfrowane dane, uproszczeniu procesu aktualizacji aplikacji. Z kolei dostęp do inspekcji Dzienniki aktywności bazy danych SQL zawiera informacje, które są potrzebne, co należy wiedzieć, jak i kiedy jest uzyskiwany dostęp do danych.

## <a name="next-steps"></a>Kolejne kroki
- Aby dowiedzieć się więcej na temat reguł zapory, zobacz [reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).
- Aby dowiedzieć się więcej o użytkownikach i danych logowania, zobacz [Zarządzanie danymi logowania](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).
- Samouczek, zobacz [zabezpieczenia bazy danych SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial).
