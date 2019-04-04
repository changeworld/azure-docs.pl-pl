---
title: Informacje o wersji dla usługi Azure BizTalk Services | Dokumentacja firmy Microsoft
description: Wymieniono znane problemy dotyczące usługi Azure BizTalk Services
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
ms.openlocfilehash: 92fc8e9edfc745ae89c2b4d44e193566292d4f08
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918879"
---
# <a name="release-notes-for-azure-biztalk-services"></a>Informacje o wersji dla usługi Azure BizTalk Services

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]
> 
> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

Informacje o wersji programu Microsoft Azure BizTalk Services zawierają znane problemy w tej wersji.

## <a name="whats-new-in-the-november-update-of-biztalk-services"></a>What's new in Listopadowa aktualizacja usługi BizTalk Services
* Można włączyć szyfrowanie danych magazynowanych w portalu usługi BizTalk Services. Zobacz [włączyć szyfrowanie danych magazynowanych w portalu usługi BizTalk Services](/previous-versions/azure/dn874052(v=azure.100)).

## <a name="update-history"></a>Historia aktualizacji
### <a name="october-update"></a>Aktualizacja z października
* Konta organizacyjne są obsługiwane:  
  * **Scenariusz**: Zarejestrowany wdrożenia usługi BizTalk, przy użyciu konta Microsoft (takich jak user@live.com). W tym scenariuszu tylko Account Microsoft użytkownicy mogą zarządzać usługi BizTalk przy użyciu portalu usługi BizTalk Services. Nie można użyć konta organizacji.  
  * **Scenariusz**: Zarejestrowany wdrażania usługi BizTalk przy użyciu konta organizacyjnego usługi Azure Active Directory (takich jak user@fabrikam.com lub user@contoso.com). W tym scenariuszu tylko użytkownicy usługi Azure Active Directory w ramach tej samej organizacji można zarządzać za pomocą portalu usługi BizTalk Services usługi BizTalk. Nie można używać konta Microsoft.  
* Podczas tworzenia usługi BizTalk, są automatycznie rejestrowane w portalu usługi BizTalk Services.
  * **Scenariusz**: Tworzenie usługi BizTalk zalogujesz się do platformy Azure, a następnie wybierz pozycję **Zarządzaj** po raz pierwszy. Po otwarciu portalu usługi BizTalk Services, usługa BizTalk automatycznie rejestruje i jest gotowa do wdrożenia.  
    Zobacz [rejestrowanie i aktualizowanie wdrożenia usługi BizTalk w BizTalk Services Portal](/previous-versions/azure/hh689837(v=azure.100)).  

### <a name="august-14-update"></a>14 sierpnia aktualizacji
* Umowy i Mostek oddzielenie — umowy z partnerami i mostków są całkowicie niezależni teraz w portalu usługi BizTalk Services. Możesz teraz tworzyć umowy i mostków osobno, a w czasie wykonywania mostków rozpoznać umowy, na podstawie wartości w komunikatów EDI. Zobacz [Tworzenie umów w usłudze Azure BizTalk Services](/previous-versions/azure/hh689908(v=azure.100)), [utworzyć mostek EDI za pomocą portalu usługi BizTalk Services](/previous-versions/azure/dn793986(v=azure.100)), [utworzyć mostek AS2, za pomocą portalu usługi BizTalk Services](/previous-versions/azure/dn793993(v=azure.100))i [ Jak mostków rozwiązać umowy w czasie wykonywania?](/previous-versions/azure/dn794001(v=azure.100))  
* Możliwość tworzenia szablonów dla umowy jest przerywane.  
* Na stronie wysyłania umowy możesz teraz określić ogranicznik różne zestawy dla każdego schematu. Ta konfiguracja jest określona w ustawieniach protokołu umowy po stronie wysyłania. Aby uzyskać więcej informacji, zobacz [Utwórz wiadomość X12 umowy w usłudze Azure BizTalk Services](/previous-versions/azure/hh689847(v=azure.100)) i [tworzenia umowy EDIFACT w usłudze Azure BizTalk Services](/previous-versions/azure/dn606267(v=azure.100)). Dwa nowe jednostki są również dodawane do interfejsu API TPM OM w tym samym celu. Zobacz [X12DelimiterOverrides](/previous-versions/azure/dn798749(v=azure.100)) i [EDIFACTDelimiterOverride](/previous-versions/azure/dn798748(v=azure.100)).  
* Konstrukcje XSD, w tym typy pochodne są teraz obsługiwane. Zobacz [XSD standardowe użycie konstrukcji w swoich mapach](/previous-versions/azure/dn793987(v=azure.100)) i [typy pochodne użycia w scenariuszach mapowania i przykłady](/previous-versions/azure/).  
* AS2 obsługuje nowych algorytmów kontroli integralności uprawnień do podpisywania wiadomości i nowych algorytmów szyfrowania. Zobacz [tworzenia umowy AS2 w usłudze Azure BizTalk Services](/previous-versions/azure/hh689890(v=azure.100)).  

