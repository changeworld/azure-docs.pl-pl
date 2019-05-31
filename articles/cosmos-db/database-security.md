---
title: Zabezpieczenia bazy danych — Azure Cosmos DB
description: Dowiedz się, jak usługi Azure Cosmos DB udostępnia zabezpieczenia ochrony i danych bazy danych dla swoich danych.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: b82f7fed2407da86c036237a2de10363c4706d67
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241128"
---
# <a name="security-in-azure-cosmos-db---overview"></a>Zabezpieczenia w usłudze Azure Cosmos DB — omówienie

W tym artykule omówiono najlepsze rozwiązania dotyczące zabezpieczeń bazy danych i kluczowe funkcje oferowane przez usługę Azure Cosmos DB ułatwiają zapobieganie, wykrywanie ich i reagowanie na naruszenie bazy danych.

## <a name="whats-new-in-azure-cosmos-db-security"></a>What's new in zabezpieczeń usługi Azure Cosmos DB

Szyfrowanie w spoczynku jest teraz dostępna dla dokumentów i kopii zapasowych przechowywanych w usłudze Azure Cosmos DB we wszystkich regionach platformy Azure. Szyfrowanie w spoczynku, jest automatycznie stosowany dla istniejących i nowych klientów w tych regionach. Nie ma potrzeby konfigurowania; Możesz uzyskać takie samo opóźnienie wspaniałe, przepływności, dostępności i funkcjonalność jak przed w oparciu o dane, wiedząc, jest bezpieczny z szyfrowanie danych magazynowanych.

## <a name="how-do-i-secure-my-database"></a>Jak zabezpieczyć bazę danych

