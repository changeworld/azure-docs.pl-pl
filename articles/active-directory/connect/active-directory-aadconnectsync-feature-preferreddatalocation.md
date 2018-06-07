---
title: 'Synchronizacja programu Azure Active Directory Connect: Skonfiguruj preferowany danych lokalizację dla możliwości wielu geograficznie w usłudze Office 365 | Dokumentacja firmy Microsoft'
description: Opisuje sposób put zasobami użytkowników usługi Office 365 zbliża się użytkownika z synchronizacji usługi Azure Active Directory Connect.
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
ms.date: 04/16/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 5eae173e02c92bd43faaa9533ce29489d40f1389
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34592929"
---
# <a name="azure-active-directory-connect-sync-configure-preferred-data-location-for-office-365-resources"></a>Synchronizacja programu Azure Active Directory Connect: Skonfiguruj preferowany danych lokalizację dla zasobów usługi Office 365
W tym temacie ma na celu przeprowadzi użytkownika przez proces konfigurowania atrybut lokalizacji preferowanych danych synchronizacji usługi Azure Active Directory (Azure AD) Połącz. Gdy ktoś korzysta z możliwości wielu geograficznie w usłudze Office 365, możesz użyć tego atrybutu do wyznaczenia lokalizacja geograficzna danych użytkowników usługi Office 365. (Warunki *region* i *geograficznie* są używane zamiennie.)

## <a name="enable-synchronization-of-preferred-data-location"></a>Włącz synchronizację danych preferowanych lokalizacji
Domyślnie zasobów usługi Office 365 dla użytkowników znajdują się w tej samej lokalizacji geograficznej jako dzierżawy usługi Azure AD. Na przykład jeśli dzierżawy znajduje się w Ameryce Północnej, następnie skrzynek pocztowych programu Exchange użytkowników znajdują się również w Ameryce Północnej. W przypadku międzynarodowej organizacji to może nie być optymalne.

Ustawienie atrybutu **preferredDataLocation**, można zdefiniować geograficznie użytkownika. Możesz mieć użytkownika usługi Office 365 zasoby, takie jak skrzynek pocztowych i OneDrive, w tej samej lokalizacji geograficznej jako użytkownik i jeszcze jeden dzierżawy dla całej organizacji.

> [!IMPORTANT]
> Multi-geograficzna jest obecnie dostępny dla klientów z co najmniej 5000 subskrypcji usługi Office 365. Skontaktuj się z przedstawicielem firmy Microsoft, aby uzyskać szczegółowe informacje.
>
>

