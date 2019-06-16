---
title: Azure AD Connect — błędów LargeObject spowodowanych przez atrybut userCertificate | Dokumentacja firmy Microsoft
description: Ten temat zawiera instrukcje korygowania błędów LargeObject spowodowanych przez atrybut userCertificate.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62095493"
---
# <a name="azure-ad-connect-sync-handling-largeobject-errors-caused-by-usercertificate-attribute"></a>Synchronizacja programu Azure AD Connect: Obsługa błędów LargeObject spowodowanych przez atrybut userCertificate

Azure AD wymusza maksymalny limit wynoszący **15** certyfikatu wartości na **userCertificate** atrybutu. Jeśli program Azure AD Connect umożliwia wyeksportowanie obiektu z więcej niż 15 wartości do usługi Azure AD, usługa Azure AD zwraca **LargeObject** błąd z komunikatem:

>*"Elastycznie obiektu jest zbyt duży. Ogranicz liczbę wartości atrybutu dla tego obiektu. Operacja zostanie ponowiona podczas następnego cyklu synchronizacji..."*

Błąd LargeObject może być spowodowany przez inne atrybuty usługi AD. Aby upewnić się, w rzeczywistości jest to spowodowane przez atrybut userCertificate, należy sprawdzić przed obiektu w lokalnej usługi AD lub na [wyszukiwanie magazynu Metaverse Menedżera usługi synchronizacji](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-mvsearch).

Aby uzyskać listę obiektów w dzierżawie, z błędami LargeObject, użyj jednej z następujących metod:

 * Jeśli włączono dzierżawy usługi Azure AD Connect Health do celów synchronizacji, możesz zapoznać się z [raport o błędach synchronizacji](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-sync) podane.
 
 * Powiadomienia e-mail dla błędów synchronizacji katalogu wysyłanym na końcu każdego cyklu synchronizacji zawiera listę obiektów z błędami LargeObject. 
 * [Karcie operacje Menedżera usługi synchronizacji](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-operations) Wyświetla listę obiektów z błędami LargeObject po kliknięciu najnowsze eksportu do operacji usługi Azure AD.
 
