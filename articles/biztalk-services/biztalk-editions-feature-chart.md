---
title: Informacje na temat funkcji w wersjach usługi BizTalk Services | Microsoft Docs
description: 'Porównanie funkcji w różnych wersjach usługi BizTalk Services: Bezpłatne, Deweloper, podstawowa, standardowa i Premium MABS, WABS.'
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: c589629f-06b1-44bb-b8ca-1db71826ea59
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: d16bf73c1d9871e4de8940e4ab9bbd7ed0f39384
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2019
ms.locfileid: "57765632"
---
# <a name="biztalk-services-editions-chart"></a>BizTalk Services: Wykres przedstawiający wersje

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

W ramach usługi Azure BizTalk Services oferowane są różne wersje. Artykuł ułatwia określenie, która wersja jest odpowiednia do danego scenariusza i potrzeb związanych z prowadzeniem działalności biznesowej.

## <a name="compare-the-editions"></a>Porównanie wersji
**Bezpłatna (wersja zapoznawcza)**

Można tworzyć połączenia hybrydowe i nimi zarządzać. Połączenie hybrydowe to prosty sposób na łącznie witryny sieci Web Azure z systemem lokalnym, np. serwerem SQL Server.

**Developer**

Obejmuje przetwarzanie komunikatów połączeń hybrydowych, EAI i EDI za pomocą prostego w obsłudze portalu zarządzania dla partnerów handlowych oraz obsługę typowych schematów EDI i dynamicznego przetwarzania EDI zgodnie z normami X12 i AS2. Umożliwia tworzenie typowych scenariuszy EAI, w ramach których odbywa się łączenie usług w chmurze za pośrednictwem protokołów HTTP/S, REST, FTP, WCF i SFTP w celu odczytywania i zapisywania komunikatów.  Nawiązuj łączność z lokalnymi systemami LOB, korzystając z gotowych do użycia adapterów SAP, Oracle eBusiness, Oracle DB, Siebel oraz serwera SQL Server. Za pomocą środowiska skoncentrowany na deweloperach narzędzi programu Visual Studio umożliwiające łatwe projektowanie i wdrażanie. Usługa ograniczona do celów programowania i testowania, bez umowy dotyczącej poziomu usług (SLA).

**Podstawowa**

Obejmuje większość funkcji dla deweloperów oraz rozszerzone funkcje połączeń w zakresie połączeń hybrydowych, mostków EAI, umów EDI i BizTalk Adapter Pack. Oferuje wysoką dostępność i możliwość skalowania w ramach umowy dotyczącej poziomu usług (SLA).

**Standardowa**

Obejmuje wszystkie funkcje wersji podstawowej oraz rozszerzone funkcje połączeń w zakresie połączeń hybrydowych, mostków EAI, umów EDI i BizTalk Adapter Pack. Oferuje wysoką dostępność i możliwość skalowania w ramach umowy dotyczącej poziomu usług (SLA).

**Premium**

Obejmuje wszystkie funkcje wersji standardowej oraz rozszerzone funkcje połączeń w zakresie połączeń hybrydowych, mostków EAI, umów EDI i BizTalk Adapter Pack. Obejmuje również archiwizację, wysoką dostępność i możliwość skalowania w ramach umowy dotyczącej poziomu usług (SLA).

## <a name="editions-chart"></a>Wykres przedstawiający wersje
W poniższej tabeli przedstawiono różnice.

<table border="1">
<tr bgcolor="FAF9F9">
        <th></th>
        <th>Bezpłatna (wersja zapoznawcza)</th>
        <th>Developer</th>
        <th>Podstawowa</th>
        <th>Standardowa (Standard)</th>
        <th>Premium</th>
</tr>