Bezpieczeństwo danych jest wspólna odpowiedzialność między, klienta i dostawcy bazy danych. W zależności od dostawcy bazy danych, które wybierzesz ilość odpowiedzialność, które wykonuje mogą się różnić. Jeśli wybierzesz rozwiązania lokalnego, musisz podać wszystko — od ochronę punktów końcowych do zabezpieczenia fizyczne sprzętu — czyli nie łatwym zadaniem. Jeśli wybierzesz PaaS dostawcy bazy danych w chmurze takich jak Azure Cosmos DB, znacznie zmniejsza obszar zainteresowania. Poniższy obraz, pobierają z firmy Microsoft [obowiązki udostępnione Cloud Computing](https://aka.ms/sharedresponsibility) oficjalnym dokumencie pokazano, jak odpowiedzialny za zmniejsza się z dostawcą PaaS, takich jak usługi Azure Cosmos DB.

![Obowiązki klienta i bazy danych dostawcy:](./media/database-security/nosql-database-security-responsibilities.png)

Na powyższym diagramie przedstawiono ogólne chmury składniki zabezpieczeń, ale elementy trzeba martwić się o specjalnie dla używanego rozwiązania bazy danych? I jak porównać rozwiązań ze sobą?

Firma Microsoft zaleca z poniższej listy kontrolnej wymagania, na którym chcesz porównać systemy baz danych:

- Zabezpieczenia sieciowe i ustawienia zapory
- Uwierzytelnianie użytkowników i kontrolki użytkownika szczegółowe
- Możliwość Replikuj dane globalnie dla regionalnych awarii
- Możliwość przełączenia awaryjnego z centrum danych z jednego do drugiego
- Replikacja danych lokalnych w centrum danych
- Kopie zapasowe danych automatyczne
- Przywracanie usuniętych danych z kopii zapasowych
- Ochrona i izolowania danych poufnych
- Monitorowanie ataków
- Odpowiadanie na ataki
- Możliwość dane geograficzne horyzont stosować się do ograniczeń nadzoru danych
- Ochrona fizycznych serwerów w centrach danych chronionych
- Certyfikacje

I chociaż może wydawać się oczywiste, ostatnie [naruszeń bazy danych na dużą skalę](https://thehackernews.com/2017/01/mongodb-database-security.html) Przypomnij USA prostą, ale krytyczne znaczenie następujące wymagania:

- Poprawionego serwerów, które są zawsze na bieżąco
- Protokół HTTPS przez szyfrowanie domyślne/SSL
- Konta z uprawnieniami administracyjnymi przy użyciu silnych haseł

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>Jak usługa Azure Cosmos DB zabezpieczyć moją bazę danych

Przyjrzyjmy się ponownie na powyższej liście — ile tych wymagań dotyczących zabezpieczeń w usłudze Azure Cosmos DB zapewnia? Każdy jednego.

Przyjrzyjmy się bliżej temu w każdej z nich szczegółów.

|Wymaganie dotyczące zabezpieczeń|Podejście do zabezpieczeń danych usługi Azure Cosmos DB|
|---|---|
|Bezpieczeństwo sieci|Za pomocą zapory adresów IP jest pierwszą warstwę ochrony w celu zabezpieczenia bazy danych. Usługa Azure Cosmos DB obsługuje zasady na podstawie kontroli dostępu opartych na protokole IP, obsługi zapory dla ruchu przychodzącego. Kontroli dostępu opartych na protokole IP są podobne do reguł zapory używane przez tradycyjnych systemów baz danych, ale one rozszerzane tak, aby konta bazy danych usługi Azure Cosmos DB jest dostępny tylko z zatwierdzonego zestawu maszyn lub usług w chmurze. <br><br>Usługa Azure Cosmos DB umożliwia włączenie określonego adresu IP (168.61.48.0), zakresu adresów IP (168.61.48.0/8) i kombinacji adresów IP i zakresów. <br><br>Wszystkie żądania pochodzące z maszyn spoza tej listy dozwolonych są blokowane przez usługę Azure Cosmos DB. Żądania z zatwierdzonych maszyn i następnie usług w chmurze musi przejść proces uwierzytelniania, należy podać kontroli dostępu do zasobów.<br><br>Dowiedz się więcej w [Obsługa zapory usługi Azure Cosmos DB](firewall-support.md).|
|Autoryzacja|Usługa Azure Cosmos DB używa kodu uwierzytelniania wiadomości bazujących na skrótach (HMAC), do autoryzacji. <br><br>Każde żądanie jest wyznaczana wartość skrótu przy użyciu klucza tajnego konta, a kolejne skrótu zakodowany base-64 jest wysyłany z każdego wywołania do usługi Azure Cosmos DB. Aby zatwierdzić żądanie, usługi Azure Cosmos DB używa poprawny klucz tajny i właściwości do generowania wartości skrótu, a następnie porównuje wartości przy użyciu jednego w żądaniu. Jeśli dwie wartości są zgodne, operacja jest Autoryzacja powiodła się i żądanie jest przetwarzane, w przeciwnym razie występuje błąd autoryzacji, a żądanie zostanie odrzucone.<br><br>Można użyć dowolnego [klucz główny](secure-access-to-data.md#master-keys), lub [token zasobu](secure-access-to-data.md#resource-tokens) umożliwiając szczegółową kontrolę dostępu do zasobów, takich jak dokumentu.<br><br>Dowiedz się więcej w [zabezpieczanie dostępu do zasobów usługi Azure Cosmos DB](secure-access-to-data.md).|
|Użytkownicy i uprawnienia|Dla konta, za pomocą klucza głównego, można utworzyć zasobów użytkowników i uprawnień na bazę danych. Token zasobu jest skojarzony z uprawnień w bazie danych i określa, czy użytkownik ma dostęp (odczytu i zapisu, tylko do odczytu, lub Brak dostępu) do zasobu aplikacji w bazie danych. Zasoby aplikacji obejmują kontenera, dokumentów, załączniki, procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika. Token zasobu jest następnie używany podczas uwierzytelniania do zapewnienia zawartości lub odmawiające dostępu do zasobu.<br><br>Dowiedz się więcej w [zabezpieczanie dostępu do zasobów usługi Azure Cosmos DB](secure-access-to-data.md).|
|Integracja usługi Active directory (RBAC)| Można również podać lub ograniczanie dostępu do konta Cosmos, bazy danych, kontenerów i ofert (przepływność) za pomocą kontroli dostępu (IAM) w witrynie Azure portal. Zarządzanie dostępem i Tożsamościami zapewnia kontroli dostępu opartej na rolach i integruje się z usługą Active Directory. Można użyć wbudowanych ról lub ról niestandardowych dla użytkowników indywidualnych i grup. Zobacz [integracji usługi Active Directory](role-based-access-control.md) artykuł, aby uzyskać więcej informacji.|
|Replikacji globalnej|Usługa Azure Cosmos DB oferuje gotową do użycia dystrybucji globalnej, co pozwala na replikowanie danych do jednej centrów danych platformy Azure na całym świecie za pomocą kliknięcia przycisku. Globalnej replikacji umożliwia skalowanie globalne i zapewniają małe opóźnienia dostępu do danych na całym świecie.<br><br>W kontekście zabezpieczeń globalnej replikacji zapewnia ochronę danych przed regionalnymi awariami.<br><br>Dowiedz się więcej z artykułu [Distribute data globally](distribute-data-globally.md) (Globalna dystrybucja danych).|
|Wywoływania regionalnego trybu failover|Jeśli zostały zreplikowane dane w więcej niż jedno centrum danych, usługi Azure Cosmos DB automatycznie zestawia za pośrednictwem operacji powinno regionalnym centrum danych przejdą w tryb offline. Można utworzyć priorytetową listą regionów trybu failover przy użyciu określonych regionów, w których dane są replikowane. <br><br>Dowiedz się więcej w [wywoływania regionalnego trybu failover w usłudze Azure Cosmos DB](high-availability.md).|
|Lokalnej replikacji|Nawet w obrębie jednego centrum danych, usługi Azure Cosmos DB automatycznie replikuje dane dotyczące wysokiej dostępności, umożliwiając wybór [poziomów spójności](consistency-levels.md). Replikacja gwarantuje dostępność przez 99,99% [umowa SLA gwarantująca dostępność](https://azure.microsoft.com/support/legal/sla/cosmos-db) dla wszystkich kont w obrębie jednego regionu i wszystkich kont w wielu regionach za pomocą rozluźnionej spójności i dostępności na wszystkich multiregionalne konta baz danych do odczytu przez 99,999% czasu.|
|Zautomatyzowane tworzenie kopii zapasowej online|Baz danych Azure Cosmos DB są regularnie wykonywana kopia zapasowa i przechowywane w magazynie geograficznie nadmiarowe. <br><br>Dowiedz się więcej w [automatyczne tworzenie kopii zapasowej online i przywracanie za pomocą usługi Azure Cosmos DB](online-backup-and-restore.md).|
|Przywracanie usuniętych danych|Automatyczne kopie zapasowe online może służyć do odzyskiwania danych, może przypadkowo usunięto maksymalnie 30 dni po wystąpieniu zdarzenia. <br><br>Dowiedz się więcej w [automatyczne tworzenie kopii zapasowej online i przywracanie za pomocą usługi Azure Cosmos DB](online-backup-and-restore.md)|
|Ochrona i izolowania danych poufnych|Wszystkie dane w regionach wymienionych w What's new? teraz są szyfrowane w stanie spoczynku.<br><br>Dane osobowe i innych poufnych danych może być izolowane do określonego kontenera oraz odczytu i zapisu lub tylko do odczytu może być ograniczony do konkretnych użytkowników.|
|Monitor ataków|Za pomocą [inspekcji rejestrowania i Dzienniki aktywności](logging.md), można monitorować konta na potrzeby typowych i nietypowych działań. Można wyświetlić, jakie operacje zostały wykonane względem zasobów, którzy zainicjował operację, gdy operacja Wystąpił stan operacji i znacznie bardziej, jak pokazano na zrzucie ekranu pod tą tabelą.|
|Odpowiedzi na ataki|Gdy nawiązano pomocy technicznej platformy Azure, aby zgłosić potencjalny atak procesu reagowania na zdarzenia kroku 5 rozpocznie się kompilowanie. Celem procesu kroku 5 jest przywrócenie normalnego użytkowania, zabezpieczenia i operacje możliwie jak najszybciej po wykryciu problemu i badanie została uruchomiona.<br><br>Dowiedz się więcej w [systemu Microsoft Azure Security Response w chmurze](https://aka.ms/securityresponsepaper).|
|Grodzenia|Usługa Azure Cosmos DB gwarantuje nadzór nad danymi na niezależne regiony (na przykład, Niemcy, Chiny, USA).|
|Urządzenia chronione|Dane w usłudze Azure Cosmos DB są przechowywane na dyskach SSD w centrach danych chronionych na platformie Azure.<br><br>Dowiedz się więcej w [globalnych centrów danych firmy Microsoft](https://www.microsoft.com/en-us/cloud-platform/global-datacenters)|
|Szyfrowanie protokołu HTTPS/SSL/TLS|Wszystkie usługi klienta usługi Azure Cosmos DB interakcje są protokoły SSL/TLS 1.2 stanie. Protokoły SSL/TLS 1.2, wymuszane są także wszystkie replikacje wewnątrz centrum danych i między centrum danych.|
|Szyfrowanie w spoczynku|Wszystkie dane przechowywane w usłudze Azure Cosmos DB są szyfrowane w stanie spoczynku. Dowiedz się więcej w [usługi Azure Cosmos DB szyfrowanie w spoczynku](./database-encryption-at-rest.md)|
|Poprawionego serwerów|Jako zarządzana baza danych usługi Azure Cosmos DB eliminuje konieczność zarządzania i stosowanie poprawek do serwerów, która została wykonana, automatycznie.|
|Konta z uprawnieniami administracyjnymi przy użyciu silnych haseł|Jest trudno uwierzyć, musimy nawet wspomina o identyfikatorach tego wymagania, ale w przeciwieństwie do niektórych z naszych konkurentów nie jest możliwe jest konto administracyjne, bez hasła w usłudze Azure Cosmos DB.<br><br> Domyślnie jest wbudowanymi zabezpieczeń za pośrednictwem protokołu SSL i HMAC uwierzytelniania na podstawie klucza tajnego.|
|Certyfikaty ochrony bezpieczeństwa i danych| Aby uzyskać najbardziej aktualną listę certyfikatów, zobacz ogólnych [lokacji zgodność platformy Azure](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) oraz najnowsze [dokumentu zgodności platformy Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) o wszystkie certyfikaty (Wyszukaj Cosmos). Aby uzyskać bardziej ukierunkowaną przeczytaj Sprawdź wpis 25 kwietnia 2018 r. [Azure #CosmosDB: Bezpieczne, prywatne i zgodne, która zawiera Soc 1/2 typu 2, HITRUST, PCI DSS poziom 1, ISO 27001, HIPAA, FedRAMP High i wiele innych.

Poniższy zrzut ekranu pokazuje, jak można użyć inspekcji rejestrowania i Dzienniki aktywności do monitorowania na koncie: ![Dzienniki aktywności usługi Azure Cosmos DB](./media/database-security/nosql-database-security-application-logging.png)

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat kluczy głównych i tokenów zasobów, zobacz [zabezpieczanie dostępu do danych usługi Azure Cosmos DB](secure-access-to-data.md).

Aby uzyskać więcej informacji na temat rejestrowania inspekcji, zobacz [rejestrowania diagnostycznego usługi Azure Cosmos DB](logging.md).

Aby uzyskać więcej informacji o certyfikaty firmy Microsoft, zobacz [Centrum zaufania systemu Azure](https://azure.microsoft.com/support/trust-center/).