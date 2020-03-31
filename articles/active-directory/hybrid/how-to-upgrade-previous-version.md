---
title: 'Usługa Azure AD Connect: uaktualnienie z poprzedniej wersji | Dokumenty firmy Microsoft'
description: W tym artykule wyjaśniono różne metody uaktualniania do najnowszej wersji usługi Azure Active Directory Connect, w tym uaktualnienie w miejscu i migrację huśtawka.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60347752"
---
# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect: uaktualnianie z wcześniejszej wersji do najnowszej
W tym temacie opisano różne metody, których można użyć do uaktualnienia instalacji usługi Azure Active Directory (Azure AD) Connect do najnowszej wersji. Zaleca się, aby zachować aktualną z wersji usługi Azure AD Connect. Kroki opisane w sekcji [Migracja huśtawka](#swing-migration) są również używane podczas wprowadzania istotnej zmiany konfiguracji.

>[!NOTE]
> Jest obecnie obsługiwany do uaktualnienia z dowolnej wersji usługi Azure AD Connect do bieżącej wersji. Uaktualnienia w miejscu DirSync lub ADSync nie są obsługiwane i migracji huśtawka jest wymagane.  Jeśli chcesz uaktualnić z DirSync, zobacz [Uaktualnianie z narzędzia synchronizacji usługi Azure AD (DirSync)](how-to-dirsync-upgrade-get-started.md) lub sekcji [migracji swing.](#swing-migration)  </br>W praktyce klienci w bardzo starych wersjach mogą napotkać problemy niezwiązane bezpośrednio z usługą Azure AD Connect. Serwery, które są w produkcji od kilku lat, zazwyczaj miały kilka poprawek stosowanych do nich i nie wszystkie z nich mogą być rozliczane.  Ogólnie rzecz biorąc, klienci, którzy nie uaktualnili w ciągu 12-18 miesięcy, powinni rozważyć uaktualnienie huśtawka zamiast tego, ponieważ jest to najbardziej konserwatywna i najmniej ryzykowna opcja.

Jeśli chcesz uaktualnić z DirSync, zobacz [Uaktualnienie z narzędzia synchronizacji usługi Azure AD (DirSync)](how-to-dirsync-upgrade-get-started.md) zamiast tego.

Istnieje kilka różnych strategii, których można użyć do uaktualnienia usługi Azure AD Connect.

| Metoda | Opis |
| --- | --- |
| [Automatyczne uaktualnianie](how-to-connect-install-automatic-upgrade.md) |Jest to najprostsza metoda dla klientów z instalacją ekspresową. |
| [Uaktualnienie w miejscu](#in-place-upgrade) |Jeśli masz jeden serwer, można uaktualnić instalację w miejscu na tym samym serwerze. |
| [Migracja typu swing](#swing-migration) |Za pomocą dwóch serwerów można przygotować jeden z serwerów z nową wersją lub konfiguracją i zmienić aktywny serwer, gdy będziesz gotowy. |

Aby uzyskać informacje o uprawnieniach, zobacz [uprawnienia wymagane do uaktualnienia](reference-connect-accounts-permissions.md#upgrade).

> [!NOTE]
> Po włączeniu nowego serwera usługi Azure AD Connect, aby rozpocząć synchronizowanie zmian do usługi Azure AD, nie należy przywracać do przy użyciu DirSync lub Usługi Azure AD Sync. Przejście z usługi Azure AD Connect do starszych klientów, w tym DirSync i Usługi Azure AD Sync, nie jest obsługiwane i może prowadzić do problemów, takich jak utrata danych w usłudze Azure AD.

## <a name="in-place-upgrade"></a>Uaktualnienie w miejscu
Uaktualnienie w miejscu działa do przenoszenia z usługi Azure AD Sync lub usługi Azure AD Connect. Nie działa w przypadku przenoszenia z programu DirSync ani rozwiązania z programem Forefront Identity Manager (FIM) + Azure AD Connector.

Ta metoda jest preferowana, gdy masz jeden serwer i mniej niż około 100 000 obiektów. Jeśli są jakieś zmiany w regułach synchronizacji out-of-box, po uaktualnieniu nastąpi pełny import i pełna synchronizacja. Ta metoda zapewnia, że nowa konfiguracja jest stosowana do wszystkich istniejących obiektów w systemie. To uruchomienie może potrwać kilka godzin, w zależności od liczby obiektów, które znajdują się w zakresie aparatu synchronizacji. Normalny harmonogram synchronizacji różnicowej (który domyślnie synchronizuje co 30 minut) jest zawieszony, ale synchronizacja haseł jest kontynuowana. Możesz rozważyć wykonanie uaktualnienia w miejscu podczas weekendu. Jeśli nie ma żadnych zmian w konfiguracji out-of-box z nową wersją usługi Azure AD Connect, zamiast tego rozpoczyna się normalny import/synchronizacja różnicy.  
![Uaktualnienie w miejscu](./media/how-to-upgrade-previous-version/inplaceupgrade.png)

Jeśli wprowadzono zmiany w regułach synchronizacji po wyjęciu z pudełka, reguły te zostaną ustawione z powrotem na domyślną konfigurację podczas uaktualniania. Aby upewnić się, że konfiguracja jest zachowywana między uaktualnieniami, upewnij się, że wprowadzasz zmiany, ponieważ są one opisane w [najlepszych praktykach dotyczących zmiany konfiguracji domyślnej](how-to-connect-sync-best-practices-changing-default-configuration.md).

Podczas uaktualniania w miejscu mogą wystąpić zmiany, które wymagają określonych działań synchronizacji (w tym kroku pełnego importu i pełnej synchronizacji) do wykonania po zakończeniu uaktualniania. Aby odroczyć takie działania, patrz sekcja [Jak odroczyć pełną synchronizację po uaktualnieniu](#how-to-defer-full-synchronization-after-upgrade).

Jeśli używasz usługi Azure AD Connect z łącznikiem niestandardowym (na przykład ogólny łącznik LDAP i ogólny łącznik SQL), należy odświeżyć odpowiednią konfigurację łącznika w [Menedżerze usług synchronizacji](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-connectors) po uaktualnieniu w miejscu. Szczegółowe informacje na temat odświeżania konfiguracji łącznika można znaleźć w sekcji [Artykuł Historia wersji łącznika — Rozwiązywanie problemów](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-connector-version-history#troubleshooting). Jeśli konfiguracja nie zostanie odświeżona, kroki uruchamiania importu i eksportu nie będą działać poprawnie dla łącznika. W dzienniku zdarzeń aplikacji pojawi się następujący błąd z komunikatem *"Wersja zestawu w konfiguracji łącznika usługi AAD ("X.X.XXX. X") jest wcześniejsza niż rzeczywista wersja ("X.X.XXX. X") z "C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll".*

## <a name="swing-migration"></a>Migracja typu swing
Jeśli masz złożone wdrożenie lub wiele obiektów, może być niepraktyczne, aby wykonać uaktualnienie w miejscu w systemie na żywo. W przypadku niektórych klientów ten proces może potrwać wiele dni — w tym czasie nie są przetwarzane żadne zmiany różnicowe. Tej metody można również użyć, gdy planujesz wprowadzić istotne zmiany w konfiguracji i chcesz je wypróbować, zanim zostaną wypchnięte do chmury.

Zalecaną metodą dla tych scenariuszy jest użycie migracji huśtawka. Potrzebujesz (co najmniej) dwóch serwerów — jednego aktywnego serwera i jednego serwera przemieszczania. Aktywny serwer (pokazany z niebieskimi liniami na poniższym rysunku) jest odpowiedzialny za aktywne obciążenie produkcyjne. Serwer przemieszczania (pokazany z przerywanymi fioletowymi liniami) jest przygotowywany z nową wersją lub konfiguracją. Gdy jest w pełni gotowy, ten serwer jest aktywny. Poprzedni aktywny serwer, który ma teraz zainstalowaną starą wersję lub konfigurację, jest wprowadzany do serwera przemieszczania i uaktualniany.

Dwa serwery mogą używać różnych wersji. Na przykład aktywny serwer, który planujesz likwidację, może korzystać z usługi Azure AD Sync, a nowy serwer przemieszczania może korzystać z usługi Azure AD Connect. Jeśli używasz migracji huśtawka do opracowania nowej konfiguracji, to dobry pomysł, aby mieć te same wersje na dwóch serwerach.  
![Serwer przemieszczania](./media/how-to-upgrade-previous-version/stagingserver1.png)

> [!NOTE]
> Niektórzy klienci wolą mieć trzy lub cztery serwery dla tego scenariusza. Po uaktualnieniu serwera przemieszczania nie ma serwera kopii zapasowej do [odzyskiwania po awarii](how-to-connect-sync-staging-server.md#disaster-recovery). Za pomocą trzech lub czterech serwerów można przygotować jeden zestaw serwerów podstawowych/rezerwowych z nową wersją, co zapewnia, że zawsze istnieje serwer przejściowy, który jest gotowy do przejęcia.

Te kroki działają również w celu przejścia z usługi Azure AD Sync lub rozwiązania z FIM + Azure AD Connector. Te kroki nie działają w przypadku programu DirSync, ale ta sama metoda migracji zmian (nazywana również wdrożeniem równoległym) z krokami dla programu DirSync znajduje się w [synchronizacji usługi Azure Active Directory (DirSync) w trybie uaktualnienia.](how-to-dirsync-upgrade-get-started.md)

### <a name="use-a-swing-migration-to-upgrade"></a>Uaktualnianie za pomocą migracji wahadłowej
1. Jeśli używasz usługi Azure AD Connect na obu serwerach i planujesz tylko wprowadzić zmianę konfiguracji, upewnij się, że aktywny serwer i serwer przemieszczania są zarówno przy użyciu tej samej wersji. To sprawia, że łatwiej porównać różnice później. Jeśli uaktualniasz z usługi Azure AD Sync, te serwery mają różne wersje. Jeśli uaktualniasz ze starszej wersji usługi Azure AD Connect, warto zacząć od dwóch serwerów, które używają tej samej wersji, ale nie jest to wymagane.
2. Jeśli konfiguracja niestandardowa została wykonana, a serwer przejściowy jej nie ma, wykonaj czynności opisane w obszarze [Przenoszenie konfiguracji niestandardowej z aktywnego serwera na serwer przejściowy](#move-a-custom-configuration-from-the-active-server-to-the-staging-server).
3. Jeśli uaktualniasz z wcześniejszej wersji usługi Azure AD Connect, uaktualnij serwer przejściowy do najnowszej wersji. Jeśli przenosisz się z usługi Azure AD Sync, zainstaluj usługę Azure AD Connect na serwerze przejściowym.
4. Pozwól aparatowi synchronizacji uruchomić pełny import i pełną synchronizację na serwerze przejściowym.
5. Sprawdź, czy nowa konfiguracja nie spowodowała żadnych nieoczekiwanych zmian, wykonując kroki w obszarze "Weryfikuj" w [obszarze Sprawdź konfigurację serwera](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server). Jeśli coś nie jest zgodne z oczekiwaniami, popraw go, uruchom import i synchronizuj i zweryfikuj dane, aż będą wyglądały dobrze, wykonując kroki.
6. Przełącz serwer przejściowy jako serwer aktywny. Jest to ostatni krok "Przełącz aktywny serwer" w [Verify konfiguracji serwera](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server).
7. Jeśli uaktualniasz usługę Azure AD Connect, uaktualnij serwer, który jest teraz w trybie przejściowym do najnowszej wersji. Wykonaj te same kroki, co poprzednio, aby uaktualnić dane i konfigurację. Jeśli uaktualniono usługę Azure AD Sync, można teraz wyłączyć i zlikwidować stary serwer.

### <a name="move-a-custom-configuration-from-the-active-server-to-the-staging-server"></a>Przenoszenie konfiguracji niestandardowej z aktywnego serwera na serwer przemieszczania
Jeśli wprowadzono zmiany konfiguracji na aktywnym serwerze, należy upewnić się, że te same zmiany są stosowane do serwera przemieszczania. Aby pomóc w tym przeprowadzce, można użyć [dokumentu konfiguracji usługi Azure AD Connect](https://github.com/Microsoft/AADConnectConfigDocumenter).

Reguły synchronizacji niestandardowej utworzone przy użyciu programu PowerShell można przenosić. Należy zastosować inne zmiany w ten sam sposób w obu systemach i nie można migrować zmian. [Dokumentujący konfigurację](https://github.com/Microsoft/AADConnectConfigDocumenter) może pomóc w porównaniu dwóch systemów, aby upewnić się, że są identyczne. Narzędzie może również pomóc w automatyzacji kroków znalezionych w tej sekcji.

Na obu serwerach należy skonfigurować następujące elementy w ten sam sposób:

* Połączenie z tymi samymi lasami
* Filtrowanie dowolnej domeny i usługi organizacyjnej
* Te same funkcje opcjonalne, takie jak synchronizacja hasła i zapisywanie hasła

**Przenoszenie niestandardowych reguł synchronizacji**  
Aby przenieść niestandardowe reguły synchronizacji, wykonaj następujące czynności:

1. Otwórz **Edytor reguł synchronizacji** na aktywnym serwerze.
2. Wybierz regułę niestandardową. Kliknij polecenie **Eksportuj**. Spowoduje to wyświetlenie okna Notatnika. Zapisz plik tymczasowy z rozszerzeniem PS1. To sprawia, że skrypt programu PowerShell. Skopiuj plik PS1 na serwer przejściowy.  
   ![Eksport reguł synchronizacji](./media/how-to-upgrade-previous-version/exportrule.png)
3. Identyfikator GUID łącznika jest inny na serwerze przejściowym i należy go zmienić. Aby uzyskać identyfikator GUID, uruchom **Edytor reguł synchronizacji**, wybierz jedną z gotowych reguł, które reprezentują ten sam połączony system, a następnie kliknij przycisk **Eksportuj**. Zastąp identyfikator GUID w pliku PS1 identyfikatorem GUID z serwera przemieszczania.
4. W wierszu programu PowerShell uruchom plik PS1. Spowoduje to utworzenie reguły synchronizacji niestandardowej na serwerze przejściowym.
5. Powtórz to dla wszystkich reguł niestandardowych.

## <a name="how-to-defer-full-synchronization-after-upgrade"></a>Jak odroczyć pełną synchronizację po uaktualnieniu
Podczas uaktualniania w miejscu mogą wystąpić zmiany, które wymagają wykonania określonych działań synchronizacji (w tym kroku pełnego importu i pełnej synchronizacji). Na przykład zmiany schematu łącznika wymagają pełnego kroku **importu** i nieurządzenia zmiany reguł synchronizacji wymagają pełnego kroku **synchronizacji,** który ma być wykonywany na łącznikach, których dotyczy problem. Podczas uaktualniania usługa Azure AD Connect określa, jakie działania synchronizacji są wymagane i rejestruje je jako *zastąpienia.* W poniższym cyklu synchronizacji harmonogram synchronizacji odbiera te zastąpienia i wykonuje je. Po pomyślnym wykonaniu zastąpienia jest usuwany.

Mogą wystąpić sytuacje, w których nie chcesz, aby te zastąpienia miały miejsce natychmiast po uaktualnieniu. Na przykład masz wiele zsynchronizowanych obiektów i chcesz, aby te kroki synchronizacji występują po godzinach pracy. Aby usunąć te zastąpienia:

1. Podczas uaktualniania **odznacz** opcję **Rozpocznij proces synchronizacji po zakończeniu konfiguracji**. Spowoduje to wyłączenie harmonogramu synchronizacji i zapobiega automatycznemu przechodzeniu cyklu synchronizacji przed usunięciem nadpisań.

   ![DisableFullSyncPo uaktualnieniu](./media/how-to-upgrade-previous-version/disablefullsync01.png)

2. Po zakończeniu uaktualniania uruchom następujące polecenie cmdlet, aby dowiedzieć się, jakie zastąpienia zostały dodane:`Get-ADSyncSchedulerConnectorOverride | fl`

   >[!NOTE]
   > Zastąpienia są specyficzne dla łącznika. W poniższym przykładzie krok pełnego importu i pełna synchronizacja zostały dodane do lokalnego łącznika usługi AD i usługi Azure AD Connector.

   ![DisableFullSyncPo uaktualnieniu](./media/how-to-upgrade-previous-version/disablefullsync02.png)

3. Zanotuj istniejące zastąpienia, które zostały dodane.
   
4. Aby usunąć zastąpienia zarówno pełnego importu, jak i pełnej synchronizacji na dowolnym łączniku, uruchom następujące polecenie cmdlet:`Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid-of-ConnectorIdentifier> -FullImportRequired $false -FullSyncRequired $false`

   Aby usunąć zastąpienia wszystkich łączników, wykonaj następujący skrypt programu PowerShell:

   ```
   foreach ($connectorOverride in Get-ADSyncSchedulerConnectorOverride)
   {
       Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier $connectorOverride.ConnectorIdentifier.Guid -FullSyncRequired $false -FullImportRequired $false
   }
   ```

5. Aby wznowić harmonogram, uruchom następujące polecenie cmdlet:`Set-ADSyncScheduler -SyncCycleEnabled $true`

   >[!IMPORTANT]
   > Pamiętaj, aby wykonać wymagane kroki synchronizacji w najbliższym czasie. Te kroki można wykonać ręcznie za pomocą Menedżera usług synchronizacji lub dodać zastąpienia z powrotem za pomocą polecenia cmdlet Set-ADSyncSchedulerConnectorOverride.

Aby dodać zastąpienia zarówno pełnego importu, jak i pełnej synchronizacji na dowolnym łączniku, uruchom następujące polecenie cmdlet:`Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid> -FullImportRequired $true -FullSyncRequired $true`

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Poniższa sekcja zawiera rozwiązywanie problemów i informacje, których można użyć w przypadku wystąpienia problemu z uaktualnieniem usługi Azure AD Connect.

### <a name="azure-active-directory-connector-missing-error-during-azure-ad-connect-upgrade"></a>Brak błędu błędu błędu łącznika usługi Azure Active Directory podczas uaktualniania usługi Azure AD Connect

Podczas uaktualniania usługi Azure AD Connect z poprzedniej wersji, może wystąpić następujący błąd na początku uaktualnienia 

![Błąd](./media/how-to-upgrade-previous-version/error1.png)

Ten błąd występuje, ponieważ łącznik usługi Azure Active Directory z identyfikatorem b891884f-051e-4a83-95af-2544101c9083 nie istnieje w bieżącej konfiguracji usługi Azure AD Connect. Aby sprawdzić, czy tak jest, otwórz okno programu PowerShell, uruchom polecenie Cmdlet`Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083`

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

Polecenie cmdlet programu PowerShell zgłasza **błąd, który nie można odnaleźć określonego magistratu.**

Powodem, dla którego taka sytuacja występuje, jest to, że bieżąca konfiguracja usługi Azure AD Connect nie jest obsługiwana do uaktualnienia. 

Jeśli chcesz zainstalować nowszą wersję usługi Azure AD Connect: zamknij kreatora usługi Azure AD Connect, odinstaluj istniejącą usługę Azure AD Connect i wykonaj czystą instalację nowszej usługi Azure AD Connect.



## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [integrując tożsamości lokalne z usługą Azure Active Directory.](whatis-hybrid-identity.md)
