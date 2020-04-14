---
title: Zabezpieczenia bazy danych — usługa Azure Cosmos DB
description: Dowiedz się, jak usługa Azure Cosmos DB zapewnia ochronę baz danych i bezpieczeństwo danych.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: mjbrown
ms.openlocfilehash: 8a0ef4c5abcd4c4137a878adf6a7fff9e80c2764
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273515"
---
# <a name="security-in-azure-cosmos-db---overview"></a>Zabezpieczenia w usłudze Azure Cosmos DB — omówienie

W tym artykule omówiono najlepsze rozwiązania dotyczące zabezpieczeń baz danych i kluczowe funkcje oferowane przez usługę Azure Cosmos DB, które pomagają zapobiegać naruszeniom, wykrywać je i reagować na nie.

## <a name="whats-new-in-azure-cosmos-db-security"></a>Co nowego w zabezpieczeniach usługi Azure Cosmos DB

Szyfrowanie w stanie spoczynku jest teraz dostępne dla dokumentów i kopii zapasowych przechowywanych w usłudze Azure Cosmos DB we wszystkich regionach platformy Azure. Szyfrowanie w spoczynku jest stosowane automatycznie zarówno dla nowych, jak i istniejących klientów w tych regionach. Nie ma potrzeby, aby skonfigurować coś; i otrzymasz takie same duże opóźnienie, przepustowość, dostępność i funkcjonalność, jak wcześniej, dzięki temu, że wiesz, że Twoje dane są bezpieczne dzięki szyfrowaniu w spoczynku.

## <a name="how-do-i-secure-my-database"></a>Jak zabezpieczyć bazę danych

Bezpieczeństwo danych jest współużytkowana odpowiedzialność między Tobą, klientem i dostawcą bazy danych. W zależności od wybranego dostawcy bazy danych wysokość odpowiedzialności, którą ponosisz, może się różnić. Jeśli wybierzesz rozwiązanie lokalne, musisz zapewnić wszystko, od ochrony punktu końcowego do fizycznego bezpieczeństwa sprzętu - co nie jest łatwym zadaniem. Jeśli wybierzesz dostawcy bazy danych w chmurze PaaS, takich jak Usługi Azure Cosmos DB, obszar zainteresowania zmniejsza się znacznie. Na poniższej ilustracji, zapożyczone z pracy firmy Microsoft [współdzielonych dla przetwarzania w chmurze,](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91) pokazano, jak zmniejsza się odpowiedzialność z dostawcą PaaS, takich jak Usługa Azure Cosmos DB.

![Obowiązki klientów i dostawców baz danych](./media/database-security/nosql-database-security-responsibilities.png)

Na poprzednim diagramie przedstawiono składniki zabezpieczeń w chmurze wysokiego poziomu, ale jakie elementy należy się martwić specjalnie dla rozwiązania bazy danych? I jak można porównać rozwiązania do siebie?

Zalecamy następującą listę kontrolną wymagań dotyczących porównywania systemów baz danych:

- Ustawienia zabezpieczeń sieci i zapory sieciowej
- Uwierzytelnianie użytkownika i precyzyjne kontrole użytkownika
- Możliwość replikowania danych globalnie w przypadku awarii regionalnych
- Możliwość awaryjnego pracy z jednego centrum danych do drugiego
- Replikacja danych lokalnych w centrum danych
- Automatyczne tworzenie kopii zapasowych danych
- Przywracanie usuniętych danych z kopii zapasowych
- Ochrona i izolowanie poufnych danych
- Monitorowanie ataków
- Reagowanie na ataki
- Możliwość geo-ogrodzenia danych w celu przestrzegania ograniczeń zarządzania danymi
- Fizyczna ochrona serwerów w chronionych centrach danych
- Certyfikaty

