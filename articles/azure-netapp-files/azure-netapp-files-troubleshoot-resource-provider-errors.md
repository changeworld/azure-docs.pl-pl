---
title: Rozwiązywanie problemów z błędami dostawcy zasobów Azure NetApp Files | Microsoft Docs
description: Opisuje przyczyny, rozwiązania i obejścia typowych błędów dostawcy zasobów Azure NetApp Files.
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
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597204"
---
# <a name="troubleshoot-azure-netapp-files-resource-provider-errors"></a>Rozwiązywanie problemów związanych z błędami dostawcy zasobów usługi Azure NetApp Files 

W tym artykule opisano typowe błędy dostawcy zasobów Azure NetApp Files, ich przyczyny, rozwiązania i obejścia (jeśli są dostępne).

## <a name="common-azure-netapp-files-resource-provider-errors"></a>Typowe błędy dostawcy zasobów Azure NetApp Files

***Nie można zmienić BareMetalTenantId.***  

Ten błąd występuje podczas próby zaktualizowania lub poprawki woluminu, a właściwość `BaremetalTenantId` ma zmienioną wartość.

* Przyczyna:   
Próbujesz zaktualizować wolumin, a właściwość `BaremetalTenantId` ma inną wartość niż wartość przechowywana na platformie Azure.
* Narzędzie   
Nie dołączaj `BaremetalTenantId` do żądania patch i Update (Put). Alternatywnie upewnij się, że `BaremetalTenantId` jest taka sama w żądaniu.

***Nie można zmienić poziomu ServiceLevel.***  

Ten błąd występuje podczas próby zaktualizowania lub poprawki puli pojemności przy użyciu innego poziomu usługi, gdy pula pojemności zawiera już woluminy.

* Przyczyna:   
Podjęto próbę zaktualizowania poziomu usługi puli pojemności, gdy pula zawiera woluminy.
* Narzędzie   
Usuń wszystkie woluminy z puli pojemności, a następnie zmień poziom usługi.
* Poprawkę   
Utwórz kolejną pulę pojemności, a następnie ponownie utwórz woluminy w nowej puli pojemności.

***Nie można zmienić PoolId***  

Ten błąd występuje podczas próby zaktualizowania lub poprawki puli pojemności przy użyciu zmienionej właściwości `PoolId`.

* Przyczyna:   
Podjęto próbę zaktualizowania właściwości `PoolId` puli pojemności. Właściwość `PoolId` jest właściwością tylko do odczytu i nie można jej zmienić.
* Narzędzie   
Nie dołączaj `PoolId` do żądania patch i Update (Put).  Alternatywnie upewnij się, że `PoolId` jest taka sama w żądaniu.

***Nie można zmienić CreationToken.***

Ten błąd występuje podczas próby zmiany ścieżki pliku (`CreationToken`) po utworzeniu woluminu. Ścieżka pliku (`CreationToken`) musi być ustawiona podczas tworzenia woluminu i nie można jej później zmienić.

* Przyczyna:   
Próbujesz zmienić ścieżkę pliku (`CreationToken`) po utworzeniu woluminu, co nie jest obsługiwaną operacją. 
* Narzędzie   
Jeśli zmiana ścieżki pliku nie jest konieczna, Rozważ usunięcie parametru z żądania, aby odrzucić komunikat o błędzie.
* Poprawkę   
Jeśli musisz zmienić ścieżkę pliku (`CreationToken`), możesz utworzyć nowy wolumin z nową ścieżką pliku, a następnie przeprowadzić migrację danych do nowego woluminu.

***CreationToken musi składać się z co najmniej 16 znaków.***

Ten błąd występuje, gdy ścieżka pliku (`CreationToken`) nie spełnia wymagań dotyczących długości. Długość ścieżki pliku musi wynosić co najmniej jeden znak.

* Przyczyna:   
Ścieżka pliku jest pusta.  W przypadku tworzenia woluminu przy użyciu interfejsu API wymagany jest token tworzenia. Jeśli używasz Azure Portal, ścieżka pliku jest generowana automatycznie.
* Narzędzie   
Wprowadź co najmniej jeden znak jako ścieżkę do pliku (`CreationToken`).

***Nie można zmienić nazwy domeny.***

Ten błąd występuje podczas próby zmiany nazwy domeny w Active Directory.

* Przyczyna:   
Próbujesz zaktualizować Właściwość nazwy domeny.
* Narzędzie    
Brak. Nie można zmienić nazwy domeny.
* Poprawkę   
Usuń wszystkie woluminy, korzystając z konfiguracji Active Directory. Następnie usuń konfigurację Active Directory i ponownie utwórz woluminy.

***Błąd zduplikowanej wartości dla obiektu ExportPolicy. rules [RuleIndex].***

