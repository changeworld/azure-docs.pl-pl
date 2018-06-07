---
title: Rozwiązywanie problemów z obiektu, której nie można zsynchronizować z usługą Azure AD | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z powodu obiektu nie można zsynchronizować z usługą Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 997269efc017a024f2abbcb6561c951d7957af86
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34594333"
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-to-azure-ad"></a>Rozwiązywanie problemów z obiektu, której nie można zsynchronizować z usługą Azure AD

Jeśli obiekt nie jest synchronizowane zgodnie z oczekiwaniami do usługi Azure AD, może być z kilku przyczyn. Odebrano wiadomość e-mail błędu z usługi Azure AD lub zostanie wyświetlony błąd w Azure AD Connect Health, następnie odczytywane [Rozwiązywanie problemów z błędami eksportu](active-directory-aadconnect-troubleshoot-sync-errors.md) zamiast tego. Ale jeśli rozwiązywania problemu, gdy obiekt nie jest w usłudze Azure AD, wówczas w tym temacie jest dla Ciebie. Przedstawiono sposób znaleźć błędy synchronizacji Azure AD Connect składnika lokalnymi.

>[!IMPORTANT]
>Do wdrożenia usługi Azure Active Directory (AAD) Połącz z wersji <verison> lub nowszą, użyj [Rozwiązywanie problemów z zadań](active-directory-aadconnect-troubleshoot-objectsync.md) w kreatorze, aby rozwiązać problemy z synchronizacją obiektu. 

Aby znaleźć błędy, będą wyglądać na kilku różnych miejscach w następującej kolejności:

