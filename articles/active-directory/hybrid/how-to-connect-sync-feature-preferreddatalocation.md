---
title: 'Usługa Azure AD Connect: konfigurowanie preferowanej lokalizacji danych dla zasobów usługi Office 365'
description: W tym artykule opisano sposób umieszczania zasobów użytkowników usługi Office 365 blisko użytkownika za pomocą synchronizacji usługi Azure Active Directory Connect.
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
ms.date: 11/11/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a71c5328c6fa85f85db4bd7e6103f6470b86d99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258332"
---
# <a name="azure-active-directory-connect-sync-configure-preferred-data-location-for-office-365-resources"></a>Synchronizacja łączy usługi Azure Active Directory: konfigurowanie preferowanej lokalizacji danych dla zasobów usługi Office 365
Celem tego tematu jest omówienie sposobu konfigurowania atrybutu dla preferowanej lokalizacji danych w synchronizacji usługi Azure Active Directory (Azure AD) Connect. Gdy ktoś korzysta z funkcji multi-geo w usłudze Office 365, używasz tego atrybutu do wyznaczania lokalizacji geograficznej danych usługi Office 365 użytkownika. (Terminy *region* i *geo* są używane zamiennie.)

## <a name="enable-synchronization-of-preferred-data-location"></a>Włącz synchronizację preferowanej lokalizacji danych
Domyślnie zasoby usługi Office 365 dla użytkowników znajdują się w tym samym obszarze geograficznym, co dzierżawa usługi Azure AD. Na przykład jeśli dzierżawa znajduje się w Ameryce Północnej, skrzynki pocztowe programu Exchange użytkowników znajdują się również w Ameryce Północnej. Dla międzynarodowej organizacji może to nie być optymalne.

Ustawiając atrybut **preferredDataLocation,** można zdefiniować lokalizację geograficzną użytkownika. Zasoby usługi Office 365 użytkownika, takie jak skrzynka pocztowa i usługa OneDrive, mogą być dostępne w tym samym pliku geograficznym co użytkownik, i nadal mają jedną dzierżawę dla całej organizacji.

> [!IMPORTANT]
> Multi-Geo jest obecnie dostępny dla klientów z aktywną umową Enterprise Agreement i co najmniej 500 subskrypcjami usług Office 365. Aby uzyskać szczegółowe informacje, skontaktuj się z przedstawicielem firmy Microsoft.
>
>

