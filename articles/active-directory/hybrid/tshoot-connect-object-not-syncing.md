---
title: Rozwiązywanie problemów z obiektem, który nie jest synchronizowany z usługą Azure AD | Dokumentacja firmy Microsoft
description: Rozwiązywania problemów związanych z obiektu nie jest synchronizowany z usługą Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 4336cabd256e492981e1bbff8d1b3b9e4ef07df1
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55820525"
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-to-azure-ad"></a>Rozwiązywanie problemów z obiektem, który nie jest synchronizowany z usługą Azure AD

Jeśli obiekt nie jest synchronizowany zgodnie z oczekiwaniami z usługą Azure AD, może być z kilku powodów. Jeśli otrzymasz wiadomość e-mail błędów z usługi Azure AD lub zostanie wyświetlony błąd w usłudze Azure AD Connect Health, następnie zapoznaj się z [Rozwiązywanie problemów z błędami eksportu](tshoot-connect-sync-errors.md) zamiast tego. Ale w przypadku rozwiązywania problemu, gdzie obiekt nie jest w usłudze Azure AD ten temat jest dla Ciebie. Przedstawiono sposób znaleźć błędy w synchronizacji Azure AD Connect składnika w środowisku lokalnym.

>[!IMPORTANT]
>Dla usługi Azure Active Directory (AAD) Connect wdrożenia przy użyciu wersji 1.1.749.0 lub wyższą od nich, użyj [Rozwiązywanie problemów z zadaniem](tshoot-connect-objectsync.md) w kreatorze, aby rozwiązać problemy z synchronizacją obiektu. 

## <a name="synchronization-process"></a>Proces synchronizacji