Ten błąd występuje, gdy zasady eksportowania nie są zdefiniowane przy użyciu unikatowego indeksu. Podczas definiowania zasad eksportu wszystkie reguły eksportu muszą mieć unikatowy indeks z zakresu od 1 do 5.

* Przyczyna:   
Zdefiniowane zasady eksportowania nie spełniają wymagań dotyczących eksportu reguł zasad. Na poziomie minimalnym i pięciu reguł dotyczących zasad eksportu wymagana jest jedna reguła eksportowania zasad.
* Narzędzie   
Upewnij się, że indeks nie jest jeszcze używany i że znajduje się w zakresie od 1 do 5.
* Poprawkę   
Użyj innego indeksu dla reguły, którą próbujesz ustawić.

***Błąd {Action} {resourceTypeName}***

Ten błąd jest wyświetlany, gdy w innej obsłudze błędów nie udało się obsłużyć błędu podczas wykonywania akcji dla zasobu.   Zawiera tekst "Error". @No__t_0 może być dowolną z (`getting`, `creating`, `updating` lub `deleting`).  @No__t_0 jest `resourceTypeName` (na przykład `netAppAccount`, `capacityPool`, `volume` itd.).

* Przyczyna:   
Ten błąd jest nieobsługiwanym wyjątkiem, gdy przyczyna jest nieznana.
* Narzędzie   
Skontaktuj się z centrum pomocy technicznej platformy Azure, aby zgłosić szczegółowy powód w dziennikach.
* Poprawkę   
Brak.

***Nazwa ścieżki pliku może zawierać litery, cyfry i łączniki (""-"").***

Ten błąd występuje, gdy ścieżka pliku zawiera nieobsługiwane znaki, na przykład kropkę ("."), przecinek (","), podkreślenie ("_") lub znak dolara ("$").

* Przyczyna:   
Ścieżka pliku zawiera nieobsługiwane znaki, na przykład kropkę ("."), przecinek (","), podkreślenie ("_") lub znak dolara ("$").
* Narzędzie   
Usuń znaki, które nie mają liter, cyfr ani łączników ("-") z wprowadzonej ścieżki pliku.
* Poprawkę   
Możesz zamienić znak podkreślenia na łącznik lub użyć wielkich liter zamiast spacji, aby wskazać początek nowych wyrazów.  Na przykład użyj "NewVolume" zamiast "New Volume".

***Nie można zmienić FileSystemId.***

Ten błąd występuje podczas próby zmiany `FileSystemId`.  Zmiana `FileSystemdId` nie jest obsługiwaną operacją. 

* Przyczyna:   
Identyfikator systemu plików jest ustawiany podczas tworzenia woluminu. nie można później zmienić `FileSystemId`.
* Narzędzie   
Nie dołączaj `FileSystemId` do żądania patch i Update (Put).  Alternatywnie upewnij się, że `FileSystemId` jest taka sama w żądaniu.

***ActiveDirectory o identyfikatorze: "{String}" nie istnieje.***

Część `{string}` jest wartością wprowadzoną we właściwości `ActiveDirectoryId` dla połączenia Active Directory.

* Przyczyna:   
Podczas tworzenia konta z konfiguracją Active Directory wprowadzono wartość dla `ActiveDirectoryId`, która powinna być pusta.
* Narzędzie   
Nie dołączaj `ActiveDirectoryId` do żądania Create (Put).

***Nieprawidłowa wersja interfejsu API.***

Wersja interfejsu API nie została przesłana lub zawiera nieprawidłową wartość.

* Przyczyna:   
Wartość w parametrze zapytania `api-version` zawiera nieprawidłową wartość.
* Narzędzie   
Użyj poprawnej wartości wersji interfejsu API.  Dostawca zasobów obsługuje wiele wersji interfejsu API. Wartość jest w formacie RRRR-MM-DD.

***Odebrano nieprawidłową wartość "{value}" dla {1}.***

Ten komunikat wskazuje na błąd w polach dla `RuleIndex`, `AllowedClients`, `UnixReadOnly`, `UnixReadWrite`, `Nfsv3` i `Nfsv4`.

* Przyczyna:   
Żądanie walidacji danych wejściowych nie powiodło się dla co najmniej jednego z następujących pól: `RuleIndex`, `AllowedClients`, `UnixReadOnly`, `UnixReadWrite`, `Nfsv`3 i `Nfsv4`.
* Narzędzie   
Upewnij się, że ustawiono wszystkie wymagane i niepowodujące konflikt parametry w wierszu polecenia. Na przykład nie można jednocześnie ustawić parametrów `UnixReadOnly` i `UnixReadWrite`.
* Poprawkę   
Zapoznaj się z powyższym rozwiązaniem.

***Zakres adresów IP {0} do {1} dla sieci VLAN {2} jest już używany***

Ten błąd występuje, ponieważ wewnętrzne rekordy używanych zakresów adresów IP mają konflikt z nowo przypisanym adresem IP.

