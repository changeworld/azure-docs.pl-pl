---
title: Rozwiązywanie problemów z błędami dostawcy zasobów plików NetApp platformy Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano przyczyny, rozwiązania i obejścia typowych błędów dostawcy zasobów plików NetApp platformy Azure.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: b-juche
ms.openlocfilehash: 62e67d4965444df0e731b4387808ed3b89e4673a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72597204"
---
# <a name="troubleshoot-azure-netapp-files-resource-provider-errors"></a>Rozwiązywanie problemów związanych z błędami dostawcy zasobów usługi Azure NetApp Files 

W tym artykule opisano typowe błędy dostawcy zasobów plików NetApp platformy Azure, ich przyczyny, rozwiązania i obejścia (jeśli są dostępne).

## <a name="common-azure-netapp-files-resource-provider-errors"></a>Typowe błędy dostawcy zasobów plików NetApp platformy Azure

***Nie można zmienić bareMetalTenantId.***  

Ten błąd występuje podczas próby aktualizacji lub `BaremetalTenantId` poprawki woluminu i właściwość ma zmienioną wartość.

* Przyczyna:   
Próbujesz zaktualizować wolumin, a `BaremetalTenantId` właściwość ma inną wartość niż wartość przechowywana na platformie Azure.
* Rozwiązanie:   
Nie dołączaj `BaremetalTenantId` do poprawki i żądania aktualizacji (put). Alternatywnie upewnij `BaremetalTenantId` się, że jest taka sama w żądaniu.

***Nie można zmienić poziomu serwisowego.***  

Ten błąd występuje podczas próby aktualizacji lub poprawki puli pojemności z innym poziomem usług, gdy pula pojemności ma już woluminy w nim.

* Przyczyna:   
Próbujesz zaktualizować poziom usługi puli pojemności, gdy pula zawiera woluminy.
* Rozwiązanie:   
Usuń wszystkie woluminy z puli pojemności, a następnie zmień poziom usługi.
* Obejście:   
Utwórz inną pulę pojemności, a następnie ponownie utwórz woluminy w nowej puli pojemności.

***Nie można zmienić programu PoolId***  

Ten błąd występuje podczas próby aktualizacji lub poprawki `PoolId` puli pojemności ze zmienioną właściwością.

* Przyczyna:   
Próbujesz zaktualizować właściwość `PoolId` puli pojemności. Właściwość `PoolId` jest właściwością tylko do odczytu i nie można jej zmienić.
* Rozwiązanie:   
Nie dołączaj `PoolId` do poprawki i żądania aktualizacji (put).  Alternatywnie upewnij `PoolId` się, że jest taka sama w żądaniu.

***CreationToken nie można zmienić.***

Ten błąd występuje podczas próby zmiany`CreationToken`ścieżki pliku ( ) po utworzeniu woluminu. Ścieżka pliku`CreationToken`( ) musi być ustawiona podczas tworzenia woluminu i nie można jej później zmienić.

* Przyczyna:   
Próbujesz zmienić ścieżkę`CreationToken`pliku ( ) po utworzeniu woluminu, który nie jest obsługiwaną operacją. 
* Rozwiązanie:   
Jeśli zmiana ścieżki pliku nie jest potrzebna, należy rozważyć usunięcie parametru z żądania, aby odrzucić komunikat o błędzie.
* Obejście:   
Aby zmienić ścieżkę pliku`CreationToken`( ), można utworzyć nowy wolumin z nową ścieżką pliku, a następnie przeprowadzić migrację danych do nowego woluminu.

***CreationToken musi mieć co najmniej 16 znaków.***

Ten błąd występuje, gdy`CreationToken`ścieżka pliku ( ) nie spełnia wymagań dotyczących długości. Długość ścieżki pliku musi mieć co najmniej jeden znak długości.

* Przyczyna:   
Ścieżka pliku jest pusta.  Podczas tworzenia woluminu przy użyciu interfejsu API wymagany jest token tworzenia. Jeśli używasz witryny Azure portal, ścieżka pliku jest generowany automatycznie.
* Rozwiązanie:   
Wprowadź co najmniej jeden znak`CreationToken`jako ścieżkę pliku ( ).

***Nie można zmienić nazwy domeny.***

Ten błąd występuje podczas próby zmiany nazwy domeny w usłudze Active Directory.

* Przyczyna:   
Próbujesz zaktualizować właściwość nazwy domeny.
* Rozwiązanie:    
Brak. Nie można zmienić nazwy domeny.
* Obejście:   
Usuń wszystkie woluminy przy użyciu konfiguracji usługi Active Directory. Następnie usuń konfigurację usługi Active Directory i ponownie utwórz woluminy.

***Błąd zduplikowanej wartości dla obiektu ExportPolicy.Rules[RuleIndex].***

Ten błąd występuje, gdy zasady eksportu nie jest zdefiniowany z unikatowym indeksem. Podczas definiowania zasad eksportu wszystkie reguły zasad eksportu muszą mieć unikatowy indeks zamiejscem od 1 do 5.

