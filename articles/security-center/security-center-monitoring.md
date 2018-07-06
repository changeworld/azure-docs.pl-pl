---
title: Monitorowanie zabezpieczeń w usłudze Azure Security Center | Microsoft Docs
description: Informacje zawarte w tym artykule ułatwiają rozpoczęcie korzystania z funkcji monitorowania w usłudze Azure Security Center.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/26/2018
ms.author: terrylan
ms.openlocfilehash: 434b73d4625f86fab195dbda1fed9c841791f5b6
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37099463"
---
# <a name="security-health-monitoring-in-azure-security-center"></a>Monitorowanie kondycji zabezpieczeń w Centrum zabezpieczeń Azure
Informacje zawarte w tym artykule ułatwiają korzystanie z funkcji monitorowania w usłudze Azure Security Center w celu monitorowania zgodności z zasadami.

## <a name="what-is-security-health-monitoring"></a>Czym jest monitorowanie kondycji zabezpieczeń?
Monitorowanie jest często postrzegane jako obserwowanie i oczekiwanie na wystąpienie zdarzenia, aby można było zareagować na zaistniałą sytuację. Monitorowanie zabezpieczeń polega na wdrożeniu aktywnej strategii przeprowadzania inspekcji zasobów w celu zidentyfikowania systemów, które nie spełniają standardów organizacji lub są niezgodne z najlepszymi rozwiązaniami.

## <a name="monitoring-security-health"></a>Monitorowanie kondycji zabezpieczeń
Po włączeniu [zasad zabezpieczeń](security-center-policies.md) dla zasobów subskrypcji usługa Security Center analizuje zabezpieczenia zasobów, aby zidentyfikować potencjalne luki. Informacje o konfiguracji sieci są dostępne natychmiast. W zależności od liczby maszyn wirtualnych i komputerów z zainstalowanym agentem, zebranie informacji o konfiguracji maszyn wirtualnych i komputerów, takich jak stan aktualizacji zabezpieczeń i konfiguracja systemu operacyjnego, może potrwać godzinę lub więcej, zanim zostaną one udostępnione. Stan zabezpieczeń zasobów oraz informacje o problemach można wyświetlić w sekcji **Zapobieganie**. Listę tych problemów można również wyświetlić na kafelku **Zalecenia**.

Więcej informacji dotyczących stosowania zaleceń można znaleźć w temacie [Wdrażanie zaleceń dotyczących zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md).

W obszarze **Monitorowanie kondycji zasobu** można monitorować stan zabezpieczeń zasobów. W poniższym przykładzie widać, że na kafelkach poszczególnych zasobów (Obliczenia i aplikacje, Sieć, Bezpieczeństwo danych oraz Tożsamość i dostęp) jest wyświetlana łączna liczba zidentyfikowanych problemów.

![Kafelek Kondycja zabezpieczeń zasobów](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)


### <a name="monitor-compute--apps"></a>Monitorowanie obliczeń i aplikacji
Aby uzyskać więcej informacji, zobacz [Ochrona maszyn wirtualnych i aplikacji w usłudze Azure Security Center](security-center-virtual-machine-recommendations.md).

### <a name="monitor-virtual-networks"></a>Monitorowanie sieci wirtualnych
Kliknięcie kafelka **Sieć** powoduje otwarcie bloku **Sieć** zawierającego więcej szczegółowych informacji, jak pokazano na poniższym zrzucie ekranu:

![Blok Sieć](./media/security-center-monitoring/security-center-monitoring-fig9-new3.png)

#### <a name="networking-recommendations"></a>Zalecenia dotyczące sieci
Podobnie jak w przypadku informacji o kondycji zasobów maszyny wirtualnej, w górnej części widoczna jest skrócona lista problemów, a w dolnej — lista monitorowanych sieci.

Sekcja ze szczegółowymi danymi o stanie sieci zawiera listę potencjalnych problemów z zabezpieczeniami oraz [zalecenia](security-center-network-recommendations.md). Mogą wystąpić następujące problemy:

* Zapora nowej generacji (NGFW) nie jest zainstalowana
* Sieciowe grupy zabezpieczeń w podsieciach nie są włączone
* Sieciowe grupy zabezpieczeń w maszynach wirtualnych nie są włączone
* Ograniczanie dostępu zewnętrznego za pośrednictwem publicznego zewnętrznego punktu końcowego
* Punkty końcowe mające połączenie z Internetem w dobrej kondycji

Po kliknięciu zalecenia zostanie wyświetlone więcej szczegółów na temat zalecenia, jak pokazano w poniższym przykładzie:

![Szczegóły dotyczące zalecenia w bloku Sieć](./media/security-center-monitoring/security-center-monitoring-fig9-ga.png)

W tym przykładzie blok **Konfiguruj brakujące grupy zabezpieczeń sieci dla podsieci** zawiera listę podsieci i maszyn wirtualnych, dla których brakuje ochrony sieciowej grupy zabezpieczeń. Po kliknięciu podsieci, do której chcesz zastosować grupę zabezpieczeń sieci, zostanie otwarty blok **Wybieranie grupy zabezpieczeń sieci**. W tym miejscu możesz wybrać najbardziej odpowiednią sieciową grupę zabezpieczeń dla podsieci lub utworzyć nową sieciową grupę zabezpieczeń.

