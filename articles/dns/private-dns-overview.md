---
title: Użyj usługi Azure DNS dla domen prywatnych
description: Przegląd prywatnej strefy DNS, obsługującego usługę w systemie Microsoft Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 3/1/2019
ms.author: victorh
ms.openlocfilehash: 7f5f377f34a43dfb01ea516e023bb98f118d0dd4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596554"
---
# <a name="use-azure-dns-for-private-domains"></a>Użyj usługi Azure DNS dla domen prywatnych

System nazw domen lub DNS, odpowiada za tłumaczenia (lub rozpoznawanie) nazwę usługi na jej adres IP. Usługa hostingowa przeznaczona dla domen DNS, usługa Azure DNS udostępnia rozpoznawanie nazw przy użyciu infrastruktury Microsoft Azure. Oprócz obsługi domeny DNS na dostępnym z Internetu, usługi Azure DNS teraz obsługuje również prywatne domen DNS jako funkcja w wersji zapoznawczej.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Usługa DNS platformy Azure zapewnia niezawodną i bezpieczną usługę DNS do zarządzania nazwami i rozpoznawania domeny w sieci wirtualnej bez konieczności dodawania niestandardowego rozwiązania DNS. Przy użyciu prywatnych stref DNS, można użyć nazwy domeny niestandardowej, a nie nazwy platformy Azure, które muszą być dostępne już dzisiaj. Przy użyciu niestandardowych nazw domen pomaga dostosować architektury sieci wirtualnej w zależności do potrzeb swojej organizacji. Oferuje ona rozpoznawanie nazw maszyn wirtualnych (VM) w sieci wirtualnej, a także między sieciami wirtualnymi. Ponadto można skonfigurować nazwy stref z widokiem split-horizon, co pozwala prywatnej i publicznej strefie DNS mieć nazwę.

Aby opublikować prywatną strefę DNS w sieci wirtualnej, musisz określić listę sieci wirtualnych, które mogą rozpoznawać rekordy w strefie. Są to tak zwane *sieci wirtualne rozpoznawania*. Możesz również określić sieć wirtualną, dla której usługa Azure DNS utrzymuje rekordy nazw hosta zawsze, gdy maszyna wirtualna jest tworzona, zmienia protokół internetowy lub jest usuwana. Jest to tak zwana *sieć wirtualna rejestracji*.

Jeśli określisz sieć wirtualną rejestracji, rekordy DNS dla maszyn wirtualnych z tej sieci wirtualnej, które są zarejestrowane do prywatnej strefy nie są widoczne i możliwe do pobierania z programu Azure Powershell i interfejsów API interfejsu wiersza polecenia platformy Azure, ale rekordy maszyny Wirtualnej w rzeczywistości są zarejestrowane i będzie rozwiązany pomyślnie.

![Omówienie systemu DNS](./media/private-dns-overview/scenario.png)

> [!NOTE]
> Najlepszym rozwiązaniem jest nieużywanie domeny .local dla prywatnej strefy DNS. Nie wszystkie systemy operacyjne obsługują to.

## <a name="benefits"></a>Korzyści

Usługa DNS platformy Azure zapewnia następujące korzyści:

* **Eliminuje konieczność stosowania niestandardowego rozwiązania DNS**. Wielu klientów utworzone wcześniej, niestandardowych rozwiązań DNS, aby zarządzać strefami DNS w sieci wirtualnej. Zarządzanie strefami DNS można teraz wykonywać za pomocą natywnej infrastruktury platformy Azure, co spowoduje usunięcie obciążeń tworzenia i zarządzania niestandardowego rozwiązania DNS.

* **Użyj wszystkie popularne typy rekordów DNS**. Usługa DNS platformy Azure obsługuje rekordów A, AAAA, CNAME, MX, PTR, SOA, SRV i TXT.

