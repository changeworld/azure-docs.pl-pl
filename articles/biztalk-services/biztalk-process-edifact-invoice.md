---
title: 'Samouczek: Przetwarzanie faktur EDIFACT za pomocą usługi Azure BizTalk Services | Dokumentacja firmy Microsoft'
description: Jak utworzyć i skonfigurować łącznik pole lub interfejsu API aplikacji i użyć go w aplikacji logiki w usłudze Azure App Service
services: biztalk-services
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: ''
ms.assetid: 7e0b93fa-3e2b-4a9c-89ef-abf1d3aa8fa9
ms.service: biztalk-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/31/2016
ms.author: deonhe
ms.openlocfilehash: 7093be36e34785d4153c257d411128efe463dee1
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918964"
---
# <a name="tutorial-process-edifact-invoices-using-azure-biztalk-services"></a>Samouczek: Przetwarzanie faktur EDIFACT za pomocą usługi Azure BizTalk Services

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Aby skonfigurować i wdrożyć X12 i umowy EDIFACT, można użyć portalu usługi BizTalk Services. W tym samouczku przyjrzymy się jak umowy EDIFACT wymiany faktur między partnerami handlowymi. Niniejszy samouczek został napisany wokół rozwiązania end-to-end firm obejmujących dwie partnerami handlowymi, Northwind i firmy Contoso, które wymianę komunikatów EDIFACT.  