* Przyczyna:   
Adres IP przypisany do tworzenia woluminu jest już zarejestrowany.
Przyczyną może być wcześniejsze utworzenie woluminu zakończonego niepowodzeniem.
* Narzędzie   
Skontaktuj się z centrum pomocy technicznej platformy Azure.

***Brak wartości właściwości "{Property}".***

Ten błąd wskazuje, że w żądaniu brakuje wymaganej właściwości. Ciąg {Property} zawiera nazwę brakującej właściwości.

* Przyczyna:   
Żądanie walidacji danych wejściowych nie powiodło się dla co najmniej jednej właściwości.
* Narzędzie   
Upewnij się, że w żądaniu ustawiono wszystkie wymagane i niepowodujące konflikt właściwości.

***Nie można zmienić MountTargets.***

Ten błąd występuje, gdy użytkownik próbuje zaktualizować lub poprawić Właściwość MountTargets woluminu.

* Przyczyna:   
Próbujesz zaktualizować Właściwość `MountTargets` woluminu. Zmiana tej właściwości nie jest obsługiwana.
* Narzędzie   
Nie dołączaj `MountTargets` do żądania patch i Update (Put).  Alternatywnie upewnij się, że `MountTargets` jest taka sama w żądaniu.

***Nazwa jest już używana.***

Ten błąd wskazuje, że nazwa zasobu jest już używana.

* Przyczyna:   
Próbujesz utworzyć zasób o nazwie, która jest używana dla istniejącego zasobu.
* Narzędzie   
Użyj unikatowej nazwy podczas tworzenia zasobu.

***Ścieżka pliku jest już używana.***

Ten błąd wskazuje, że ścieżka pliku dla woluminu jest już używana.

* Przyczyna:   
Próbujesz utworzyć wolumin z ścieżką pliku, która jest taka sama jak istniejący wolumin.
* Narzędzie   
Podczas tworzenia woluminu Użyj unikatowej ścieżki pliku.

***Nazwa jest za długa.***

Ten błąd wskazuje, że nazwa zasobu nie spełnia wymagań dotyczących maksymalnej długości.

* Przyczyna:   
Nazwa zasobu jest za długa.
* Narzędzie   
Użyj krótszej nazwy zasobu.

***Ścieżka pliku jest za długa.***

Ten błąd wskazuje, że ścieżka pliku dla woluminu nie spełnia wymagań dotyczących maksymalnej długości.

* Przyczyna:   
Ścieżka pliku woluminu jest za długa.
* Narzędzie   
Użyj krótszej ścieżki pliku.

***Nazwa jest za krótka.***

Ten błąd wskazuje, że nazwa zasobu nie spełnia wymagania dotyczącego minimalnej długości.

* Przyczyna:   
Nazwa zasobu jest za krótka.
* Narzędzie   
Użyj dłuższej nazwy zasobu.

***Ścieżka pliku jest za krótka.***

Ten błąd wskazuje, że ścieżka pliku woluminu nie spełnia wymagań dotyczących minimalnej długości.

* Przyczyna:   
Ścieżka pliku woluminu jest za krótka.
* Narzędzie   
Zwiększ długość ścieżki pliku woluminu.

***Azure NetApp Files interfejs API jest nieosiągalny.***

Interfejs API platformy Azure opiera się na interfejsie API Azure NetApp Files, aby zarządzać woluminami. Ten błąd wskazuje na problem z połączeniem interfejsu API.

* Przyczyna:   
Źródłowy interfejs API nie odpowiada, co spowodowało błąd wewnętrzny. Ten błąd może być tymczasowy.
* Narzędzie   
Problem jest prawdopodobnie tymczasowy. Żądanie powinno zakończyć się za jakiś czas.
* Poprawkę   
Brak. Podstawowy interfejs API jest istotny do zarządzania woluminami.

***Nie znaleziono identyfikatora wyniku operacji dla "{0}".***

Ten błąd wskazuje, że błąd wewnętrzny uniemożliwia ukończenie operacji.

* Przyczyna:   
Wystąpił błąd wewnętrzny i uniemożliwiło ukończenie operacji.
* Narzędzie   
Ten błąd może być tymczasowy. Poczekaj kilka minut i spróbuj ponownie. Jeśli problem będzie nadal występować, Utwórz bilet, aby uzyskać pomoc techniczną w celu zbadania problemu.
* Poprawkę   
Poczekaj kilka minut i sprawdź, czy problem nadal występuje.

***Nie można mieszać typów protokołów CIFS i NFS***

Ten błąd występuje, gdy próbujesz utworzyć wolumin, a w właściwościach woluminu istnieją zarówno typy protokołu CIFS (SMB), jak i systemu plików NFS.

* Przyczyna:   
Typy protokołów CIFS (SMB) i NFS są używane we właściwościach woluminu.
* Narzędzie   
Usuń jeden z typów protokołów.
* Poprawkę   
Pozostaw pustą właściwość typu protokołu lub wartość null.

