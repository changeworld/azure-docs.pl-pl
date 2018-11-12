---
title: Pulpit nawigacyjny, monitorowanie, skala, konfigurowanie i połączeń hybrydowych w BizTalk Services | Dokumentacja firmy Microsoft
description: Więcej informacji na temat formantów i monitorowania wydajności dla usługi BizTalk Services
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
ms.openlocfilehash: 3f4763b5e15d4b9b84e868262a9e8538b8a407a2
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51228831"
---
# <a name="review-the-dashboard-monitor-scale-configure-and-hybrid-connection-tabs"></a>Przegląd kart Pulpit nawigacyjny, Monitorowanie, Skala, Konfigurowanie i Połączenie hybrydowe

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Po utworzeniu usługi BizTalk i wdrożyć aplikację, możesz zmienić niektóre ustawienia usługi BizTalk i monitorowanie wydajności aplikacji. 

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

Spowoduje to otwarcie nowego okna z następującymi kartami. W tym temacie opisano te karty.

## <a name="quickstart-quickstartquickstart"></a>(Przewodnik Szybki Start![Szybki start][Quickstart])
W zależności od wersji usługi BizTalk wszystkie opcje na liście mogą nie być dostępne. 

<table border="1">
    <tr>
        <td><strong>Pobierz narzędzia</strong></td>
        <td>Pobierz zestaw SDK usługi BizTalk do zainstalowania szablony projektu Visual Studio na komputerze deweloperskim w środowisku lokalnym. Te szablony tworzą <strong>usługi BizTalk Services</strong> (mostek) i <strong>artefaktów usługi BizTalk</strong> projektów programu Visual Studio (przekształcenia), które są wdrażane do usługi BizTalk.
        <br/><br/>
        <a HREF="https://go.microsoft.com/fwlink/p/?LinkID=302335"> Jak uruchomić, przy użyciu zestawu SDK usługi Azure BizTalk Services </a> i <a HREF="https://go.microsoft.com/fwlink/p/?LinkID=241589">Instalowanie zestawu SDK usługi Azure BizTalk Services</a> wymieniono kroki, aby rozpocząć pracę.
        </td>
    </tr>
    <tr>
        <td><strong>Tworzenie umów partnera</strong></td>
        <td>Zostanie otwarty Portal usługi BizTalk Azure hostowanej na platformie Azure, gdzie dodać partnerów i utworzyć X12, AS2 i umów integracji EDI EDIFACT.
        <br/><br/>
        <a HREF="https://go.microsoft.com/fwlink/p/?LinkID=303653">Konfigurowanie składników dla EDI wiadomości w portalu usługi BizTalk Services</a> wymieniono kroki, aby rozpocząć pracę.
        </td>
    </tr>

<tr>
        <td><strong>Dowiedz się więcej na temat usługi BizTalk Services</strong></td>
        <td>Przejdź do <a HREF="https://azure.microsoft.com/documentation/services/biztalk-services/">Centrum uczenia</a> Aby dowiedzieć się więcej o usłudze Azure BizTalk Services.</td>
</tr>
</table>


Na pasku zadań u dołu możesz wykonywać następujące czynności:

<table border="1">

<tr>
<td><strong>Zarządzanie</strong> wdrażania aplikacji</td>
<td>Zostanie otwarty portal usługi Azure BizTalk Services. Portalu usługi BizTalk Services to wejścia do konfiguracji EDI, w tym dodawanie partnerów i tworzenie X12, AS2 i umowy EDIFACT.
<br/><br/>
To jest taka sama jak <strong>tworzenie umowy z partnerami</strong> na <strong>— Szybki Start</strong> kartę.
<br/><br/>
<a HREF="https://go.microsoft.com/fwlink/p/?LinkID=303653">Konfigurowanie składników dla EDI wiadomości w portalu usługi BizTalk Services</a> zamieszczono więcej informacji dotyczących portalu usługi BizTalk Services.</td>
</tr>

<tr>
<td><strong>Informacje o połączeniu</strong> z Namespace kontroli dostępu</td>
<td>Po wybraniu informacji o połączeniu, następnie Namespace kontroli dostępu, domyślne wystawcy i klucza domyślne są wyświetlane. Możesz skopiować te wartości.
<br/><br/>
Można również otworzyć portalu kontroli dostępu. <a HREF="https://go.microsoft.com/fwlink/p/?LinkID=285670">Tworzyć mechanizmy kontroli dostępu Namespace</a> zamieszczono więcej informacji dotyczących portalu kontroli dostępu.</td>
</tr>

