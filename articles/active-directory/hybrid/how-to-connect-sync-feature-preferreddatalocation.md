---
title: 'Program Azure AD Connect: Konfigurowanie Preferowana lokalizacja danych dla zasobów usługi Office 365'
description: W tym artykule opisano, jak umieścić swoje zasoby użytkowników usługi Office 365 blisko użytkowników za pomocą synchronizacji programu Azure Active Directory Connect.
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
ms.topic: conceptual
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 927987237b51a47d0c8b7c66054842b0a7ff09a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66473029"
---
# <a name="azure-active-directory-connect-sync-configure-preferred-data-location-for-office-365-resources"></a>Usługa Azure Active Directory Connect sync: Konfigurowanie Preferowana lokalizacja danych dla zasobów usługi Office 365
Celem tego tematu jest przeprowadzi Cię przez Konfigurowanie atrybutu Preferowana lokalizacja danych podczas synchronizacji usługi Azure Active Directory (Azure AD) Connect. Gdy ktoś będzie korzystać z możliwości wielu regionów geograficznych w usłudze Office 365 możesz użyć tego atrybutu do wyznaczenia lokalizacja geograficzna użytkownika danych usługi Office 365. (Warunki *region* i *geograficznie* są używane zamiennie.)

## <a name="enable-synchronization-of-preferred-data-location"></a>Włącz synchronizację Preferowana lokalizacja danych
Domyślnie zasobów usług Office 365 dla użytkowników, znajdują się w tej samej lokalizacji geograficznej co dzierżawy usługi Azure AD. Na przykład jeśli Twoja dzierżawa znajduje się w Ameryce Północnej, następnie skrzynek pocztowych programu Exchange użytkownika znajdują się również w Ameryce Północnej. Dla międzynarodowych organizacji to może nie być optymalne.

Przez ustawienie atrybutu **preferredDataLocation**, można zdefiniować geograficznej użytkownika. Możesz mieć użytkownika usługi Office 365 zasoby, takie jak skrzynki pocztowej i OneDrive, w tym samym obszarze geograficznym, jako użytkownik i nadal masz jedną dzierżawę dla całej organizacji.

> [!IMPORTANT]
> Wielu regionów geograficznych jest obecnie dostępny dla klientów z co najmniej 2500 subskrypcji usługi Office 365 Services. Skontaktuj się z przedstawicielem firmy Microsoft, aby uzyskać szczegółowe informacje.
>
>