Przed sprawdzeniem problemów z synchronizacją, Przyjrzyjmy się **program Azure AD Connect** w procesie synchronizacji:

  ![Proces synchronizacji programu Azure AD Connect](./media/tshoot-connect-object-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologia**

* **CS:** Obszar łączników tabeli w bazie danych.
* **MV:** Metaverse, tabeli w bazie danych.
* **USŁUGI AD:** Usługa Active Directory
* **USŁUGI AAD:** Usługa Azure Active Directory

### <a name="synchronization-steps"></a>**Kroki synchronizacji**
Proces synchronizacji obejmuje następujące kroki:

1. **Importuj z usługi AD:** **Usługi Active Directory** obiekty są przenoszone do **usług AD CS**.

2. **Importuj z usługi AAD:** **Usługa Azure Active Directory** obiekty są przenoszone do **AAD CS**.

3. **Synchronizacja:** **Reguły synchronizacji ruchu przychodzącego** i **reguły synchronizacji ruchu wychodzącego** są uruchamiane według numerze pierwszeństwa od niższych do wyższych. Aby wyświetlić reguły synchronizacji, możesz przejść do **Synchronization Rules Editor** z aplikacji klasycznych. **Reguły synchronizacji ruchu przychodzącego** zapewnia dodatkowe dane z CS MV. **Reguły synchronizacji ruchu wychodzącego** przenosi dane z MV do CS.

4. **Eksportowanie do usługi AD:** Po uruchomieniu synchronizacji, obiekty są eksportowane z usług AD CS **usługi Active Directory**.

5. **Eksportowanie do usługi AAD:** Po uruchomieniu synchronizacji, obiekty są eksportowane z usługi AAD CS **usługi Azure Active Directory**.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Aby znaleźć błędy, którą chcesz Przyjrzyj się kilku różnych miejscach w następującej kolejności:

1. [Dzienniki operacji](#operations) służące do znajdowania błędów identyfikowane przez aparat synchronizacji podczas importowania i synchronizacji.
2. [Obszaru łącznika](#connector-space-object-properties) służące do znajdowania brakujących obiektów i błędy synchronizacji.
3. [Metaverse](#metaverse-object-properties) służące do znajdowania problemów związanych z danymi.

Rozpocznij [Menedżera usługi synchronizacji](how-to-connect-sync-service-manager-ui.md) przed przystąpieniem do wykonywania tych kroków.

## <a name="operations"></a>Operacje
Na karcie operacje Menedżera usługi synchronizacji jest, gdzie należy rozpocząć rozwiązywanie problemów. Na karcie operacje przedstawia wyniki z ostatnich operacji.  
![Menedżera usługi synchronizacji](./media/tshoot-connect-object-not-syncing/operations.png)  

Górnej połowie pokazuje wszystkie przebiegi w porządku chronologicznym. Domyślnie operacje logowania zachowuje informacje o ostatnich siedmiu dni, ale ustawienie to można zmienić za pomocą [harmonogramu](how-to-connect-sync-feature-scheduler.md). Chcesz wyszukać każde uruchomienie, które nie są wyświetlane stan oznaczający powodzenie. Można zmienić sortowania, klikając nagłówki.

**Stan** kolumna jest najważniejsze informacje i pokazuje najpoważniejszych problemów dotyczące przebiegu. Poniżej przedstawiono krótkie podsumowanie najbardziej typowych stanów w kolejności priorytetów w celu zbadania (gdzie * kilka ciągi błędów można wskazać,).

| Stan | Komentarz |
| --- | --- |
| Zatrzymano-* |Nie można ukończyć działanie. Jeśli na przykład nie działa i nie można skontaktować się z systemu zdalnego. |
| stopped-error-limit |Wystąpiły błędy w więcej niż 5000. Uruchom automatycznie zostało zatrzymane z powodu dużej liczby błędów. |
| ukończone -\*— błędy |Przebieg został ukończony, ale występują błędy (mniej niż 5000), które należy zbadać. |
| ukończone -\*— ostrzeżenia |Uruchom ukończone, ale niektóre dane nie jest w oczekiwanym stanie. Jeśli błędy, następnie ten komunikat jest zwykle tylko objawem. Dopóki nie zostały rozwiązane błędy, nie powinien być sprawdzony ostrzeżenia. |
| powodzenie |Brak problemów. |

Po zaznaczeniu wiersza dolnej aktualizuje umożliwiające wyświetlenie szczegółów uruchomienia. Na końcu z lewej strony dolnego, Niewykluczone, że powiedzenie listy **kroku #**. Ta lista pojawia się tylko, jeśli masz wiele domen w lesie, gdzie każda domena jest reprezentowany przez krok. Nazwa domeny można znaleźć pod nagłówkiem **partycji**. W obszarze **statystyki synchronizacji**, można znaleźć więcej informacji na temat liczby zmian, które zostały przetworzone. Po kliknięciu łącza, aby uzyskać listę zmienionych obiektów. Jeśli masz obiektów z błędami, usuń te błędy są wyświetlane w oknie **błędów synchronizacji**.

### <a name="troubleshoot-errors-in-operations-tab"></a>Rozwiązywanie problemów z błędami w karcie operacje
![Menedżera usługi synchronizacji](./media/tshoot-connect-object-not-syncing/errorsync.png)  
W przypadku błędów obiektu w błąd i błędu, sama są łączy, które zawierają więcej informacji.

Rozpocznij, klikając ciąg błędu (**wyzwalane synchronizacji przez reguły — błąd — funkcja** na ilustracji). Najpierw przedstawiono przegląd obiektu. Aby wyświetlić błędu, kliknij przycisk **ślad stosu**. Ten ślad zawiera poziom informacji debugowania dla błędu.

Kliknięcie prawym przyciskiem myszy w **informacje stosu wywołań** wybierz **Zaznacz wszystko**, i **kopiowania**. Następnie możesz skopiować stosu i przyjrzyj się błędu w ulubionym edytorze, takiego jak Notatnik.

* Jeśli został zgłoszony błąd **SyncRulesEngine**, a następnie informacje stosu wywołań najpierw zawiera listę wszystkich atrybutów obiektu. Przewiń w dół, aż zobaczysz nagłówek **InnerException = >**.  
  ![Menedżera usługi synchronizacji](./media/tshoot-connect-object-not-syncing/errorinnerexception.png)  
  Wiersz po wyświetlany błąd. Na powyższym rysunku błąd jest z utworzony niestandardowy Fabrikam reguły synchronizacji.

Jeśli błąd, sama nie zapewnia wystarczająco dużo informacji, nadszedł czas na Przyjrzyj się same dane. Można kliknąć łącze o identyfikatorze obiektu i kontynuować rozwiązywanie problemów z [łącznika miejsca obiekt importowany](#cs-import).

## <a name="connector-space-object-properties"></a>Właściwości obiektu obszaru łącznika
Jeśli nie masz żadnych błędów znalezionych w [operacji](#operations) kartę, a następnie następnym krokiem jest wykonanie czynności opisanych obiektu przestrzeni łącznika usługi Active Directory, do środowiska metaverse, a także do usługi Azure AD. W tej ścieżce powinien znajdować się w przypadku problemu.

### <a name="search-for-an-object-in-the-cs"></a>Wyszukaj obiektu w CS

W **Menedżera usługi synchronizacji**, kliknij przycisk **łączników**, wybierz łącznik usługi Active Directory, a **wyszukiwania obszaru łącznika**.

W **zakres**, wybierz opcję **RDN** (Jeśli chcesz wyszukać w atrybucie, CN) lub **nazwy Wyróżniającej lub zakotwiczenia** (Jeśli chcesz wyszukać na atrybut distinguishedName). Wprowadź wartość, a następnie kliknij przycisk **wyszukiwania**.  
![Wyszukiwanie w obszarze łączników](./media/tshoot-connect-object-not-syncing/cssearch.png)  

Jeśli obiekt nie zostanie znaleziony szukasz, a następnie może zostały przefiltrowane z [filtrowania opartego na domenie](how-to-connect-sync-configure-filtering.md#domain-based-filtering) lub [filtrowania na podstawie jednostki Organizacyjnej](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering). Odczyt [Konfigurowanie filtrowania](how-to-connect-sync-configure-filtering.md) tematu, aby sprawdzić, czy filtrowanie skonfigurowane zgodnie z oczekiwaniami.

Inne przydatne wyszukiwania jest wybierz łącznik usługi Azure AD, **zakres** wybierz **oczekującego importowania**i wybierz **Dodaj** pola wyboru. To wyszukiwanie zapewnia wszystkie synchronizowane obiekty w usłudze Azure AD, która nie może być skojarzony z obiektem w środowisku lokalnym.  
![Oddzielony wyszukiwania przestrzeni łącznika](./media/tshoot-connect-object-not-syncing/cssearchorphan.png)  
Te obiekty zostały utworzone przez inny aparat synchronizacji lub aparatu synchronizacji przy użyciu różnych konfiguracji filtrowania. Ten widok znajduje się lista **oddzielony** obiekty nie są już zarządzane. Należy przejrzeć tej listy i Rozważ usunięcie tych obiektów za pomocą [usługi Azure AD PowerShell](https://aka.ms/aadposh) polecenia cmdlet.

### <a name="cs-import"></a>Importuj CS
Po otwarciu obiekt cs, istnieje kilka kart u góry. **Zaimportować** karta zawiera dane, które są przygotowywane po imporcie.  
![Obiekt CS](./media/tshoot-connect-object-not-syncing/csobject.png)    
**Stara wartość** pokazuje, co to jest przechowywane w programie Connect i **nową wartość** co zostało odebrane z systemu źródłowego i nie została jeszcze zastosowana. Jeśli występuje błąd w obiekcie, zmiany nie są przetwarzane.

**Error**  
![Obiekt CS](./media/tshoot-connect-object-not-syncing/cssyncerror.png)  
**Błąd synchronizacji** karta jest widoczna, jeśli występuje problem z obiektem tylko. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z błędami synchronizacji](#troubleshoot-errors-in-operations-tab).

### <a name="cs-lineage"></a>Pochodzenie CS
Na karcie pochodzenie pokazuje, jak obiektu przestrzeni łącznika jest powiązany z obiektu metaverse. Możesz zobaczyć importowane łącznik ostatnie zmiany z połączonego systemu i której reguły są stosowane do wypełniania danych w magazynie metaverse.  
![Pochodzenie CS](./media/tshoot-connect-object-not-syncing/cslineage.png)  
W **akcji** kolumny, możesz zobaczyć jest jeden **przychodzący** reguły synchronizacji za pomocą akcji **aprowizacji**. Wskazuje, że tak długo, jak ma to obiektu przestrzeni łącznika, pozostanie obiektu metaverse. Jeśli na liście reguł synchronizacji zamiast nich pokazują reguły synchronizacji w kierunku **ruchu wychodzącego** i **Aprowizowanie**, oznacza to, że ten obiekt zostanie usunięta po usunięciu obiektu metaverse.  
![Menedżera usługi synchronizacji](./media/tshoot-connect-object-not-syncing/cslineageout.png)  
Widoczna jest także w **PasswordSync** kolumny, która może współtworzyć obszaru łącznika dla ruchu przychodzącego zmienia hasło, ponieważ jedna reguła synchronizacji ma wartość **True**. To hasło zostanie przesłany do usługi Azure AD za pomocą reguł ruchu wychodzącego.

Na karcie pochodzenie możesz uzyskać do środowiska metaverse, klikając [właściwości obiektu Metaverse](#mv-attributes).

W dolnej części wszystkie karty są dwa przyciski: **Podgląd** i **dziennika**.

### <a name="preview"></a>Wersja zapoznawcza
Na stronie (wersja zapoznawcza) jest używane do synchronizacji jeden pojedynczy obiekt. Jest to przydatne, jeśli są Rozwiązywanie problemów z niektórych reguł synchronizacji niestandardowych i chcesz zobaczyć wpływ zmiany na pojedynczy obiekt. Możesz wybrać między **Full sync** i **synchronizacja różnicowa**. Możesz również wybrać między **Generowanie podglądu**, który przechowuje tylko zmiany w pamięci, i **zatwierdzenia (wersja zapoznawcza)**, który zaktualizowano metaverse i przygotowuje wszystkie zmiany dotyczące łącznika docelowego miejsca do magazynowania.  
![Menedżera usługi synchronizacji](./media/tshoot-connect-object-not-syncing/preview.png)  
Można sprawdzić i reguły, które są stosowane dla przepływu atrybutu określonego obiektu.  
![Menedżera usługi synchronizacji](./media/tshoot-connect-object-not-syncing/previewresult.png)

### <a name="log"></a>Log
Na stronie dziennik służy aby zobaczyć stan synchronizacji haseł i historię. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z synchronizacją skrótów haseł](tshoot-connect-password-hash-synchronization.md).

## <a name="metaverse-object-properties"></a>Właściwości obiektu Metaverse
Jest to zazwyczaj lepiej rozpocząć wyszukiwanie ze źródła obszaru łącznika usługi Active Directory. Ale można również uruchomić wyszukiwanie w magazynie metaverse.

### <a name="search-for-an-object-in-the-mv"></a>Wyszukaj obiektu w MV
W **Menedżera usługi synchronizacji**, kliknij przycisk **wyszukiwanie magazynu Metaverse**. Utwórz zapytanie, znanych znajduje się użytkownik. Możesz wyszukać atrybuty wspólne, takie jak nazwa konta (sAMAccountName) ani atrybutu userPrincipalName. Aby uzyskać więcej informacji, zobacz [wyszukiwanie magazynu Metaverse](how-to-connect-sync-service-manager-ui-mvsearch.md).
![Menedżera usługi synchronizacji](./media/tshoot-connect-object-not-syncing/mvsearch.png)  

W **wyniki wyszukiwania** okna, kliknij obiekt.

Jeśli go nie znaleziono obiektu, następnie go nie osiągnęły jeszcze obiektu metaverse. Kontynuuj wyszukiwanie obiektu w **usługi Active Directory** [obszaru łącznika](#connector-space-object-properties). Jeśli okaże się obiektu w **usługi Active Directory** przestrzeni łącznika, następnie może być błąd z synchronizacji, która blokuje obiekt do obiektu metaverse lub może być stosowane filtru określania zakresu reguły synchronizacji.

### <a name="object-not-found-in-the-mv"></a>Nie można odnaleźć obiektu w MV
Jeśli obiekt jest w **usługi Active Directory** CS, ale nie znajduje się w MV, a następnie zastosowaniu filtru określania zakresu. 

* Przyjrzyj się filtru określania zakresu przejdź do menu aplikacji pulpitu i kliknij **Synchronization Rules Editor**. Filtruj zasady mające zastosowanie obiektu przez dostosowanie poniższym filtrze.

  ![Wyszukaj reguły synchronizacji ruchu przychodzącego](./media/tshoot-connect-object-not-syncing/syncrulessearch.png)

* Wyświetlanie każdą regułę z góry na liście i sprawdź **filtru Scoping**. W poniższych filtru, określania zakresu, jeśli **isCriticalSystemObject** wartość null lub wartość FALSE lub jest pusty, a następnie znajduje się w zakresie.

  ![Wyszukaj reguły synchronizacji ruchu przychodzącego](./media/tshoot-connect-object-not-syncing/scopingfilter.png)

* Przejdź do [importu CS](#cs-import) atrybutu z listy i wyboru filtru, który blokuje obiektu, aby przejść do MV. Oznacza, km **obszaru łącznika** atrybut Lista będzie zawierała tylko atrybuty inną niż null i nie jest pusty. Na przykład jeśli **isCriticalSystemObject** nie są wyświetlane na liście, a następnie oznacza, że wartość tego atrybutu o wartości null ani być pusta.

### <a name="object-not-found-in-the-aad-cs"></a>Nie można odnaleźć obiektu w CS usługi AAD
Jeśli obiekt nie jest obecny w **obszaru łącznika** z **usługi Azure Active Directory**. Jednak znajduje się on na MV, Przyjrzyj się filtru Scoping **ruchu wychodzącego** reguły odpowiadającego **obszaru łącznika** i sprawdź, jeśli obiekt jest odfiltrowana z powodu [MV atrybuty](#mv-attributes) nie spełnia kryteriów.

* Aby wyświetlić wychodzącego filtru określania zakresu, wybierz odpowiednie reguły dla obiektu, dostosowując poniższym filtrze. Wyświetl każdej reguły i spójrz na odpowiednich [atrybut MV](#mv-attributes) wartość.

  ![Wyszukaj reguły ruchu wychodzącego Synchroniztion](./media/tshoot-connect-object-not-syncing/outboundfilter.png)


### <a name="mv-attributes"></a>Atrybuty MV
Na karcie atrybuty można zobaczyć wartości, a następnie łącznika, które przyczyniły się go.  
![Menedżera usługi synchronizacji](./media/tshoot-connect-object-not-syncing/mvobject.png)  

Jeśli obiekt nie jest synchronizowany, Przyjrzyj się następujące atrybuty w magazynie metaverse programu:
- Jest to atrybut **cloudFiltered** obecna i ustawiona na **true**? Jeśli istnieje, a następnie zostały przefiltrowane zgodnie z instrukcjami w [filtrowaniu na podstawie atrybutu](how-to-connect-sync-configure-filtering.md#attribute-based-filtering).
- Jest to atrybut **sourceAnchor** obecne? Jeśli nie masz topologią lasu zasobów konta? Jeśli obiekt jest identyfikowany jako połączona Skrzynka pocztowa (atrybut **msExchRecipientTypeDetails** ma wartość 2), a następnie sourceAnchor jest pochodzącego z lasu za pomocą konta usługi Active Directory włączone. Upewnij się, że konto główne został zaimportowany i poprawnie synchronizowane. Konto główne musi być wymienione w [łączników](#mv-connectors) dla obiektu.

### <a name="mv-connectors"></a>Łączniki MV
Karta łączników przedstawia wszystkie spacje łącznika, które mają reprezentacji obiektu.  
![Menedżera usługi synchronizacji](./media/tshoot-connect-object-not-syncing/mvconnectors.png)  
Musisz mieć łącznika na:

- Każdy las usługi Active Directory użytkownika jest reprezentowana w. Taka reprezentacja może zawierać foreignSecurityPrincipals i skontaktuj się z obiektów.
- Łącznik w usłudze Azure AD.

Jeśli użytkownik nie ma łącznika do usługi Azure AD, następnie zapoznaj się z [atrybuty MV](#mv-attributes) Aby zweryfikować kryteria aprowizowane do usługi Azure AD.

Ta karta umożliwia również przejść do [obiektu przestrzeni łącznika](#connector-space-object-properties). Zaznacz wiersz, a następnie kliknij przycisk **właściwości**.

## <a name="next-steps"></a>Kolejne kroki
- [Synchronizacja programu Azure AD Connect](how-to-connect-sync-whatis.md).
- [Co to jest tożsamość hybrydowa? ](whatis-hybrid-identity.md).
