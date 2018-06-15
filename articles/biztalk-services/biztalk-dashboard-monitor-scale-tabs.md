---
title: Pulpit nawigacyjny, monitora, skalowania, skonfigurować i połączeń hybrydowych w usługi BizTalk Services | Dokumentacja firmy Microsoft
description: Więcej informacji na temat kontroli i monitorowania wydajności dla usługi BizTalk Services
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: 7a1815db-0de2-4274-8be0-198c1b077324
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 351809cd5f165a863dc02bfadf78fa59cbaabfd7
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2017
ms.locfileid: "24103365"
---
# <a name="review-the-dashboard-monitor-scale-configure-and-hybrid-connection-tabs"></a>Przegląd kart Pulpit nawigacyjny, Monitorowanie, Skala, Konfigurowanie i Połączenie hybrydowe

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Po utworzeniu usługi BizTalk i wdrażania aplikacji, można zmienić niektóre ustawienia usługi BizTalk i monitorowania wydajności aplikacji. 

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

Spowoduje to otwarcie nowego okna zawierająca poniższe karty. W tym temacie opisano te karty.

## <a name="quickstart-quickstartquickstart"></a>(Szybki Start![Szybki start][Quickstart])
W zależności od wersji usługi BizTalk wszystkie opcje wymienione mogą nie być dostępne. 

<table border="1">
    <tr>
        <td><strong>Pobierz narzędzia</strong></td>
        <td>Pobierz zestaw SDK usługi BizTalk do zainstalowania na komputerze deweloperskim lokalnymi szablony projektu Visual Studio. Te szablony tworzą <strong>usługi BizTalk Services</strong> (mostek) i <strong>artefaktów usługi BizTalk</strong> projektów programu Visual Studio (Transform), które są wdrażane do usługi BizTalk.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302335">Jak uruchomić, przy użyciu zestawu SDK usługi Azure BizTalk </a> i <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=241589">Instalowanie zestawu SDK usługi Azure BizTalk</a> zawiera listę czynności, aby rozpocząć pracę.
        </td>
    </tr>
    <tr>
        <td><strong>Tworzenie partnerów, umów</strong></td>
        <td>Otwiera hostowanej na platformie Azure, gdzie Dodawanie partnerami i tworzenie X12, AS2, portalu usługi Azure BizTalk i umów EDIFACT EDI.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Konfigurowanie składników EDI obsługi wiadomości w portalu usługi BizTalk</a> zawiera listę czynności, aby rozpocząć pracę.
        </td>
    </tr>

<tr>
        <td><strong>Dowiedz się więcej na temat usługi BizTalk Services</strong></td>
        <td>Przejdź do <a HREF="http://azure.microsoft.com/documentation/services/biztalk-services/">Centrum uczenia</a> Aby dowiedzieć się więcej o usługach BizTalk Azure.</td>
</tr>
</table>


Na pasku zadań u dołu można:

<table border="1">

<tr>
<td><strong>Zarządzanie</strong> wdrażania aplikacji</td>
<td>Otwiera portalu Azure usługi BizTalk. Portal usługi BizTalk jest wejścia do konfiguracji EDI, w tym dodawanie partnerami i tworzenie X12, AS2 oraz umów EDIFACT.
<br/><br/>
To jest taka sama jak <strong>Tworzenie umów z partnerami</strong> na <strong>Szybki Start</strong> kartę.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Konfigurowanie składników EDI obsługi wiadomości w portalu usługi BizTalk</a> zamieszczono więcej informacji dotyczących portalu usługi BizTalk.</td>
</tr>

<tr>
<td><strong>Informacje o połączeniu</strong> z Namespace kontroli dostępu</td>
<td>Po wybraniu informacje o połączeniu, następnie Namespace kontroli dostępu, domyślne wystawcy i klucza domyślne są wyświetlane. Możesz skopiować te wartości.
<br/><br/>
Można również otworzyć Portal kontroli dostępu. <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Utwórz kontroli dostępu Namespace</a> zamieszczono więcej informacji dotyczących portalu kontroli dostępu.</td>
</tr>

