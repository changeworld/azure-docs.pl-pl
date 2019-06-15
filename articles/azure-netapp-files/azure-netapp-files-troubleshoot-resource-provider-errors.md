---
title: Rozwiązywanie problemów z błędami dostawcy zasobów plików NetApp platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano przyczyny, rozwiązania i rozwiązania dla typowych błędów dostawcy zasobów plików NetApp platformy Azure.
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
ms.date: 03/25/2019
ms.author: b-juche
ms.openlocfilehash: d4e06429aa1efec7c3301c7d0f0e7e17800fd520
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "63769439"
---
# <a name="troubleshoot-azure-netapp-files-resource-provider-errors"></a>Rozwiązywanie problemów związanych z błędami dostawcy zasobów usługi Azure NetApp Files
W tym artykule opisano typowe dostawcy zasobów plików NetApp platformy Azure błędy, ich przyczyny, rozwiązania i rozwiązania problemu. 

<a name="error_01"></a>***Usługa Azure Key Vault nie jest skonfigurowane.***   
Usługa Azure Key Vault przechowuje poświadczenia wymagane do uzyskiwania dostępu do podstawowego interfejsu API. Ten błąd wskazuje, czy usługi Azure Key Vault nie otrzymał pełną poświadczeń do uzyskania dostępu do podstawowego interfejsu API.

* Przyczyna  
Usługa Azure Key Vault nie otrzymał poprawne poświadczenia lub poświadczenia są niekompletne.  

* Rozwiązanie   
Usługa Azure NetApp Files korzysta z usługi Azure Key Vault. Usługa Azure Key Vault jest uwierzytelniany przy użyciu tokenu z usługi Azure Active Directory. W związku z tym właściciel aplikacji musi zarejestrować aplikację w usłudze Azure Active Directory.

* Obejście   
Brak.  Usługa Azure Key Vault musi być poprawnie skonfigurowane dla przy użyciu usługi Azure Files NetApp.  

<a name="error_02"></a>***Nie można zmienić tworzenia tokenu.***   
Ten błąd występuje, gdy spróbujesz zmienić token tworzenia po utworzeniu woluminu.
Gdy wolumin zostanie utworzona i nie można zmienić później, należy ustawić token tworzenia.

* Przyczyna   
Chcesz zmienić token tworzenia po utworzeniu woluminu, który nie jest obsługiwaną operacją.

* Rozwiązanie   
Po utworzeniu woluminu, rozważ usunięcie parametru w żądaniu, aby zamknąć komunikat o błędzie.

* Obejście   
Jeśli musisz zmienić token tworzenia Utwórz nowy wolumin za pomocą nowego tokenu tworzenia, a następnie przeprowadzić migrację danych do nowego woluminu.


<a name="error_03"></a>***Tworzenie tokenu musi zawierać co najmniej 16 znaków.***   
Ten błąd występuje, gdy token tworzenia nie spełnia wymagań długości. Długość do utworzenia tokenu musi mieć co najmniej 16 znaków.

* Przyczyna   
Tworzenie tokenu nie spełnia wymagań długości.  Podczas tworzenia woluminu za pomocą interfejsu API tworzenia tokenu jest wymagany. Jeśli używasz portalu token może być generowane automatycznie.

* Rozwiązanie   
Zwiększ długość do utworzenia tokenu. Na przykład można dodać inne słowo, na początku lub końcu token tworzenia.

* Obejście   
Minimalna wymagana długość do utworzenia tokenu nie można pominąć.  Zwiększyć długość tworzenia tokenu, można użyć prefiksu lub sufiksu.


<a name="error_04"></a>***Błąd podczas usuwania woluminu, który nie został znaleziony w usłudze Azure Files NetApp.***   
Ten błąd wystąpił z powodu wewnętrznego rejestru zasobów nie jest zsynchronizowany.

* Przyczyna   
Wolumin mogą pozostać wyświetlanych w portalu przez pewien czas, po jego usunięciu. Jeśli usuniesz wolumin za pomocą interfejsu API jest możliwe, że wolumin nie został poprawnie określony. Błąd może być także spowodowany przez pamięć podręczną z nieaktualnej przeglądarki.

* Rozwiązanie   
Pamięć podręczną przeglądarki wyczyść korzystania z portalu. Istnieje również wewnętrznej pamięci podręcznej, które są odświeżane co 10 minut.  Możesz spróbować ponownie wyczyścić pamięć podręczną.  Jeśli problem będzie się powtarzać, po upływie 10 minut, możesz utworzyć bilet pomocy technicznej.

* Obejście   
W międzyczasie Użyj inny wolumin i Ignoruj istniejącą grupę.


