---
title: Zaawansowane wieloetapowe wykrywanie ataków w usłudze Azure Sentinel
description: Technologia Fusion w usłudze Azure Sentinel zmniejsza zmęczenie alertów i tworzy zdarzenia, które można zasuwać w trybie działania, które są oparte na zaawansowanym wieloetapowym wykrywaniu ataków.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: yelevin
ms.openlocfilehash: 87ca322cbdfdd8a53a3ecefcb120a961ea1bb936
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587927"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Zaawansowane wieloetapowe wykrywanie ataków w usłudze Azure Sentinel


> [!IMPORTANT]
> Niektóre funkcje fuzji w usłudze Azure Sentinel są obecnie w publicznej wersji zapoznawczej.
> Te funkcje są dostarczane bez umowy dotyczącej poziomu usług i nie jest zalecane dla obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Korzystając z technologii Fusion, która jest oparta na uczeniu maszynowym, usługa Azure Sentinel może automatycznie wykrywać ataki wieloetapowe, łącząc nietypowe zachowania i podejrzane działania obserwowane na różnych etapach łańcucha zabijania. Usługa Azure Sentinel następnie generuje zdarzenia, które w przeciwnym razie byłyby bardzo trudne do wyłowienia. Te zdarzenia obejmują dwa lub więcej alertów lub działań. Zgodnie z projektem te zdarzenia są niskiej głośności, wysokiej wierności i wysokiej ważności.

Dostosowane do środowiska wykrywanie to nie tylko zmniejsza wskaźniki fałszywie dodatnie, ale może również wykrywać ataki z ograniczonymi lub brakującymi informacjami.

## <a name="configuration-for-advanced-multistage-attack-detection"></a>Konfiguracja zaawansowanego wieloetapowego wykrywania ataków

To wykrywanie jest domyślnie włączone w usłudze Azure Sentinel. Aby sprawdzić stan lub wyłączyć go być może dlatego, że używasz alternatywnego rozwiązania do tworzenia zdarzeń na podstawie wielu alertów, należy użyć następujących instrukcji:

1. Jeśli jeszcze tego nie zrobiono, zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Przejdź do **usługi Azure Sentinel** > **Configuration** > **Analytics**

3. Wybierz **aktywne reguły** i znajdź **zaawansowane wieloetapowe wykrywanie ataków** w kolumnie **NAZWA.** Sprawdź kolumnę **STAN,** aby sprawdzić, czy to wykrywanie jest włączone, czy wyłączone.

4. Aby zmienić stan, wybierz ten wpis i na bloku **Zaawansowane wieloetapowe wykrywanie ataków** wybierz pozycję **Edytuj**.

5. W bloku **Kreatora tworzenia reguł** zmiana stanu jest automatycznie wybierana, więc wybierz **pozycję Dalej: Przejrzyj**, a następnie **zapisz**. 

Szablony reguł nie mają zastosowania do zaawansowanego wykrywania ataków wieloetapowych.

> [!NOTE]
> Usługa Azure Sentinel używa obecnie 30 dni danych historycznych do szkolenia systemów uczenia maszynowego. Te dane są zawsze szyfrowane przy użyciu kluczy firmy Microsoft, gdy przechodzi przez potok uczenia maszynowego. Jednak dane szkoleniowe nie są szyfrowane przy użyciu [kluczy zarządzanych klienta (CMK),](customer-managed-keys.md) jeśli włączono cmk w obszarze roboczym usługi Azure Sentinel. Aby zrezygnować z fusion, przejdź do **reguły Azure Sentinel** \> **Configuration** \> **Analytics \> Active \> Zaawansowane wieloetapowe wykrywanie ataków** i w kolumnie **Stan** wybierz **pozycję Wyłącz.**

## <a name="fusion-using-palo-alto-networks-and-microsoft-defender-atp"></a>Fuzja za pomocą Palo Alto Networks i Microsoft Defender ATP

