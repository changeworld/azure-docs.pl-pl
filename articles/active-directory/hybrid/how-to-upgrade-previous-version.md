---
title: 'Program Azure AD Connect: Uaktualnianie z poprzedniej wersji | Dokumentacja firmy Microsoft'
description: W tym artykule wyjaśniono różne metody, aby uaktualnić do najnowszej wersji usługi Azure Active Directory Connect, w tym uaktualnienia w miejscu i migracja typu swing.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 31f084d8-2b89-478c-9079-76cf92e6618f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 04/08/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a3e7373a8b0354a3d08debf944f2f77f1609382
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59267042"
---
# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Program Azure AD Connect: Uaktualnianie z poprzedniej wersji do najnowszej wersji
W tym temacie opisano różne metody, których można użyć, aby zaktualizować swoją instalację usługi Azure Active Directory (Azure AD) Connect do najnowszej wersji. Firma Microsoft zaleca zachowywanie samodzielnie bieżącego z wersjami programu Azure AD Connect. Użyto również w krokach w [migracja typu Swing](#swing-migration) sekcji po wprowadzeniu znaczące zmiany konfiguracji.

>[!NOTE]
> Obecnie jest obsługiwane uaktualnienie z dowolnej wersji programu Azure AD Connect do bieżącej wersji. Nie są obsługiwane uaktualnienia w miejscu z narzędzia DirSync lub ADSync i migracja typu swing jest wymagana.  Do uaktualnienia narzędzia DirSync, zobacz temat [uaktualnienie z narzędzia Azure AD sync (DirSync)](how-to-dirsync-upgrade-get-started.md) lub [migracja typu Swing](#swing-migration) sekcji.  </br>W praktyce klientom bardzo starsze wersje mogą wystąpić problemy, które nie są bezpośrednio związane z programem Azure AD Connect. Serwery, które zostały w praktyce od kilku lat, zwykle mieli kilka poprawek zastosowanych do nich, a nie wszystkie z nich może być rejestrowana.  Ogólnie rzecz biorąc, klienci, którzy nie zostały uaktualnione w ciągu 12 – 18 miesięcy należy rozważyć uaktualnienie swing zamiast tego, jak jest to opcja zachowawcze najbardziej i najmniej ryzykowne.

Do uaktualnienia narzędzia DirSync, zobacz temat [uaktualnienie z narzędzia Azure AD sync (DirSync)](how-to-dirsync-upgrade-get-started.md) zamiast tego.

Istnieje kilka różne strategie, których można użyć, aby uaktualnić program Azure AD Connect.

| Metoda | Opis |
| --- | --- |
| [Automatycznie uaktualnianie](how-to-connect-install-automatic-upgrade.md) |Jest to najprostsza metoda dla klientów korzystających z instalacji ekspresowej. |
| [Uaktualnienie w miejscu](#in-place-upgrade) |W przypadku pojedynczego serwera, możesz uaktualnić instalację w miejscu na tym samym serwerze. |
| [Migracja typu Swing](#swing-migration) |Przy użyciu dwóch serwerów możesz przygotować jeden z serwerów za pomocą nowej wersji lub konfiguracji i zmienianie aktywnego serwera, gdy wszystko będzie gotowe. |

Aby uzyskać informacje o uprawnieniach, zobacz [uprawnienia wymagane dla uaktualnienie](reference-connect-accounts-permissions.md#upgrade).

> [!NOTE]
> Po włączeniu nowego serwera Azure AD Connect rozpocząć synchronizowanie zmian z usługą Azure AD, należy nie wycofasz do korzystania z narzędzia DirSync lub Azure AD Sync. Zmiany na starszą wersję z usługi Azure AD Connect do starszych klientów, w tym DirSync i Azure AD Sync, nie jest obsługiwana i może prowadzić do problemów, takich jak utrata danych w usłudze Azure AD.

## <a name="in-place-upgrade"></a>Uaktualnienie w miejscu
Uaktualnienie w miejscu działa w przypadku przechodzenia z programu Azure AD Sync lub Azure AD Connect. To nie zadziała, przenoszenie z narzędzia DirSync lub rozwiązania przy użyciu programu Forefront Identity Manager (FIM) i łącznik usługi Azure AD.

Ta metoda jest preferowana w przypadku pojedynczego serwera i mniej niż około 100 000 obiektów. W przypadku zmiany reguł synchronizacji out-of-box pełne importowanie i pełną synchronizację wystąpić po uaktualnieniu. Ta metoda zapewnia, że nowa konfiguracja jest stosowane do wszystkich istniejących obiektów w systemie. Ten przebieg może potrwać kilka godzin w zależności od liczby obiektów, które znajdują się w zakresie aparat synchronizacji. Harmonogram synchronizacji różnicowa (która domyślnie synchronizuje co 30 minut) jest wstrzymana, ale kontynuuje synchronizacji haseł. Należy rozważyć, wykonując uaktualnienie w miejscu weekend. W przypadku wydania bez zmian w konfiguracji out-of-box za pomocą nowej usługi Azure AD Connect, następnie Importuj/synchronizacja różnicowa uruchamia zamiast tego.  
![Uaktualnienie w miejscu](./media/how-to-upgrade-previous-version/inplaceupgrade.png)

Jeśli wprowadzono zmiany reguł synchronizacji out-of-box, te reguły są ustawiane Wstecz w domyślnej konfiguracji w uaktualnieniu. Aby upewnić się, że konfigurację jest przechowywana między uaktualnień, upewnij się, wprowadzić zmiany, opisane są one w [najlepsze rozwiązania dotyczące zmieniania konfiguracji domyślnej](how-to-connect-sync-best-practices-changing-default-configuration.md).

Podczas uaktualniania w miejscu, mogą istnieć zmiany wprowadzone, które wymagają synchronizacji określonych działań (w tym kroki pełny Import i pełną synchronizację) do wykonania po zakończeniu uaktualniania. Mają być odroczone takich działań, zobacz sekcję [jak mają być odroczone pełnej synchronizacji po uaktualnieniu](#how-to-defer-full-synchronization-after-upgrade).

Jeśli używasz usługi Azure AD Connect za pomocą łącznika niestandardowego (na przykład ogólnego łącznika LDAP i ogólnego łącznika SQL), należy odświeżyć odpowiedniej konfiguracji łącznika w [Menedżera usługi synchronizacji](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-connectors) Po uaktualnieniu w miejscu. Aby uzyskać szczegółowe informacje na temat odświeżania konfiguracji łącznika, można znaleźć w sekcji artykułu [Historia wersji łącznika — Rozwiązywanie problemów z](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-connector-version-history#troubleshooting). Jeśli nie odświeżaj, konfigurację importu i eksportu, wykonaj kroki nie będzie działać prawidłowo dla łącznika. Zostanie wyświetlony następujący błąd w dzienniku zdarzeń aplikacji z komunikatem *"wersja zestawu w konfiguracji łącznika usługi AAD ("X.X.XXX. "X") jest starsza niż wersja rzeczywista ("X.X.XXX. "X") z "C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll".*

## <a name="swing-migration"></a>Migracja typu swing
Jeśli masz złożone wdrożenia lub wiele obiektów, może być niepraktyczne przeprowadzenie uaktualnienia w miejscu w systemie produkcyjnym. Dla niektórych klientów ten proces może potrwać kilka dni — i w tym czasie, nie zmiany różnicowe są przetwarzane. Tej metody można użyć również, gdy planujesz wprowadzić istotne zmiany w konfiguracji i chcesz wypróbować je przed ich są wypychane do chmury.

Zalecaną metodą dla tych scenariuszy jest migracja typu swing używać. Należy (co najmniej) dwa serwery — jednego aktywnego serwera i jeden serwer przejściowy. ASP (niebieska linia ciągła na poniższej ilustracji przedstawiono) jest odpowiedzialny za obciążeniem produkcyjnym active. Serwer przejściowy (pokazane z kreskowanym purpurowe linie) są przygotowywane za pomocą nowej wersji lub konfiguracji. Gdy jest to w pełni gotowe, ten serwer jest aktywowane. Poprzednie aktywnego serwera, która ma teraz starej wersji lub konfiguracji, zainstalowany, zostanie utworzony serwer przejściowy oraz jest uaktualniany.

Dwa serwery, możesz użyć innej wersji. Na przykład aktywnego serwera, z którego planujesz zlikwidować można użyć usługi Azure AD Sync, a nowy serwer przejściowy, można użyć usługi Azure AD Connect. Jeśli używasz migracja typu swing do tworzenia nowej konfiguracji jest dobry pomysł, aby mieć takie same jak wersje na dwóch serwerach.  
![Serwer przejściowy](./media/how-to-upgrade-previous-version/stagingserver1.png)

> [!NOTE]
> Niektórzy klienci preferują trzy lub cztery serwery w tym scenariuszu. Po uaktualnieniu serwera przejściowego, nie masz kopii zapasowej serwera dla [odzyskiwania po awarii](how-to-connect-sync-staging-server.md#disaster-recovery). Za pomocą trzech lub czterech serwerów należy przygotować jednego zestawu serwerów podstawowy/w gotowości za pomocą nowej wersji, którą zapewnia, że zawsze jest serwer przejściowy, który jest gotowy do zastąpienia.

Te kroki są również działać można przenieść z usługi Azure AD Sync lub rozwiązania z usługą FIM + łącznik usługi Azure AD. Te kroki nie zadziałają z narzędziem DirSync, ale tej samej swing migracji metody (nazywane również wdrożenie równoległe) z krokami z narzędziem DirSync jest w [uaktualnienie usługi Azure Active Directory sync (DirSync)](how-to-dirsync-upgrade-get-started.md).

### <a name="use-a-swing-migration-to-upgrade"></a>Użyj migracja typu swing, aby uaktualnić
1. Jeśli używasz usługi Azure AD Connect na obu serwerach i planujesz tylko wprowadzić zmiany, upewnij się, że konfiguracja aktywnego serwera i serwer przejściowy zarówno korzystają z tej samej wersji. Ułatwia do porównania różnic później. Jeśli wykonujesz uaktualnienie z narzędzia Azure AD Sync, te serwery mają różne wersje. Jeśli uaktualniasz ze starszych wersji programu Azure AD Connect, to dobry pomysł, aby uruchomić przy użyciu dwóch serwerów, które korzystają z tej samej wersji, ale nie jest to wymagane.
2. Jeśli zostały wprowadzone w konfiguracji niestandardowej przejściowego serwer nie ma go, wykonaj opisane w sekcji [przenieść Konfiguracja niestandardowa z aktywnego serwera na serwer przejściowy](#move-a-custom-configuration-from-the-active-server-to-the-staging-server).
3. Jeśli wykonujesz uaktualnienie z wcześniejszej wersji programu Azure AD Connect, należy uaktualnić serwer przejściowy do najnowszej wersji. Jeśli przenosisz z usługi Azure AD Sync, zainstaluj program Azure AD Connect na serwerze przejściowym.
4. Pozwól, aparat synchronizacji, uruchom pełne importowanie i pełną synchronizację na serwerze przejściowym.
5. Sprawdź, czy nowa konfiguracja nie powodują zmiany nieoczekiwany wykonując kroki opisane w obszarze "Zweryfikuj" w [Zweryfikuj konfigurację serwera](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server). Jeśli coś, co nie jest, zgodnie z oczekiwaniami, popraw go, uruchomić operację importu i synchronizacji i sprawdzić dane, dopóki nie wygląda na to dobre rozwiązanie, wykonując następujące kroki.
6. Przełącz serwer przejściowy jako aktywnego serwera. Jest to ostatni krok "Przełącznika active server" w [Zweryfikuj konfigurację serwera](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server).
7. Jeśli uaktualniasz program Azure AD Connect, należy uaktualnić serwer, który jest teraz w trybie przejściowym do najnowszej wersji. Wykonaj te same kroki przed pobierać dane i Konfiguracja uaktualnienia. Po uaktualnieniu z programu Azure AD Sync, możesz teraz wyłączyć i zlikwidować stary serwer.

### <a name="move-a-custom-configuration-from-the-active-server-to-the-staging-server"></a>Przenoszenie konfiguracji niestandardowej z aktywnego serwera na serwer przejściowy
Zmiany konfiguracji zostały wprowadzone do aktywnego serwera, należy się upewnić, że te same zmiany są stosowane do serwer przejściowy. Aby ułatwić to przeniesienie, można użyć [Dokumentatora konfiguracji usługi Azure AD Connect](https://github.com/Microsoft/AADConnectConfigDocumenter).

Można przenieść synchronizacji niestandardowych reguł został utworzony przy użyciu programu PowerShell. Należy najpierw zastosować inne zmiany w taki sam sposób w obu systemach i nie można migrować zmiany. [Dokumentatora konfiguracji](https://github.com/Microsoft/AADConnectConfigDocumenter) ułatwia porównanie tych dwóch systemów, aby upewnić się, że są one identyczne. Narzędzie może również pomóc w przypadku automatyzacji kroki opisane w tej sekcji.

Należy skonfigurować następujące elementy w taki sam sposób na obu serwerach:

* Połączenia do tego samego lasów
* Wszelkie filtrowanie domen i jednostek organizacyjnych
* Te same funkcje opcjonalne, takie jak synchronizacja haseł i zapisywania zwrotnego haseł

**Przenieś reguły synchronizacji niestandardowych**  
Aby przenieść reguły synchronizacji niestandardowego, wykonaj następujące czynności:

1. Otwórz **Synchronization Rules Editor** na aktywnym serwerem.
2. Wybierz regułę niestandardową. Kliknij przycisk **wyeksportować**. Wyświetlenie okna Notatnik. Zapisz plik tymczasowy z rozszerzeniem nazwy PS1. Dzięki temu skrypt programu PowerShell. Skopiuj plik PS1, aby serwer przejściowy.  
   ![Wyeksportowanie reguły synchronizacji](./media/how-to-upgrade-previous-version/exportrule.png)
3. Identyfikator GUID łącznika różni się na serwerze i należy ją zmienić. Aby uzyskać identyfikator GUID, należy uruchomić **Synchronization Rules Editor**, wybierz jedną z reguł out-of-box, które reprezentują ten sam połączonego systemu, a następnie kliknij przycisk **wyeksportować**. Zastąp identyfikator GUID w pliku PS1 identyfikatora GUID z serwer przejściowy.
4. W wierszu polecenia programu PowerShell Uruchom plik PS1. Spowoduje to utworzenie reguły niestandardowe synchronizacji na serwerze.
5. Powtórz tę czynność dla wszystkich niestandardowych reguł.

## <a name="how-to-defer-full-synchronization-after-upgrade"></a>Jak mają być odroczone pełnej synchronizacji po uaktualnieniu
Podczas uaktualniania w miejscu, mogą istnieć zmiany wprowadzone, które wymagają synchronizacji określonych działań (w tym kroki pełny Import i pełną synchronizację) do wykonania. Na przykład wymagać zmiany schematu łącznika **pełny import** krok i out-of-box zmiany reguł synchronizacji wymagają **pełnej synchronizacji** krok do wykonania, na których to dotyczy łączników. Podczas uaktualniania, Azure AD Connect Określa, jakie działania synchronizacji są wymagane i rejestruje je jako *zastępuje*. W poniższym cykl synchronizacji harmonogramu synchronizacji przejmuje te zastąpienia i wykonuje je. Po zastąpienia pomyślnie jest wykonywana, jest ona usuwana.

Mogą wystąpić sytuacje, w których nie chcesz te zastąpienia została wykonana natychmiast po uaktualnieniu. Na przykład masz wiele obiektów synchronizowanych i chcesz, aby te kroki synchronizacji, aby miejsce po godzinach pracy. Aby usunąć te zastąpienia:

1. Podczas uaktualniania **Usuń zaznaczenie pola wyboru** opcję **proces synchronizacji rozpocznie się po ukończeniu konfiguracji**. Wyłącza harmonogram synchronizacji i zapobiega cykl synchronizacji miejsce automatycznie przed usunięciem zastąpienia.

   ![DisableFullSyncAfterUpgrade](./media/how-to-upgrade-previous-version/disablefullsync01.png)

2. Po zakończeniu uaktualniania, uruchom następujące polecenie cmdlet, aby dowiedzieć się, jakie zastąpienia zostały dodane: `Get-ADSyncSchedulerConnectorOverride | fl`

   >[!NOTE]
   > Zastąpienia są właściwe dla łącznika. W poniższym przykładzie krok pełny Import i pełną synchronizację zostały dodane do środowiska lokalnego łącznika usługi AD i łącznik usługi Azure AD.

   ![DisableFullSyncAfterUpgrade](./media/how-to-upgrade-previous-version/disablefullsync02.png)

3. Zanotuj istniejących zastąpień, które zostały dodane.
   
4. Aby usunąć zastąpienia zarówno pełne importowanie i pełną synchronizację przeprowadzono arbitralnej łącznik, uruchom następujące polecenie cmdlet: `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid-of-ConnectorIdentifier> -FullImportRequired $false -FullSyncRequired $false`

   Aby usunąć zastąpień dla wszystkich łączników, uruchom następujący skrypt programu PowerShell:

   ```
   foreach ($connectorOverride in Get-ADSyncSchedulerConnectorOverride)
   {
       Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier $connectorOverride.ConnectorIdentifier.Guid -FullSyncRequired $false -FullImportRequired $false
   }
   ```

5. Aby wznowić działanie usługi scheduler, uruchom następujące polecenie cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $true`

   >[!IMPORTANT]
   > Pamiętaj, aby wykonać kroki wymagane synchronizacji przy najbliższej sposobności. Możesz ręcznie wykonać te kroki przy użyciu Menedżera usługi synchronizacji lub dodać zastąpienia z powrotem przy użyciu polecenia cmdlet Set-ADSyncSchedulerConnectorOverride.

Aby dodać zastąpienia zarówno pełne importowanie i pełną synchronizację łącznika dowolnego, uruchom następujące polecenie cmdlet:  `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid> -FullImportRequired $true -FullSyncRequired $true`

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Poniższa sekcja zawiera Rozwiązywanie problemów i informacje, które można użyć, jeśli wystąpi problem, uaktualnianie usługi Azure AD Connect.

### <a name="azure-active-directory-connector-missing-error-during-azure-ad-connect-upgrade"></a>Uaktualnianie usługi Azure Active Directory connector Brak wystąpił błąd podczas program Azure AD Connect

Kiedy uaktualniasz program Azure AD Connect z poprzedniej wersji, może napotkać w następujący błąd, na początku uaktualnienia 

![Błąd](./media/how-to-upgrade-previous-version/error1.png)

Ten błąd występuje, ponieważ łącznik usługi Azure Active Directory o identyfikatorze, b891884f-051e-4a83-95af - 2544101c 9083, nie istnieje w bieżącej konfiguracji usługi Azure AD Connect. Aby sprawdzić, czy jest to możliwe, Otwórz okno programu PowerShell, uruchom polecenie Cmdlet `Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083`

```
PS C:\> Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083
Get-ADSyncConnector : Operation failed because the specified MA could not be found.
At line:1 char:1
+ Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ReadError: (Microsoft.Ident...ConnectorCmdlet:GetADSyncConnectorCmdlet) [Get-ADSyncConne
   ctor], ConnectorNotFoundException
    + FullyQualifiedErrorId : Operation failed because the specified MA could not be found.,Microsoft.IdentityManageme
   nt.PowerShell.Cmdlet.GetADSyncConnectorCmdlet

```

Polecenia Cmdlet programu PowerShell zgłasza błąd **nie można odnaleźć określonego MA**.

Przyczyna, dzieje się tak jest, ponieważ bieżąca konfiguracja usługi Azure AD Connect nie jest obsługiwana dla uaktualnienie. 

Jeśli chcesz zainstalować nowszą wersję programu Azure AD Connect: Zamknij kreatora programu Azure AD Connect, Odinstaluj istniejący program Azure AD Connect, a jest czysta instalacja nowszą programu Azure AD Connect.



## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