***Liczba elementów: {value} dla obiektu: ExportPolicy. rules [RuleIndex] jest poza zakresem minimalnym.***

Ten błąd występuje, gdy reguły eksportowania zasad nie spełniają wymagań dotyczących minimalnej lub maksymalnej liczby zakresów. Jeśli zdefiniujesz zasady eksportowania, musi mieć jedną regułę eksportowania zasad z minimalnym i pięcioma regułami eksportowania zasad.

* Przyczyna:   
Zdefiniowane zasady eksportowania nie są zgodne z wymaganym zakresem.
* Narzędzie   
Upewnij się, że indeks nie jest jeszcze używany i znajduje się w zakresie od 1 do 5.
* Poprawkę   
Użycie zasad eksportowania na woluminach nie jest obowiązkowe. Zasady eksportu można pominąć całkowicie, jeśli nie jest konieczne używanie reguł eksportowania zasad.

***Dozwolona jest tylko jedna usługa Active Directory***

Ten błąd występuje podczas próby utworzenia konfiguracji Active Directory, która już istnieje dla subskrypcji w regionie. Ten błąd może również wystąpić podczas próby utworzenia więcej niż jednej konfiguracji Active Directory.

* Przyczyna:   
Próbujesz utworzyć (nie zaktualizować) usługi Active Directory, ale już istnieje.
* Narzędzie   
Jeśli konfiguracja Active Directory nie jest używana, można najpierw usunąć istniejącą konfigurację, a następnie ponowić próbę wykonania operacji tworzenia.
* Poprawkę   
Brak. Dozwolony jest tylko jeden Active Directory.

***Operacja "{Operation}" nie jest obsługiwana.***

Ten błąd wskazuje, że operacja jest niedostępna dla aktywnej subskrypcji lub zasobu.

* Przyczyna:   
Operacja jest niedostępna dla subskrypcji lub zasobu.
* Narzędzie   
Upewnij się, że operacja została wprowadzona poprawnie i że jest dostępna dla zasobu i subskrypcji, której używasz.

***Nie można zmienić OwnerId***

Ten błąd występuje podczas próby zmiany właściwości OwnerId woluminu. Zmiana OwnerId nie jest obsługiwaną operacją. 

* Przyczyna:   
Właściwość `OwnerId` jest ustawiana podczas tworzenia woluminu. Nie można później zmienić właściwości.
* Narzędzie   
Nie dołączaj `OwnerId` do żądania patch i Update (Put). Alternatywnie upewnij się, że `OwnerId` jest taka sama w żądaniu.

***Nie znaleziono puli nadrzędnej***

Ten błąd występuje podczas próby utworzenia woluminu i puli pojemności, w której tworzysz wolumin, nie można odnaleźć.

* Przyczyna:   
Nie znaleziono puli pojemności, w której tworzony jest wolumin.
* Narzędzie   
Najprawdopodobniej pula nie została w pełni utworzona lub została już usunięta w momencie tworzenia woluminu.

***Operacja patch nie jest obsługiwana dla tego typu zasobu.***

Ten błąd występuje podczas próby zmiany docelowej instalacji lub migawki.

* Przyczyna:   
Obiekt docelowy instalacji jest definiowany podczas jego tworzenia i nie można go zmienić później.
Migawki nie zawierają żadnych właściwości, które można zmienić.
* Narzędzie   
Brak. Te zasoby nie mają żadnych właściwości, które można zmienić.

***Rozmiar puli jest za mały dla łącznego rozmiaru woluminu.***

Ten błąd występuje, gdy aktualizujesz rozmiar puli pojemności, a rozmiar jest mniejszy niż łączna wartość `usedBytes` wszystkich woluminów w tej puli pojemności.  Ten błąd może również wystąpić podczas tworzenia nowego woluminu lub zmiany rozmiaru istniejącego woluminu, a nowy rozmiar woluminu przekracza ilość wolnego miejsca w puli pojemności.

* Przyczyna:   
Podjęto próbę zaktualizowania puli pojemności do mniejszej wielkości niż usedBytes we wszystkich woluminach w puli pojemności.  Lub próbujesz utworzyć wolumin, który jest większy niż ilość wolnego miejsca w puli pojemności.  Alternatywnie należy zmienić rozmiar woluminu, a nowy rozmiar przekracza ilość wolnego miejsca w puli pojemności.
* Narzędzie   
Ustaw rozmiar puli pojemności na większą wartość lub Utwórz mniejszy wolumin dla woluminu.
* Poprawkę   
Usuń wystarczającą ilość woluminów, aby można było zaktualizować rozmiar puli pojemności do tego rozmiaru.

***Właściwość: Location dla migawki musi być taka sama jak wolumin***

Ten błąd występuje podczas tworzenia migawki z lokalizacją inną niż wolumin, który jest właścicielem migawki.

