---
title: Azure AD Connect - LargeObject błędów spowodowanych przez atrybut certyfikatu użytkownika | Dokumentacja firmy Microsoft
description: Ten temat zawiera kroki korygujące LargeObject błędy spowodowane przez atrybut certyfikatu użytkownika.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 146ad5b3-74d9-4a83-b9e8-0973a19828d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 9866454735b33239a812dca238006299c74e5ae2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34592810"
---
# <a name="azure-ad-connect-sync-handling-largeobject-errors-caused-by-usercertificate-attribute"></a>Synchronizacja programu Azure AD Connect: LargeObject obsługi błędów spowodowanych przez atrybut certyfikatu użytkownika

Usługi Azure AD wymusza maksymalny limit **15** certyfikatów wartości na **userCertificate** atrybutu. Jeśli usługa Azure AD Connect eksportuje obiektu z więcej niż 15 wartości do usługi Azure AD, Azure AD zwraca **LargeObject** błędu z komunikatem:

>*"Obiekt inicjowana jest zbyt duży. Ogranicz liczbę wartości atrybutów dla tego obiektu. Operacja zostanie ponowiona w następnym cyklu synchronizacji..."*

Inne atrybuty AD może być przyczyną błędu LargeObject. Aby upewnić się, w rzeczywistości jest spowodowany przez atrybut certyfikatu użytkownika, należy sprawdzić w obiekcie w lokalnej usłudze AD lub w [wyszukiwanie Metaverse Menedżera usługi synchronizacji](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-mvsearch).

