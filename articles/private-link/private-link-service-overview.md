---
title: Co to jest usługa Azure Private Link?
description: Dowiedz się więcej o usłudze Azure Private Link.
services: private-link
author: sumeetmittal
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: sumi
ms.openlocfilehash: 2cc6c577abdb3698ef6aca1f1f04d239f09d119c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280432"
---
# <a name="what-is-azure-private-link-service"></a>Co to jest usługa Azure Private Link?

Usługa Azure Private Link jest odwołaniem do własnej usługi, która jest obsługiwana przez usługę Azure Private Link. Usługa działająca za pomocą [standardowego modułu równoważenia obciążenia platformy Azure](../load-balancer/load-balancer-standard-overview.md) można włączyć dla dostępu do łączy prywatnych, dzięki czemu konsumenci usługi mogą uzyskiwać do niej prywatny dostęp z własnych sieci wirtualnych. Klienci mogą utworzyć prywatny punkt końcowy wewnątrz sieci wirtualnej i zamapować go na tę usługę. W tym artykule wyjaśniono pojęcia związane po stronie dostawcy usług. 

## <a name="workflow"></a>Przepływ pracy

![Przepływ pracy usługi Łącze prywatne](media/private-link-service-overview/private-link-service-workflow.png)

### <a name="create-your-private-link-service"></a>Tworzenie usługi private link

- Skonfiguruj aplikację tak, aby działała za standardowym modułem równoważenia obciążenia w sieci wirtualnej. Jeśli aplikacja jest już skonfigurowana za standardowym modułem równoważenia obciążenia, możesz pominąć ten krok.   
- Utwórz usługę łącza prywatnego, odwołując się do modułu równoważenia obciążenia powyżej. W procesie wyboru modułu równoważenia obciążenia wybierz konfigurację IP frontendu, w której chcesz odbierać ruch. Wybierz podsieć adresów IP NAT dla usługi łącza prywatnego. Zaleca się, aby w podsieci było co najmniej osiem adresów IP NAT. Cały ruch konsumentów będzie pochodzić z tej puli prywatnych adresów IP do dostawcy usług. Wybierz odpowiednie właściwości/ustawienia usługi łącza prywatnego.    

    > [!NOTE]
    > Usługa Azure Private Link service jest obsługiwana tylko w standardowym modułie równoważenia obciążenia. 
    
### <a name="share-your-service"></a>Udostępnianie usługi

Po utworzeniu usługi łącze prywatne platforma Azure wygeneruje globalnie unikatowy pseudonim o nazwie "alias" na podstawie nazwy, którą udostępniasz dla usługi. Alias lub identyfikator URI zasobów usługi można udostępnić klientom w trybie offline. Konsumenci mogą rozpocząć połączenie łącza prywatnego przy użyciu aliasu lub identyfikatora URI zasobu.
 
### <a name="manage-your-connection-requests"></a>Zarządzanie żądaniami połączenia

Po zainicjowaniu połączenia przez konsumenta dostawca usług może zaakceptować lub odrzucić żądanie połączenia. Wszystkie żądania połączenia zostaną wyświetlone w ramach właściwości **privateendpointconnections** w usłudze Private Link.
 
### <a name="delete-your-service"></a>Usuwanie usługi

Jeśli usługa Private Link nie jest już używana, można ją usunąć. Jednak przed usunięciem usługi upewnij się, że nie ma żadnych połączeń prywatnych punktów końcowych skojarzonych z nim. Można odrzucić wszystkie połączenia i usunąć usługę.

## <a name="properties"></a>Właściwości

Usługa łącze prywatne określa następujące właściwości: 