<tr>
<td><strong>Cena początkowa</strong></td>
<td colspan="5"><a HREF="https://go.microsoft.com/fwlink/p/?LinkID=304011">Azure BizTalk Services — cennik</a> <br/><br/> <a HREF="https://azure.microsoft.com/pricing/calculator/?scenario=full">Kalkulator cen platformy Azure</a></td>
</tr>
<tr>
<td><strong>Domyślna konfiguracja minimalna</strong></td>
<td>1 jednostka bezpłatna</td>
<td>1 jednostka dla dewelopera</td>
<td>1 jednostka podstawowa</td>
<td>1 jednostka standardowa</td>
<td>1 jednostka Premium</td>
</tr>
<tr>
<td><strong>Skalowanie</strong></td>
<td>Brak skalowania</td>
<td>Brak skalowania</td>
<td>Tak, w przyrostach co 1 jednostkę podstawową</td>
<td>Tak, w przyrostach co 1 jednostkę standardową</td>
<td>Tak, w przyrostach co 1 jednostkę Premium</td>
</tr>
<tr>
<td><strong>Maksymalne dozwolone skalowanie w poziomie</strong></td>
<td>Brak skalowania</td>
<td>Brak skalowania</td>
<td>Maksymalnie 8 jednostek</td>
<td>Maksymalnie 8 jednostek</td>
<td>Maksymalnie 8 jednostek</td>
</tr>
<tr>
<td><strong>Mostki EAI na jednostkę</strong></td>
<td>Nie dołączono</td>
<td>25</td>
<td>25</td>
<td>125</td>
<td>500</td>
</tr>
<tr>
<td><strong>EDI, AS2</strong>
<br/><br/>
Zawiera umowy modułu TPM</td>
<td>Nie dołączono</td>
<td>Dołączono. 10 umów na jednostkę.</td>
<td>Dołączono. 50 umów na jednostkę.</td>
<td>Dołączono. 250 umów na jednostkę.</td>
<td>Dołączono. 1000 umów na jednostkę.</td>
</tr>
<tr>
<td><strong>Połączenia hybrydowe na jednostkę</strong></td>
<td>5</td>
<td>5</td>
<td>10</td>
<td>50</td>
<td>100</td>
</tr>
<tr>
<td><strong>Transfer danych połączenia hybrydowego (GB) na jednostkę</strong></td>
<td>5</td>
<td>5</td>
<td>50</td>
<td>250</td>
<td>500</td>
</tr>
<tr>
<td><strong>Połączenia usługi adaptera BizTalk z lokalnymi systemami LOB</strong></td>
<td>Nie dołączono</td>
<td>1 połączenie</td>
<td>2 połączenia</td>
<td>5 połączeń</td>
<td>25 połączeń</td>
</tr>
<tr>
<td align="left"><strong>Obsługiwane protokoły/systemy:</strong>
<ul>
<li>HTTP</li>
<li>HTTPS</li>
<li>FTP</li>
<li>SFTP</li>
<li>WCF</li>
<li>Magistrala usług (SB)</li>
<li>Obiekt bob Azure</li>
<li>Interfejsy API REST</li>
</ul>
</td>
<td>Nie dołączono</td>
<td>Dołączono</td>
<td>Dołączono</td>
<td>Dołączono</td>
<td>Dołączono</td>
</tr>
<tr>
<td><strong>Wysoka dostępność</strong>
<br/><br/>
Umowa dotycząca poziomu usług (SLA) znajduje się w artykule <a HREF="https://go.microsoft.com/fwlink/p/?LinkID=304011">Usługa BizTalk — cennik</a>.
</td>
<td>Nie dołączono</td>
<td>Nie dołączono</td>
<td>Dołączono</td>
<td>Dołączono</td>
<td>Dołączono</td>
</tr>
<tr>
<td><strong>Tworzenie kopii zapasowej i przywracanie</strong></td>
<td>Nie dołączono</td>
<td>Dołączono</td>
<td>Dołączono</td>
<td>Dołączono</td>
<td>Dołączono</td>
</tr>
<tr>
<td><strong>Śledzenie</strong></td>
<td>Nie dołączono</td>
<td>Dołączono</td>
<td>Dołączono</td>
<td>Dołączono</td>
<td>Dołączono</td>
</tr>
<tr>
<td><strong>Archiwizacja</strong><br/><br/>
Obejmuje odrzucenie bez otrzymania (NRR) i pobieranie śledzonych wiadomości</td>
<td>Nie dołączono</td>
<td>Dołączono</td>
<td>Nie dołączono</td>
<td>Nie dołączono</td>
<td>Dołączono</td>
</tr>
<tr>
<td><strong>Użycie kodu niestandardowego</strong></td>
<td>Nie dołączono</td>
<td>Dołączono</td>
<td>Dołączono</td>
<td>Dołączono</td>
<td>Dołączono</td>
</tr>
<tr>
<td><strong>Użycie przekształceń, w tym niestandardowych przekształceń XSLT</strong></td>
<td>Nie dołączono</td>
<td>Dołączono</td>
<td>Dołączono</td>
<td>Dołączono</td>
<td>Dołączono</td>
</tr>
</table>