Lista wszystkich obszarach geograficznych dla usługi Office 365 można znaleźć w [gdzie są Twoje dane znajdujące się?](https://aka.ms/datamaps).

Obszarach geograficznych w usłudze Office 365 dostępne dla wielu regionów geograficznych, są następujące:

| Obszar geograficzny | wartość preferredDataLocation |
| --- | --- |
| Azja i Pacyfik | APC |
| Australia | JEDNOSTKI ALOKACJI |
| Kanada | MOŻNA |
| Unii Europejskiej | EUR |
| Francja | FRA |
| Indie | IND |
| Japonia | JPN |
| Korea | KOR |
| Zjednoczone Królestwo | GBR |
| Stany Zjednoczone | NAZWA |

* Jeśli georegionu nie znajduje się w tej tabeli (na przykład, Ameryka Południowa), następnie go nie można używać dla wielu regionów geograficznych.

* Nie wszystkie obciążenia usługi Office 365 obsługują ustawienia geograficznej użytkownika.

### <a name="azure-ad-connect-support-for-synchronization"></a>Usługa Azure AD Connect — pomoc techniczna dla synchronizacji

Azure AD Connect obsługuje synchronizację **preferredDataLocation** atrybutu dla **użytkownika** obiektów w 1.1.524.0 wersji i nowszych. W szczególności:

* Schemat typu obiektu **użytkownika** w łączniku usługi Azure AD jest rozszerzona, aby uwzględnić **preferredDataLocation** atrybutu. Ten atrybut jest typu ciąg pojedynczej wartości.
* Schemat typu obiektu **osoby** w magazynie metaverse jest rozszerzona, aby uwzględnić **preferredDataLocation** atrybutu. Ten atrybut jest typu ciąg pojedynczej wartości.

Domyślnie **preferredDataLocation** nie jest włączona dla synchronizacji. Ta funkcja jest przeznaczony dla większych organizacji. Należy także zidentyfikować atrybutu do przechowywania geograficznego usługi Office 365 dla użytkowników, ponieważ nie istnieje żadne **preferredDataLocation** atrybutu w usłudze Active Directory w środowisku lokalnym. Będzie to być różne dla każdej organizacji.

> [!IMPORTANT]
> Usługa Azure AD umożliwia **preferredDataLocation** atrybutu na **obiektów użytkowników w chmurze** bezpośrednio skonfigurować przy użyciu programu Azure AD PowerShell. Usługa Azure AD nie zezwala już **preferredDataLocation** atrybutu na **synchronizowane obiekty użytkownika** bezpośrednio skonfigurować przy użyciu programu Azure AD PowerShell. Aby skonfigurować ten atrybut na **synchronizowane obiekty użytkownika**, należy użyć usługi Azure AD Connect.

Przed włączeniem synchronizacji:

* Zdecyduj, atrybut usługi Active Directory w środowisku lokalnym, który ma być używany jako atrybut źródłowy. Powinna być typu, **pojedynczej wartości ciągu**. W krokach, które należy wykonać, jeden z **extensionAttributes** jest używany.
* Jeśli wcześniej skonfigurowano **preferredDataLocation** atrybutu o istniejących **synchronizowane obiekty użytkownika** w usłudze Azure AD przy użyciu programu PowerShell usługi Azure AD, należy najpierw Poprawka usterki systemu wartości atrybutu odpowiadające **użytkownika** obiektów w usłudze Active Directory w środowisku lokalnym.

    > [!IMPORTANT]
    > Jeśli poprawka usterki nie systemu wykonaj te wartości, program Azure AD Connect spowoduje usunięcie istniejącej wartości atrybutów w usłudze Azure AD podczas synchronizacji dla **preferredDataLocation** atrybutu jest włączona.

* Skonfiguruj teraz atrybutu źródłowego na co najmniej kilka obiektów użytkowników usługi Active Directory w środowisku lokalnym. Służy to do weryfikacji później.

W poniższych sekcjach przedstawiono kroki, aby włączyć synchronizację **preferredDataLocation** atrybutu.

> [!NOTE]
> Kroki opisane w ramach wdrożenia usługi Azure AD z topologią obejmujących jeden las i bez synchronizacji niestandardowych reguł. Jeśli masz topologią wielu lasów, reguły synchronizacji niestandardowych skonfigurowany lub masz serwer przejściowy, należy odpowiednio dostosować czynności.

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Krok 1: Wyłącz harmonogram synchronizacji i sprawdzić, czy jest brak synchronizacji w toku
Aby uniknąć niezamierzonych zmian eksportowanych do usługi Azure AD, upewnij się, że synchronizacja nie ma miejsce, gdy trwa aktualizowanie reguł synchronizacji. Aby wyłączyć harmonogram synchronizacji wbudowane:

1. Uruchom sesję programu PowerShell na serwerze programu Azure AD Connect.
2. Wyłącz zaplanowanej synchronizacji, uruchamiając to polecenie cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $false`.
3. Rozpocznij **Menedżera usługi synchronizacji** , przechodząc do **START** > **usługi synchronizacji**.
4. Wybierz **operacji** kartę i upewnij się, brak żadna operacja ze stanem *w toku*.

![Zrzut ekranu Menedżera usługi synchronizacji](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-add-the-source-attribute-to-the-on-premises-active-directory-connector-schema"></a>Krok 2: Dodaj atrybut źródłowy ze schematem łącznika usługi Active Directory w środowisku lokalnym
Nie wszystkie atrybuty usługi Azure AD są importowane do lokalnej przestrzeni łącznika usługi Active Directory. Jeśli wybrano opcję Użyj atrybutu, który nie jest zsynchronizowana domyślnie, należy go zaimportować. Aby dodać atrybut źródłowy do listy atrybutów zaimportowane:

1. Wybierz **łączników** kartę w Menedżerze usługi synchronizacji.
2. Kliknij prawym przyciskiem myszy łącznika lokalnej usługi Active Directory, a następnie wybierz pozycję **właściwości**.
3. W podręcznym oknie dialogowym, przejdź do **wybierz atrybuty** kartę.
4. Upewnij się, że zaznaczone jest pole atrybutu źródłowego wybranego do użycia na liście atrybutów. Jeśli atrybut nie jest widoczne, wybierz **Pokaż wszystko** pole wyboru.
5. Aby zapisać, zaznacz **OK**.

![Zrzut ekranu Synchronization Service Manager i właściwości, okno dialogowe](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-3-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>Krok 3: Dodaj **preferredDataLocation** ze schematem łącznik usługi Azure AD
Domyślnie **preferredDataLocation** atrybut nie został zaimportowany do obszaru łącznika usługi Azure AD. Aby dodać go do listy atrybutów zaimportowane:

1. Wybierz **łączników** kartę w Menedżerze usługi synchronizacji.
2. Kliknij prawym przyciskiem myszy łącznik usługi Azure AD, a następnie wybierz pozycję **właściwości**.
3. W podręcznym oknie dialogowym, przejdź do **wybierz atrybuty** kartę.
4. Wybierz **preferredDataLocation** atrybutu na liście.
5. Aby zapisać, zaznacz **OK**.

![Zrzut ekranu Synchronization Service Manager i właściwości, okno dialogowe](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-4-create-an-inbound-synchronization-rule"></a>Krok 4: Utwórz regułę synchronizacji ruchu przychodzącego
Reguła synchronizacji ruchu przychodzącego zezwala na wartość atrybutu mogą przepływać z atrybutu źródłowego w usłudze Active Directory środowiska lokalnego do obiektu metaverse.

1. Rozpocznij **Synchronization Rules Editor** , przechodząc do **START** > **Synchronization Rules Editor**.
2. Ustaw filtr wyszukiwania **kierunek** jako **przychodzący**.
3. Aby utworzyć nową regułę dla ruchu przychodzącego, wybierz **Dodaj nową regułę**.
4. W obszarze **opis** kartę, należy zapewnić następującą konfigurację:

    | Atrybut | Wartość | Szczegóły |
    | --- | --- | --- |
    | Name (Nazwa) | *Podaj nazwę* | Na przykład "w z usługi AD — preferredDataLocation użytkownika" |
    | Opis | *Podaj opis niestandardowego* |  |
    | Połączonego systemu | *Wybierz łącznik usługi Active Directory w środowisku lokalnym* |  |
    | Połączony System typu obiektu | **Użytkownik** |  |
    | Typ obiektu Metaverse | **Osoby** |  |
    | Typ łącza | **Join** |  |
    | Pierwszeństwo | *Wybierz liczbę z zakresu od 1 – 99* | 1 – 99 jest zarezerwowana dla reguły synchronizacji niestandardowych. Nie wybierz wartość, która jest używana przez inną regułę synchronizacji. |

5. Zachowaj **filtru Scoping** puste, aby uwzględnić wszystkie obiekty. Może być konieczne dostosowanie filtru określania zakresu, zgodnie z wdrożenia usługi Azure AD Connect.
6. Przejdź do **kartę przekształcania**i zaimplementować następującą regułę przekształcania:

    | Typ przepływu | Atrybut docelowy | source | Zastosuj raz | Scal typu |
    | --- | --- | --- | --- | --- |
    |Direct | preferredDataLocation | Wybierz atrybut źródłowy | Niezaznaczone | Aktualizacja |

7. Aby utworzyć regułę dla ruchu przychodzącego, wybierz **Dodaj**.

![Zrzut ekranu opcji Utwórz regułę synchronizacji ruchu przychodzącego](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-5-create-an-outbound-synchronization-rule"></a>Krok 5. Utwórz regułę synchronizacji ruchu wychodzącego
Reguła synchronizacji ruchu wychodzącego zezwala na wartość atrybutu mogą przepływać z metaverse do **preferredDataLocation** atrybutu w usłudze Azure AD:

1. Przejdź do **synchronizacji reguł edytora**.
2. Ustaw filtr wyszukiwania **kierunek** jako **ruchu wychodzącego**.
3. Wybierz **Dodaj nową regułę**.
4. W obszarze **opis** kartę, należy zapewnić następującą konfigurację:

    | Atrybut | Wartość | Szczegóły |
    | ----- | ------ | --- |
    | Name (Nazwa) | *Podaj nazwę* | Na przykład "poziomie w celu preferredDataLocation użytkownika z usługi Azure AD" |
    | Opis | *Podaj opis* ||
    | Połączonego systemu | *Wybierz łącznik usługi Azure AD* ||
    | Połączony System typu obiektu | **Użytkownik** ||
    | Typ obiektu Metaverse | **Osoby** ||
    | Typ łącza | **Join** ||
    | Pierwszeństwo | *Wybierz liczbę z zakresu od 1 – 99* | 1 – 99 jest zarezerwowana dla reguły synchronizacji niestandardowych. Nie wybierz wartość, która jest używana przez inną regułę synchronizacji. |

5. Przejdź do **filtru Scoping** karta i Dodaj pojedynczą grupę filtrów określania zakresu przy użyciu dwóch klauzul:

    | Atrybut | Operator | Wartość |
    | --- | --- | --- |
    | sourceObjectType | RÓWNA SIĘ | Użytkownik |
    | cloudMastered | NOTEQUAL | True |

    Określa filtr określania zakresu, który obiekty usługi Azure AD, że ta reguła synchronizacji ruchu wychodzącego. W tym przykładzie używamy tego samego filtru określania zakresu od "Poza usługą AD — tożsamość użytkownika" reguła synchronizacji OOB (out-of-box). Uniemożliwia reguła synchronizacji jest stosowany do **użytkownika** obiektów, które nie są zsynchronizowane z usługą Active Directory w środowisku lokalnym. Może być konieczne dostosowanie filtru określania zakresu, zgodnie z wdrożenia usługi Azure AD Connect.

6. Przejdź do **przekształcania** kartę i wdrożenie następującą regułę przekształcania:

    | Typ przepływu | Atrybut docelowy | source | Zastosuj raz | Scal typu |
    | --- | --- | --- | --- | --- |
    | Direct | preferredDataLocation | preferredDataLocation | Niezaznaczone | Aktualizacja |

7. Zamknij **Dodaj** do utworzenia reguły ruchu wychodzącego.

![Zrzut ekranu opcji Utwórz regułę synchronizacji ruchu wychodzącego](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-6-run-full-synchronization-cycle"></a>Krok 6: Uruchom pełną synchronizację cyklu
Ogólnie rzecz biorąc cyklu Pełna synchronizacja jest wymagana. Jest to spowodowane dodaje nowe atrybuty usługi Active Directory i schemat łącznika usługi Azure AD, a wprowadzone reguły synchronizacji niestandardowych. Zweryfikować zmiany przed rozpoczęciem eksportowania ich do usługi Azure AD. Następujące kroki można użyć, aby sprawdzić zmiany, ręcznie uruchamiając czynności, które tworzą cyklu pełną synchronizację.

1. Uruchom **pełny import** na łącznika lokalnej usługi Active Directory:

   1. Przejdź do **operacji** kartę w Menedżerze usługi synchronizacji.
   2. Kliknij prawym przyciskiem myszy **środowiska lokalnego łącznika usługi Active Directory**i wybierz **Uruchom**.
   3. W oknie dialogowym wybierz **pełny Import**i wybierz **OK**.
   4. Poczekaj na zakończenie operacji.

      > [!NOTE]
      > Jeśli atrybut źródłowy znajduje się już na liście atrybutów zaimportowane, możesz pominąć pełny import na łącznika lokalnej usługi Active Directory. Oznacza to nie trzeba wprowadzać zmian w kroku 2, w tym artykule.

2. Uruchom **pełny import** w programie Azure AD Connector:

   1. Kliknij prawym przyciskiem myszy **łącznik usługi Azure AD**i wybierz **Uruchom**.
   2. W oknie dialogowym wybierz **pełny Import**i wybierz **OK**.
   3. Poczekaj na zakończenie operacji.

3. Zweryfikuj zmiany reguł synchronizacji na istniejącym **użytkownika** obiektu.

   Atrybut źródłowy z usługi Active Directory w środowisku lokalnym, a **preferredDataLocation** z usługi Azure AD zostały zaimportowane do każdego obszaru odpowiednich łącznika. Przed przejściem do etapu pełną synchronizację, wykonaj (wersja zapoznawcza) na istniejącym **użytkownika** obiektu w przestrzeni łącznika usługi Active Directory w środowisku lokalnym. Obiekt wybrany przez Ciebie powinny mieć atrybut źródłowy wypełnione. Pomyślne podglądu z **preferredDataLocation** wypełnione w magazynie metaverse jest dobry wskaźnik skonfigurowano synchronizację reguł poprawnie. Aby dowiedzieć się, jak to zrobić w wersji zapoznawczej, zobacz [sprawdzić zmiany](how-to-connect-sync-change-the-configuration.md#verify-the-change).

4. Uruchom **pełnej synchronizacji** na lokalny łącznik usługi Active Directory:

   1. Kliknij prawym przyciskiem myszy **środowiska lokalnego łącznika usługi Active Directory**i wybierz **Uruchom**.
   2. W oknie dialogowym wybierz **pełną synchronizację**i wybierz **OK**.
   3. Poczekaj na zakończenie operacji.

5. Sprawdź **oczekujące operacje eksportu** do usługi Azure AD:

   1. Kliknij prawym przyciskiem myszy **łącznik usługi Azure AD**i wybierz **wyszukiwania obszaru łącznika**.
   2. W **wyszukiwania obszaru łącznika** okno dialogowe:

        a. Ustaw **zakres** do **operacja eksportowania oczekująca**.<br>
        b. Zaznacz wszystkie trzy pola wyboru, w tym **Dodawanie, modyfikowanie i usuwanie**.<br>
        c. Zaznacz, aby wyświetlić listę obiektów, ze zmianami, które mają zostać wyeksportowane **wyszukiwania**. Aby sprawdzić zmiany dla danego obiektu, kliknij dwukrotnie obiekt.<br>
        d. Upewnij się, czy zmiany są oczekiwane.

6. Uruchom **wyeksportować** na **łącznik usługi Azure AD**

   1. Kliknij prawym przyciskiem myszy **łącznik usługi Azure AD**i wybierz **Uruchom**.
   2. W **uruchomienia łącznika** okno dialogowe, wybierz opcję **wyeksportować**i wybierz **OK**.
   3. Poczekaj na zakończenie operacji.

> [!NOTE]
> Może się okazać, że kroki nie obejmują kroku Pełna synchronizacja w łączniku usługi Azure AD lub krok eksportu, dla łącznika usługi Active Directory. Kroki nie są wymagane, ponieważ wartości atrybutów przepływają do usługi Azure AD tylko z usługi Active Directory w środowisku lokalnym.

## <a name="step-7-re-enable-sync-scheduler"></a>Krok 7: Ponownie Włącz harmonogram synchronizacji
Ponownie włączyć harmonogram synchronizacji wbudowane:

1. Uruchom sesję programu PowerShell.
2. Ponownie włączyć zaplanowanej synchronizacji, uruchamiając polecenie cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-8-verify-the-result"></a>Krok 8: Sprawdź wynik
Teraz nadszedł czas na sprawdzenie konfiguracji i włącz ją dla użytkowników.

1. Dodaj geo do wybranego atrybutu na koncie użytkownika. Lista dostępnych obszarach geograficznych można znaleźć w poniższej tabeli.  
![Zrzut ekranu przedstawiający atrybutu usługi AD, które dodano do użytkownika](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. Poczekaj, aż atrybutów, które mają być synchronizowane z usługą Azure AD.
3. Za pomocą programu Exchange Online PowerShell, sprawdź, czy region skrzynki pocztowej został poprawnie ustawiony.  
![Zrzut ekranu przedstawiający programu Exchange Online PowerShell](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
Przy założeniu, że aby móc korzystać z tej funkcji został oznaczony dzierżawy, Skrzynka pocztowa jest przenoszony do poprawne lokalizacji geograficznej. Można to sprawdzić przez spojrzenie na nazwę serwera, w którym znajduje się skrzynki pocztowej.
4. Aby zweryfikować, że to ustawienie zostało skuteczne za pośrednictwem wielu skrzynek pocztowych, należy użyć skryptu w [galerii TechNet](https://gallery.technet.microsoft.com/office/PowerShell-Script-to-a6bbfc2e). Ten skrypt zawiera także listę prefiksów serwera centrów usługi Office 365, a które geograficznie znajduje się on w. Może służyć jako odwołanie w poprzednim kroku do Sprawdź lokalizację skrzynki pocztowej.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o wielu regionów geograficznych w usłudze Office 365:

* [Sesje wielu regionów geograficznych na konferencji Ignite](https://aka.ms/MultiGeoIgnite)
* [Wielu regionów geograficznych w usłudze OneDrive](https://aka.ms/OneDriveMultiGeo)
* [Wielu regionów geograficznych w programie SharePoint Online](https://aka.ms/SharePointMultiGeo)

Dowiedz się więcej na temat modelu konfiguracji w aparacie synchronizacji:

* Dowiedz się więcej o modelu konfiguracji w [Aprowizacja Deklaratywna opis](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Dowiedz się więcej o języku wyrażeń w [interpretacji wyrażenia inicjowania obsługi administracyjnej deklaratywne](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).

Tematy poglądowe:

* [Synchronizacja w programie Azure AD Connect: Zrozumienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
