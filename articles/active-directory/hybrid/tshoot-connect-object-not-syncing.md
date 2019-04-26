---
title: Rozwiązywanie problemów z obiektem, który nie jest synchronizowany z usługą Azure Active Directory | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z obiektem, który nie jest synchronizowany z usługą Azure Active Directory.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 931865803328189d89c0fbae15caa801c3f7f7c6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60455240"
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-with-azure-active-directory"></a>Rozwiązywanie problemów z obiektem, który nie jest synchronizowany z usługą Azure Active Directory

Obiekt nie jest synchronizowany zgodnie z oczekiwaniami w usłudze Microsoft Azure Active Directory (Azure AD), można go z kilku powodów. Jeśli otrzymasz wiadomość e-mail błędów z usługi Azure AD lub zostanie wyświetlony błąd w usłudze Azure AD Connect Health, zapoznaj się z [Rozwiązywanie problemów z błędami występującymi podczas synchronizacji](tshoot-connect-sync-errors.md) zamiast tego. Ale w przypadku rozwiązywania problemu, gdzie obiekt nie jest w usłudze Azure AD, ten artykuł jest dla Ciebie. Przedstawiono sposób znaleźć błędy w lokalnych składników usługi Azure AD Connect synchronizacji.

>[!IMPORTANT]
>Dla usługi Azure AD Connect wdrożenia przy użyciu wersji 1.1.749.0 lub wyższą od nich, użyj [Rozwiązywanie problemów z zadaniem](tshoot-connect-objectsync.md) w Kreatorze rozwiązywać problemy z obiektu synchronizacji problemów. 

## <a name="synchronization-process"></a>Proces synchronizacji