Aby uzyskać listę obiektów w dzierżawie z błędami LargeObject, wykonaj jedną z następujących metod:

 * Jeśli włączono dzierżawy usługi Azure AD Connect Health do celów synchronizacji, może się odwoływać do [raport o błędzie synchronizacji](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-sync#object-level-synchronization-error-report-preview) podane.
 
 * Powiadomienia e-mail dla błędów synchronizacji katalogu wysyłany na koniec cyklu synchronizacji zawiera listę obiektów z błędami LargeObject. 
 * [Kartę operacje Menedżera usługi synchronizacji](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-operations) Wyświetla listę obiektów z błędami LargeObject kliknięcie najnowsze eksportu do operacji usługi Azure AD.
 
## <a name="mitigation-options"></a>Środki zaradcze opcje
Do momentu rozwiązania błąd LargeObject inne zmiany atrybutów do tego samego obiektu nie można wyeksportować do usługi Azure AD. Aby rozwiązać problem, należy wziąć pod uwagę następujące opcje:

 * Uaktualnij Azure AD Connect do kompilacji 1.1.524.0 lub po. W programie Azure AD Connect kompilacji 1.1.524.0, synchronizacji out-of-box, które zasady zostały zaktualizowane w usłudze nie eksportu certyfikatu użytkownika atrybutów i userSMIMECertificate. Jeśli atrybut ma więcej niż 15 wartości. Aby uzyskać więcej informacji na temat sposobu uaktualnienia Azure AD Connect, można znaleźć w artykule [Azure AD Connect: uaktualnianie z poprzedniej wersji do najnowszej wersji](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

 * Implementowanie **reguła synchronizacji ruchu wychodzącego** w programie Azure AD Connect, które eksportuje **wartość zamiast rzeczywistej wartości dla obiektów z więcej niż 15 wartości certyfikatu null**. Ta opcja jest odpowiednia, jeśli nie wymagają certyfikatu wartości mają zostać wyeksportowane do usługi Azure AD dla obiektów z wartościami więcej niż 15. Aby uzyskać więcej informacji na temat sposobu wykonania tej reguły synchronizacji, można znaleźć w następnej sekcji [wdrażania reguły synchronizacji, aby ograniczyć eksportu certyfikatu użytkownika atrybutu](#implementing-sync-rule-to-limit-export-of-usercertificate-attribute).

 * Zmniejsz liczbę wartości certyfikatu na lokalnej obiektu usługi Active Directory (co najwyżej 15) przez usunięcie wartości, które nie są już używane przez Twoją organizację. Jest to odpowiednie, jeśli rozrostu atrybutu jest spowodowany przez certyfikaty wygasły lub nieużywane. Można użyć [dostępne tutaj skrypt programu PowerShell](https://gallery.technet.microsoft.com/Remove-Expired-Certificates-0517e34f) do znajdowania, wykonaj kopię zapasową i Usuń wygasłe certyfikaty w lokalnej usługi AD. Przed usunięciem certyfikaty, zalecane jest, sprawdź z administratorami infrastruktury w przypadku klucza publicznego w organizacji.

 * Konfigurowanie usługi Azure AD Connect do wykluczenia atrybutu certyfikatu użytkownika są eksportowane do usługi Azure AD. Ogólnie rzecz biorąc zaleca się tę opcję, ponieważ atrybut mogą być używane przez usługi Online firmy Microsoft w celu włączenia określonych scenariuszy. W szczególności:
    * Atrybut certyfikatu użytkownika na obiekt użytkownika jest używany przez usługi Exchange Online i klientów programu Outlook komunikat podpisywania i szyfrowania. Aby dowiedzieć się więcej o tej funkcji, można znaleźć w artykule [S/MIME komunikatu podpisywania i szyfrowania](https://technet.microsoft.com/library/dn626158(v=exchg.150).aspx).

    * Atrybut certyfikatu użytkownika na obiekcie komputera jest używany przez usługę Azure AD umożliwia lokalne systemu Windows 10 przyłączonych do domeny urządzeniom na łączenie z usługą Azure AD. Aby dowiedzieć się więcej o tej funkcji, przeczytaj artykuł [łączenie urządzeń przyłączonych do domeny do usługi Azure AD dla systemu Windows 10 napotyka](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy).

## <a name="implementing-sync-rule-to-limit-export-of-usercertificate-attribute"></a>Implementowanie regułę synchronizacji, aby ograniczyć eksportu certyfikatu użytkownika atrybutu
Aby rozwiązać problem LargeObject spowodowane przez atrybut certyfikatu użytkownika, można zaimplementować regułę synchronizacji ruchu wychodzącego w programie Azure AD Connect, które eksportuje **wartość zamiast rzeczywistej wartości dla obiektów z więcej niż 15 wartości certyfikatunull**. W tej sekcji opisano kroki wymagane do wykonania dla reguły synchronizacji **użytkownika** obiektów. Kroki mogą być dostosowywane do **skontaktuj się z** i **komputera** obiektów.

> [!IMPORTANT]
> Eksportowanie wartości null powoduje usunięcie certyfikatu wartości wcześniej pomyślnie wyeksportowane do usługi Azure AD.

Kroki można podsumować jako:

1. Wyłączanie harmonogramu synchronizacji i sprawdź, że nie ma synchronizacji w toku.
3. Znajdź istniejącą regułę synchronizacji ruchu wychodzącego dla atrybutu certyfikatu użytkownika.
4. Utwórz regułę synchronizacji ruchu wychodzącego wymagane.
5. Sprawdź nową regułę synchronizacji do istniejącego obiektu z powodu błędu LargeObject.
6. Zastosuj nowy regułę synchronizacji do pozostałe obiekty z powodu błędu LargeObject.
7. Sprawdź, nie wprowadzono żadnych zmian nieoczekiwany oczekiwania mają być eksportowane do usługi Azure AD.
8. Wyeksportuj zmiany do usługi Azure AD.
9. Włącz ponownie harmonogram synchronizacji.

### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Krok 1. Wyłączanie harmonogramu synchronizacji i sprawdź, że nie ma synchronizacji w toku
Upewnij się, że synchronizacja nie ma miejsce, gdy są w trakcie wdrażania nowej reguły synchronizacji, aby uniknąć zmian niezamierzone eksportowane do usługi Azure AD. Aby wyłączyć harmonogram synchronizacji wbudowany:
1. Uruchom sesję programu PowerShell na serwerze programu Azure AD Connect.

2. Wyłącz zaplanowanej synchronizacji, uruchamiając polecenie cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $false`

> [!Note]
> Te czynności mają zastosowanie tylko wtedy do nowszej wersji (1.1.xxx.x) programu Azure AD Connect z wbudowanych harmonogramu. Jeśli używasz starszej wersji (1.0.xxx.x) programu Azure AD Connect, która używa harmonogramu zadań systemu Windows lub są przy użyciu własnego harmonogramu niestandardowego (nie wspólnej) do wyzwolenia okresową synchronizację, należy odpowiednio je wyłączyć.

3. Uruchom **Menedżera usługi synchronizacji** , przechodząc do rozpoczęcia → usługi synchronizacji.

4. Przejdź do **operacji** karcie i upewnij się, Brak operacji ze stanem *"w"toku.*

### <a name="step-2-find-the-existing-outbound-sync-rule-for-usercertificate-attribute"></a>Krok 2. Znajdź istniejącą regułę synchronizacji ruchu wychodzącego dla atrybutu certyfikatu użytkownika
Powinien być istniejącą regułę synchronizacji, która została włączona i skonfigurowana, aby wyeksportować atrybut certyfikatu użytkownika dla obiektów użytkowników do usługi Azure AD. Ta reguła synchronizacji, aby dowiedzieć się, Znajdź jego **pierwszeństwo** i **zakresu filtru** konfiguracji:

1. Uruchom **Edytor reguł synchronizacji** , przechodząc do Edytor reguł synchronizacji START →.

2. Skonfiguruj filtry wyszukiwania z następujących wartości:

    | Atrybut | Wartość |
    | --- | --- |
    | Kierunek |**Wychodzące** |
    | Typ obiektu MV |**Osoby** |
    | Łącznik |*Nazwa łącznika usługi Azure AD* |
    | Typ obiektu łącznika |**Użytkownika** |
    | Atrybut MV |**certyfikatu użytkownika** |

3. Jeśli używasz OOB reguły synchronizacji (out-of-box) do łącznika usługi Azure AD można wyeksportować atrybutu userCertficiate obiektów użytkownika, powinny zostać wyświetlone *"Się do usługi AAD — użytkownik ExchangeOnline"* reguły.
4. Należy zanotować **pierwszeństwo** wartość ta reguła synchronizacji.
5. Wybierz regułę synchronizacji, a następnie kliknij przycisk **Edytuj**.
6. W *"Edytuj zastrzeżone potwierdzenie reguły"* podręczne okno dialogowe, kliknij przycisk **nr**. (Nie martw się, nie zamierzamy wprowadzać żadnych zmian do tej reguły synchronizacji).
7. Na ekranie edycji wybierz **filtru Scoping** kartę.
8. Należy zanotować konfiguracji zakresu filtru. Jeśli używasz reguły synchronizacji OOB dokładnie należy **jedną grupę ustalania zakresu filtru zawierające dwie klauzule**, takie jak:

    | Atrybut | Operator | Wartość |
    | --- | --- | --- |
    | sourceObjectType | RÓWNOŚCI | Użytkownik |
    | cloudMastered | NOTEQUAL | True |

### <a name="step-3-create-the-outbound-sync-rule-required"></a>Krok 3. Utwórz regułę synchronizacji ruchu wychodzącego wymagane
Nowa reguła synchronizacji muszą mieć ten sam **zakresu filtru** i **wyższy priorytet** niż istniejącą regułę synchronizacji. Dzięki temu, że nowa reguła synchronizacji ma zastosowanie do tego samego zestawu obiektów jako istniejącą regułę synchronizacji i zastępuje istniejącą regułę synchronizacji dla atrybutu certyfikatu użytkownika. Aby utworzyć regułę synchronizacji:
1. W edytorze reguły synchronizacji, kliknij przycisk **Dodaj nową regułę** przycisku.
2. W obszarze **kartę opis**, podaj następującej konfiguracji:

    | Atrybut | Wartość | Szczegóły |
    | --- | --- | --- |
    | Name (Nazwa) | *Podaj nazwę* | Np. *"Się do usługi AAD — niestandardowy zastąpienie dla certyfikatu użytkownika"* |
    | Opis | *Podaj opis* | Np. *"Jeśli certyfikatu użytkownika atrybut ma więcej niż 15 wartości, wyeksportuj NULL".* |
    | System połączony | *Wybierz łącznik usługi Azure AD* |
    | Połączony System typu obiektu | **Użytkownika** | |
    | Typ obiektu Metaverse | **Osoby** | |
    | Typ łącza | **Dołącz** | |
    | Pierwszeństwo | *Wybierz liczbę z zakresu od 1 do 99* | Liczba wybrana nie mogą być używane przez żadną istniejącą regułę synchronizacji i ma mniejszą wartość (i w związku z tym wyższy priorytet) niż istniejącą regułę synchronizacji. |

3. Przejdź do **filtru Scoping** karcie i wdrożenia tego samego zakresu filtru używa istniejącą regułę synchronizacji.
4. Pomiń **dołączyć reguły** kartę.
5. Przejdź do **przekształcenia** kartę, aby dodać nowy przekształcenia przy użyciu następujących konfiguracji:

    | Atrybut | Wartość |
    | --- | --- |
    | Typ przepływu |**wyrażenie** |
    | Atrybut docelowy |**certyfikatu użytkownika** |
    | Atrybut źródłowy |*Należy użyć następującego wyrażenia*: `IIF(IsNullOrEmpty([userCertificate]), NULL, IIF((Count([userCertificate])> 15),AuthoritativeNull,[userCertificate]))` |
    
6. Kliknij przycisk **Dodaj** przycisk, aby utworzyć regułę synchronizacji.

### <a name="step-4-verify-the-new-sync-rule-on-an-existing-object-with-largeobject-error"></a>Krok 4. Sprawdź nową regułę synchronizacji do istniejącego obiektu z powodu błędu LargeObject
To jest sprawdzenie, czy reguła synchronizacji utworzona działa poprawnie na istniejącego obiektu AD z powodu błędu LargeObject przed zastosowaniem do innych obiektów:
1. Przejdź do **operacji** kartę Menedżera usługi synchronizacji.
2. Wybierz najnowsze eksportu do operacji usługi Azure AD i kliknij jeden z obiektów z błędami LargeObject.
3.  Na ekranie podręczne właściwości obiektu obszaru łącznika polecenie **Podgląd** przycisku.
4. Na ekranie wyskakującego Podgląd wybierz **pełnej synchronizacji** i kliknij przycisk **zatwierdzić Podgląd**.
5. Zamknij podgląd ekranu i ekranu właściwości obiektu obszaru łącznika.
6. Przejdź do **łączniki** kartę Menedżera usługi synchronizacji.
7. Kliknij prawym przyciskiem myszy **usługi Azure AD** łącznik i wybierz **Uruchom...**
8. W oknie podręcznym uruchomić łącznik, wybierz **wyeksportować** kroku i kliknij przycisk **OK**.
9. Poczekaj na eksport do usługi Azure AD do ukończenia i upewnij się, że nie było więcej LargeObject błędu tego określonego obiektu.

### <a name="step-5-apply-the-new-sync-rule-to-remaining-objects-with-largeobject-error"></a>Krok 5. Dotyczą nową regułę synchronizacji pozostałe obiekty z powodu błędu LargeObject
Po dodaniu reguły synchronizacji, należy uruchomić krok pełnej synchronizacji na łączniku usługi AD:
1. Przejdź do **łączniki** kartę Menedżera usługi synchronizacji.
2. Kliknij prawym przyciskiem myszy **AD** łącznik i wybierz **Uruchom...**
3. W oknie podręcznym uruchomić łącznik, wybierz **pełną synchronizację** kroku i kliknij przycisk **OK**.
4. Poczekaj, aż krok pełną synchronizację, aby zakończyć.
5. Jeśli masz więcej niż jeden AD łączników, powtórz powyższe kroki dla pozostałych łączników AD. Wiele łączników są zazwyczaj wymagane, jeśli masz wiele katalogów lokalnych.

### <a name="step-6-verify-there-are-no-unexpected-changes-waiting-to-be-exported-to-azure-ad"></a>Krok 6. Sprawdź nie wprowadzono żadnych zmian nieoczekiwany oczekiwania mają być eksportowane do usługi Azure AD
1. Przejdź do **łączniki** kartę Menedżera usługi synchronizacji.
2. Kliknij prawym przyciskiem myszy **usługi Azure AD** łącznik i wybierz **przestrzeni łącznika wyszukiwania**.
3. W oknie podręcznym przestrzeni łącznika wyszukiwania:
    1. Ustaw zakres na **oczekujących eksportu**.
    2. Sprawdź wszystkie 3 pola wyboru, w tym **Dodaj**, **Modyfikuj** i **usunąć**.
    3. Kliknij przycisk **wyszukiwania** przycisk, aby zwrócić wszystkie obiekty z zmian oczekujących mają być eksportowane do usługi Azure AD.
    4. Sprawdź, czy nie ma żadnych zmian nieoczekiwany. Aby sprawdzić zmiany dla danego obiektu, kliknij dwukrotnie obiekt.

### <a name="step-7-export-the-changes-to-azure-ad"></a>Krok 7. Eksportowanie zmiany do usługi Azure AD
Aby wyeksportować zmiany do usługi Azure AD:
1. Przejdź do **łączniki** kartę Menedżera usługi synchronizacji.
2. Kliknij prawym przyciskiem myszy **usługi Azure AD** łącznik i wybierz **Uruchom...**
4. W oknie podręcznym uruchomić łącznik, wybierz **wyeksportować** kroku i kliknij przycisk **OK**.
5. Poczekaj na eksport do usługi Azure AD, aby zakończyć i upewnij się, że nie ma żadnych błędów więcej LargeObject.

### <a name="step-8-re-enable-sync-scheduler"></a>Krok 8. Włącz ponownie harmonogram synchronizacji
Teraz, gdy problem zostanie rozwiązany, należy ponownie włączyć harmonogramu synchronizacji wbudowany:
1. Uruchom sesję programu PowerShell.
2. Ponownie włączyć zaplanowanej synchronizacji, uruchamiając polecenie cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $true`

> [!Note]
> Te czynności mają zastosowanie tylko wtedy do nowszej wersji (1.1.xxx.x) programu Azure AD Connect z wbudowanych harmonogramu. Jeśli używasz starszej wersji (1.0.xxx.x) programu Azure AD Connect, która używa harmonogramu zadań systemu Windows lub są przy użyciu własnego harmonogramu niestandardowego (nie wspólnej) do wyzwolenia okresową synchronizację, należy odpowiednio je wyłączyć.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md).