Te scenariusze łączą dwa podstawowe dzienniki używane przez analityków zabezpieczeń: dzienniki zapory z sieci Palo Alto Networks i dzienniki wykrywania punktów końcowych z usługi Microsoft Defender ATP. We wszystkich scenariuszach wymienionych poniżej podejrzana aktywność jest wykrywana w punkcie końcowym, który obejmuje zewnętrzny adres IP, a następnie następuje nietypowy ruch z zewnętrznego adresu IP z powrotem do zapory. W dziennikach Palo Alto usługa Azure Sentinel koncentruje się na [dziennikach zagrożeń,](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)a ruch jest uważany za podejrzany, gdy dozwolone są zagrożenia (podejrzane dane, pliki, powodzie, pakiety, skany, programy szpiegujące, adresy URL, wirusy, luki w zabezpieczeniach, wirusy pożarów, pożary).

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Żądanie sieciowe do usługi anonimizacji TOR, a następnie nietypowy ruch oznaczony przez zaporę Palo Alto Networks.

W tym scenariuszu usługa Azure Sentinel najpierw wykrywa alert, że usługa Microsoft Defender Advanced Threat Protection wykryła żądanie sieciowe do usługi anonimizacji TOR, która prowadzi do nietypowej aktywności. Zostało to zainicjowane przy podstawie konta {nazwa konta} o identyfikatorze SID {sid} w {time}. Wychodzący adres IP do połączenia to {IndividualIp}.
Następnie nietypowa aktywność została wykryta przez Zaporę sieci Palo Alto w {TimeGenerated}. Oznacza to, że złośliwy ruch wszedł do sieci Docelowy adres IP dla ruchu sieciowego to {DestinationIP}.

Ten scenariusz jest obecnie w publicznej wersji zapoznawczej.


### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Program PowerShell nawiązał podejrzane połączenie sieciowe, a następnie nietypowy ruch oznaczony przez zaporę Palo Alto Networks.

W tym scenariuszu usługa Azure Sentinel najpierw wykrywa alert, że usługa Microsoft Defender Advanced Threat Protection wykryła, że program PowerShell nawiązał podejrzane połączenie sieciowe, co doprowadziło do nietypowej aktywności wykrytej przez zaporę sieciową Palo Alto. Zostało to zainicjowane przez konto {nazwa konta} o identyfikatorze SID {sid} w {time}. Wychodzący adres IP do połączenia to {IndividualIp}. Następnie nietypowa aktywność została wykryta przez Zaporę sieci Palo Alto w {TimeGenerated}. Oznacza to, że złośliwy ruch trafił do sieci. Docelowy adres IP dla ruchu sieciowego to {DestinationIP}.

Ten scenariusz jest obecnie w publicznej wersji zapoznawczej.

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Połączenie wychodzące z adresem IP z historią prób nieautoryzowanego dostępu, po których następuje nietypowy ruch oznaczony przez zaporę Palo Alto Networks

W tym scenariuszu usługa Azure Sentinel wykrywa alert, że usługa Microsoft Defender Advanced Threat Protection wykryła połączenie wychodzące z adresem IP z historią prób nieautoryzowanego dostępu, które prowadzą do wykrycia nietypowej aktywności przez Palo Alto Zapora sieciowa. Zostało to zainicjowane przez konto {nazwa konta} o identyfikatorze SID {sid} w {time}. Wychodzący adres IP do połączenia to {IndividualIp}. Następnie nietypowa aktywność została wykryta przez Zaporę sieci Palo Alto w {TimeGenerated}. Oznacza to, że złośliwy ruch trafił do sieci. Docelowy adres IP dla ruchu sieciowego to {DestinationIP}.

Ten scenariusz jest obecnie w publicznej wersji zapoznawczej.



## <a name="fusion-using-identity-protection-and-microsoft-cloud-app-security"></a>Fuzja za pomocą ochrony tożsamości i zabezpieczeń aplikacji Microsoft Cloud App

Korzystając z zaawansowanego wykrywania ataków wieloetapowych, usługa Azure Sentinel obsługuje następujące scenariusze, które łączą zdarzenia anomalii z usługi Azure Active Directory Identity Protection i Microsoft Cloud App Security:

- [Niemożliwe podróżowanie do nietypowej lokalizacji, a następnie nietypowa aktywność usługi Office 365](#impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity)
- [Działanie logowania w nieznanej lokalizacji, po której następuje nietypowa aktywność usługi Office 365](#sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity)
- [Aktywność logowania z zainfekowanego urządzenia, po której następuje nietypowa aktywność usługi Office 365](#sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity)
- [Aktywność logowania z anonimowego adresu IP, po której następuje nietypowa aktywność usługi Office 365](#sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity)
- [Działanie logowania użytkownika z wyciekiem poświadczeń, po którym następuje nietypowe działanie usługi Office 365](#sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity)

Musisz mieć skonfigurowany [łącznik danych usługi Azure AD Identity Protection](connect-azure-ad-identity-protection.md) i łączniki usługi Cloud App [Security.](connect-cloud-app-security.md)

W kolejnych opisach usługa Azure Sentinel wyświetli rzeczywistą wartość z danych, która jest reprezentowana na tej stronie jako zmienne w nawiasach. Na przykład rzeczywista nazwa wyświetlana \<konta, a nie *nazwa konta*>, a rzeczywista liczba, \<a nie *liczba*>.

### <a name="impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity"></a>Niemożliwe podróżowanie do nietypowej lokalizacji, a następnie nietypowa aktywność usługi Office 365

Istnieje siedem możliwych zdarzeń usługi Azure Sentinel, które łączą niemożliwe podróże do nietypowych alertów lokalizacji z usługi Azure AD Identity Protection i nietypowych alertów usługi Office 365 generowanych przez usługę Microsoft Cloud App Security:

- **Niemożliwe podróż do nietypowych miejsc prowadzących do eksfiltracji skrzynek pocztowych usługi Office 365**
    
    Ten alert jest wskazaniem zdarzenia logowania \<według *nazwy konta,*> z \<niemożliwej podróży do *lokalizacji*>, nietypowej lokalizacji, po której na skrzynce odbiorczej użytkownika ustawiono podejrzaną regułę przekazywania skrzynki odbiorczej.
    
    Może to oznaczać, że konto zostało przejęte i że skrzynka pocztowa jest używana do eksfiltracji informacji z organizacji. Nazwa \< *konta* użytkownika> utworzona lub zaktualizowana reguła przekazywania w skrzynce \<odbiorczej, która przekazuje wszystkie przychodzące wiadomości e-mail na *adres e-mail* zewnętrznego>.

- **Niemożliwe podróżowanie do nietypowych miejsc prowadzących do podejrzanej aktywności administracyjnej aplikacji w chmurze**
    
    Ten alert jest wskazaniem zdarzenia logowania \<według *nazwy konta*> z \<niemożliwej podróży do *lokalizacji*>, nietypowej lokalizacji.
    
    Następnie nazwa \< *konta*> wykonywane przez \< *liczbę*> działania administracyjne w jednej sesji.

- **Niemożliwe podróż do nietypowych miejsc prowadzących do masowego usuwania plików**
    
    Ten alert jest wskazaniem zdarzenia logowania \<według *nazwy konta* \<> do *lokalizacji*>, nietypowej lokalizacji. 
    
    Następnie nazwa \< *konta*> \< *usunięto liczbę*> unikatowych plików w jednej sesji.

- **Niemożliwe podróż do nietypowych miejsc prowadzących do masowego pobierania plików**
    
    Ten alert jest wskazaniem zdarzenia logowania \<według *nazwy konta*> z \<niemożliwej podróży do *lokalizacji*>, nietypowej lokalizacji. 
    
    Następnie nazwa \< *konta*> pobrana z \< *liczby*> unikatowych plików w jednej sesji.

- **Niemożliwe podróżowanie do nietypowych lokalizacji prowadzących do podszywania się pod office 365**
    
    Ten alert jest wskazaniem zdarzenia logowania \<według *nazwy konta*> z \<niemożliwej podróży do *lokalizacji*>, nietypowej lokalizacji. 
    
    Następnie nazwa \< *konta*> wykonała nietypową\<kwotę *(liczbę działań*>) działań związanych z personifikacją w jednej sesji.

- **Niemożliwe podróż do nietypowych miejsc prowadzących do masowego udostępniania plików**
    
    Ten alert jest wskazaniem zdarzenia logowania \<według *nazwy konta*> z \<niemożliwej podróży do *lokalizacji*>, nietypowej lokalizacji. 
    
    Następnie nazwa \< *konta*> udostępniana przez \< *liczbę*> unikatowych plików w jednej sesji.

- **Niemożliwe podróż do nietypowych miejsc prowadzących do ransomware w aplikacji w chmurze**
    
    Ten alert jest wskazaniem zdarzenia logowania \<według *nazwy konta*> z \<niemożliwej podróży do *lokalizacji*>, nietypowej lokalizacji. 
    
    Następnie nazwa \< *konta*> przesłana \< *liczba*> plików i usunięto \<łączną *liczbę* plików>. 
    
    Ten wzorzec aktywności wskazuje na potencjalny atak ransomware.


### <a name="sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity"></a>Działanie logowania w nieznanej lokalizacji, po której następuje nietypowa aktywność usługi Office 365

Istnieje siedem możliwych zdarzeń usługi Azure Sentinel, które łączą aktywność logowania dla nieznanych alertów lokalizacji z usługi Azure AD Identity Protection i nietypowych alertów usługi Office 365 generowanych przez usługę Microsoft Cloud App Security.

- **Zdarzenie logowania z nieznanej lokalizacji prowadzącej do eksfiltracji skrzynki pocztowej usługi Exchange Online**
    
    Ten \<alert wskazuje zdarzenie logowania według nazwy *konta,*> \<z *lokalizacji*> nieznanej lokalizacji, po której w skrzynce odbiorczej użytkownika ustawiono podejrzaną regułę przekazywania skrzynki odbiorczej.
    
    Może to oznaczać, że konto zostało przejęte i że skrzynka pocztowa jest używana do eksfiltracji informacji z organizacji. Nazwa \< *konta* użytkownika> utworzona lub zaktualizowana reguła przekazywania w skrzynce \<odbiorczej, która przekazuje wszystkie przychodzące wiadomości e-mail na *adres e-mail* zewnętrznego>. 

- **Zdarzenie logowania z nieznanej lokalizacji prowadzącej do podejrzanej aktywności administracyjnej aplikacji w chmurze**
    
    Ten alert jest wskazaniem zdarzenia logowania \<według *nazwy konta* \<> od *lokalizacji*>, nieznanej lokalizacji. 
    
    Następnie nazwa \< *konta*> wykonywane w \< *ciągu kilku*> działań administracyjnych w jednej sesji.

- **Zdarzenie logowania z nieznanej lokalizacji prowadzące do masowego usunięcia pliku**
    
    Ten alert jest wskazaniem zdarzenia logowania \<według *nazwy konta* \<> od *lokalizacji*>, nieznanej lokalizacji. 
    
    Następnie nazwa \< *konta*> \< *usunięto liczbę*> unikatowych plików w jednej sesji.

- **Zdarzenie logowania z nieznanej lokalizacji prowadzącej do masowego pobierania plików**
    
    Ten alert jest wskazaniem zdarzenia logowania \<według *nazwy konta* \<> od *lokalizacji*>, nieznanej lokalizacji. 
    
    Następnie nazwa \< *konta*> pobrana z \< *liczby*> unikatowych plików w jednej sesji.

- **Zdarzenie logowania z nieznanej lokalizacji prowadzącej do podszywania się pod urząd usługi Office 365**
    
    Ten alert jest wskazaniem zdarzenia logowania \<według *nazwy konta* \<> od *lokalizacji*>, nieznanej lokalizacji.
    
    Następnie nazwa \< *konta*> podszywana nad \< *liczbą*> różnych kont w jednej sesji.

- **Zdarzenie logowania z nieznanej lokalizacji prowadzącej do masowego udostępniania plików**
    
    Ten alert jest wskazaniem zdarzenia logowania \<według *nazwy konta* \<> od *lokalizacji*>, nieznanej lokalizacji. 
    
    Następnie nazwa \< *konta*> udostępniana przez \< *liczbę*> unikatowych plików w jednej sesji.

- **Zdarzenie logowania z nieznanej lokalizacji prowadzącej do ransomware w aplikacji w chmurze**
    
    Ten alert jest wskazaniem zdarzenia logowania \<według *nazwy konta* \<> od *lokalizacji*>, nieznanej lokalizacji. 
    
    Następnie nazwa \< *konta*> przesłana \< *liczba*> plików i usunięto \<łączną *liczbę* plików>. 
    
    Ten wzorzec aktywności wskazuje na potencjalny atak ransomware.

### <a name="sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity"></a>Aktywność logowania z zainfekowanego urządzenia, po której następuje nietypowa aktywność usługi Office 365

Istnieje siedem możliwych zdarzeń usługi Azure Sentinel, które łączą aktywność logowania z zainfekowanych alertów urządzeń z usługi Azure AD Identity Protection i nietypowych alertów usługi Office 365 generowanych przez usługę Microsoft Cloud App Security:

- **Zdarzenie logowania z zainfekowanego urządzenia prowadzącego do eksfiltracji skrzynek pocztowych usługi Office 365**
    
    Ten alert wskazuje zdarzenie logowania według \<nazwy *konta,*> z urządzenia potencjalnie zainfekowanego złośliwym oprogramowaniem, po którym w skrzynce odbiorczej użytkownika ustawiono podejrzaną regułę przekazywania skrzynek odbiorczych.
    
    Może to oznaczać, że konto zostało przejęte i że skrzynka pocztowa jest używana do eksfiltracji informacji z organizacji. Nazwa \< *konta* użytkownika> utworzona lub zaktualizowana reguła przekazywania w skrzynce \<odbiorczej, która przekazuje wszystkie przychodzące wiadomości e-mail na *adres e-mail* zewnętrznego>. 

- **Zdarzenie logowania z zainfekowanego urządzenia prowadzące do podejrzanej aktywności administracyjnej aplikacji w chmurze**
    
    Ten alert wskazuje zdarzenie logowania według \<nazwy *konta*> z urządzenia potencjalnie zainfekowanego złośliwym oprogramowaniem.
    
    Następnie nazwa \< *konta*> wykonywane w \< *ciągu kilku*> działań administracyjnych w jednej sesji.

- **Zdarzenie logowania z zainfekowanego urządzenia prowadzące do masowego usunięcia pliku**
    
    Ten alert wskazuje zdarzenie logowania według \<nazwy *konta*> z urządzenia potencjalnie zainfekowanego złośliwym oprogramowaniem. 
    
    Następnie nazwa \< *konta*> \< *usunięto liczbę*> unikatowych plików w jednej sesji.

- **Zdarzenie logowania z zainfekowanego urządzenia prowadzące do masowego pobierania plików**
    
    Ten alert wskazuje zdarzenie logowania według \<nazwy *konta*> z urządzenia potencjalnie zainfekowanego złośliwym oprogramowaniem. 
    
    Następnie nazwa \< *konta*> pobrana z \< *liczby*> unikatowych plików w jednej sesji.

- **Zdarzenie logowania z zainfekowanego urządzenia prowadzącego do personifikacji usługi Office 365**
    
    Ten alert wskazuje zdarzenie logowania według \<nazwy *konta*> z urządzenia potencjalnie zainfekowanego złośliwym oprogramowaniem. 
    
    Następnie nazwa \< *konta*> podszywana nad \< *liczbą*> różnych kont w jednej sesji.

- **Zdarzenie logowania z zainfekowanego urządzenia prowadzące do masowego udostępniania plików**
    
    Ten alert wskazuje zdarzenie logowania według \<nazwy *konta*> z urządzenia potencjalnie zainfekowanego złośliwym oprogramowaniem. 
    
    Następnie nazwa \< *konta*> udostępniana przez \< *liczbę*> unikatowych plików w jednej sesji.

- **Zdarzenie logowania z zainfekowanego urządzenia prowadzącego do ransomware w aplikacji w chmurze**
    
    Ten alert wskazuje zdarzenie logowania według \<nazwy *konta*> z urządzenia potencjalnie zainfekowanego złośliwym oprogramowaniem. 
    
    Następnie nazwa \< *konta*> przesłana \< *liczba*> plików i usunięto \<łączną *liczbę* plików>. 
    
    Ten wzorzec aktywności wskazuje na potencjalny atak ransomware.

### <a name="sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity"></a>Aktywność logowania z anonimowego adresu IP, po której następuje nietypowa aktywność usługi Office 365

Istnieje siedem możliwych zdarzeń usługi Azure Sentinel, które łączą aktywność logowania z anonimowych alertów adresów IP z usługi Azure AD Identity Protection i nietypowych alertów usługi Office 365 generowanych przez usługę Microsoft Cloud App Security:

- **Zdarzenie logowania z anonimowego adresu IP prowadzącego do eksfiltracji skrzynek pocztowych usługi Office 365**
    
    Ten alert jest wskazaniem zdarzenia logowania \<według *nazwy konta,*> z \<adresu *IP* anonimowego serwera proxy>, po którym w skrzynce odbiorczej użytkownika ustawiono podejrzaną regułę przekazywania skrzynek odbiorczych.
    
    Może to oznaczać, że konto zostało przejęte i że skrzynka pocztowa jest używana do eksfiltracji informacji z organizacji. Nazwa \< *konta* użytkownika> utworzona lub zaktualizowana reguła przekazywania w skrzynce \<odbiorczej, która przekazuje wszystkie przychodzące wiadomości e-mail na *adres e-mail* zewnętrznego>. 

- **Zdarzenie logowania z anonimowego adresu IP prowadzącego do podejrzanej aktywności administracyjnej aplikacji w chmurze**
    
    Ten alert jest wskazaniem zdarzenia logowania \<według *nazwy konta*> z adresu \< *IP* anonimowego serwera proxy>. 
    
    Następnie nazwa \< *konta*> wykonywane w \< *ciągu kilku*> działań administracyjnych w jednej sesji.

- **Zdarzenie logowania z anonimowego adresu IP prowadzącego do masowego usunięcia pliku**
    
    Ten alert jest wskazaniem zdarzenia logowania \<według *nazwy konta*> z adresu \< *IP* anonimowego serwera proxy>. 
    
    Następnie nazwa \< *konta*> \< *usunięto liczbę*> unikatowych plików w jednej sesji.

- **Zdarzenie logowania z anonimowego adresu IP prowadzącego do masowego pobierania plików**
    
    Ten alert jest wskazaniem zdarzenia logowania \<według *nazwy konta*> z adresu \< *IP* anonimowego serwera proxy>. 
    
    Następnie nazwa \< *konta*> pobrana z \< *liczby*> unikatowych plików w jednej sesji.

- **Zdarzenie logowania z anonimowego adresu IP prowadzącego do personifikacji usługi Office 365**
    
    Ten alert jest wskazaniem zdarzenia logowania \<według *nazwy konta*> z adresu \< *IP* anonimowego serwera proxy>. 
    
    Następnie nazwa \< *konta*> podszywana nad \< *liczbą*> różnych kont w jednej sesji.

- **Zdarzenie logowania z anonimowego adresu IP prowadzącego do masowego udostępniania plików**
    
    Ten alert jest wskazaniem zdarzenia logowania \<według *nazwy konta*> z adresu \< *IP* anonimowego serwera proxy>. 
    
    Następnie nazwa \< *konta*> udostępniana przez \< *liczbę*> unikatowych plików w jednej sesji.

- **Zdarzenie logowania z anonimowego adresu IP do oprogramowania ransomware w aplikacji w chmurze**
    
    Ten alert jest wskazaniem zdarzenia logowania \<według *nazwy konta*> z adresu \< *IP* anonimowego serwera proxy>. 
    
    Następnie nazwa \< *konta*> przesłana \< *liczba*> plików i usunięto \<łączną *liczbę* plików>. 
    
    Ten wzorzec aktywności wskazuje na potencjalny atak ransomware.

### <a name="sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity"></a>Działanie logowania użytkownika z wyciekiem poświadczeń, po którym następuje nietypowe działanie usługi Office 365

Istnieje siedem możliwych zdarzeń usługi Azure Sentinel, które łączą działanie logowania od użytkownika z alertami o wyciekach poświadczeń z usługi Azure AD Identity Protection i nietypowymi alertami usługi Office 365 generowanymi przez usługę Microsoft Cloud App Security:

- **Zdarzenie logowania od użytkownika z wyciekiem poświadczeń prowadzących do eksfiltracji skrzynek pocztowych usługi Office 365**
    
    Ten alert wskazuje, że zdarzenie logowania \<według *nazwy konta*> używane wyciekły poświadczenia, a następnie podejrzana reguła przekazywania skrzynki odbiorczej została ustawiona w skrzynce odbiorczej użytkownika. 
    
    Może to oznaczać, że konto zostało przejęte i że skrzynka pocztowa jest używana do eksfiltracji informacji z organizacji. Nazwa \< *konta* użytkownika> utworzona lub zaktualizowana reguła przekazywania w skrzynce \<odbiorczej, która przekazuje wszystkie przychodzące wiadomości e-mail na *adres e-mail* zewnętrznego>. 

- **Zdarzenie logowania od użytkownika z wyciekiem poświadczeń prowadzących do podejrzanej aktywności administracyjnej aplikacji w chmurze**
    
    Ten alert jest wskazanie, że \<zdarzenie logowania według *nazwy konta*> używane wyciekły poświadczenia.
    
    Następnie nazwa \< *konta*> wykonywane w \< *ciągu kilku*> działań administracyjnych w jednej sesji.

- **Zdarzenie logowania od użytkownika z wyciekiem poświadczeń prowadzących do masowego usunięcia pliku**
    
    Ten alert jest wskazanie, że \<zdarzenie logowania według *nazwy konta*> używane wyciekły poświadczenia.
    
    Następnie nazwa \< *konta*> \< *usunięto liczbę*> unikatowych plików w jednej sesji.

- **Zdarzenie logowania od użytkownika z wyciekiem poświadczeń prowadzących do masowego pobierania plików**
    
    Ten alert jest wskazanie, że \<zdarzenie logowania według *nazwy konta*> używane wyciekły poświadczenia.
    
    Następnie nazwa \< *konta*> pobrana z \< *liczby*> unikatowych plików w jednej sesji.

- **Zdarzenie logowania od użytkownika z wyciekiem poświadczeń prowadzących do personifikacji usługi Office 365**
    
    Ten alert jest wskazanie, że \<zdarzenie logowania według *nazwy konta*> używane wyciekły poświadczenia. 
    
    Następnie nazwa \< *konta*> podszywana nad \< *liczbą*> różnych kont w jednej sesji.

- **Zdarzenie logowania od użytkownika z wyciekiem poświadczeń prowadzących do masowego udostępniania plików**
    
    Ten alert jest wskazanie, że \<zdarzenie logowania według *nazwy konta*> używane wyciekły poświadczenia.
    
    Następnie nazwa \< *konta*> udostępniana przez \< *liczbę*> unikatowych plików w jednej sesji.

- **Zdarzenie logowania od użytkownika z wyciekiem poświadczeń do oprogramowania wymuszającego okup w aplikacji w chmurze**
    
    Ten alert jest wskazanie, że \<zdarzenie logowania według *nazwy konta*> używane wyciekły poświadczenia. 
    
    Następnie nazwa \< *konta*> przesłana \< *liczba*> plików i usunięto \<łączną *liczbę* plików>. 
    
    Ten wzorzec aktywności wskazuje na potencjalny atak ransomware.

## <a name="next-steps"></a>Następne kroki

Teraz dowiesz się więcej o zaawansowanym wieloetapowym wykrywaniu ataków, możesz zainteresować się następującym przewodnikiem Szybki start, aby dowiedzieć się, jak uzyskać wgląd w dane i potencjalne zagrożenia: [Wprowadzenie do usługi Azure Sentinel.](quickstart-get-visibility.md)

Jeśli chcesz zbadać zdarzenia, które są tworzone dla Ciebie, zobacz następujący samouczek: [Badanie zdarzeń za pomocą usługi Azure Sentinel](tutorial-investigate-cases.md).