* Przyczyna:   
Nieprawidłowa wartość we właściwości Location dla migawki.
* Narzędzie   
Ustaw prawidłowy ciąg we właściwości Location.

***Nazwa {sourceType} musi być taka sama jak nazwa identyfikatora zasobu.***

Ten błąd występuje podczas tworzenia zasobu i wypełnij Właściwość Name inną wartością niż Właściwość Name elementu `resourceId`.

* Przyczyna:   
Nieprawidłowa wartość właściwości Name podczas tworzenia zasobu.
* Narzędzie   
Pozostaw Właściwość Name pustą lub Zezwól, aby używała tej samej wartości co Właściwość Name (od ostatniego ukośnika odwrotnego "/" i znaku zapytania "?") w `resourceId`.

***Nieznany typ protokołu {Value}***

Ten błąd występuje podczas tworzenia woluminu z nieznanym typem protokołu.  Prawidłowe wartości to "NFSv3", "NFSv4" i "CIFS".

* Przyczyna:   
Podjęto próbę ustawienia nieprawidłowej wartości we właściwości `protocolType` woluminu.
* Narzędzie   
Ustaw prawidłowy ciąg w `protocolType`.
* Poprawkę   
Ustaw `protocolType` jako null.

***Nie można zmienić typów protokołów***

Ten błąd występuje podczas próby aktualizacji lub poprawki `ProtocolType` woluminu.  Zmiana ProtocolType nie jest obsługiwaną operacją.

* Przyczyna:   
Właściwość `ProtocolType` jest ustawiana podczas tworzenia woluminu.  Nie można jej zaktualizować.
* Narzędzie   
Brak.
* Poprawkę   
Utwórz inny wolumin z nowymi typami protokołów.

***Utworzenie zasobu typu {ResourceType} spowodowałoby przekroczenie przydziału zasobów {Quote} typu {ResourceType} na {parentResourceType}. Bieżąca liczba zasobów to {currentCount}, Usuń część zasobów tego typu przed utworzeniem nowego.***

Ten błąd występuje podczas próby utworzenia zasobu (`NetAppAccount`, `CapacityPool`, `Volume` lub `Snapshot`), ale limit przydziału został osiągnięty.

* Przyczyna:   
Podjęto próbę utworzenia zasobu, ale osiągnięto limit przydziału (przykład: `NetAppAccounts` na subskrypcję lub `CapacityPools` na `NetAppAccount`).
* Narzędzie   
Zwiększ limit przydziału.
* Poprawkę   
Usuń nieużywane zasoby tego samego typu i utwórz je ponownie.

***Odebrano wartość właściwości "{propertyName}" tylko do odczytu.***

Ten błąd występuje, gdy definiujesz wartość właściwości, której nie można zmienić. Nie można na przykład zmienić identyfikatora woluminu.

* Przyczyna:   
Próbujesz zmodyfikować parametr (na przykład identyfikator woluminu), którego nie można zmienić.
* Narzędzie   
Nie należy modyfikować wartości właściwości.

***Nie znaleziono żądanego zasobu {Resource}.***

Ten błąd występuje podczas próby odwołania się do nieistniejącego zasobu, na przykład woluminu lub migawki. Zasób mógł zostać usunięty lub mieć nieprawidłową nazwę zasobu.

* Przyczyna:   
Próbujesz odwołać się do nieistniejącego zasobu (na przykład woluminu lub migawki), który został już usunięty lub ma błędną nazwę zasobu.
* Narzędzie   
Sprawdź żądanie błędów pisowni, aby upewnić się, że jest ono prawidłowo wywoływane.
* Poprawkę   
Zobacz sekcję rozwiązanie powyżej.

***Poziom usług "{volumeServiceLevel}" jest wyższy niż element nadrzędny "{poolServiceLevel}"***

Ten błąd występuje podczas tworzenia lub aktualizowania woluminu oraz ustawiania poziomu usługi na wyższy poziom niż Pula pojemności, która ją zawiera.

* Przyczyna:   
Próbujesz utworzyć lub zaktualizować wolumin o wyższym poziomie rangi usługi niż Pula nadrzędna pojemności.
* Narzędzie   
Ustaw poziom usługi na taki sam lub niższy od nadrzędnej puli pojemności.
* Poprawkę   
Utwórz wolumin w innej puli pojemności przy użyciu poprawnego poziomu usługi. Alternatywnie Usuń wszystkie woluminy z puli pojemności i ustaw poziom usług dla puli pojemności na wyższą rangę.

***Nazwa serwera SMB nie może być dłuższa niż 10 znaków.***

Ten błąd występuje podczas tworzenia lub aktualizowania konfiguracji Active Directory dla konta.