## <a name="known-issues"></a>Znane problemy

### <a name="connectivity-issues-after-biztalk-services-portal-update"></a>Problemy z łącznością po aktualizacji w portalu usługi BizTalk Services
  W przypadku portalu usługi BizTalk Services otworzyć, gdy usługa BizTalk Services jest uaktualniany konieczne przywrócenie początkowej zmiany w usłudze może występować problemy z łącznością z portalu usługi BizTalk Services.  
  Jako obejście może ponownie uruchomić przeglądarkę, usuń pamięć podręczną przeglądarki lub start portalu w trybie prywatnym.  

### <a name="visual-studio-ide-cannot-locate-the-artifact-if-you-click-an-error-or-warning-in-a-biztalk-services-project"></a>Środowiska IDE programu Visual Studio nie może zlokalizować artefaktu, po kliknięciu błąd lub ostrzeżenie w projekcie usługi BizTalk Services
Zainstaluj program Visual Studio 2012 Update 3 RC 1 pozwalające rozwiązać problem.  

### <a name="custom-binding-project-reference"></a>Odwołanie do projektu powiązania niestandardowego
Należy wziąć pod uwagę następujące sytuacje, w projekcie usługi BizTalk Services w rozwiązaniu Visual Studio:  

* W tym samym rozwiązaniu Visual Studio ma projektu usługi BizTalk Services a projektem niestandardowego powiązania. Projekt usługi BizTalk zawiera odwołanie do tego pliku projektu niestandardowego powiązania.
* Projekt usługi BizTalk ma odwołania do niestandardowego powiązania/zachowanie biblioteki DLL.

Użytkownik "kompilacja" rozwiązania w programie Visual Studio pomyślnie. Następnie "Skompiluj ponownie" lub "Czysta" rozwiązania. Po tym gdy odbudować lub wyczyścić ponownie, wystąpi następujący błąd:  
  Unable to copy file <Path to DLL> to “bin\Debug\FileName.dll”. Proces uzyskać dostępu do pliku "bin\Debug\FileName.dll", ponieważ jest on używany przez inny proces.  