|Właściwość |Wyjaśnienie  |
|---------|---------|
|APOJUJ (ApoKAZ obsługi administracyjnej)  |Właściwość tylko do odczytu, która wyświetla bieżący stan inicjowania obsługi administracyjnej dla usługi Private Link. Obowiązującymi stanami inicjowania obsługi administracyjnej są: "Usuwanie; Nie powiodło się; Udało się; Aktualizacja". Gdy stan inicjowania obsługi administracyjnej jest "Powodzenie", pomyślnie zainicjowano usługę Private Link.        |
|Alias (alias)     | Alias to unikatowy na skalę globalny ciąg tylko do odczytu dla usługi. Pomaga maskować dane klienta dla usługi, a jednocześnie tworzy nazwę łatwe do udostępnienia dla usługi. Podczas tworzenia usługi łącze prywatne platforma Azure generuje alias usługi, który można udostępnić klientom. Klienci mogą użyć tego aliasu, aby zażądać połączenia z usługą.          |
|Widoczność (widoczność)     | Widoczność jest właściwością, która kontroluje ustawienia ekspozycji dla usługi Private Link. Dostawcy usług mogą ograniczyć ekspozycję na ich usługi do subskrypcji z uprawnieniami kontroli dostępu opartego na rolach (RBAC), ograniczonym zestawem subskrypcji lub wszystkimi subskrypcjami platformy Azure.          |
|Automatyczne zatwierdzanie (autozatwierdzalna)    |   Automatyczne zatwierdzanie steruje automatycznym dostępem do usługi Private Link. Subskrypcje określone na liście automatycznego zatwierdzania są zatwierdzane automatycznie, gdy połączenie jest wymagane z prywatnych punktów końcowych w tych subskrypcjach.          |
|Konfiguracja IP modułu równoważenia obciążenia (konfiguracja ip obciążeniaBalancerFrontendIpConfigurations)    |    Usługa Private Link jest powiązana z adresem IP frontendu standardowego modułu równoważenia obciążenia. Cały ruch przeznaczony do usługi dotrze do frontu SLB. Można skonfigurować reguły równoważenia obciążenia sieciowego, aby kierować ten ruch do odpowiednich pul zaplecza, w których są uruchomione aplikacje. Konfiguracje IP modułu równoważenia obciążenia różnią się od konfiguracji IP NAT.      |
|Konfiguracja protokołu IP translatora adresów sieciowych (konfiguracje ipConfiguracje)    |    Ta właściwość odnosi się do konfiguracji IP TRANSLATOR (Translacje adresów sieciowych) dla usługi Private Link. Adres IP NAT można wybrać z dowolnej podsieci w sieci wirtualnej dostawcy usług. Usługa Private Link wykonuje usługę NAT-ing po stronie docelowej w ruchu Link prywatny. Gwarantuje to, że nie ma konfliktu IP między źródłem (po stronie konsumenta) i miejsca docelowego (usługodawca) przestrzeni adresowej. Po stronie docelowej (po stronie dostawcy usług) adres IP NAT będzie pokazywał się jako źródłowy adres IP dla wszystkich pakietów odebranych przez usługę i docelowy adres IP dla wszystkich pakietów wysyłanych przez usługę.       |
|Prywatne połączenia z punktami końcowymi (privateEndpointConnections)     |  Ta właściwość zawiera listę prywatnych punktów końcowych łączących się z usługą Private Link. Wiele prywatnych punktów końcowych może łączyć się z tą samą usługą Private Link, a dostawca usług może kontrolować stan dla poszczególnych prywatnych punktów końcowych.        |
|Serwer proxy TCP V2 (EnableProxyProtocol)     |  Ta właściwość umożliwia dostawcy usług korzystanie z serwera proxy tcp w wersji 2 do pobierania informacji o połączeniu o konsumenta usługi. Usługodawca jest odpowiedzialny za konfigurowanie konfiguracji odbiornika, aby móc przeanalizować nagłówek protokołu proxy w wersji 2.        |
|||


### <a name="details"></a>Szczegóły

- Usługa Private Link jest dostępna z zatwierdzonych prywatnych punktów końcowych w tym samym regionie. Do prywatnego punktu końcowego można uzyskać dostęp z tej samej sieci wirtualnej, regionalnych sieci wirtualnych, globalnie równorzędnych sieci wirtualnych oraz lokalnie przy użyciu prywatnych połączeń sieci VPN lub Usługi ExpressRoute. 
 
- Podczas tworzenia usługi łącza prywatnego tworzony jest interfejs sieciowy dla cyklu życia zasobu. Ten interfejs nie jest zarządzany przez klienta.
 
- Usługa łącza prywatnego musi być wdrożona w tym samym regionie co sieć wirtualna i standardowy moduł równoważenia obciążenia.  
 
