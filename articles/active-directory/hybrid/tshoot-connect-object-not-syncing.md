---
title: Rozwiązywanie problemów z obiektem, który nie jest synchronizowany z usługą Azure Active Directory | Dokumenty firmy Microsoft
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253535"
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-with-azure-active-directory"></a>Rozwiązywanie problemów z obiektem, który nie jest synchronizowany z usługą Azure Active Directory

Jeśli obiekt nie jest synchronizowany zgodnie z oczekiwaniami z usługą Microsoft Azure Active Directory (Azure AD), może to być spowodowane kilkoma przyczynami. Jeśli otrzymałeś wiadomość e-mail z błędem z usługi Azure AD lub zostanie wyświetlony błąd w usłudze Azure AD Connect Health, zamiast tego przeczytaj [błędy rozwiązywania problemów podczas synchronizacji.](tshoot-connect-sync-errors.md) Ale jeśli rozwiązujesz problem, w którym obiekt nie znajduje się w usłudze Azure AD, ten artykuł jest dla Ciebie. Opisano w nim, jak znaleźć błędy w lokalnej synchronizacji składnika usługi Azure AD Connect.

>[!IMPORTANT]
>W przypadku wdrażania usługi Azure AD Connect w wersji 1.1.749.0 lub nowszej, użyj [zadania rozwiązywania problemów](tshoot-connect-objectsync.md) w kreatorze, aby rozwiązać problemy z synchronizacją obiektów. 

## <a name="synchronization-process"></a>Proces synchronizacji