Listę wszystkich danych geograficznych dla usługi Office 365 można znaleźć w miejscu [Gdzie znajdują się Twoje dane?](https://aka.ms/datamaps).

Geos w usłudze Office 365 dostępne dla multi-geo to:

| Obszar geograficzny | preferowanawartość lokalizacja danych |
| --- | --- |
| Azja i Pacyfik | Apc |
| Australia | Aus |
| Kanada | Cna |
| Unia Europejska | EUR |
| Francja | FRA |
| Indie | IND |
| Japonia | JPN |
| Korea | KOR |
| Republika Południowej Afryki | Zaf |
| Zjednoczone Emiraty Arabskie | Są |
| Wielka Brytania | Gbr |
| Stany Zjednoczone | NAM (nam) |

* Jeśli geo nie jest wymieniony w tej tabeli (na przykład Ameryka Południowa), to nie może być używany dla Multi-Geo.

* Nie wszystkie obciążenia usługi Office 365 obsługują ustawienie obszaru geograficznego użytkownika.

### <a name="azure-ad-connect-support-for-synchronization"></a>Obsługa synchronizacji usługi Azure AD Connect

Usługa Azure AD Connect obsługuje synchronizację atrybutu **preferredDataLocation** dla obiektów **użytkownika** w wersji 1.1.524.0 i nowszych. Są to:

* Schemat typu obiektu **Użytkownik** w usłudze Azure AD Connector jest rozszerzony o atrybut **preferredDataLocation.** Atrybut jest typu, ciąg o pojedynczej wartości.
* Schemat typu obiektu **Osoba** w metaverse jest rozszerzony o **atrybut preferredDataLocation.** Atrybut jest typu, ciąg o pojedynczej wartości.

Domyślnie **preferredDataLocation** nie jest włączona do synchronizacji. Ta funkcja jest przeznaczona dla większych organizacji. Schemat usługi Active Directory w systemie Windows Server 2019 ma atrybut **msDS-preferredDataLocation,** którego należy użyć w tym celu. Jeśli schemat usługi Active Directory nie został zaktualizowany i nie można tego zrobić, należy zidentyfikować atrybut, który będzie przechowywał obszar geograficzny usługi Office 365 dla użytkowników. Będzie inaczej dla każdej organizacji.

> [!IMPORTANT]
> Usługa Azure AD umożliwia **preferowanedalokalizacja** w **chmurze Obiekty użytkownika** mają być bezpośrednio skonfigurowane przy użyciu usługi Azure AD PowerShell. Aby skonfigurować ten atrybut na **zsynchronizowanych obiektach użytkownika,** należy użyć usługi Azure AD Connect.

Przed włączeniem synchronizacji:

* Jeśli schemat usługi Active Directory nie został uaktualniony do programu Active Directory do 2019 r., zdecyduj, który lokalny atrybut usługi Active Directory ma być używany jako atrybut źródłowy. Powinien być **typu, jednowartościowy ciąg**.
* Jeśli wcześniej skonfigurowano atrybut **preferredDataLocation** na istniejących **zsynchronizowanych obiektach użytkownika** w usłudze Azure AD przy użyciu usługi Azure AD PowerShell, należy przesłać wsteczne wartości atrybutów do odpowiednich obiektów **użytkownika** w lokalnej usłudze Active Directory.

    > [!IMPORTANT]
    > Jeśli te wartości nie zostaną przeniesione wstecz, usługa Azure AD Connect usunie istniejące wartości atrybutów w usłudze Azure AD po włączeniu synchronizacji dla atrybutu **preferredDataLocation.**

* Skonfiguruj atrybut źródłowy na co najmniej kilku lokalnych obiektach użytkownika usługi Active Directory. Można użyć tego do weryfikacji później.

W poniższych sekcjach przedstawiono kroki umożliwiające synchronizację atrybutu **preferredDataLocation.**

> [!NOTE]
> Kroki są opisane w kontekście wdrożenia usługi Azure AD z topologii jednego lasu i bez reguł synchronizacji niestandardowej. Jeśli masz topologię wielu lasów, skonfigurowane niestandardowe reguły synchronizacji lub serwer przejściowy, należy odpowiednio dostosować te kroki.

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Krok 1: Wyłącz harmonogram synchronizacji i sprawdź, czy nie ma synchronizacji w toku
Aby uniknąć niezamierzonych zmian eksportowanych do usługi Azure AD, upewnij się, że synchronizacja nie ma miejsca, gdy jesteś w trakcie aktualizowania reguł synchronizacji. Aby wyłączyć wbudowany harmonogram synchronizacji:

1. Uruchom sesję programu PowerShell na serwerze usługi Azure AD Connect.
2. Wyłącz zaplanowaną synchronizację, uruchamiając `Set-ADSyncScheduler -SyncCycleEnabled $false`to polecenie cmdlet: .
3. Uruchom **Menedżera usług synchronizacji,** przechodząc do**usługi SYNCHRONIZACJI** **START** > .
4. Wybierz kartę **Operacje** i upewnij się, że nie ma operacji ze *stanem w toku*.

![Zrzut ekranu przedstawiający Menedżera usług synchronizacji](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-refresh-the-schema-for-active-directory"></a>Krok 2: Odśwież schemat usługi Active Directory
Jeśli schemat usługi Active Directory został zaktualizowany do 2019 r., a program Connect został zainstalowany przed rozszerzeniem schematu, pamięć podręczna schematu Connect nie ma zaktualizowanego schematu. Następnie należy odświeżyć schemat z kreatora, aby był on wyświetlany w interfejsie użytkownika.

1. Uruchom kreatora usługi Azure AD Connect z pulpitu.
2. Wybierz opcję **Odśwież schemat katalogu** i kliknij przycisk **Dalej**.
3. Wprowadź poświadczenia usługi Azure AD i kliknij przycisk **Dalej**.
4. Na stronie **Odśwież schemat katalogu** upewnij się, że wszystkie lasy są zaznaczone, a następnie kliknij przycisk **Dalej**.
5. Po zakończeniu zamknij kreatora.

![Zrzut ekranu przedstawiający schemat odświeżania katalogu w kreatorze połączenia](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-refreshschema.png)

## <a name="step-3-add-the-source-attribute-to-the-on-premises-active-directory-connector-schema"></a>Krok 3: Dodawanie atrybutu źródłowego do lokalnego schematu łącznika usługi Active Directory
**Ten krok jest potrzebny tylko wtedy, gdy uruchomisz connect w wersji 1.3.21 lub starszej. Jeśli jesteś na 1.4.18 lub nowsze, a następnie przejść do kroku 5.**  
Nie wszystkie atrybuty usługi Azure AD są importowane do lokalnego obszaru łącznika usługi Active Directory. Jeśli wybrano użycie atrybutu, który nie jest domyślnie synchronizowany, należy go zaimportować. Aby dodać atrybut źródłowy do listy importowanych atrybutów:

1. Wybierz kartę **Łączniki** w Menedżerze usług synchronizacji.
2. Kliknij prawym przyciskiem myszy lokalny łącznik usługi Active Directory i wybierz polecenie **Właściwości**.
3. W wyskakującym oknie dialogowym przejdź do karty **Wybierz atrybuty.**
4. Upewnij się, że atrybut źródłowy wybrany do użycia jest zaznaczony na liście atrybutów. Jeśli atrybut nie jest widoczny, zaznacz pole wyboru **Pokaż wszystko.**
5. Aby zapisać, wybierz **przycisk OK**.

![Zrzut ekranu przedstawiający menedżera usług i właściwości synchronizacji,](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-4-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>Krok 4: Dodawanie **preferowanejlokacji danych** do schematu łącznika usługi Azure AD
**Ten krok jest potrzebny tylko wtedy, gdy uruchomisz connect w wersji 1.3.21 lub starszej. Jeśli jesteś na 1.4.18 lub nowsze, a następnie przejść do kroku 5.**  
Domyślnie **atrybut preferredDataLocation** nie jest importowany do przestrzeni programu Azure AD Connector. Aby dodać go do listy zaimportowanych atrybutów:

1. Wybierz kartę **Łączniki** w Menedżerze usług synchronizacji.
2. Kliknij prawym przyciskiem myszy łącznik usługi Azure AD i wybierz polecenie **Właściwości**.
3. W wyskakującym oknie dialogowym przejdź do karty **Wybierz atrybuty.**
4. Wybierz atrybut **preferredDataLocation** na liście.
5. Aby zapisać, wybierz **przycisk OK**.

![Zrzut ekranu przedstawiający menedżera usług i właściwości synchronizacji,](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-5-create-an-inbound-synchronization-rule"></a>Krok 5: Tworzenie reguły synchronizacji przychodzącej
Reguła synchronizacji przychodzącej umożliwia przepływ wartości atrybutu z atrybutu źródłowego w lokalnej usłudze Active Directory do metaverse.

1. Uruchom **Edytor reguł synchronizacji,** przechodząc do**edytora reguł synchronizacji** **START** > .
2. Ustaw filtr wyszukiwania **Kierunek** jako **Przychodzący**.
3. Aby utworzyć nową regułę przychodzącą, wybierz pozycję **Dodaj nową regułę**.
4. Na karcie **Opis** podaj następującą konfigurację:

    | Atrybut | Wartość | Szczegóły |
    | --- | --- | --- |
    | Nazwa | *Podaj nazwę* | Na przykład "In from AD – User preferredDataLocation" |
    | Opis | *Podaj niestandardowy opis* |  |
    | Podłączony system | *Wybieranie lokalnego łącznika usługi Active Directory* |  |
    | Typ obiektu systemu podłączonego | **Użytkownik** |  |
    | Typ obiektu Metaverse | **Person (Osoba)** |  |
    | Typ łącza | **Dołącz** |  |
    | Pierwszeństwo | *Wybierz liczbę z 1–99* | 1–99 jest zarezerwowany dla reguł synchronizacji niestandardowej. Nie należy wybierać wartości używanej przez inną regułę synchronizacji. |

5. Zachowaj **filtr zakresu pustego,** aby uwzględnić wszystkie obiekty. Może być konieczne dostosowanie filtru zakresu zgodnie z wdrożeniem usługi Azure AD Connect.
6. Przejdź do **karty Transformacja**i zaimplementuj następującą regułę transformacji:

    | Typ przepływu | Atrybut docelowy | Element źródłowy | Złóż wniosek raz | Typ scalania |
    | --- | --- | --- | --- | --- |
    |Direct | preferredDataLocation (lokalizacja preferowana | Wybieranie atrybutu źródłowego | Niezaznaczone | Aktualizacja |

7. Aby utworzyć regułę przychodzącą, wybierz pozycję **Dodaj**.

![Zrzut ekranu przedstawiający regułę tworzenia synchronizacji przychodzącej](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-6-create-an-outbound-synchronization-rule"></a>Krok 6: Tworzenie reguły synchronizacji ruchu wychodzącego
Reguła synchronizacji ruchu wychodzącego umożliwia przepływ wartości atrybutu z metaverse do atrybutu **preferredDataLocation** w usłudze Azure AD:

1. Przejdź do **Edytora reguł synchronizacji**.
2. Ustaw kierunek filtru wyszukiwania **jako** **Wychodzący**.
3. Wybierz **pozycję Dodaj nową regułę**.
4. Na karcie **Opis** podaj następującą konfigurację:

    | Atrybut | Wartość | Szczegóły |
    | ----- | ------ | --- |
    | Nazwa | *Podaj nazwę* | Na przykład "Out to Azure AD — user preferredDataLocation" |
    | Opis | *Podaj opis* ||
    | Podłączony system | *Wybieranie łącznika usługi Azure AD* ||
    | Typ obiektu systemu podłączonego | **Użytkownik** ||
    | Typ obiektu Metaverse | **Person (Osoba)** ||
    | Typ łącza | **Dołącz** ||
    | Pierwszeństwo | *Wybierz liczbę z 1–99* | 1–99 jest zarezerwowany dla reguł synchronizacji niestandardowej. Nie należy wybierać wartości używanej przez inną regułę synchronizacji. |

5. Przejdź do karty **Filtr zakresu** i dodaj jedną grupę filtrów zakresu z dwiema klauzulami:

    | Atrybut | Operator | Wartość |
    | --- | --- | --- |
    | SourceObjectType | Równe | Użytkownik |
    | chmuraMastered | NOTEQUAL (100) | True |

    Filtr zakresu określa, do których obiektów usługi Azure AD jest stosowana ta reguła synchronizacji ruchu wychodzącego. W tym przykładzie używamy tego samego filtru zakresu z reguły synchronizacji OOB (out to Azure AD — User Identity". Zapobiega to stosowaniu reguły synchronizacji do obiektów **użytkownika,** które nie są synchronizowane z lokalnej usługi Active Directory. Może być konieczne dostosowanie filtru zakresu zgodnie z wdrożeniem usługi Azure AD Connect.

6. Przejdź do karty **Transformacja** i zaimplementuj następującą regułę transformacji:

    | Typ przepływu | Atrybut docelowy | Element źródłowy | Złóż wniosek raz | Typ scalania |
    | --- | --- | --- | --- | --- |
    | Direct | preferredDataLocation (lokalizacja preferowana | preferredDataLocation (lokalizacja preferowana | Niezaznaczone | Aktualizacja |

7. Zamknij **dodaj,** aby utworzyć regułę ruchu wychodzącego.

![Zrzut ekranu przedstawiający regułę tworzenia synchronizacji ruchu wychodzącego](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-7-run-full-synchronization-cycle"></a>Krok 7: Uruchom pełny cykl synchronizacji
Ogólnie rzecz biorąc wymagany jest pełny cykl synchronizacji. Dzieje się tak, ponieważ dodano nowe atrybuty do schematu usługi Active Directory i usługi Azure AD Connector i wprowadzono niestandardowe reguły synchronizacji. Sprawdź zmiany przed wyeksportem ich do usługi Azure AD. Aby zweryfikować zmiany, można wykonać następujące kroki, a jednocześnie ręcznie uruchomić kroki, które składają się na pełny cykl synchronizacji.

1. Uruchom **pełne importowanie** w lokalnym łączniku usługi Active Directory:

   1. Przejdź do karty **Operacje** w Menedżerze usług synchronizacji.
   2. Kliknij prawym przyciskiem myszy **lokalny łącznik usługi Active Directory**i wybierz polecenie **Uruchom**.
   3. W oknie dialogowym wybierz pozycję **Pełny import**i wybierz przycisk **OK**.
   4. Poczekaj na zakończenie operacji.

      > [!NOTE]
      > Można pominąć pełne importowanie lokalnego łącznika usługi Active Directory, jeśli atrybut źródłowy jest już uwzględniony na liście zaimportowanych atrybutów. Innymi słowy, nie trzeba było wprowadzać żadnych zmian podczas kroku 2 wcześniej w tym artykule.

2. Uruchom **pełne importowanie** w usłudze Azure AD Connector:

   1. Kliknij prawym przyciskiem myszy łącznik **usługi Azure AD i**wybierz polecenie **Uruchom**.
   2. W oknie dialogowym wybierz pozycję **Pełny import**i wybierz przycisk **OK**.
   3. Poczekaj na zakończenie operacji.

3. Sprawdź zmiany reguły synchronizacji w istniejącym **obiekcie Użytkownik.**

   Atrybut źródłowy z lokalnej usługi Active Directory i **preferredDataLocation** z usługi Azure AD został zaimportowany do każdego odpowiedniego obszaru łącznika. Przed przystąpieniem do pełnego kroku synchronizacji wykonaj podgląd istniejącego **obiektu User** w lokalnym obszarze łącznika usługi Active Directory. Wybrany obiekt powinien być wypełniony atrybutem źródłowym. Pomyślny podgląd z **preferredDataLocation** wypełnione w metaverse jest dobrym wskaźnikiem, że zostały skonfigurowane reguły synchronizacji poprawnie. Aby uzyskać informacje dotyczące wykonywania podglądu, zobacz [Weryfikowanie zmiany](how-to-connect-sync-change-the-configuration.md#verify-the-change).

4. Uruchom **pełną synchronizację** na lokalnym łączniku usługi Active Directory:

   1. Kliknij prawym przyciskiem myszy **lokalny łącznik usługi Active Directory**i wybierz polecenie **Uruchom**.
   2. W oknie dialogowym wybierz pozycję **Pełna synchronizacja**i wybierz przycisk **OK**.
   3. Poczekaj na zakończenie operacji.

5. Sprawdź **oczekujące eksporty** do usługi Azure AD:

   1. Kliknij prawym przyciskiem myszy łącznik **usługi Azure AD i**wybierz polecenie Obszar **łącznika wyszukiwania**.
   2. W oknie dialogowym **Obszar łącznika wyszukiwania:**

        a. Ustaw **zakres** **na Oczekujące na Eksport**.<br>
        b. Zaznacz wszystkie trzy pola wyboru, w tym **Dodaj, Modyfikuj i Usuń**.<br>
        d. Aby wyświetlić listę obiektów ze zmianami do wyeksportowania, wybierz **wyszukaj**. Aby sprawdzić zmiany dla danego obiektu, kliknij go dwukrotnie.<br>
        d. Sprawdź, czy zmiany są oczekiwane.

6. Uruchamianie **eksportu** w usłudze **Azure AD Connector**

   1. Kliknij prawym przyciskiem myszy łącznik **usługi Azure AD i**wybierz polecenie **Uruchom**.
   2. W oknie dialogowym **Uruchamianie łącznika** wybierz pozycję **Eksportuj**i wybierz przycisk **OK**.
   3. Poczekaj na zakończenie operacji.

> [!NOTE]
> Można zauważyć, że kroki nie obejmują pełny krok synchronizacji w usłudze Azure AD Connector lub krok eksportu w usłudze Active Directory Connector. Kroki nie są wymagane, ponieważ wartości atrybutów są przepływające z lokalnej usługi Active Directory tylko do usługi Azure AD.

## <a name="step-8-re-enable-sync-scheduler"></a>Krok 8: Ponowne włączanie harmonogramu synchronizacji
Ponownie włącz wbudowany harmonogram synchronizacji:

1. Rozpocznij sesję programu PowerShell.
2. Ponownie włącz zaplanowaną synchronizację, uruchamiając to polecenie cmdlet:`Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-9-verify-the-result"></a>Krok 9: Sprawdź wynik
Nadszedł czas, aby zweryfikować konfigurację i włączyć ją dla użytkowników.

1. Dodaj geo do wybranego atrybutu użytkownika. Listę dostępnych geos można znaleźć w tej tabeli.  
![Zrzut ekranu przedstawiający atrybut usługi AD dodany do użytkownika](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. Poczekaj, aż atrybut ma zostać zsynchronizowany z usługą Azure AD.
3. Korzystając z programu Exchange Online PowerShell, sprawdź, czy region skrzynki pocztowej został poprawnie ustawiony.  
![Zrzut ekranu przedstawiający program Exchange Online PowerShell](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
Zakładając, że dzierżawa została oznaczona, aby móc korzystać z tej funkcji, skrzynka pocztowa jest przenoszona do właściwego obszaru geograficznego. Można to zweryfikować, patrząc na nazwę serwera, w którym znajduje się skrzynka pocztowa.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o wielogładowej w usłudze Office 365:

* [Sesje Multi-Geo w Ignite](https://aka.ms/MultiGeoIgnite)
* [Wiele geo w usłudze OneDrive](https://aka.ms/OneDriveMultiGeo)
* [Wiele geodezji w usłudze SharePoint Online](https://aka.ms/SharePointMultiGeo)

Dowiedz się więcej o modelu konfiguracji w silniku synchronizacji:

* Dowiedz się więcej o modelu konfiguracji w [opisie aprowizacji deklaratywnej](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Dowiedz się więcej o języku wyrażeń w [opisie wyrażeń deklaratywnej inicjowania obsługi administracyjnej](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).

Tematy przeglądu:

* [Synchronizacja usługi Azure AD Connect: zrozumienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
