---
title: Baza danych zabezpieczeń — bazy danych Azure rozwiązania Cosmos | Dokumentacja firmy Microsoft
description: Dowiedz się, jak bazy danych Azure rozwiązania Cosmos zapewnia ochronę i danych zabezpieczeń bazy danych dla danych.
keywords: bazy danych nosql, zabezpieczeń, ochrony informacji, bezpieczeństwo danych, szyfrowania bazy danych, ochrona bazy danych, zasady zabezpieczeń testowania zabezpieczeń
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: a02a6a82-3baf-405c-9355-7a00aaa1a816
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: sngun
ms.openlocfilehash: 2b0cb1ed92694a7859912dfe0339ef719c0d15ef
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="azure-cosmos-db-database-security"></a>Zabezpieczenia bazy danych w usłudze Azure DB rozwiązania Cosmos

W tym artykule omówiono najlepszych rozwiązań dotyczących zabezpieczeń bazy danych i najważniejsze funkcje oferowane przez bazy danych rozwiązania Cosmos Azure w celu zapobiegania, wykrywania i odpowiedzi w przypadku naruszenia bazy danych.
 
## <a name="whats-new-in-azure-cosmos-db-security"></a>What's new in Azure DB rozwiązania Cosmos zabezpieczeń?

Szyfrowanie magazynowanych jest teraz dostępna dla dokumentów i kopie zapasowe przechowywane w usłudze Azure DB rozwiązania Cosmos we wszystkich regionach platformy Azure. Szyfrowanie magazynowanych jest automatycznie stosowane do istniejących i nowych klientów z tych obszarów. Nie istnieje potrzeba konfigurowania; Pobierz tego samego opóźnienia doskonały, przepływności, dostępności i funkcje, co przed w oparciu o znajomość danych jest bezpieczne i bezpieczne z szyfrowaniem przechowywane.

## <a name="how-do-i-secure-my-database"></a>Jak zabezpieczyć bazy danych? 

