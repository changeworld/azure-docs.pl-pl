## <a name="load-balancer-differences"></a>Różnice między modułami równoważenia obciążenia

Istnieją różne opcje umożliwiające dystrybucję ruchu sieciowego za pomocą platformy Microsoft Azure. Działanie tych opcji różni się między sobą, a same opcje wykorzystują różny zbiór funkcji i obsługują różne scenariusze. Można korzystać z nich osobno lub używać ich łącznie.

* Usługa **Azure Load Balancer** działa w warstwie transportowej (warstwa 4. w modelu odniesienia OSI). Usługa zapewnia dystrybucję ruchu na poziomie sieci między wystąpieniami aplikacji uruchomionej w tym samym centrum danych Azure.
* Usługa **Application Gateway** działa w warstwie aplikacji (warstwa 7. w modelu odniesienia OSI). Działa jako usługa zwrotnego serwera proxy, kończy połączenie klienta i przekazuje żądania do punktów końcowych zaplecza.
* Usługa **Traffic Manager** działa na poziomie usługi DNS.  Przy użyciu odpowiedzi usługi DNS kieruje ona ruch użytkowników końcowych do punktów końcowych rozproszonych po całym świecie. Klienci następnie łączą się z tymi punktami końcowymi bezpośrednio.

W poniższej tabeli podsumowano funkcje poszczególnych usług:

| Usługa | Azure Load Balancer | Application Gateway | Traffic Manager |
| --- | --- | --- | --- |
| Technologia |Poziom transportu (warstwa 4) |Poziom aplikacji (warstwa 7) |Poziom DNS |
| Obsługiwane protokoły aplikacji |Dowolne |Protokoły HTTP, HTTPS i WebSockets |Dowolne (do monitorowania punktu końcowego jest wymagany punkt końcowy HTTP) |
| Punkty końcowe |Wystąpienia roli maszyn wirtualnych i usług w chmurze Azure |Dowolny wewnętrzny adres IP platformy Azure, publiczny adres IP, maszyna wirtualna platformy Azure lub usługa w chmurze platformy Azure |Azure Virtual Machines, Cloud Services, Azure Web Apps i zewnętrzne punkty końcowe |
| Obsługa sieci wirtualnej |Możliwe użycie zarówno z aplikacjami połączonymi z Internetem, jak i aplikacjami wewnętrznymi (w sieci wirtualnej) |Możliwe użycie zarówno z aplikacjami połączonymi z Internetem, jak i aplikacjami wewnętrznymi (w sieci wirtualnej) |Obsługuje tylko aplikacje łączące się z Internetem |
| Monitorowanie punktu końcowego |Obsługiwane z użyciem sond |Obsługiwane z użyciem sond |Obsługiwane z użyciem żądania GET protokołu HTTP/HTTPS |

Usługi Azure Load Balancer i Application Gateway zapewniają routing ruchu sieciowego do punktów końcowych, ale dotyczą ich inne scenariusze użycia w zakresie obsługiwanego ruchu. Poniższa tabela zawiera rozróżnienie między dwoma modułami równoważenia obciążenia:

| Typ | Azure Load Balancer | Application Gateway |
| --- | --- | --- |
| Protokoły |UDP/TCP |Protokoły HTTP, HTTPS i WebSockets |
| Rezerwacja adresu IP |Obsługiwane |Nieobsługiwane |
| Tryb równoważenia obciążenia |5-krotka (źródłowy adres IP, port źródłowy, docelowy adres IP, port docelowy, typ protokołu) |Działanie okrężne<br>Routing na podstawie adresu URL |
| Tryb równoważenia obciążenia (źródłowy adres IP / sesje umocowane) |2-krotka (źródłowy adres IP i docelowy adres IP), 3-krotka (źródłowy adres IP, docelowy adres IP i port). Możliwe jest skalowanie odpowiednio w górę lub w dół w oparciu o liczbę maszyn wirtualnych |Koligacja na podstawie pliku cookie<br>Routing na podstawie adresu URL |
| Sondy kondycji |Wartość domyślna: interwał sondowania — 15 sekund. Wykluczenie z rotacji: dwa powtarzające się niepowodzenia. Obsługa sond zdefiniowanych przez użytkownika |Interwał bezczynności sondy — 30 sekund. Usunięta po pięciu kolejnych niepowodzeniach obsługi ruchu lub po pojedynczym niepowodzeniu sondy w trybie bezczynności. Obsługa sond zdefiniowanych przez użytkownika |
| Odciążanie protokołu SSL |Nieobsługiwane |Obsługiwane |
| Routing oparty na adresach URL | Nieobsługiwane | Obsługiwane|
| Zasady SSL | Nieobsługiwane | Obsługiwane|