Lista wszystkich obszarach geograficznych dla usługi Office 365 można znaleźć w [danych znajdujących się w przypadku?](https://aka.ms/datamaps).

Obszarach geograficznych w usłudze Office 365 dostępne dla wielu geograficznie są:

| Obszar geograficzny | wartość preferredDataLocation |
| --- | --- |
| Azja i Pacyfik | APC |
| Australia | AUS |
| Kanada | MOŻNA |
| Unia Europejska | EUR |
| Indie | ZNAJDŹ |
| Japonia | JPN |
| Korea | KOR |
| Wielka Brytania | GBR |
| Stany Zjednoczone | NAZWA |

* Jeśli obszar nie znajduje się w tej tabeli (na przykład Ameryka Południowa), następnie go nie może służyć do wielu Geo.
* Indie geograficzna jest dostępna tylko w przypadku klientów z rozliczeń adres i licencji zakupionych w tym z magazynu geograficznie.
* Nie wszystkie usługi Office 365 obciążeń obsługują ustawienia geo użytkownika.

### <a name="azure-ad-connect-support-for-synchronization"></a>Azure AD Connect obsługę synchronizacji

Azure AD Connect obsługuje synchronizacji **preferredDataLocation** atrybutu dla **użytkownika** obiektów w wersji 1.1.524.0 i nowszych. W szczególności:

* Schemat typu obiektu **użytkownika** w programie Azure AD Connector jest rozszerzona, aby uwzględnić **preferredDataLocation** atrybutu. Ten atrybut jest typu ciąg pojedynczej wartości.
* Schemat typu obiektu **osoby** w magazynie metaverse jest rozszerzona, aby uwzględnić **preferredDataLocation** atrybutu. Ten atrybut jest typu ciąg pojedynczej wartości.

Domyślnie **preferredDataLocation** nie jest włączone dla synchronizacji. Ta funkcja jest przeznaczony dla większych organizacji. Również należy określić atrybut do przechowywania geo usługi Office 365 dla użytkowników, ponieważ nie istnieje żadne **preferredDataLocation** w lokalnej usłudze Active Directory. Ma to być inne dla każdej organizacji.

> [!IMPORTANT]
> Azure AD pozwala działowi **preferredDataLocation** atrybutu **obiektów użytkowników w chmurze** bezpośrednio skonfigurować przy użyciu programu Azure AD PowerShell. Usługi Azure AD nie umożliwia już **preferredDataLocation** atrybutu **synchronizowane obiekty użytkownika** bezpośrednio skonfigurować przy użyciu programu Azure AD PowerShell. Aby skonfigurować ten atrybut na **synchronizowane obiekty użytkownika**, należy użyć usługi Azure AD Connect.

Przed włączeniem synchronizacji:

* Zdecyduj, które lokalnymi atrybut usługi Active Directory mają być używane jako atrybut źródłowy. Należy go typu **jednowartościowych ciąg**. W krokach, które wykonaj jedną z **extensionAttributes** jest używany.
* Jeśli wcześniej skonfigurowano **preferredDataLocation** atrybut na istniejący **synchronizowane obiekty użytkownika** w usłudze Azure AD przy użyciu programu PowerShell usługi Azure AD, należy najpierw Poprawka usterki wartości atrybutu do systemu odpowiadającego **użytkownika** obiekty w lokalnej usłudze Active Directory.

    > [!IMPORTANT]
    > Jeśli jednak nie Poprawka usterki systemu te wartości Azure AD Connect usuwa istniejące wartości atrybutu w usłudze Azure AD podczas synchronizacji dla **preferredDataLocation** atrybutu jest włączona.

* Teraz skonfigurować atrybut źródłowy na co najmniej kilka obiektów Active Directory użytkownika lokalnego. Możesz użyć tego weryfikacji później.

W poniższych sekcjach przedstawiono kroki, aby włączyć synchronizację **preferredDataLocation** atrybutu.

> [!NOTE]
> Kroki opisane w ramach wdrożenia usługi Azure AD z topologii z jednym lasem i bez reguł niestandardowych. Jeśli masz topologią wielu lasów, reguły synchronizacji niestandardowych skonfigurowane lub tymczasowej serwera, należy odpowiednio dostosować procedury.

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Krok 1: Wyłącz harmonogramu synchronizacji i sprawdź, że nie ma synchronizacji w toku
Aby uniknąć niezamierzonych zmian eksportowane do usługi Azure AD, upewnij się, że synchronizacja nie ma miejsce, gdy trwa aktualizowanie reguł synchronizacji. Aby wyłączyć harmonogram synchronizacji wbudowany:

1. Uruchom sesję programu PowerShell na serwerze programu Azure AD Connect.
2. Wyłącz zaplanowanej synchronizacji, uruchamiając polecenie cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $false`.
3. Uruchom **Menedżera usługi synchronizacji** , przechodząc do **START** > **usługi synchronizacji**.
4. Wybierz **operacji** , a następnie upewnij się, Brak operacji ze stanem *w toku*.

![Zrzut ekranu Menedżera usługi synchronizacji](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-add-the-source-attribute-to-the-on-premises-active-directory-connector-schema"></a>Krok 2: Dodaj atrybut źródłowy schematu lokalnego łącznika usługi Active Directory
Nie wszystkie atrybuty usługi Azure AD są importowane do lokalnej przestrzeni łącznika usługi Active Directory. Jeśli wybrano opcję Użyj atrybutu, który nie jest domyślnie synchronizowane, następnie należy go zaimportować. Aby dodać atrybut źródłowy do listy importowanych atrybuty:

1. Wybierz **łączniki** kartę Menedżera usługi synchronizacji.
2. Kliknij prawym przyciskiem myszy łącznika lokalnej usługi Active Directory, a następnie wybierz **właściwości**.
3. W wyświetlonym oknie dialogowym, przejdź do **wybierz atrybuty** kartę.
4. Upewnij się, że zaznaczono atrybut źródłowy wybranego do użycia na liście atrybutów. Jeśli atrybut nie jest widoczny, wybierz **Pokaż wszystko** pole wyboru.
5. Aby zapisać, zaznacz **OK**.

![Zrzut ekranu Synchronization Service Manager i właściwości — okno dialogowe](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-3-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>Krok 3: Dodawanie **preferredDataLocation** schematu łącznika usługi Azure AD
Domyślnie **preferredDataLocation** atrybut nie został zaimportowany do przestrzeni łącznika usługi Azure AD. Aby dodać go do listy importowanych atrybutów:

1. Wybierz **łączniki** kartę Menedżera usługi synchronizacji.
2. Kliknij prawym przyciskiem myszy łącznik usługi Azure AD, a następnie wybierz **właściwości**.
3. W wyświetlonym oknie dialogowym, przejdź do **wybierz atrybuty** kartę.
4. Wybierz **preferredDataLocation** atrybutu na liście.
5. Aby zapisać, zaznacz **OK**.

![Zrzut ekranu Synchronization Service Manager i właściwości — okno dialogowe](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-4-create-an-inbound-synchronization-rule"></a>Krok 4: Utwórz regułę synchronizacji ruchu przychodzącego
Reguła synchronizacji ruchu przychodzącego zezwala na wartość atrybutu mogą przepływać z atrybutu źródłowego w lokalnej usłudze Active Directory do środowiska metaverse.

1. Uruchom **Edytor reguł synchronizacji** , przechodząc do **START** > **Edytor reguł synchronizacji**.
2. Ustaw filtr wyszukiwania **kierunek** jako **przychodzący**.
3. Aby utworzyć nową regułę ruchu przychodzącego, wybierz **Dodaj nową regułę**.
4. W obszarze **opis** karcie, podaj następującej konfiguracji:

    | Atrybut | Wartość | Szczegóły |
    | --- | --- | --- |
    | Name (Nazwa) | *Podaj nazwę* | Na przykład "w z usługi Active Directory — preferredDataLocation użytkownika" |
    | Opis | *Podaj opis, niestandardowe* |  |
    | System połączony | *Wybierz łącznik lokalnej usługi Active Directory* |  |
    | Połączony System typu obiektu | **Użytkownika** |  |
    | Typ obiektu Metaverse | **Osoby** |  |
    | Typ łącza | **Dołącz** |  |
    | Pierwszeństwo | *Wybierz liczbę z zakresu od 1 – 99* | 1 – 99 jest zarezerwowana dla reguły synchronizacji niestandardowych. Odbiera wartość, która jest używana przez inną regułę synchronizacji. |

5. Zachowaj **filtru Scoping** puste, aby uwzględnić wszystkie obiekty. Może być konieczne dostosowanie zakresu filtru zgodnie z wdrożenia usługi Azure AD Connect.
6. Przejdź do **tab przekształcenia**i implementuje następującą regułę przekształcania:

    | Typ przepływu | Atrybut docelowy | Element źródłowy | Zastosuj raz | Scal typu |
    | --- | --- | --- | --- | --- |
    |Bezpośrednie | PreferredDataLocation | Wybierz atrybut źródłowy | Unchecked | Aktualizacja |

7. Aby utworzyć regułę ruchu przychodzącego, wybierz **Dodaj**.

![Zrzut ekranu utworzyć regułę synchronizacji ruchu przychodzącego](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-5-create-an-outbound-synchronization-rule"></a>Krok 5: Tworzenie reguła synchronizacji ruchu wychodzącego
Reguła synchronizacji ruchu wychodzącego zezwala na wartość atrybutu mogą przepływać z metaverse do **preferredDataLocation** atrybutu w usłudze Azure AD:

1. Przejdź do **Edytor reguł synchronizacji**.
2. Ustaw filtr wyszukiwania **kierunek** jako **wychodzące**.
3. Wybierz **Dodaj nową regułę**.
4. W obszarze **opis** karcie, podaj następującej konfiguracji:

    | Atrybut | Wartość | Szczegóły |
    | ----- | ------ | --- |
    | Name (Nazwa) | *Podaj nazwę* | Na przykład, "Out do usługi Azure AD — preferredDataLocation użytkownika" |
    | Opis | *Podaj opis* ||
    | System połączony | *Wybierz łącznik usługi Azure AD* ||
    | Połączony System typu obiektu | **Użytkownika** ||
    | Typ obiektu Metaverse | **Osoby** ||
    | Typ łącza | **Dołącz** ||
    | Pierwszeństwo | *Wybierz liczbę z zakresu od 1 – 99* | 1 – 99 jest zarezerwowana dla reguły synchronizacji niestandardowych. Odbiera wartość, która jest używana przez inną regułę synchronizacji. |

5. Przejdź do **filtru Scoping** karta i Dodaj pojedynczej grupy ustalania zakresu filtru z klauzulami dwóch:

    | Atrybut | Operator | Wartość |
    | --- | --- | --- |
    | sourceObjectType | RÓWNOŚCI | Użytkownik |
    | cloudMastered | NOTEQUAL | True |

    Filtr zakresu określa obiekty usługi Azure AD, że ta reguła synchronizacji ruchu wychodzącego. W tym przykładzie używamy tego samego zakresu filtru z "Out do AD — tożsamości użytkownika" reguła synchronizacji OOB (out-of-box). Uniemożliwia reguły synchronizacji są stosowane do **użytkownika** obiektów, które nie są synchronizowane z lokalnej usługi Active Directory. Może być konieczne dostosowanie zakresu filtru zgodnie z wdrożenia usługi Azure AD Connect.

6. Przejdź do **przekształcania** karcie i implementuje następującą regułę przekształcania:

    | Typ przepływu | Atrybut docelowy | Element źródłowy | Zastosuj raz | Scal typu |
    | --- | --- | --- | --- | --- |
    | Bezpośrednie | PreferredDataLocation | PreferredDataLocation | Unchecked | Aktualizacja |

7. Zamknij **Dodaj** Tworzenie reguły ruchu wychodzącego.

![Zrzut ekranu utworzyć regułę synchronizacji ruchu wychodzącego](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-6-run-full-synchronization-cycle"></a>Krok 6: Uruchomienie pełnej synchronizacji cyklu
Ogólnie rzecz biorąc cykl Pełna synchronizacja jest wymagana. To dlatego zostały dodane nowe atrybuty usługi Active Directory i Azure AD Connector schematu i wprowadzić reguły synchronizacji niestandardowych. Sprawdź zmiany przed wykonaniem operacji eksportowania ich do usługi Azure AD. Aby zweryfikować zmiany, ręcznie uruchamiając kroki, które tworzą cyklu pełnej synchronizacji, można użyć następujące kroki.

1. Uruchom **pełny import** na łącznika lokalnej usługi Active Directory:

   1. Przejdź do **operacji** kartę Menedżera usługi synchronizacji.
   2. Kliknij prawym przyciskiem myszy **lokalnego łącznika usługi Active Directory**i wybierz **Uruchom**.
   3. W oknie dialogowym wybierz **pełny Import**i wybierz **OK**.
   4. Poczekaj na ukończenie tej operacji.

    > [!NOTE]
    > Pełny import można pominąć na łącznika lokalnej usługi Active Directory, jeśli atrybut źródłowy jest już na liście atrybutów zaimportowany. Innymi słowy użytkownik nie miał na wprowadzenie zmian w kroku 2, w tym artykule.

2. Uruchom **pełny import** łącznika usługi Azure AD:

   1. Kliknij prawym przyciskiem myszy **łącznika usługi Azure AD**i wybierz **Uruchom**.
   2. W oknie dialogowym wybierz **pełny Import**i wybierz **OK**.
   3. Poczekaj na ukończenie tej operacji.

3. Sprawdź zmian reguły synchronizacji na istniejącym **użytkownika** obiektu.

   Atrybut źródłowy z usługi Active Directory lokalne, i **preferredDataLocation** z usługi Azure AD, zostały zaimportowane do każdej przestrzeni odpowiednich łącznika. Przed wykonaniem kroku Pełna synchronizacja, czy podgląd na istniejącym **użytkownika** obiektu w lokalnej przestrzeni łącznika usługi Active Directory. Obiekt, przez Ciebie powinien mieć atrybut źródłowy wypełnione. Pomyślne podglądu z **preferredDataLocation** w magazynie metaverse jest dobry wskaźnik skonfigurowano synchronizacji zasady poprawnie. Aby uzyskać informacje na temat podglądu, zobacz [zweryfikować zmianę](active-directory-aadconnectsync-change-the-configuration.md#verify-the-change).

4. Uruchom **pełnej synchronizacji** łącznika lokalnej usługi Active Directory:

   1. Kliknij prawym przyciskiem myszy **lokalnego łącznika usługi Active Directory**i wybierz **Uruchom**.
   2. W oknie dialogowym wybierz **pełną synchronizację**i wybierz **OK**.
   3. Poczekaj na ukończenie tej operacji.

5. Sprawdź **oczekujące operacje eksportu** do usługi Azure AD:

   1. Kliknij prawym przyciskiem myszy **łącznika usługi Azure AD**i wybierz **przestrzeni łącznika wyszukiwania**.
   2. W **przestrzeni łącznika wyszukiwania** okno dialogowe:

        a. Ustaw **zakres** do **oczekujących eksportu**.<br>
        b. Zaznacz wszystkie trzy pola wyboru, w tym **dodawania, modyfikowania i usuwania**.<br>
        c. Aby wyświetlić listę obiektów ze zmianami do wyeksportowania, wybierz **wyszukiwania**. Aby sprawdzić zmiany dla danego obiektu, kliknij dwukrotnie obiekt.<br>
        d. Upewnij się, że zmiany zostaną wprowadzone.

6. Uruchom **wyeksportować** na **Azure AD Connector**

   1. Kliknij prawym przyciskiem myszy **łącznika usługi Azure AD**i wybierz **Uruchom**.
   2. W **uruchomić łącznik** okno dialogowe, wybierz opcję **wyeksportować**i wybierz **OK**.
   3. Poczekaj na ukończenie tej operacji.

> [!NOTE]
> Można zauważyć, że kroki nie obejmują krok pełną synchronizację łącznika usługi Azure AD lub wykonać krok eksportowania na łącznika usługi Active Directory. Kroki nie są wymagane, ponieważ wartości atrybutów są przesyłane z lokalnej usługi Active Directory do usługi Azure AD tylko.

## <a name="step-7-re-enable-sync-scheduler"></a>Krok 7: Ponownie włączyć harmonogram synchronizacji
Ponowne włączenie harmonogramu synchronizacji wbudowany:

1. Uruchom sesję programu PowerShell.
2. Ponownie włączyć zaplanowanej synchronizacji, uruchamiając polecenie cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-8-verify-the-result"></a>Krok 8: Sprawdzenie, czy wynik
Teraz nadszedł czas, aby sprawdzić konfigurację i włącz ją dla użytkowników.

1. Dodaj geo do wybranego atrybutu na koncie użytkownika. Lista dostępnych obszarach geograficznych znajdują się w [tej tabeli](#enable-synchronization-of-preferreddatalocation).  
![Zrzut ekranu przedstawiający atrybutu AD dodane do użytkownika](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. Poczekaj, aż ten atrybut do synchronizacji usługi Azure AD.
3. Za pomocą programu Exchange Online PowerShell, sprawdź, czy obszar skrzynki pocztowej został ustawiony prawidłowo.  
![Zrzut ekranu przedstawiający programu Exchange Online PowerShell](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
Zakładając, że aby można było korzystać z tej funkcji został oznaczony dzierżawy, skrzynki pocztowej jest przenoszona do poprawne geo. Można to sprawdzić za spojrzenie na nazwę serwera, w którym znajduje się skrzynki pocztowej.
4. Aby sprawdzić, czy to ustawienie zostało skuteczne za pośrednictwem wielu skrzynek pocztowych, użyj skryptu w [galerii TechNet](https://gallery.technet.microsoft.com/office/PowerShell-Script-to-a6bbfc2e). Ten skrypt ma również listę prefiksów serwera wszystkie usługi Office 365 centrów danych, a które geograficznie znajduje się on w. Może służyć jako odwołanie w poprzednim kroku można zweryfikować lokalizacji skrzynki pocztowej.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat Multi-geograficzną, w usłudze Office 365:

* [Geograficznie wielu sesji w Ignite](https://aka.ms/MultiGeoIgnite)
* [Multi geograficzną, w usłudze OneDrive](https://aka.ms/OneDriveMultiGeo)
* [Multi geograficzną, w programie SharePoint Online](https://aka.ms/SharePointMultiGeo)

Dowiedz się więcej na temat modelu konfiguracji w aparacie synchronizacji:

* Dowiedz się więcej o model konfiguracji w [Aprowizacją deklaratywną opis](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
* Dowiedz się więcej o języku wyrażenia w [opis deklaratywne inicjowania obsługi administracyjnej wyrażenia](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).

Omówienie tematy:

* [Synchronizacja programu Azure AD Connect: zrozumienie i dostosowywanie synchronizacji](active-directory-aadconnectsync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md)