Zanim firma badać problemy dotyczące synchronizacji, omówimy proces synchronizacji Azure AD Connect:

  ![Diagram procesu synchronizacji Azure AD Connect](./media/tshoot-connect-object-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologia**

* **CS:** Obszar łączników, tabeli w bazie danych
* **MV:** Metaverse, tabeli w bazie danych

### <a name="synchronization-steps"></a>**Kroki synchronizacji**
Proces synchronizacji obejmuje następujące kroki:

1. **Importuj z usługi AD:** Obiekty usługi Active Directory są włączane do Active Directory CS.

2. **Importuj z usługi Azure AD:** Obiekty usługi Azure AD są włączane do usługi Azure AD CS.

3. **Synchronizacja:** Reguły synchronizacji ruchu przychodzącego i reguły synchronizacji ruchu wychodzącego są uruchamiane zgodnie z kolejnością numerze pierwszeństwa, od niższych do wyższych. Aby wyświetlić reguły synchronizacji, przejdź do narzędzia Synchronization Rules Editor z aplikacji klasycznych. Reguły synchronizacji ruchu przychodzącego pobranie danych z CS do MV. Reguły synchronizacji ruchu wychodzącego przenieść dane z MV do CS.

4. **Eksportowanie do usługi AD:** Po zakończeniu synchronizacji, obiekty są eksportowane z Active Directory CS do usługi Active Directory.

5. **Eksportowanie do usługi Azure AD:** Po zakończeniu synchronizacji, obiekty są eksportowane z usługi Azure AD CS z usługą Azure AD.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Aby znaleźć błędy, Przyjrzyj się kilku różnych miejscach, w następującej kolejności:

1. [Dzienniki operacji](#operations) Aby znaleźć błędy identyfikowane przez aparat synchronizacji podczas importowania i synchronizacji.
2. [Obszaru łącznika](#connector-space-object-properties) brakujących obiektów i błędy synchronizacji.
3. [Metaverse](#metaverse-object-properties) można znaleźć problemy związane z danymi.

Rozpocznij [Menedżera usługi synchronizacji](how-to-connect-sync-service-manager-ui.md) przed przystąpieniem do wykonywania tych kroków.

## <a name="operations"></a>Operacje
**Operacji** kartę w Menedżerze usługi synchronizacji jest, gdzie należy rozpocząć rozwiązywanie problemów. Ta karta przedstawia wyniki z ostatnich operacji. 

![Zrzut ekranu z Menedżera usługi synchronizacji, przedstawiający wybraną kartą operacji](./media/tshoot-connect-object-not-syncing/operations.png)  

W górnej połowie **operacji** karcie są wyświetlane wszystkie przebiegi w porządku chronologicznym. Domyślnie operacje logowania zachowuje informacje o ostatnich siedmiu dni, ale ustawienie to można zmienić za pomocą [harmonogramu](how-to-connect-sync-feature-scheduler.md). Wygląd dla dowolnego uruchomienia, które nie są wyświetlane **Powodzenie** stanu. Można zmienić sortowania, klikając nagłówki.

**Stan** kolumna zawiera najważniejsze informacje i pokaże najpoważniejszych problemów dotyczące przebiegu. Poniżej przedstawiono krótkie podsumowanie najbardziej typowych stanów w kolejności priorytetów badania (gdzie * oznacza kilka ciągów możliwy błąd).

| Stan | Komentarz |
| --- | --- |
| Zatrzymano-* |Nie można zakończyć działanie. Może się zdarzyć, na przykład nie działa i nie można skontaktować się z systemu zdalnego. |
| stopped-error-limit |Wystąpiły błędy w więcej niż 5000. Uruchom automatycznie zostało zatrzymane z powodu dużej liczby błędów. |
| ukończone -\*— błędy |Zakończono działanie, ale występują błędy (mniej niż 5000), które należy zbadać. |
| ukończone -\*— ostrzeżenia |Zakończono działanie, ale niektóre dane nie są w oczekiwanym stanie. Jeśli błędy, ten komunikat jest zwykle tylko objawem. Nie należy zbadać ostrzeżenia, dopóki nie zostały rozwiązane błędy. |
| powodzenie |Brak problemów. |

Po zaznaczeniu wiersza na koniec **operacji** karta zostanie zaktualizowany i będzie wyświetlenie szczegółów dotyczących tego uruchomienia. Po stronie lewej tego obszaru, Niewykluczone, że lista pod tytułem **kroku #**. Ta lista jest wyświetlana tylko wtedy, gdy masz wiele domen w lesie i każdej domeny, jest reprezentowane przez krok. Nazwa domeny można znaleźć pod nagłówkiem **partycji**. W obszarze **statystyki synchronizacji** nagłówka, można znaleźć więcej informacji na temat liczby zmian, które zostały przetworzone. Wybierz łącza, aby uzyskać listę zmienionych obiektów. Jeśli masz obiektów z błędami, usuń te błędy są wyświetlane w oknie **błędów synchronizacji** nagłówka.

### <a name="errors-on-the-operations-tab"></a>Błędy na karcie operacje
W przypadku błędów Menedżera usługi synchronizacji zawiera zarówno obiekt w wyniku błędu, jak i błąd jako linki, które zawierają więcej informacji.

![Zrzut ekranu przedstawiający błędy w Menedżerze usługi synchronizacji](./media/tshoot-connect-object-not-syncing/errorsync.png)  
Rozpocznij, wybierając ciąg błędu. (W tym przykładzie jest ciąg błędu **wyzwalane synchronizacji przez reguły — błąd — funkcja**.) Najpierw przedstawiono przegląd obiektu. Aby wyświetlić błędu, wybierz **ślad stosu**. Ślad zawiera informacje na poziomie debugowania błędu.

Kliknij prawym przyciskiem myszy **Wywołaj informacje stosu** kliknij **Zaznacz wszystko**, a następnie wybierz pozycję **kopiowania**. Następnie skopiuj stosu i spójrz na błąd w ulubionym edytorze, takiego jak Notatnik.

Jeśli został zgłoszony błąd **SyncRulesEngine**, informacje stosu wywołań wyświetla najpierw wszystkie atrybuty obiektu. Przewiń w dół, aż zobaczysz nagłówek **InnerException = >**.  

  ![Zrzut ekranu z Menedżera usługi synchronizacji, przedstawiający informacje o błędzie pod nagłówkiem InnerException = >](./media/tshoot-connect-object-not-syncing/errorinnerexception.png)
  
Wiersz po nagłówku wyświetlany błąd. Na poprzednim rysunku błąd jest z reguły synchronizacji niestandardowego utworzonego w firmie Fabrikam.

Jeśli ten błąd nie daje wystarczającą ilość informacji, nadszedł czas na Przyjrzyj się same dane. Wybierz łącze o identyfikatorze obiektu i kontynuować rozwiązywanie problemów [łącznika miejsca obiekt importowany](#cs-import).

## <a name="connector-space-object-properties"></a>Właściwości obiektu obszaru łącznika
Jeśli [ **operacji** ](#operations) karcie są wyświetlane żadne błędy, postępuj zgodnie z obiektu przestrzeni łącznika z usługą Active Directory do środowiska metaverse do usługi Azure AD. W tej ścieżce powinien znajdować się w przypadku problemu.

### <a name="searching-for-an-object-in-the-cs"></a>Wyszukiwanie w odniesieniu do obiektu w CS

W Menedżerze usługi synchronizacji wybierz **łączników**, wybierz łącznik usługi Active Directory i wybierz **wyszukiwania obszaru łącznika**.

W **zakres** wybierz opcję **RDN** umożliwia wyszukiwanie w atrybucie nazwy Pospolitej lub wybierz **nazwy Wyróżniającej lub zakotwiczenia** umożliwia wyszukiwanie **distinguishedName**  atrybutu. Wprowadź wartość, a następnie wybierz **wyszukiwania**. 
 
![Zrzut ekranu przedstawiający wyszukiwanie w obszarze łączników](./media/tshoot-connect-object-not-syncing/cssearch.png)  

Jeśli nie możesz znaleźć obiekt szukasz, być może zostały przefiltrowane z [filtrowania opartego na domenie](how-to-connect-sync-configure-filtering.md#domain-based-filtering) lub [filtrowania na podstawie jednostki Organizacyjnej](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering). Aby sprawdzić, czy filtrowanie skonfigurowane zgodnie z oczekiwaniami, przeczytaj [synchronizacji programu Azure AD Connect: Konfigurowanie filtrowania](how-to-connect-sync-configure-filtering.md).

Inne przydatne wyszukiwania można wykonywać, wybierając łącznik usługi Azure AD. W **zakres** wybierz opcję **oczekującego importowania**, a następnie wybierz pozycję **Dodaj** pole wyboru. To wyszukiwanie zapewnia wszystkie zsynchronizowane obiekty w usłudze Azure AD, która nie może być skojarzony z obiektem w środowisku lokalnym.  

![Zrzut ekranu przedstawiający porzucone w wyszukiwanie w obszarze łączników](./media/tshoot-connect-object-not-syncing/cssearchorphan.png) 
 
Te obiekty zostały utworzone przez inny aparat synchronizacji lub aparatem synchronizacji z inną konfigurację filtrowania. Te obiekty oddzielony nie będą już zarządzane. Przejrzyj tę listę i Rozważ usunięcie tych obiektów za pomocą [usługi Azure AD PowerShell](https://aka.ms/aadposh) polecenia cmdlet.

### <a name="cs-import"></a>Importuj CS
Po otwarciu obiekt CS, istnieje kilka kart u góry. **Zaimportować** karta zawiera dane, które są przygotowywane po imporcie.  

![Zrzut ekranu okna właściwości obiektu obszaru łącznika z wybraną kartą importu](./media/tshoot-connect-object-not-syncing/csobject.png)    

**Stara wartość** kolumna pokazuje, co to jest przechowywane w Connect, a **nową wartość** kolumna pokazuje, co zostało odebrane z systemu źródłowego i nie została jeszcze zastosowana. Jeśli występuje błąd w obiekcie, zmiany nie są przetwarzane.

**Błąd synchronizacji** karta jest widoczna w **właściwości obiektu obszaru łącznika** okna tylko wtedy, gdy występuje problem z obiektem. Aby uzyskać więcej informacji, zobacz instrukcje [Rozwiązywanie problemów z błędami synchronizacji na **operacji** kartę](#errors-on-the-operations-tab).

![Zrzut ekranu przedstawiający kartę błąd synchronizacji, w oknie właściwości obiektu obszaru łącznika](./media/tshoot-connect-object-not-syncing/cssyncerror.png)  

### <a name="cs-lineage"></a>Pochodzenie CS
**Pochodzenie** karcie **właściwości obiektu obszaru łącznika** okno pokazuje, jak obiektu przestrzeni łącznika jest powiązany z obiektu metaverse. Widać, gdy łącznik ostatnio zaimportowana zmiany z połączonego systemu i której reguły są stosowane do wypełniania danych w magazynie metaverse programu.  

![Zrzut ekranu przedstawiający kartę pochodzenie w oknie właściwości obiektu obszaru łącznika](./media/tshoot-connect-object-not-syncing/cslineage.png)  

Na poprzednim rysunku **akcji** kolumnie jest wyświetlana reguła synchronizacji ruchu przychodzącego z akcją **Aprowizowanie**. Wskazuje, że tak długo, jak ma to obiektu przestrzeni łącznika, pozostanie obiektu metaverse. Jeśli na liście reguł synchronizacji zamiast nich pokazują regułę synchronizacji ruchu wychodzącego z **Aprowizowanie** akcji, ten obiekt zostanie usunięty po usunięciu obiektu metaverse.  

![Zrzut ekranu przedstawiający okno pochodzenie, na karcie pochodzenie w oknie właściwości obiektu obszaru łącznika](./media/tshoot-connect-object-not-syncing/cslineageout.png)  

W tym przykładzie widać również w **PasswordSync** kolumny, która może współtworzyć obszaru łącznika dla ruchu przychodzącego zmienia hasło, ponieważ jedna reguła synchronizacji ma wartość **True**. Hasło jest przesyłane do usługi Azure AD za pomocą reguł ruchu wychodzącego.

Z **pochodzenie** kartę, uzyskasz do środowiska metaverse, wybierając [ **właściwości obiektu Metaverse**](#mv-attributes).

### <a name="preview"></a>Wersja zapoznawcza
W lewym dolnym rogu **właściwości obiektu obszaru łącznika** okno jest **Podgląd** przycisku. Wybierz ten przycisk, aby otworzyć **Podgląd** strony, w którym można synchronizować pojedynczy obiekt. Ta strona jest przydatne, jeśli są Rozwiązywanie problemów z niektórych reguł synchronizacji niestandardowych i chcesz zobaczyć wpływ zmiany na pojedynczy obiekt. Możesz wybrać **Full sync** lub **synchronizacja różnicowa**. Możesz również wybrać **Generowanie podglądu**, który przechowuje tylko zmiany w pamięci. Lub wybierz **zatwierdzenia w wersji zapoznawczej**, która aktualizuje metaverse i przygotowuje wszystkie zmiany dotyczące łącznika docelowego miejsca do magazynowania.  

![Zrzut ekranu przedstawiający stronę podglądu z Uruchom Podgląd wybrane](./media/tshoot-connect-object-not-syncing/preview.png)  

W wersji zapoznawczej można sprawdzić obiektu i Zobacz reguły, które stosowane dla przepływu określonego atrybutu.  

![Zrzut ekranu strony Podgląd przepływu atrybutu importowania](./media/tshoot-connect-object-not-syncing/previewresult.png)

### <a name="log"></a>Log
Obok pozycji **Podgląd** przycisku Wybierz **dziennika** przycisk, aby otworzyć **dziennika** strony. Tutaj można zobaczyć stan synchronizacji haseł i historię. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z synchronizacją skrótów haseł z usługą Azure AD Connect sync](tshoot-connect-password-hash-synchronization.md).

## <a name="metaverse-object-properties"></a>Właściwości obiektu Metaverse
Jest to zazwyczaj lepiej rozpocząć wyszukiwanie ze źródła obszaru łącznika usługi Active Directory. Ale można również uruchomić wyszukiwanie w magazynie metaverse.

### <a name="searching-for-an-object-in-the-mv"></a>Wyszukiwanie w odniesieniu do obiektu w MV
W Menedżerze usługi synchronizacji wybierz **wyszukiwanie magazynu Metaverse**, jak na poniższej ilustracji. Utwórz zapytanie, które znasz znajduje się użytkownik. Wyszukiwania typowych atrybutów, takich jak **accountName** (**sAMAccountName**) i **userPrincipalName**. Aby uzyskać więcej informacji, zobacz [wyszukiwanie magazynu Metaverse Menedżera usługi synchronizacji](how-to-connect-sync-service-manager-ui-mvsearch.md).

![Zrzut ekranu z Menedżera usługi synchronizacji, z wybraną kartą wyszukiwanie magazynu Metaverse](./media/tshoot-connect-object-not-syncing/mvsearch.png)  

W **wyniki wyszukiwania** okna, kliknij obiekt.

Jeśli go nie znaleziono obiektu, go nie osiągnęły jeszcze obiektu metaverse. Wyszukaj obiektu w usłudze Active Directory w dalszym ciągu [obszaru łącznika](#connector-space-object-properties). Jeśli okaże się obiekt w obszarze łącznika usługi Active Directory, może być błąd synchronizacji, która blokuje obiekt z pojawi się w magazynie metaverse programu lub może być stosowana filtru określania zakresu reguły synchronizacji.

### <a name="object-not-found-in-the-mv"></a>Nie można odnaleźć obiektu w MV
Jeśli obiekt jest w Active Directory CS, ale nie znajduje się w MV, jest stosowany filtr określania zakresu. Aby zobaczyć filtru określania zakresu, przejdź do menu aplikacji pulpitu, a następnie wybierz **Synchronization Rules Editor**. Filtruj zasady mające zastosowanie do obiektu, dostosowując poniższym filtrze.

  ![Zrzut ekranu z Synchronization Rules Editor, wyświetlanie wyników wyszukiwania reguły synchronizacji ruchu przychodzącego](./media/tshoot-connect-object-not-syncing/syncrulessearch.png)

Wyświetlanie każdą regułę z góry na liście i sprawdź **filtru Scoping**. W następujących filtrów określania zakresu Jeśli **isCriticalSystemObject** wartość null lub wartość FALSE lub jest pusty, znajduje się w zakresie.

  ![Zrzut ekranu przedstawiający filtr określania zakresu w wyszukiwaniu reguła synchronizacji ruchu przychodzącego](./media/tshoot-connect-object-not-syncing/scopingfilter.png)

Przejdź do [importu CS](#cs-import) atrybutu z listy i wyboru filtru, który blokuje obiektu, przechodząc do MV. **Obszaru łącznika** atrybut Lista będzie zawierała tylko atrybuty inną niż null i nie jest pusty. Na przykład jeśli **isCriticalSystemObject** nie będzie widoczny na liście wartość tego atrybutu jest wartość null lub jest pusty.

### <a name="object-not-found-in-the-azure-ad-cs"></a>Nie można odnaleźć obiektu w usłudze Azure AD CS
Jeśli obiekt nie jest obecny w obszarze łącznika usługi Azure AD, ale znajduje się w MV, Przyjrzyj się filtru określania zakresu wychodzących reguł odpowiedniego obszaru łącznika i Dowiedz się, jeśli obiekt jest odfiltrowane, ponieważ [atrybuty MV](#mv-attributes)nie spełnia kryteriów.

Aby wyświetlić wychodzącego filtru określania zakresu, wybierz odpowiednie reguły dla obiektu, dostosowując poniższym filtrze. Wyświetl każdej reguły i spójrz na odpowiednich [atrybut MV](#mv-attributes) wartość.

  ![Zrzut ekranu przedstawiający wyszukiwania reguły synchronizacji ruchu wychodzącego w Synchronization Rules Editor](./media/tshoot-connect-object-not-syncing/outboundfilter.png)


### <a name="mv-attributes"></a>Atrybuty MV
Na **atrybuty** karty, możesz zobaczyć wartości i łączników, które przyczyniły się je.  

![Zrzut ekranu okna właściwości obiektu Metaverse z wybraną kartą atrybuty](./media/tshoot-connect-object-not-syncing/mvobject.png)  

Jeśli obiekt nie synchronizuje, należy odpowiedzieć na następujące pytania dotyczące stanów atrybutu w magazynie metaverse programu:
- Jest to atrybut **cloudFiltered** obecna i ustawiona na **True**? Jeśli tak jest, ma zostały odfiltrowane zgodnie z instrukcjami w [filtrowania na podstawie atrybutu](how-to-connect-sync-configure-filtering.md#attribute-based-filtering).
- Jest to atrybut **sourceAnchor** obecne? Jeśli nie masz topologią lasu zasobów konta? Jeśli obiekt jest identyfikowany jako połączona Skrzynka pocztowa (atrybut **msExchRecipientTypeDetails** ma wartość **2**), **sourceAnchor** jest pochodzącego z lasu za pomocą włączone konto usługi Active Directory. Upewnij się, że konto główne został zaimportowany i prawidłowo zsynchronizowane. Konto główne, muszą być umieszczone między [łączników](#mv-connectors) dla obiektu.

### <a name="mv-connectors"></a>Łączniki MV
**Łączników** karta przedstawia wszystkie spacje łącznika, które mają reprezentacji obiektu. 
 
![Zrzut ekranu okna właściwości obiektu Metaverse z wybraną kartą łączników](./media/tshoot-connect-object-not-syncing/mvconnectors.png)  

Musisz mieć łącznika na:

- Każdy las usługi Active Directory użytkownika jest reprezentowana w. Taka reprezentacja może obejmować **foreignSecurityPrincipals** i **skontaktuj się z pomocą** obiektów.
- Łącznik w usłudze Azure AD.

Jeśli masz Brak łącznika do usługi Azure AD, zapoznaj się z sekcją na [atrybuty MV](#mv-attributes) do Sprawdź kryteria dotyczące inicjowania obsługi administracyjnej do usługi Azure AD.

Z **łączników** można także przejść do karty [obiektu przestrzeni łącznika](#connector-space-object-properties). Zaznacz wiersz, a następnie kliknij przycisk **właściwości**.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [synchronizacji programu Azure AD Connect](how-to-connect-sync-whatis.md).
- Dowiedz się więcej o [tożsamości hybrydowej](whatis-hybrid-identity.md).