I chociaż może się to wydawać oczywiste, ostatnie [naruszenia bazy danych na dużą skalę](https://thehackernews.com/2017/01/mongodb-database-security.html) przypominają nam o prostym, ale krytycznym znaczeniu następujących wymagań:

- Poprawione serwery, które są aktualne
- Domyślnie https/szyfrowanie TLS
- Konta administracyjne z silnymi hasłami

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>W jaki sposób usługa Azure Cosmos DB zabezpiecza moją bazę danych

Przyjrzyjmy się poprzedniej liście — ile z tych wymagań dotyczących zabezpieczeń zapewnia usługa Azure Cosmos DB? Każdy.

Przyjmijmy się szczegółowo każdego z nich.

|Wymóg bezpieczeństwa|Podejście usługi Azure Cosmos DB do zabezpieczeń|
|---|---|
|Bezpieczeństwo sieci|Korzystanie z zapory IP jest pierwszą warstwą ochrony do zabezpieczenia bazy danych. Usługa Azure Cosmos DB obsługuje oparte na zasadach formanty dostępu oparte na protokoiści ip dla obsługi zapory przychodzącej. Formanty dostępu oparte na protoke są podobne do reguł zapory używanych przez tradycyjne systemy baz danych, ale są one rozszerzane, dzięki czemu konto bazy danych usługi Azure Cosmos jest dostępne tylko z zatwierdzonego zestawu maszyn lub usług w chmurze. Dowiedz się więcej w [artykule pomocy technicznej usługi Azure Cosmos DB zapory.](firewall-support.md)<br><br>Usługa Azure Cosmos DB umożliwia włączenie określonego adresu IP (168.61.48.0), zakresu adresów IP (168.61.48.0/8) oraz kombinacji adresów IP i zakresów. <br><br>Wszystkie żądania pochodzące z maszyn spoza tej listy dozwolonych są blokowane przez usługę Azure Cosmos DB. Żądania z zatwierdzonych komputerów i usług w chmurze następnie należy zakończyć proces uwierzytelniania, aby uzyskać kontrolę dostępu do zasobów.<br><br> Za pomocą [tagów usługi sieci wirtualnej](../virtual-network/service-tags-overview.md) można osiągnąć izolację sieci i chronić zasoby usługi Azure Cosmos DB przed ogólnym Internetem. Podczas tworzenia reguł zabezpieczeń należy używać tagów usług zamiast określonych adresów IP. Określając nazwę tagu usługi (na przykład AzureCosmosDB) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić lub odmówić ruchu dla odpowiedniej usługi.|
|Autoryzacja|Usługa Azure Cosmos DB używa kodu uwierzytelniania wiadomości opartego na skrótach (HMAC) do autoryzacji. <br><br>Każde żądanie jest mieszane przy użyciu klucza konta tajnego, a kolejne kodowane skróty base-64 są wysyłane z każdym wywołaniem usługi Azure Cosmos DB. Aby sprawdzić poprawność żądania, usługa Azure Cosmos DB używa poprawnego klucza tajnego i właściwości do generowania skrótu, a następnie porównuje wartość z wartością w żądaniu. Jeśli dwie wartości są zgodne, operacja jest pomyślnie autoryzowana i żądanie jest przetwarzane, w przeciwnym razie występuje błąd autoryzacji i żądanie zostanie odrzucone.<br><br>Można użyć [klucza głównego](secure-access-to-data.md#master-keys)lub [tokenu zasobu](secure-access-to-data.md#resource-tokens) umożliwiającego szczegółowy dostęp do zasobu, takiego jak dokument.<br><br>Dowiedz się więcej [na temat zabezpieczanie dostępu do zasobów usługi Azure Cosmos DB](secure-access-to-data.md).|
|Użytkownicy i uprawnienia|Za pomocą klucza głównego dla konta można tworzyć zasoby użytkownika i zasoby uprawnień na bazę danych. Token zasobu jest skojarzony z uprawnieniem w bazie danych i określa, czy użytkownik ma dostęp (odczyt,zapis, tylko do odczytu lub brak dostępu) do zasobu aplikacji w bazie danych. Zasoby aplikacji obejmują kontener, dokumenty, załączniki, procedury przechowywane, wyzwalacze i pliki UDF. Token zasobu jest następnie używany podczas uwierzytelniania w celu zapewnienia lub odmowy dostępu do zasobu.<br><br>Dowiedz się więcej [na temat zabezpieczanie dostępu do zasobów usługi Azure Cosmos DB](secure-access-to-data.md).|
|Integracja z usługą Active Directory (RBAC)| Można również zapewnić lub ograniczyć dostęp do konta usługi Cosmos, bazy danych, kontenera i ofert (przepływność) przy użyciu kontroli dostępu (IAM) w witrynie Azure portal. Usługa IAM zapewnia kontrolę dostępu opartą na rolach i integruje się z usługą Active Directory. Dla osób i grup można używać wbudowanych ról lub ról niestandardowych. Aby uzyskać więcej informacji, zobacz artykuł o [integracji usługi Active Directory.](role-based-access-control.md)|
|Replikacja globalna|Usługa Azure Cosmos DB oferuje globalną dystrybucję "pod klucz", która umożliwia replikowanie danych do dowolnego z centrów danych platformy Azure za pomocą kliknięcia przycisku. Replikacja globalna umożliwia skalowanie globalnie i zapewnia dostęp do danych o małym opóźnieniu na całym świecie.<br><br>W kontekście zabezpieczeń globalna replikacja zapewnia ochronę danych przed awariami regionalnymi.<br><br>Dowiedz się więcej z artykułu [Distribute data globally](distribute-data-globally.md) (Globalna dystrybucja danych).|
|Praca w trybie failover w regionach|Jeśli dane zostały zreplikowane w więcej niż jednym centrum danych, usługa Azure Cosmos DB automatycznie przejedzie operacje, jeśli regionalne centrum danych przejdzie w tryb offline. Można utworzyć priorytetową listę regionów trybu failover przy użyciu regionów, w których dane są replikowane. <br><br>Dowiedz się więcej w [regionalnych trybu failover w usłudze Azure Cosmos DB](high-availability.md).|
|Replikacja lokalna|Nawet w jednym centrum danych usługa Azure Cosmos DB automatycznie replikuje dane w celu uzyskania wysokiej dostępności, zapewniając wybór [poziomów spójności.](consistency-levels.md) Ta replikacja gwarantuje 99,99% [dostępności umowy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) dla wszystkich kont w jednym regionie i wszystkich kont wieloregionalnych z luźną spójnością i dostępnością odczytu 99,999% na wszystkich kontach bazy danych w wielu regionach.|
|Automatyczne tworzenie kopii zapasowych online|Bazy danych usługi Azure Cosmos są regularnie archiwizowane i przechowywane w magazynie geograficznie nadmiarowym. <br><br>Dowiedz się więcej w [automatycznej kopii zapasowej i przywracaniu online za pomocą usługi Azure Cosmos DB](online-backup-and-restore.md).|
|Przywracanie usuniętych danych|Automatyczne kopie zapasowe online mogą być używane do odzyskiwania danych, które mogły zostać przypadkowo usunięte do ~30 dni po zdarzeniu. <br><br>Dowiedz się więcej w [sprawie Automatyczna kopia zapasowa i przywracanie online za pomocą usługi Azure Cosmos DB](online-backup-and-restore.md)|
|Ochrona i izolowanie poufnych danych|Wszystkie dane w regionach wymienionych w co nowego? jest teraz szyfrowany w spoczynku.<br><br>Dane osobowe i inne poufne dane mogą być izolowane do określonego kontenera i odczytu i zapisu, lub dostęp tylko do odczytu może być ograniczony do określonych użytkowników.|
|Monitoruj ataki|Korzystając z [rejestrowania inspekcji i dzienników aktywności,](logging.md)możesz monitorować swoje konto pod kątem normalnej i nietypowej aktywności. Można wyświetlić, jakie operacje zostały wykonane na zasoby, kto zainicjował operację, gdy wystąpiła operacja, stan operacji i wiele więcej, jak pokazano na zrzucie ekranu po tej tabeli.|
|Reagowanie na ataki|Po skontaktowaniu się z pomocą techniczną platformy Azure w celu zgłoszenia potencjalnego ataku zostanie rozpoczęty 5-etapowy proces reagowania na incydenty. Celem 5-etapowego procesu jest przywrócenie normalnego bezpieczeństwa usługi i operacji tak szybko, jak to możliwe po wykryciu problemu i rozpoczęciu dochodzenia.<br><br>Dowiedz się więcej w [programie Microsoft Azure Security Response in the Cloud](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).|
|Ogrodzenia geograficzne|Usługa Azure Cosmos DB zapewnia zarządzanie danymi dla suwerennych regionów (na przykład Niemcy, Chiny, us Gov).|
|Obiekty chronione|Dane w usłudze Azure Cosmos DB są przechowywane na ssd w chronionych centrach danych platformy Azure.<br><br>Dowiedz się więcej w [globalnych centrach danych firmy Microsoft](https://www.microsoft.com/en-us/cloud-platform/global-datacenters)|
|Szyfrowanie HTTPS/SSL/TLS|Wszystkie połączenia z usługą Azure Cosmos DB obsługują protokół HTTPS. Usługa Azure Cosmos DB obsługuje również protokół TLS 1.2.<br>Istnieje możliwość wymuszenia minimalnej wersji TLS po stronie serwera. W tym celu [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com)należy skontaktować się z firmą .|
|Szyfrowanie w spoczynku|Wszystkie dane przechowywane w usłudze Azure Cosmos DB są szyfrowane w spoczynku. Dowiedz się więcej w [usłudze Azure Cosmos DB w spoczynku](./database-encryption-at-rest.md)|
|Poprawione serwery|Jako zarządzana baza danych usługi Azure Cosmos DB eliminuje konieczność zarządzania i instalowania poprawek serwerów, co odbywa się automatycznie.|
|Konta administracyjne z silnymi hasłami|Trudno uwierzyć, że nawet trzeba wspomnieć o tym wymaganiu, ale w przeciwieństwie do niektórych naszych konkurentów, nie można mieć konta administracyjnego bez hasła w usłudze Azure Cosmos DB.<br><br> Bezpieczeństwo za pośrednictwem TLS i uwierzytelniania opartego na tajnym kodze HMAC jest domyślnie pieczone.|
|Certyfikaty bezpieczeństwa i ochrony danych| Aby uzyskać najbardziej aktualną listę certyfikatów, zobacz ogólną [witrynę zgodności platformy Azure,](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) a także najnowszy [dokument zgodności platformy Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) ze wszystkimi certyfikatami (wyszukiwanie usługi Cosmos). Aby uzyskać bardziej skoncentrowany odczyt, zapoznaj się z postem z 25 kwietnia 2018 r. [Azure #CosmosDB: Bezpieczny, prywatny, zgodny z normami, który zawiera SOCS 1/2 Typu 2, HITRUST, PCI DSS Level 1, ISO 27001, HIPAA, FedRAMP High i wiele innych.

Na poniższym zrzucie ekranu przedstawiono, jak można ![używać rejestrowania inspekcji i dzienników aktywności do monitorowania konta: Dzienniki aktywności dla usługi Azure Cosmos DB](./media/database-security/nosql-database-security-application-logging.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat kluczy głównych i tokenów zasobów, zobacz [Zabezpieczanie dostępu do danych usługi Azure Cosmos DB](secure-access-to-data.md).

Aby uzyskać więcej informacji na temat rejestrowania inspekcji, zobacz [Rejestrowanie diagnostyczne usługi Azure Cosmos DB](logging.md).

Aby uzyskać więcej informacji o certyfikatach [firmy Microsoft,](https://azure.microsoft.com/support/trust-center/)zobacz Centrum zaufania platformy Azure .
