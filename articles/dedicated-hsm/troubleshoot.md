---
title: Co to jest dedykowany moduł HSM? — Dedykowany moduł HSM platformy Azure | Microsoft Docs
description: Omówienie dedykowanego modułu HSM platformy Azure udostępniającego funkcje magazynu kluczy na platformie Azure, który spełnia wymogi certyfikatu FIPS 140-2 poziom 3
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc, seodec18
ms.date: 12/07/2018
ms.author: mbaldwin
ms.openlocfilehash: ad3c9d702384b8a32a9d4f0c8aebe44de4bb526e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80337176"
---
# <a name="troubleshooting"></a>Rozwiązywanie problemów

Usługa dedykowanego modułu HSM platformy Azure ma dwa różne aspekty. Po pierwsze rejestracji i wdrażania na platformie Azure urządzeń HSM z ich podstawowych składników sieciowych. Po drugie, konfiguracja urządzeń HSM w ramach przygotowań do użycia/integracji z danym obciążeniem lub aplikacją. Mimo że urządzenia HSM sieci Thales Luna są takie same na platformie Azure, jak można kupić bezpośrednio od firmy Thales, fakt, że są one zasobem na platformie Azure tworzy pewne unikatowe zagadnienia. Te zagadnienia i wszelkie wynikające z tego szczegółowe informacje dotyczące rozwiązywania problemów lub najlepsze rozwiązania są udokumentowane w tym miejscu, aby zapewnić wysoką widoczność i dostęp do krytycznych informacji. Gdy usługa jest w użyciu, ostateczne informacje są dostępne za pośrednictwem żądań pomocy technicznej do firmy Microsoft lub Thales bezpośrednio. 