* **Automatyczne hostname zarządzania rekordami**. Wraz z hostingu niestandardowych rekordów DNS, Azure automatycznie utrzymuje rekordy nazw hosta dla maszyn wirtualnych w określonej sieci wirtualnej. W tym scenariuszu można zoptymalizować nazwy domeny, które umożliwia bez konieczności tworzenia niestandardowego rozwiązania DNS lub modyfikowania aplikacji.

* **Rozpoznawanie nazw hostów między sieciami wirtualnymi**. W odróżnieniu od nazwy hosta platformy Azure prywatnych stref DNS mogą być współużytkowane między sieciami wirtualnymi. Ta funkcja upraszcza scenariusze między siecią i odnajdowania usług, takie jak komunikacja równorzędna sieci wirtualnych.

* **Dobrze znane narzędzia i środowisko użytkownika**. Aby skrócić czas nauki, ta nowa oferta używa sprawdzone narzędzia usługi Azure DNS (PowerShell, szablony usługi Azure Resource Manager i interfejsu API REST).

* **Obsługa w systemie DNS split-horizon**. Dzięki usłudze Azure DNS można utworzyć strefy o tej samej nazwie, które nawiązują do różnych odpowiedzi z sieci wirtualnej i z publicznej sieci internet. Typowy scenariusz użycia split-horizon DNS jest zapewnienie dedykowanych wersji usługi do użytku w Twojej sieci wirtualnej.

* **Dostępne we wszystkich regionach platformy Azure**. Funkcja strefami prywatnymi usługi Azure DNS jest dostępna we wszystkich regionach platformy Azure w chmurze publicznej Azure.

## <a name="capabilities"></a>Możliwości

Usługa DNS platformy Azure zapewnia następujące możliwości:

* **Automatycznej rejestracji maszyn wirtualnych z jednej sieci wirtualnej, która jest połączona z prywatnej strefy jako sieć wirtualną rejestracji**. Maszyny wirtualne są zarejestrowane (dodany) do strefy prywatnej jako rekordy, wskazując ich prywatnych adresów IP. Maszynę wirtualną rejestracji z sieci wirtualnej zostanie usunięta w usłudze Azure również automatycznie usuwa odpowiedniego DNS rekord z połączonej strefa prywatna. 

  Domyślnie sieci wirtualne rejestracji również działać jako sieciami wirtualnymi rozpoznawania, w tym sensie, że działa rozpoznawanie nazw DNS dla strefy, pochodzących z dowolnych z maszyn wirtualnych w ramach sieci wirtualnych rejestracji.

  > [!NOTE]
  > Jeśli określisz sieć wirtualną rejestracji, rekordy DNS dla maszyn wirtualnych z tej sieci wirtualnej, które są zarejestrowane do prywatnej strefy nie są widoczne i możliwe do pobierania z programu Azure Powershell i interfejsów API interfejsu wiersza polecenia platformy Azure. Rekordy maszyny Wirtualnej w rzeczywistości są zarejestrowane i zostanie rozwiązany pomyślnie.

* **Do przodu rozpoznawania nazw DNS jest obsługiwana dla sieci wirtualnych, które są połączone z prywatnej strefy jako sieciami wirtualnymi rozpoznawania**. Dla sieci wirtualnej między rozpoznawania nazw DNS jest niezależne jawne taki sposób, że wirtualne sieci równorzędne ze sobą. Jednak klienci mogą chcą nawiązać komunikację równorzędną między sieciami wirtualnymi w innych sytuacjach (na przykład ruch HTTP).

* **Wyszukiwanie wsteczne DNS jest obsługiwana w ramach zakresu sieci wirtualnej**. Wyszukiwanie wsteczne DNS, aby uzyskać prywatny adres IP w sieci wirtualnej przypisany do prywatnej strefy zwraca nazwę FQDN, która zawiera nazwę hosta/rekordu i nazwę strefy, jako sufiks.

## <a name="limitations"></a>Ograniczenia

Usługa DNS platformy Azure ma następujące ograniczenia:

* Sieć wirtualną tylko jeden rejestracji jest dozwoloną na strefę prywatną.
* Maksymalnie 10 rozpoznawanie sieci wirtualne są dozwolone na strefę prywatną. Ten limit zostanie usunięta, gdy ta funkcja jest ogólnie dostępna.
* Określonej sieci wirtualnej można połączyć tylko jedną strefę prywatnego jako sieć wirtualną rejestracji.
* Określonej sieci wirtualnej można połączyć maksymalnie 10 stref prywatnych jako sieć wirtualną rozpoznawania. Ten limit zostanie usunięta, gdy ta funkcja jest ogólnie dostępna.
* Jeśli określisz sieć wirtualną rejestracji, rekordy DNS dla maszyn wirtualnych z tej sieci wirtualnej, które są zarejestrowane do prywatnej strefy nie są widoczne i możliwe do pobierania z programu Azure Powershell i interfejsów API interfejsu wiersza polecenia platformy Azure. Rekordy maszyny Wirtualnej w rzeczywistości są zarejestrowane i zostanie rozwiązany pomyślnie.
* Odwrotnego DNS działa tylko w przypadku prywatnej przestrzeni adresów IP w sieci wirtualnej rejestracji.
* Odwrotne DNS dla prywatnego adresu IP, który nie jest zarejestrowany w prywatnej strefy (na przykład, prywatny adres IP dla maszyny wirtualnej w sieci wirtualnej, która zostanie połączona jako sieć wirtualną rozpoznawania prywatnej strefy) zwraca *internal.cloudapp.net* sufiks DNS. Jednak ten sufiks nie można rozpoznać.
* Sieć wirtualna musi być całkowicie pusty po raz pierwszy możesz połączyć strefę prywatną jako sieć wirtualną rejestracji lub rozpoznawania. Jednak sieci wirtualnej następnie może być pusty dla przyszłych połączeń jako rejestracji lub rozpoznawania sieci wirtualnej, do innych stref prywatnych.
* Obecnie warunkowego przesyłania dalej nie jest obsługiwane (na przykład w przypadku włączania rozpoznawania między sieciami Azure i lokalnego). Aby dowiedzieć się, jak jak klienci mogą weź pod uwagę, w tym scenariuszu za pośrednictwem innych mechanizmów, zobacz [rozpoznawanie nazw dla maszyn wirtualnych i wystąpień roli](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

Typowe pytania i odpowiedzi na pytania dotyczące stref prywatnych w usłudze Azure DNS, w tym określone zachowanie Rejestracja i rozpoznawanie DNS można oczekiwać na niektóre rodzaje operacji, zobacz [— często zadawane pytania](./dns-faq.md#private-dns).  

## <a name="pricing"></a>Cennik

Funkcja prywatne strefy DNS jest bezpłatne w okresie publicznej wersji zapoznawczej. Ogólnie dostępnej wersji ta funkcja oferuje opartej na użyciu model cenowy podobny do istniejącej usługi Azure DNS oferty. 

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak utworzyć prywatną strefę w usłudze Azure DNS przy użyciu [programu Azure PowerShell](./private-dns-getstarted-powershell.md) lub [wiersza polecenia platformy Azure](./private-dns-getstarted-cli.md).

Przeczytaj o niektórych typowych [scenariusze prywatnej strefy](./private-dns-scenarios.md) , które można realizować ze strefami prywatnymi w usłudze Azure DNS.

Typowe pytania i odpowiedzi na pytania dotyczące stref prywatnych w usłudze Azure DNS, w tym określone zachowanie można oczekiwać na niektóre rodzaje operacji, zobacz [— często zadawane pytania](./dns-faq.md#private-dns). 

Więcej informacji na temat stref i rekordów DNS, odwiedzając [DNS strefy i rekordy Przegląd](dns-zones-records.md).

Poznaj inne kluczowe [możliwości sieciowe](../networking/networking-overview.md) platformy Azure.