1. [Dzienniki operacji](#operations) do znajdowania zidentyfikowane przez aparat synchronizacji podczas importowania i synchronizacji błędy.
2. [Przestrzeni łącznika](#connector-space-object-properties) do znajdowania brakujących obiektów i błędy synchronizacji.
3. [Metaverse](#metaverse-object-properties) do znajdowania problemów związanych z danymi.

Uruchom [Menedżera usługi synchronizacji](active-directory-aadconnectsync-service-manager-ui.md) przed rozpoczęciem tych kroków.

## <a name="operations"></a>Operacje
Na karcie operacje Menedżera usługi synchronizacji jest, gdzie należy zacząć Rozwiązywanie problemów. Na karcie operacje pokazuje wyniki od ostatniej operacji.  
![Menedżera usługi synchronizacji](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/operations.png)  

Górnej połowie zawiera wszystkie elementy w kolejności chronologicznej. Domyślnie operacje logowania zachowuje informacje dotyczące ostatnich siedmiu dni, ale ustawienie to można zmienić z [harmonogramu](active-directory-aadconnectsync-feature-scheduler.md). Chcesz znaleźć dla dowolnego przebiegu pokazywać stanu Powodzenie. Można zmienić sortowania, klikając nagłówki.

**Stan** jest najważniejsze informacje i przedstawia najbardziej poważny problem dla przebiegu. Oto krótkie podsumowanie stanów najczęściej w kolejności priorytetu do sprawdzania, czy (gdzie * wskazać ciągi możliwy błąd kilka).

| Stan | Komentarz |
| --- | --- |
| Zatrzymano-* |Nie można wykonać przebiegu. Na przykład, jeśli nie działa i nie można skontaktować się z systemu zdalnego. |
| stopped-error-limit |Wystąpiły błędy więcej niż 5000. Uruchom automatycznie zostało zatrzymane z powodu dużej liczby błędów. |
| Ukończono -\*— błędy |Przy uruchomieniu ukończone, ale wystąpiły błędy (mniej niż 5000), które należy zbadać. |
| Ukończono -\*— ostrzeżenia |Przy uruchomieniu ukończone, ale niektóre dane nie jest w oczekiwanym stanem. Jeśli masz błędy, następnie ten komunikat jest zwykle tylko objawem. Dopóki usunąć błędy, nie powinien być sprawdzony ostrzeżenia. |
| powodzenie |Brak problemów. |

Po zaznaczeniu wiersza dolnej aktualizuje Pokaż szczegóły uruchomienia. Na lewo od dołu, może mieć listy informacją o tym **krok nr**. Ta lista jest wyświetlana tylko, jeśli masz wiele domen w lesie gdzie każdej domeny jest reprezentowany przez krok. Nazwa domeny znajduje się w pozycji **partycji**. W obszarze **statystyki synchronizacji**, można znaleźć więcej informacji na temat liczby zmian, które zostały przetworzone. Kliknięcie łącza spowoduje wyświetlenie listy zmienionych obiektów. Jeśli masz obiektów z błędami, te błędy są wyświetlane w oknie **błędy synchronizacji**.

### <a name="troubleshoot-errors-in-operations-tab"></a>Rozwiązywanie problemów na karcie operacje
![Menedżera usługi synchronizacji](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/errorsync.png)  
Jeśli masz błędy obiektu błąd i sam błąd są łącza, aby uzyskać więcej informacji.

Uruchomić, klikając ciąg błędu (**wyzwalane synchronizacji — zasada — błąd funkcja** na rysunku). Najpierw przedstawiono przegląd obiektu. Aby wyświetlić rzeczywiste błąd, kliknij przycisk **ślad stosu**. Ślad informacje debugowania poziomu tego błędu.

Kliknąć prawym przyciskiem myszy w **informacje stosu wywołań** wybierz **Zaznacz wszystko**, i **kopiowania**. Następnie można skopiować stosu i przyjrzyj się kod błędu w Twojego ulubionego edytora, takiego jak Notatnik.

* Jeśli błąd jest z **SyncRulesEngine**, a następnie informacje stosu wywołań najpierw zawiera listę wszystkich atrybutów obiektu. Przewiń w dół do momentu wyświetlenia pozycji **InnerException = >**.  
  ![Menedżera usługi synchronizacji](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/errorinnerexception.png)  
  Wiersz po zawiera błąd. W powyższym rysunku błąd pochodzi z utworzony niestandardowy Fabrikam reguły synchronizacji.

Jeśli błąd się nie zapewnia wystarczającą ilość informacji, nadszedł czas, aby przyjrzeć się samych danych. Można kliknąć łącze z identyfikatorem obiektu i kontynuować rozwiązywanie problemów z [łącznika miejsce obiekt importowany](#cs-import).

## <a name="connector-space-object-properties"></a>Właściwości obiektu obszaru łącznika
Jeśli nie masz żadnych błędy znalezione w [operacji](#operations) karcie, a następnie następnym krokiem jest wykonaj obiekt miejsca łącznika usługi Active Directory, do środowiska metaverse, a także do usługi Azure AD. W tej ścieżce powinien znajdować się w przypadku problemu.

### <a name="search-for-an-object-in-the-cs"></a>Wyszukiwanie obiektu w CS

W **Menedżera usługi synchronizacji**, kliknij przycisk **łączniki**, wybierz łącznik usługi Active Directory, a **przestrzeni łącznika wyszukiwania**.

W **zakres**, wybierz pozycję **RDN** (Jeśli chcesz wyszukać w atrybucie CN) lub **nazwa Wyróżniająca lub zakotwiczenia** (Jeśli chcesz wyszukać na atrybut distinguishedName). Wprowadź wartość, a następnie kliknij przycisk **wyszukiwania**.  
![Wyszukiwanie w obszarze łączników](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cssearch.png)  

Jeśli nie można odnaleźć obiektu szukasz, a następnie może spowodowały odfiltrowanie z [filtrowanie oparte na domenie](active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering) lub [filtrowanie na podstawie jednostki Organizacyjnej](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering). Odczyt [skonfigurować filtrowanie](active-directory-aadconnectsync-configure-filtering.md) tematu, aby sprawdzić, czy filtrowanie jest skonfigurowany zgodnie z oczekiwaniami.

Inne przydatne wyszukiwania, należy wybrać łącznik usługi Azure AD w **zakres** wybierz **oczekującego importowania**i wybierz **Dodaj** wyboru. To wyszukiwanie zapewnia wszystkie synchronizowane obiekty w usłudze Azure AD, która nie może być skojarzony z obiektem lokalnym.  
![Łącznik miejsce wyszukiwania oddzielony](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cssearchorphan.png)  
Te obiekty zostały utworzone przez inny aparat synchronizacji lub aparatem synchronizacji z innej konfiguracji filtrowania. Ten widok znajduje się lista **oddzielony** obiektów nie jest już zarządzany. Należy Przejrzyj tę listę i Rozważ usunięcie tych obiektów przy użyciu [programu Azure AD PowerShell](https://aka.ms/aadposh) polecenia cmdlet.

### <a name="cs-import"></a>Importuj CS
Po otwarciu obiektu cs istnieje kilka kart u góry. **Zaimportować** karcie są wyświetlane dane, które są przygotowywane po zaimportowaniu.  
![Obiekt CS](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/csobject.png)    
**Stara wartość** pokazuje, co to jest przechowywane w Connect i **nową wartość** co zostały odebrane z systemu źródłowego i nie została jeszcze zastosowana. Jeśli występuje błąd w obiekcie, zmiany nie są przetwarzane.

**Error**  
![Obiekt CS](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cssyncerror.png)  
**Błąd synchronizacji** karta jest tylko widoczne, jeśli występuje problem z obiektem. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z błędami synchronizacji](#troubleshoot-errors-in-operations-tab).

### <a name="cs-lineage"></a>Elementy powiązane CS
Na karcie elementy powiązane pokazuje, jak obiekt miejsca łącznika jest powiązany z obiektu metaverse. Widać importowane łącznik ostatniej zmiany z podłączonych systemów i reguły stosowane do wypełniania danych w magazynie metaverse.  
![Elementy powiązane CS](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cslineage.png)  
W **akcji** kolumny widoczny jest jeden **przychodzący** reguły synchronizacji z akcją **udostępniania**. Który wskazuje, że tak długo, jak długo ma ten obiekt przestrzeni łącznika, pozostanie obiektu metaverse. Jeśli na liście reguł synchronizacji zamiast tego zawiera reguły synchronizacji w kierunku **wychodzące** i **udostępniania**, wskazuje, czy ten obiekt jest usunięty po usunięciu obiektu metaverse.  
![Menedżera usługi synchronizacji](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cslineageout.png)  
Możesz również sprawdzić w **PasswordSync** kolumny, która może przyczynić się przestrzeni łącznika dla ruchu przychodzącego zmiany hasła, ponieważ jedna reguła synchronizacji ma wartość **True**. To hasło jest następnie wysyłane do usługi Azure AD za pomocą reguł wychodzących.

Na karcie elementy powiązane, możesz uzyskać do środowiska metaverse, klikając [właściwości obiektu Metaverse](#mv-attributes).

W dolnej części wszystkie karty są dwa przyciski: **Podgląd** i **dziennika**.

### <a name="preview"></a>Wersja zapoznawcza
Strona podglądu służy do synchronizowania jednego pojedynczego obiektu. Jest on przydatny, gdy są Rozwiązywanie problemów z niektórych reguł niestandardowych synchronizacji i chcesz zobaczyć efekt zmian na pojedynczy obiekt. Możesz wybrać między **pełnej synchronizacji** i **synchronizacja różnicowa**. Można również wybrać między **Generowanie podglądu**, który przechowuje tylko zmiany w pamięci, i **zatwierdzić Podgląd**, który zaktualizowane metaverse i przygotowuje wszelkie zmiany wprowadzone w docelowej — obszary łączników.  
![Menedżera usługi synchronizacji](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/preview.png)  
Możesz sprawdzić obiekt i reguły, które zostały zastosowane do przepływu określonego atrybutu.  
![Menedżera usługi synchronizacji](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/previewresult.png)

### <a name="log"></a>Log
Strona dziennika jest używana aby zobaczyć stan synchronizacji haseł i historii. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z synchronizacją skrótów haseł](active-directory-aadconnectsync-troubleshoot-password-hash-synchronization.md).

## <a name="metaverse-object-properties"></a>Właściwości obiektu Metaverse
Zazwyczaj lepiej się rozpocząć wyszukiwanie źródła usługi Active Directory [przestrzeni łącznika](#connector-space). Ale można również uruchomić wyszukiwanie od metaverse.

### <a name="search-for-an-object-in-the-mv"></a>Wyszukiwanie obiektu MV
W **Menedżera usługi synchronizacji**, kliknij przycisk **wyszukiwanie Metaverse**. Utwórz zapytanie, które znasz znajduje użytkownika. Atrybuty wspólne, takie jak nazwa konta (sAMAccountName) i userPrincipalName można wyszukiwać. Aby uzyskać więcej informacji, zobacz [wyszukiwanie Metaverse](active-directory-aadconnectsync-service-manager-ui-mvsearch.md).
![Menedżera usługi synchronizacji](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/mvsearch.png)  

W **wyniki wyszukiwania** oknie kliknij obiekt.

Jeśli nie znalazła obiekt, następnie go nie osiągnęła jeszcze metaverse. Kontynuować wyszukiwanie obiektu w usłudze Active Directory [przestrzeni łącznika](#connector-space-object-properties). Może to być błąd z synchronizacji, która blokuje obiekt do środowiska metaverse lub może być stosowany filtr.

### <a name="mv-attributes"></a>Atrybuty MV
Na karcie atrybuty można zobaczyć wartości i łącznika, które przyczyniły się go.  
![Menedżera usługi synchronizacji](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/mvobject.png)  

Jeśli nie można zsynchronizować obiektów, przejrzyj następujące atrybuty w magazynie metaverse:
- Jest to atrybut **cloudFiltered** stanowią i ustawić **true**? Jeśli istnieje, a następnie zostały przefiltrowane według kroki opisane w [na atrybutach filtrowania](active-directory-aadconnectsync-configure-filtering.md#attribute-based-filtering).
- Jest to atrybut **sourceAnchor** obecny? Jeśli nie masz topologią lasu zasobów konta? Jeśli obiekt jest rozpoznawany jako połączona Skrzynka pocztowa (atrybut **msExchRecipientTypeDetails** ma wartość 2), następnie sourceAnchor jest przekazanych przez las z włączonym kontem usługi Active Directory. Upewnij się, został zaimportowany i poprawnie synchronizowane konta głównego. Konta głównego musi być wymienione w [łączniki](#mv-connectors) dla obiekt.

### <a name="mv-connectors"></a>Łączniki MV
Karcie łączników są wyświetlane wszystkie obszary łączników, które ma reprezentacji w postaci obiektu.  
![Menedżera usługi synchronizacji](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/mvconnectors.png)  
Musisz mieć łącznika na:

- Reprezentowaną w każdym lesie usługi Active Directory użytkownika. Taka reprezentacja mogą obejmować foreignSecurityPrincipals i skontaktuj się z pomocą obiektów.
- Łącznik usługi Azure AD.

Jeśli brakuje łącznika do usługi Azure AD, następnie odczytywane [atrybuty MV](#mv-attributes) można zweryfikować kryteria aprowizowany do usługi Azure AD.

Na tej karcie można również przejść do [obiekt miejsca łącznika](#connector-space-object-properties). Wybierz wiersz i kliknij przycisk **właściwości**.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [synchronizacja programu Azure AD Connect](active-directory-aadconnectsync-whatis.md) konfiguracji.

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md).