- Dostęp do jednej usługi łącza prywatnego można uzyskać z wielu prywatnych punktów końcowych należących do różnych sieci wirtualnych, subskrypcji i/lub dzierżaw usługi Active Directory. Połączenie jest nawiązywało za pośrednictwem przepływu pracy połączenia. 
 
- Wiele usług private link można tworzyć na tym samym standardowym równoważku obciążenia przy użyciu różnych konfiguracji adresów IP front-end. Istnieją ograniczenia dotyczące liczby usług łącza prywatnego, które można utworzyć na standardowy moduł równoważenia obciążenia i na subskrypcję. Aby uzyskać szczegółowe informacje, zobacz [limity platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).
 
- Usługa Private Link może mieć więcej niż jedną konfigurację IP TRANSLATORa. Wybranie więcej niż jednej konfiguracji ADRES IP NAT może pomóc dostawcom usług w skalowaniu. Obecnie dostawcy usług mogą przypisać maksymalnie osiem adresów IP NAT na usługę Private Link. Przy każdym adresie IP NAT można przypisać więcej portów dla połączeń TCP, a tym samym skalować w poziomie. Po dodaniu wielu adresów IP NAT do usługi Private Link nie można usunąć adresów IP NAT. Odbywa się to w celu zapewnienia, że aktywne połączenia nie mają wpływu podczas usuwania adresów IP NAT.


## <a name="alias"></a>Alias

**Alias** to globalnie unikatowa nazwa usługi. Pomaga maskować dane klienta dla usługi, a jednocześnie tworzy nazwę łatwe do udostępnienia dla usługi. Podczas tworzenia usługi łącze prywatne platforma Azure generuje alias dla usługi, który można udostępnić klientom. Klienci mogą użyć tego aliasu, aby zażądać połączenia z usługą.

Alias składa się z trzech części: *Prefiks*. *IDENTYFIKATOR GUID*. *Przyrostek*

- Prefiks to nazwa usługi. Możesz wybrać własny prefiks. Po utworzeniu "Aliasu" nie można go zmienić, więc odpowiednio wybierz prefiks.  
- GuiD będą dostarczane przez platformę. Dzięki temu nazwa jest unikatowa na całym świecie. 
- Sufiks jest dołączany przez platformę Azure: *region*.azure.privatelinkservice 

Kompletny alias: *Prefiks*. {GUID}. *region*.azure.privatelinkservice  

## <a name="control-service-exposure"></a>Kontrola narażenia na działanie usługi

Usługa Private Link zapewnia opcje kontrolowania ekspozycji usługi za pomocą ustawienia "Widoczność". Usługa może być prywatna do użytku z różnych posiadanych sieci wirtualnych (tylko uprawnienia RBAC), ograniczyć ekspozycję do ograniczonego zestawu zaufanych subskrypcji lub upublicznić ją, aby wszystkie subskrypcje platformy Azure mogły żądać połączeń w łączu prywatnym. Usługi. Ustawienia widoczności decydują, czy konsument może połączyć się z twoją usługą, czy nie. 

## <a name="control-service-access"></a>Kontrola dostępu do usługi

Konsumenci, którzy mają ekspozycję (kontrolowaną przez ustawienie widoczności) do usługi Private Link, mogą utworzyć prywatny punkt końcowy w swoich sieciach wirtualnych i zażądać połączenia z usługą Private Link. Prywatne połączenie punktu końcowego zostanie utworzone w stanie "Oczekujące" w obiekcie usługi Łącze prywatne. Usługodawca jest odpowiedzialny za działanie na żądanie połączenia. Można zatwierdzić połączenie, odrzucić połączenie lub usunąć połączenie. Tylko zatwierdzone połączenia mogą wysyłać ruch do usługi Private Link.

