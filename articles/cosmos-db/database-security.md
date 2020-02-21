---
title: Zabezpieczenia bazy danych — Azure Cosmos DB
description: Dowiedz się, jak usługi Azure Cosmos DB udostępnia zabezpieczenia ochrony i danych bazy danych dla swoich danych.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 145473dfce3d9036b2262ccf3f65f570a2939ef3
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77524584"
---
# <a name="security-in-azure-cosmos-db---overview"></a>Zabezpieczenia w Azure Cosmos DB — Omówienie

W tym artykule omówiono najlepsze rozwiązania dotyczące zabezpieczeń baz danych i kluczowe funkcje oferowane przez usługę Azure Cosmos DB, które pomagają zapobiegać naruszeniom, wykrywać je i reagować na nie.

## <a name="whats-new-in-azure-cosmos-db-security"></a>Co nowego w zabezpieczeniach Azure Cosmos DB

Szyfrowanie w spoczynku jest teraz dostępna dla dokumentów i kopii zapasowych przechowywanych w usłudze Azure Cosmos DB we wszystkich regionach platformy Azure. Szyfrowanie w spoczynku, jest automatycznie stosowany dla istniejących i nowych klientów w tych regionach. Nie ma potrzeby konfigurowania; Możesz uzyskać takie samo opóźnienie wspaniałe, przepływności, dostępności i funkcjonalność jak przed w oparciu o dane, wiedząc, jest bezpieczny z szyfrowanie danych magazynowanych.

## <a name="how-do-i-secure-my-database"></a>Jak mogę zabezpieczyć moją bazę danych