<tr>
<td><strong>Synchronizowanie kluczy</strong> na koncie magazynu</td>
<td>Podczas tworzenia konta usługi Storage następuje automatyczne utworzenie klucza podstawowego i klucza pomocniczego. Te klucze szyfrowania kontroli dostępu do konta magazynu. Usługi BizTalk automatycznie korzysta z klucza podstawowego. <strong>Synchronizowanie kluczy</strong> użytkownicy mogą przełączać się między klucz podstawowy i klucz pomocniczy bez zakłócania działania usługi BizTalk.
<br/><br/>
Możesz na przykład usługi BizTalk do nowego podstawowego klucza dla konta magazynu. W tym celu:
<br/><br/>
<ol>
<li>Wybierz usługę BizTalk i wybierz <strong>klucze synchronizacji</strong>. Wybierz klucz pomocniczy. Po wykonaniu tej czynności usługa BizTalk jest uruchamiana za pomocą klucza pomocniczego.</li>
<li>Wybierz konto magazynu i ponowne wygenerowanie klucza podstawowego. Należy pamiętać, że usługi BizTalk używa klucza pomocniczego.</li>
<li>Wybierz usługę BizTalk i wybierz <strong>klucze synchronizacji</strong>. Teraz wybierz klucz podstawowy. Jest to nowy klucz podstawowy zostanie ponownie wygenerowany.</li>
<li>Wybierz konto magazynu i ponowne wygenerowanie klucza pomocniczego.</li>
</ol>
<br/>
Ten proces jest nazywany "przerzucania kluczy". Celem jest umożliwienie użytkownikom przełączać się między klucz podstawowy i klucz pomocniczy bez zakłócania działania usługi BizTalk.</td>
</tr>

<tr>
<td><strong>Usuń</strong> aplikacji</td>
<td>Po wybraniu usunąć usługi BizTalk i zostaną usunięte wszystkie elementy w nim wdrożona.</td>
</tr>
</table>


## <a name="dashboard"></a>Pulpit nawigacyjny
W zależności od wersji usługi BizTalk wszystkie opcje wymienione mogą nie być dostępne. 

Po wybraniu nazwy usługi BizTalk karty Pulpit nawigacyjny jest wyświetlany. Na pulpicie nawigacyjnym można:

##### <a name="usage-overview-shows-the-number-of-used-hybrid-connections"></a>Przegląd wykorzystania: Pokazuje liczbę używanych połączeń hybrydowych
Dane użycia są również wyświetlane w GB. 

##### <a name="metric-graph-shows-a-fixed-list-of-performance-metrics"></a>Wykres metryki: Lista stałej metryki wydajności
Te metryki Podaj wartości w czasie rzeczywistym dotyczące kondycji usługi BizTalk. Można również wybrać **względną** lub **bezwzględną** wartości i zakres czasu **interwał** metryk, które są wyświetlane na wykresie. 