Akcja zatwierdzania połączeń można zautomatyzować przy użyciu właściwości automatycznego zatwierdzania w usłudze Private Link. Automatyczne zatwierdzanie to możliwość wstępnego zatwierdzania przez dostawców usług zestawu subskrypcji w celu automatycznego dostępu do ich usługi. Klienci będą musieli udostępnić swoje subskrypcje w trybie offline, aby dostawcy usług mogli dodać je do listy automatycznego zatwierdzania. Automatyczne zatwierdzanie jest podzbiorem tablicy widoczności. Widoczność steruje ustawieniami ekspozycji, podczas gdy automatyczne zatwierdzanie steruje ustawieniami zatwierdzania usługi. Jeśli klient zażąda połączenia z subskrypcji na liście automatycznego zatwierdzania, połączenie zostanie automatycznie zatwierdzone i nawiązane zostanie połączenie. Dostawcy usług nie muszą już ręcznie zatwierdzać żądania. Z drugiej strony, jeśli klient zażąda połączenia z subskrypcji w tablicy widoczności, a nie w tablicy automatycznego zatwierdzania, żądanie dotrze do dostawcy usług, ale dostawca usług musi ręcznie zatwierdzić połączenia.

## <a name="getting-connection-information-using-tcp-proxy-v2"></a>Uzyskiwanie informacji o połączeniu przy użyciu serwera proxy TCP w wersji 2

W przypadku korzystania z usługi łącza prywatnego źródłowy adres IP pakietów pochodzących z prywatnego punktu końcowego jest przetłumaczony jako adres sieciowy (NAT) po stronie dostawcy usług przy użyciu adresu IP NAT przydzielonego z sieci wirtualnej dostawcy. W związku z tym aplikacje otrzymują przydzielony adres IP NAT zamiast rzeczywistego źródłowego adresu IP konsumentów usługi. Jeśli aplikacja potrzebuje rzeczywistego źródłowego adresu IP od strony konsumenta, można włączyć protokół proxy w usłudze i pobrać informacje z nagłówka protokołu proxy. Oprócz źródłowego adresu IP nagłówek protokołu proxy zawiera również linkid prywatnego punktu końcowego. Połączenie źródłowego adresu IP i LinkID może pomóc dostawcom usług w unikatowym identyfikowaniu ich konsumentów. Aby uzyskać więcej informacji na temat protokołu proxy, odwiedź [tutaj](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt). 

Te informacje są kodowane przy użyciu niestandardowego wektora wartości typu długości (TLV) w następujący sposób:

Niestandardowe szczegóły TLV:

|Pole |Długość (oktety)  |Opis  |
|---------|---------|----------|
|Typ  |1        |PP2_TYPE_AZURE (0xEE)|
|Długość  |2      |Długość wartości|
|Wartość  |1     |PP2_SUBTYPE_AZURE_PRIVATEENDPOINT_LINKID (0x01)|
|  |4        |UINT32 (4 bajty) reprezentujący LINKID prywatnego punktu końcowego. Zakodowane w małym formacie endian.|

 > [!NOTE]
 > Dostawca usług jest odpowiedzialny za upewnienie się, że usługa stojąca za standardowym modułem równoważenia obciążenia jest skonfigurowana do analizowania nagłówka protokołu proxy zgodnie ze [specyfikacją,](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt) gdy protokół proxy jest włączony w usłudze łączy prywatnych. Żądanie zakończy się niepowodzeniem, jeśli ustawienie protokołu proxy jest włączone w usłudze łączy prywatnych, ale usługa dostawcy usług nie jest skonfigurowana do analizowania nagłówka. Podobnie żądanie zakończy się niepowodzeniem, jeśli usługa usługodawcy oczekuje nagłówka protokołu proxy, podczas gdy ustawienie nie jest włączone w usłudze łącza prywatnego. Po włączeniu ustawienia protokołu proxy nagłówek protokołu proxy będzie również dołączany do sond kondycji HTTP/TCP z hosta do maszyn wirtualnych wewnętrznej bazy danych, mimo że w nagłówku nie będzie żadnych informacji o kliencie. 

## <a name="limitations"></a>Ograniczenia

Poniżej przedstawiono znane ograniczenia podczas korzystania z usługi Private Link:
- Obsługiwane tylko w standardowym modułze równoważenia obciążenia 
- Obsługuje tylko ruch IPv4
- Obsługuje tylko ruch TCP

## <a name="next-steps"></a>Następne kroki
- [Tworzenie prywatnej usługi łączy przy użyciu programu Azure PowerShell](create-private-link-service-powershell.md)
- [Tworzenie prywatnej usługi łączy przy użyciu interfejsu wiersza polecenia platformy Azure](create-private-link-service-cli.md)
