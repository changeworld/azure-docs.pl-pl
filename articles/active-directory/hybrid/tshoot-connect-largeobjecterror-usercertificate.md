---
title: Usługa Azure AD Connect — błędy largeobject spowodowane atrybutem userCertificate | Dokumenty firmy Microsoft
description: W tym temacie przedstawiono kroki korygowania dla błędów LargeObject spowodowanych przez atrybut userCertificate.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 146ad5b3-74d9-4a83-b9e8-0973a19828d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: c851b5ef024e6584e6f8c93995208b08a91fbb60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "62095493"
---
# <a name="azure-ad-connect-sync-handling-largeobject-errors-caused-by-usercertificate-attribute"></a>Synchronizacja usługi Azure AD Connect: obsługa błędów LargeObject spowodowanych przez atrybut userCertificate

Usługa Azure AD wymusza maksymalny limit **15** wartości certyfikatów w atrybucie **userCertificate.** Jeśli usługa Azure AD Connect eksportuje obiekt o wartościach więcej niż 15 do usługi Azure AD, usługa Azure AD zwraca błąd **LargeObject** z komunikatem:

>*"Obiekt aprowizacji jest za duży. Przytnij liczbę wartości atrybutów w tym obiekcie. Operacja zostanie ponowiona w następnym cyklu synchronizacji..."*

Błąd LargeObject może być spowodowany przez inne atrybuty usługi AD. Aby potwierdzić, że jest rzeczywiście spowodowane przez userCertificate atrybut, należy sprawdzić względem obiektu albo w lokalnej ad lub w [menedżerze synchronizacji Metaverse Search](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-mvsearch).

Aby uzyskać listę obiektów w dzierżawie z błędami LargeObject, należy użyć jednej z następujących metod:

 * Jeśli dzierżawa jest włączona dla usługi Azure AD Connect Health do synchronizacji, można odwołać się do [raportu o błędach synchronizacji.](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-sync)
 
 * Wiadomość e-mail z powiadomieniem o błędach synchronizacji katalogów, która jest wysyłana na końcu każdego cyklu synchronizacji, zawiera listę obiektów z błędami LargeObject. 
 * Karta [Operacje programu Synchronizacja usługi Service Manager](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-operations) wyświetla listę obiektów z błędami LargeObject po kliknięciu najnowszej operacji Eksportuj do usługi Azure AD.
 
