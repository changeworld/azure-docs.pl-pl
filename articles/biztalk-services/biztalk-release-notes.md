---
title: Informacje o wersji dla usług Azure BizTalk | Dokumentacja firmy Microsoft
description: Wymieniono znane problemy dotyczące usług Azure BizTalk
services: biztalk-services
documentationcenter: ''
author: msftman
manager: erikre
editor: ''
ms.assetid: f4906fdc-4cd9-4a57-a007-a88c2e51a18f
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: deonhe
ms.openlocfilehash: 5c05c7c4c0f8f0b3619b36390c8a0224a03c900a
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063546"
---
# <a name="release-notes-for-azure-biztalk-services"></a>Informacje o wersji dla usługi Azure BizTalk Services

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

Informacje o wersji dla usługi Microsoft Azure BizTalk zawierają znane problemy występujące w tej wersji.

## <a name="whats-new-in-the-november-update-of-biztalk-services"></a>Nowości w aktualizacji z listopada usług BizTalk
* Można włączyć szyfrowanie przechowywanych w portalu usługi BizTalk. Zobacz [włączyć szyfrowanie przechowywanych w portalu usługi BizTalk](https://msdn.microsoft.com/library/azure/dn874052.aspx).

## <a name="update-history"></a>Historia aktualizacji
### <a name="october-update"></a>Aktualizacja z października
* Konta organizacyjne są obsługiwane:  
  * **Scenariusz**: zarejestrowany wdrażania usługi BizTalk przy użyciu konta Microsoft (takich jak user@live.com). W tym scenariuszu tylko Account Microsoft użytkownicy mogą zarządzać za pomocą portalu usługi BizTalk Services usługi BizTalk. Nie można użyć konta organizacyjnego.  
  * **Scenariusz**: zarejestrowanych w usłudze Azure Active Directory za pomocą konta organizacyjnego wdrożenia usługi BizTalk (takich jak user@fabrikam.com lub user@contoso.com). W tym scenariuszu tylko użytkownicy usługi Azure Active Directory w danej organizacji można zarządzać przy użyciu portalu usługi BizTalk Services usługi BizTalk. Nie można użyć konta Microsoft.  
* Podczas tworzenia usługi BizTalk, są automatycznie rejestrowane w portalu usługi BizTalk.
  * **Scenariusz**: możesz zalogować się do platformy Azure, Utwórz usługę BizTalk, a następnie wybierz **Zarządzaj** po raz pierwszy. Po otwarciu portalu usługi BizTalk Services usługi BizTalk automatycznie rejestruje i jest gotowy do wdrożeń.  
    Zobacz [rejestrowanie i aktualizowanie wdrożenia usługi BizTalk na BizTalk usług Portal](https://msdn.microsoft.com/library/azure/hh689837.aspx).  

### <a name="august-14-update"></a>Aktualizacja z sierpnia 14
* Umowy oraz Mostek oddzielenie — umów z partnerami i mostków są całkowicie niezależna teraz w portalu usługi BizTalk. Możesz teraz utworzyć umowy i mostków oddzielnie, a w czasie wykonywania mostków rozpoznać na podstawie wartości w komunikacie EDI umowy. Zobacz [Tworzenie umów w usług Azure BizTalk](https://msdn.microsoft.com/library/azure/hh689908.aspx), [utworzyć mostek EDI przy użyciu portalu usługi BizTalk](https://msdn.microsoft.com/library/azure/dn793986.aspx), [utworzyć mostek AS2 przy użyciu portalu usługi BizTalk](https://msdn.microsoft.com/library/azure/dn793993.aspx)i [ Jak mostków usunąć umowy w czasie wykonywania](https://msdn.microsoft.com/library/azure/dn794001.aspx)  
* Możliwość tworzenia szablonów dla umowy jest przerywane.  
* Umowy po stronie wysyłania można teraz określić ogranicznik różne zestawy dla każdego schematu. Ta konfiguracja jest wybranym w ustawieniach protokołu umowy po stronie wysyłania. Aby uzyskać więcej informacji, zobacz [Utwórz moduł X12 umowy w usłudze Azure BizTalk Services](https://msdn.microsoft.com/library/azure/hh689847.aspx) i [Tworzenie umów EDIFACT w usług Azure BizTalk](https://msdn.microsoft.com/library/azure/dn606267.aspx). Dwa nowe jednostki również są dodawane do interfejsu API OM modułu TPM do takiej obsługi. Zobacz [X12DelimiterOverrides](https://msdn.microsoft.com/library/azure/dn798749.aspx) i [EDIFACTDelimiterOverride](https://msdn.microsoft.com/library/azure/dn798748.aspx).  
* Konstrukcje XSD, łącznie z typów pochodnych, są teraz obsługiwane. Zobacz [XSD standardowych użyj konstrukcji w map](https://msdn.microsoft.com/library/azure/dn793987.aspx) i [Użyj typów pochodnych w przykłady i scenariusze mapowania](https://msdn.microsoft.com/library/azure/dn793997.aspx).  
* AS2 obsługuje nowych algorytmów Mikrofon do podpisywania wiadomości i nowych algorytmów szyfrowania. Zobacz [Tworzenie umów AS2 w usługach Azure BizTalk](https://msdn.microsoft.com/library/azure/hh689890.aspx).  
  ## <a name="know-issues"></a>Znać problemów

### <a name="connectivity-issues-after-biztalk-services-portal-update"></a>Problemy z połączeniem po aktualizacji portalu usług BizTalk
  Jeśli masz Portal usługi BizTalk otworzyć, gdy usługi BizTalk Services jest uaktualniany wycofanie zmian do usługi mogą stają przed problemy z łącznością z portalem usługi BizTalk.  
  Jako obejście może ponownie uruchomić przeglądarkę, usunąć pamięci podręcznej przeglądarki lub uruchomić portalu w trybie prywatnym.  

### <a name="visual-studio-ide-cannot-locate-the-artifact-if-you-click-an-error-or-warning-in-a-biztalk-services-project"></a>Środowiska IDE programu Visual Studio nie może zlokalizować artefaktu, jeśli klikniesz przycisk błąd lub ostrzeżenie w projekcie usługi BizTalk Services
Zainstaluj program Visual Studio 2012 Update 3 RC 1 do rozwiązania problemu.  

### <a name="custom-binding-project-reference"></a>Odwołanie do niestandardowego powiązania projektu
Należy wziąć pod uwagę następujące sytuacje z usługi BizTalk Services projektu w rozwiązaniu Visual Studio:  

* W tym samym rozwiązaniu Visual Studio jest projektu usługi BizTalk Services oraz projekt niestandardowego powiązania. Projekt usługi BizTalk zawiera odwołanie do tego pliku projektu niestandardowego powiązania.
* Projekt usługi BizTalk zawiera odwołanie do niestandardowego powiązania/zachowanie biblioteki DLL.

"Tworzenia" rozwiązania w programie Visual Studio pomyślnie. Następnie "Odbudować" lub "Czysta" rozwiązania. Po tym, gdy odbudować lub wyczyścić ponownie, wystąpił następujący błąd:  
  Nie można skopiować pliku <Path to DLL> do "bin\Debug\FileName.dll". Proces uzyskać dostępu do pliku "bin\Debug\FileName.dll", ponieważ jest on używany przez inny proces.  

#### <a name="workaround"></a>Obejście
* Jeśli [Visual Studio 2012 Update 3](https://docs.microsoft.com/en-us/visualstudio/releasenotes/vs2012-update3-vs) jest zainstalowany, masz następujące dwie opcje:
  
  * Uruchom ponownie program Visual Studio lub
  * Uruchom ponownie rozwiązanie. Następnie należy wykonać tylko kompilację w ramach rozwiązania.  
* Jeśli [Visual Studio 2012 Update 3](https://docs.microsoft.com/en-us/visualstudio/releasenotes/vs2012-update3-vs) nie jest zainstalowany, Otwórz Menedżer zadań kliknij procesów, kliknij proces MSBuild.exe, a następnie kliknij przycisk Zakończ proces.  

### <a name="routing-to-basichttprelay-endpoints-is-not-supported-from-bridges-and-biztalk-services-portal-if-non-printable-characters-are-promoted-as-http-headers"></a>Routing do punktów końcowych BasicHttpRelay nie jest obsługiwana w portalu usługi BizTalk i mostków czy znaki niedrukowalne są awansowane nagłówki HTTP
Jeśli używasz niedrukowalne znaki jako część awansowanej właściwości wiadomości tych wiadomości nie kierowane do przekazywania miejsc docelowych, korzystające z wiązania BasicHttpRelay. Ponadto awansowanej właściwości dostępnych jako część śledzenia są zakodowane w adresie URL dla obiektów blob i cofanie zakodowanego dla miejsc docelowych.  

### <a name="mdn-is-sent-asynchronously-even-if-the-send-asynchronous-mdn-option-is-unchecked"></a>MDN są wysyłane asynchronicznie, nawet jeśli jest zaznaczona opcja MDN asynchronicznego wysyłania
Rozważmy scenariusz, w tym — w przypadku wybrania **Wysyłanie asynchroniczne MDN** wyboru i wprowadź adres URL Wysyłanie asynchroniczne MDN, a następnie usuń zaznaczenie pola wyboru **Wysyłanie asynchroniczne MDN** wyboru ponownie, MDN jest nadal wysyłane do określony adres URL, nawet jeśli nie wybrano opcję, aby wysłać async MDNs.  
Określony adres URL jako obejście, należy wyczyścić przed zaznaczenie pola wyboru **Wysyłanie asynchroniczne MDN** pole wyboru, a następnie wdrożyć umowy AS2.  

### <a name="whitespace-characters-beyond-a-valid-interchange-cause-an-empty-message-to-be-sent-to-the-suspend-endpoint"></a>Odstęp poza prawidłową wymiany spowodować pusty komunikat do wysłania do punktu końcowego zawieszenia
W przypadku białe znaki poza segment IEA dezasembler traktuje tego jako końca bieżącego wymiany i analizuje następny zestaw odstępy jako następny komunikat. Ponieważ to nie jest prawidłową wymiany, widoczny czy jeden komunikat pomyślne jest wysyłany do docelowego trasy i jeden pusty komunikat jest wysyłany Wstrzymaj punktu końcowego.  

### <a name="tracking-in-biztalk-services-portal"></a>Śledzenie w portalu usługi BizTalk
Zdarzenia śledzenia są przechwytywane do przetwarzania komunikatów EDI i wszelkie korelacji. W przypadku niepowodzenia komunikat poza etap protokołu śledzenia zostaną wyświetlone pomyślnym przeprowadzeniu. W takiej sytuacji można znaleźć w sekcji dziennika pod **szczegóły** kolumny w **śledzenia** szczegóły błędów.
X12 odbierania i wysyłania ustawień ([Utwórz moduł X12 umowy w usłudze Azure BizTalk Services](https://msdn.microsoft.com/library/azure/hh689847.aspx)) zawierają informacje o fazie protokołu.  

### <a name="update-agreement"></a>Aktualizowanie umowy
Portal usługi BizTalk umożliwia modyfikowanie kwalifikator tożsamości, gdy Umowa jest skonfigurowany. Może to spowodować właściwości jest niezgodna. Na przykład istnieje umowa przy użyciu ZZ:1234567 i ZZ:7654321 kwalifikator. W ustawieniach profilu Portal usługi BizTalk możesz zmienić ZZ:1234567 jako 01:ChangedValue. Otwórz umowę i 01:ChangedValue jest wyświetlany zamiast ZZ:1234567.
Aby zmodyfikować kwalifikator tożsamości, usunąć umowy, zaktualizuj **tożsamości** w profilu partnera, a następnie ponownie utwórz umowy.  

> AZURE. Ostrzeżenie zachowanie tej wpływa na X12 i AS2.  
> 
> 

### <a name="as2-attachments"></a>Załączniki AS2
Załączniki wiadomości nie są obsługiwane w AS2 wysyłać ani odbierać. W szczególności załączników dyskretnie są ignorowane, a treść komunikatu jest przetwarzany jako zwykłe wiadomości AS2.  

### <a name="resources-remembering-path"></a>Zasoby: Ścieżka zapamiętywanie
Podczas dodawania **zasobów**, okno dialogowe nie może pamiętać ścieżki wcześniej służy do dodawania zasobów. Do zapamiętania wcześniej używane ścieżki, spróbuj dodać witryny sieci web portalu usługi BizTalk **Zaufane witryny** w programie Internet Explorer.  

### <a name="if-you-rename-the-entity-name-of-a-bridge-and-close-the-project-without-saving-changes-opening-the-entity-again-results-in-an-error"></a>Możesz zmienić nazwę jednostki mostka, zamknij projekt bez zapisywania zmian ponownie otworzyć obiekt powoduje wystąpienie błędu
Rozważmy scenariusz, w następującej kolejności:  

* Dodaj do projektu usługi BizTalk mostka (na przykład XML One-Way mostek)  
* Zmień nazwę mostka przez określenie wartości dla właściwości Nazwa jednostki. Zmienia jego nazwę pliku .bridgeconfig skojarzone z podanej nazwy.  
* Zamknij plik .bcs (zamykając kartę w programie Visual Studio) bez zapisywania zmian.  
* Otwórz plik .bcs ponownie z Eksploratora rozwiązań.  
  Można zauważyć, że podczas .bridgeconfig skojarzony plik ma nowy podana nazwa, nazwa jednostki na powierzchni projektu jest nadal starej nazwy. Jeśli spróbujesz otwórz konfigurację mostka, klikając dwukrotnie składnika Mostek otrzymasz następujący błąd:  
  `‘<old name>’ Entity’s associated file ‘<old name>.bridgeconfig’ does not exist` Aby uniknąć uruchomiona w tym scenariuszu, upewnij się, że po zmianie nazwy jednostki w projekcie usługi BizTalk zapisywania zmian.  
  
### <a name="biztalk-service-project-builds-successfully-even-if-an-artifact-has-been-excluded-from-a-visual-studio-project"></a>Projekt usługi BizTalk kompilacje pomyślnie, nawet jeśli artefakty został on wykluczony z projektu programu Visual Studio
Rozważmy scenariusz, w którym możesz dodać artefaktu (na przykład plik XSD) do projektu usługi BizTalk, obejmują tego artefaktów w konfiguracji mostu (na przykład, określając go jako typ komunikatu żądania) i wyklucz go z projektu programu Visual Studio. W takim przypadku skompilowanie projektu nie będzie przekazywać wszelkie błędy tak długo, jak tylko usunięte artefakt znajduje się na dysku w tej samej lokalizacji, z którym został dołączony do projektu programu Visual Studio.
  
### <a name="the-biztalk-service-project-does-not-check-for-schema-availability-while-configuring-the-bridges"></a>Projekt usługi BizTalk nie sprawdza dostępność schematu podczas konfigurowania mostków
W projekcie usługi BizTalk Jeśli schemat, który zostanie dodany do projektu importuje inny schemat projektu usługi BizTalk nie sprawdza, czy zaimportowanego schematu jest dodawane do projektu. Jeśli podczas próby skompilowania takiego projektu, nie są błędy kompilacji.
  
### <a name="the-response-message-for-a-xml-request-reply-bridge-is-always-of-charset-utf-8"></a>Komunikat odpowiedzi dla mostka "żądanie-odpowiedź" XML jest zawsze zestawu znaków UTF-8
W tym wydaniu zestaw znaków komunikat odpowiedzi z mostka żądanie-odpowiedź XML ma zawsze wartość UTF-8.
  
### <a name="user-defined-datatypes"></a>Zdefiniowane przez użytkownika typów danych
Karty BizTalk Adapter Pack w ramach funkcji usługi karty BizTalk mogą wykorzystywać zdefiniowanych przez użytkownika typów danych w operacjach karty.
Używając zdefiniowanych przez użytkownika typów danych, należy skopiować pliki (.dll) na dysku: \Program Files\Microsoft Service\BAServiceRuntime\bin\ karty BizTalk lub do globalnej pamięci podręcznej zestawów (GAC) na serwerze obsługującym usługę BizTalk karty usługi. W przeciwnym razie następujący błąd może wystąpić na komputerze klienckim:  
```
<s:Fault xmlns:s="http://schemas.xmlsoap.org/soap/envelope/">
<faultcode>s:Client</faultcode>
<faultstring xml:lang="en-US">The UDT with FullName "File, FileUDT, Version=Value, Culture=Value, PublicKeyToken=Value" could not be loaded. Try placing the assembly containing the UDT definition in the Global Assembly Cache.</faultstring>
<detail>
  <AFConnectRuntimeFault xmlns="http://Microsoft.ApplicationServer.Integration.AFConnect/2011" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
    <ExceptionCode>ERROR_IN_SENDING_MESSAGE</ExceptionCode>
  </AFConnectRuntimeFault>
</detail>
</s:Fault>
```  
  
> [!IMPORTANT]
> Zaleca GACUtil.exe umożliwia zainstalować plik w globalnej pamięci podręcznej zestawów. GACUtil.exe dokumentów, jak używać tego narzędzia i opcje wiersza polecenia programu Visual Studio.  
> 
> 

### <a name="restarting-the-biztalk-adapter-service-web-site"></a>Ponowne uruchamianie witryny sieci Web usługi BizTalk karty
Instalowanie **środowiska uruchomieniowego usługi karty BizTalk*** tworzy **Usługa karty BizTalk** witryny sieci web w usługach IIS, która zawiera **BAService** aplikacji. **BAService** aplikacji wewnętrznie używa powiązania przekazywania rozszerzyć zasięg lokalnego punktu końcowego usługi w chmurze. Do usługi hostowanej w sieci lokalnej odpowiedniego punktu końcowego przekazywania zostanie zarejestrowany na magistrali usług, tylko wtedy, gdy rozpoczyna się usługa lokalna.  

Jeśli zatrzymać i uruchomić aplikację, nie jest honorowana konfigurację automatycznego uruchamiania aplikacji. W takim przypadku **BAService** jest zatrzymana, należy zawsze należy ponownie uruchomić **Usługa karty BizTalk** witryny sieci web zamiast tego. Się nie uruchomić lub zatrzymać **BAService** aplikacji.

### <a name="special-characters-should-not-be-used-for-address-and-entity-names-of-lob-components"></a>Nie należy używać znaków specjalnych nazw adres i jednostek biznesowych składników
Dla nazwy adresu i jednostek biznesowych składników nie należy używać znaków specjalnych. Jeśli tak zrobisz, wystąpi błąd podczas wdrażania projektu usługi BizTalk. Dla niektórych znaków, takich jak '%', BizTalk karty usługi witryny sieci Web może przejść w stan zatrzymania i trzeba będzie uruchomić go ręcznie.

### <a name="test-map-with-get-context-property"></a>Mapa testu z Get właściwości kontekstu
Jeśli zawiera transformacji **Get właściwości kontekstu** operacji mapy **mapy testu** zakończy się niepowodzeniem. Tymczasowo zastąpić **Get właściwości kontekstu** mapy operację, podając ciąg połączenia mapy operację fikcyjny danymi. To wypełnić schemat docelowy i zezwala na test innych funkcji przekształcenia.

### <a name="test-map-property-does-not-display"></a>Właściwość mapy testu nie jest wyświetlana
**Mapy testu** właściwości nie są wyświetlane w programie Visual Studio. Taka sytuacja może wystąpić, jeśli **właściwości** okna i **Eksploratora rozwiązań** okno nie jest zadokowany jednocześnie. Aby rozwiązać ten problem, dock **właściwości** i **Eksploratora rozwiązań** systemu windows.  

### <a name="datetime-reformat-drop-down-is-grayed-out"></a>Ponowne formatowanie daty/godziny listy rozwijanej jest wyszarzony.
Gdy operację mapy ponowne formatowanie daty/godziny zostanie dodany do powierzchni projektu i skonfigurowane, na liście rozwijanej Format może wygaszone. Może się to zdarzyć, jeśli komputer wyświetlana jest ustawiony **średni — 125%** lub **większe – 150%**. Aby rozwiązać, ustawić **mniejsze — 100% (ustawienie domyślne)** wykonując poniższe kroki:  

1. Otwórz **Panelu sterowania** i kliknij przycisk **wygląd i personalizacja**.
2. Kliknij przycisk **wyświetlania**.
3. Kliknij przycisk **mniejsze — 100% (ustawienie domyślne)** i kliknij przycisk **Zastosuj**.

**Format** listy rozwijanej teraz powinny działać zgodnie z oczekiwaniami.

### <a name="duplicate-agreements-in-the-biztalk-services-portal"></a>Zduplikowane umów w portalu usługi BizTalk
Rozważmy następujący scenariusz:

1. Tworzenie umowy z za pomocą API OM handlowymi zarządzania partnera.
2. Otwórz umowę w dwóch różnych kartach zawierających portalu usługi BizTalk.
3. Wdróż umowy z obu kart.
4. W związku z tym zarówno umowy wdrożony co zduplikowane wpisy w portalu usługi BizTalk

**Obejście**. Otwórz dowolną zduplikowane umowy w portalu usługi BizTalk i wdrożyć.  

### <a name="bridges-do-not-use-updated-certificate-even-after-a-certificate-has-been-updated-in-the-artifact-store"></a>Mostków nie należy używać zaktualizowany certyfikat, nawet po zaktualizowaniu certyfikatu w magazynie artefaktów
Należy rozważyć następujące scenariusze:  

**Scenariusz 1: Przy użyciu na podstawie odcisku palca certyfikatów do zabezpieczenia transferu wiadomości z mostka dla punktu końcowego**  
Rozważmy scenariusz, w których są używane certyfikaty oparte na odcisk palca w projekcie usługi BizTalk. Aktualizuj certyfikat w portalu usługi BizTalk o takiej samej nazwie, ale inny odcisk palca, ale nie zaktualizować projektu usługi BizTalk, w związku z tym. W takiej sytuacji mostka może kontynuować przetwarzanie wiadomości, ponieważ starsze dane certyfikatu może nadal być w pamięci podręcznej kanału. Po wykonaniu tej przetwarzanie komunikatu kończy się niepowodzeniem.  

**Obejście**: aktualizowanie certyfikatów w projekcie usługi BizTalk i wdrożenie projektu.  

**Scenariusz 2: Za pomocą opartego na nazwie zachowań do identyfikowania certyfikatów do zabezpieczenia transferu wiadomości z mostka dla punktu końcowego**

Rozważmy scenariusz, w których są używane na podstawie nazwy zachowania do identyfikowania certyfikaty w projekcie usługi BizTalk. Aktualizuj certyfikat w portalu usługi BizTalk, ale nie aktualizuj odpowiednio projektu usługi BizTalk. W takiej sytuacji mostka może kontynuować przetwarzanie wiadomości, ponieważ starsze dane certyfikatu może nadal być w pamięci podręcznej kanału. Po wykonaniu tej przetwarzanie komunikatu kończy się niepowodzeniem.  

**Obejście**: aktualizowanie certyfikatów w projekcie usługi BizTalk i wdrożenie projektu.  

### <a name="bridges-continue-to-process-messages-even-when-the-sql-database-is-offline"></a>Mostków dalsze przetwarzanie komunikatów, nawet wtedy, gdy baza danych SQL jest w trybie offline
Mostków usługi BizTalk Services dalsze przetwarzanie wiadomości przez pewien czas, nawet jeśli Microsoft Azure bazy danych SQL (służący do przechowywania uruchomionych informacje, takie jak wdrożonej artefakty i potoki) jest w trybie offline. Jest to spowodowane usługi BizTalk Services korzysta z pamięci podręcznej artefakty i Mostek konfiguracji.
Jeśli nie chcesz, aby mostków przetworzyć komunikaty, gdy baza danych SQL jest w trybie offline, umożliwia poleceń cmdlet programu PowerShell usługi BizTalk zatrzymać lub wstrzymać usługi BizTalk. Zobacz [przykład zarządzania usługi BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=329019) dla poleceń cmdlet programu Windows PowerShell do zarządzania operacjami.  

### <a name="reading-the-xml-message-within-a-bridges-custom-code-component-includes-an-extra-bom-character"></a>Odczytywanie wiadomości XML w składniku kod niestandardowy Mostek zawiera dodatkowy znak BOM
Rozważmy scenariusz, gdzie chcesz odczytać komunikatu XML w Mostek niestandardowego kodu. Jeśli używasz System.Text.Encoding.UTF8.GetString(bytes) interfejsu API platformy .NET, nadmiarowe znaki BOM znajduje się w danych wyjściowych na początku komunikatu. Tak, jeśli nie chcesz, aby dane wyjściowe do uwzględnienia nadmiarowe znaki BOM, należy użyć ```System.IO.StreamReader().ReadToEnd()```.

### <a name="sending-messages-to-a-bridge-using-wcf-does-not-scale"></a>Nie obsługuje wysyłanie komunikatów do mostka przy użyciu programu WCF
Komunikaty wysyłane do mostka przy użyciu usługi WCF nie działa. Zamiast tego należy używać HttpWebRequest, jeśli klient skalowalnych.

### <a name="upgrade-token-provider-error-after-upgrading-from-biztalk-services-preview-to-general-availability-ga"></a>UAKTUALNIANIE: Błąd dostawcy tokenu po uaktualnieniu z wersji zapoznawczej usługi BizTalk do dostępności ogólne (GA)
Brak EDI lub umowy AS2 z active partie. Po uaktualnieniu z wersji zapoznawczej usługi BizTalk do GA, mogą wystąpić następujące czynności:

* Błąd: Dostawca tokenu nie była w stanie tokenu zabezpieczającego. Dostawca tokenu zwrócił komunikat: nie można rozpoznać nazwy zdalnej.
* Zadania wsadowe zostały anulowane.

**Obejście**: po usługa BizTalk jest aktualizowane na ogólne dostępności (GA), należy ponownie wdrożyć umowy.  

### <a name="upgrade-toolbox-shows-the-old-bridge-icons-after-upgrading-the-biztalk-services-sdk"></a>UAKTUALNIANIE: Przybornika pokazuje starego ikony Mostek po uaktualnieniu do wersji zestawu SDK usługi BizTalk
Po uaktualnieniu starszej wersji SDK usługi BizTalk, która ma stare ikony reprezentujące mostków, przybornika w dalszym ciągu Pokaż ikony starego mostków. Jednak jeśli dodasz mostka na powierzchnię projektanta projektu usługi BizTalk powierzchni zawiera ikonę Nowy.  

**Obejście**. Można obejść ten problem, usuwając pliki .tbd w <system drive>: \Users\<użytkownika > \AppData\Local\Microsoft\VisualStudio\11.0.  

### <a name="upgrade-biztalk-portal-update-from-preview-to-ga-might-show-an-error-indicating-that-the-edi-capability-is-not-available"></a>UAKTUALNIANIE: Aktualizacja BizTalk portalu w wersji zapoznawczej GA mogą być wyświetlane komunikat o błędzie informujący, że możliwości EDI nie jest dostępna
Jeśli użytkownik jest zalogowany do portalu usługi BizTalk, gdy usługi BizTalk Services jest uaktualniany z wersji zapoznawczej do GA, może uzyskać następujący błąd w portalu:  

Ta funkcja nie jest dostępna w ramach tej wersji usług Microsoft Azure BizTalk. Użyj tych możliwości przełącznika do odpowiedniej wersji.  

**Rozdzielczość**: wylogowania z portalu, zamknij i Otwórz przeglądarkę, a następnie zaloguj się do portalu.  

### <a name="upgrade-new-tracking-data-does-not-show-up-after-biztalk-services-is-upgraded-to-ga"></a>UAKTUALNIANIE: Nowe dane śledzenia nie jest wyświetlany po uaktualnieniu do GA usługi BizTalk Services
Przykładowa scenariusz, w którym masz Mostek XML wdrożone w wersji zapoznawczej usługi BizTalk subskrypcji. Wysyłanie komunikatów do mostka i odpowiednie dane śledzenia są dostępne w portalu usługi BizTalk. Teraz Jeśli bitów środowiska uruchomieniowego BizTalk usługi portalu i usługi BizTalk Services zostaną uaktualnione do GA i wysyła komunikat do tego samego punktu końcowego mostka wdrożonej wcześniej, dane śledzenia nie jest wyświetlany dla wiadomości wysyłanych po uaktualnieniu.  

### <a name="pipelines-versus-bridges"></a>Potoki i mostków
W tym dokumencie termin "potoki" i "mostków" są używane zamiennie. Zasadniczo zarówno oznaczają to samo, który jest wdrożony na usługi BizTalk Services jednostki przetwarzania komunikatu.  

### <a name="concepts"></a>Pojęcia
[BizTalk Services](https://msdn.microsoft.com/library/azure/hh689864.aspx)   