#### <a name="internet-facing-endpoints-section"></a>Sekcja Internet facing endpoints (Punkty końcowe umożliwiające dostęp do Internetu)
Sekcja **Punkty końcowe mające połączenie z Internetem** zawiera maszyny wirtualne, które są aktualnie skonfigurowane z użyciem punktu końcowego połączonego z Internetem, oraz ich bieżący stan.

![Maszyny wirtualne skonfigurowane z użyciem punktu końcowego połączonego z Internetem oraz stan](./media/security-center-monitoring/security-center-monitoring-fig10-ga.png)

Ta tabela zawiera nazwę punktu końcowego, która reprezentuje maszynę wirtualną, adres IP umożliwiający dostęp do Internetu oraz bieżący stan ważności dla sieciowej grupy zabezpieczeń i zapory nowej generacji. Informacje w tabeli są posortowane według ważności:

* Kolor czerwony (u góry): wysoki priorytet, konieczne jest natychmiastowe rozwiązanie problemu
* Kolor pomarańczowy: średni priorytet, konieczne jest możliwie szybkie rozwiązanie problemu
* Kolor zielony (ostatni): stan prawidłowy

#### <a name="networking-topology-section"></a>Sekcja Topologia sieci
Sekcja **Topologia sieci** zawiera zasoby przedstawione według hierarchii, jak pokazano na poniższym zrzucie ekranu:

![Hierarchiczny widok zasobów w sekcji Topologia sieci](./media/security-center-monitoring/security-center-monitoring-fig121-new4.png)

Informacje w tej tabeli (dotyczące maszyn wirtualnych i podsieci) zostały posortowane według ważności:

* Kolor czerwony (u góry): wysoki priorytet, konieczne jest natychmiastowe rozwiązanie problemu
* Kolor pomarańczowy: średni priorytet, konieczne jest możliwie szybkie rozwiązanie problemu
* Kolor zielony (ostatni): stan prawidłowy

W tym widoku topologii pierwszy poziom obejmuje pozycje [sieci wirtualne](../virtual-network/virtual-networks-overview.md), [bramy sieci wirtualnej](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) i [sieci wirtualne (klasyczne)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Drugi poziom obejmuje podsieci, natomiast trzeci poziom przedstawia maszyny wirtualne, które należą do tych podsieci. Prawa kolumna przedstawia bieżący stan sieciowej grupy zabezpieczeń dla tych zasobów, jak pokazano w poniższym przykładzie:

![Stan grupy zabezpieczeń sieci w sekcji Topologia sieci](./media/security-center-monitoring/security-center-monitoring-fig12-ga.png)

W dolnej części tego bloku znajdują się zalecenia dotyczące tej maszyny wirtualnej, co jest podobne do powyższego opisu. Kliknij zalecenie, aby dowiedzieć się więcej i ewentualnie zastosować odpowiednie środki kontroli lub konfigurację zabezpieczeń.

### <a name="monitor-data-security"></a>Monitorowanie bezpieczeństwa danych

Po kliknięciu pozycji **Bezpieczeństwo danych** w sekcji **Zapobieganie** zostanie otwarty blok **Zasoby danych** z zaleceniami dotyczącymi usług SQL i Storage. Przedstawia on także [zalecenia](security-center-sql-service-recommendations.md) dotyczące ogólnej kondycji bazy danych. Aby uzyskać więcej informacji dotyczących szyfrowania magazynu, przeczytaj artykuł [Enable encryption for Azure storage account in Azure Security Center](security-center-enable-encryption-for-storage-account.md) (Włączanie szyfrowania dla konta usługi Azure Storage w usłudze Azure Security Center).

![Zasoby danych](./media/security-center-monitoring/security-center-monitoring-fig13-newUI-2017.png)

W obszarze **Zalecenia SQL** można kliknąć dowolne zalecenie i uzyskać więcej szczegółów na temat dalszych działań w celu rozwiązania problemu. Poniższy przykład pokazuje rozszerzenie zalecenia **Inspekcja bazy danych i wykrywanie zagrożeń w bazach danych SQL**.

![Szczegółowe informacje o zaleceniach SQL](./media/security-center-monitoring/security-center-monitoring-fig14-ga-new.png)

Blok **Włączanie inspekcji i wykrywania zagrożeń dla baz danych SQL** zawiera następujące informacje:

* Lista baz danych SQL
* Serwer, na którym znajdują się bazy danych SQL
* Informacje o tym, czy to ustawienie jest dziedziczone z serwera lub czy jest unikatowe w tej bazie danych
* Bieżący stan
* Ważność problemu

Po kliknięciu bazy danych w celu zastosowania tego zalecenia zostanie otwarty blok **Inspekcja i wykrywanie zagrożeń**, jak pokazano na poniższym ekranie.

![Inspekcja i wykrywanie zagrożeń](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

Aby włączyć funkcję inspekcji, zaznacz pole wyboru **WŁĄCZONE** poniżej opcji **Inspekcja**.

### <a name="monitor-identity--access"></a>Monitorowanie tożsamości i dostępu

Aby uzyskać więcej informacji, zobacz [Monitorowanie tożsamości i dostępu w usłudze Azure Security Center](security-center-identity-access.md).

## <a name="see-also"></a>Zobacz też
W tym artykule przedstawiono sposób korzystania z funkcji monitorowania w usłudze Azure Security Center. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń Azure, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](security-center-policies.md) — informacje na temat konfigurowania ustawień zabezpieczeń w usłudze Azure Security Center.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w usłudze Azure Security Center](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure Security](http://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