## <a name="sample-based-on-this-tutorial"></a>W tym samouczku przykład
Niniejszy samouczek został napisany wokół próbkę, **wysyłania EDIFACT faktury za pomocą usługi BizTalk Services**, który jest dostępny do pobrania z [galerii kodu MSDN](https://go.microsoft.com/fwlink/?LinkId=401005). Może korzystać z przykładu, a następnie go za pomocą tego samouczka, aby zrozumieć, jak został zbudowany próbki. Ewentualnie można użyć w tym samouczku do tworzenia podstaw własnego rozwiązania. W tym samouczku jest skierowany drugiej metody, tak aby zrozumieć, jak został zbudowany tego rozwiązania. Ponadto jak najszerzej samouczka jest spójna z przykładu i używa tych samych nazw artefakty (na przykład, schematów, przekształcenia) użytymi w przykładzie.  

> [!NOTE]
> Ponieważ tego rozwiązania polega na wysyłanie wiadomości z mostka EAI do mostka EDI, ponownie używa [Mostek usługi BizTalk łańcucha przykładowe](https://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104) próbki.  
> 
> 

## <a name="what-does-the-solution-do"></a>Do czego służy rozwiązania?
W tym rozwiązaniu Northwind odbiera faktur EDIFACT z firmy Contoso. Te faktury nie są w standardowym formacie EDI. Tak przed wysłaniem faktury do Northwind, go musi zostać przekształcone do dokumentu (nazywane również ZALICZKOWĄ) faktury EDIFACT. Po otrzymaniu Northwind musi przetworzyć faktur EDIFACT i zwrócić komunikat kontroli (nazywane również CONTRL) w firmie Contoso.

![][1]  

Uzyskanie tego scenariusza biznesowego, firma Contoso używa funkcji oferowanych z usługą Microsoft Azure BizTalk Services.

* Firma Contoso używa mostków integracji EAI, aby przekształcić oryginalną fakturę ZALICZKOWĄ EDIFACT.
* Mostek EAI następnie wysyła wiadomość do wysłania EDI mostka wdrażane jako część umowy skonfigurowane w portalu usługi BizTalk Services.
* Mostek wysyłania EDI przetwarza ZALICZKOWĄ EDIFACT i kieruje je do Northwind.
* Po otrzymaniu faktury, Northwind powraca CONTRL komunikatów EDI otrzymują Mostek wdrażane jako część niniejszej Umowy.  

> [!NOTE]
> Opcjonalnie to rozwiązanie ilustruje też sposób na potrzeby przetwarzania wsadowego wysyłania faktur w partii, zamiast wysyłać każdej fakturze oddzielnie.  
> 
> 

Aby ukończyć ten scenariusz, użyjemy kolejek usługi Service Bus Faktura z firmy Contoso do Northwind lub odebrać potwierdzenia z Northwind. Te kolejki mogą być tworzone za pomocą aplikacji klienckiej, który jest dostępny do pobrania i znajduje się w pakiecie przykładowych, który jest dostępny w ramach tego samouczka.  

## <a name="prerequisites"></a>Wymagania wstępne
* Konieczne jest posiadanie przestrzeni nazw usługi Service Bus. Aby uzyskać instrukcje dotyczące tworzenia przestrzeni nazw, zobacz [How to: Utwórz lub zmodyfikuj Namespace usługi Service Bus Service](/previous-versions/azure/azure-services/hh674478(v=azure.100)). Daj nam założono, że już zainicjowano obsługę administracyjną, przestrzeni nazw usługi Service Bus o nazwie **edifactbts**.
* Musisz mieć subskrypcję usługi BizTalk Services. Na potrzeby tego samouczka, Daj nam założono, że masz subskrypcję usługi BizTalk Services, o nazwie **contosowabs**.
* Zarejestruj swoją subskrypcję usługi BizTalk Services w portalu usługi BizTalk Services. Aby uzyskać instrukcje, zobacz [rejestrowanie wdrożenia usługi BizTalk w portalu usługi BizTalk Services](/previous-versions/azure/hh689837(v=azure.100))
* Musisz mieć zainstalowany program Visual Studio.
* Musisz mieć zainstalowany zestaw SDK usługi BizTalk Services. Możesz pobrać zestaw SDK z [https://go.microsoft.com/fwlink/?LinkId=235057](https://go.microsoft.com/fwlink/?LinkId=235057)  

## <a name="step-1-create-the-service-bus-queues"></a>Krok 1: Tworzenie kolejek usługi Service Bus
To rozwiązanie używa kolejek usługi Service Bus do wymiany wiadomości między partnerami handlowymi. Contoso i Northwind wysyłają komunikaty do kolejki, z którym mostków integracji EAI i/lub EDI korzystania z nich. W tym rozwiązaniu potrzebne są trzy kolejek usługi Service Bus:

* **northwindreceive** — Northwind odbiera Faktura z firmy Contoso za pośrednictwem tej kolejki.
* **contosoreceive** — Contoso odbiera potwierdzenie z Northwind za pośrednictwem tej kolejki.
* **zawieszone** — wszystkie wstrzymane komunikaty są kierowane do tej kolejki. Komunikaty są wstrzymywane, jeżeli zakończą się niepowodzeniem podczas przetwarzania.

Te kolejki usługi Service Bus można utworzyć za pomocą uwzględniony w pakiecie przykładowych aplikacji klienckiej.  

1. Z lokalizacji, w której pobrano plik, otwórz **samouczek dotyczący wysyłania faktur przy użyciu BizTalk Services EDI Bridges.sln**.
2. Naciśnij klawisz **F5** Aby skompilować i uruchomić **samouczek klienta** aplikacji.
3. Na ekranie wprowadź przestrzeń nazw usługi Service Bus ACS, nazwa wystawcy i klucza wystawcy.
   
   ![][2]  
4. Okno komunikatu monituje o tym, że trzy kolejki zostanie utworzona w Twojej przestrzeni nazw usługi Service Bus. Kliknij przycisk **OK**.
5. Pozostaw klienta samouczek uruchamiania. Otwórz, następnie kliknij pozycję **usługi Service Bus** > ***przestrzeni nazw usługi Service Bus*** > **kolejek**i sprawdź, czy są tworzone trzy kolejki.  

## <a name="step-2-create-and-deploy-trading-partner-agreement"></a>Krok 2: Tworzenie i wdrażanie umowy z partnerem handlowym
Tworzenie umowy z partnerem handlowym między Contoso i Northwind. Umowy z partnerem handlowym definiuje kontrakt handlu partnerów dwóch biznesowych, takich jak schemat wiadomości, które używać protokołu obsługi komunikatów itp. Umowy z partnerem handlowym obejmuje dwa mostków integracji EDI, jednego do wysyłania komunikatów do partnerów handlowych (o nazwie **Wyślij EDI Mostek**) i jeden do odbierania komunikatów z partnerami handlowymi (o nazwie **otrzymywać EDI Mostek**).

W kontekście tego rozwiązania Most wysyłania EDI odpowiada stronie wysyłania umowy i jest używane do wysyłania faktur EDIFACT z firmy Contoso Northwind. Podobnie Mostek receive EDI odpowiada stronie odbierającej umowy i jest używany do potwierdzeń odbioru z Northwind.  

### <a name="create-the-trading-partners"></a>Utwórz partnerów handlowych
Na początek z Utwórz partnerów handlowych firmy Contoso i Northwind.  

1. W portalu usługi BizTalk Services na **partnerów** kliknij pozycję **Dodaj**.
2. W nowej strony partnera, wprowadź **Contoso** Nazwa partnera, a następnie kliknij przycisk **Zapisz**.
3. Powtórz krok, aby utworzyć drugiego partnera, **Northwind**.  

### <a name="create-the-agreement"></a>Tworzenie umowy z
Umowy z partnerami handlowymi są tworzone profilom firm partnerów handlowych. To rozwiązanie korzysta z domyślnych profilów partnera, które są automatycznie tworzone podczas tworzenia partnerów.  

1. W portalu usługi BizTalk Services, kliknij przycisk **umów** > **Dodaj**.
2. W nowej umowy **ustawienia ogólne** strony, podaj wartości, jak pokazano na poniższej ilustracji, a następnie kliknij **Kontynuuj**.
   
   ![][3]  
   
   Po kliknięciu **Kontynuuj**, dwie karty **ustawienia odbierania** i **ustawienia wysyłania** stają się dostępne.
3. Utwórz Wyślij umowę między Contoso i Northwind. Niniejsza Umowa decyduje o tym, jak firmy Contoso wysyła faktur EDIFACT do Northwind.
   
   1. Kliknij przycisk **ustawienia wysyłania**.
   2. Zachowaj wartości domyślne na **dla ruchu przychodzącego adresu URL**, **Przekształcanie**, i **przetwarzania wsadowego** karty.
   3. Na **protokołu** , w obszarze **schematów** sekcji, a następnie przekaż **EFACT_D93A_INVOIC.xsd** schematu. Ten schemat jest dostępny za pomocą przykładowego pakietu.
      
      ![][4]  
   4. Na **transportu** karcie, określ szczegóły dla kolejek usługi Service Bus. Na stronie wysyłania umowy, używamy **northwindreceive** kolejkę do wysyłania faktur EDIFACT do Northwind i **zawieszone** kolejki do rozsyłania komunikatów, które się nie powieść podczas przetwarzania i są wstrzymywane. Utworzono te kolejki w **krok 1: Tworzenie kolejek usługi Service Bus** (w tym temacie).
      
      ![][5]  
      
      W obszarze **ustawienia transportu > Typ transportu** i **ustawieniach zawieszenia komunikatu > Typ transportu**, wybierz pozycję Azure Service Bus i podaj wartości, jak pokazano na ilustracji.
4. Utwórz umowie odbierania między Contoso i Northwind. Niniejsza Umowa decyduje o tym, jak Contoso odbierze potwierdzenia z Northwind.
   
   1. Kliknij przycisk **otrzymał ustawienia**.
   2. Zachowaj wartości domyślne na **transportu** i **Przekształcanie** karty.
   3. Na **protokołu** , w obszarze **schematów** sekcji, a następnie przekaż **EFACT_4.1_CONTRL.xsd** schematu. Ten schemat jest dostępny za pomocą przykładowego pakietu.
   4. Na **trasy** kartę, utworzyć filtr, aby upewnić się, że tylko potwierdzeń z Northwind są kierowane do firmy Contoso. W obszarze **ustawienia trasy**, kliknij przycisk **Dodaj** utworzyć filtr routingu.
      
      ![][6]  
      
      1. Podaj wartości dla **Nazwa reguły**, **reguły trasy**, i **docelowej tras** jak pokazano na ilustracji.
      2. Kliknij pozycję **Zapisz**.
   5. Na **trasy** karcie ponownie, należy określić, gdzie potwierdzeń zawieszone (potwierdzeń, które się nie powieść podczas przetwarzania) są kierowane do. Ustaw typ transportu do usługi Azure Service Bus oraz kierować typ docelowy **kolejki**, typ uwierzytelniania do **sygnatura dostępu współdzielonego** (SAS), podaj ciąg połączenia SAS przestrzeni nazw usługi Service Bus, a następnie wprowadź nazwę kolejki jako **zawieszone**.
5. Na koniec kliknij **Wdróż** wdrażania umowy. Należy pamiętać, punkty końcowe gdzie wysyłania i odbierania wdrożony umowy.
   
   * Na **ustawienia wysyłania** , w obszarze **dla ruchu przychodzącego adresu URL**, należy pamiętać, punkt końcowy. Wysyłać wiadomość z firmy Contoso Northwind za pomocą mostka wysyłania EDI, możesz wysłać komunikat do tego punktu końcowego.
   * Na **ustawienia odbierania** , w obszarze **transportu**, należy pamiętać, punkt końcowy. Do wysyłania wiadomości z Northwind do firmy Contoso za pomocą EDI otrzymywać most, należy wysłać wiadomość do tego punktu końcowego.  

## <a name="step-3-create-and-deploy-the-biztalk-services-project"></a>Krok 3: Tworzenie i wdrażanie projektu usługi BizTalk Services
W poprzednim kroku wdrożono EDI wysyłania oraz odbierania umowy do przetwarzanie faktur EDIFACT oraz potwierdzeń. Umowy te mogą tylko przetwarzania wiadomości zgodny ze schematem standardowych komunikatów EDIFACT. Jednak na scenariuszu, w tym rozwiązaniu, Contoso wysyła faktury Northwind w schemacie własności wewnętrznych. Tak przed wysłaniem wiadomości do mostka wysyłania EDI, go musi zostać przekształcone z wewnętrznych schematu standardowego schematu faktur EDIFACT. Projektu BizTalk EAI usług dzieje.

Projekt usługi BizTalk Services **InvoiceProcessingBridge**, że przekształcenia wiadomości jest również dołączone jako część przykładu został pobrany. Projekt obejmuje następujących artefaktów:

* **INHOUSEINVOICE. XSD** — schematu faktury wewnętrznych, które są wysyłane do Northwind.
* **EFACT_D93A_INVOIC. XSD** — schemat standardowy faktur EDIFACT.
* **EFACT_4.1_CONTRL. XSD** — schematu potwierdzenie protokołu EDIFACT, która Northwind wysyła do firmy Contoso.
* **INHOUSEINVOICE_to_D93AINVOIC. TRFM** — przekształcenia mapuje schematu wewnętrznych faktury schemat standardowy faktur EDIFACT.  

### <a name="create-the-biztalk-services-project"></a>Tworzenie projektu usługi BizTalk Services
1. W rozwiązaniu Visual Studio rozwiń projekt InvoiceProcessingBridge, a następnie otwórz **MessageFlowItinerary.bcs** pliku.
2. Kliknij w dowolnym miejscu na kanwie i ustaw **adres URL usługi BizTalk** w polu właściwości, aby określić, Nazwa subskrypcji usługi BizTalk Services. Na przykład `https://contosowabs.biztalk.windows.net`.
   
   ![][7]  
3. Z przybornika przeciągnij **Mostek One-Way Xml** do kanwy. Ustaw **nazwa jednostki** i **adresu względnego** właściwości mostek z kanałem **ProcessInvoiceBridge**. Kliknij dwukrotnie **ProcessInvoiceBridge** otworzyć powierzchni konfiguracji mostka.
4. W ramach **typy komunikatów** kliknij znak plus (**+**) przycisk, aby określić schemat komunikatu przychodzącego. Ponieważ komunikatu przychodzącego mostka EAI jest zawsze wewnętrznych faktury, ustaw tę opcję na **INHOUSEINVOICE**.
   
   ![][8]  
5. Kliknij przycisk **przekształceń danych Xml** kształtu, a w polu właściwości dla **mapy** właściwości, kliknij przycisk wielokropka (**...** ) przycisku. W **wybór mapy** okno dialogowe, wybierz opcję **INHOUSEINVOICE_to_D93AINVOIC** przekształcanie plików, a następnie kliknij przycisk **OK**.
   
   ![][9]  
6. Wróć do **MessageFlowItinerary.bcs**i z przybornika przeciągnij **dwustronny jest zewnętrzny punkt końcowy usługi** po prawej stronie **ProcessInvoiceBridge**. Ustaw jego **nazwa jednostki** właściwości **EDIBridge**.
7. W Eksploratorze rozwiązań rozwiń **MessageFlowItinerary.bcs** i kliknij dwukrotnie **EDIBridge.config** pliku. Zastąp zawartość **EDIBridge.config** następującym kodem.
   
   > [!NOTE]
   > Dlaczego trzeba edytować plik Config? Punkt końcowy usługi zewnętrznej, który dodane do obszaru projektowania Mostek reprezentuje mostków integracji EDI, które firma Microsoft wdrożonego wcześniej. Mostków integracji EDI są mostków dwukierunkowe, za pomocą wysyłania i odbierania po stronie. Mostek EAI, który dodano do projektanta mostka jest jednak Mostek jednokierunkowe. Tak do obsługi wzorców exchange inny komunikat o mostki dwóch, używamy zachowanie niestandardowe bridge przez dołączenie jego konfigurację w pliku Config. Ponadto niestandardowe zachowanie obsługuje także uwierzytelniania do endpoint Mostek wysyłania EDI. To niestandardowe zachowanie jest dostępna jako osobne próbki o [Mostek usługi BizTalk łańcucha przykładowy — EAI na EDI](https://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104). To rozwiązanie używa próbki.  
   > 
   > 
   
   ```
   <?xml version="1.0" encoding="utf-8"?>
   <configuration>
     <system.serviceModel>
       <extensions>
         <behaviorExtensions>
           <add name="BridgeAuthentication" 
                 type="Microsoft.BizTalk.Bridge.Behaviour.BridgeBehaviorElement, Microsoft.BizTalk.Bridge.Behaviour, Version=1.0.0.0, Culture=neutral, PublicKeyToken=ae58f69b69495c05" />
         </behaviorExtensions>
       </extensions>
       <behaviors>
         <endpointBehaviors>
           <behavior name="BridgeAuthenticationConfiguration">
             <!-- Enter the ACS namespace, issuer name and issuer secret of the BizTalk Services deployment -->
             <BridgeAuthentication acsnamespace="[YOUR ACS NAMESPACE]" 
                                   issuername="owner" 
                                   issuersecret="[YOUR ACS SECRET]" />
             <webHttp />
           </behavior>
         </endpointBehaviors>
       </behaviors>
       <bindings>
         <webHttpBinding>
           <binding name="BridgeBindingConfiguration">
             <security mode="Transport" />
           </binding>
         </webHttpBinding>
       </bindings>
       <client>
         <clear />
         <!--
           Go BizTalk Portal > Agreement > Send Settings > Inbound URL
           Copy the Endpoint URL and paste it in the below address field
         -->
         <endpoint name="TwoWayExternalServiceEndpointReference1" 
                   address="[YOUR EDI BRIDGE SEND URI]" 
                   behaviorConfiguration="BridgeAuthenticationConfiguration" 
                   binding="webHttpBinding" 
                   bindingConfiguration="BridgeBindingConfiguration" 
                   contract="System.ServiceModel.Routing.IRequestReplyRouter" />
       </client>
     </system.serviceModel>
   </configuration>
   
   ```
8. Zaktualizuj plik EDIBridge.config zawierać szczegółowych informacji o konfiguracji
   
   * W obszarze *<behaviors>*, podaj klucz skojarzony z subskrypcją usługi BizTalk Services i przestrzeni nazw usługi ACS.
   * W obszarze *<client>*, podaj punkt końcowy wdrożonym EDI wysyłania umowy.
   
   Zapisz zmiany i zamknij plik konfiguracji.
9. W przyborniku kliknij **łącznika** i Dołącz do **ProcessInvoiceBridge** i **EDIBridge** składników. Wybierz łącznik, a w oknie właściwości ustaw **warunek filtru** do **wszystkie dopasowania**. Daje to gwarancję, że wszystkie komunikaty przetwarzane przez mostek EAI są kierowane do mostka EDI.
   
   ![][10]  
10. Zapisz zmiany w rozwiązaniu.  

### <a name="deploy-the-project"></a>Wdrażanie projektu
1. Na komputerze, na którym utworzono projekt usługi BizTalk Services należy pobrać i zainstalować certyfikat SSL dla Twojej subskrypcji usługi BizTalk Services. W obszarze usługi BizTalk Services, kliknij **pulpit nawigacyjny**, a następnie kliknij przycisk **Pobierz certyfikat SSL**. Kliknij dwukrotnie certyfikat, a następnie po wyświetleniu monitu aby ukończyć instalację. Upewnij się, należy zainstalować certyfikat w obszarze **zaufane główne urzędy certyfikacji** magazynu certyfikatów.
2. W Eksploratorze rozwiązań w usłudze Visual Studio, kliknij prawym przyciskiem myszy **InvoiceProcessingBridge** projektu, a następnie kliknij przycisk **Wdróż**.
3. Podaj wartości, jak pokazano na ilustracji, a następnie kliknij przycisk **Wdróż**. Można uzyskać poświadczeń usługi ACS dla usługi BizTalk Services, klikając **informacje o połączeniu** na pulpicie nawigacyjnym usługi BizTalk Services.
   
   ![][11]  
   
   W okienku danych wyjściowych kopiowanie punktu końcowego mostka EAI wdrożonym, na przykład `https://contosowabs.biztalk.windows.net/default/ProcessInvoiceBridge`. Ten adres URL punktu końcowego będą potrzebne później.  

## <a name="step-4-test-the-solution"></a>Krok 4: Testowanie rozwiązania
W tym temacie przyjrzymy się jak przetestować rozwiązanie za pomocą **samouczek klienta** aplikacji w ramach przykładu.  

1. W programie Visual Studio, naciśnij klawisz F5, aby rozpocząć **samouczek klienta**.
2. Ekran musi mieć wartości wstępnie wypełnione z kroku, gdzie utworzyliśmy kolejek usługi Service Bus. Kliknij przycisk **Dalej**.
3. W następnym oknie Podaj poświadczenia usługi ACS dla subskrypcji usługi BizTalk Services i punktów końcowych gdzie EAI i EDI (odbieranie) mostków są wdrażane.
   
   Punkt końcowy Mostek EAI skopiowany w poprzednim kroku. W przypadku EDI odbierać Mostek punktu końcowego, w portalu usługi BizTalk Services, przejdź do Porozumienia > Ustawienia odbierania > transportu > punktu końcowego.
   
   ![][12]  
4. W oknie dalej w obszarze Contoso, kliknij przycisk **Wyślij fakturę wewnętrznych** przycisku. W pliku, Otwórz okno dialogowe, otwórz plik INHOUSEINVOICE.txt. Sprawdź zawartość pliku, a następnie kliknij przycisk **OK** do wysłania faktury.
   
   ![][13]  
5. W ciągu kilku sekund faktury są odbierane w Northwind. Kliknij przycisk **wyświetlanie komunikatu** linku umożliwia wyświetlenie listy otrzymanych przez Northwind faktur. Zwróć uwagę, jak faktury odebranych przez Northwind jest schemat standardowy EDIFACT podczas sygnaturą wysłaną przez firmę Contoso wewnętrznych schematu.
   
   ![][14]  
6. Wybierz faktury, a następnie kliknij przycisk **potwierdzenia wysyłania**. W oknie dialogowym, które się pojawi Zwróć uwagę, czy identyfikator wymiany jest taki sam w otrzymanej faktury i potwierdzenie są wysyłane. Kliknij przycisk OK w **potwierdzenia wysyłania** okno dialogowe.
   
   ![][15]  
7. W ciągu kilku sekund potwierdzenia została odebrana pomyślnie w firmie Contoso.
   
   ![][16]  

## <a name="step-5-optional-send-edifact-invoice-in-batches"></a>Krok 5 (opcjonalnie): Wyślij fakturę EDIFACT w partiach
Mostki integracji EDI usługi BizTalk obsługuje również przetwarzanie wsadowe wiadomości wychodzących. Ta funkcja jest przydatna do odbierania partnerów, którzy preferują partię komunikatów (spełniające określone kryterium) zamiast pojedynczych komunikatów.

Najważniejszym aspektem podczas pracy z partii jest rzeczywista wersji usługi Batch, jest określana skrótem kryteriach zwalniania. Kryteria zwalniania może bazować na jak chce odbierać komunikaty odbierający partnera. Jeśli włączono dzielenia na partie Mostek EDI nie wysyła komunikatu wychodzącego do odbierania partnera kryteriach zwalniania jest realizowany. Na przykład kryteriów przetwarzania wsadowego na podstawie wysyłki rozmiar komunikatu partii tylko wtedy, gdy "n" komunikaty są przetwarzane wsadowo. Kryteria batch może być również oparte na czasie taki sposób, że usługi batch są wysyłane w stałym czasie każdego dnia. W tym rozwiązaniu spróbujemy kryteria na podstawie rozmiaru wiadomości.

1. W portalu usługi BizTalk Services kliknij umowę, która została utworzona wcześniej. Kliknij przycisk Wyślij Ustawienia > Przetwarzanie wsadowe > Dodaj usługi Batch.
2. Wprowadź nazwę instancji **InvoiceBatch**, podaj opis, a następnie kliknij **dalej**.
3. Określ kryteria usługi batch, definiujących, wiadomości, które muszą być partii. W tym rozwiązaniu firma partii wszystkie komunikaty. Tak, wybierz opcję Użyj definicji opcji zaawansowanej i wprowadź **1 = 1**. Jest warunek, który zawsze będzie mieć wartość true, a więc wszystkie komunikaty zostaną partii. Kliknij przycisk **Dalej**.
   
   ![][17]  
4. Określanie kryteriów publikowania partii. W polu listy, wybierz **MessageCountBased**oraz **liczba**, określ **3**. Oznacza to, że trzy komunikaty zbiorczo będą wysyłane do Northwind. Kliknij przycisk **Dalej**.
   
   ![][18]  
5. Przejrzyj podsumowanie, a następnie kliknij przycisk **Zapisz**. Kliknij przycisk **Wdróż** do ponownego wdrożenia umowy.
6. Wróć do **samouczek klienta**, kliknij przycisk **Wyślij fakturę wewnętrznych**i postępuj zgodnie z monitami, aby wysłać faktury. Zauważysz, że faktury nie są odbierane w Northwind, ponieważ rozmiar partii nie jest spełniony. Powtórz ten krok dwa razy, dlatego, że masz trzy faktury komunikaty wysyłane do Northwind. Spełnia on kryteriów publikowania partii 3 wiadomości i powinien zostać wyświetlony faktury w Northwind.

<!--Image references-->
[1]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-1.PNG  
[2]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-2.PNG  
[3]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-3.PNG
[4]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-4.PNG  
[5]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-5.PNG  
[6]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-6.PNG  
[7]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-7.PNG  
[8]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-8.PNG
[9]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-9.PNG  
[10]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-10.PNG  
[11]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-11.PNG  
[12]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-12.PNG  
[13]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-13.PNG
[14]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-14.PNG  
[15]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-15.PNG  
[16]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-16.PNG  
[17]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-17.PNG  
[18]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-18.PNG