#### <a name="workaround"></a>Obejście
* Jeśli [Visual Studio 2012 Update 3](https://docs.microsoft.com/visualstudio/releasenotes/vs2012-update3-vs) jest zainstalowany, dostępne są następujące dwie opcje:
  
  * Uruchom ponownie program Visual Studio, lub
  * Uruchom ponownie rozwiązanie. Następnie należy wykonać tylko kompilacji rozwiązania.  
* Jeśli [Visual Studio 2012 Update 3](https://docs.microsoft.com/visualstudio/releasenotes/vs2012-update3-vs) nie jest zainstalowany, Otwórz Menedżer zadań kliknij procesy kartę, kliknij proces MSBuild.exe, a następnie kliknij przycisk Zakończ proces.  

### <a name="routing-to-basichttprelay-endpoints-is-not-supported-from-bridges-and-biztalk-services-portal-if-non-printable-characters-are-promoted-as-http-headers"></a>Routing do punktów końcowych BasicHttpRelay nie jest możliwe z mostków i portalu usługi BizTalk Services niedrukowalne znaki są przenoszone jako nagłówki HTTP
Jeśli używasz niedrukowalne znaki jako część podwyższone właściwości wiadomości te komunikaty nie kierowane do przekazywania miejsc docelowych, korzystające z wiązania BasicHttpRelay. Ponadto podwyższone właściwości, są dostępne jako część śledzenia są zakodowane jako adres URL dla obiektów blob i usunięcie zakodowanym dla miejsc docelowych.  

### <a name="mdn-is-sent-asynchronously-even-if-the-send-asynchronous-mdn-option-is-unchecked"></a>Powiadomienia MDN są wysyłane asynchronicznie, nawet jeśli zaznaczono opcję wysyłania asynchronicznego powiadomienia MDN
Należy wziąć pod uwagę w tym scenariuszu — jeśli zostanie wybrana **wysyłanie asynchronicznych powiadomień MDN** pole wyboru i określ adres URL dla wysłania asynchronicznego powiadomienia MDN i usuń zaznaczenie opcji **wysyłanie asynchronicznych powiadomień MDN** wyboru ponownie powiadomienia MDN jest nadal wysyłane do określony adres URL, nawet jeśli nie wybrano opcję, aby wysłać asynchronicznych komunikatów Mdn.  
Jako obejście tego problemu, należy wyczyścić pod określony adres URL przed usuwając zaznaczenie pola wyboru **wysyłanie asynchronicznych powiadomień MDN** pole wyboru, a następnie wdrożyć umowy AS2.  

### <a name="whitespace-characters-beyond-a-valid-interchange-cause-an-empty-message-to-be-sent-to-the-suspend-endpoint"></a>Odstęp po przekroczeniu prawidłowe wymiany spowodować pustego komunikatu do wysłania do endpoint Wstrzymaj
W przypadku spacji poza segment IEA dezasembler traktuje ją jako koniec bieżącego wymiany i analizuje następny zestaw białych znaków jako następny komunikat. Ponieważ nie jest prawidłową wymiany, może obserwować, że jeden pomyślne wiadomość jest wysyłana do lokalizacji docelowej tras i jeden pusty komunikat jest wysyłany punkt końcowy wstrzymania.  

### <a name="tracking-in-biztalk-services-portal"></a>Śledzenie w portalu usługi BizTalk Services
Zdarzenia śledzenia są przechwytywane do przetwarzania komunikatów EDI i wszelka korelacja. Jeśli komunikat nie powiedzie się poza etapu protokołu, śledzenia zostaną wyświetlone jako pomyślne. W takiej sytuacji można znaleźć w sekcji dziennika, w obszarze **szczegóły** kolumny w **śledzenia** szczegóły błędów.
X12 odbieranie i wysyłanie ustawień ([Utwórz wiadomość X12 umowy w usłudze Azure BizTalk Services](/previous-versions/azure/hh689847(v=azure.100))) zawierają informacje dotyczące etapów protokołu.  

### <a name="update-agreement"></a>Aktualizowanie umowy
Portalu usługi BizTalk Services umożliwia modyfikowanie kwalifikator tożsamości, w przypadku skonfigurowania umowę. Może to spowodować niespójne właściwości. Na przykład istnieje umowa przy użyciu ZZ:1234567 i ZZ:7654321 kwalifikator. W ustawieniach profilu w portalu usługi BizTalk Services możesz zmienić ZZ:1234567 jako 01:ChangedValue. Otwórz umowę, a 01:ChangedValue będzie wyświetlany zamiast ZZ:1234567.
Modyfikowanie kwalifikator tożsamości, usunąć umowy, aktualizowanie **tożsamości** w profilu partnera, a następnie ponownie utwórz umowy.  

> AZURE. Ostrzeżenie to zachowanie ma wpływ X12 i AS2.  
> 
> 

### <a name="as2-attachments"></a>Załączniki AS2
Załączniki dla pola AS2 komunikaty nie są obsługiwane w wysyłać ani odbierać. W szczególności załączniki dyskretnie są ignorowane i treść komunikatu jest przetwarzany jako regularne komunikatu AS2.  

### <a name="resources-remembering-path"></a>Zasoby: Uzupełnij ścieżki
Podczas dodawania **zasobów**, okno dialogowe może pamiętasz ścieżki wcześniej można dodać zasobu. Do zapamiętania wcześniej używane ścieżki, spróbuj Dodawanie witryny sieci web portalu usługi BizTalk Services **Zaufane witryny** w programie Internet Explorer.  

### <a name="if-you-rename-the-entity-name-of-a-bridge-and-close-the-project-without-saving-changes-opening-the-entity-again-results-in-an-error"></a>Jeśli możesz zmienić nazwę jednostki bridge, zamknij projekt bez zapisywania zmian ponownie otworzyć jednostki powoduje błąd
Rozważmy scenariusz, w następującej kolejności:  

* Dodawanie Most (na przykład XML One-Way mostek) do projektu usługi BizTalk  
* Zmień nazwę mostka, określając wartość dla właściwości Nazwa jednostki. To polecenie zmienia .bridgeconfig skojarzony plik przy użyciu podanej nazwy.  
* Zamknij plik .bcs (przez zamknięcie karty w programie Visual Studio), bez zapisywania zmian.  
* Otwórz plik .bcs ponownie z Eksploratora rozwiązań.  
  Można zauważyć, że chociaż .bridgeconfig skojarzony plik ma nową nazwę, który określiłeś, nazwa jednostki na powierzchni projektowej jest nadal stara nazwa. Jeśli podczas próby otwarcia do konfiguracji mostka przez dwukrotne kliknięcie składnika most, otrzymasz następujący błąd:  
  `‘<old name>’ Entity’s associated file ‘<old name>.bridgeconfig’ does not exist`
  Aby uniknąć uruchomione w tym scenariuszu, upewnij się, że zapiszesz zmiany po zmianie nazwy jednostki w projekcie usługi BizTalk.  
  
### <a name="biztalk-service-project-builds-successfully-even-if-an-artifact-has-been-excluded-from-a-visual-studio-project"></a>Usługa BizTalk projekt jest kompilowany pomyślnie nawet wtedy, gdy artefakt został wykluczony z projektu programu Visual Studio
Rozważmy scenariusz, w którym dodajesz artefaktu (na przykład plik XSD) do projektu usługi BizTalk, objęte tego artefaktu do konfiguracji mostka (na przykład, określając ją jako typ komunikatu żądania) i wykluczyć go z projektu programu Visual Studio. W takim przypadku skompilowanie projektu nie zapewni żadnych błędów tak długo, jak usunięto artefakt znajduje się na dysku w tej samej lokalizacji, z której został on uwzględniony w projekcie programu Visual Studio.
  
### <a name="the-biztalk-service-project-does-not-check-for-schema-availability-while-configuring-the-bridges"></a>Projekt usługi BizTalk nie sprawdza dostępność schematu podczas konfigurowania mostków
W projekcie usługi BizTalk Jeśli schemat, który jest dodawany do projektu importuje inny schemat projektu usługi BizTalk nie sprawdza, czy zaimportowanego schematu jest dodawany do projektu. Jeśli zostanie podjęta próba utworzenia takiego projektu, nie uzyskasz żadnych błędów kompilacji.
  
### <a name="the-response-message-for-a-xml-request-reply-bridge-is-always-of-charset-utf-8"></a>Komunikat odpowiedzi dla most "żądanie-odpowiedź" XML jest zawsze zestaw znaków UTF-8
W tej wersji zestaw znaków komunikat odpowiedzi z mostka "żądanie-odpowiedź" XML jest zawsze ustawiony na UTF-8.
  
### <a name="user-defined-datatypes"></a>Typy zdefiniowane przez użytkownika danych
Adapterów BizTalk Adapter Pack w ramach funkcji Usługa adaptera BizTalk mogą wykorzystywać zdefiniowanych przez użytkownika typy danych dla operacji karty.
Korzystając z typów zdefiniowanych przez użytkownika danych, należy skopiować pliki (.dll), dysk: \Program Files\Microsoft Service\BAServiceRuntime\bin\ adaptera BizTalk lub do globalnej pamięci podręcznej zestawów (GAC) na serwerze hostującym usługę usługa adaptera BizTalk. W przeciwnym razie może wystąpić następujący błąd, na komputerze klienckim:  
```
<s:Fault xmlns:s="http://schemas.xmlsoap.org/soap/envelope/">
<faultcode>s:Client</faultcode>
<faultstring xml:lang="en-US">The UDT with FullName "File, FileUDT, Version=Value, Culture=Value, PublicKeyToken=Value" could not be loaded. Try placing the assembly containing the UDT definition in the Global Assembly Cache.</faultstring>
<detail>
  <AFConnectRuntimeFault xmlns="http://Microsoft.ApplicationServer.Integration.AFConnect/2011" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
    <ExceptionCode>ERROR_IN_SENDING_MESSAGE</ExceptionCode>
  </AFConnectRuntimeFault>
</detail>
</s:Fault>
```  
  
> [!IMPORTANT]
> Zalecane jest, aby użyć programu GACUtil.exe można zainstalować plik w globalnej pamięci podręcznej zestawów. GACUtil.exe dokumentują sposób używania tego narzędzia i opcje wiersza polecenia programu Visual Studio.  
> 
> 

### <a name="restarting-the-biztalk-adapter-service-web-site"></a>Ponowne uruchamianie witryny sieci Web uzyskiwania informacji na temat usługi adaptera BizTalk
Instalowanie **środowiska uruchomieniowego usługi adaptera BizTalk*** tworzy **usługa adaptera BizTalk** witryny sieci web w usługach IIS, która zawiera **BAService** aplikacji. **BAService** aplikacja używa wewnętrznie powiązania przekazywania celu rozszerzenia zasięgu punktu końcowego usługi lokalnych do chmury. Dla usługi hostowanej w środowisku lokalnym odpowiedni punkt końcowy przekazywania zostanie zarejestrowana w usłudze Service Bus, tylko wtedy, gdy rozpoczyna się usługa lokalna.  

Jeśli zatrzymasz i uruchom aplikację konfiguracji do automatycznego uruchamiania aplikacji nie zostanie uznane. W takim przypadku **BAService** jest zatrzymana, należy zawsze ponownie uruchomić **usługa adaptera BizTalk** zamiast witryny sieci web. Nie uruchomić lub zatrzymać **BAService** aplikacji.

### <a name="special-characters-should-not-be-used-for-address-and-entity-names-of-lob-components"></a>Nie należy używać znaków specjalnych nazw adres i jednostki BIZNESOWE składników
Nie należy używać znaków specjalnych nazw adres i jednostki BIZNESOWE składników. Jeśli tak zrobisz, otrzymają komunikat o błędzie podczas wdrażania projektu usługi BizTalk. W przypadku niektórych znaków, takich jak "%", usługa adaptera BizTalk witryny sieci Web może przejść w stan zatrzymania i trzeba będzie uruchomić je ręcznie.

### <a name="test-map-with-get-context-property"></a>Testowanie mapy za pomocą właściwości kontekstu Get
Jeśli zawiera przekształcenia **pobieranie właściwości kontekstu** operacji mapy **mapy testu** zakończy się niepowodzeniem. Jako rozwiązanie tymczasowe, Zastąp **pobieranie właściwości kontekstu** mapy operację, podając ciągów łączenie mapy operacji zawierający fikcyjne dane. Spowoduje to wypełnić schemat docelowy i umożliwia testowanie innych funkcji przekształcenia.

### <a name="test-map-property-does-not-display"></a>Właściwości mapy testu nie są wyświetlane
**Mapy testu** właściwości nie są wyświetlane w programie Visual Studio. Taka sytuacja może wystąpić, jeśli **właściwości** okna i **Eksploratora rozwiązań** okno nie są zadokowane, jednocześnie. Aby rozwiązać ten problem, zadokować **właściwości** i **Eksploratora rozwiązań** systemu windows.  

### <a name="datetime-reformat-drop-down-is-grayed-out"></a>Ponowne formatowanie daty/godziny listy rozwijanej jest wyszarzona.
Gdy operacją mapy formatowania daty i godziny zostaną dodane do powierzchni projektowej i skonfigurowane, mogą być wyszarzone Format listy rozwijanej. Może się to zdarzyć, jeśli komputer, wyświetlana jest ustawiony **średni — 125%** lub **większe — 150%**. Aby rozwiązać problem, Ustaw wyświetlanie **mniejsze – 100% (ustawienie domyślne)** wykonując poniższe kroki:  

1. Otwórz **Panelu sterowania** i kliknij przycisk **wygląd i personalizacja**.
2. Kliknij przycisk **wyświetlania**.
3. Kliknij przycisk **mniejsze – 100% (ustawienie domyślne)** i kliknij przycisk **Zastosuj**.

**Format** listy rozwijanej teraz powinien działać zgodnie z oczekiwaniami.

### <a name="duplicate-agreements-in-the-biztalk-services-portal"></a>Zduplikowane umów w portalu usługi BizTalk Services
Rozważmy następujący scenariusz:

1. Tworzenie umowy z korzystania z handlem partnera zarządzania programu OM interfejsu API.
2. Otwórz umowę w portalu usługi BizTalk Services w dwóch różnych kartach.
3. Wdróż umowy na obu kartach.
4. W rezultacie zarówno umowy wdrożony skutkuje zduplikowane wpisy w portalu usługi BizTalk Services

**Obejście**. Otwórz dowolny zduplikowaną umów w portalu usługi BizTalk Services i cofnięcie wdrożenia.  

### <a name="bridges-do-not-use-updated-certificate-even-after-a-certificate-has-been-updated-in-the-artifact-store"></a>Mostki nie należy używać zaktualizowany certyfikat, nawet po zakończeniu certyfikatu została zaktualizowana w magazynie artefaktu
Należy rozważyć następujące scenariusze:  

**Scenariusz 1: Za pomocą na podstawie odcisku palca certyfikatów do zabezpieczenia transferu wiadomości z mostka punktu końcowego usługi**  
Rozważmy scenariusz, w którym używasz certyfikaty oparte na odcisk palca w projekcie usługi BizTalk. Zaktualizuj certyfikat w portalu usługi BizTalk Services przy użyciu tej samej nazwie, ale inny odcisk palca, ale są uaktualniane w związku z tym projektu usługi BizTalk. W takiej sytuacji mostka mogą nadal być przetwarzania komunikatów, ponieważ starsze dane certyfikatu może nadal znajdować się w pamięci podręcznej kanału. Przetwarzanie komunikatów, kończy się niepowodzeniem.  

**Obejście**: Zaktualizuj certyfikat w projekcie usługi BizTalk, a następnie ponownie wdrożyć projekt.  

**Scenariusz 2: Aby zidentyfikować certyfikatów do zabezpieczenia transferu wiadomości z mostka punktu końcowego usługi za pomocą zachowania na podstawie nazwy**

Rozważmy scenariusz, w którym używasz zachowania na podstawie nazwy do identyfikowania certyfikaty w projekcie usługi BizTalk. Aktualizuj certyfikat w portalu usługi BizTalk Services, ale są uaktualniane w związku z tym projektu usługi BizTalk. W takiej sytuacji mostka mogą nadal być przetwarzania komunikatów, ponieważ starsze dane certyfikatu może nadal znajdować się w pamięci podręcznej kanału. Przetwarzanie komunikatów, kończy się niepowodzeniem.  

**Obejście**: Zaktualizuj certyfikat w projekcie usługi BizTalk, a następnie ponownie wdrożyć projekt.  

### <a name="bridges-continue-to-process-messages-even-when-the-sql-database-is-offline"></a>Mostki w dalszym ciągu przetwarzać komunikatów, nawet wtedy, gdy baza danych SQL jest w trybie offline
Mostków usługi BizTalk Services w dalszym ciągu przetwarzać komunikaty od pewnego czasu, nawet jeśli Microsoft Azure SQL Database (która przechowuje uruchomionej informacje, takie jak wdrożonej artefaktów i potoki), jest w trybie offline. Jest to spowodowane usługi BizTalk Services korzysta z pamięci podręcznej artefaktów i konfiguracji mostka.
Jeśli użytkownik nie chce mostków przetwarzać komunikatów, gdy baza danych SQL jest w trybie offline, można użyć poleceń cmdlet programu PowerShell usługi BizTalk, aby zatrzymać lub wstrzymać usługi BizTalk. Zobacz [przykład zarządzania usługi BizTalk Azure](https://go.microsoft.com/fwlink/p/?LinkID=329019) dla poleceń cmdlet programu Windows PowerShell do zarządzania operacjami.  

### <a name="reading-the-xml-message-within-a-bridges-custom-code-component-includes-an-extra-bom-character"></a>Odczytywanie wiadomości XML w składniku kod niestandardowy Mostek zawiera nadmiarowy znak BOM
Rozważmy scenariusz, gdzie chcesz odczytać wiadomości XML w kodzie niestandardowym mostka. Jeśli używasz System.Text.Encoding.UTF8.GetString(bytes) interfejsu API .NET nadmiarowy znak BOM znajduje się w danych wyjściowych na początku komunikatu. Tak, jeśli chcesz, aby dane wyjściowe do uwzględnienia nadmiarowy znak BOM, należy użyć ```System.IO.StreamReader().ReadToEnd()```.

### <a name="sending-messages-to-a-bridge-using-wcf-does-not-scale"></a>Wysyłanie komunikatów do mostka przy użyciu usługi WCF nie jest skalowany.
Komunikaty wysyłane do mostka przy użyciu usługi WCF nie jest skalowany. Zamiast tego należy używać HttpWebRequest, jeśli klient skalowalne.

### <a name="upgrade-token-provider-error-after-upgrading-from-biztalk-services-preview-to-general-availability-ga"></a>UAKTUALNIENIE: Token błąd dostawcy, po uaktualnieniu z wersji zapoznawczej usługi BizTalk do ogólna dostępność (GA)
Brak umowy AS2 lub EDI usługi za pomocą active partii. Po uaktualnieniu usługi BizTalk w wersji zapoznawczej wersji ogólnodostępnej mogą wystąpić następujące czynności:

* Błąd: Dostawca tokenu nie mógł zapewnić tokenu zabezpieczającego. Dostawca tokenu zwrócił komunikat: Nie można rozpoznać nazwy zdalnej.
* Zadania podrzędne usługi Batch są anulowane.

**Obejście**: Po zaktualizowaniu usługi BizTalk do ogólna dostępność (GA) ponownie wdróż umowy.  

### <a name="upgrade-toolbox-shows-the-old-bridge-icons-after-upgrading-the-biztalk-services-sdk"></a>UAKTUALNIENIE: Przybornik zawiera stary ikony Mostek po uaktualnieniu z zestawem SDK usługi BizTalk
Po uaktualnieniu wcześniejszej wersji zestawu SDK usługi BizTalk, która była starego ikon reprezentujących mostków, przybornika w dalszym ciągu wyświetlać stare ikony mostków. Jednak jeśli dodasz Most do powierzchni projektanta projektu usługi BizTalk powierzchni zawiera ikonę nowego elementu.  

**Obejście**. Ten problem można obejść, usuwając pliki .tbd w obszarze <system drive>: \Users\<użytkownika > \AppData\Local\Microsoft\VisualStudio\11.0.  

### <a name="upgrade-biztalk-portal-update-from-preview-to-ga-might-show-an-error-indicating-that-the-edi-capability-is-not-available"></a>UAKTUALNIENIE: Aktualizacja portalu usługi BizTalk w wersji zapoznawczej w wersji ogólnodostępnej może wyświetlać komunikat o błędzie informujący, że możliwości integracji EDI nie jest dostępna
Jeśli użytkownik jest zalogowany do portalu usługi BizTalk Services, gdy usługa BizTalk Services jest uaktualniany z wersji zapoznawczej wersji ogólnodostępnej, być może występuje następujący błąd w portalu:  

Ta funkcja nie jest dostępna w ramach tej wersji programu Microsoft Azure BizTalk Services. Aby użyć tych możliwości Przełącz się do odpowiedniego wydania.  

**Rozwiązanie**: Wyloguj się z portalu, zamknij i Otwórz przeglądarkę, a następnie zaloguj się do portalu.  

### <a name="upgrade-new-tracking-data-does-not-show-up-after-biztalk-services-is-upgraded-to-ga"></a>UAKTUALNIENIE: Nowe dane śledzenia nie są wyświetlane po uaktualnieniu do wersji ogólnie dostępnej usługi BizTalk Services
Załóżmy scenariusz, w którym masz Mostek XML wdrożone w ramach subskrypcji usługi BizTalk w wersji zapoznawczej. Wysyłanie komunikatów do mostka, i odpowiednie dane śledzenia są dostępne w portalu usługi BizTalk Services. Teraz Jeśli bitów środowiska uruchomieniowego w portalu usługi BizTalk Services i BizTalk Services zostaną uaktualnione do wersji ogólnie dostępnej i wysłać wiadomość do tego samego punktu końcowego mostka wdrożonego wcześniej, dane śledzenia nie jest wyświetlany dla wiadomości wysyłanych po uaktualnieniu.  

### <a name="pipelines-versus-bridges"></a>Potoki i mostków
W tym dokumencie termin "potoki" i "mostków" są używane zamiennie. Zasadniczo zarówno oznaczają to samo, który jest wdrożony w usłudze BizTalk Services jednostka przetwarzania wiadomości.  

### <a name="concepts"></a>Pojęcia
[BizTalk Services](/previous-versions/azure/hh689864(v=azure.100))   