Aby uzyskać opis tych metryk wydajności, przejdź do [dostępne metryki](#Metrics) w tym temacie.

##### <a name="quick-glance-lists-your-biztalk-service-properties"></a>Szybkiego dostępu: Wyświetla listę właściwości usługi BizTalk
<table border="1">

<tr>
<td><strong>Zaktualizuj poświadczenia śledzenia bazy danych</strong></td>
<td>Zmienia nazwę użytkownika i hasło używane do logowania do bazy danych śledzenia.</td>
</tr>
<tr>
<td><strong>Aktualizuj certyfikat protokołu SSL</strong></td>
<td>Można zaktualizować usługi BizTalk, aby użyć innego certyfikatu SSL. Certyfikat SSL z podpisem własnym jest tworzony automatycznie, gdy użytkownik <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Utwórz usługę BizTalk</a>.</td>
</tr>
<tr>
<td><strong>Pobierz certyfikat</strong></td>
<td>Możesz pobrać certyfikat SSL używany przez usługę BizTalk do komputera lokalnego.</td>
</tr>
<tr>
<td><strong>Stan</strong></td>
<td>Wyświetla bieżący stan usługi BizTalk. Zobacz <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=329870">usługi BizTalk Services: Usługa stanu wykresu</a>. </td>
</tr>
<tr>
<td><strong>Adres URL usługi</strong></td>
<td>Adres URL dla usługi BizTalk. To jest taka sama jak <strong>adresu URL domeny</strong> wprowadzane podczas tworzenia usługi BizTalk.</td>
</tr>
<tr>
<td><strong>Adres publiczny wirtualnego adresu IP (VIP)</strong></td>
<td>Adres IP przypisany do usługi BizTalk. Jest on używany dla wszystkich wejściowych punktów końcowych i jest adresu źródłowego dla ruchu wychodzącego. Ten adres IP należy do usługi BizTalk, pod warunkiem jego tworzenia. Usunięcie usługi BizTalk, adres IP jest przypisany do innej usługi BizTalk.</td>
</tr>
<tr>
<td><strong>Namespace ACS</strong></td>
<td>Uwierzytelnianie przy użyciu usługi BizTalk.</td>
</tr>
<tr>
<td><strong>Wersja</strong></td>
<td>Wyświetla wersję wprowadzane podczas tworzenia usługi BizTalk.</td>
</tr>
<tr>
<td><strong>Lokalizacja</strong></td>
<td>Wyświetla region geograficzny, który jest hostem usługi BizTalk.</td>
</tr>
<tr>
<td><strong>Utworzone</strong></td>
<td>Wyświetla datę i godzinę utworzenia usługi BizTalk.</td>
</tr>
<tr>
<td><strong>Śledzenie bazy danych</strong></td>
<td>Nazwa bazy danych SQL Azure, która przechowuje tabele śledzenia używane przez usługi BizTalk. 
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Wymagania dotyczące poradnik</a> zawiera szczegółowe informacje w bazie danych śledzenia.</td>
</tr>
<tr>
<td><strong>Monitorowanie archiwizacji magazynu</strong></td>
<td>Nazwa konta usługi Azure Storage przechowuje dane wyjściowe monitorowania usługi BizTalk.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Wymagania dotyczące poradnik</a> zawiera szczegółowe informacje na koncie magazynu.</td>
</tr>
<tr>
<td><strong>Nazwa subskrypcji</strong></td>
<td>Wyświetla listę subskrypcji, który jest hostem usługi BizTalk. Subskrypcja podlega dostępu.</td>
</tr>
<tr>
<td><strong>Identyfikator subskrypcji</strong></td>
<td>Identyfikator subskrypcji jest generowany automatycznie podczas tworzenia subskrypcji. Podczas korzystania z interfejsów API REST, należy podać nazwę subskrypcji.</td>
</tr>
</table>

[Usługi BizTalk Services: Inicjowanie obsługi administracyjnej](http://go.microsoft.com/fwlink/p/?LinkID=302280) zawiera listę czynności w celu utworzenia usługi BizTalk.

##### <a name="manage-connection-information-sync-keys-and-delete-in-the-task-bar"></a>Zarządzanie, informacje o połączeniu, klucze synchronizacji i Usuń na pasku zadań:
<table border="1">

<tr>
<td><strong>Zarządzanie</strong> wdrażania aplikacji</td>
<td>Otwiera portalu usługi Azure BizTalk. Portal usługi BizTalk jest wejścia do konfiguracji EDI, w tym dodawanie partnerami i tworzenie X12, AS2 oraz umów EDIFACT.
<br/><br/>
To jest taka sama jak <strong>Tworzenie umów z partnerami</strong> na <strong>Szybki Start</strong> kartę.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Konfigurowanie składników EDI obsługi wiadomości w portalu usługi BizTalk</a> zamieszczono więcej informacji dotyczących portalu usługi BizTalk.</td>
</tr>
<tr>
<td><strong>Informacje o połączeniu</strong> z Namespace kontroli dostępu</td>
<td>Wyświetla Namespace kontroli dostępu, domyślne wystawcy i klucza domyślne wartości. które mogą zostać skopiowane.
<br/><br/>
Można również otworzyć Portal kontroli dostępu. Ten Portal kontroli dostępu jest taka sama jak przy użyciu opcji usługi Active Directory, w lewym okienku nawigacji.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Zarządzanie Your Namespace ACS</a> zamieszczono więcej informacji dotyczących portalu kontroli dostępu.</td>
</tr>
<tr>
<td><strong>Synchronizowanie kluczy</strong> na koncie magazynu</td>
<td>Podczas tworzenia konta usługi Storage następuje automatyczne utworzenie klucza podstawowego i klucza pomocniczego. Te klucze szyfrowania kontroli dostępu do konta magazynu. Usługi BizTalk automatycznie korzysta z klucza podstawowego. <strong>Synchronizowanie kluczy</strong> użytkownicy mogą przełączać się między klucz podstawowy i klucz pomocniczy bez zakłócania działania usługi BizTalk.
<br/><br/>
Możesz na przykład usługi BizTalk do nowego podstawowego klucza dla konta magazynu. W tym celu:
<br/><br/>
<ol>
<li>Wybierz usługę BizTalk i wybierz <strong>klucze synchronizacji</strong>. Wybierz klucz pomocniczy. Po wykonaniu tej czynności usługa BizTalk jest uruchamiana za pomocą klucza pomocniczego.</li>
<li>Wybierz konto magazynu i ponowne wygenerowanie klucza podstawowego. Należy pamiętać, że usługi BizTalk używa klucza pomocniczego.</li>
<li>Wybierz usługę BizTalk i wybierz <strong>klucze synchronizacji</strong>. Teraz wybierz klucz podstawowy. Jest to nowy klucz podstawowy zostanie ponownie wygenerowany.</li>
<li>Wybierz konto magazynu i ponowne wygenerowanie klucza pomocniczego.</li>
</ol>
<br/>
Ten proces jest nazywany "przerzucania kluczy". Celem jest umożliwienie użytkownikom przełączać się między klucz podstawowy i klucz pomocniczy bez zakłócania działania usługi BizTalk.</td>
</tr>

<tr>
<td><strong>Usuń</strong> aplikacji</td>
<td>Usługi BizTalk i wszystkie elementy w nim wdrożona zostały usunięte.</td>
</tr>
</table>


## <a name="monitor"></a>Monitorowanie
Nie ma zastosowania do bezpłatna wersja.

Po wybraniu nazwy usługi BizTalk karcie Monitor jest dostępne i zostaną wyświetlone następujące informacje:

##### <a name="metric-graph-displays-the-selected-performance-metrics"></a>Wykres metryki: Wyświetla metryki wydajności wybranych
Te metryki Podaj wartości w czasie rzeczywistym dotyczące kondycji usługi BizTalk. Możesz wybrać metryki wydajności, które są wyświetlane. Maksymalnie sześć metryki wydajności mogą być jednocześnie wyświetlane. 

Można również wybrać **względną** lub **bezwzględną** wartości i zakres czasu **interwał** metryk, które są wyświetlane. 

##### <a name="to-remove-or-display-metrics-in-the-graph"></a>Aby usunąć lub wyświetlania metryk na wykresie:
1. Wybierz **Monitor** kartę.
2. Wybierz **dodać metryki** na pasku zadań:  
   ![Wybierz opcję Dodaj metryk][AddMetrics]
3. Sprawdź metryki wydajności, które mają być wyświetlane.
4. Wybierz znacznik wyboru, aby powrócić do **Monitor** kartę.
5. Wybierz kółko obok metrykę, aby wyświetlić wartość tego metryki na wykresie.  
   
    Na przykład **użycie procesora CPU** metryka jest wyszarzona; jego dane wyjściowe nie są wyświetlane na wykresie:  
   ![Metryki użycia procesora CPU jest szary.][GrayedMetric]  
   
    Wybierz na wygaszone się koło, aby włączyć **użycie procesora CPU** metrykę, aby wyświetlić dane wyjściowe na wykresie:  
   ![Metryki użycia procesora CPU jest włączona.][EnabledMetric]
6. Aby usunąć metryki z wykresu wyświetlania i listy, wybierz **usunąć Metryka** na pasku zadań. Aby dodać metryki wstecz do listy, wybierz opcję **dodać metryki** na pasku zadań sprawdź metrykę, a następnie wybierz znacznik wyboru, aby powrócić do **Monitor** kartę. Wybierz wygaszone koło, aby włączyć metryki.

## <a name="Metrics"></a>Dostępne metryki
Dostępne są następujące liczniki wydajności/metryki:

<table border="1">

<tr>
<td><strong>Opóźnienie RountdTrip</strong></td>
<td>Wyświetla wszystkie mostków Średni czas przetwarzania wiadomości od czasu odbioru wiadomości do momentu wiadomości jest w pełni przetworzone przez usługę BizTalk w milisekundach (ms). Zliczane są tylko pomyślnie przetworzonych komunikatów.<br/><br/>
Gdy występują następujące zdarzenia, tworzona jest sygnatura czasowa:
<ul>
<li>Brama wprowadza wiadomości</li>
<li>Komunikat jest kierowany do miejsca docelowego</li>
<li>Miejsce docelowe odpowiedzi</li>
<li>Wysłane do bramy odpowiedzi potwierdzenia docelowego</li>
</ul>
<br/>
Ta metryka przedstawia wynik następującego obliczenia:
<br/><br/>
[Docelowy potwierdzenia odpowiedzi wysłanych do bramy] - [brama wprowadza wiadomości]</td>
</tr>
<tr>
<td><strong>Błędy w źródle</strong></td>
<td>Wyświetla całkowitą liczbę wiadomości, których nie powiodła się przez usługę BizTalk, gdy ściąganie wiadomości z punktów końcowych źródła.</td>
</tr>
<tr>
<td><strong>Użycie procesora CPU</strong></td>
<td>Wyświetla średni procent czasu procesora wszystkich wystąpień ról.</td>
</tr>
<tr>
<td><strong>Opóźnienie przetwarzania</strong></td>
<td>Wyświetla średni czas w milisekundach (ms) do przetworzenia komunikatu przez usługę BizTalk mostków wszystkich, z wyłączeniem czasu poświęconego w miejsc docelowych. Zliczane są tylko pomyślnie przetworzonych komunikatów.<br/><br/>
W przypadku wystąpienia każdej z następujących zdarzeń, tworzona jest sygnatura czasowa:

<ul>
<li>Brama wprowadza wiadomości</li>
<li>Komunikat jest kierowany do miejsca docelowego</li>
<li>Miejsce docelowe odpowiedzi</li>
<li>Wysłane do bramy odpowiedzi potwierdzenia docelowego</li>
</ul>
<br/>Ta metryka przedstawia wynik następującego obliczenia:<br/><br/>
[Docelowy potwierdzenia odpowiedzi wysłanych do bramy] - [brama wprowadza wiadomości] - [docelowy odpowiedzi] + [wiadomość jest przesyłana do lokalizacji docelowej]</td>
</tr>
<tr>
<td><strong>Błędy w procesie</strong></td>
<td>Wyświetla całkowitą liczbę komunikatów, których nie powiodła się podczas przetwarzania przez usługę BizTalk we wszystkich mostków w interwale czasu.</td>
</tr>
<tr>
<td><strong>Komunikaty wysyłane</strong></td>
<td>Wyświetla całkowitą liczbę komunikatów wysłanych przez usługę BizTalk we wszystkich mostków w interwale czasu. Ta metryka jest zwiększany, gdy komunikat wysłany z potokiem osiągnie docelowy trasy. Ta metryka nie wskazuje, że wiadomość jest pomyślnie przetwarzane.<br/><br/>
W scenariuszu żądanie-odpowiedź metryka jest zwiększany po docelowy trasy wysyła potwierdzenia otrzymania z powrotem do potoku.</td>
</tr>
<tr>
<td><strong>Odebrane wiadomości</strong></td>
<td>Wyświetla całkowitą liczbę komunikatów odebranych przez usługę BizTalk we wszystkich mostków w interwale czasu. Ta metryka jest zwiększany, gdy nowy komunikat jest odbierany przez potok.</td>
</tr>
<tr>
<td><strong>Komunikaty w procesie</strong></td>
<td>Wyświetla całkowitą liczbę komunikatów aktualnie przetwarzanych przez usługę BizTalk w interwale czasu.</td>
</tr>
<tr>
<td><strong>Liczba przetworzonych komunikatów</strong></td>
<td>Wyświetla całkowitą liczbę komunikatów pomyślnie przetworzone przez usługę BizTalk we wszystkich mostków w interwale czasu. Ta metryka jest zwiększany, gdy komunikat jest pomyślnie zostały odebrane przez potok i pomyślnie skierowany do miejsca docelowego.</td>
</tr>
</table>


## <a name="scale"></a>Skalowanie
Na karcie skali można dodać lub odjąć liczbę jednostek używanych przez usługi BizTalk. Domyślnie istnieje skonfigurowane jednostki. Można dodać dodatkowe jednostki skalowania usługi BizTalk. Zwiększenie skali są zwiększyć przepustowość. Ilość zasobów zwiększa także, tym mostków wdrożone, umowy, LOB połączeń i mocy obliczeniowej. Na przykład zwiększenie skali od 1 jednostka do 2 jednostki. W takiej sytuacji można wdrożyć podwoić liczbę mostków, dwukrotnie umowy, dwukrotnie połączeń biznesowych i dwukrotnie moc obliczeniową.

Niektóre wersje BizTalk nie oferują opcji skalowania. W takim przypadku jednostki jest dozwolone. Aby określić liczbę jednostek, które mogą być skalowane posiadanej wersji, zajrzyj do [usługi BizTalk Services: wersje wykresu](biztalk-editions-feature-chart.md).

Zwiększenie liczby jednostek może mieć wpływ cennik. Jeśli zwiększysz jednostki, wybierając **zapisać** zostanie wyświetlony komunikat informujący o tym, jeśli jest wpływ na rozliczenia. Następnie można kontynuować. Wraz ze zwiększeniem liczba jednostek, zmiany stanu usługi BizTalk z aktywnego aktualizacji. Stan aktualizacji usługi BizTalk jest nadal uruchomiona.

[Usługi BizTalk Services: Wersje wykresu](biztalk-editions-feature-chart.md) definiuje "Jednostki".

## <a name="configure"></a>Konfigurowanie
Nie ma zastosowania do połączeń hybrydowych było możliwe.

Ustawia stan kopii zapasowej, None lub automatyczne. Jeśli wartość None, nie ma kopii zapasowych są tworzone automatycznie. Jeśli ustawiono automatyczne, możesz skonfigurować lokalizację kopii zapasowej, częstotliwości kopii zapasowych i jak długo, aby zachować pliki kopii zapasowej. 

[Usługi BizTalk Services: Kopia zapasowa i przywracanie](biztalk-backup-restore.md) udostępnia szczegółowe informacje. 

## <a name="HybridConnections"></a>Połączenia hybrydowe
Połączenia hybrydowe umożliwiają łączenie aplikacji Azure, takich jak aplikacje sieci Web lub Mobile Apps w usłudze Azure App Service lokalnymi zasobem, który korzysta z portu statycznego TCP, takich jak SQL Server, MySQL, interfejsów API sieci Web HTTP i większość niestandardowych usług sieci Web. Usługi BizTalk Services zarządzania połączeń hybrydowych było możliwe.

Aby utworzyć lub zarządzać połączeń hybrydowych w usłudze Azure BizTalk Services, zobacz [połączeń hybrydowych](integration-hybrid-connection-overview.md).

## <a name="next"></a>Następne kroki
Teraz, kiedy znasz różnych kartach zawierających, można dowiedzieć się więcej o funkcji usług BizTalk Azure:

* [BizTalk Services: Throttling (Usługa BizTalk Services: ograniczanie przepływności)](biztalk-throttling-thresholds.md)  
* [BizTalk Services: Issuer Name and Issuer Key (Usługa BizTalk Services: nazwa i klucz wydawcy)](biztalk-issuer-name-issuer-key.md)  
* [BizTalk Services: Backup and Restore (Usługa BizTalk Services: tworzenie kopii zapasowej i przywracanie)](biztalk-backup-restore.md)

## <a name="see-also"></a>Zobacz też
* [Połączenia hybrydowe](integration-hybrid-connection-overview.md)  
* [Usługi BizTalk Services: Developer, podstawowa, standardowa i Premium Edition wykresu](biztalk-editions-feature-chart.md)  
* [Usługi BizTalk Services: Inicjowanie obsługi administracyjnej](biztalk-provision-services.md)  
* [Usługi BizTalk Services: Stan usługi BizTalk wykresu](biztalk-service-state-chart.md)  
* [Jak rozpocząć pracę z zestawem SDK usługi Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[Quickstart]: ./media/biztalk-dashboard-monitor-scale-tabs/QuickStartIcon.png
[AddMetrics]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png
[GrayedMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png
[EnabledMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png