<tr>
<td><strong>Synchronizuj klucze</strong> na koncie magazynu</td>
<td>Podczas tworzenia konta usługi Storage następuje automatyczne utworzenie klucza podstawowego i klucza pomocniczego. Te klucze szyfrowania kontrola dostępu do konta magazynu. Usługa BizTalk automatycznie używa klucza podstawowego. <strong>Synchronizuj klucze</strong> użytkownicy mogą przełączać się między klucz podstawowy i klucz pomocniczy bez zakłócania działania usługi BizTalk.
<br/><br/>
Na przykład chcesz usługi BizTalk przy użyciu nowego klucza podstawowego dla konta magazynu. W tym celu:
<br/><br/>
<ol>
<li>Wybierz usługę BizTalk, a następnie wybierz pozycję <strong>zsynchronizuj klucze</strong>. Wybierz klucz pomocniczy. Gdy to zrobisz, rozpoczyna się usługi BizTalk przy użyciu klucza pomocniczego.</li>
<li>Wybierz konto magazynu i ponowne wygenerowanie klucza podstawowego. Należy pamiętać, że usługa BizTalk używa klucza pomocniczego.</li>
<li>Wybierz usługę BizTalk, a następnie wybierz pozycję <strong>zsynchronizuj klucze</strong>. Teraz wybierz klucz podstawowy. Jest to nowy klucz podstawowy, możesz ponownie wygenerowany.</li>
<li>Wybierz konto magazynu i ponowne wygenerowanie klucza pomocniczego.</li>
</ol>
<br/>
Ten proces jest nazywany "przerzucania kluczy". Celem jest umożliwienie użytkownikom przełączać się między klucz podstawowy i klucz pomocniczy bez zakłócania działania usługi BizTalk.</td>
</tr>

<tr>
<td><strong>Usuń</strong> aplikacji</td>
<td>Po wybraniu usunąć, usługa BizTalk i zostaną usunięte wszystkie elementy w nim wdrożona.</td>
</tr>
</table>


## <a name="dashboard"></a>Pulpit nawigacyjny
W zależności od wersji usługi BizTalk wszystkie opcje na liście mogą nie być dostępne. 

Po wybraniu nazwę usługi BizTalk, zostanie wyświetlona karta pulpitu nawigacyjnego. Na pulpicie nawigacyjnym można wykonywać następujące czynności:

##### <a name="usage-overview-shows-the-number-of-used-hybrid-connections"></a>Omówienie użycia: Pokazuje liczbę używanych połączeń hybrydowych
Wyświetla również użycie danych w GB. 

##### <a name="metric-graph-shows-a-fixed-list-of-performance-metrics"></a>Wykres metryk: Pokazuje listę stałych metryki wydajności
Metryki te przedstawiają wartości w czasie rzeczywistym dotyczących kondycji usługi BizTalk. Można również wybrać **względną** lub **bezwzględne** wartości i zakres czasu **interwał** metryk, które są wyświetlane na wykresie. 

