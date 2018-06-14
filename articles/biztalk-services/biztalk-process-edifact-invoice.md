---
title: 'Samouczek: Przetworzyć EDIFACT faktury za pomocą usług Azure BizTalk | Dokumentacja firmy Microsoft'
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
ms.openlocfilehash: 2ebd6a8cb70f218c3b56bc78c9b853dbf51ab468
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2017
ms.locfileid: "26633869"
---
# <a name="tutorial-process-edifact-invoices-using-azure-biztalk-services"></a>Samouczek: Proces EDIFACT faktury za pomocą usługi Azure BizTalk Services

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Portal usługi BizTalk umożliwia konfigurowanie i wdrażanie X12 i umów EDIFACT. W ramach tego samouczka przyjrzymy się Tworzenie umów EDIFACT wymiany faktury między partnerami handlowymi. Ten samouczek jest przeznaczony wokół obejmujące dwoma partnerami handlowymi Northwind i Contoso, które wymiany wiadomości EDIFACT firm end-to-end.  

## <a name="sample-based-on-this-tutorial"></a>Przykładowe na podstawie w tym samouczku
Ten samouczek jest przeznaczony wokół próbki **wysyłania EDIFACT faktury za pomocą usługi BizTalk Services**, który jest dostępny do pobrania z [galerii kodu MSDN](http://go.microsoft.com/fwlink/?LinkId=401005). Można użyć próbki i przejść opisanych w tym samouczku, aby zrozumieć, jak próbki został skompilowany. Lub, w tym samouczku można użyć do utworzenia podstaw własne rozwiązania. W tym samouczku jest przeznaczona do drugiej metody, aby zrozumieć, jak to rozwiązanie został skompilowany. Również jak to możliwe, samouczka są zgodne z próbki i używa tych samych nazw artefaktów (na przykład, schematów, przekształceń) co w przykładzie.  

> [!NOTE]
> Ponieważ to rozwiązanie polega na wysyłanie wiadomości z mostka EAI Mostek EDI, ponownie [Mostek usługi BizTalk łańcucha próbki](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104) próbki.  
> 
> 

## <a name="what-does-the-solution-do"></a>Do czego służy rozwiązania?
W tym rozwiązaniu Northwind odbiera faktury EDIFACT z firmy Contoso. Te faktury nie znajdują się w standardowym formacie EDI. Tak przed wysłaniem faktury Northwind, go musi zostać przekształcone do dokumentu EDIFACT faktury (nazywanych również ZALICZKOWĄ). Po otrzymaniu Northwind musi przetworzyć faktury EDIFACT i Przywróć komunikat kontrolny (nazywanych również CONTRL) firmy Contoso.

![][1]  

Aby osiągnąć tego scenariusza biznesowego, firma Contoso używa funkcji dostarczane z usługi Microsoft Azure BizTalk.

* Firma Contoso używa mostków EAI oryginalną fakturę ZALICZKOWĄ EDIFACT transformacji.
* Mostek EAI następnie wysyła wiadomość do wysłania EDI mostka wdrożenia w ramach umowy skonfigurowane w portalu usługi BizTalk.
* Mostek wysyłania EDI przetwarza ZALICZKOWĄ EDIFACT i kieruje je do Northwind.
* Po otrzymaniu faktury, zwraca Northwind CONTRL komunikatów do EDI odbierać Mostek wdrożenia w ramach niniejszej Umowy.  

> [!NOTE]
> Opcjonalnie to rozwiązanie także przedstawiono sposób na potrzeby przetwarzania wsadowego wysłać faktury w partii, zamiast wysyłać każdej faktury oddzielnie.  
> 
> 

Aby ukończyć scenariusz, używamy kolejek usługi Service Bus Faktura z domeny Contoso do Northwind lub odebrać potwierdzenia z Northwind. Te kolejki mogą być tworzone za pomocą aplikacji klienckiej, która jest dostępna do pobrania i znajduje się w pakiecie próbki, który jest dostępny w ramach tego samouczka.  

## <a name="prerequisites"></a>Wymagania wstępne
* Musi mieć nazw usługi Service Bus. Aby uzyskać instrukcje dotyczące tworzenia przestrzeni nazw, zobacz [jak: utworzyć lub zmodyfikować Namespace usługi magistrali usługi](https://msdn.microsoft.com/library/azure/hh674478.aspx). Załóżmy już przestrzeń nazw magistrali usług alokacji o nazwie **edifactbts**.
* Musisz mieć subskrypcję usługi BizTalk Services. W tym samouczku, poinformuj nas założono, że masz subskrypcję usługi BizTalk Services o nazwie **contosowabs**.
* Zarejestruj subskrypcję usługi BizTalk Services w portalu usługi BizTalk. Aby uzyskać instrukcje, zobacz [rejestrowanie wdrożenia usługi BizTalk w portalu usługi BizTalk](https://msdn.microsoft.com/library/hh689837.aspx)
* Musi mieć zainstalowanego programu Visual Studio.
* Musisz mieć zainstalowany zestaw SDK usług BizTalk. Możesz pobrać zestaw SDK z [http://go.microsoft.com/fwlink/?LinkId=235057](http://go.microsoft.com/fwlink/?LinkId=235057)  

## <a name="step-1-create-the-service-bus-queues"></a>Krok 1: Tworzenie kolejek usługi Service Bus
To rozwiązanie używa kolejek usługi Service Bus do wymiany wiadomości między partnerami handlowymi. Contoso i Northwind wysyłać wiadomości do kolejki, z którym mostków EAI i/lub EDI mogły ich używać. W tym rozwiązaniu są potrzebne trzy kolejek usługi Service Bus:

* **northwindreceive** — Northwind odebrał faktury z Contoso za pośrednictwem tej kolejki.
* **contosoreceive** — Contoso odebrał potwierdzenia z Northwind za pośrednictwem tej kolejki.
* **zawieszone** — wszystkie wstrzymane komunikaty są kierowane do tej kolejki. Komunikaty są wstrzymane w razie awarii podczas przetwarzania.

Te kolejki usługi Service Bus można utworzyć za pomocą aplikacji klienckiej uwzględniony w pakiecie w próbce.  

1. Z lokalizacji, do którego pobrano próbki, należy otworzyć **samouczek wysyłania faktury za pomocą usług BizTalk — wersja usługi EDI Bridges.sln**.
2. Naciśnij klawisz **F5** Aby skompilować i uruchomić **klienta samouczek** aplikacji.
3. Na ekranie wprowadź przestrzeń nazw usługi ACS magistrali, nazwa wystawcy i klucza wystawcy.
   
   ![][2]  
4. Okno komunikatu monituje o tym, że zostaną utworzone trzy kolejki w przestrzeni nazw magistrali usług. Kliknij przycisk **OK**.
5. Pozostaw klient Samouczek systemu. Otwórz kliknij **usługi Service Bus** > ***przestrzeni nazw usługi Service Bus*** > **kolejek**i sprawdź, czy są tworzone trzy kolejki.  

## <a name="step-2-create-and-deploy-trading-partner-agreement"></a>Krok 2: Tworzenie i wdrażanie umowy z partnerem handlowym
Tworzenie umowy z partnerem handlowym między Contoso i Northwind. Umowy z partnerem handlowym definiuje kontrakt handlu partnerów biznesowych dwa, takich jak które schematu komunikat do użycia, które obsługi wiadomości protokołu itp. Umowy z partnerem handlowym obejmuje dwa mostków EDI, jednego do wysyłania komunikatów do partnerami handlowymi (o nazwie **wysyłania EDI Mostek**) i jeden do odbierania wiadomości z partnerami handlowymi (o nazwie **odbierania EDI Mostek**).

W kontekście tego rozwiązania Mostek wysyłania EDI odpowiada stronie wysyłającej umowy i jest używany do wysyłania faktury EDIFACT z domeny Contoso do Northwind. Podobnie Mostek receive EDI odpowiada skalowanie po stronie umowy i jest używany do odbierania potwierdzeń z Northwind.  

### <a name="create-the-trading-partners"></a>Utwórz partnerami handlowymi
Do utworzenia partnerami handlowymi Contoso i Northwind.  

1. W portalu usługi BizTalk na **partnerów** , kliknij pozycję **Dodaj**.
2. W nowej strony partnera, wprowadź **Contoso** Nazwa partnera, a następnie kliknij przycisk **zapisać**.
3. Powtórz krok, aby utworzyć drugiego partnera, **Northwind**.  

### <a name="create-the-agreement"></a>Tworzenie umowy
Umowy z partnerami handlowymi są tworzone profilom biznesowych z partnerami handlowymi. To rozwiązanie używa domyślnych profilów partnera, które są automatycznie tworzone podczas utworzyliśmy partnerów.  

1. W portalu usługi BizTalk, kliknij przycisk **umowy** > **Dodaj**.
2. W nowej umowy **ustawienia ogólne** określić wartości, jak pokazano na poniższej ilustracji, a następnie kliknij pozycję **Kontynuuj**.
   
   ![][3]  
   
   Po kliknięciu **Kontynuuj**, dwie karty, **ustawienia odbierania** i **ustawienia wysyłania** staną się dostępne.
3. Tworzenie umowy wysyłania między Contoso i Northwind. Niniejsza Umowa decyduje o tym, jak Contoso wysyła faktury EDIFACT Northwind.
   
   1. Kliknij przycisk **ustawień wysyłania**.
   2. Zachowaj wartości domyślne na **adres URL komunikatów przychodzących**, **przekształcenie**, i **wsadowe** karty.
   3. Na **protokołu** , w obszarze **schematy** sekcji, Przekaż **EFACT_D93A_INVOIC.xsd** schematu. Ten schemat jest dostępna z pakietem próbki.
      
      ![][4]  
   4. Na **transportu** karcie, określ szczegóły dla kolejek usługi Service Bus. Umowy po stronie send stosujemy **northwindreceive** kolejkę do wysyłania do Northwind, faktury EDIFACT i **zawieszone** kolejki rozesłać komunikaty zakończyć się niepowodzeniem podczas przetwarzania, które są wstrzymane. Utworzono te kolejki w **krok 1: tworzenie kolejek usługi Service Bus** (w tym temacie).
      
      ![][5]  
      
      W obszarze **ustawienia transportu > Typ transportu** i **ustawienia zawieszenia wiadomości > Typ transportu**, wybierz usługi Azure Service Bus i podaj wartości, jak pokazano w obrazie.
4. Tworzenie umowy receive między Contoso i Northwind. Niniejsza Umowa decyduje o tym, jak Contoso odbiera potwierdzenia z Northwind.
   
   1. Kliknij przycisk **odbierają ustawienia**.
   2. Zachowaj wartości domyślne na **transportu** i **przekształcenie** karty.
   3. Na **protokołu** , w obszarze **schematy** sekcji, Przekaż **EFACT_4.1_CONTRL.xsd** schematu. Ten schemat jest dostępna z pakietem próbki.
   4. Na **trasy** pozycję Utwórz filtr w celu zapewnienia, że tylko potwierdzeń z Northwind są kierowane do firmy Contoso. W obszarze **ustawienia trasy**, kliknij przycisk **Dodaj** do tworzenia filtru routingu.
      
      ![][6]  
      
      1. Podaj wartości dla **Nazwa reguły**, **reguły trasy**, i **docelowy trasy** jak pokazano w obrazie.
      2. Kliknij pozycję **Zapisz**.
   5. Na **trasy** ponownie karcie, określ, gdzie potwierdzeń zawieszone (potwierdzeń, które nie są podczas przetwarzania) są kierowane do. Ustaw typ transportu do usługi Azure Service Bus, route typ docelowy **kolejki**, typ uwierzytelniania do **sygnatura dostępu współdzielonego** (SAS), podaj ciąg połączenia SAS nazw usługi Service Bus a następnie wprowadź nazwę kolejki jako **zawieszone**.
5. Na koniec kliknij **Wdróż** wdrażania umowy. Należy pamiętać, punkty końcowe gdzie wysyłania i odbierania wdrożony umowy.
   
   * Na **ustawienia wysyłania** , w obszarze **adres URL komunikatów przychodzących**, należy zwrócić uwagę punktu końcowego. Aby wysłać wiadomość z domeny Contoso do Northwind przy użyciu mostu wysyłania EDI, możesz wysłać wiadomość do tego punktu końcowego.
   * Na **ustawienia odbierania** , w obszarze **transportu**, należy zwrócić uwagę punktu końcowego. Aby wysłać wiadomość z Northwind w firmie Contoso za pomocą EDI odbierania Mostek musi wysłać wiadomość do tego punktu końcowego.  

## <a name="step-3-create-and-deploy-the-biztalk-services-project"></a>Krok 3: Tworzenie i wdrażanie projektu usługi BizTalk Services
W poprzednim kroku wdrożone EDI wysyłania i odbierania umowy, aby przetworzyć EDIFACT faktury i potwierdzeń. Umowy te mogą tylko przetwarzania komunikatów o zgodnych ze standardami standardowe schematu EDIFACT w wiadomości. Jednak na scenariuszu w przypadku tego rozwiązania firmy Contoso wysyła faktury Northwind w schemacie zastrzeżonych wewnętrznych. Tak przed wysłaniem wiadomości do mostka wysyłania EDI, go musi zostać przekształcone ze schematu wewnętrznych standardowe schematu faktury EDIFACT. Projekt BizTalk EAI usług robi to.

Projekt usługi BizTalk Services **InvoiceProcessingBridge**, że transformacje wiadomość jest również uwzględniane jako część próbki został pobrany. Projekt zawiera następujące artefakty:

* **INHOUSEINVOICE. XSD** — schematu wewnętrznych faktury, które są wysyłane do Northwind.
* **EFACT_D93A_INVOIC. XSD** — schematu standardowe EDIFACT faktury.
* **EFACT_4.1_CONTRL. XSD** — schematu potwierdzenia EDIFACT, która Northwind wysyła do firmy Contoso.
* **INHOUSEINVOICE_to_D93AINVOIC. TRFM** — mapowanego schematu wewnętrznych faktury standardowe schematu faktury EDIFACT transformacji.  

### <a name="create-the-biztalk-services-project"></a>Tworzenie projektu usługi BizTalk Services
1. W rozwiązaniu Visual Studio rozwiń projekt InvoiceProcessingBridge, a następnie otwórz **MessageFlowItinerary.bcs** pliku.
2. Kliknij w dowolnym miejscu w obszarze roboczym i ustaw **adres URL usługi BizTalk** w polu właściwości, aby określić nazwę subskrypcji usługi BizTalk Services. Na przykład `https://contosowabs.biztalk.windows.net`.
   
   ![][7]  
3. Przeciągnij z przybornika, **Mostek One-Way Xml** do obszaru roboczego. Ustaw **nazwa jednostki** i **adres względny** właściwości mostek z **ProcessInvoiceBridge**. Kliknij dwukrotnie **ProcessInvoiceBridge** aby otworzyć obszar konfiguracji mostu.
4. W ramach **typów komunikatów** , kliknij przycisk plus (**+**) przycisk, aby określić schematu wiadomości przychodzącej. Ponieważ komunikat przychodzący dla mostu EAI jest zawsze wewnętrznych faktury, ustaw tę wartość na **INHOUSEINVOICE**.
   
   ![][8]  
5. Kliknij przycisk **transformacji Xml** kształtu, a w polu właściwości dla **mapy** właściwości, kliknij przycisk wielokropka (**...** ) przycisku. W **wybór mapy** okno dialogowe, wybierz opcję **INHOUSEINVOICE_to_D93AINVOIC** Przekształć plik, a następnie kliknij przycisk **OK**.
   
   ![][9]  
6. Wróć do **MessageFlowItinerary.bcs**i przeciągnij z przybornika, **dwustronny zewnętrznego punktu końcowego usługi** z prawej strony **ProcessInvoiceBridge**. Ustaw jego **nazwa jednostki** właściwości **EDIBridge**.
7. W Eksploratorze rozwiązań rozwiń **MessageFlowItinerary.bcs** i kliknij dwukrotnie **EDIBridge.config** pliku. Zamień zawartość **EDIBridge.config** następującym kodem.
   
   > [!NOTE]
   > Dlaczego trzeba edytować pliku .config? Punkt końcowy zewnętrznej usługi, który dodano do obszaru projektowania Mostek reprezentuje mostków EDI, które wcześniej wdrożone. Mostków EDI są mostków dwukierunkowe, z send i skalowanie po stronie. Mostek EAI dodaną do projektanta mostek jest jednak Mostek jednokierunkowe. Tak do obsługi wzorców exchange inny komunikat dwóch mostków, używamy zachowanie niestandardowych Mostek przez dołączenie jego konfigurację do pliku .config. Ponadto niestandardowe zachowanie również obsługę uwierzytelniania do punktu końcowego mostka wysyłania EDI. To zachowanie niestandardowych jest dostępna jako osobne próbki w [Mostek usługi BizTalk łańcucha próbki - EAI do EDI](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104). To rozwiązanie próbki są ponownie używane.  
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
8. Zaktualizuj plik EDIBridge.config uwzględnienie szczegółów konfiguracji
   
   * W obszarze  *<behaviors>* , podaj ACS przestrzeni nazw i klucz skojarzony z subskrypcją usługi BizTalk Services.
   * W obszarze  *<client>* , podaj wdrożonym umowy wysyłania EDI punktu końcowego.
   
   Zapisz zmiany i zamknij plik konfiguracji.
9. Z przybornika, kliknij przycisk **łącznik** , a następnie dołącz **ProcessInvoiceBridge** i **EDIBridge** składników. Wybierz łącznik, a w polu właściwości należy ustawić **warunek filtru** do **dopasowanie wszystkich**. Daje to pewność, że wszystkie komunikaty przetwarzane przez mostek EAI są kierowane do Mostek EDI.
   
   ![][10]  
10. Zapisz zmiany w rozwiązaniu.  

### <a name="deploy-the-project"></a>Wdrażanie projektu
1. Na komputerze, na którym utworzono projekt usługi BizTalk Services Pobierz i zainstaluj certyfikat SSL dla Twojej subskrypcji usługi BizTalk Services. W obszarze usługi BizTalk Services kliknij **pulpitu nawigacyjnego**, a następnie kliknij przycisk **Pobierz certyfikat SSL**. Kliknij dwukrotnie certyfikat i wykonaj wiersz, aby zakończyć instalację. Upewnij się, zainstaluj certyfikat w obszarze **zaufane główne urzędy certyfikacji** magazynu certyfikatów.
2. W Eksploratorze rozwiązań programu Visual Studio, kliknij prawym przyciskiem myszy **InvoiceProcessingBridge** projektu, a następnie kliknij przycisk **Wdróż**.
3. Podaj wartości, jak pokazano na ilustracji, a następnie kliknij przycisk **Wdróż**. Możesz też uzyskać poświadczenia ACS usługi BizTalk, klikając **informacje o połączeniu** na pulpicie nawigacyjnym usługi BizTalk Services.
   
   ![][11]  
   
   W okienku danych wyjściowych, skopiuj punktu końcowego mostka EAI wdrożonym, na przykład `https://contosowabs.biztalk.windows.net/default/ProcessInvoiceBridge`. Ten adres URL punktu końcowego będą potrzebne później.  

## <a name="step-4-test-the-solution"></a>Krok 4: Testowanie rozwiązania
W tym temacie opisano, jak przetestować rozwiązania przy użyciu **klienta samouczek** aplikacji w ramach przykładu.  

1. W programie Visual Studio, naciśnij klawisz F5, aby uruchomić **klienta samouczek**.
2. Ekran musi mieć wartości wstępnie z kroku, gdzie utworzyliśmy kolejek usługi Service Bus. Kliknij przycisk **Dalej**.
3. W następnym oknie, podaj poświadczenia usługi ACS dla subskrypcji usługi BizTalk Services i punkty końcowe gdzie EAI i EDI (odbierać) mostków są wdrażane.
   
   Punkt końcowy usługi EAI Mostek skopiowany w poprzednim kroku. W przypadku EDI odbierać Mostek punktu końcowego, w portalu usługi BizTalk, przejdź do Porozumienia > Ustawienia odbierania > transportu > punktu końcowego.
   
   ![][12]  
4. W następnym oknie, w obszarze Contoso, kliknij przycisk **Wyślij fakturę wewnętrznych** przycisku. W pliku — okno dialogowe, otwórz plik INHOUSEINVOICE.txt. Sprawdź zawartość pliku, a następnie kliknij przycisk **OK** wysłać faktury.
   
   ![][13]  
5. W ciągu kilku sekund faktury odebraniu Northwind. Kliknij przycisk **komunikat widoku** łącze, aby wyświetlić faktury odebranych przez Northwind. Należy zauważyć, jak faktury odebranych przez Northwind jest w schemacie EDIFACT standardowe podczas sygnaturą wysłaną przez firmę Contoso wewnętrznych schematu.
   
   ![][14]  
6. Wybierz faktury, a następnie kliknij przycisk **potwierdzenia wysyłania**. W oknie dialogowym, które pojawia się należy zauważyć, że identyfikator wymiany jest tej samej otrzymanej faktury i potwierdzenie są wysyłane. Kliknij przycisk OK w **potwierdzenia wysyłania** okno dialogowe.
   
   ![][15]  
7. W ciągu kilku sekund potwierdzenia została odebrana pomyślnie w firmie Contoso.
   
   ![][16]  

## <a name="step-5-optional-send-edifact-invoice-in-batches"></a>Krok 5 (opcjonalny): faktury wysyłania EDIFACT w partiach
Mostków EDI usługi BizTalk obsługuje również tworzenie partii komunikatów wychodzących. Ta funkcja jest przydatna do odbierania partnerów, którzy preferują partii wiadomości (spełniające określone kryterium) zamiast poszczególnych wiadomości.

Najważniejszym aspektem podczas pracy z partii jest rzeczywista wersja partii, nazywany również kryteria wersji. Kryteria zlecenia może bazować na sposób odbierania partnera chce odbierać wiadomości. Jeśli włączono tworzenie plików wsadowych Mostek EDI nie wysyła komunikatu wychodzącego do odbierania partnera do momentu wypełnienia kryteriów wersji. Na przykład, przetwarzanie wsadowe kryteria na podstawie komunikatów wysyła rozmiar partii tylko wtedy, gdy "n" wiadomości są przetwarzane wsadowo. Kryteria partii można też na podstawie czasu taki sposób, że plik wsadowy jest wysyłany o stałej godzinie każdego dnia. W tym rozwiązaniu spróbujemy kryteriów na podstawie rozmiaru dla wiadomości.

1. W portalu usługi BizTalk kliknij umowę, utworzony wcześniej. Kliknij przycisk Wyślij Ustawienia > Przetwarzanie wsadowe > Dodaj partii.
2. Wprowadź nazwę usługi partia zadań **InvoiceBatch**, podaj opis, a następnie kliknij przycisk **dalej**.
3. Określ kryteria partii, które definiuje, które wiadomości musi można umieścić w partii. W tym rozwiązaniu możemy partii wszystkie komunikaty. Tak, wybrać zaawansowanych opcji definicje i wprowadź **1 = 1**. To jest warunek, który będzie zawsze równa true, i dlatego wszystkie komunikaty będzie można umieścić w partii. Kliknij przycisk **Dalej**.
   
   ![][17]  
4. Określ kryteria wersji partii. W polu listy, wybierz **MessageCountBased**oraz **liczba**, określ **3**. Oznacza to, że trzy komunikaty zbiorczo zostaną wysłane do Northwind. Kliknij przycisk **Dalej**.
   
   ![][18]  
5. Przejrzyj podsumowanie, a następnie kliknij przycisk **zapisać**. Kliknij przycisk **Wdróż** można wdrożyć ponownie umowy.
6. Wróć do **klienta samouczek**, kliknij przycisk **Wyślij fakturę wewnętrznych**i postępuj zgodnie z monitami, aby wysłać faktury. Można zauważyć, że w firmie Northwind nieodebrania nie faktury, ponieważ rozmiar partii nie są spełnione. Powtórz ten krok dwa razy, tak aby było możliwe faktury wiadomości wysyłane do Northwind. To nie będzie spełniał kryteriów wersji partii, 3 wiadomości i powinien zostać wyświetlony w firmie Northwind faktury.

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