> [!NOTE]
> W celu zapewnienia odporności na awarie sprzętu wysoka dostępność zakłada posiadanie wielu maszyn wirtualnych w ramach jednej jednostki BizTalk.
> 
> 

## <a name="faqs"></a>Często zadawane pytania
#### <a name="what-is-a-biztalk-unit"></a>Czym jest jednostka BizTalk?
„Jednostka” jest niepodzielnym poziomem wdrożenia usługi Azure BizTalk Services. Każda wersja zawiera jednostkę o określonej wydajności obliczeniowej i pamięci. Na przykład jednostka wersji podstawowej ma większą moc obliczeniową niż jednostka wersji dla deweloperów, a jednostka wersji standardowej ma większą moc obliczeniową niż jednostka wersji podstawowej itd. Jednostka jest również miarą skalowania usługi BizTalk.

#### <a name="what-is-the-difference-between-biztalk-services-and-azure-biztalk-vm"></a>Jaka jest różnica między usługą BizTalk Services i maszyną wirtualną usługi Azure BizTalk?
Usługa BizTalk Services zapewnia prawdziwą architekturę typu platforma jako usługa (PaaS) do tworzenia rozwiązań integracji w chmurze. W przypadku modelu PaaS użytkownik skupia się całkowicie na logice aplikacji i pozostawia całe zarządzanie infrastrukturą firmie Microsoft:

* nie trzeba zarządzać maszynami wirtualnymi ani ich poprawiać.
* Firma Microsoft zapewnia dostępność.
* Skalowanie sprowadza się do żądania mniejszej lub większej wydajności za pośrednictwem portalu Azure.

Usługa BizTalk Server na maszynach wirtualnych Azure zapewnia architekturę typu infrastruktura jako usługa (IaaS). Tworzysz maszyny wirtualne i konfigurujesz je dokładnie tak jak środowisko lokalne, ułatwiając uruchamianie istniejących aplikacji w chmurze bez zmian w kodzie. W usłudze IaaS nadal odpowiadasz za konfigurowanie maszyn wirtualnych, zarządzanie nimi (np. instalację oprogramowania i poprawek systemu operacyjnego) oraz tworzenie architektury aplikacji w celu zapewnienia wysokiej dostępności.

Jeśli chcesz tworzyć nowe rozwiązania integracji, które zminimalizują wysiłek związany z zarządzaniem infrastrukturą, użyj usługi BizTalk Services. Jeśli chcesz szybko migrować istniejące rozwiązania BizTalk lub szukasz środowiska na żądanie do programowania i testowania aplikacji BizTalk Server, użyj usługi BizTalk Server na maszynie wirtualnej Azure.

#### <a name="what-is-the-difference-between-biztalk-adapter-service-and-hybrid-connections"></a>Jaka jest różnica między usługą adaptera BizTalk i połączeniami hybrydowymi?
Usługa adaptera BizTalk jest używana przez usługę Azure BizTalk. Usługa adaptera BizTalk używa rozwiązania BizTalk Adapter Pack do łączenia z lokalnym systemem Line of Business (LOB). Połączenie hybrydowe zapewnia łatwy i wygodny sposób łączenia aplikacji Azure, takich jak funkcja Web Apps w usługach Azure App Service i Azure Mobile Services, z zasobami lokalnymi.