Bezpieczeństwo danych jest udostępniony odpowiedzialność między, klienta i dostawcy bazy danych. W zależności od wybranego dostawcy bazy danych ilość odpowiedzialność wykonać może się różnić. Jeśli wybierzesz rozwiązania lokalnego, musisz podać wszystkie elementy z ochrony punktu końcowego na zabezpieczenia fizyczne sprzętu — czyli bez łatwe zadania. Decydując PaaS dostawcy bazy danych w chmurze takich jak bazy danych Azure rozwiązania Cosmos znacznie zmniejsza obszaru zainteresowania. Poniższy obraz, pobierają z firmy Microsoft [obowiązki udostępnionych chmury obliczeniowej](https://aka.ms/sharedresponsibility) oficjalny dokument przedstawia, jak Twoje odpowiedzialność zmniejsza się z dostawcą PaaS, takie jak bazy danych Azure rozwiązania Cosmos.

![Obowiązki klienta i bazy danych dostawcy:](./media/database-security/nosql-database-security-responsibilities.png)

Na powyższym diagramie przedstawiono ogólny chmury składniki zabezpieczeń, ale elementy czy trzeba martwić specjalnie dla własnego rozwiązania bazy danych? I jak porównać rozwiązań do siebie? 

Zaleca się z poniższej listy kontrolnej wymagania, na których należy porównywać systemów bazy danych:

- Zabezpieczenia sieci i ustawieniami zapory
- Uwierzytelnianie użytkownika i kontrolek użytkownika szczegółowe
- Zdolność do replikacji danych globalnie niepowodzeń regionalne
- Możliwość wykonywania pracy w trybie failover z centrum danych z jednego do innego
- Replikacja danych lokalnych w obrębie centrum danych
- Automatyczne kopie zapasowe
- Przywrócenie usuniętych danych z kopii zapasowych
- Ochrona i izolowanie danych poufnych
- Monitorowanie ataków
- Odpowiada na ataki
- Możliwość geograficznie ogranicznika danych do przestrzegania ograniczeń ładu danych
- Fizyczna ochrona serwerach w centrach danych chronionych

I chociaż wydaje się to oczywistym, ostatnie [naruszeń bazy danych na dużą skalę](http://thehackernews.com/2017/01/mongodb-database-security.html) Przypomnij nam prostą, ale krytyczne znaczenie następujące wymagania:
- Poprawiono serwerów, które są zawsze aktualne
- HTTPS przez szyfrowanie domyślne/SSL
- Konta z uprawnieniami administracyjnymi o silnych haseł

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>Jak bazy danych Azure rozwiązania Cosmos zabezpieczyć bazy danych?

Przyjrzyjmy się wstecz powyższej listy — jak wiele z tych wymagań zabezpieczeń bazy danych rozwiązania Cosmos Azure zapewnia? Każdy jedno.

Umożliwia pracę na każdej z nich szczegółowo.

|Wymaganie dotyczące zabezpieczeń|Metoda zabezpieczeń Azure DB rozwiązania Cosmos|
|---|---|---|
|Bezpieczeństwo sieci|Używanie zapory IP jest pierwszą warstwę ochrony w celu zabezpieczenia bazy danych. Azure DB rozwiązania Cosmos obsługuje zasady na kontroli dostępu na podstawie adresu IP dla strony Obsługa zapory dla ruchu przychodzącego. Kontroli dostępu opartej na protokole IP są podobne do reguł zapory używane przez systemy tradycyjne bazy danych, ale są one rozszerzane tak, aby konto bazy danych Azure DB rozwiązania Cosmos jest dostępna tylko w zatwierdzonym zbiór maszyny lub usługi w chmurze. <br><br>Azure DB rozwiązania Cosmos umożliwia włączenie określonego adresu IP (168.61.48.0), zakresu adresów IP (168.61.48.0/8) i kombinacje adresów IP i zakresów. <br><br>Wszystkie żądania pochodzące z komputerów spoza tej listy dozwolonych są blokowane przez bazy danych Azure rozwiązania Cosmos. Żądania z zatwierdzonych maszyn i następnie usługi w chmurze przeprowadzić proces uwierzytelniania należy podać kontroli dostępu do zasobów.<br><br>Dowiedz się więcej w [Obsługa zapory bazy danych Azure rozwiązania Cosmos](firewall-support.md).|
|Autoryzacja|Azure DB rozwiązania Cosmos używa kod uwierzytelniania wiadomości oparty na skrótu (HMAC) do autoryzacji. <br><br>Każdego żądania jest generowany skrót przy użyciu klucza tajnego konta, a kolejne skrótu zakodowanego base-64 wysyłane w przypadku każdego wywołania do bazy danych Azure rozwiązania Cosmos. Aby zatwierdzić żądanie, usługa bazy danych Azure rozwiązania Cosmos używa poprawny klucz tajny i właściwości do generowania wartości skrótu, a następnie porównuje wartości z elementem w żądaniu. Jeśli dwie wartości są takie same, operacja jest autoryzowany pomyślnie i przetworzyć żądania, w przeciwnym razie braku autoryzacji i żądanie zostało odrzucone.<br><br>Możesz użyć dowolnej [klucza głównego](secure-access-to-data.md#master-keys), lub [token zasobu](secure-access-to-data.md#resource-tokens) stosowanie szczegółowych dostęp do zasobów, takich jak dokumentu.<br><br>Dowiedz się więcej w [zabezpieczanie dostępu do zasobów bazy danych Azure rozwiązania Cosmos](secure-access-to-data.md).|
|Uprawnienia użytkowników i|Przy użyciu [klucza głównego](#master-key) dla konta, można utworzyć zasobów użytkowników i uprawnienia dla jednej bazy danych. A [token zasobu](#resource-token) jest skojarzony z uprawnień w bazie danych i określa, czy użytkownik ma dostęp (Odczyt i zapis, tylko do odczytu, lub Brak dostępu) do zasobów aplikacji w bazie danych. Zasoby aplikacji obejmują kolekcje, dokumenty, załączniki, procedur składowanych, wyzwalaczy i funkcji UDF. Token zasobu jest następnie używany podczas uwierzytelniania w celu świadczenia usług lub odmówić dostępu do zasobu.<br><br>Dowiedz się więcej w [zabezpieczanie dostępu do zasobów bazy danych Azure rozwiązania Cosmos](secure-access-to-data.md).|
|Integracja usługi Active directory (RBAC)| Można również zapewniają dostęp do konta bazy danych za pomocą kontroli dostępu (IAM) w portalu Azure, jak pokazano na zrzucie ekranu poniżej tej tabeli. IAM umożliwia kontroli dostępu opartej na rolach i integruje się z usługą Active Directory. Role wbudowane lub role niestandardowe można użyć dla użytkowników indywidualnych i grup, jak pokazano na poniższej ilustracji.|
|Globalne replikacji|Azure DB rozwiązania Cosmos oferuje gotowe globalne dystrybucji, który umożliwia replikowanie danych do dowolnego centrów danych na całym świecie platformy Azure z kliknięcie przycisku. Globalne replikacji umożliwia globalne skalowanie i małych opóźnieniach dostęp do danych na całym świecie.<br><br>W kontekście zabezpieczeń globalnych Replikacja zapewnia ochronę danych przed regionalnymi awariami.<br><br>Dowiedz się więcej w [globalnie dystrybucji danych](distribute-data-globally.md).|
|Regionalnej pracy w trybie Failover|Jeśli zostały zreplikowane dane w więcej niż jednego centrum danych, bazy danych rozwiązania Cosmos Azure automatycznie najedzie na operacje powinny centrum danych regionalnych przejścia do trybu offline. Można utworzyć listę regionów trybu failover przy użyciu regionów, w których dane są replikowane z określonymi priorytetami. <br><br>Dowiedz się więcej w [regionalnej pracy w trybie Failover w usłudze Azure DB rozwiązania Cosmos](regional-failover.md).|
|Lokalnej replikacji|Nawet w ramach jednego centrum danych, bazy danych rozwiązania Cosmos Azure automatycznie replikuje dane dotyczące wysokiej dostępności, umożliwiając wybór [poziomy spójności](consistency-levels.md). Gwarantuje to 99,99% [dostępność](https://azure.microsoft.com/support/legal/sla/cosmos-db) dla wszystkich kont w pojedynczym regionie i wszystkich kont w przypadku z rozluźnić spójności i 99,999% odczytu dostępności na wszystkich kontach w przypadku bazy danych.|
|Automatyczne kopie zapasowe online|Azure DB rozwiązania Cosmos baz danych są regularnie wykonywana kopia zapasowa i przechowywane w magazynie georedundant. <br><br>Dowiedz się więcej w [automatyczne kopie zapasowe online i przywracania bazy danych Azure rozwiązania Cosmos](online-backup-and-restore.md).|
|Przywracanie usuniętych danych|Automatyczne kopie zapasowe online może służyć do odzyskiwania danych mogą przypadkowo usunięto do ciągu 30 dni po zdarzeniu. <br><br>Dowiedz się więcej [automatyczne kopie zapasowe online i przywracania bazy danych Azure rozwiązania Cosmos](online-backup-and-restore.md)|
|Ochrona i izolowanie danych poufnych|Wszystkie dane w regionach wymienionych w [nowości?](#whats-new) teraz jest szyfrowane, gdy.<br><br>Dane osobowe i innych poufnych danych mogą być izolowane do określonej kolekcji i odczytu i zapisu lub dostęp tylko do odczytu można ograniczyć do określonych użytkowników.|
|Monitor łącznego czasu ataków|Za pomocą [rejestrowania i działania dzienniki inspekcji](logging.md), można monitorować konto dla typowych i nietypowych działań. Można wyświetlić, jakie operacje były wykonywane na zasoby, które zainicjować operacji, w przypadku operacji wystąpił stan operacji, a także bardziej pokazany na zrzucie ekranu poniżej tej tabeli.|
|Odpowiadanie na ataki|Gdy nawiązano pomocy technicznej platformy Azure, aby zgłosić potencjalny atak proces odpowiedzi na zdarzenia krok 5 zostało rozpoczęte. Celem procesu krok 5 jest przywrócenie normalnego użytkowania, zabezpieczenia i operacje możliwie jak najszybciej po zostanie wykryty problem i dochodzenia została uruchomiona.<br><br>Dowiedz się więcej w [Response zabezpieczeń firmy Microsoft Azure w chmurze](https://aka.ms/securityresponsepaper).|
|Grodzenia|Azure DB rozwiązania Cosmos zapewnia zarządzanie danymi i zgodności dla suwerennych regionów (na przykład Niemczech, Chinach, nam wersji dla instytucji rządowych).|
|Urządzenia chronione|Dane w usłudze Azure DB rozwiązania Cosmos są przechowywane na dyskach SSD w centrach danych chronionych na platformie Azure.<br><br>Dowiedz się więcej [Globalne centra danych firmy Microsoft](https://www.microsoft.com/en-us/cloud-platform/global-datacenters)|
|Szyfrowanie protokołu HTTPS/SSL/TLS|Wszystkie interakcje bazy danych rozwiązania Cosmos Azure Usługa klienta są SSL/TLS 1.2 stanie. Ponadto wszystkie replikacji wewnątrz centrum danych i między centrum danych jest SSL/TLS 1.2, wymuszane.|
|Szyfrowanie w spoczynku|Wszystkie dane przechowywane w bazie danych rozwiązania Cosmos Azure jest szyfrowane, gdy. Dowiedz się więcej [szyfrowania bazy danych Azure rozwiązania Cosmos w stanie spoczynku](.\database-encryption-at-rest.md)|
|Poprawioną serwerów|Jako zarządzane bazy danych bazy danych Azure rozwiązania Cosmos eliminuje konieczność zarządzania i poprawka serwerów, jaka została wykonana automatycznie.|
|Konta z uprawnieniami administracyjnymi o silnych haseł|Trudno jest podejrzeń, musimy jeszcze wspomina to wymaganie, lecz w odróżnieniu od niektórych naszych konkurencji nie jest możliwe jest konto administracyjne bez hasła w usłudze Azure DB rozwiązania Cosmos.<br><br> Zabezpieczenia za pośrednictwem protokołu SSL i HMAC tajny uwierzytelnianie oparte na jest rozszerzania w domyślnie.|
|Certyfikaty ochrony zabezpieczeń i danych|Zawiera bazę danych systemu Azure rozwiązania Cosmos [ISO 27001](https://www.microsoft.com/en-us/TrustCenter/Compliance/ISO-IEC-27001), [europejskiego modelu klauzule (EUMC)](https://www.microsoft.com/en-us/TrustCenter/Compliance/EU-Model-Clauses), i [HIPAA](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA) certyfikaty. Dodatkowe certyfikaty są w toku.|

Poniższy zrzut ekranu przedstawia integracji usługi Active directory (RBAC) za pomocą kontroli dostępu (IAM) w portalu Azure: ![kontrola (IAM) dostępu w portalu Azure - prezentacja zabezpieczeń bazy danych](./media/database-security/nosql-database-security-identity-access-management-iam-rbac.png)

Poniższy zrzut ekranu pokazuje, jak za pomocą dzienników inspekcji rejestrowania i działania monitorowania konta: ![działania w dziennikach bazy danych Azure rozwiązania Cosmos](./media/database-security/nosql-database-security-application-logging.png)

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat kluczy głównych i tokenów zasobów zobacz [zabezpieczanie dostępu do danych bazy danych Azure rozwiązania Cosmos](secure-access-to-data.md).

Aby uzyskać więcej informacji na temat rejestrowania inspekcji, zobacz [rejestrowania diagnostycznego bazy danych Azure rozwiązania Cosmos](logging.md).

Aby uzyskać więcej informacji o certyfikatach firmy Microsoft, zobacz [Centrum zaufania Azure](https://azure.microsoft.com/support/trust-center/).