Zanim zbadamy problemy z synchronizacją, zapoznajmy się z procesem synchronizacji usługi Azure AD Connect:

  ![Diagram procesu synchronizacji usługi Azure AD Connect](./media/tshoot-connect-object-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologia**

* **CS:** Spacja łącznika, tabela w bazie danych
* **MV:** Metaverse, tabela w bazie danych

### <a name="synchronization-steps"></a>**Kroki synchronizacji**
Proces synchronizacji obejmuje następujące kroki:

1. **Import z usługi AD:** Obiekty usługi Active Directory są wprowadzane do cs usługi Active Directory.

2. **Importowanie z usługi Azure AD:** Obiekty usługi Azure AD są wprowadzane do usługi Azure AD CS.

3. **Synchronizacja:** Reguły synchronizacji ruchu przychodzącego i reguły synchronizacji ruchu wychodzącego są uruchamiane w kolejności liczby pierwszeństwa, od dolnej do wyższej. Aby wyświetlić reguły synchronizacji, przejdź do Edytora reguł synchronizacji z aplikacji klasycznych. Reguły synchronizacji przychodzącej wprowadzają dane z CS do MV. Reguły synchronizacji ruchu wychodzącego przenoszą dane z MV do CS.

4. **Eksport do usługi AD:** Po synchronizacji obiekty są eksportowane z usługi Cs usługi Active Directory do usługi Active Directory.

5. **Eksportowanie do usługi Azure AD:** Po synchronizacji obiekty są eksportowane z usługi Azure AD CS do usługi Azure AD.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Aby znaleźć błędy, spójrz na kilka różnych miejsc, w następującej kolejności:

1. Operacja [rejestruje,](#operations) aby znaleźć błędy zidentyfikowane przez aparat synchronizacji podczas importu i synchronizacji.
2. [Miejsce łącznika](#connector-space-object-properties) w celu znalezienia brakujących obiektów i błędów synchronizacji.
3. [Metaverse,](#metaverse-object-properties) aby znaleźć problemy związane z danymi.

Uruchom [Menedżera usług synchronizacji](how-to-connect-sync-service-manager-ui.md) przed rozpoczęciem tych kroków.

## <a name="operations"></a>Operacje
Karta **Operacje** w programie Service Manager synchronizacji umożliwia rozpoczęcie rozwiązywania problemów. Ta karta pokazuje wyniki z najnowszych operacji. 

![Zrzut ekranu przedstawiający Menedżera usług synchronizacji z wybraną kartą Operacje](./media/tshoot-connect-object-not-syncing/operations.png)  

W górnej połowie karty **Operacje** są wyświetlane wszystkie przebiegi w porządku chronologicznym. Domyślnie dziennik operacji przechowuje informacje o ostatnich siedmiu dniach, ale to ustawienie można zmienić za pomocą [harmonogramu](how-to-connect-sync-feature-scheduler.md). Poszukaj dowolnego uruchomienia, który nie pokazuje stanu **sukcesu.** Sortowanie można zmienić, klikając nagłówki.

Kolumna **Stan** zawiera najważniejsze informacje i pokazuje najpoważniejszy problem dla przebiegu. Oto krótkie podsumowanie najczęstszych stanów w kolejności priorytetu badania (gdzie * wskazuje kilka możliwych ciągów błędów).

| Stan | Komentarz |
| --- | --- |
| zatrzymana** |Nie można ukończyć biegu. Może się tak zdarzyć, na przykład, jeśli system zdalny jest w dół i nie można skontaktować się z nim. |
| limit zatrzymanego błędu |Istnieje ponad 5000 błędów. Przebieg został automatycznie wstrzymany z powodu dużej liczby błędów. |
| zakończone-\*-błędy |Przebieg zakończony, ale istnieją błędy (mniej niż 5000), które powinny być badane. |
| zakończone-\*-ostrzeżenia |Przebieg zakończony, ale niektóre dane nie są w oczekiwanym stanie. Jeśli masz błędy, ten komunikat jest zwykle tylko objawem. Nie badaj ostrzeżeń, dopóki nie zostaną uwzględnione błędy. |
| powodzenie |Żadnych problemów. |

Po wybraniu wiersza w dolnej części karty **Operacje** jest aktualizowana, aby wyświetlić szczegóły tego uruchomienia. Po lewej stronie tego obszaru może znajdować się lista zatytułowana **Krok #**. Ta lista jest wyświetlana tylko wtedy, gdy masz wiele domen w lesie, a każda domena jest reprezentowana przez krok. Nazwę domeny można znaleźć pod nagłówkiem **Partycja**. W nagłówku **Statystyka synchronizacji** można znaleźć więcej informacji na temat liczby przetworzonych zmian. Wybierz łącza, aby uzyskać listę zmienionych obiektów. Jeśli masz obiekty z błędami, te błędy są wyświetlane w nagłówku **Błędy synchronizacji.**

### <a name="errors-on-the-operations-tab"></a>Błędy na karcie Operacje
W przypadku wystąpienia błędów program Menedżer usług synchronizacji pokazuje zarówno obiekt w błędzie, jak i sam błąd jako łącza, które dostarczają więcej informacji.

![Zrzut ekranu przedstawiający błędy w Menedżerze usług synchronizacji](./media/tshoot-connect-object-not-syncing/errorsync.png)  
Zacznij od wybrania ciągu błędu. (Na poprzednim rysunku ciąg błędu jest **wywołany przez sync-rule-error-function.)** Po raz pierwszy przedstawiono przegląd obiektu. Aby wyświetlić rzeczywisty błąd, wybierz **opcję Śledzenie stosu**. Ten ślad zawiera informacje na poziomie debugowania o błędzie.

Kliknij prawym przyciskiem myszy pole **Informacje o stosie połączeń,** kliknij polecenie **Zaznacz wszystko**, a następnie wybierz polecenie **Kopiuj**. Następnie skopiuj stos i spójrz na błąd w ulubionym edytorze, takim jak Notatnik.

Jeśli błąd pochodzi z **SyncRulesEngine**, informacje o stosie wywołań najpierw wyświetla wszystkie atrybuty obiektu. Przewiń w dół, aż zobaczysz nagłówek **InnerException =>**.  

  ![Zrzut ekranu przedstawiający Menedżera usług synchronizacji, przedstawiający informacje o błędzie pod nagłówkiem InnerException =>](./media/tshoot-connect-object-not-syncing/errorinnerexception.png)
  
Wiersz po nagłówku pokazuje błąd. Na powyższej ilustracji błąd pochodzi z niestandardowej reguły synchronizacji utworzonej przez program Fabrikam.

Jeśli błąd nie daje wystarczającej ilości informacji, nadszedł czas, aby spojrzeć na same dane. Zaznacz łącze z identyfikatorem obiektu i kontynuuj rozwiązywanie problemów z [importowanym obiektem przestrzeni łącznika](#cs-import).

## <a name="connector-space-object-properties"></a>Właściwości obiektu obszaru łącznika
Jeśli [**na**](#operations) karcie Operacje nie są wyświetlane żadne błędy, postępuj zgodnie z obiektem spacji łącznika z usługi Active Directory do metaverse do usługi Azure AD. W tej ścieżce należy znaleźć, gdzie jest problem.

### <a name="searching-for-an-object-in-the-cs"></a>Wyszukiwanie obiektu w CS

W Menedżerze usług synchronizacji wybierz **pozycję Łączniki**, wybierz łącznik usługi Active Directory i wybierz pozycję **Obszar łącznika wyszukiwania**.

W polu **Zakres** wybierz **rdn,** jeśli chcesz wyszukać atrybut CN, lub wybierz **pozycję DN lub kotwicę,** gdy chcesz wyszukać atrybut **distinguishedName.** Wprowadź wartość i wybierz **pozycję Wyszukaj**. 
 
![Zrzut ekranu przedstawiający wyszukiwanie przestrzeni łączników](./media/tshoot-connect-object-not-syncing/cssearch.png)  

Jeśli nie znajdziesz szukany obiekt, być może został on odfiltrowany za pomocą [filtrowania opartego na domenie](how-to-connect-sync-configure-filtering.md#domain-based-filtering) lub [filtrowania opartego na UU.](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) Aby sprawdzić, czy filtrowanie jest skonfigurowane zgodnie z oczekiwaniami, przeczytaj [artykuł Synchronizacja usługi Azure AD Connect: Konfigurowanie filtrowania](how-to-connect-sync-configure-filtering.md).

Można wykonać inne przydatne wyszukiwanie, wybierając usługę Azure AD Connector. W polu **Zakres** wybierz pozycję **Oczekujące importowanie**, a następnie zaznacz pole wyboru **Dodaj.** To wyszukiwanie zapewnia wszystkie zsynchronizowane obiekty w usłudze Azure AD, które nie mogą być skojarzone z obiektem lokalnym.  

![Zrzut ekranu przedstawiający sieroty w wyszukiwaniu przestrzeni łączników](./media/tshoot-connect-object-not-syncing/cssearchorphan.png) 
 
Obiekty te zostały utworzone przez inny aparat synchronizacji lub aparat synchronizacji z inną konfiguracją filtrowania. Te obiekty osierocone nie są już zarządzane. Przejrzyj tę listę i rozważ usunięcie tych obiektów przy użyciu poleceń cmdlet [programu Azure AD PowerShell.](https://aka.ms/aadposh)

### <a name="cs-import"></a>Import CS
Po otwarciu obiektu CS u góry znajduje się kilka kart. Karta **Importuj** zawiera dane przemieszczane po zaimportowaniu.  

![Zrzut ekranu przedstawiający okno Właściwości obiektu obszaru łącznika z wybraną kartą Importuj](./media/tshoot-connect-object-not-syncing/csobject.png)    

**Kolumna Stara wartość** pokazuje, co jest obecnie przechowywane w connect, a **kolumna Nowa wartość** pokazuje, co zostało odebrane z systemu źródłowego i nie zostało jeszcze zastosowane. Jeśli występuje błąd w obiekcie, zmiany nie są przetwarzane.

Karta **Błąd synchronizacji** jest widoczna w oknie **Właściwości obiektu obszaru łącznika** tylko wtedy, gdy występuje problem z obiektem. Aby uzyskać więcej informacji, zapoznaj się [z instrukcjami rozwiązywania problemów z błędami synchronizacji na karcie **Operacje** ](#errors-on-the-operations-tab).

![Zrzut ekranu przedstawiający kartę Błąd synchronizacji we właściwościach obiektu obszaru łącznika](./media/tshoot-connect-object-not-syncing/cssyncerror.png)  

### <a name="cs-lineage"></a>Rodowód CS
Karta **Rodowód** w oknie **Właściwości obiektu spacji łącznika** pokazuje, jak obiekt spacji łącznika jest powiązany z obiektem metaverse. Można zobaczyć, kiedy łącznik ostatnio zaimportował zmianę z podłączonego systemu i jakie reguły zostały zastosowane do wypełniania danych w metaverse.  

![Zrzut ekranu przedstawiający kartę Rodowód we właściwościach obiektu obszaru łącznika](./media/tshoot-connect-object-not-syncing/cslineage.png)  

Na powyższej ilustracji kolumna **Akcja** przedstawia regułę synchronizacji przychodzącej z akcją **Provision**. Oznacza to, że tak długo, jak ten obiekt spacji łącznika jest obecny, obiekt metaverse pozostaje. Jeśli na liście reguł synchronizacji zamiast tego jest wyświetlana reguła synchronizacji ruchu wychodzącego z akcją **Provision,** ten obiekt jest usuwany po usunięciu obiektu metaverse.  

![Zrzut ekranu przedstawiający okno rodowód na karcie Rodowód we właściwościach obiektu obszaru łącznika](./media/tshoot-connect-object-not-syncing/cslineageout.png)  

Na powyższej ilustracji można również zobaczyć w kolumnie **PasswordSync,** że przestrzeń łącznika przychodzącego może przyczynić się do zmian w haśle, ponieważ jedna reguła synchronizacji ma wartość **Prawda**. To hasło jest wysyłane do usługi Azure AD za pośrednictwem reguły ruchu wychodzącego.

Na karcie **Rodowód** możesz przejść do metaverse, wybierając polecenie [**Metaverse Object Properties**](#mv-attributes).

### <a name="preview"></a>Wersja zapoznawcza
W lewym dolnym rogu okna **Właściwości obiektu obszaru łącznika** znajduje się przycisk **Podgląd.** Wybierz ten przycisk, aby otworzyć stronę **Podgląd,** na której można zsynchronizować pojedynczy obiekt. Ta strona jest przydatna, jeśli rozwiązujesz niektóre niestandardowe reguły synchronizacji i chcesz zobaczyć wpływ zmiany na pojedynczy obiekt. Możesz wybrać **pełną synchronizację** lub **synchronizację delta**. Można również wybrać **opcję Generuj podgląd,** który zachowuje tylko zmianę w pamięci. Lub wybierz **opcję Zatwierdzj podgląd**, który aktualizuje metaverse i etapy wszystkie zmiany do docelowych przestrzeni łączników.  

![Zrzut ekranu przedstawiający stronę Podgląd z wybraną wybraną wybraną wybraną wybraną wybraną w](./media/tshoot-connect-object-not-syncing/preview.png)  

W podglądzie można sprawdzić obiekt i zobaczyć, która reguła zastosowana dla określonego przepływu atrybutów.  

![Zrzut ekranu przedstawiający stronę Podgląd z wyświetlaniem przepływu atrybutów importu](./media/tshoot-connect-object-not-syncing/previewresult.png)

### <a name="log"></a>Log
Obok przycisku **Podgląd** wybierz przycisk **Zaloguj,** aby otworzyć stronę **Dziennik.** Tutaj możesz zobaczyć stan synchronizacji haseł i historię. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z synchronizacją skrótów haseł z synchronizacją usługi Azure AD Connect](tshoot-connect-password-hash-synchronization.md).

## <a name="metaverse-object-properties"></a>Właściwości obiektu Metaverse
Zazwyczaj lepiej jest rozpocząć wyszukiwanie ze źródła obszaru łącznika usługi Active Directory. Ale można również rozpocząć wyszukiwanie od metaverse.

### <a name="searching-for-an-object-in-the-mv"></a>Wyszukiwanie obiektu w MV
W Menedżerze usług synchronizacji wybierz **metaverse search**, jak na poniższym rysunku. Utwórz kwerendę, która znajduje użytkownika. Wyszukaj typowe atrybuty, takie jak **accountName** (**sAMAccountName**) i **userPrincipalName**. Aby uzyskać więcej informacji, zobacz [Wyszukiwanie Metaverse menedżera usług synchronizacji](how-to-connect-sync-service-manager-ui-mvsearch.md).

![Zrzut ekranu przedstawiający Menedżera usług synchronizacji z wybraną kartą Wyszukiwanie metaverse](./media/tshoot-connect-object-not-syncing/mvsearch.png)  

W oknie **Wyniki wyszukiwania** kliknij obiekt.

Jeśli nie znalazłeś obiektu, nie osiągnął jeszcze metaverse. Kontynuuj wyszukiwanie obiektu w [przestrzeni łączników](#connector-space-object-properties)usługi Active Directory . Jeśli obiekt zostanie znaleziony w przestrzeni łączników usługi Active Directory, może wystąpić błąd synchronizacji, który blokuje dojście obiektu do metaverse lub może zostać zastosowany filtr zakresu reguł synchronizacji.

### <a name="object-not-found-in-the-mv"></a>Nie znaleziono obiektu w MV
Jeśli obiekt znajduje się w cs usługi Active Directory, ale nie jest obecny w MV, stosuje się filtr zakresu. Aby przyjrzeć się filtrowi zakresu, przejdź do menu aplikacji na pulpicie i wybierz pozycję **Edytor reguł synchronizacji**. Filtruj reguły mające zastosowanie do obiektu, dostosowując poniższy filtr.

  ![Zrzut ekranu przedstawiający Edytor reguł synchronizacji z wyszukiwaniem reguł synchronizacji przychodzącej](./media/tshoot-connect-object-not-syncing/syncrulessearch.png)

Wyświetl każdą regułę na liście z góry i sprawdź **filtr Zakresu**. W poniższym filtrze zakresu, jeśli wartość **isCriticalSystemObject** jest null lub FALSE lub pusta, jest w zakresie.

  ![Zrzut ekranu przedstawiający filtr zakresu w wyszukiwaniu reguł synchronizacji przychodzącej](./media/tshoot-connect-object-not-syncing/scopingfilter.png)

Przejdź do listy [atrybutów CS Import](#cs-import) i sprawdź, który filtr blokuje przejście obiektu do MV. Lista **atrybutów Spacja łącznika** będzie wyświetlać tylko atrybuty inne niż null i niepuste. Na przykład jeśli **isCriticalSystemObject** nie pojawi się na liście, wartość tego atrybutu jest null lub puste.

### <a name="object-not-found-in-the-azure-ad-cs"></a>Nie znaleziono obiektu w usłudze Azure AD CS
Jeśli obiekt nie jest obecny w przestrzeni łącznika usługi Azure AD, ale jest obecny w MV, spójrz na filtr zakresu reguł wychodzących odpowiedniego miejsca łącznika i dowiedzieć się, czy obiekt jest odfiltrowany, ponieważ [atrybuty MV](#mv-attributes) nie spełniają kryteriów.

Aby przyjrzeć się filtrowi zakresu ruchu wychodzącego, wybierz odpowiednie reguły dla obiektu, dostosowując filtr poniżej. Wyświetl każdą regułę i przyjrzyj się odpowiedniej wartości [atrybutu MV.](#mv-attributes)

  ![Zrzut ekranu przedstawiający wyszukiwanie reguł synchronizacji ruchu wychodzącego w Edytorze reguł synchronizacji](./media/tshoot-connect-object-not-syncing/outboundfilter.png)


### <a name="mv-attributes"></a>Atrybuty MV
Na karcie **Atrybuty** można zobaczyć wartości i współtworzyć je łączniki.  

![Zrzut ekranu przedstawiający okno Właściwości obiektu Metaverse z wybraną kartą Atrybuty](./media/tshoot-connect-object-not-syncing/mvobject.png)  

Jeśli obiekt nie jest synchronizowany, zadaj następujące pytania dotyczące stanów atrybutów w metaverse:
- Czy atrybut **cloudFiltered** jest obecny i ustawiony na **True?** Jeśli tak jest, został odfiltrowany zgodnie z krokami [filtrowania opartego na atrybutach](how-to-connect-sync-configure-filtering.md#attribute-based-filtering).
- Czy źródło **atrybutuAnchor** jest obecny? Jeśli nie, czy masz topologię lasu zasobów konta? Jeśli obiekt jest identyfikowany jako połączona skrzynka pocztowa (atrybut **msExchRecipientTypeDetails** ma wartość **2),** **sourceAnchor** jest przekazywał przez las z włączonym kontem usługi Active Directory. Upewnij się, że konto główne zostało poprawnie zaimportowane i zsynchronizowane. Konto główne musi być wymienione wśród [łączników](#mv-connectors) obiektu.

### <a name="mv-connectors"></a>Złącza MV
Na karcie **Łączniki** są wyświetlane wszystkie spacje łączników, które mają reprezentację obiektu. 
 
![Zrzut ekranu przedstawiający okno Właściwości obiektu Metaverse z wybraną kartą Łączniki](./media/tshoot-connect-object-not-syncing/mvconnectors.png)  

Powinieneś mieć łącznik do:

- Każdy las usługi Active Directory, w których jest reprezentowany użytkownik. Reprezentacja ta może zawierać **foreignSecurityPrincipals** i **Contact** obiektów.
- Łącznik w usłudze Azure AD.

Jeśli brakuje łącznika do usługi Azure AD, przejrzyj sekcję [atrybutów MV,](#mv-attributes) aby sprawdzić kryteria inicjowania obsługi administracyjnej usługi Azure AD.

Na karcie **Łączniki** można również przejść do [obiektu spacji łączników](#connector-space-object-properties). Zaznacz wiersz i kliknij pozycję **Właściwości**.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [synchronizacji usługi Azure AD Connect](how-to-connect-sync-whatis.md).
- Dowiedz się więcej o [tożsamości hybrydowej](whatis-hybrid-identity.md).