#### <a name="what-does-hybrid-connection-data-transfer-gb-per-unit-mean-is-this-per-minutehourdayweekmonth-what-happens-when-the-limit-is-reached"></a>Co oznacza „Transfer danych połączenia hybrydowego (GB) na jednostkę”? Czy jednostka jest liczona co minutę/godzinę/dzień/tydzień/miesiąc? Co się stanie po osiągnięciu limitu?
Koszt połączenia hybrydowego na jednostkę zależy od wersji usługi BizTalk Services. Krótko mówiąc, koszty zależą od ilości przesyłanych danych. Na przykład transfer 10 GB danych dziennie kosztuje mniej niż transfer 100 GB danych dziennie. Użyj [Kalkulatora cen](https://azure.microsoft.com/pricing/calculator/?scenario=full) usługi BizTalk Services, aby obliczyć określone koszty. Limity są zazwyczaj dzienne. Po przekroczeniu limitu opłaty są naliczane według stawki 1 USD za GB.

#### <a name="when-i-create-an-agreement-in-biztalk-services-why-does-the-number-of-bridges-go-up-by-two-instead-of-just-one"></a>Dlaczego podczas tworzenia umowy w usłudze BizTalk Services liczba mostków zwiększa się o 2 zamiast o 1?
Każda umowa składa się z dwóch różnych mostków: mostka komunikacji po stronie wysyłania i mostka komunikacji po stronie odbierania.

#### <a name="what-happens-when-i-hit-the-quota-limit-on-the-number-of-bridges-or-agreements"></a>Co się stanie, gdy osiągnę limit przydziału liczby mostków lub umów?
Nie możesz wówczas wdrożyć żadnych nowych mostków ani utworzyć żadnych nowych umów. Aby wdrożyć ich więcej, musisz skalować w górę do większej liczby jednostek usługi BizTalk lub uaktualnić do nowszej wersji.

#### <a name="how-do-i-migrate-from-one-tier-of-biztalk-services-to-another"></a>Jak migrować z jednej warstwy usługi BizTalk Services do innej?
Bezpłatnej wersji nie można migrować ani „skalować w górę” do innej warstwy i nie można utworzyć jej kopii zapasowej ani przywrócić do innej warstwy. Jeśli potrzebujesz innej warstwy, utwórz nową usługę BizTalk przy użyciu nowej warstwy. Wszelkie artefakty utworzone przy użyciu wersji bezpłatnej, w tym połączenia hybrydowe, należy odtworzyć w nowej usłudze BizTalk. 

W przypadku pozostałych wersji użyj funkcji kopii zapasowej i przywracania do migracji artefaktów z jednej warstwy do innej. Na przykład utwórz kopię zapasową artefaktów w warstwie wersji standardowej, a następnie przywróć je do warstwy wersji Premium. [BizTalk Services: Kopia zapasowa i przywracanie](biztalk-backup-restore.md) opisano obsługiwane ścieżki migracji i wyświetla, jakie artefakty kopie zapasowe są tworzone. Pamiętaj, że nie tworzy się kopii zapasowej połączeń hybrydowych. Po utworzeniu kopii zapasowej i przywróceniu do nowej warstwy należy ponownie utworzyć połączenia hybrydowe.  

#### <a name="is-the-biztalk-adapter-service-included-in-the-service-how-do-i-receive-the-software"></a>Czy usługa adaptera BizTalk jest dołączona do usługi? Jak otrzymać oprogramowanie?
Tak, usługa adaptera BizTalk razem z usługą BizTalk Adapter Pack są dołączone do plików [do pobrania](https://www.microsoft.com/download/details.aspx?id=39087) zestawu SDK usługi Azure BizTalk Services.

## <a name="next-steps"></a>Kolejne kroki
Aby utworzyć usługi Azure BizTalk Services w witrynie Azure portal, przejdź do [usługi BizTalk Services: Inicjowanie obsługi administracyjnej za pomocą witryny Azure portal](biztalk-provision-services.md). Aby rozpocząć tworzenie aplikacji, przejdź do artykułu [Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=235197) (Usługa Azure BizTalk Services).

## <a name="additional-resources"></a>Dodatkowe zasoby
* [BizTalk Services: Inicjowanie obsługi administracyjnej za pomocą witryny Azure portal](biztalk-provision-services.md)<br/>
* [BizTalk Services: Wykres stanu aprowizacji](biztalk-service-state-chart.md)<br/>
* [BizTalk Services: Karty Pulpit nawigacyjny, Monitor i Skala](biztalk-dashboard-monitor-scale-tabs.md)<br/>
* [BizTalk Services: Kopia zapasowa i przywracanie](biztalk-backup-restore.md)<br/>
* [BizTalk Services: Ograniczanie przepustowości](biztalk-throttling-thresholds.md)<br/>
* [BizTalk Services: Nazwa wystawcy i klucz wydawcy](biztalk-issuer-name-issuer-key.md)<br/>
* [Jak rozpocząć pracę z zestawem SDK usługi Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>