* Przyczyna:   
Zdefiniowana polityka eksportu nie spełnia wymogu reguł polityki eksportowej. Minimalna reguła zasad eksportu musi mieć jedną regułę zasad eksportu i maksymalnie pięć reguł zasad eksportu.
* Rozwiązanie:   
Upewnij się, że indeks nie jest już używany i że znajduje się w zakresie od 1 do 5.
* Obejście:   
Użyj innego indeksu dla reguły, którą próbujesz ustawić.

***Błąd {akcja} {resourceTypeName}***

Ten błąd jest wyświetlany, gdy inna obsługa błędów nie może obsłużyć błędu podczas wykonywania akcji na zasobie.   Zawiera tekst "Błąd". Może `{action}` to być`getting`dowolny `creating` `updating`z `deleting`( , , , lub ).  Jest `{resourceTypeName}` `resourceTypeName` (na przykład `netAppAccount`, `capacityPool` `volume`, , i tak dalej).

* Przyczyna:   
Ten błąd jest nieobsługiwana wyjątek, gdzie przyczyna nie jest znana.
* Rozwiązanie:   
Skontaktuj się z Centrum pomocy technicznej platformy Azure, aby zgłosić szczegółową przyczynę w dziennikach.
* Obejście:   
Brak.

***Nazwa ścieżki pliku może zawierać tylko litery, cyfry i łączniki ("-"").***

Ten błąd występuje, gdy ścieżka pliku zawiera nieobsługiwały znaki, na przykład kropkę ("."), przecinek (","), znak podkreślenia ("_") lub znak dolara ("$").

* Przyczyna:   
Ścieżka pliku zawiera nieobsługiwały się znaki, na przykład kropkę ("."), przecinek (","), znak podkreślenia ("_") lub znak dolara ("$").
* Rozwiązanie:   
Usuń znaki, które nie są alfabetycznymi literami, cyframi lub myślnikami ("-") z wprowadzonej ścieżki pliku.
* Obejście:   
Można zastąpić podkreślenie łącznikiem lub użyć wielkich liter zamiast spacji, aby wskazać początek nowych słów.  Na przykład użyj "NewVolume" zamiast "nowy wolumin".

***Nie można zmienić funkcji FileSystemId.***

Ten błąd występuje podczas `FileSystemId`próby zmiany .  Zmiana `FileSystemdId` nie jest obsługiwaną operacją. 

* Przyczyna:   
Identyfikator systemu plików jest ustawiany podczas tworzenia woluminu. `FileSystemId`nie można później zmienić.
* Rozwiązanie:   
Nie dołączaj `FileSystemId` do poprawki i żądania aktualizacji (put).  Alternatywnie upewnij `FileSystemId` się, że jest taka sama w żądaniu.

***ActiveDirectory z identyfikatorem: '{string}' nie istnieje.***

Część `{string}` jest wartością wprowadzona `ActiveDirectoryId` we właściwości połączenia usługi Active Directory.

* Przyczyna:   
Podczas tworzenia konta z konfiguracją usługi Active Directory wprowadzono `ActiveDirectoryId` wartość, dla której ma być pusta.
* Rozwiązanie:   
Nie uwzględniaj `ActiveDirectoryId` w żądaniu tworzenia (umieszczania).

***Nieprawidłowa wersja interfejsu API.***

Wersja interfejsu API nie jest przesłana lub zawiera nieprawidłową wartość.

* Przyczyna:   
Wartość w parametrze `api-version` kwerendy zawiera nieprawidłową wartość.
* Rozwiązanie:   
Użyj poprawnej wartości wersji interfejsu API.  Dostawca zasobów obsługuje wiele wersji interfejsu API. Wartość jest w formacie yyyy-mm-dd.

***Odebrano nieprawidłową wartość "{value}" dla {1}.***

Ten komunikat wskazuje błąd w `RuleIndex`polach dla `Nfsv3`, `Nfsv4` `AllowedClients`, `UnixReadOnly`, `UnixReadWrite`, i .

* Przyczyna:   
Żądanie sprawdzania poprawności danych wejściowych nie powiodło `RuleIndex`się `AllowedClients` `UnixReadOnly`dla `UnixReadWrite`co najmniej jednego z następujących pól: , , , , `Nfsv`3 i `Nfsv4`.
* Rozwiązanie:   
Upewnij się, że w wierszu polecenia ustawiono wszystkie wymagane i niekonfliktowe parametry. Na przykład nie można `UnixReadOnly` ustawić `UnixReadWrite` zarówno i parametry w tym samym czasie.
* Obejście:   
Zobacz rozwiązanie powyżej.

***Zakres {0} IP {1} do {2} vlan jest już w użyciu***

Ten błąd występuje, ponieważ wewnętrzne rekordy używanych zakresów adresów IP mają konflikt z nowo przypisanym adresem IP.

* Przyczyna:   
Adres IP przypisany do tworzenia woluminu jest już zarejestrowany.
Przyczyną może być wcześniejsze nie powiodło się utworzenie woluminu.
* Rozwiązanie:   
Skontaktuj się z Centrum pomocy technicznej platformy Azure.

***Brak wartości elementu "{property}".***

Ten błąd wskazuje, że wymagana właściwość brakuje żądania. Ciąg {property} zawiera nazwę brakującej właściwości.

* Przyczyna:   
Żądanie sprawdzania poprawności danych wejściowych nie powiodło się dla co najmniej jednej z właściwości.
* Rozwiązanie:   
Upewnij się, aby ustawić wszystkie wymagane i niekonfliktowanie właściwości w żądaniu, szczególnie, właściwość z komunikatu o błędzie.

***Nie można zmienić mounttargets.***

Ten błąd występuje, gdy użytkownik próbuje zaktualizować lub załatać woluminu MountTargets właściwości.

* Przyczyna:   
Próbujesz zaktualizować właściwość `MountTargets` woluminu. Zmiana tej właściwości nie jest obsługiwana.
* Rozwiązanie:   
Nie dołączaj `MountTargets` do poprawki i żądania aktualizacji (put).  Alternatywnie upewnij się, że `MountTargets` jest taka sama w żądaniu.

***Nazwa już używana.***

Ten błąd wskazuje, że nazwa zasobu jest już w użyciu.

* Przyczyna:   
Próbujesz utworzyć zasób o nazwie, która jest używana dla istniejącego zasobu.
* Rozwiązanie:   
Podczas tworzenia zasobu należy użyć unikatowej nazwy.

***Ścieżka pliku jest już używana.***

Ten błąd wskazuje, że ścieżka pliku dla woluminu jest już używana.

* Przyczyna:   
Próbujesz utworzyć wolumin ze ścieżką pliku, która jest taka sama jak istniejący wolumin.
* Rozwiązanie:   
Podczas tworzenia woluminu należy użyć unikatowej ścieżki pliku.

***Nazwa jest za długa.***

Ten błąd wskazuje, że nazwa zasobu nie spełnia wymagań dotyczących maksymalnej długości.

* Przyczyna:   
Nazwa zasobu jest za długa.
* Rozwiązanie:   
Użyj krótszej nazwy zasobu.

***Ścieżka pliku jest za długa.***

Ten błąd wskazuje, że ścieżka pliku dla woluminu nie spełnia wymagań dotyczących maksymalnej długości.

* Przyczyna:   
Ścieżka pliku woluminu jest za długa.
* Rozwiązanie:   
Użyj krótszej ścieżki pliku.

***Nazwa za krótka.***

Ten błąd wskazuje, że nazwa zasobu nie spełnia wymagań dotyczących minimalnej długości.

* Przyczyna:   
Nazwa zasobu jest za krótka.
* Rozwiązanie:   
Użyj dłuższej nazwy zasobu.

***Ścieżka pliku jest za krótka.***

Ten błąd wskazuje, że ścieżka pliku woluminu nie spełnia wymagań dotyczących minimalnej długości.

* Przyczyna:   
Ścieżka pliku woluminu jest za krótka.
* Rozwiązanie:   
Zwiększ długość ścieżki pliku woluminu.

***Nieosiągalny interfejs API plików usługi Azure NetApp.***

Interfejs API platformy Azure opiera się na interfejsie API plików NetApp platformy Azure do zarządzania woluminami. Ten błąd wskazuje na problem z połączeniem interfejsu API.

* Przyczyna:   
Podstawowy interfejs API nie odpowiada, co powoduje błąd wewnętrzny. Ten błąd może być tymczasowy.
* Rozwiązanie:   
Problem może być tymczasowy. Żądanie powinno zakończyć się pomyślnie po pewnym czasie.
* Obejście:   
Brak. Podstawowy interfejs API jest niezbędny do zarządzania woluminami.

***Nie znaleziono identyfikatora wyniku{0}operacji dla ' '.***

Ten błąd wskazuje, że błąd wewnętrzny uniemożliwia wykonanie operacji.

* Przyczyna:   
Wystąpił błąd wewnętrzny, który uniemożliwił wykonanie operacji.
* Rozwiązanie:   
Ten błąd może być tymczasowy. Odczekaj kilka minut i spróbuj ponownie. Jeśli problem będzie się powtarzał, utwórz bilet, aby pomoc techniczna zbadała problem.
* Obejście:   
Odczekaj kilka minut i sprawdź, czy problem nie występuje.

***Nie wolno mieszać typów protokołów CIFS i NFS***

Ten błąd występuje podczas próby utworzenia woluminu i istnieją zarówno typy protokołów CIFS (SMB), jak i NFS we właściwościach woluminu.

* Przyczyna:   
Zarówno typy protokołów CIFS (SMB), jak i NFS są używane we właściwościach woluminu.
* Rozwiązanie:   
Usuń jeden z typów protokołu.
* Obejście:   
Pozostaw właściwość typu protokołu pustą lub null.

***Liczba elementów: {value} dla obiektu: ExportPolicy.Rules[RuleIndex] znajduje się poza zakresem min-max.***

Ten błąd występuje, gdy reguły zasad eksportu nie spełniają wymagań dotyczących minimalnego lub maksymalnego zakresu. Jeśli zdefiniujesz zasady eksportu, musi ona mieć jedną regułę zasad eksportu na co najmniej i pięć reguł zasad eksportu na maksimum.

* Przyczyna:   
Zdefiniowana polityka eksportu nie spełnia wymaganego zakresu.
* Rozwiązanie:   
Upewnij się, że indeks nie jest już używany i znajduje się w zakresie od 1 do 5.
* Obejście:   
Stosowanie zasad eksportowych w ilościach nie jest obowiązkowe. Zasady eksportu można całkowicie pominąć, jeśli nie trzeba używać reguł zasad eksportu.

***Dozwolona tylko jedna usługa active directory***

Ten błąd występuje podczas próby utworzenia konfiguracji usługi Active Directory, a jeden już istnieje dla subskrypcji w regionie. Błąd może również wystąpić podczas próby utworzenia więcej niż jednej konfiguracji usługi Active Directory.

* Przyczyna:   
Próbujesz utworzyć (nie zaktualizować) aktywną usługę katalogową, ale już istnieje.
* Rozwiązanie:   
Jeśli konfiguracja usługi Active Directory nie jest używana, można najpierw usunąć istniejącą konfigurację, a następnie ponowić próbę utworzenia.
* Obejście:   
Brak. Dozwolona jest tylko jedna usługa Active Directory.

***Operacja "{operation}" nie jest obsługiwana.***

Ten błąd wskazuje, że operacja nie jest dostępna dla aktywnej subskrypcji lub zasobu.

* Przyczyna:   
Operacja nie jest dostępna dla subskrypcji lub zasobu.
* Rozwiązanie:   
Upewnij się, że operacja została wprowadzona poprawnie i że jest dostępna dla zasobu i subskrypcji, którego używasz.

***Nie można zmienić właścicielaidu***

Ten błąd występuje podczas próby zmiany OwnerId właściwości woluminu. Zmiana ownerid nie jest obsługiwana operacja. 

* Przyczyna:   
Właściwość `OwnerId` jest ustawiana podczas tworzenia woluminu. Nie można później zmienić właściwości.
* Rozwiązanie:   
Nie dołączaj `OwnerId` do poprawki i żądania aktualizacji (put). Alternatywnie upewnij się, że `OwnerId` jest taka sama w żądaniu.

***Nie znaleziono puli nadrzędnej***

Ten błąd występuje podczas próby utworzenia woluminu, a pula pojemności, w której tworzysz wolumin, nie zostanie znaleziona.

* Przyczyna:   
Nie można odnaleźć puli pojemności, w której tworzony jest wolumin.
* Rozwiązanie:   
Najprawdopodobniej pula nie została w pełni utworzona lub została już usunięta w momencie tworzenia woluminu.

***Operacja poprawki nie jest obsługiwana dla tego typu zasobu.***

Ten błąd występuje podczas próby zmiany obiektu docelowego lub migawki instalacji.

* Przyczyna:   
Miejsce docelowe instalacji jest definiowane podczas tworzenia i nie można go później zmienić.
Migawki nie zawierają żadnych właściwości, które można zmienić.
* Rozwiązanie:   
Brak. Te zasoby nie mają żadnych właściwości, które można zmienić.

***Rozmiar puli jest za mały dla całkowitego rozmiaru woluminu.***

Ten błąd występuje podczas aktualizowania rozmiaru puli pojemności, a rozmiar `usedBytes` jest mniejszy niż całkowita wartość wszystkich woluminów w tej puli pojemności.  Ten błąd może również wystąpić podczas tworzenia nowego woluminu lub zmiany rozmiaru istniejącego woluminu, a nowy rozmiar woluminu przekracza wolne miejsce w puli pojemności.

* Przyczyna:   
Próbujesz zaktualizować pulę pojemności do mniejszego rozmiaru niż używaneBajty we wszystkich woluminach w puli pojemności.  Lub próbujesz utworzyć wolumin, który jest większy niż wolne miejsce w puli pojemności.  Alternatywnie próbujesz zmienić rozmiar woluminu, a nowy rozmiar przekracza wolne miejsce w puli pojemności.
* Rozwiązanie:   
Ustaw rozmiar puli pojemności na większą wartość lub utwórz mniejszą objętość woluminu.
* Obejście:   
Usuń wystarczającą liczbę woluminów, aby można było zaktualizować rozmiar puli pojemności do tego rozmiaru.

***Właściwość: Lokalizacja migawki musi być taka sama jak wolumin***

Ten błąd występuje podczas tworzenia migawki z lokalizacją inną niż wolumin, który jest właścicielem migawki.

* Przyczyna:   
Nieprawidłowa wartość we właściwości Lokalizacja dla migawki.
* Rozwiązanie:   
Ustaw prawidłowy ciąg we właściwości Lokalizacja.

***Nazwa {resourceType} musi być taka sama jak nazwa identyfikatora zasobu.***

Ten błąd występuje podczas tworzenia zasobu i wypełniasz właściwość name inną wartością niż właściwość name `resourceId`.

* Przyczyna:   
Nieprawidłowa wartość we właściwości name podczas tworzenia zasobu.
* Rozwiązanie:   
Pozostaw właściwość name pustą lub pozwól jej użyć tej samej wartości co właściwość name (między ostatnim ukośnikem odwrotnym "/" a znakiem zapytania "?) w pliku `resourceId`.

***Typ protokołu {value} nie jest znany***

Ten błąd występuje podczas tworzenia woluminu o nieznanym typie protokołu.  Prawidłowe wartości to "NFSv3", "NFSv4" i "CIFS".

* Przyczyna:   
Próbujesz ustawić nieprawidłową wartość we `protocolType` właściwości woluminu.
* Rozwiązanie:   
Ustaw prawidłowy `protocolType`ciąg w pliku .
* Obejście:   
Ustaw `protocolType` jako null.

***Nie można zmienić typów protokołów***

Ten błąd występuje podczas próby `ProtocolType` aktualizacji lub poprawki dla woluminu.  Zmiana typu protokołu Protocol nie jest obsługiwaną operacją.

* Przyczyna:   
Właściwość `ProtocolType` jest ustawiana podczas tworzenia woluminu.  Nie można go zaktualizować.
* Rozwiązanie:   
Brak.
* Obejście:   
Utwórz kolejny wolumin z nowymi typami protokołów.

***Utworzenie zasobu typu {resourceType} przekraczałoby przydział zasobów {quota} typu {resourceType} na {parentResourceType}. Bieżąca liczba zasobów to {currentCount}, usuń niektóre zasoby tego typu przed utworzeniem nowego.***

Ten błąd występuje podczas próby utworzenia`NetAppAccount` `CapacityPool`zasobu ( , , `Volume`, lub `Snapshot`), ale przydział osiągnął limit.

* Przyczyna:   
Próbujesz utworzyć zasób, ale limit przydziału `NetAppAccounts` został osiągnięty (np. na subskrypcję lub `CapacityPools` na `NetAppAccount`).
* Rozwiązanie:   
Zwiększ limit przydziału.
* Obejście:   
Usuń nieużywane zasoby tego samego typu i utwórz je ponownie.

***Odebrano wartość właściwości tylko do odczytu "{propertyName}".***

Ten błąd występuje podczas definiowania wartości dla właściwości, której nie można zmienić. Na przykład nie można zmienić identyfikatora woluminu.

* Przyczyna:   
Próbujesz zmodyfikować parametr (na przykład identyfikator woluminu), którego nie można zmienić.
* Rozwiązanie:   
Nie należy modyfikować wartość właściwości.

***Nie znaleziono żądanego {resource}.***

Ten błąd występuje podczas próby odwoływania się do nieistniejącego zasobu, na przykład woluminu lub migawki. Zasób mógł zostać usunięty lub mieć nazwę zasobu misspelt.

* Przyczyna:   
Próbujesz odwołać się do nieistniejącego zasobu (na przykład woluminu lub migawki), który został już usunięty lub ma błędnie wtyczoną nazwę zasobu.
* Rozwiązanie:   
Sprawdź żądanie błędów pisowni, aby upewnić się, że jest poprawnie odwołuje.
* Obejście:   
Zobacz sekcję Rozwiązanie powyżej.

***Poziom usługi "{volumeServiceLevel}" jest wyższy niż nadrzędny '{poolServiceLevel}'***

Ten błąd występuje podczas tworzenia lub aktualizowania woluminu i ustawiono poziom usługi na wyższym poziomie niż pula pojemności, która go zawiera.

* Przyczyna:   
Próbujesz utworzyć lub zaktualizować wolumin z wyższym poziomem usług w rankingu niż nadrzędna pula pojemności.
* Rozwiązanie:   
Ustaw poziom usługi na taką samą lub niższą rangę niż nadrzędna pula pojemności.
* Obejście:   
Utwórz wolumin w innej puli pojemności z poprawnym poziomem usług. Alternatywnie usuń wszystkie woluminy z puli pojemności i ustaw poziom usług dla puli pojemności na wyższą rangę.

***Nazwa serwera SMB nie może być dłuższa niż 10 znaków.***

Ten błąd występuje podczas tworzenia lub aktualizowania konfiguracji usługi Active Directory dla konta.

* Przyczyna:   
Długość nazwy serwera SMB przekracza 10 znaków.
* Rozwiązanie:   
Użyj krótszej nazwy serwera. Maksymalna długość wynosi 10 znaków.
* Obejście:   
Brak.  Zobacz rozwiązanie powyżej. 

***Nie można zmienić podsieci.***

Ten błąd występuje podczas próby `subnetId` zmiany po utworzeniu woluminu.  `SubnetId`należy ustawić podczas tworzenia woluminu i nie można go później zmienić.

* Przyczyna:   
Próbujesz zmienić `subnetId` po utworzeniu woluminu, który nie jest obsługiwaną operacją. 
* Rozwiązanie:   
Jeśli zmiana `subnetId` nie jest potrzebna, należy rozważyć usunięcie parametru z żądania, aby odrzucić komunikat o błędzie.
* Obejście:   
Jeśli trzeba zmienić `subnetId`program , można utworzyć nowy `subnetId`wolumin z nowym , a następnie przenieść dane do nowego woluminu.

***PodsieciId jest w nieprawidłowym formacie.***

Ten błąd występuje podczas próby utworzenia `subnetId` nowego woluminu, ale nie jest `resourceId` dla podsieci.

* Przyczyna:   
Ten błąd występuje podczas próby utworzenia nowego `subnetId` woluminu, ale nie jest `resourceId` dla podsieci. 
* Rozwiązanie:   
Sprawdź wartość dla, `subnetId` aby upewnić `resourceId` się, że zawiera dla podsieci używane.
* Obejście:   
Brak. Zobacz rozwiązanie powyżej. 

***Podsieć musi mieć delegowanie "Microsoft.NetApp/volumes".***

Ten błąd występuje podczas tworzenia woluminu, a wybrana podsieć nie jest delegowana do `Microsoft.NetApp/volumes`pliku .

* Przyczyna:   
Próbowano utworzyć wolumin i wybrano podsieć, która nie jest delegowana do `Microsoft.NetApp/volumes`.
* Rozwiązanie:   
Wybierz inną podsieć, `Microsoft.NetApp/volumes`do których jest delegowana .
* Obejście:   
Dodaj prawidłowe delegowanie do podsieci.

***Określony typ zasobu jest nieznany/nie dotyczy.***

Ten błąd występuje, gdy zażądano sprawdzania nazwy w przypadku niepodanego zastosowania typu zasobu lub nieznanego typu zasobu.

* Przyczyna:   
Zażądano sprawdzania nazw dla nieznanego lub nieobsługiconego typu zasobu.
* Rozwiązanie:   
Sprawdź, czy zasób, dla którego wykonujesz żądanie, jest obsługiwany lub nie zawiera żadnych błędów pisowni.
* Obejście:   
Zobacz rozwiązanie powyżej.

***Nieznany błąd plików NetApp platformy Azure.***

Interfejs API platformy Azure opiera się na interfejsie API plików NetApp platformy Azure do zarządzania woluminami. Błąd wskazuje na problem w komunikacji z interfejsem API.

* Przyczyna:   
Podstawowy interfejs API wysyła nieznany błąd. Ten błąd może być tymczasowy.
* Rozwiązanie:   
Problem może być tymczasowy, a żądanie powinno zakończyć się pomyślnie po pewnym czasie. Jeśli problem będzie się powtarzał, utwórz bilet pomocy technicznej, aby zbadać problem.
* Obejście:   
Brak. Podstawowy interfejs API jest niezbędny do zarządzania woluminami.

***Wartość odebrana dla nieznanej właściwości "{propertyName}".***

Ten błąd występuje, gdy nieistniejące właściwości są dostarczane dla zasobu, takich jak wolumin, migawka lub miejsce docelowe instalacji.

* Przyczyna:   
Żądanie ma zestaw właściwości, które mogą być używane z każdego zasobu. Nie można dołączyć żadnych nieistniejących właściwości w żądaniu.
* Rozwiązanie:   
Upewnij się, że wszystkie nazwy właściwości są wpisane poprawnie i że właściwości są dostępne dla subskrypcji i zasobu.
* Obejście:   
Zmniejsz liczbę właściwości zdefiniowanych w żądaniu, aby wyeliminować właściwość, która jest przyczyną błędu.

***Operacja aktualizacji nie jest obsługiwana dla tego typu zasobu.***

Można aktualizować tylko woluminy. Ten błąd występuje podczas próby wykonania nieobsługiconej operacji aktualizacji, na przykład aktualizowanie migawki.

* Przyczyna:   
Zasób, który próbujesz zaktualizować, nie obsługuje operacji aktualizacji. Tylko woluminy mogą mieć zmodyfikowane ich właściwości.
* Rozwiązanie:   
Brak. Zasób, który próbujesz zaktualizować, nie obsługuje operacji aktualizacji. W związku z tym nie można go zmienić.
* Obejście:   
W przypadku woluminu utwórz nowy zasób z aktualizacją w miejscu i migruj dane.

***Woluminu nie można utworzyć w puli, która nie jest w stanie powiodła się.***

Ten błąd występuje podczas próby utworzenia woluminu w puli, która nie jest w stanie powiodło się. Najprawdopodobniej operacja tworzenia dla puli pojemności nie powiodła się z jakiegoś powodu.

* Przyczyna:   
Pula pojemności zawierająca nowy wolumin jest w stanie awarii.
* Rozwiązanie:   
Sprawdź, czy pula pojemności została utworzona pomyślnie i czy nie jest w stanie awarii.
* Obejście:   
Utwórz nową pulę pojemności i utwórz wolumin w nowej puli.

***Wolumin jest tworzony i obecnie nie można go usunąć.***

Ten błąd występuje podczas próby usunięcia woluminu, który jest nadal tworzony.

* Przyczyna:   
Wolumin jest nadal tworzony podczas próby usunięcia woluminu.
* Rozwiązanie:   
Poczekaj na zakończenie tworzenia woluminu, a następnie ponów próbę usunięcia.
* Obejście:   
Zobacz rozwiązanie powyżej.

***Wolumin jest usuwany i obecnie nie można go usunąć.***

Ten błąd występuje podczas próby usunięcia woluminu, gdy jest on już usuwany.

* Przyczyna:   
Wolumin jest już usuwany podczas próby usunięcia woluminu.
* Rozwiązanie:   
Poczekaj na zakończenie bieżącej operacji usuwania.
* Obejście:   
Zobacz rozwiązanie powyżej.

***Wolumin jest aktualizowany i obecnie nie można go usunąć.***

Ten błąd występuje podczas próby usunięcia woluminu, który jest aktualizowany.

* Przyczyna:   
Wolumin jest aktualizowany podczas próby usunięcia woluminu.
* Rozwiązanie:   
Poczekaj na zakończenie operacji aktualizacji, a następnie ponów próbę usunięcia.
* Obejście:   
Zobacz rozwiązanie powyżej.

***Wolumin nie został znaleziony lub nie został pomyślnie utworzony.***

Ten błąd występuje, gdy tworzenie woluminu nie powiodło się i próbujesz zmienić wolumin lub utworzyć migawkę dla woluminu.

* Przyczyna:   
Wolumin nie istnieje lub tworzenie nie powiodło się.
* Rozwiązanie:   
Sprawdź, czy zmieniasz prawidłowy wolumin i czy utworzenie woluminu zakończyło się pomyślnie. Lub sprawdź, czy wolumin, dla którego tworzysz migawkę, istnieje.
* Obejście:   
Brak.  Zobacz rozwiązanie powyżej. 

***Określony token tworzenia już istnieje***

Ten błąd występuje podczas próby utworzenia woluminu i określić token tworzenia (ścieżka eksportu), dla którego wolumin już istnieje.

* Przyczyna:   
Token tworzenia (ścieżka eksportu) określony podczas tworzenia woluminu jest już skojarzony z innym woluminem. 
* Rozwiązanie:   
Wybierz inny token tworzenia.  Alternatywnie usuń inny wolumin.

***Określony token tworzenia jest zarezerwowany***

Ten błąd występuje podczas próby utworzenia woluminu i określić "domyślny" lub "brak" jako ścieżkę pliku (token tworzenia).

* Przyczyna:    
Próbujesz utworzyć wolumin i określić "domyślny" lub "brak" jako ścieżkę pliku (token tworzenia).
* Rozwiązanie:   
Wybierz inną ścieżkę pliku (token tworzenia).
 
***Poświadczenia usługi Active Directory są używane***

Ten błąd występuje podczas próby usunięcia konfiguracji usługi Active Directory z konta, na którym nadal istnieje co najmniej jeden wolumin SMB.  Wolumin SMB został utworzony przy użyciu konfiguracji usługi Active Directory, którą próbujesz usunąć.

* Przyczyna:   
Próbujesz usunąć konfigurację usługi Active Directory z konta, ale nadal istnieje co najmniej jeden wolumin SMB, który został pierwotnie utworzony przy użyciu konfiguracji usługi Active Directory. 
* Rozwiązanie:   
Najpierw usuń wszystkie woluminy SMB utworzone przy użyciu konfiguracji usługi Active Directory.  Następnie ponów próbę usunięcia konfiguracji.

***Nie można zmodyfikować przypisania jednostki organizacyjnej, jeśli poświadczenia są używane***

Ten błąd występuje podczas próby zmiany jednostki organizacyjnej konfiguracji usługi Active Directory, ale nadal istnieje co najmniej jeden wolumin SMB.  Wolumin SMB został utworzony przy użyciu tej konfiguracji usługi Active Directory, którą próbujesz usunąć.

* Przyczyna:   
Próbujesz zmienić jednostkę organizacyjną konfiguracji usługi Active Directory.  Ale nadal istnieje co najmniej jeden wolumin SMB, który został pierwotnie utworzony przy użyciu konfiguracji usługi Active Directory.
* Rozwiązanie:   
 Najpierw usuń wszystkie woluminy SMB utworzone przy użyciu konfiguracji usługi Active Directory.  Następnie ponów próbę usunięcia konfiguracji. 

***Aktualizacja usługi Active Directory już w toku***

Ten błąd występuje podczas próby edycji konfiguracji usługi Active Directory, dla której operacja edycji jest już w toku.

* Przyczyna:   
Próbujesz edytować konfigurację usługi Active Directory, ale inna operacja edycji jest już w toku.
* Rozwiązanie:   
Poczekaj, aż aktualnie uruchomiona operacja edycji się zakończy.

***Najpierw usuń wszystkie woluminy przy użyciu wybranych poświadczeń***

Ten błąd występuje podczas próby usunięcia konfiguracji usługi Active Directory, ale nadal istnieje co najmniej jeden wolumin SMB.  Wolumin SMB został utworzony przy użyciu konfiguracji usługi Active Directory, którą próbujesz usunąć.

* Przyczyna:   
Próbujesz usunąć konfigurację usługi Active Directory, ale nadal istnieje co najmniej jeden wolumin SMB, który został pierwotnie utworzony przy użyciu konfiguracji usługi Active Directory.
* Rozwiązanie:   
Najpierw usuń wszystkie woluminy SMB utworzone przy użyciu konfiguracji usługi Active Directory.  Następnie ponów próbę usunięcia konfiguracji. 

***W regionie nie znaleziono poświadczeń usługi Active Directory***

Ten błąd występuje podczas próby utworzenia woluminu SMB, ale nie dodano konfiguracji usługi Active Directory do konta dla regionu.

* Przyczyna:   
Próbujesz utworzyć wolumin SMB, ale do konta nie została dodana żadna konfiguracja usługi Active Directory. 
* Rozwiązanie:   
Przed utworzeniem woluminu SMB należy dodać konfigurację usługi Active Directory do konta.

***Nie można zbadać serwera DNS. Sprawdź, czy konfiguracja sieci jest poprawna i czy są dostępne serwery DNS.***

Ten błąd występuje podczas próby utworzenia woluminu SMB, ale serwer DNS (określony w konfiguracji usługi Active Directory) jest nieosiągalny. 

* Przyczyna:   
Próbujesz utworzyć wolumin SMB, ale serwer DNS (określony w konfiguracji usługi Active Directory) jest nieosiągalny.
* Rozwiązanie:   
Przejrzyj konfigurację usługi Active Directory i upewnij się, że adresy IP serwera DNS są poprawne i osiągalne.
Jeśli nie ma problemów z adresami IP serwera DNS, sprawdź, czy żadne zapory nie blokują dostępu.

***Zbyt wiele równoczesnych zadań***

Ten błąd występuje podczas próby utworzenia migawki, gdy trzy inne operacje tworzenia migawki są już w toku dla subskrypcji.

* Przyczyna:   
Próbujesz utworzyć migawkę, gdy trzy inne operacje tworzenia migawki są już w toku dla subskrypcji. 
* Rozwiązanie:   
Zadania tworzenia migawek zająć najwyżej kilka sekund, aby zakończyć.  Odczekaj kilka sekund i ponów próbę wykonania operacji tworzenia migawki.

***Nie można zrodzić dodatkowych zadań. Poczekaj na zakończenie trwających zadań i spróbuj ponownie***

Ten błąd może wystąpić podczas próby utworzenia lub usunięcia woluminu w określonych okolicznościach.

* Przyczyna:   
Próbujesz utworzyć lub usunąć wolumin w określonych okolicznościach.
* Rozwiązanie:   
Zaczekaj minutę i ponów próbę wykonania operacji.

***Wolumin jest już przejście między stanami***

Ten błąd może wystąpić podczas próby usunięcia woluminu, który jest obecnie w stanie przejścia (czyli obecnie w stanie tworzenia, aktualizowania lub usuwania).

* Przyczyna:   
Próbujesz usunąć wolumin, który jest obecnie w stanie przejścia.
* Rozwiązanie:   
Poczekaj, aż aktualnie uruchomiona (przejście stanu) operacja zostanie zakończona, a następnie ponów próbę wykonania operacji.

***Nie można podzielić nowego woluminu z migawki woluminu źródłowego***

 Ten błąd może wystąpić podczas próby utworzenia woluminu z migawki.  

* Przyczyna:   
Spróbuj utworzyć wolumin z migawki, a wolumin kończy się w stanie błędu.
* Rozwiązanie:   
Usuń wolumin, a następnie ponów próbę utworzenia woluminu z migawki.

 
## <a name="next-steps"></a>Następne kroki

* [Tworzenie plików NetApp platformy Azure za pomocą interfejsu API REST](azure-netapp-files-develop-with-rest-api.md)