Bezpieczeństwo danych jest wspólna odpowiedzialność między, klienta i dostawcy bazy danych. W zależności od dostawcy bazy danych, które wybierzesz ilość odpowiedzialność, które wykonuje mogą się różnić. Jeśli wybierzesz rozwiązania lokalnego, musisz podać wszystko — od ochronę punktów końcowych do zabezpieczenia fizyczne sprzętu — czyli nie łatwym zadaniem. Jeśli wybierzesz PaaS dostawcy bazy danych w chmurze takich jak Azure Cosmos DB, znacznie zmniejsza obszar zainteresowania. Na poniższym obrazie, które zostały zapożyczone przez [współużytkowane obowiązki firmy Microsoft w celu uzyskania](https://aka.ms/sharedresponsibility) oficjalnego dokumentu w chmurze, pokazuje, jak Twoja odpowiedzialność maleje z dostawcą PaaS, takim jak Azure Cosmos DB.

![Obowiązki klienta i bazy danych dostawcy:](./media/database-security/nosql-database-security-responsibilities.png)

Na powyższym diagramie przedstawiono ogólne chmury składniki zabezpieczeń, ale elementy trzeba martwić się o specjalnie dla używanego rozwiązania bazy danych? I jak porównać rozwiązań ze sobą?

Firma Microsoft zaleca z poniższej listy kontrolnej wymagania, na którym chcesz porównać systemy baz danych:

- Zabezpieczenia sieciowe i ustawienia zapory
- Uwierzytelnianie użytkowników i kontrolki użytkownika szczegółowe
- Możliwość Replikuj dane globalnie dla regionalnych awarii
- Możliwość przełączenia w tryb failover z jednego centrum danych do innego
- Replikacja danych lokalnych w centrum danych
- Kopie zapasowe danych automatyczne
- Przywracanie usuniętych danych z kopii zapasowych
- Ochrona i izolowania danych poufnych
- Monitorowanie ataków
- Odpowiadanie na ataki
- Możliwość dane geograficzne horyzont stosować się do ograniczeń nadzoru danych
- Ochrona fizycznych serwerów w centrach danych chronionych
- Certyfikaty

Chociaż może wydawać się oczywiste, ostatnie [naruszenia bazy danych na dużą skalę](https://thehackernews.com/2017/01/mongodb-database-security.html) przypominają nam o prostym, ale krytycznym znaczeniu dla następujących wymagań:

- Aktualizowane serwery z poprawkami
- Protokół HTTPS przez szyfrowanie domyślne/SSL
- Konta z uprawnieniami administracyjnymi przy użyciu silnych haseł

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>Jak usługa Azure Cosmos DB zabezpiecza moją bazę danych

Przyjrzyjmy się ponownie na powyższej liście — ile tych wymagań dotyczących zabezpieczeń w usłudze Azure Cosmos DB zapewnia? Każdy jednego.

Przyjrzyjmy się bliżej temu w każdej z nich szczegółów.

|Wymaganie dotyczące zabezpieczeń|Podejście do zabezpieczeń danych usługi Azure Cosmos DB|
|---|---|
|Bezpieczeństwo sieci|Za pomocą zapory adresów IP jest pierwszą warstwę ochrony w celu zabezpieczenia bazy danych. Usługa Azure Cosmos DB obsługuje zasady na podstawie kontroli dostępu opartych na protokole IP, obsługi zapory dla ruchu przychodzącego. Kontroli dostępu opartej na protokole IP są podobne do reguł zapory używanych przez tradycyjne systemy baz danych, ale są rozszerzane, dzięki czemu konto bazy danych usługi Azure Cosmos jest dostępne tylko z zatwierdzonego zestawu maszyn lub usług w chmurze. <br><br>Usługa Azure Cosmos DB umożliwia włączenie określonego adresu IP (168.61.48.0), zakresu adresów IP (168.61.48.0/8) i kombinacji adresów IP i zakresów. <br><br>Wszystkie żądania pochodzące z maszyn spoza tej listy dozwolonych są blokowane przez usługę Azure Cosmos DB. Żądania z zatwierdzonych maszyn i następnie usług w chmurze musi przejść proces uwierzytelniania, należy podać kontroli dostępu do zasobów.<br><br>Dowiedz się więcej w temacie [Obsługa zapory Azure Cosmos DB](firewall-support.md).|
|Autoryzacja|Usługa Azure Cosmos DB używa kodu uwierzytelniania wiadomości bazujących na skrótach (HMAC), do autoryzacji. <br><br>Każde żądanie jest wyznaczana wartość skrótu przy użyciu klucza tajnego konta, a kolejne skrótu zakodowany base-64 jest wysyłany z każdego wywołania do usługi Azure Cosmos DB. Aby zatwierdzić żądanie, usługi Azure Cosmos DB używa poprawny klucz tajny i właściwości do generowania wartości skrótu, a następnie porównuje wartości przy użyciu jednego w żądaniu. Jeśli dwie wartości są zgodne, operacja jest Autoryzacja powiodła się i żądanie jest przetwarzane, w przeciwnym razie występuje błąd autoryzacji, a żądanie zostanie odrzucone.<br><br>Można użyć [klucza głównego](secure-access-to-data.md#master-keys)lub [tokenu zasobu](secure-access-to-data.md#resource-tokens) , co pozwala uzyskać szczegółowy dostęp do zasobu, takiego jak dokument.<br><br>Dowiedz się więcej w temacie [Zabezpieczanie dostępu do zasobów Azure Cosmos DB](secure-access-to-data.md).|
|Użytkownicy i uprawnienia|Korzystając z klucza głównego dla konta, można tworzyć zasoby użytkownika i uprawnienia do poszczególnych baz danych. Token zasobu jest skojarzony z uprawnieniem w bazie danych i określa, czy użytkownik ma dostęp (do odczytu i zapisu, tylko do odczytu lub brak dostępu) do zasobu aplikacji w bazie danych. Zasoby aplikacji obejmują kontenera, dokumentów, załączniki, procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika. Token zasobu jest następnie używany podczas uwierzytelniania do zapewnienia zawartości lub odmawiające dostępu do zasobu.<br><br>Dowiedz się więcej w temacie [Zabezpieczanie dostępu do zasobów Azure Cosmos DB](secure-access-to-data.md).|
|Integracja usługi Active directory (RBAC)| Możesz także zapewnić lub ograniczyć dostęp do konta Cosmos, bazy danych, kontenera i ofert (przepływności) przy użyciu funkcji kontroli dostępu (IAM) w Azure Portal. Zarządzanie dostępem i Tożsamościami zapewnia kontroli dostępu opartej na rolach i integruje się z usługą Active Directory. Możesz użyć wbudowanych ról lub ról niestandardowych dla użytkowników indywidualnych i grup. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [integracji Active Directory](role-based-access-control.md) .|
|Replikacji globalnej|Usługa Azure Cosmos DB oferuje gotową do użycia dystrybucji globalnej, co pozwala na replikowanie danych do jednej centrów danych platformy Azure na całym świecie za pomocą kliknięcia przycisku. Globalnej replikacji umożliwia skalowanie globalne i zapewniają małe opóźnienia dostępu do danych na całym świecie.<br><br>W kontekście zabezpieczeń globalnej replikacji zapewnia ochronę danych przed regionalnymi awariami.<br><br>Dowiedz się więcej z artykułu [Distribute data globally](distribute-data-globally.md) (Globalna dystrybucja danych).|
|Wywoływania regionalnego trybu failover|Jeśli zostały zreplikowane dane w więcej niż jedno centrum danych, usługi Azure Cosmos DB automatycznie zestawia za pośrednictwem operacji powinno regionalnym centrum danych przejdą w tryb offline. Można utworzyć priorytetową listą regionów trybu failover przy użyciu określonych regionów, w których dane są replikowane. <br><br>Więcej informacji na temat [regionalnych trybu failover znajduje się w Azure Cosmos DB](high-availability.md).|
|Lokalnej replikacji|Nawet w ramach jednego centrum danych Azure Cosmos DB automatycznie replikuje dane pod kątem wysokiej dostępności, zapewniając wybór [poziomów spójności](consistency-levels.md). Ta replikacja gwarantuje dostępność na 99,99 [% dla wszystkich](https://azure.microsoft.com/support/legal/sla/cosmos-db) kont w jednym regionie, a wszystkie konta wieloregionowe ze spójną spójnością i 99,999% odczytu na wszystkich wieloregionowych kontach baz danych.|
|Zautomatyzowane tworzenie kopii zapasowej online|Kopie zapasowe baz danych usługi Azure Cosmos są regularnie tworzone i przechowywane w geograficznie nadmiarowym magazynie. <br><br>Dowiedz się więcej w temacie [Automatyczne tworzenie kopii zapasowych online i przywracanie za pomocą Azure Cosmos DB](online-backup-and-restore.md).|
|Przywracanie usuniętych danych|Automatyczne kopie zapasowe online może służyć do odzyskiwania danych, może przypadkowo usunięto maksymalnie 30 dni po wystąpieniu zdarzenia. <br><br>Dowiedz się więcej w temacie [Automatyczne tworzenie kopii zapasowych online i przywracanie za pomocą Azure Cosmos DB](online-backup-and-restore.md)|
|Ochrona i izolowania danych poufnych|Wszystkie dane w regionach na liście nowości? jest teraz szyfrowana w stanie spoczynku.<br><br>Dane osobowe i innych poufnych danych może być izolowane do określonego kontenera oraz odczytu i zapisu lub tylko do odczytu może być ograniczony do konkretnych użytkowników.|
|Monitor ataków|Korzystając z [rejestrowania inspekcji i dzienników aktywności](logging.md), można monitorować konto pod kątem normalnej i nieprawidłowej aktywności. Można wyświetlić, jakie operacje zostały wykonane względem zasobów, którzy zainicjował operację, gdy operacja Wystąpił stan operacji i znacznie bardziej, jak pokazano na zrzucie ekranu pod tą tabelą.|
|Odpowiedzi na ataki|Gdy nawiązano pomocy technicznej platformy Azure, aby zgłosić potencjalny atak procesu reagowania na zdarzenia kroku 5 rozpocznie się kompilowanie. Celem procesu kroku 5 jest przywrócenie normalnego użytkowania, zabezpieczenia i operacje możliwie jak najszybciej po wykryciu problemu i badanie została uruchomiona.<br><br>Dowiedz się więcej w [Microsoft Azure odpowiedzi na zabezpieczenia w chmurze](https://aka.ms/securityresponsepaper).|
|Grodzenia|Usługa Azure Cosmos DB gwarantuje nadzór nad danymi na niezależne regiony (na przykład, Niemcy, Chiny, USA).|
|Urządzenia chronione|Dane w usłudze Azure Cosmos DB są przechowywane na dyskach SSD w centrach danych chronionych na platformie Azure.<br><br>Dowiedz się więcej w [globalnych centrach danych firmy Microsoft](https://www.microsoft.com/en-us/cloud-platform/global-datacenters)|
|Szyfrowanie protokołu HTTPS/SSL/TLS|Wszystkie połączenia do Azure Cosmos DB obsługują protokół HTTPS. Azure Cosmos DB obsługuje również protokół TLS 1,2.<br>Istnieje możliwość wymuszenia minimalnej wersji protokołu TLS po stronie serwera. Aby to zrobić, skontaktuj się z [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com).|
|Szyfrowanie w spoczynku|Wszystkie dane przechowywane w usłudze Azure Cosmos DB są szyfrowane w stanie spoczynku. Dowiedz się więcej w temacie [szyfrowanie Azure Cosmos DB](./database-encryption-at-rest.md) w stanie spoczynku|
|Poprawionego serwerów|Jako zarządzana baza danych usługi Azure Cosmos DB eliminuje konieczność zarządzania i stosowanie poprawek do serwerów, która została wykonana, automatycznie.|
|Konta z uprawnieniami administracyjnymi przy użyciu silnych haseł|Jest trudno uwierzyć, musimy nawet wspomina o identyfikatorach tego wymagania, ale w przeciwieństwie do niektórych z naszych konkurentów nie jest możliwe jest konto administracyjne, bez hasła w usłudze Azure Cosmos DB.<br><br> Domyślnie jest wbudowanymi zabezpieczeń za pośrednictwem protokołu SSL i HMAC uwierzytelniania na podstawie klucza tajnego.|
|Certyfikaty ochrony bezpieczeństwa i danych| Najbardziej aktualną listę certyfikatów zawiera ogólna [Witryna zgodności platformy Azure](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) , a także najnowszy [dokument zgodności platformy Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) ze wszystkimi certyfikatami (Search for Cosmos). Aby uzyskać bardziej skoncentrowany odczyt, zapoznaj się z dniem 25 kwietnia 2018. [Azure #CosmosDB: Secure, Private, zgodny, który zawiera SOC 1/2 Type 2, HITRUST, PCI DSS Level 1, ISO 27001, HIPAA, FedRAMP High i wiele innych.

Poniższy zrzut ekranu pokazuje, jak można użyć rejestrowania inspekcji i dzienników aktywności do monitorowania konta: ![dzienników aktywności dla Azure Cosmos DB](./media/database-security/nosql-database-security-application-logging.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat kluczy głównych i tokenów zasobów, zobacz [Zabezpieczanie dostępu do danych Azure Cosmos DB](secure-access-to-data.md).

Aby uzyskać więcej informacji na temat rejestrowania inspekcji, zobacz [Azure Cosmos DB rejestrowanie diagnostyczne](logging.md).

Aby uzyskać więcej informacji o certyfikatach firmy Microsoft, zobacz [Centrum zaufania Azure](https://azure.microsoft.com/support/trust-center/).