* Przyczyna:   
Długość nazwy serwera SMB przekracza 10 znaków.
* Narzędzie   
Użyj krótszej nazwy serwera. Maksymalna długość wynosi 10 znaków.
* Poprawkę   
Brak.  Zapoznaj się z powyższym rozwiązaniem. 

***Nie można zmienić SubnetId.***

Ten błąd występuje podczas próby zmiany `subnetId` po utworzeniu woluminu.  należy ustawić `SubnetId`, gdy wolumin jest tworzony i nie można go później zmienić.

* Przyczyna:   
Podjęto próbę zmiany `subnetId` po utworzeniu woluminu, który nie jest obsługiwaną operacją. 
* Narzędzie   
Jeśli zmiana `subnetId` nie jest konieczna, Rozważ usunięcie parametru z żądania, aby odrzucić komunikat o błędzie.
* Poprawkę   
Jeśli trzeba zmienić `subnetId`, można utworzyć nowy wolumin z nowym `subnetId`, a następnie przeprowadzić migrację danych do nowego woluminu.

***SubnetId ma nieprawidłowy format.***

Ten błąd występuje podczas próby utworzenia nowego woluminu, ale `subnetId` nie jest `resourceId` dla podsieci.

* Przyczyna:   
Ten błąd występuje podczas próby utworzenia nowego woluminu, ale `subnetId` nie jest `resourceId` dla podsieci. 
* Narzędzie   
Sprawdź wartość `subnetId`, aby upewnić się, że zawiera `resourceId` dla używanej podsieci.
* Poprawkę   
Brak. Zapoznaj się z powyższym rozwiązaniem. 

***Podsieć musi mieć delegowanie "Microsoft. NetApp/Volumes".***

Ten błąd występuje podczas tworzenia woluminu, a wybrana podsieć nie jest delegowana do `Microsoft.NetApp/volumes`.

* Przyczyna:   
Podjęto próbę utworzenia woluminu i wybrania podsieci, która nie jest delegowana do `Microsoft.NetApp/volumes`.
* Narzędzie   
Wybierz inną podsieć delegowaną do `Microsoft.NetApp/volumes`.
* Poprawkę   
Dodaj poprawne delegowanie do podsieci.

***Określony typ zasobu jest nieznany/nie ma zastosowania.***

Ten błąd występuje, gdy zażądano sprawdzenia nazwy dla nieodpowiedniego typu zasobu lub dla nieznanego typu zasobu.

* Przyczyna:   
Zażądano sprawdzenia nazwy dla nieznanego lub nieobsługiwanego typu zasobu.
* Narzędzie   
Sprawdź, czy zasób, dla którego wykonujesz żądanie, jest obsługiwany lub nie zawiera błędów pisowni.
* Poprawkę   
Zapoznaj się z powyższym rozwiązaniem.

***Nieznany błąd Azure NetApp Files.***

Interfejs API platformy Azure opiera się na interfejsie API Azure NetApp Files, aby zarządzać woluminami. Ten błąd wskazuje na problem związany z komunikacją z interfejsem API.

* Przyczyna:   
Źródłowy interfejs API wysyła nieznanego błędu. Ten błąd może być tymczasowy.
* Narzędzie   
Problem jest prawdopodobnie tymczasowy i żądanie powinno zakończyć się powodzeniem po pewnym czasie. Jeśli problem będzie nadal występować, Utwórz bilet pomocy technicznej, aby rozwiązać problem.
* Poprawkę   
Brak. Podstawowy interfejs API jest istotny do zarządzania woluminami.

***Odebrano wartość dla nieznanej właściwości "{propertyName}".***

Ten błąd występuje, gdy dla zasobu, takiego jak wolumin, migawka lub miejsce docelowe instalacji, są podane nieistniejące właściwości.

* Przyczyna:   
Żądanie zawiera zestaw właściwości, które mogą być używane z każdym zasobem. W żądaniu nie można uwzględnić żadnych nieistniejących właściwości.
* Narzędzie   
Upewnij się, że wszystkie nazwy właściwości są poprawnie napisane i że właściwości są dostępne dla subskrypcji i zasobu.
* Poprawkę   
Zmniejsz liczbę właściwości zdefiniowanych w żądaniu, aby wyeliminować właściwość, która powoduje błąd.

***Operacja aktualizacji nie jest obsługiwana dla tego typu zasobu.***

Można aktualizować tylko woluminy. Ten błąd występuje podczas próby wykonania nieobsługiwanej operacji aktualizacji, na przykład zaktualizowania migawki.

* Przyczyna:   
Zasób, który próbujesz zaktualizować, nie obsługuje operacji aktualizacji. Tylko woluminy mogą mieć zmodyfikowane właściwości.
* Narzędzie   
Brak. Zasób, który próbujesz zaktualizować, nie obsługuje operacji aktualizacji. W związku z tym nie można go zmienić.
* Poprawkę   
W przypadku woluminu Utwórz nowy zasób z aktualizacją w miejscu i Przeprowadź migrację danych.