> [!NOTE]
> Należy zauważyć, że przed wykonaniem jakiejkolwiek konfiguracji na nowo wdrożonym urządzeniu HSM należy ją zaktualizować o odpowiednie poprawki. Określona wymagana poprawka to [KB0019789](https://supportportal.gemalto.com/csm?id=kb_article_view&sys_kb_id=19a81c8bdb9a1fc8d298728dae96197d&sysparm_article=KB0019789) w portalu pomocy technicznej firmy Thales, która rozwiązuje problem z ponownym uruchomieniem.

## <a name="hsm-registration"></a>Rejestracja HSM

Dedykowany moduł HSM nie jest swobodnie dostępny do użytku, ponieważ dostarcza zasoby sprzętowe w chmurze, a zatem jest cennym zasobem, który wymaga ochrony. Dlatego korzystamy z procesu umieszczania HSMrequest@microsoft.comna białej liście za pośrednictwem poczty elektronicznej za pomocą . 

### <a name="getting-access-to-dedicated-hsm"></a>Uzyskiwanie dostępu do dedykowanego modułu HSM

Jeśli uważasz, że dedykowany moduł HSM HSMrequest@microsoft.com będzie pasować do wymagań magazynu kluczy, a następnie wysłać wiadomość e-mail, aby zażądać dostępu. Określ aplikację, regiony, w których chcesz modułów HSM i ilość hsmów, których szukasz. Jeśli pracujesz z przedstawicielem firmy Microsoft, na przykład account executive lub Cloud Solution Architect, dołącz je do dowolnego żądania.

## <a name="hsm-provisioning"></a>Inicjowanie obsługi administracyjnej modułu HSM

Inicjowanie obsługi administracyjnej urządzenia HSM na platformie Azure można wykonać za pośrednictwem interfejsu wiersza polecenia lub programu PowerShell. Podczas rejestracji do usługi, przykładowy szablon ARM zostaną dostarczone i pomoc zostanie udzielona do wstępnego dostosowania. 

### <a name="hsm-deployment-failure-information"></a>Informacje o niepowodzeniu wdrażania modułu HSM

Dedykowany moduł HSM obsługuje polecenia cli i powershell do wdrożenia, więc informacje o błędach oparte na portalu są ograniczone i nie są pełne. Lepsze informacje można znaleźć za pomocą Eksploratora zasobów. Strona główna portalu ma ikonę dla tego i bardziej szczegółowe informacje o błędzie jest dostępna. Te informacje bardzo pomaga, jeśli wklejone podczas tworzenia żądania pomocy technicznej związane z problemami wdrażania.

![Informacje o niepowodzeniu](./media/troubleshoot/failure-information.png)

### <a name="hsm-subnet-delegation"></a>Delegowanie podsieci HSM
Numer jeden przyczyną niepowodzeń wdrażania jest zapominanie ustawić odpowiednie delegowanie dla podsieci zdefiniowanej przez klienta, w którym będą aprowizacji modułów HSM. Ustawienie, że delegowanie jest częścią sieci wirtualnej i podsieci wymagania wstępne dotyczące wdrażania i więcej szczegółów można znaleźć w samouczkach.

![Delegowanie podsieci](./media/troubleshoot/subnet-delegation.png)

### <a name="hsm-deployment-race-condition"></a>Stan wyścigu wdrażania HSM

Standardowy szablon ARM przewidziany do wdrożenia zawiera zasoby związane z usługami HSM i usługi ExpressRoute gateway. Zasoby sieciowe są zależnością dla pomyślnego wdrożenia modułu HSM i czas może mieć kluczowe znaczenie.  Od czasu do czasu widzieliśmy błędy wdrażania związane z problemami z zależnościami i ponowne uruchomienie wdrożenia często rozwiązuje problem. Jeśli nie, usunięcie zasobów, a następnie ponowne wdrożenie często zakończy się powodzeniem. Po próbie tego problemu i nadal znalezienie problemu, należy podnieść żądanie pomocy technicznej w witrynie Azure portal wybierając typ problemu "Problemy z konfigurowaniem konfiguracji platformy Azure".

### <a name="hsm-deployment-using-terraform"></a>Wdrażanie HSM przy użyciu terraform

Kilku klientów korzystało z programu Terraform jako środowiska automatyzacji zamiast szablonów ARM dostarczonych podczas rejestracji tej usługi. Modułów HSM nie można wdrożyć w ten sposób, ale zasoby sieciowe zależne mogą. Terraform ma moduł do wywołania minimalnego szablonu ARM, który jut ma wdrożenie HSM.  W tej sytuacji należy zadbać o to, aby zapewnić pełne wdrożenie zasobów sieciowych, takich jak wymagana brama usługi ExpressRoute Gateway, przed wdrożeniem modułów HSM. Następujące polecenie interfejsu wiersza polecenia może służyć do testowania ukończonego wdrożenia i zintegrowane zgodnie z wymaganiami. Wymień uchwyty na uchwyty kątowe dla konkretnego nazewnictwa. Należy szukać wyniku "Inicjowanie obsługi administracyjnejState jest powiódł się"

```azurecli
az resource show --ids /subscriptions/<subid>/resourceGroups/<myresourcegroup>/providers/Microsoft.Network/virtualNetworkGateways/<myergateway>
```

### <a name="deployment-failure-based-on-quota"></a>Niepowodzenie wdrożenia na podstawie przydziału
Wdrożenia mogą zakończyć się niepowodzeniem, jeśli przekroczysz 2 moduły HSM na znaczek i 4 moduły HSM na region. Aby uniknąć tej sytuacji, upewnij się, że zostały usunięte zasoby z wcześniej nieudanych wdrożeń przed ponownym wdrożeniem. Zapoznaj się z poniższym elementem "Jak zobaczyć moduły HSM", aby sprawdzić zasoby. Jeśli uważasz, że musisz przekroczyć ten limit, który jest HSMrequest@microsoft.com przede wszystkim tam jako zabezpieczenie, wyślij e-mail ze szczegółami.

### <a name="deployment-failure-based-on-capacity"></a>Błąd wdrożenia na podstawie zdolności produkcyjnych
Gdy określony znaczek lub region staje się pełna, oznacza to, że prawie wszystkie bezpłatne moduły HSM są aprowizacji, może to prowadzić do błędów wdrażania. Każdy znaczek ma 11 modułów HSM dostępnych dla klientów, co oznacza 22 na region. W każdym znaczku znajdują się również 3 części zamienne i 1 urządzenie testowe. Jeśli uważasz, że możesz osiągnąć HSMrequest@microsoft.com limit, wyślij wiadomość e-mail z informacją o poziomie wypełnienia określonych znaczków.

###  <a name="how-do-i-see-hsms-when-provisioned"></a>Jak wyświetlić moduły HSM po zainicjowaniu obsługi administracyjnej?
Ze względu na dedykowane HSM jest usługi białej listy, jest uważany za "typ ukryty" w witrynie Azure portal. Aby wyświetlić zasoby modułu HSM, należy zaznaczyć pole wyboru "Pokaż ukryte typy", jak pokazano poniżej. Zasób karty sieciowej zawsze podąża za modułem HSM i jest dobrym miejscem, aby dowiedzieć się adres IP modułu HSM przed użyciem protokołu SSH do nawiązania połączenia.

![Delegowanie podsieci](./media/troubleshoot/hsm-provisioned.png)

## <a name="networking-resources"></a>Zasoby sieciowe

Wdrażanie dedykowanego modułu HSM ma zależność od zasobów sieciowych i niektóre ograniczenia następcze, o czym należy pamiętać.

### <a name="provisioning-expressroute"></a>Inicjowanie obsługi administracyjnej usługi ExpressRoute

Dedykowany moduł HSM używa bramy usługi ExpressRoute jako "tunelu" do komunikacji między prywatną przestrzenią adresów IP klienta a fizycznym modułem HSM w centrum danych platformy Azure.  Biorąc pod uwagę, że istnieje ograniczenie jednej bramy na sieci wirtualnej, klienci wymagający połączenia z ich zasobów lokalnych za pośrednictwem usługi ExpressRoute, będzie musiał użyć innej sieci wirtualnej dla tego połączenia.  

### <a name="hsm-private-ip-address"></a>Prywatny adres IP HSM

Przykładowe szablony dostarczone dla dedykowanego modułu HSM zakładają, że adres IP modułu HSM zostanie automatycznie pobrany z danego zakresu podsieci. Można określić jawny adres IP dla modułu HSM za pomocą atrybutu "NetworkInterfaces" w szablonie ARM. 

![Delegowanie podsieci](./media/troubleshoot/private-ip-address.png)

## <a name="hsm-initialization"></a>Inicjowanie modułu HSM

Inicjalizacja przygotowuje nowy moduł HSM do użycia lub istniejący moduł HSM do ponownego użycia. Inicjowanie modułu HSM musi zostać zakończone, zanim będzie można wygenerować lub przechowywać obiekty, zezwolić klientom na łączenie się lub wykonywanie operacji kryptograficznych.

### <a name="lost-credentials"></a>Utracone poświadczenia

Utrata hasła administratora powłoki spowoduje utratę materiału klucza HSM. Należy złożyć żądanie pomocy technicznej w celu zresetowania modułu HSM.
Podczas inicjowania modułu HSM bezpiecznie przechowuj poświadczenia. Poświadczenia powłoki i modułu HSM powinny być przechowywane zgodnie z zasadami firmy.

### <a name="failed-logins"></a>Nieudane logowania

Podanie niepoprawnych poświadczeń do modułów HSM może mieć destrukcyjne konsekwencje. Poniżej przedstawiono domyślne zachowania ról modułu HSM.

| Rola | Próg (liczba prób) | W wyniku zbyt wielu złych prób logowania | Odzyskiwanie |
|--|--|--|--|
| HSM TAK | 3 |  Hsm jest zerowany (wszystkie tożsamości obiektów HSM, a wszystkie partycje zniknęły)  |  Moduł HSM musi zostać ponownie zainicjowany. Zawartość można przywrócić z kopii zapasowych. | 
| Partycja TAK | 10 |  Partycja jest zerowana. |  Partycja musi zostać ponownie zainicjowana. Zawartość można przywrócić z kopii zapasowej. |  
| Inspekcja | 10 | Blokady | Odblokowany automatycznie po 10 minutach. |  
| Oficer krypto | 10 (można zmniejszyć) | Jeśli zasady MODUŁU HSM 15: Włącz resetowanie tak numeru PIN partycji jest ustawiona na 1 (włączona), role CO i CU są zablokowane.<br>Jeśli zasady MODUŁU HSM 15: Włącz resetowanie tak numeru PIN partycji jest ustawiona na 0 (wyłączone), role CO i CU są trwale zablokowane, a zawartość partycji nie są już dostępne. Jest to ustawienie domyślne. | Rola CO musi zostać odblokowana, a poświadczenie zresetowane przez partition SO, za pomocą . `role resetpw -name co`<br>Partycja musi zostać ponownie zainicjowana, a materiał klucza przywrócony z urządzenia kopii zapasowej. |  

## <a name="hsm-configuration"></a>Konfiguracja modułu HSM 

Następujące elementy są sytuacje, w których błędy konfiguracji są wspólne lub mają wpływ, który jest godny wywołania:

### <a name="hsm-documentation-and-software"></a>Dokumentacja i oprogramowanie HSM
Oprogramowanie i dokumentacja urządzeń HSM Thales SafeNet Luna 7 nie jest dostępna od firmy Microsoft i musi zostać pobrana bezpośrednio z firmy Thales. Rejestracja jest wymagana przy użyciu identyfikatora klienta Thales otrzymanego podczas procesu rejestracji. Urządzenia, dostarczone przez firmę Microsoft, mają wersję oprogramowania 7.2 i firmware w wersji 7.0.3. Na początku 2020 r. Thales upublicznił dokumentację i można ją znaleźć [tutaj](https://thalesdocs.com/gphsm/luna/7.2/docs/network/Content/Home_network.htm).  

### <a name="hsm-networking-configuration"></a>Konfiguracja sieci HSM

Należy zachować ostrożność podczas konfigurowania sieci w ramach modułu HSM.  Moduł HSM ma połączenie za pośrednictwem bramy usługi ExpressRoute z prywatnej przestrzeni adresów IP klienta bezpośrednio do modułu HSM.  Ten kanał komunikacji służy wyłącznie do komunikacji z klientami, a firma Microsoft nie ma dostępu. Jeśli moduł HSM jest skonfigurowany w taki sposób, że ta ścieżka sieciowa ma wpływ, oznacza to, że cała komunikacja z modułem HSM jest usuwany.  W tej sytuacji jedyną opcją jest zgłoszenie żądania pomocy technicznej firmy Microsoft za pośrednictwem witryny Azure portal, aby zresetować urządzenie. Ta procedura resetowania ustawia hsm z powrotem do stanu początkowego i wszystkie konfiguracji i materiału klucza jest tracona.  Konfiguracja musi zostać odtworzona, a gdy urządzenie dołączy do grupy wysokiej sieci, zostanie zreplikowane z materiału klucza.  

### <a name="hsm-device-reboot"></a>Ponowne uruchomienie urządzenia HSM

Niektóre zmiany konfiguracji wymagają, aby moduł HSM był cyklicznie zasilany lub ponownie uruchamiany. Testy firmy Microsoft modułu HSM na platformie Azure ustaliły, że w niektórych przypadkach ponowne uruchomienie może się zawiesić. Implikacją jest to, że żądanie pomocy technicznej musi zostać utworzone w witrynie Azure portal z żądaniem twardego ponownego uruchomienia komputera, a ukończenie może potrwać do 48 godzin, biorąc pod uwagę, że jest to proces ręczny w centrum danych platformy Azure.  Aby uniknąć tej sytuacji, upewnij się, że wdrożono poprawkę ponownego uruchomienia dostępną bezpośrednio z thales. Zapoznaj się z [numerem KB0019789](https://supportportal.gemalto.com/csm?sys_kb_id=d66911e2db4ffbc0d298728dae9619b0&id=kb_article_view&sysparm_rank=1&sysparm_tsqueryId=d568c35bdb9a4850d6b31f3b4b96199e&sysparm_article=KB0019789) w pliku Thales Luna Network HSM 7.2 Downloads, aby uzyskać zalecaną poprawkę, aby uzyskać problem z zawieszeniem na czas ponownego uruchomienia (Uwaga: aby pobrać, musisz zarejestrować się w portalu pomocy technicznej firmy Thales).

### <a name="ntls-certificates-out-of-sync"></a>Brak synchronizacji certyfikatów NTLS
Klient może utracić łączność z modułem HSM po wygaśnięciu lub zastąpieniu certyfikatu za pomocą aktualizacji konfiguracji. Konfiguracja klienta wymiany certyfikatów powinna być ponownie stosowana z każdym modułem HSM.
Przykład rejestrowania NTLS z nieprawidłowym certyfikatem:

> NTLS[8508]: info : 0 : Żądanie połączenia przychodzącego... : 192.168.50.2/59415 NTLS[8508]: Komunikat o błędzie z S SSLAccept jest: error:14094418:SSL routines:ssl3_read_bytes:tlsv1 alert unknown ca NTLS[8508]: Błąd podczas akceptowania protokołu SSL ( RC_SSL_ERROR ) NTLS[8508]: info : 0xc0000711 : Nie można ustanowić bezpiecznego kanału z klientem: 192.168.50.2/59415 : RC_SSL_FAILED_HANDSHAKE NTLS[8508]: info : 0 : NTLS Klient "Nieznana nazwa hosta" Usunięto wystąpienie połączenia: 192.168.50.2/59415

### <a name="failed-tcp-communication"></a>Nieudana komunikacja TCP

Komunikacja z instalacji luna klienta do modułu HSM wymaga co najmniej portu TCP 1792. Należy wziąć to pod uwagę, jak wszystkie konfiguracje sieci są zmieniane w środowisku.

### <a name="failed-ha-group-member-doesnt-recover"></a>Nie powiodło się członek grupy HA nie odzyskiwanie

Jeśli nie powiodło się członek grupy HA nie odzyska, musi być ręcznie odzyskane z klienta Luna przy użyciu polecenia hagroup odzyskać.
Konieczne jest skonfigurowanie liczby ponownych prób dla grupy wysokiej haw, aby włączyć automatyczne odzyskiwanie. Domyślnie grupa wysokiej haw nie będzie próbować odzyskać członka wysokiej haw do grupy po odzyskaniu.

### <a name="ha-group-doesnt-sync"></a>Grupa HA nie synchronizuje się

W przypadku, gdy partycje członkowskie nie mają tej samej domeny klonowania, polecenie ha synchronize wyświetli następujące informacje: Ostrzeżenie: Synchronizacja może zakończyć się niepowodzeniem.  Elementy członkowskie w gniazdach 0 i 1 mają sprzeczne ustawienia klonowania kluczy prywatnych.
Nowa partycja z właściwą domeną klonowania powinna zostać dodana do grupy wysokiej klasy, a następnie usunięcie niepoprawnie skonfigurowaną partycję.

## <a name="hsm-deprovisioning"></a>Anulowanie obsługi administracyjnej HSM 

Tylko po pełnym zakończeniu z hsm może być deprovisioned, a następnie Microsoft będzie go zresetować i zwrócić go do bezpłatnej puli. 

### <a name="how-to-delete-an-hsm-resource"></a>Jak usunąć zasób HSM

Nie można usunąć zasobu platformy Azure dla modułu HSM, chyba że moduł HSM jest w stanie "zerowanym".  W związku z tym wszystkie materiały klucza muszą zostać usunięte przed próbą usunięcia go jako zasobu. Najszybszym sposobem zerowania jest 3-krotne podanie hasła administratora modułu HSM (uwaga: odnosi się to do administratora hsm, a nie do administratora na poziomie urządzenia). Powłoka Luna ma `hsm -factoryreset` polecenie, które zeroizes, ale może być wykonywany tylko za pośrednictwem konsoli na porcie szeregowym i klienci nie mają do tego dostępu.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono obszary w całym cyklu życia wdrażania modułu HSM, które mogą mieć problemy lub wymagają rozwiązywania problemów lub starannego rozważenia. Mam nadzieję, że ten artykuł pomoże Ci uniknąć niepotrzebnych opóźnień i frustracji, a jeśli masz odpowiednie dodatki lub zmiany, a następnie zgłosić prośbę o pomoc techniczną z firmą Microsoft i daj nam znać. 