## <a name="mitigation-options"></a>Opcje ograniczania ryzyka
Do momentu rozwiązania błąd LargeObject inne zmiany atrybutów ten sam obiekt nie można wyeksportować do usługi Azure AD. Aby naprawić błąd, należy wziąć pod uwagę następujące opcje:

 * Uaktualnij program Azure AD Connect do tworzenia w 1.1.524.0 lub po. W programie Azure AD Connect kompilacji 1.1.524.0, out-of-box synchronizacji, które zasady zostały zaktualizowanie, aby nie Eksportuj atrybutów userCertificate i userSMIMECertificate, jeśli więcej niż 15 wartości atrybutów. Aby uzyskać szczegółowe informacje o tym, jak uaktualnić program Azure AD Connect, można znaleźć w artykule [program Azure AD Connect: Uaktualnianie z poprzedniej wersji do najnowszej](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

 * Implementowanie **reguły synchronizacji ruchu wychodzącego** w programie Azure AD Connect, które eksportuje **wartość null, wartość zamiast wartości faktycznych dla obiektów z więcej niż 15 wartości certyfikatu**. Ta opcja jest odpowiednia, jeśli nie potrzebujesz dowolna z wartości certyfikatów, które mają zostać wyeksportowane do usługi Azure AD dla obiektów z więcej niż 15 wartości. Aby uzyskać szczegółowe informacje na temat sposobu wykonania tej reguły synchronizacji, można znaleźć w następnej sekcji [Implementowanie reguły synchronizacji, aby ograniczyć eksportu atrybutu userCertificate](#implementing-sync-rule-to-limit-export-of-usercertificate-attribute).

 * Zmniejsz liczbę wartości certyfikatu dla lokalnego obiektu usługi AD (15 lub mniej), usuwając wartości, które nie są już używane przez Twoją organizację. Jest to odpowiednie, jeśli rozrostu atrybutu jest spowodowany przez certyfikaty wygasłe lub nieużywane. Możesz użyć [tutaj dostępny skrypt programu PowerShell](https://gallery.technet.microsoft.com/Remove-Expired-Certificates-0517e34f) ułatwia znalezienie kopii zapasowej i Usuń wygasłe certyfikaty w lokalnej usługi AD. Przed usunięciem certyfikaty, zalecane jest, sprawdź z administratorami infrastruktury w przypadku klucza publicznego w Twojej organizacji.

 * Skonfiguruj program Azure AD Connect, które mają zostać wykluczone atrybutu userCertificate są eksportowane do usługi Azure AD. Ogólnie rzecz biorąc zaleca się tę opcję, ponieważ atrybut może być używane Microsoft Online Services w celu włączenia określonych scenariuszy. W szczególności:
    * Atrybut userCertificate obiektu użytkownika jest używany przez usługi Exchange Online i klientów programu Outlook komunikat podpisywania i szyfrowania. Aby dowiedzieć się więcej na temat tej funkcji, można znaleźć w artykule [S/MIME wiadomości podpisywania i szyfrowania](https://technet.microsoft.com/library/dn626158(v=exchg.150).aspx).

    * Atrybut userCertificate obiektu komputera umożliwia przez usługę Azure AD Windows 10 w środowisku lokalnym urządzeń przyłączonych do domeny, do łączenia z usługą Azure AD. Aby dowiedzieć się więcej na temat tej funkcji, przeczytaj artykuł [łączenie urządzeń przyłączonych do domeny do usługi Azure AD, dla środowisk systemu Windows 10](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy).

## <a name="implementing-sync-rule-to-limit-export-of-usercertificate-attribute"></a>Implementowanie reguły synchronizacji, aby ograniczyć eksportu atrybutu userCertificate
Aby rozwiązać błąd LargeObject spowodowany przez atrybut userCertificate, można zaimplementować regułę synchronizacji ruchu wychodzącego w programie Azure AD Connect, które eksportuje **wartość null, wartość zamiast wartości faktycznych dla obiektów z więcej niż 15 wartości certyfikatu**. W tej sekcji opisano kroki wymagane do wykonania dla reguły synchronizacji **użytkownika** obiektów. Kroki mogą być dostosowane do **skontaktuj się z pomocą** i **komputera** obiektów.

> [!IMPORTANT]
> Eksportowanie wartości null powoduje usunięcie certyfikatu wartości wcześniej pomyślnie wyeksportowane do usługi Azure AD.

Kroki można podsumować jako:

1. Wyłącz harmonogram synchronizacji i sprawdzić, czy jest brak synchronizacji w toku.
3. Atrybut userCertificate można znaleźć w istniejącą regułę synchronizacji ruchu wychodzącego.
4. Utwórz regułę synchronizacji ruchu wychodzącego, wymagane.
5. Sprawdź, czy nowa reguła synchronizacji na istniejący obiekt z błędem LargeObject
6. Zastosować nową regułę synchronizacji do pozostałych obiektów z błędami LargeObject.
7. Sprawdź, brak nieoczekiwanych zmian oczekujących mają zostać wyeksportowane do usługi Azure AD.
8. Wyeksportuj zmiany do usługi Azure AD.
9. Ponownie Włącz harmonogram synchronizacji.

### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Krok 1. Wyłącz harmonogram synchronizacji i sprawdzić, czy jest brak synchronizacji w toku
Upewnij się, że synchronizacja nie ma miejsce, gdy jesteś w trakcie wdrażania nowej reguły synchronizacji, aby uniknąć niezamierzonych zmian eksportowanych do usługi Azure AD. Aby wyłączyć harmonogram synchronizacji wbudowane:
1. Uruchom sesję programu PowerShell na serwerze usługi Azure AD Connect.

2. Wyłącz zaplanowanej synchronizacji, uruchamiając polecenie cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $false`

> [!Note]
> Te czynności mają zastosowanie tylko wtedy do nowszej wersji (1.1.xxx.x) programu Azure AD Connect za pomocą wbudowanych harmonogramu. Jeśli używasz starszej wersji (1.0.xxx.x) programu Azure AD Connect, który używa harmonogramu zadań Windows lub własny niestandardowy harmonogram (nie jest to częsty) używają do wyzwolenia okresowej synchronizacji, należy odpowiednio je wyłączyć.

1. Rozpocznij **Menedżera usługi synchronizacji** , przechodząc do rozpoczęcia → usługi synchronizacji.

1. Przejdź do **operacji** kartę i upewnij się, brak żadnej operacji, których stan to *"w"toku.*

### <a name="step-2-find-the-existing-outbound-sync-rule-for-usercertificate-attribute"></a>Krok 2. Znajdź istniejącą regułę synchronizacji ruchu wychodzącego dla atrybutu userCertificate
Powinna to być istniejącą regułę synchronizacji, która jest włączone i skonfigurowane do wyeksportowania certyfikatu użytkownika atrybutu dla obiektów użytkowników do usługi Azure AD. Odszukaj tę regułę synchronizacji, aby dowiedzieć się, jego **pierwszeństwo** i **filtru określania zakresu** konfiguracji:

1. Rozpocznij **Synchronization Rules Editor** , przechodząc do rozpoczęcia → Synchronization Rules Editor.

2. Konfigurowanie filtrów wyszukiwania, z następującymi wartościami:

    | Atrybut | Wartość |
    | --- | --- |
    | Direction |**Wychodzące** |
    | Typ obiektu MV |**Osoby** |
    | Łącznik |*Nazwa łącznika usługi Azure AD* |
    | Typ obiektu łącznika |**Użytkownik** |
    | Atrybut MV |**userCertificate** |

3. Jeśli używasz OOB reguły synchronizacji (out-of-box) do łącznika usługi Azure AD można wyeksportować userCertficiate atrybutu dla obiektów użytkowników, należy uzyskać ponownie *"Się do usługi AAD — użytkownik ExchangeOnline"* reguły.
4. Zanotuj **pierwszeństwo** wartość ta reguła synchronizacji.
5. Wybierz regułę synchronizacji, a następnie kliknij przycisk **Edytuj**.
6. W *"Edytuj zarezerwowanych reguły Confirmation"* wyskakujące okno dialogowe, kliknij przycisk **nie**. (Nie martw się, nie będziemy wprowadzać zmian tej reguły synchronizacji).
7. Na ekranie edycji wybierz **filtru Scoping** kartę.
8. Zanotuj wartości zakresu konfiguracji filtru. Jeśli używane są reguły synchronizacji OOB, powinien dokładnie istnieć **jedna grupa filtrów określania zakresu zawierające dwóch klauzul**, w tym:

    | Atrybut | Operator | Wartość |
    | --- | --- | --- |
    | sourceObjectType | RÓWNA SIĘ | Użytkownik |
    | cloudMastered | NOTEQUAL | True |

### <a name="step-3-create-the-outbound-sync-rule-required"></a>Krok 3. Utwórz regułę synchronizacji ruchu wychodzącego, wymagane
Nowa reguła synchronizacji musi mieć taką samą **filtru określania zakresu** i **wyższy priorytet** niż istniejące reguły synchronizacji. Gwarantuje to, że nowa reguła synchronizacji ma zastosowanie do tego samego zestawu obiektów jako istniejącą regułę synchronizacji i zastępuje istniejącą regułę synchronizacji dla atrybutu userCertificate. Aby utworzyć regułę synchronizacji:
1. Za pomocą edytora reguł synchronizacji, kliknij przycisk **Dodaj nową regułę** przycisku.
2. W obszarze **kartę opis**, podaj następującą konfigurację:

    | Atrybut | Wartość | Szczegóły |
    | --- | --- | --- |
    | Name (Nazwa) | *Podaj nazwę* | Np. *"Się do usługi AAD — niestandardowe przesłonięcia dla certyfikatu użytkownika"* |
    | Opis | *Podaj opis* | Np. *"Jeśli atrybut userCertificate ma więcej niż 15 wartości, Eksportuj NULL".* |
    | Połączonego systemu | *Wybierz łącznik usługi Azure AD* |
    | Połączony System typu obiektu | **Użytkownik** | |
    | Typ obiektu Metaverse | **Osoby** | |
    | Typ łącza | **Join** | |
    | Pierwszeństwo | *Wybierz liczbę z zakresu od 1 do 99* | Wybrana liczba nie mogą być używane przez istniejące reguły synchronizacji i ma niższą wartość (i w związku z tym, wyższy priorytet) niż istniejące reguły synchronizacji. |

3. Przejdź do **filtru Scoping** kartę i wdrożenie tego samego filtru określania zakresu używa istniejącej reguły synchronizacji.
4. Pomiń **Dołącz zasady** kartę.
5. Przejdź do **przekształcenia** kartę, aby dodać nowe przekształcenia przy użyciu następujących konfiguracji:

    | Atrybut | Wartość |
    | --- | --- |
    | Typ przepływu |**Expression** |
    | Atrybut docelowy |**userCertificate** |
    | Atrybut źródłowy |*Użyj następującego wyrażenia*: `IIF(IsNullOrEmpty([userCertificate]), NULL, IIF((Count([userCertificate])> 15),AuthoritativeNull,[userCertificate]))` |
    
6. Kliknij przycisk **Dodaj** przycisk, aby utworzyć regułę synchronizacji.

### <a name="step-4-verify-the-new-sync-rule-on-an-existing-object-with-largeobject-error"></a>Krok 4. Sprawdź nową regułę synchronizacji na istniejący obiekt z błędem LargeObject
To, aby sprawdzić, czy reguła synchronizacji utworzona działa poprawnie na istniejący obiekt AD błąd LargeObject przed zastosowaniem do innych obiektów:
1. Przejdź do **operacji** kartę w Menedżerze usługi synchronizacji.
2. Wybierz najbardziej aktualną eksportu do operacji usługi Azure AD i kliknij jeden z obiektów z błędami LargeObject.
3.  Na ekranie podręczne właściwości obiektu obszaru łącznika kliknij **Podgląd** przycisku.
4. Na ekranie wyskakujące (wersja zapoznawcza) wybierz **pełnej synchronizacji** i kliknij przycisk **zatwierdzenia w wersji zapoznawczej**.
5. Zamknij ekran (wersja zapoznawcza) i ekran właściwości obiektu obszaru łącznika.
6. Przejdź do **łączników** kartę w Menedżerze usługi synchronizacji.
7. Kliknij prawym przyciskiem myszy **usługi Azure AD** łącznika, a następnie wybierz pozycję **uruchamianie...**
8. W oknie podręcznym uruchomienia łącznika wybierz **wyeksportować** krok, a następnie kliknij przycisk **OK**.
9. Poczekaj, aż eksportu do usługi Azure AD, aby zakończyć i upewnij się, że nie ma więcej LargeObject błędów dla tego określonego obiektu.

### <a name="step-5-apply-the-new-sync-rule-to-remaining-objects-with-largeobject-error"></a>Krok 5. Zastosować nową regułę synchronizacji do pozostałych obiektów z błędami LargeObject
Po dodaniu reguły synchronizacji, musisz uruchomić kroku Pełna synchronizacja w łączniku usługi AD:
1. Przejdź do **łączników** kartę w Menedżerze usługi synchronizacji.
2. Kliknij prawym przyciskiem myszy **AD** łącznika, a następnie wybierz pozycję **uruchamianie...**
3. W oknie podręcznym uruchomienia łącznika wybierz **pełną synchronizację** krok, a następnie kliknij przycisk **OK**.
4. Poczekaj, aż kroku pełnej synchronizacji, aby ukończyć.
5. Powtórz powyższe kroki dla pozostałych łączników usługi AD, jeśli masz więcej niż jedną łączników usługi AD. Wiele łączników są zazwyczaj wymagane, jeśli masz wiele katalogów lokalnych.

### <a name="step-6-verify-there-are-no-unexpected-changes-waiting-to-be-exported-to-azure-ad"></a>Krok 6. Nie występują żadne nieoczekiwanych zmian oczekujących mają zostać wyeksportowane do usługi Azure AD
1. Przejdź do **łączników** kartę w Menedżerze usługi synchronizacji.
2. Kliknij prawym przyciskiem myszy **usługi Azure AD** łącznika, a następnie wybierz pozycję **wyszukiwania obszaru łącznika**.
3. W oknie podręcznym wyszukiwania obszaru łącznika:
    1. Ustaw zakres na **operacja eksportowania oczekująca**.
    2. Sprawdź wszystkie 3 pola wyboru, w tym **Dodaj**, **Modyfikuj** i **Usuń**.
    3. Kliknij przycisk **wyszukiwania** przycisk, aby zwrócić wszystkie obiekty za pomocą zmian oczekujących mają zostać wyeksportowane do usługi Azure AD.
    4. Sprawdź, czy występują nieoczekiwane zmiany. Aby sprawdzić zmiany dla danego obiektu, kliknij dwukrotnie obiekt.

### <a name="step-7-export-the-changes-to-azure-ad"></a>Krok 7. Eksportuj zmiany do usługi Azure AD
Aby wyeksportować zmiany do usługi Azure AD:
1. Przejdź do **łączników** kartę w Menedżerze usługi synchronizacji.
2. Kliknij prawym przyciskiem myszy **usługi Azure AD** łącznika, a następnie wybierz pozycję **uruchamianie...**
4. W oknie podręcznym uruchomienia łącznika wybierz **wyeksportować** krok, a następnie kliknij przycisk **OK**.
5. Poczekaj, aż eksportu do usługi Azure AD, aby zakończyć i upewnij się, że nie ma żadnych więcej błędów LargeObject.

### <a name="step-8-re-enable-sync-scheduler"></a>Krok 8. Ponownie Włącz harmonogram synchronizacji
Teraz, gdy problem zostanie rozwiązany, ponownie włączyć harmonogram synchronizacji wbudowane:
1. Uruchom sesję programu PowerShell.
2. Ponownie włączyć zaplanowanej synchronizacji, uruchamiając polecenie cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $true`

> [!Note]
> Te czynności mają zastosowanie tylko wtedy do nowszej wersji (1.1.xxx.x) programu Azure AD Connect za pomocą wbudowanych harmonogramu. Jeśli używasz starszej wersji (1.0.xxx.x) programu Azure AD Connect, który używa harmonogramu zadań Windows lub własny niestandardowy harmonogram (nie jest to częsty) używają do wyzwolenia okresowej synchronizacji, należy odpowiednio je wyłączyć.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).