***Nie można utworzyć woluminu w puli, która nie ma stanu powiodła się.***

Ten błąd występuje podczas próby utworzenia woluminu w puli, która nie jest w stanie "powodzenie". Z jakiegoś powodu operacja tworzenia dla puli pojemności nie powiodła się.

* Przyczyna:   
Pula pojemności zawierająca nowy wolumin jest w stanie niepowodzenia.
* Narzędzie   
Sprawdź, czy pula pojemności została utworzona pomyślnie i czy nie jest w stanie niepowodzenia.
* Poprawkę   
Utwórz nową pulę pojemności i Utwórz wolumin w nowej puli.

***Trwa Tworzenie woluminu i nie można go usunąć.***

Ten błąd występuje podczas próby usunięcia woluminu, który jest nadal tworzony.

* Przyczyna:   
Wolumin jest wciąż tworzony podczas próby usunięcia woluminu.
* Narzędzie   
Poczekaj na zakończenie tworzenia woluminu, a następnie spróbuj ponownie wykonać operację usuwania.
* Poprawkę   
Zapoznaj się z powyższym rozwiązaniem.

***Trwa Usuwanie woluminu i nie można go usunąć.***

Ten błąd występuje podczas próby usunięcia woluminu, gdy jest już usuwany.

* Przyczyna:   
Wolumin jest już usuwany podczas próby usunięcia woluminu.
* Narzędzie   
Poczekaj na zakończenie bieżącej operacji usuwania.
* Poprawkę   
Zapoznaj się z powyższym rozwiązaniem.

***Trwa aktualizowanie woluminu i nie można go usunąć.***

Ten błąd występuje podczas próby usunięcia woluminu, który jest aktualizowany.

* Przyczyna:   
Wolumin jest aktualizowany podczas próby usunięcia woluminu.
* Narzędzie   
Poczekaj na zakończenie operacji aktualizacji, a następnie ponów próbę usunięcia.
* Poprawkę   
Zapoznaj się z powyższym rozwiązaniem.

***Nie odnaleziono woluminu lub nie został on utworzony pomyślnie.***

Ten błąd występuje, gdy Tworzenie woluminu nie powiodło się i podjęto próbę zmiany woluminu lub utworzenia migawki dla tego woluminu.

* Przyczyna:   
Wolumin nie istnieje lub tworzenie nie powiodło się.
* Narzędzie   
Sprawdź, czy zmieniasz prawidłowy wolumin i czy Tworzenie woluminu zakończyło się pomyślnie. Możesz też sprawdzić, czy istnieje wolumin, dla którego tworzysz migawkę.
* Poprawkę   
Brak.  Zapoznaj się z powyższym rozwiązaniem. 

***Określony token tworzenia już istnieje***

Ten błąd występuje podczas próby utworzenia woluminu i określania tokenu tworzenia (ścieżki eksportu), dla którego wolumin już istnieje.

* Przyczyna:   
Token tworzenia (ścieżka eksportu) określony podczas tworzenia woluminu jest już skojarzony z innym woluminem. 
* Narzędzie   
Wybierz inny token tworzenia.  Alternatywnie Usuń inny wolumin.

***Określony token tworzenia jest zarezerwowany***

Ten błąd występuje podczas próby utworzenia woluminu i określania wartości "default" lub "none" jako ścieżki pliku (tokenu tworzenia).

* Przyczyna:    
Próbujesz utworzyć wolumin i określić wartość "default" lub "none" jako ścieżkę pliku (token tworzenia).
* Narzędzie   
Wybierz inną ścieżkę pliku (token tworzenia).
 
***Poświadczenia Active Directory są używane***

Ten błąd występuje podczas próby usunięcia konfiguracji Active Directory z konta, w którym wciąż istnieje co najmniej jeden wolumin SMB.  Wolumin SMB został utworzony przy użyciu konfiguracji Active Directory, którą próbujesz usunąć.

* Przyczyna:   
Podjęto próbę usunięcia konfiguracji Active Directory z konta, ale nadal istnieje co najmniej jeden wolumin SMB, który został pierwotnie utworzony przy użyciu konfiguracji Active Directory. 
* Narzędzie   
Najpierw usuń wszystkie woluminy SMB, które zostały utworzone przy użyciu konfiguracji Active Directory.  Następnie ponów próbę usunięcia konfiguracji.

***Nie można zmodyfikować przypisania jednostki organizacyjnej, jeśli poświadczenia są używane***

Ten błąd występuje podczas próby zmiany jednostki organizacyjnej konfiguracji Active Directory, ale nadal istnieje co najmniej jeden wolumin SMB.  Wolumin SMB został utworzony przy użyciu tej konfiguracji Active Directory, którą próbujesz usunąć.