<a name="error_05"></a>***Błąd podczas wstawiania nowego woluminu w usłudze Azure Files NetApp.***   
Ten błąd występuje, ponieważ wewnętrzny rejestru zasobów nie jest zsynchronizowany.

* Przyczyna   
Wolumin może być nadal wyświetlana w portalu przez pewien czas, po jego usunięciu. Jeśli usuniesz wolumin za pomocą interfejsu API jest możliwe, że wolumin nie został poprawnie określony.

* Rozwiązanie   
Jeśli używasz portalu wolumin został już utworzony.  Wolumin powinna pojawić się automatycznie. Jeśli problem nie zniknie, możesz utworzyć bilet pomocy technicznej.

* Obejście   
Wolumin można utworzyć przy użyciu innej nazwy i token różnych tworzenia.


<a name="error_06"></a>***Nazwa ścieżki pliku może zawierać litery, cyfry i łączniki (""-"") tylko.***   
Ten błąd występuje, gdy ścieżka pliku zawiera nieobsługiwane znaki, na przykład kropki ("."), przecinek (","), znaku podkreślenia ("\_"), lub znak dolara ($").

* Przyczyna   
Ścieżka pliku zawiera nieobsługiwane znaki, na przykład kropki ("."), przecinek (","), znaku podkreślenia ("\_"), lub znak dolara ($").

* Rozwiązanie   
Usuń znaki, które nie są alfabetycznej litery, cyfry lub łączniki ("-") ze ścieżki plików zostały wprowadzone.

* Obejście   
Można zastąpić znaku podkreślenia znak minusa lub użyj wielkich liter zamiast spacji, aby wskazać początku nowych słów (na przykład za pomocą "NewVolume" zamiast "nowy wolumin").


<a name="error_07"></a>***Nie można zmienić identyfikator woluminu.***   
Ten błąd występuje, gdy spróbujesz zmienić identyfikator woluminu.  Zmiana Identyfikatora woluminu nie jest obsługiwaną operacją.

* Przyczyna   
Identyfikator systemu plików jest ustawiona, gdy tworzony jest wolumin. Identyfikator woluminu nie można później zmienić.

* Rozwiązanie   
Brak.

* Obejście   
Brak.  Identyfikator woluminu jest generowany, gdy wolumin jest tworzony i nie można później zmienić.


<a name="error_08"></a>***Nieprawidłowa wartość "{0}" Odebrano {1}.***   
Ten komunikat oznacza błąd w polach RuleIndex, AllowedClients, UnixReadOnly UnixReadWrite, Nfsv3 i Nfsv4.

* Przyczyna   
Żądanie sprawdzania poprawności danych wejściowych nie powiodło się dla co najmniej jednej z następujących pól: RuleIndex, AllowedClients, UnixReadOnly, UnixReadWrite, Nfsv3 i Nfsv4.

* Rozwiązanie   
Upewnij się ustawić wszystkie parametry wymagane i bezkonfliktowe w wierszu polecenia. Na przykład nie można ustawić parametrów UnixReadOnly i UnixReadWrite, w tym samym czasie.

* Obejście   
W sekcji rozwiązanie.  


<a name="error_09"></a>***Brak wartości dla '{0}".***   
Ten błąd wskazuje, że brakuje wymaganego atrybutu z żądania dla co najmniej jednej z następujących parametrów: RuleIndex, AllowedClients, UnixReadOnly, UnixReadWrite, Nfsv3 i Nfsv4.

* Przyczyna   
Żądanie sprawdzania poprawności danych wejściowych nie powiodło się dla co najmniej jednej z następujących pól: RuleIndex, AllowedClients, UnixReadOnly, UnixReadWrite, Nfsv3 i Nfsv4.

* Rozwiązanie   
Upewnij się ustawić wszystkie parametry wymagane i bezkonfliktowe w wierszu polecenia. Na przykład nie można ustawić parametrów UnixReadOnly i UnixReadWrite w tym samym czasie

* Obejście   
W sekcji rozwiązanie.  


<a name="error_10"></a> ***{0} już w użyciu.***   
Ten błąd wskazuje, że nazwa zasobu usługi został już użyty.

* Przyczyna   
Próbujesz utworzyć wolumin o nazwie, która jest taka sama jak istniejącego woluminu.

* Rozwiązanie   
Unikatowa nazwa używana podczas tworzenia woluminu.

* Obejście   
Jeśli to konieczne, można zmienić nazwę istniejącego woluminu, dzięki czemu można użyć zamierzonego nazwy, nowy wolumin.


<a name="error_11"></a> ***{0} zbyt krótki.***   
Ten błąd wskazuje, że nazwa woluminu nie spełnia wymagań minimalnej długości.

* Przyczyna   
Nazwa woluminu jest zbyt krótki.

* Rozwiązanie   
Zwiększ długość nazwy woluminu.  

* Obejście   
Nazwa woluminu można dodać wspólnych prefiksu lub sufiksu.


<a name="error_12"></a>***Usługa Azure API plików NetApp jest nieosiągalny.***   
Interfejs API usługi Azure korzysta z interfejsu API plików NetApp platformy Azure do zarządzania woluminami.  Ten błąd wskazuje wystąpił problem z połączeniem interfejsu API.

* Przyczyna   
Podstawowy interfejs API nie odpowiada, co powodowało błąd wewnętrzny. Ten błąd prawdopodobnie może być tymczasowe.

* Rozwiązanie   
Problem polega na mogą być tymczasowe.  Żądanie ma być pomyślnie wykonane po pewnym czasie.

* Obejście   
Brak. Podstawowy interfejs API ma zasadnicze znaczenie dla zarządzania woluminami.  


<a name="error_13"></a>***Żadne poświadczenia nie można odnaleźć subskrypcji "{0}".***   
Ten błąd wskazuje, że podane poświadczenia są nieprawidłowe lub nie zostały ustawione prawidłowo w ramach subskrypcji.

* Przyczyna   
Poświadczenia, które są nieprawidłowe lub niepoprawnie ustawioną uniemożliwić dostęp do usługi zarządzania woluminami.

* Rozwiązanie   
Upewnij się, że poświadczenia są ustawiane i wprowadzona poprawnie, w wierszu polecenia.

* Obejście   
Brak.  Ustawianie poświadczeń poprawnie ma zasadnicze znaczenie dla przy użyciu usługi Azure Files NetApp.  


<a name="error_14"></a>***Nie identyfikatora wyniku operacji znaleziono "{0}".***   
Ten błąd wskazuje, że wystąpił wewnętrzny błąd uniemożliwia ukończenie operacji.

* Przyczyna   
Wystąpił wewnętrzny błąd i uniemożliwił ukończenie operacji.

* Rozwiązanie   
Ten błąd prawdopodobnie może być tymczasowe.  Poczekaj kilka minut i spróbuj ponownie. Jeśli problem będzie się powtarzać, Utwórz bilet, aby dostęp do pomocy technicznej zbadania problemu.

* Obejście   
Poczekaj kilka minut, a następnie sprawdź, czy problem nadal występuje.


<a name="error_15"></a>***Operacja "{0}" nie jest obsługiwane.***   
Ten błąd wskazuje, że polecenie nie jest dostępna dla aktywną subskrypcję lub zasób.

* Przyczyna   
Operacja nie jest dostępna dla subskrypcji lub zasobu.

* Rozwiązanie   
Upewnij się, że polecenie jest prawidłowy i dostępne dla zasobów i subskrypcji, które są używane.

* Obejście   
W sekcji rozwiązanie.  


<a name="error_16"></a>***Operacja Patch nie jest obsługiwana dla tego typu zasobu.***   
Ten błąd występuje, gdy spróbujesz zmienić miejsce docelowe instalacji lub migawki.

* Przyczyna   
Miejsce docelowe instalacji jest definiowany podczas jego tworzenia i nie można zmienić później.

* Rozwiązanie   
Brak.  Nie można zmienić miejsca docelowego instalacji, po jego utworzeniu.

* Obejście   
Brak.


<a name="error_17"></a>***Otrzymano wartość dla właściwości tylko do odczytu "{0}".***   
Ten błąd występuje podczas definiowania wartości dla właściwości, której nie można zmienić. Na przykład nie można zmienić identyfikator woluminu.

* Przyczyna   
Próbowano modyfikować parametrów (na przykład identyfikator woluminu), nie można jej zmienić.

* Rozwiązanie   
Brak. Nie można zmodyfikować parametry dla Identyfikatora woluminu.

* Obejście   
Identyfikator woluminu nie powinny wymagać modyfikacji.  W związku z tym obejście tego problemu nie jest konieczne.

<a name="error_18"></a>***Żądany {0} nie został znaleziony.***   
Ten błąd występuje podczas próby odwoływać się do nieistniejącego zasobu, na przykład wolumin lub migawki. Zasób został usunięty lub mają nazwę, błędnie zasobów.

* Przyczyna   
Chcesz się odwoływać się do nieistniejącego zasobu (na przykład wolumin lub migawki), który już został usunięty lub nie ma nazwę zasobu niepoprawnie właściwej.

* Rozwiązanie   
Sprawdź, czy żądanie dla błędów pisowni upewnić się, jest prawidłowo przywoływany

* Obejście   
W sekcji rozwiązanie.

<a name="error_19"></a>***Nie można pobrać poświadczeń dla subskrypcji "{0}".***   
Ten błąd wskazuje, że podane poświadczenia są nieprawidłowe lub niepoprawnie ustawione w ramach subskrypcji.

* Przyczyna   
Poświadczenia, które są nieprawidłowe lub niepoprawnie zestawu w ramach subskrypcji zablokowanie dostępu do usługi zarządzania woluminami.

* Rozwiązanie   
Upewnij się, że poświadczenia są ustawiane i wprowadzona poprawnie, w wierszu polecenia.

* Obejście   
Brak.  Poprawnie Ustaw poświadczenia są niezbędne do korzystania z plików NetApp platformy Azure.

<a name="error_20"></a>***Błąd pliki nieznany NetApp platformy Azure.***   
Interfejs API usługi Azure korzysta z interfejsu API plików NetApp platformy Azure do zarządzania woluminami. Ten błąd wskazuje problem podczas komunikacji z interfejsem API.

* Przyczyna   
Podstawowy interfejs API wysyła wystąpił nieznany błąd.  Ten błąd prawdopodobnie może być tymczasowe.

* Rozwiązanie   
Problem polega na mogą być tymczasowe i żądanie ma być pomyślnie wykonane po pewnym czasie. Jeśli problem będzie się powtarzać, Utwórz bilet pomocy technicznej, aby ten problem zbadaniu.

* Obejście   
Brak.  Podstawowy interfejs API ma zasadnicze znaczenie dla zarządzania woluminami.

<a name="error_21"></a>***Odebrano nieznany właściwości wartość "{0}".***   
Ten błąd występuje, gdy nieistniejącej właściwości są dostarczane dla zasobów, takich jak wolumin, migawki lub miejsce docelowe instalacji.

* Przyczyna   
Żądanie zawiera zbiór właściwości, które mogą być używane z każdego zasobu.  Nie może zawierać żadnych nieistniejącej właściwości w żądaniu.

* Rozwiązanie   
Upewnij się, że wszystkie nazwy właściwości jest poprawna i właściwości są dostępne dla danej subskrypcji i zasobów.

* Obejście   
Zmniejsz liczbę właściwości zdefiniowane w żądaniu, aby wyeliminować właściwość, która powoduje błąd.


<a name="error_22"></a>***Operacja aktualizacji nie jest obsługiwana dla tego typu zasobu.***   
Można aktualizować tylko woluminy. Ten błąd występuje podczas próby wykonania operacji nieobsługiwany aktualizacji, na przykład aktualizacja migawki.

* Przyczyna   
Zasób, który próbujesz zaktualizować nie obsługuje operacji aktualizacji.  Tylko woluminy może mieć właściwości, ich modyfikować.

* Rozwiązanie   
Brak.  Zasób, który próbujesz zaktualizować nie obsługuje operacji aktualizacji. W związku z tym nie można zmienić.

* Obejście   
Dla woluminu Utwórz nowy zasób za pomocą aktualizacji w miejscu, a następnie przeprowadzić migrację danych.


<a name="error_23"></a>***Liczba elementów: {0} dla obiektu: {1} jest poza zakresem minimum maksimum.***   
Ten błąd występuje, gdy Eksportuj reguły zasad nie spełniają wymagań minimalnych i maksymalnych zakresu.  Jeśli zdefiniujesz zasady eksportu, musi on mieć reguły co najmniej jeden Eksport i pięć reguł zasad eksportu maksymalną częstotliwością.

* Przyczyna   
Eksportowanie zasad, zdefiniowane przez użytkownika nie spełnia wymagany zakres.  

* Rozwiązanie   
Upewnij się, że indeks nie jest już używany i który znajduje się w zakresie od 1 do 5.

* Obejście   
Nie jest wymagane, aby użyć zasad eksportu w woluminach. W związku z tym można pominąć zasad eksportu, całkowicie, jeśli nie musisz mieć reguły zasad eksportu.


<a name="error_24"></a>***Zduplikowana wartość błąd dla obiektu {0}.***   
Ten błąd występuje, gdy zasada eksportu nie jest zdefiniowana za pomocą unikatowego indeksu.  Podczas definiowania zasad eksportu wszystkie reguły zasad eksportu musi mieć unikatowy indeks zakresu od 1 do 5.

* Przyczyna   
Zasady zdefiniowane eksportu nie spełnia wymagań dla reguły zasad eksportu. Konieczne jest posiadanie reguły co najmniej jeden Eksport i pięć reguł zasad eksportu maksymalną częstotliwością.  

* Rozwiązanie   
Upewnij się, że indeks nie jest już używany i że jest z zakresu od 1 do 5.

* Obejście   
Użyj innego indeksu dla tej reguły, który próbujesz skonfigurować.