Aby uzyskać opis tych metryk wydajności, przejdź do [dostępne metryki](#Metrics) w tym temacie.

##### <a name="quick-glance-lists-your-biztalk-service-properties"></a>Przegląd: Zawiera listę właściwości usługi BizTalk
<table border="1">

<tr>
<td><strong>Zaktualizuj poświadczenia śledzenia bazą danych</strong></td>
<td>Zmienia nazwę użytkownika i hasło używane do logowania do bazy danych śledzenia.</td>
</tr>
<tr>
<td><strong>Aktualizuj certyfikat SSL</strong></td>
<td>Można zaktualizować usługi BizTalk w celu użycia innego certyfikatu SSL. Certyfikat SSL z podpisem własnym jest tworzony automatycznie w chwili możesz <a HREF="https://go.microsoft.com/fwlink/p/?LinkID=302280">Tworzenie usługi BizTalk</a>.</td>
</tr>
<tr>
<td><strong>Pobierz certyfikat</strong></td>
<td>Możesz pobrać certyfikat SSL używany przez usługę BizTalk na komputerze lokalnym.</td>
</tr>
<tr>
<td><strong>Stan</strong></td>
<td>Wyświetla bieżący stan usługi BizTalk. Zobacz <a HREF="https://go.microsoft.com/fwlink/p/?LinkID=329870">usługi BizTalk Services: wykres stanu usługi</a>. </td>
</tr>
<tr>
<td><strong>Adres URL usługi</strong></td>
<td>Adres URL usługi BizTalk. To jest taka sama jak <strong>adres URL domeny</strong> wprowadzane podczas tworzenia usługi BizTalk.</td>
</tr>
<tr>
<td><strong>Adres publiczny wirtualnego adresu IP (VIP)</strong></td>
<td>Adres IP przypisany do usługi BizTalk. Jest on używany dla wszystkich wejściowych punktów końcowych i adres źródła dla ruchu wychodzącego. Ten adres IP należy do usługi BizTalk, tak długo, jak jego tworzenia. Jeśli usługa BizTalk zostanie usunięta, adres IP jest przypisywany do innej usługi BizTalk.</td>
</tr>
<tr>
<td><strong>Usługi ACS Namespace</strong></td>
<td>Uwierzytelnia się za pomocą usługi BizTalk.</td>
</tr>
<tr>
<td><strong>Wersja</strong></td>
<td>Wyświetla wersję wprowadzona po utworzeniu usługi BizTalk.</td>
</tr>
<tr>
<td><strong>Lokalizacja</strong></td>
<td>Wyświetla regionie geograficznym, który jest hostem usługi BizTalk.</td>
</tr>
<tr>
<td><strong>Utworzone</strong></td>
<td>Wyświetla datę i godzinę utworzenia usługi BizTalk.</td>
</tr>
<tr>
<td><strong>Baza danych śledzenia</strong></td>
<td>Nazwa usługi Azure SQL Database, która przechowuje tabele śledzenia użycia przez usługę BizTalk. 
<br/><br/>
<a HREF="https://go.microsoft.com/fwlink/p/?LinkID=302280">Wymagania dotyczące poradnik</a> zawiera szczegółowe informacje o bazie danych śledzenia.</td>
</tr>
<tr>
<td><strong>Monitorowania/archiwizowania magazynu</strong></td>
<td>Nazwa konta usługi Azure Storage, która przechowuje dane wyjściowe monitorowania usługi BizTalk.
<br/><br/>
<a HREF="https://go.microsoft.com/fwlink/p/?LinkID=302280">Wymagania dotyczące poradnik</a> zawiera szczegółowe informacje dotyczące konta magazynu.</td>
</tr>
<tr>
<td><strong>Nazwa subskrypcji</strong></td>
<td>Wyświetla listę subskrypcji, który jest hostem usługi BizTalk. Subskrypcja określa dostępu.</td>
</tr>
<tr>
<td><strong>Identyfikator subskrypcji</strong></td>
<td>Po utworzeniu subskrypcji, identyfikator subskrypcji jest generowany automatycznie. Korzystając z interfejsów API REST, konieczne może być wprowadź identyfikator subskrypcji.</td>
</tr>
</table>

[Usługa BizTalk Services: Inicjowanie obsługi administracyjnej](https://go.microsoft.com/fwlink/p/?LinkID=302280) zawiera instrukcje dotyczące tworzenia usługi BizTalk.

##### <a name="manage-connection-information-sync-keys-and-delete-in-the-task-bar"></a>Zarządzanie, informacje o połączeniu, zsynchronizuj klucze, a następnie usuń na pasku zadań:
<table border="1">

<tr>
<td><strong>Zarządzanie</strong> wdrażania aplikacji</td>
<td>Zostanie otwarty z portalu usługi Azure BizTalk Services. Portalu usługi BizTalk Services to wejścia do konfiguracji EDI, w tym dodawanie partnerów i tworzenie X12, AS2 i umowy EDIFACT.
<br/><br/>
To jest taka sama jak <strong>tworzenie umowy z partnerami</strong> na <strong>— Szybki Start</strong> kartę.
<br/><br/>
<a HREF="https://go.microsoft.com/fwlink/p/?LinkID=303653">Konfigurowanie składników dla EDI wiadomości w portalu usługi BizTalk Services</a> zamieszczono więcej informacji dotyczących portalu usługi BizTalk Services.</td>
</tr>
<tr>
<td><strong>Informacje o połączeniu</strong> z Namespace kontroli dostępu</td>
<td>Wyświetla Namespace kontroli dostępu, domyślne wystawcy i wartości domyślnej klucza. które mogą być kopiowane.
<br/><br/>
Można również otworzyć portalu kontroli dostępu. Ten Portal kontroli dostępu jest taka sama jak za pomocą opcji usługi Active Directory w okienku nawigacji po lewej stronie.
<br/><br/>
<a HREF="https://go.microsoft.com/fwlink/p/?LinkID=285670">Zarządzanie Your ACS Namespace</a> zamieszczono więcej informacji dotyczących portalu kontroli dostępu.</td>
</tr>
<tr>
<td><strong>Synchronizuj klucze</strong> na koncie magazynu</td>
<td>Podczas tworzenia konta usługi Storage następuje automatyczne utworzenie klucza podstawowego i klucza pomocniczego. Te klucze szyfrowania kontrola dostępu do konta magazynu. Usługa BizTalk automatycznie używa klucza podstawowego. <strong>Synchronizuj klucze</strong> użytkownicy mogą przełączać się między klucz podstawowy i klucz pomocniczy bez zakłócania działania usługi BizTalk.
<br/><br/>
Na przykład chcesz usługi BizTalk przy użyciu nowego klucza podstawowego dla konta magazynu. W tym celu:
<br/><br/>
<ol>
<li>Wybierz usługę BizTalk, a następnie wybierz pozycję <strong>zsynchronizuj klucze</strong>. Wybierz klucz pomocniczy. Gdy to zrobisz, rozpoczyna się usługi BizTalk przy użyciu klucza pomocniczego.</li>
<li>Wybierz konto magazynu i ponowne wygenerowanie klucza podstawowego. Należy pamiętać, że usługa BizTalk używa klucza pomocniczego.</li>
<li>Wybierz usługę BizTalk, a następnie wybierz pozycję <strong>zsynchronizuj klucze</strong>. Teraz wybierz klucz podstawowy. Jest to nowy klucz podstawowy, możesz ponownie wygenerowany.</li>
<li>Wybierz konto magazynu i ponowne wygenerowanie klucza pomocniczego.</li>
</ol>
<br/>
Ten proces jest nazywany "przerzucania kluczy". Celem jest umożliwienie użytkownikom przełączać się między klucz podstawowy i klucz pomocniczy bez zakłócania działania usługi BizTalk.</td>
</tr>

<tr>
<td><strong>Usuń</strong> aplikacji</td>
<td>Usługa BizTalk i wszystkie elementy w nim wdrożona zostaną usunięte.</td>
</tr>
</table>


## <a name="monitor"></a>Monitorowanie
Nie ma zastosowania do wersji bezpłatna.

Po wybraniu nazwę usługi BizTalk, na karcie Monitor jest dostępna i wyświetla następujące informacje:

##### <a name="metric-graph-displays-the-selected-performance-metrics"></a>Wykres metryk: Wyświetla metryki wydajności wybrane
Metryki te przedstawiają wartości w czasie rzeczywistym dotyczących kondycji usługi BizTalk. Możesz wybrać metryki wydajności, które są wyświetlane. Maksymalnie sześć metryki wydajności mogą być wyświetlane jednocześnie. 

Można również wybrać **względną** lub **bezwzględne** wartości i zakres czasu **interwał** metryk, które są wyświetlane. 

##### <a name="to-remove-or-display-metrics-in-the-graph"></a>Aby usunąć lub wyświetlić metryki na wykresie:
1. Wybierz **Monitor** kartę.
2. Wybierz **Dodaj metryki** na pasku zadań:  
   ![Wybierz opcję Dodaj metryki][AddMetrics]
3. Sprawdzanie metryk wydajności, które mają być wyświetlane.
4. Wybierz znacznik wyboru, aby powrócić do **Monitor** kartę.
5. Wybierz kółko obok metrykę do wyświetlenia w przypadku tej metryki wartości na wykresie.  
   
    Na przykład **użycie procesora CPU** metryka jest wyszarzona; jego dane wyjściowe nie są wyświetlane na wykresie:  
   ![Metryka użycia procesora CPU jest wyszarzona.][GrayedMetric]  
   
    Wybierz wygaszone się koło, aby umożliwić **użycie procesora CPU** metrykę do wyświetlenia na wykresie jego dane wyjściowe:  
   ![Metryka użycia procesora CPU jest włączona.][EnabledMetric]
6. Aby usunąć metryki z wykresu wyświetlania i listy, wybierz **Usuń metryki** na pasku zadań. Aby dodać metryk wstecz do listy, wybierz **Dodaj metryki** na pasku zadań sprawdź metrykę, a następnie wybierz znacznik wyboru, aby powrócić do **Monitor** kartę. Wybierz wygaszone koła, aby włączyć metryki.

## <a name="Metrics"></a>Dostępne metryki
Dostępne są następujące liczniki wydajności/metryki:

<table border="1">

<tr>
<td><strong>Opóźnienie RountdTrip</strong></td>
<td>Wyświetla wszystkie mostków Średni czas przetwarzania komunikatów od czasu, wiadomość zostaje odebrana, aż komunikat jest w pełni przetwarzany przez usługę BizTalk w milisekundach (ms). Uwzględniane są tylko te komunikaty, które pomyślnie przetworzone.<br/><br/>
Gdy wystąpią następujące zdarzenia, tworzona jest sygnatura czasowa:
<ul>
<li>Komunikat przechodzi bramy</li>
<li>Komunikat jest kierowany do miejsca docelowego</li>
<li>Odebrano odpowiedź docelowego</li>
<li>Miejsce docelowe potwierdzenie odpowiedzi wysyłane do bramy</li>
</ul>
<br/>
Ta metryka przedstawia wynik obliczenia następujące:
<br/><br/>
[Wysłane do bramy odpowiedzi potwierdzenie docelowego] - [brama wprowadza wiadomości]</td>
</tr>
<tr>
<td><strong>Błędy w źródle</strong></td>
<td>Wyświetla łączną liczbę komunikatów, które nie powiodły się przez usługę BizTalk podczas ściągania komunikatów z punktami końcowymi źródła.</td>
</tr>
<tr>
<td><strong>Użycie procesora CPU</strong></td>
<td>Wyświetla średni procent czasu procesora wszystkich wystąpień roli.</td>
</tr>
<tr>
<td><strong>Opóźnienie przetwarzania</strong></td>
<td>Wyświetla średni czas w milisekundach (ms) do przetworzenia komunikatu przez usługę BizTalk we wszystkich mostków, z wyłączeniem czas potrzebny do miejsc docelowych. Uwzględniane są tylko te komunikaty, które pomyślnie przetworzone.<br/><br/>
W przypadku wystąpienia każdej z następujących zdarzeń jest tworzone jest sygnatura czasowa:

<ul>
<li>Komunikat przechodzi bramy</li>
<li>Komunikat jest kierowany do miejsca docelowego</li>
<li>Odebrano odpowiedź docelowego</li>
<li>Miejsce docelowe potwierdzenie odpowiedzi wysyłane do bramy</li>
</ul>
<br/>Ta metryka przedstawia wynik obliczenia następujące:<br/><br/>
[Wysłane do bramy odpowiedzi potwierdzenie docelowego] - [komunikat przechodzi bramy] - [docelowym otrzyma odpowiedź,] + [komunikat jest kierowany do miejsca docelowego]</td>
</tr>
<tr>
<td><strong>Błędy w procesie</strong></td>
<td>Wyświetla całkowitą liczbę komunikatów, które nie powiodły się podczas przetwarzania przez usługę BizTalk we wszystkich mostków w przedziale czasu.</td>
</tr>
<tr>
<td><strong>Komunikaty wysyłane</strong></td>
<td>Wyświetla całkowitą liczbę komunikatów wysłanych przez usługę BizTalk we wszystkich mostków w przedziale czasu. Ta metryka jest podwyższana, gdy wiadomość wysłana z potoku osiąga docelowej tras. Ta metryka nie wskazuje, czy komunikat jest przetwarzany pomyślnie.<br/><br/>
W scenariuszu "żądanie-odpowiedź" metryka jest zwiększany, gdy docelowy trasa wysyła potwierdzenia otrzymania z powrotem do potoku.</td>
</tr>
<tr>
<td><strong>Odebrane komunikaty</strong></td>
<td>Wyświetla całkowitą liczbę komunikatów odebranych przez usługę BizTalk we wszystkich mostków w przedziale czasu. Ta metryka jest zwiększany, gdy nowy komunikat jest odbierany przez potok.</td>
</tr>
<tr>
<td><strong>Komunikaty w procesie</strong></td>
<td>Wyświetla całkowitą liczbę komunikatów aktualnie przetwarzane przez usługę BizTalk w przedziale czasu.</td>
</tr>
<tr>
<td><strong>Przetworzone komunikaty</strong></td>
<td>Wyświetla całkowitą liczbę komunikatów pomyślnie przetworzone przez usługę BizTalk we wszystkich mostków w przedziale czasu. Ta metryka jest zwiększany, gdy komunikat jest pomyślnie odbierany przez potok i pomyślnie skierować do lokalizacji docelowej.</td>
</tr>
</table>


## <a name="scale"></a>Skalowanie
Na karcie Skala możesz dodawać lub odejmować liczbę jednostek użytych przez usługę BizTalk. Domyślnie jedno jest skonfigurowane jednostki. Można dodać usługę o kolejne jednostki skalowania usługi BizTalk. Wraz ze zwiększeniem skalowania są zwiększyć przepustowość. Ilość zasobów zwiększa także, tym mostków wdrożone, umowy, LOB połączeń i mocy obliczeniowej. Na przykład możesz zwiększyć skalę z 1 jednostkę do 2 jednostki. W takiej sytuacji można wdrożyć podwoić liczbę mostków, dwukrotnie umowy, dwukrotnie połączenia biznesowych i dwukrotnie mocy obliczeniowej.

Niektóre wersje BizTalk nie oferuje opcji skalowania. W takiej sytuacji jedna jednostka jest dozwolone. Aby określić liczbę jednostek, które mogą być skalowane posiadanej wersji, zapoznaj się [usługi BizTalk Services: Editions Chart](biztalk-editions-feature-chart.md).

Zwiększenie liczby jednostek mogą mieć wpływ na ceny. Jeśli możesz zwiększyć liczbę jednostek, wybranie opcji **Zapisz** zostanie wyświetlony komunikat informujący o tym, jeśli ma wpływ na rozliczenia. Następnie możesz wybrać kontynuować. Wraz ze zwiększeniem liczby jednostek, zmiany stanu usługi BizTalk z aktywnego aktualizacji. W trakcie aktualizacji usługi BizTalk będzie nadal działać.

[BizTalk Services: Editions Chart](biztalk-editions-feature-chart.md) definiuje "Jednostka".

## <a name="configure"></a>Konfigurowanie
Nie ma zastosowania do połączeń hybrydowych.

Ustawia stan kopii zapasowej na wartość None lub automatyczne. Po ustawieniu na wartość None, żadne kopie zapasowe są tworzone automatycznie. Gdy są ustawione na tryb automatyczny, możesz skonfigurować lokalizacji kopii zapasowej, częstotliwość tworzenia kopii zapasowej oraz jak długo można zachować pliki kopii zapasowej. 

[Usługa BizTalk Services: Kopia zapasowa i przywracanie](biztalk-backup-restore.md) udostępnia szczegółowe informacje. 

## <a name="HybridConnections"></a>Połączenia hybrydowe
Połączenia hybrydowe nawiązać zasobów w środowisku lokalnym, który używa statycznego portu TCP, takich jak SQL Server, MySQL, interfejsy API protokołu HTTP sieci Web i większość niestandardowych usług sieci Web aplikacji platformy Azure, takich jak aplikacje sieci Web lub aplikacji mobilnych w usłudze Azure App Service. Połączenia hybrydowe są zarządzane w usłudze BizTalk Services.

Aby utworzyć lub zarządzać połączeń hybrydowych w usłudze Azure BizTalk Services, zobacz [połączeń hybrydowych](integration-hybrid-connection-overview.md).

## <a name="next"></a>Następne kroki
Teraz, kiedy znasz już różne karty, można dowiedzieć się więcej na temat funkcji usługi Azure BizTalk Services:

* [BizTalk Services: Throttling (Usługa BizTalk Services: ograniczanie przepływności)](biztalk-throttling-thresholds.md)  
* [BizTalk Services: Issuer Name and Issuer Key (Usługa BizTalk Services: nazwa i klucz wydawcy)](biztalk-issuer-name-issuer-key.md)  
* [BizTalk Services: Backup and Restore (Usługa BizTalk Services: tworzenie kopii zapasowej i przywracanie)](biztalk-backup-restore.md)

## <a name="see-also"></a>Zobacz też
* [Połączenia hybrydowe](integration-hybrid-connection-overview.md)  
* [Usługa BizTalk Services: Deweloper, podstawowa, standardowa i Premium Editions Chart](biztalk-editions-feature-chart.md)  
* [Usługa BizTalk Services: Inicjowanie obsługi administracyjnej](biztalk-provision-services.md)  
* [Usługi BizTalk Services: Wykres stanu usługi BizTalk](biztalk-service-state-chart.md)  
* [Jak rozpocząć pracę z zestawem SDK usługi Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=302335)

[Quickstart]: ./media/biztalk-dashboard-monitor-scale-tabs/QuickStartIcon.png
[AddMetrics]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png
[GrayedMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png
[EnabledMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png