* Przyczyna:   
Podjęto próbę zmiany jednostki organizacyjnej konfiguracji Active Directory.  Ale nadal istnieje co najmniej jeden wolumin SMB, który został pierwotnie utworzony przy użyciu konfiguracji Active Directory.
* Narzędzie   
 Najpierw usuń wszystkie woluminy SMB, które zostały utworzone przy użyciu konfiguracji Active Directory.  Następnie ponów próbę usunięcia konfiguracji. 

***Aktualizacja Active Directory już w toku***

Ten błąd występuje podczas próby edycji konfiguracji Active Directory, dla której operacja edytowania jest już w toku.

* Przyczyna:   
Próbujesz edytować konfigurację Active Directory, ale inna operacja edytowania jest już w toku.
* Narzędzie   
Poczekaj na zakończenie aktualnie uruchomionej operacji edycji.

***Najpierw usuń wszystkie woluminy, używając wybranych poświadczeń***

Ten błąd występuje podczas próby usunięcia konfiguracji Active Directory, ale nadal istnieje co najmniej jeden wolumin SMB.  Wolumin SMB został utworzony przy użyciu konfiguracji Active Directory, którą próbujesz usunąć.

* Przyczyna:   
Podjęto próbę usunięcia konfiguracji Active Directory, ale nadal istnieje co najmniej jeden wolumin SMB, który został pierwotnie utworzony przy użyciu konfiguracji Active Directory.
* Narzędzie   
Najpierw usuń wszystkie woluminy SMB, które zostały utworzone przy użyciu konfiguracji Active Directory.  Następnie ponów próbę usunięcia konfiguracji. 

***Nie znaleziono poświadczeń Active Directory w regionie***

Ten błąd występuje podczas próby utworzenia woluminu SMB, ale nie dodano konfiguracji Active Directory do konta dla regionu.

* Przyczyna:   
Podjęto próbę utworzenia woluminu SMB, ale nie dodano konfiguracji Active Directory do konta. 
* Narzędzie   
Przed utworzeniem woluminu SMB Dodaj konfigurację Active Directory do konta.

***Nie można wykonać zapytania dotyczącego serwera DNS. Sprawdź, czy konfiguracja sieci jest poprawna i czy serwery DNS są dostępne.***

Ten błąd występuje podczas próby utworzenia woluminu SMB, ale serwer DNS (określony w konfiguracji Active Directory) jest nieosiągalny. 

* Przyczyna:   
Próbujesz utworzyć wolumin SMB, ale serwer DNS (określony w konfiguracji Active Directory) jest nieosiągalny.
* Narzędzie   
Przejrzyj konfigurację Active Directory i upewnij się, że adresy IP serwera DNS są poprawne i dostępne.
Jeśli nie ma problemów z adresami IP serwera DNS, sprawdź, czy zapory nie blokują dostępu.

***Zbyt wiele współbieżnych zadań***

Ten błąd występuje podczas próby utworzenia migawki, gdy trzy inne operacje tworzenia migawki są już w toku dla subskrypcji.

* Przyczyna:   
Podjęto próbę utworzenia migawki, gdy trzy inne operacje tworzenia migawki są już w toku dla subskrypcji. 
* Narzędzie   
Zadania tworzenia migawek trwają kilka sekund.  Poczekaj kilka sekund i spróbuj ponownie wykonać operację tworzenia migawki.

***Nie można zduplikować dodatkowych zadań. Zaczekaj na zakończenie trwających zadań i spróbuj ponownie***

Ten błąd może wystąpić podczas próby utworzenia lub usunięcia woluminu w określonych okolicznościach.

* Przyczyna:   
Próbujesz utworzyć lub usunąć wolumin w określonych okolicznościach.
* Narzędzie   
Poczekaj chwilę, a następnie spróbuj ponownie wykonać operację.

***Wolumin jest już przenoszony między Stanami***

Ten błąd może wystąpić podczas próby usunięcia woluminu, który jest aktualnie w stanie przenoszenia (czyli obecnie w stanie tworzenia, aktualizowania lub usuwania).

* Przyczyna:   
Podjęto próbę usunięcia woluminu, który jest aktualnie w stanie przejścia.
* Narzędzie   
Zaczekaj na zakończenie operacji aktualnie uruchomionej (stanu przejścia), a następnie spróbuj ponownie wykonać operację.

***Nie można podzielić nowego woluminu z migawki woluminu źródłowego***

 Ten błąd może wystąpić podczas próby utworzenia woluminu na podstawie migawki.  

* Przyczyna:   
Próba utworzenia woluminu na podstawie migawki i woluminu zostanie zakończona w stanie błędu.
* Narzędzie   
Usuń wolumin, a następnie ponów próbę wykonania operacji tworzenia woluminu z migawki.

 
## <a name="next-steps"></a>Następne kroki

* [Programowanie dla Azure NetApp Files za pomocą interfejsu API REST](azure-netapp-files-develop-with-rest-api.md)