## <a name="mitigation-options"></a>Opcje łagodzenia zmiany klimatu
Dopóki błąd LargeObject nie zostanie rozwiązany, inne zmiany atrybutu do tego samego obiektu nie mogą być eksportowane do usługi Azure AD. Aby rozwiązać ten błąd, można rozważyć następujące opcje:

 * Uaktualnij usługę Azure AD Connect do kompilacji 1.1.524.0 lub później. W usłudze Azure AD Connect kompilacji 1.1.524.0 reguły synchronizacji out-of-box zostały zaktualizowane, aby nie eksportować atrybuty userCertificate i userSMIMECertificate, jeśli atrybuty mają więcej niż 15 wartości. Szczegółowe informacje na temat uaktualniania usługi Azure AD Connect można znaleźć w artykule [Azure AD Connect: Uaktualnienie z poprzedniej wersji do najnowszej.](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version)

 * Zaimplementuj **regułę synchronizacji wychodzącej** w usłudze Azure AD Connect, która eksportuje **wartość null zamiast rzeczywistych wartości dla obiektów o wartościach więcej niż 15 certyfikatów**. Ta opcja jest odpowiednia, jeśli nie wymaga się żadnych wartości certyfikatu, które mają być eksportowane do usługi Azure AD dla obiektów z więcej niż 15 wartości. Szczegółowe informacje na temat implementacji tej reguły synchronizacji można znaleźć w następnej sekcji [Implementowanie reguły synchronizacji w celu ograniczenia eksportu atrybutu userCertificate](#implementing-sync-rule-to-limit-export-of-usercertificate-attribute).

 * Zmniejsz liczbę wartości certyfikatów w lokalnym obiekcie AD (15 lub mniej) przez usunięcie wartości, które nie są już używane przez organizację. Jest to odpowiednie, jeśli nadęcie atrybutu jest spowodowane wygasłymi lub nieużywanych certyfikatów. Skrypt programu [PowerShell dostępny w tym miejscu](https://gallery.technet.microsoft.com/Remove-Expired-Certificates-0517e34f) ułatwia znajdowanie, tworzenie kopii zapasowych i usuwanie wygasłych certyfikatów w lokalnej układzie AD. Przed usunięciem certyfikatów zaleca się sprawdzenie ich u administratorów infrastruktury kluczy publicznych w organizacji.

 * Skonfiguruj usługę Azure AD Connect, aby wykluczyć atrybut userCertificate z eksportowania do usługi Azure AD. Ogólnie rzecz biorąc nie zaleca się tej opcji, ponieważ atrybut może być używany przez usługi Microsoft Online Services w celu włączenia określonych scenariuszy. W szczególności:
    * Atrybut userCertificate w obiekcie User jest używany przez klientów usługi Exchange Online i Outlook do podpisywania i szyfrowania wiadomości. Aby dowiedzieć się więcej o tej funkcji, zapoznaj się z artykułem [S/MIME dotyczącymi podpisywania i szyfrowania wiadomości](https://technet.microsoft.com/library/dn626158(v=exchg.150).aspx).

    * Atrybut userCertificate w obiekcie Komputer jest używany przez usługę Azure AD, aby umożliwić lokalnym urządzeniom lokalnym połączonym z domeną system Windows 10 łączenie się z usługą Azure AD. Aby dowiedzieć się więcej o tej funkcji, zapoznaj się [z artykułem Łączenie urządzeń przyłączonych do domeny z usługą Azure AD dla środowiska systemu Windows 10](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy).

## <a name="implementing-sync-rule-to-limit-export-of-usercertificate-attribute"></a>Implementowanie reguły synchronizacji w celu ograniczenia eksportu atrybutu userCertificate
Aby rozwiązać błąd LargeObject spowodowany przez atrybut userCertificate, można zaimplementować regułę synchronizacji wychodzącej w usłudze Azure AD Connect, która eksportuje **wartość null zamiast rzeczywistych wartości dla obiektów o wartościach więcej niż 15 certyfikatów**. W tej sekcji opisano kroki wymagane do zaimplementowania reguły synchronizacji dla obiektów **użytkownika.** Kroki można dostosować do **kontaktów** i **komputer** obiektów.

> [!IMPORTANT]
> Eksportowanie wartości null powoduje usunięcie wartości certyfikatów wcześniej eksportowanych pomyślnie do usługi Azure AD.

Kroki można podsumować jako:

1. Wyłącz harmonogram synchronizacji i sprawdź, czy nie jest synchronizacja w toku.
3. Znajdź istniejącą regułę synchronizacji wychodzącej dla atrybutu userCertificate.
4. Utwórz wymaganą regułę synchronizacji wychodzącej.
5. Sprawdź nową regułę synchronizacji na istniejącym obiekcie z błędem LargeObject.
6. Zastosuj nową regułę synchronizacji do pozostałych obiektów z błędem LargeObject.
7. Sprawdź, czy nie ma żadnych nieoczekiwanych zmian oczekujących na wyeksportowanie do usługi Azure AD.
8. Eksportuj zmiany do usługi Azure AD.
9. Włącz ponownie harmonogram synchronizacji.

### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Krok 1. Wyłącz harmonogram synchronizacji i sprawdź, czy nie ma synchronizacji w toku
Upewnij się, że synchronizacja nie ma miejsca, gdy jesteś w trakcie implementowania nowej reguły synchronizacji, aby uniknąć niezamierzonych zmian eksportowanych do usługi Azure AD. Aby wyłączyć wbudowany harmonogram synchronizacji:
1. Uruchom sesję programu PowerShell na serwerze usługi Azure AD Connect.

2. Wyłącz zaplanowaną synchronizację, uruchamiając polecenie cmdlet:`Set-ADSyncScheduler -SyncCycleEnabled $false`

> [!Note]
> Powyższe kroki mają zastosowanie tylko do nowszych wersji (1.1.xxx.x) usługi Azure AD Connect z wbudowanym harmonogramem. Jeśli używasz starszych wersji (1.0.xxx.x) usługi Azure AD Connect, która używa harmonogramu zadań systemu Windows lub używasz własnego harmonogramu niestandardowego (nie często) do wyzwalania okresowej synchronizacji, należy je odpowiednio wyłączyć.

1. Uruchom **Menedżera usług synchronizacji,** przechodząc do START → Usługa synchronizacji.

1. Przejdź do karty **Operacje** i upewnij się, że nie ma żadnej operacji, której stan jest *"w toku".*

### <a name="step-2-find-the-existing-outbound-sync-rule-for-usercertificate-attribute"></a>Krok 2. Znajdowanie istniejącej reguły synchronizacji wychodzącej dla atrybutu userCertificate
Powinna istnieć istniejąca reguła synchronizacji, która jest włączona i skonfigurowana do eksportowania atrybutu userCertificate dla obiektów użytkownika do usługi Azure AD. Znajdź tę regułę synchronizacji, aby dowiedzieć się, że jej **konfiguracja filtru pierwszeństwa** i **zakresu:**

1. Uruchom **Edytor reguł synchronizacji,** przechodząc do START → Edytor reguł synchronizacji.

2. Skonfiguruj filtry wyszukiwania z następującymi wartościami:

    | Atrybut | Wartość |
    | --- | --- |
    | Kierunek |**Wychodzący** |
    | Typ obiektu MV |**Person (Osoba)** |
    | Łącznik |*nazwa łącznika usługi Azure AD* |
    | Typ obiektu łącznika |**Użytkownika** |
    | Atrybut MV |**userCertificate** |

3. Jeśli używasz reguł synchronizacji OOB (out-of-box) do łącznika usługi Azure AD do eksportowania atrybutu userCertficiate dla obiektów użytkownika, należy przywrócić regułę *"Out to AAD — User ExchangeOnline".*
4. Zanotuj wartość **pierwszeństwa** tej reguły synchronizacji.
5. Wybierz regułę synchronizacji i kliknij przycisk **Edytuj**.
6. W oknie podręcznym *"Edytuj potwierdzenie reguły zarezerwowanej"* kliknij przycisk **Nie**. (Nie martw się, nie zamierzamy wprowadzać żadnych zmian w tej regule synchronizacji).
7. Na ekranie edycji wybierz kartę **Filtr zakresu.**
8. Zanotuj konfigurację filtru zakresu. Jeśli używasz reguły synchronizacji OOB, powinna istnieć dokładnie **jedna grupa filtrów zakresu zawierająca dwie klauzule,** w tym:

    | Atrybut | Operator | Wartość |
    | --- | --- | --- |
    | SourceObjectType | Równe | Użytkownik |
    | chmuraMastered | NOTEQUAL (100) | True |

### <a name="step-3-create-the-outbound-sync-rule-required"></a>Krok 3. Tworzenie wymaganej reguły synchronizacji wychodzącej
Nowa reguła synchronizacji musi mieć ten sam **filtr zakresu** i **wyższy priorytet** niż istniejąca reguła synchronizacji. Gwarantuje to, że nowa reguła synchronizacji ma zastosowanie do tego samego zestawu obiektów co istniejąca reguła synchronizacji i zastępuje istniejącą regułę synchronizacji dla atrybutu userCertificate. Aby utworzyć regułę synchronizacji:
1. W Edytorze reguł synchronizacji kliknij przycisk **Dodaj nową regułę.**
2. W **zakładce Opis**podaj następującą konfigurację:

    | Atrybut | Wartość | Szczegóły |
    | --- | --- | --- |
    | Nazwa | *Podaj nazwę* | Np., *"Out to AAD - Niestandardowe zastąpienie dla userCertificate"* |
    | Opis | *Podaj opis* | Na przykład *"Jeśli atrybut userCertificate ma więcej niż 15 wartości, eksportuj wartość NULL."* |
    | Podłączony system | *Wybieranie łącznika usługi Azure AD* |
    | Typ obiektu systemu podłączonego | **Użytkownika** | |
    | Typ obiektu Metaverse | **Osoba** | |
    | Typ łącza | **Dołącz** | |
    | Pierwszeństwo | *Wybierz liczbę z 1 do 99* | Wybrana liczba nie może być używana przez żadną istniejącą regułę synchronizacji i ma niższą wartość (a zatem wyższy priorytet) niż istniejąca reguła synchronizacji. |

3. Przejdź do karty **Filtr zakresu** i zaimplementuj ten sam filtr zakresu, z który korzysta istniejąca reguła synchronizacji.
4. Pomiń kartę **Reguły dołączania.**
5. Przejdź do karty **Przekształcenia,** aby dodać nową transformację przy użyciu następującej konfiguracji:

    | Atrybut | Wartość |
    | --- | --- |
    | Typ przepływu |**Wyrażenie** |
    | Atrybut docelowy |**userCertificate** |
    | Atrybut źródłowy |*Użyj następującego wyrażenia:*`IIF(IsNullOrEmpty([userCertificate]), NULL, IIF((Count([userCertificate])> 15),AuthoritativeNull,[userCertificate]))` |
    
6. Kliknij przycisk **Dodaj,** aby utworzyć regułę synchronizacji.

### <a name="step-4-verify-the-new-sync-rule-on-an-existing-object-with-largeobject-error"></a>Krok 4. Weryfikowanie nowej reguły synchronizacji istniejącego obiektu za pomocą błędu LargeObject
Ma to na celu sprawdzenie, czy utworzona reguła synchronizacji działa poprawnie na istniejącym obiekcie AD z błędem LargeObject przed zastosowaniem go do innych obiektów:
1. Przejdź do karty **Operacje** w Menedżerze usług synchronizacji.
2. Wybierz najnowszą operację Eksportuj do usługi Azure AD i kliknij jeden z obiektów z błędami LargeObject.
3.  Na ekranie podręcznym Właściwości obiektu obszaru łącznika kliknij przycisk **Podgląd.**
4. Na ekranie podręcznym Podgląd wybierz pozycję **Pełna synchronizacja** i kliknij pozycję **Podgląd zatwierdzania**.
5. Zamknij ekran Podgląd i ekran Właściwości obiektu obszaru łącznika.
6. Przejdź do karty **Łączniki** w Menedżerze usług synchronizacji.
7. Kliknij prawym przyciskiem myszy łącznik **usługi Azure AD** i wybierz polecenie **Uruchom...**
8. W wyskakującym okienku Uruchom łącznik wybierz krok **Eksportuj** i kliknij przycisk **OK**.
9. Poczekaj na export do usługi Azure AD, aby zakończyć i upewnij się, że nie ma więcej LargeObject błąd na tym określonym obiekcie.

### <a name="step-5-apply-the-new-sync-rule-to-remaining-objects-with-largeobject-error"></a>Krok 5. Stosowanie nowej reguły synchronizacji do pozostałych obiektów z błędem LargeObject
Po dodaniu reguły synchronizacji należy uruchomić pełny krok synchronizacji na łączniku usługi AD:
1. Przejdź do karty **Łączniki** w Menedżerze usług synchronizacji.
2. Kliknij prawym przyciskiem myszy łącznik **usługi AD** i wybierz polecenie **Uruchom...**
3. W wyskakującym okienku Uruchom łącznik wybierz krok **Pełna synchronizacja** i kliknij przycisk **OK**.
4. Poczekaj na zakończenie kroku pełnej synchronizacji.
5. Powtórz powyższe kroki dla pozostałych łączników usługi AD, jeśli masz więcej niż jeden łącznik usługi AD. Zazwyczaj wiele łączników są wymagane, jeśli masz wiele katalogów lokalnych.

### <a name="step-6-verify-there-are-no-unexpected-changes-waiting-to-be-exported-to-azure-ad"></a>Krok 6. Sprawdź, czy nie ma nieoczekiwanych zmian oczekujących na wyeksportowanie do usługi Azure AD
1. Przejdź do karty **Łączniki** w Menedżerze usług synchronizacji.
2. Kliknij prawym przyciskiem myszy łącznik **usługi Azure AD** i wybierz pozycję Obszar **łącznika wyszukiwania**.
3. W wyskakującym okienku Obszar łącznika wyszukiwania:
    1. Ustaw zakres **na Oczekujące na Eksport**.
    2. Zaznacz wszystkie 3 pola wyboru, w tym **Dodaj,** **Modyfikuj** i **Usuń**.
    3. Kliknij przycisk **Wyszukaj,** aby zwrócić wszystkie obiekty ze zmianami oczekującymi na wyeksportowanie do usługi Azure AD.
    4. Sprawdź, czy nie ma żadnych nieoczekiwanych zmian. Aby sprawdzić zmiany dla danego obiektu, kliknij dwukrotnie obiekt.

### <a name="step-7-export-the-changes-to-azure-ad"></a>Krok 7. Eksportowanie zmian do usługi Azure AD
Aby wyeksportować zmiany do usługi Azure AD:
1. Przejdź do karty **Łączniki** w Menedżerze usług synchronizacji.
2. Kliknij prawym przyciskiem myszy łącznik **usługi Azure AD** i wybierz polecenie **Uruchom...**
4. W wyskakującym okienku Uruchom łącznik wybierz krok **Eksportuj** i kliknij przycisk **OK**.
5. Poczekaj na export do usługi Azure AD, aby zakończyć i upewnij się, że nie ma więcej largeobject błędy.

### <a name="step-8-re-enable-sync-scheduler"></a>Krok 8. Ponowne włączanie harmonogramu synchronizacji
Teraz, gdy problem został rozwiązany, ponownie włącz wbudowany harmonogram synchronizacji:
1. Uruchamianie sesji programu PowerShell.
2. Ponownie włącz zaplanowaną synchronizację, uruchamiając polecenie cmdlet:`Set-ADSyncScheduler -SyncCycleEnabled $true`

> [!Note]
> Powyższe kroki mają zastosowanie tylko do nowszych wersji (1.1.xxx.x) usługi Azure AD Connect z wbudowanym harmonogramem. Jeśli używasz starszych wersji (1.0.xxx.x) usługi Azure AD Connect, która używa harmonogramu zadań systemu Windows lub używasz własnego harmonogramu niestandardowego (nie często) do wyzwalania okresowej synchronizacji, należy je odpowiednio wyłączyć.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).

